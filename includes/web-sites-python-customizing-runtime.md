Az Azure határozza meg, hogy a Python melyik verzióját kell használni a virtuális környezethez, a következő prioritásokkal:

1. a gyökérmappa runtime.txt fájljában megadott verzió
1. a Python-beállítás által a webalkalmazás-konfigurációban megadott verzió (a webalkalmazása **Settings**(Beállítások)  > **App Settings** (Alkalmazásbeállítások) paneljén, az Azure portálon)
1. ha a fentiek egyike sincs megadva, a Python-2.7 az alapértelmezett verzió

Érvényes értékek a 

    \runtime.txt

tartalmához:

- python-2.7
- python-3.4

A megadott mikroverziót (harmadik számjegy) a rendszer figyelmen kívül hagyja.



<!--HONumber=Jun16_HO2-->


