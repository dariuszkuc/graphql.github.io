---
name: Caliban
description: Caliban is a purely functional library for building GraphQL servers and clients in Scala
url: https://ghostdogpr.github.io/caliban/
github: ghostdogpr/caliban
---

An example of a GraphQL schema and query with `caliban`:
```scala
case class Character(name: String, age: Int)
def getCharacters(): List[Character] = ???
def getCharacter(name: String): Option[Character] = ???
// schema
case class CharacterName(name: String)
case class Queries(characters: List[Character],
                   character: CharacterName => Option[Character])
// resolver
val queries = Queries(getCharacters, args => getCharacter(args.name))
import caliban.GraphQL.graphQL
import caliban.RootResolver
val api = graphQL(RootResolver(queries))
for {
  interpreter <- api.interpreter
} yield interpreter
case class GraphQLResponse[+E](data: ResponseValue, errors: List[E])
val query = """
  {
    characters {
      name
    }
  }"""
for {
  result <- interpreter.execute(query)
  _      <- zio.console.putStrLn(result.data.toString)
} yield ()
```
