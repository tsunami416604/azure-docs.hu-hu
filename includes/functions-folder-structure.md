
A kód egy adott funkció alkalmazás összes funkciójának egy állomás konfigurációs fájlja a gyökérmappa és egy vagy több almappában található. Minden egyes tartalmazza, a kódot egy külön függvény, az alábbi példában látható módon:

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

Host.json néhány futásidejű-specifikus konfigurációk tartalmazza, ezért a gyökérmappában található, a függvény alkalmazás helyezkedik el. Rendelkezésre álló beállításokkal kapcsolatos további információkért lásd: a [host.json hivatkozás](../articles/azure-functions/functions-host-json.md).

Minden egyes függvény rendelkezik egy vagy több kód fájlok, a function.json konfigurációs és egyéb függőségek tartalmazó mappát.

