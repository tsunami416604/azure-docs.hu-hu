Ha a funkciók állomás helyileg futtatja, naplók ír a következő elérési út:

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

A Windows rendszeren `<DefaultTempDirectory>` az első megtalált érték a TMP, TEMP, USERPROFILE környezeti változók vagy a Windows-könyvtár.
MacOS vagy Linux `<DefaultTempDirectory>` a TMPDIR környezeti változó.

[!Note]
A funkciók gazdagép indításakor felülírja a meglévő fájlstruktúra a címtárban.