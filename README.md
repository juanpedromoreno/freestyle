
[comment]: # (Start Badges)

[![Build Status](https://travis-ci.org/47deg/freestyle.svg?branch=master)](https://travis-ci.org/47deg/freestyle) [![codecov.io](http://codecov.io/github/47deg/freestyle/coverage.svg?branch=master)](http://codecov.io/github/47deg/freestyle?branch=master) [![License](https://img.shields.io/badge/license-Apache%202-blue.svg)](https://raw.githubusercontent.com/47deg/freestyle/master/LICENSE) [![Join the chat at https://gitter.im/47deg/freestyle](https://badges.gitter.im/47deg/freestyle.svg)](https://gitter.im/47deg/freestyle?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge) [![GitHub Issues](https://img.shields.io/github/issues/47deg/freestyle.svg)](https://github.com/47deg/freestyle/issues) 

[comment]: # (End Badges)


EXPERIMENTAL, UNRELEASED WIP.

A Cohesive & Pragmatic Framework of FP centric Scala libraries

# Build purely functional applications and libraries
Build stack-safe purely functional applications and libraries that support parallel and sequential computations where declaration is decoupled from interpretation.
Freestyle encourages programs built atop [Free algebras](/docs/src/main/tut/docs/core/algebras/) that are interpreted at the edge of your application ensuring effects are localized and performed in a controlled environment.
Applications built with Freestyle can be interpreted to any runtime semantics supported by the interpreter target type.

```scala
import freestyle._

@free trait Database[F[_]] {
  def get(id: UserId): FreeS[F, User]
}

@free trait Cache[F[_]] {
  def get(id: UserId): FreeS[F, User]
}

@module trait Persistence[F[_]] {
  val database: Database[F]
  val cache: Cache[F]
}
```

# Automatic Dependency Injection

Freestyle includes all the implicit machinery necessary to achieve seamless dependency injection of [`@free`](/docs/src/main/tut/docs/core/algebras) and [`@module`](/docs/src/main/tut/docs/core/modules) Algebras.
Simply require any of your `@free` or `@module` trait as implicits where needed.

```scala
def storedUsers[F[_]]
    (userId: UserId)
    (implicit persistence: Persistence[F]): FreeS[F, (User, User)] = {
  import persistence._
  for {
    cachedUser <- cache.get(userId)
    persistentUser <- database.get(userId)
  } yield (cachedUser, persistentUser)
}
```

# Ready to use integrations
Freestyle ships with [ready to use algebras and convenient syntax extensions](/docs/src/main/tut/docs/integrations) covering most of the application concerns such as persistence, configuration, logging, etc.
In addition Freestyle includes commonly used FP [effects](/docs/src/main/tut/docs/effects) stack such as `option`, `error`, `reader`, `writer`, `state` based on the capabilities of
the target runtime interpreters.

```scala
def loadUser[F[_]]
  (userId: UserId)
  (implicit 
    doobie: DoobieM[F], 
    logging: LoggingM[F]): FreeS[F, User] = {
    import doobie.implicits._
    for {
      user <- (sql"SELECT * FROM User WHERE userId = $userId"
                .query[User]
                .unique
                .liftFS[F])
      - <- logging.debug(s"Loaded User: ${user.userId}")
    } yield user
}
```

## freestyle in the wild

If you wish to add your library here please consider a PR to include it in the list below.

★ | ★ | ★
--- | --- | ---
![scala-exercises](https://www.scala-exercises.org/assets/images/navbar_brand.svg) | [**scala-exercises**](https://www.scala-exercises.org/) | Scala Exercises is an Open Source project for learning different technologies based in the Scala Programming Language.

[comment]: # (Start Copyright)
# Copyright

freestyle is designed and developed by 47 Degrees

Copyright (C) 2017 47 Degrees. <http://47deg.com>

[comment]: # (End Copyright)