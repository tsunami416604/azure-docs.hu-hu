---
title: Az Azure Notebookelőzetes verzió konfigurálása és kezelése
description: Ismerje meg, hogyan kezelheti a projekt metaadatait, a projektfájlokat, a projekt környezetét és a beállítási lépéseket mind az Azure Notebooks felhasználói felületén, mind a közvetlen terminálhozzáférésen keresztül.
ms.topic: how-to
ms.date: 02/28/2020
ms.openlocfilehash: 1674effda2cb9bda45f49c91ca618225b0a75f0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280598"
---
# <a name="manage-and-configure-projects-in-azure-notebooks-preview"></a><a id="manage-and-configure-projects" />Projektek kezelése és konfigurálása az Azure Notebookelőzetes verzióban

A projekt az Azure Notebooks Preview lényegében egy konfiguráció az alapul szolgáló Linux virtuális gép, amelyben Jupyter notebookok futnak, valamint egy fájlmappát és leíró metaadatokat. 

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Az Azure Notebooks projektirányítópultja lehetővé teszi a fájlok kezelését és a projekt jellemzőinek egyéb konfigurálását:

- A számítási szint, amelyen a projekt fut, amely lehet az ingyenes szint vagy egy Azure virtuális gép.
- A projekt metaadatai, amelyek tartalmazzák a nevet, a leírást, a projekt megosztásakor használt azonosítót, valamint azt, hogy a projekt nyilvános vagy privát.
- A projekt jegyzetfüzete, adatai és egyéb fájljai, amelyeket ugyanúgy kezel, mint bármely más fájlrendszert.
- A projekt környezete, amelyet indítási parancsfájlokon keresztül vagy közvetlenül a terminálon keresztül kezelhet.
- Naplók, amelyeket a terminálon keresztül érhet el.

> [!Note]
> Az itt leírt felügyeleti és konfigurációs funkciók csak a projektet eredetileg létrehozó projekttulajdonos számára érhetők el. A projektet azonban klónozhatja a saját fiókjába, ebben az esetben ön lesz a tulajdonos, és igény szerint konfigurálhatja a projektet.

Az Azure Notebooks elindítja az alapul szolgáló virtuális gépet, amikor egy jegyzetfüzetet vagy más fájlt futtat. A kiszolgáló 60 perc inaktivitás után automatikusan menti a fájlokat, és leáll. A kiszolgálót bármikor leállíthatja a **Shutdown** paranccsal (billentyűparancs: h).

## <a name="compute-tier"></a>Számítási szint

Alapértelmezés szerint a projektek a **Free Compute** szinten futnak, amely 4 GB memóriára és 1 GB adatra korlátozódik a visszaélések megelőzése érdekében. Megkerülheti ezeket a korlátozásokat, és növelheti a számítási teljesítményt egy azure-előfizetésben kiépített másik virtuális gép használatával. További információ: [Adatelemzési virtuális gépek használata.](use-data-science-virtual-machine.md)

## <a name="edit-project-metadata"></a>Projekt metaadatainak szerkesztése

A projekt irányítópultján válassza a **Projektbeállítások**lehetőséget, majd az **Információ** lapot, amely a projekt metaadatait tartalmazza az alábbi táblázatban leírtak szerint. A projekt metaadatait bármikor módosíthatja.

| Beállítás | Leírás |
| --- | --- |
| Projektnév | A projekt rövid neve, amelyet az Azure Notebooks megjelenítési célokra használ. Például "Hello World pythonban". |
| Projektazonosító | Egyéni azonosító, amely a projekt megosztásához használt URL részévé válik. Ez az azonosító csak betűket, számokat és kötőjeleket használhat, legfeljebb 30 karakter ből állhat, és nem lehet [fenntartott projektazonosító.](create-clone-jupyter-notebooks.md#reserved-project-ids) Ha nem biztos benne, hogy mit szeretne használni, általános konvenció a projektnév kisméretű változatának használata, ahol a szóközök kötőjelekké alakulnak, például "jegyzetfüzet-projekt" (szükség esetén csonkolva, hogy illeszkedjen a hosszkorláthoz). |
| Nyilvános projekt | Ha be van állítva, lehetővé teszi, hogy bárki, aki a linkkel rendelkezik, hozzáférjen a projekthez. Privát projekt létrehozásakor törölje a program ezt a beállítást. |
| Klónok elrejtése | Ha be van állítva, más felhasználók nem láthatják a projekthez készített klónok listáját. A klónok elrejtése olyan projektek esetében hasznos, amelyek sok olyan emberrel vannak megosztva, akik nem ugyanahhoz a szervezethez tartoznak, például amikor jegyzetfüzetet használnak egy osztály tanításához. |

> [!Important]
>
> A projektazonosító módosítása érvényteleníti a korábban megosztott projektre mutató hivatkozásokat.

## <a name="manage-project-files"></a>Projektfájlok kezelése

A projekt irányítópultja a projekt mapparendszerének tartalmát jeleníti meg. A fájlok kezeléséhez különböző parancsokat használhat.

### <a name="create-new-files-and-folders"></a>Új fájlok és mappák létrehozása

A **+ Új** parancs (billentyűparancs: n) új fájlokat vagy mappákat hoz létre. A parancs használatakor először válassza ki a létrehozandó elem típusát:

| Cikk típusa | Leírás | A parancs viselkedése |
| --- | --- | --- |
| **Jegyzetfüzet** | Jupyter-jegyzetfüzet | Egy előugró ablakot jelenít meg, amelyben megadhatja a jegyzetfüzet fájlnevét és nyelvét. |
| **Mappa** | Almappa | Létrehoz egy szerkesztési mezőt a projekt fájllistájában, amelybe beírja a mappa nevét. |
| **Üres fájl** | Olyan fájl, amelybe bármilyen tartalmat, például szöveget, adatot stb. | Létrehoz egy szerkesztési mezőt a projekt fájllistájában, amelybe beírja a fájlnevet. |
| **Markdown** | Egy Markdown-fájl. | Létrehoz egy szerkesztési mezőt a projekt fájllistájában, amelybe beírja a fájlnevet. |

### <a name="upload-files"></a>Fájlok feltöltése

A **Feltöltés** parancs két lehetőséget kínál az adatok más helyekről történő importálására: **URL-címről** és **számítógépről**. További információt az [Adatfájlok munkája az Azure Notebook-projektekben](work-with-project-data-files.md)című témakörben talál.

### <a name="select-file-specific-commands"></a>Fájlspecifikus parancsok kijelölése

A projekt fájllistájának minden eleme a jobb gombbal legördülő helyi menün keresztül biztosít parancsokat:

![Parancsok a fájl helyi menüjében](media/project-file-commands.png)

| Parancs | Billentyűparancsok | Műveletek |
| --- | --- | --- |
| Futtassa a következőt: | r (vagy kattintás) | Jegyzetfüzetfájlt futtat. Más fájltípusok megnyitása megtekintésre.  |
| Hivatkozás másolása | é | A vágólapra mutató fájlra mutató hivatkozás tmásolja. |
| Futtatás a Jupyter Laborban | J | Fut egy notebook JupyterLab, amely egy fejlesztő-orientált felület, mint Jupyter általában rendelkezik. |
| Előzetes verzió | P | Megnyitja a fájl HTML-előnézetét; a jegyzetfüzetek esetében az előnézet a jegyzetfüzet írásvédett renderelése. További információt az [Előnézet](#preview) című részben talál. |
| Fájl szerkesztése | i | Megnyitja a fájlt szerkesztésre. |
| Letöltés | n | Letölt egy zip fájlt, amely tartalmazza a fájlt vagy egy mappa tartalmát. |
| Átnevezés | a | Új nevet kér a fájlhoz vagy mappához. |
| Törlés | x | Megerősítést kér, majd véglegesen eltávolítja a fájlt a projektből. A törlés nem állítható vissza. |
| Áthelyezés | m | Fájl áthelyezése ugyanabban a projektben egy másik mappába. |

#### <a name="preview"></a>Előzetes verzió

A fájl vagy jegyzetfüzet előnézete a tartalom írásvédett nézete; a futó jegyzetfüzetcellák le vannak tiltva. Az előzetes verzió mindenki számára megjelenik, aki rendelkezik a fájlra vagy a jegyzetfüzetre mutató hivatkozással, de nem jelentkezett be az Azure Notebooks-ba. Miután bejelentkezett, a felhasználó klónozhatja a jegyzetfüzetet a saját fiókjába, vagy letöltheti a jegyzetfüzetet a helyi számítógépre.

Az előnézeti lap számos billentyűparancstal rendelkező eszköztárparancsot támogat:

| Parancs | Billentyűparancsok | Műveletek |
| --- | --- | --- |
| Megosztás | s | Megjeleníti azt a megosztási előugró ablakot, amelyből beszerezhet egy hivatkozást, megoszthatja a közösségi médiát, HTML-kódot kaphat beágyazáshoz, és e-mailt küldhet. |
| Klónozás | c  | Klónozza a jegyzetfüzetet a fiókjába. |
| Futtassa a következőt: | r | Futtatja a jegyzetfüzetet, ha erre lehetősége van. |
| Letöltés | n | Letölti a jegyzetfüzet egy példányát. |

## <a name="configure-the-project-environment"></a>A projektkörnyezet konfigurálása

Háromféleképpen konfigurálhatja annak az alapul szolgáló virtuális gépnek a környezetét, amelyben a jegyzetfüzetek futnak:

- Egyszeri inicializálási parancsfájl felvétele
- A projekt környezeti beállításainak használata a telepítési lépések megadásához
- A virtuális gép elérése terminálon keresztül.

A projektkonfiguráció minden formáját alkalmazza a rendszer a virtuális gép indításakor, és így hatással van a projekten belüli összes jegyzetfüzetre.

### <a name="one-time-initialization-script"></a>Egyszeri inicializálási parancsfájl

Az első alkalommal Az Azure Notebooks létrehoz egy kiszolgálót a projekthez, megkeresi a aznbsetup.sh *nevű*fájlt a projektben. Ha ez a fájl jelen van, az Azure Notebooks futtatja azt. A parancsfájl kimenete a projektmappában *.aznbsetup.log*néven tárolódik.

### <a name="environment-setup-steps"></a>A környezet beállításának lépései

A projekt környezeti beállításaival egyéni lépéseket hozhat létre a környezet konfigurálására.

A projekt irányítópultján válassza a **Projektbeállítások**lehetőséget, majd a **Környezet** lapot, amelybe hozzáadja, eltávolítja és módosítja a projekt beállítási lépéseit:

![A Projektbeállítások előugró ablaka kijelölt Környezet lap](media/project-settings-environment-steps.png)

Lépés hozzáadásához először válassza a **+ Hozzáadás**lehetőséget, majd válasszon egy lépéstípust a **Művelet** legördülő listában:

![Műveletválasztó egy új környezeti beállítási lépéshez](media/project-settings-environment-details.png)

A projektben szereplő információk a választott művelet típusától függnek:

- **Requirements.txt**: A második legördülő listában válassza ki a projektben már szereplő *requirements.txt* fájlt. Ezután válassza ki a Python-verziót a megjelenő harmadik legördülő listából. Egy *requirements.txt* fájl használatával az `pip install -r` Azure Notebooks fut a *requirements.txt* fájl indításakor egy notebook-kiszolgáló. Nem kell explicit módon telepíteni a csomagokat a notebook on belül is.

- **Shell script**: A második legördülő listában válasszon ki egy bash shell parancsfájlt a projektben (általában egy *.sh* kiterjesztésű fájlt), amely tartalmazza a környezet inicializálásához futtatni kívánt parancsokat.

- **Environment.yml**: A második legördülő listában jelöljön ki egy *környezet.yml* fájlt a Python-projektekhez conda környezetben.

   > [!WARNING]
   > Mivel ez egy fejlesztés alatt álló előzetes verziójú `Environment.yml` szolgáltatás, jelenleg van egy ismert probléma, ahol a beállítás nem a várt módon lesz alkalmazva a projektre. A projekt és a Jupyter-jegyzetfüzetek jelenleg nem töltik be a megadott környezeti fájlt.

Ha befejezte a lépések hozzáadását, válassza a **Mentés gombot.**

### <a name="use-the-terminal"></a>A terminál használata

A projekt irányítópultján a **Terminál** parancs megnyit egy Linux terminált, amely közvetlen hozzáférést biztosít a kiszolgálóhoz. A terminálon belül adatokat tölthet le, fájlokat szerkeszthet vagy kezelhet, folyamatokat vizsgálhat, és akár olyan eszközöket is használhat, mint a vi és a nano.

> [!Note]
> Ha a projekt környezetében indítási parancsfájlok vannak, előfordulhat, hogy a terminál megnyitása koronként üzenetet jelenít meg, amely jelzi, hogy a telepítés még folyamatban van.

Bármilyen szabványos Linux parancsot kiadhat a terminálon. `ls` A kezdőmappában a virtuális gépen található különböző környezetek , például *a anaconda2_501, a* *anaconda3_420,* a *anaconda3_501*, *az IfSharp*és az *R,* valamint a projektet tartalmazó *projektmappa* is megtekinthetők:

![Projektterminál az Azure-jegyzetfüzetekben](media/project-terminal.png)

Ha egy adott környezetre szeretne hatni, először módosítsa a könyvtárakat abba a környezeti mappába.

A Python-környezetek, `pip` megtalálja, `conda` és a *bin* mappában az egyes környezetekben. A környezetekben is használhat beépített aliasokat:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

A kiszolgálón végrehajtott módosítások csak az aktuális munkamenetre vonatkoznak, kivéve a *projektmappában* létrehozott fájlokat és mappákat. Például a projektmappában lévő fájl szerkesztése megmarad a `pip install` munkamenetek között, de a csomagokkal nem.

> [!Note]
> Ha a `python` `python3`vagy , meghívja a rendszer által telepített verziói Python, amelyek nem használják a notebookok. Egyikhöz hasonló `pip install` műveletekhez nincs engedélye, ezért ügyeljen arra, hogy a verzióspecifikus aliasokat használja.

## <a name="access-notebook-logs"></a>Jegyzetfüzet-naplók elérése

Ha a jegyzetfüzet futtatásakor problémákba ütközik, a Jupyter kimenete egy *.nb.log*nevű mappában lesz tárolva. Ezeket a naplókat a **Terminál** parancsvagy a projekt irányítópultján keresztül érheti el.

A Jupyter helyi futtatásakor általában előfordulhat, hogy egy terminálablakból indította el. A terminálablak olyan kimeneteket jelenít meg, mint például a kernel állapota.

A naplók megtekintéséhez használja a következő parancsot a terminálon:

```bash
cat .nb.log
```

A python-jegyzetfüzetek kódcellájából származó parancs is használható:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>További lépések

- [Útmutató: Projektadatfájlok kalvaló megoldása](work-with-project-data-files.md)
- [Felhőbeli adatok elérése egy jegyzetfüzetben](access-data-resources-jupyter-notebooks.md)
