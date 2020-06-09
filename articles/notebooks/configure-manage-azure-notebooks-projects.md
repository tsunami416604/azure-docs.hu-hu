---
title: Azure Notebooks előzetes verziójának konfigurálása és kezelése
description: Megtudhatja, hogyan kezelheti a projekt metaadatait, a projektfájlok, a projekt környezetét és a telepítési lépéseket a Azure Notebooks felhasználói felületen és a közvetlen terminál-hozzáférésen keresztül.
ms.topic: how-to
ms.date: 02/28/2020
ms.custom: tracking-python
ms.openlocfilehash: df64c9d90252c31118b66943b6a182319e3f1cc2
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84554308"
---
# <a name="manage-and-configure-projects-in-azure-notebooks-preview"></a><a id="manage-and-configure-projects" />Projektek kezelése és konfigurálása Azure Notebooks előzetes verzióban

Azure Notebooks előzetes verzióban a projekt lényegében a mögöttes linuxos virtuális gép konfigurációja, amelyben a Jupyter-jegyzetfüzetek futnak, valamint egy fájl mappájával és a leíró metaadatokkal. 

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

A Azure Notebooks a projekt irányítópultja lehetővé teszi a fájlok kezelését, és egyéb módon konfigurálja a projekt jellemzőit:

- A projekt futtatásának számítási szintje, amely lehet az ingyenes vagy az Azure-beli virtuális gép.
- A projekt metaadatai, beleértve a nevet, a leírást, a projekt megosztásakor használt azonosítót, valamint azt, hogy a projekt nyilvános vagy privát.
- A projekt notebook-, adat-és egyéb fájljai, amelyeket más fájlrendszerhez hasonlóan kezel.
- Egy projekt környezete, amelyet indítási parancsfájlok vagy közvetlenül a terminálon keresztül kezelhet.
- A terminálon keresztül hozzáférő naplók.

> [!Note]
> Az itt ismertetett kezelési és konfigurációs funkciók csak a projekt tulajdonosa számára érhetők el, aki eredetileg létrehozta a projektet. A projektet azonban saját fiókjába is bemásolhatja, ebben az esetben Ön lesz a tulajdonos, és igény szerint konfigurálhatja a projektet.

Azure Notebooks elindítja a mögöttes virtuális gépet, amikor egy jegyzetfüzetet vagy más fájlt futtat. A kiszolgáló automatikusan menti a fájlokat, és 60 perc inaktivitás után leáll. A kiszolgálót bármikor leállíthatja a **leállítási** parancs használatával (billentyűparancs: h).

## <a name="compute-tier"></a>Számítási szintek

Alapértelmezés szerint a projektek az **ingyenes számítási** szinten futnak, amely legfeljebb 4 GB memóriával és 1 GB-nyi adattal használható a visszaélések megelőzése érdekében. Megkerülheti ezeket a korlátozásokat, és növelheti a számítási teljesítményt az Azure-előfizetésben üzembe helyezett másik virtuális géppel. További információ: az [Adatelemzési Virtual Machines használata](use-data-science-virtual-machine.md).

## <a name="edit-project-metadata"></a>Projekt metaadatainak szerkesztése

A projekt irányítópultján válassza a **projekt beállításai**lehetőséget, majd válassza az **információ** fület, amely tartalmazza a projekt metaadatait a következő táblázatban leírtak szerint. Bármikor módosíthatja a projekt metaadatait.

| Beállítás | Leírás |
| --- | --- |
| Projektnév | A projekt felhasználóbarát neve, amelyet a Azure Notebooks a megjelenítési célokra használ. Például: ""Helló világ!"alkalmazás a Pythonban". |
| Projektazonosító | Egy egyéni azonosító, amely a projekt megosztásához használt URL-cím részévé válik. Ez az azonosító csak betűket, számokat és kötőjeleket használhat, legfeljebb 30 karakterből állhat, és nem lehet [foglalt projekt-azonosító](create-clone-jupyter-notebooks.md#reserved-project-ids). Ha nem tudja biztosan, hogy mit kell használni, a közös konvenció a projekt nevének olyan kisbetűs verzióját használja, amelyben a szóközök kötőjelbe vannak bekapcsolva, például "My-notebook-Project" (ha szükséges a hosszhoz). |
| Nyilvános projekt | Ha be van állítva, lehetővé teszi, hogy bárki hozzáférjen a projekthez. Privát projekt létrehozásakor törölje ezt a beállítást. |
| Klónozások elrejtése | Ha be van állítva, a többi felhasználó nem láthatja a projekthez készült klónok listáját. A klónok elrejtése olyan projektek esetében lehet hasznos, amelyek sok felhasználóval vannak megosztva, akik nem ugyanahhoz a szervezethez tartoznak, például egy osztály tanításához notebook használata esetén. |

> [!Important]
>
> A projekt AZONOSÍTÓjának módosítása érvényteleníti a korábban megosztott projektre mutató hivatkozásokat.

## <a name="manage-project-files"></a>Projektfájlok kezelése

A projekt irányítópultja a projekt mappa rendszerének tartalmát jeleníti meg. Ezeket a fájlokat különböző parancsokkal kezelheti.

### <a name="create-new-files-and-folders"></a>Új fájlok és mappák létrehozása

Az **+ új** parancs (billentyűparancs: n) új fájlokat vagy mappákat hoz létre. A parancs használatakor először válassza ki a létrehozandó elem típusát:

| Elemtípus | Leírás | Parancs viselkedése |
| --- | --- | --- |
| **Jegyzetfüzet** | Egy Jupyter notebook | Megjeleníti azt a felugró ablakot, amelyben megadja a notebook fájlnevét és nyelvét. |
| **Mappa** | Egy almappa | Létrehoz egy szerkesztési mezőt a projekt fájljának listájában, amelyben megadja a mappa nevét. |
| **Üres fájl** | Olyan fájl, amelybe bármilyen tartalmat, például szöveget, adatokat és más tartalmakat tárolhat. | Létrehoz egy szerkesztési mezőt a projekt fájljának listájában, amelyben megadja a fájlnevet. |
| **Markdown** | Egy Markdown-fájl. | Létrehoz egy szerkesztési mezőt a projekt fájljának listájában, amelyben megadja a fájlnevet. |

### <a name="upload-files"></a>Fájlok feltöltése

A **feltöltési** parancs két lehetőséget kínál az adatok más helyekről történő importálására: **az URL** -címről és **a számítógépről**. További információ: [adatfájlok használata az Azure notebook projects](work-with-project-data-files.md)szolgáltatásban.

### <a name="select-file-specific-commands"></a>Fájlra vonatkozó parancsok kiválasztása

A projekthez tartozó fájllista minden eleme parancsokat biztosít a jobb gombbal a helyi menüben:

![Parancsok egy fájl helyi menüjében](media/project-file-commands.png)

| Parancs | Billentyűparancsok | Műveletek |
| --- | --- | --- |
| Futtatás | r (vagy kattintson a) | Egy jegyzetfüzet-fájlt futtat. A rendszer megnyit más fájltípusokat a megtekintéshez.  |
| Hivatkozás másolása | é | A fájlra mutató hivatkozást másol a vágólapra. |
| Futtatás a Jupyter Lab-ban | j | Futtat egy jegyzetfüzetet a JupyterLab-ben, ami egy fejlesztő-orientált felület, mint a Jupyter. |
| Előnézet | P | Megnyitja a fájl HTML-előnézetét; a jegyzetfüzetek esetében az előnézet a jegyzetfüzet írásvédett megjelenítése. További információ: [előnézet](#preview) szakasz. |
| Fájl szerkesztése | i | Megnyitja a fájlt szerkesztésre. |
| Letöltés | n | Letölt egy zip-fájlt, amely a fájlt vagy egy mappa tartalmát tartalmazza. |
| Átnevezés | a | A fájl vagy mappa új nevének megadását kéri. |
| Törlés | x | Megerősítést kér, majd véglegesen eltávolítja a fájlt a projektből. A törlések nem vonhatók vissza. |
| Áthelyezés | m | Egy fájlt egy másik mappába helyez át ugyanabban a projektben. |

#### <a name="preview"></a>Előnézet

Egy fájl vagy jegyzetfüzet előnézete a tartalom írásvédett nézete. a jegyzetfüzet-cellák futtatása le van tiltva. Megjelenik egy előzetes verzió, amely a fájlra vagy jegyzetfüzetre mutató hivatkozással rendelkezik, de nem jelentkezett be a Azure Notebooksba. A bejelentkezést követően a felhasználók a saját fiókba is bejelentkezhetnek a jegyzetfüzetbe, vagy letölthetik a notebookot a helyi számítógépre.

Az előnézet oldalon számos, billentyűparancsokkal ellátott eszköztár-parancs támogatott:

| Parancs | Billentyűparancsok | Műveletek |
| --- | --- | --- |
| Megosztás | s | Megjeleníti a megosztási felugró ablakát, amelyből megszerezhet egy hivatkozást, megoszthatja a közösségi médiát, HTML-t szerezhet be beágyazáshoz, és e-mailt küldhet. |
| Klónozás | c  | A notebook klónozása a fiókjába. |
| Futtatás | r | Ha ezt engedélyezi, futtatja a jegyzetfüzetet. |
| Letöltés | n | Letölti a jegyzetfüzet egy példányát. |

## <a name="configure-the-project-environment"></a>A Project Environment konfigurálása

Az alapul szolgáló virtuális gép környezetét háromféleképpen állíthatja be, amelyben a jegyzetfüzetek futnak:

- Egyszeri inicializálási parancsfájl belefoglalása
- A projekt környezeti beállításai segítségével adhatja meg a telepítési lépéseket
- Egy terminálon keresztül érheti el a virtuális gépet.

A rendszer a projekt konfigurációjának összes formáját alkalmazza a virtuális gép indításakor, és így hatással van a projekten belüli összes jegyzetfüzetre.

### <a name="one-time-initialization-script"></a>Egyszeri inicializálási parancsfájl

Az első Azure Notebooks létrehoz egy kiszolgálót a projekthez, amely a *aznbsetup.sh*nevű projektben keres egy fájlt. Ha a fájl megtalálható, Azure Notebooks futtatja. A parancsfájl kimenetét a Project mappában, a *. aznbsetup. log*néven tárolja a rendszer.

### <a name="environment-setup-steps"></a>Környezet beállítási lépései

A projekt környezeti beállításait a környezet konfigurálására szolgáló egyedi lépések létrehozására használhatja.

A projekt irányítópultján válassza a **projekt beállításai**lehetőséget, majd válassza a **környezet** fület a projekt telepítési lépéseinek hozzáadásához, eltávolításához és módosításához:

![A Project Settings előugró ablak a környezet lapon van kijelölve](media/project-settings-environment-steps.png)

Lépés hozzáadásához először válassza a **+ Hozzáadás**lehetőséget, majd válassza ki a lépés típusát a **művelet** legördülő listában:

![Új környezet telepítési lépésének műveleti választója](media/project-settings-environment-details.png)

Az Ön által megadott adatok a választott művelet típusától függenek:

- **Követelmények. txt**: a második legördülő listában válasszon ki egy, a projektben már szereplő *követelmény. txt* fájlt. Ezután válasszon ki egy Python-verziót a megjelenő harmadik legördülő listából. A *követelmények. txt* fájl használatával Azure Notebooks `pip install -r` a *követelmények. txt* fájllal futtatja a notebook-kiszolgáló indításakor. Nem kell explicit módon telepítenie a csomagokat a notebookon belülről.

- **Rendszerhéj-parancsfájl**: a második legördülő listában válasszon ki egy bash rendszerhéj-parancsfájlt a projektben (jellemzően egy *. sh* kiterjesztésű fájl), amely a környezet inicializálásához futtatni kívánt parancsokat tartalmazza.

- **Environment. YML**: a második legördülő listában válasszon ki egy *környezetet. YML* -fájlt a Python-projektekhez Conda-környezet használatával.

   > [!WARNING]
   > Mivel ez egy előzetes verziójú szolgáltatás a fejlesztés alatt, jelenleg egy ismert probléma van, ahol a `Environment.yml` beállítás a várt módon nem lesz alkalmazva a projektre. A projekt és a Jupyter-jegyzetfüzetek nem töltik be a megadott környezeti fájlt.

Ha végzett a lépések hozzáadásával, válassza a **Mentés**lehetőséget.

### <a name="use-the-terminal"></a>A terminál használata

A projekt irányítópultján a **Terminal** parancs egy Linux-terminált nyit meg, amely közvetlen hozzáférést biztosít a kiszolgálóhoz. A terminálon az adatletöltést, a fájlok szerkesztését és kezelését, a folyamatok vizsgálatát, valamint a VI és a nano eszközt is használhatja.

> [!Note]
> Ha a projekt környezetében indítási parancsfájlok vannak, a terminál megnyitásával megjeleníthető egy üzenet, amely jelzi, hogy a telepítés még folyamatban van.

A terminálon bármilyen szabványos Linux-parancsot kiadhat. A `ls` Kezdőlap mappában is megtekintheti a virtuális gépen létező különböző környezeteket, például a *anaconda2_501*, a *anaconda3_420*, a *Anaconda3_501*, a *IfSharp*és az *R*-t, valamint a projektet tartalmazó *Project* mappát:

![Projekt-terminál Azure Notebooks](media/project-terminal.png)

Egy adott környezet befolyásolásához először módosítsa a könyvtárakat erre a környezeti mappába.

A Python-környezetek esetében az `pip` `conda` egyes környezetekhez tartozó *bin* mappában található. A környezetekhez beépített aliasokat is használhat:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

A kiszolgálón végrehajtott módosítások csak az aktuális munkamenetre érvényesek, kivéve a *projekt* mappában létrehozott fájlokat és mappákat. Például a Project mappában található fájl szerkesztése a munkamenetek között megmarad, de a csomagok és a `pip install` nem.

> [!Note]
> A vagy a használata esetén `python` `python3` meghívja a Python rendszer telepített verzióit, amelyek nem használatosak jegyzetfüzetekhez. Nincs engedélye a (z) rendszerhez hasonló műveletekhez `pip install` , ezért ügyeljen arra, hogy a verzióra vonatkozó aliasokat használja.

## <a name="access-notebook-logs"></a>Jegyzetfüzet-naplók elérése

Ha a jegyzetfüzetek futtatása során problémákba ütközik, a Jupyter kimenetét egy *. NB. log*nevű mappában tárolja a rendszer. Ezeket a naplókat a **Terminal** parancs vagy a projekt irányítópultja segítségével érheti el.

Általában a Jupyter helyi futtatásakor előfordulhat, hogy egy terminál-ablakból indította el. A terminálablak megjeleníti a kimenetet, például a kernel állapotát.

A naplók megtekintéséhez használja a következő parancsot a terminálon:

```bash
cat .nb.log
```

A parancsot egy Python-jegyzetfüzetben található kód cellából is használhatja:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Következő lépések

- [Útmutató: Project-adatfájlok használata](work-with-project-data-files.md)
- [Felhőbeli adatok elérése egy jegyzetfüzetben](access-data-resources-jupyter-notebooks.md)
