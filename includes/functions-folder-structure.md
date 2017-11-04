
A kódot az összes, egy adott funkció alkalmazást a funkciókat él, az állomás konfigurációs fájlt tartalmazó legfelső szintű mappa és egy vagy több almappáiban, amelyek tartalmazzák a kód egy külön függvény, az alábbi példában látható módon:

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

A *host.json* tartalmaz néhány futásidejű konfigurációs fájl- és a gyökérmappában található, a függvény alkalmazás helyezkedik el. A rendelkezésre álló beállítások információkért lásd: [host.json hivatkozás](../articles/azure-functions/functions-host-json.md).

Minden egyes függvény rendelkezik egy vagy több kódfájlok, a function.json konfigurációs és egyéb függőségek tartalmazó mappát.

