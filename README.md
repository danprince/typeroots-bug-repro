# tsconfig ignoring `typeRoots`

Setup to simulate a project where the root directory is a client side codebase
and the server directory is an independent server codebase which should not
share dependencies.

The problem is that the server's codebase is pulling in type declarations from
the client side `node_modules/@types/` folder, despite attempting to restrict
the `typeRoots` to ignore it.

You can test that the server compiles correctly when no client side modules are
installed.

```sh
cd server
npm install
npx tsc
```

Now install the client side dependencies, which include a type declaration for
one of the server modules, that is incompatible with the version the server has
installed.

```sh
cd .. # back to client
npm install
npx tsc
```

The client side code compiles correctly. Now return to the server:

```sh
cd server
npx tsc # error!
```

The server compilation now fails:

```
src/server.ts:4:1 - error TS2349: This expression is not callable.
  Type 'Foo' has no call signatures.

4 foo();
  ~~~


Found 1 error.
```

This is because the server is picking up `../node_modules/@types/foo` for the
`foo` module.

__I don't _ever_ want `tsc` to look in that parent directory when it is compiling
the server__, but it seems like it will, even with an explicit configuration for
`typeRoots`.

