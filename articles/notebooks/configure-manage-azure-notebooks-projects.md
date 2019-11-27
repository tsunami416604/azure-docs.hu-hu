---
title: Konfigurálhatja és kezelheti az Azure-jegyzetfüzet-projektek
description: Hogyan kezelheti a projekt metaadatok, soubory projektu, a projekt környezet és a beállítási lépéseket az Azure notebookok felhasználói felület és a terminál közvetlen hozzáférést.
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: 56c265122894412e79b3d5a7b256964c49ab81a6
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277642"
---
# <a name="manage-and-configure-projects"></a>Projektek kezelése és konfigurálása

A projekt Azure notebookok lényegében a mögöttes Linux rendszerű virtuális gép, amelyben Jupyter notebookok futtatja, a fájl mappa és a leíró metaadatok együtt egy konfigurációs. Az Azure-jegyzetfüzetekben projekt-irányítópult lehetővé teszi a fájlok kezelése és egyéb konfigurálása a projekt jellemzői:

- A projekt futtatásának számítási szintje, amely lehet az ingyenes vagy az Azure-beli virtuális gép.
- A projekt metaadatai, beleértve a nevet, a leírást, a projekt megosztásakor használt azonosítót, valamint azt, hogy a projekt nyilvános vagy privát.
- A projekt notebook-, adat-és egyéb fájljai, amelyeket más fájlrendszerhez hasonlóan kezel.
- Egy projekt környezete, amelyet indítási parancsfájlok vagy közvetlenül a terminálon keresztül kezelhet.
- A terminálon keresztül hozzáférő naplók.

> [!Note]
> Az itt ismertetett kezelési és konfigurációs funkciók csak a projekt tulajdonosa számára érhetők el, aki eredetileg létrehozta a projektet. A projektet azonban saját fiókjába is bemásolhatja, ebben az esetben Ön lesz a tulajdonos, és igény szerint konfigurálhatja a projektet.

Azure notebookok a mögöttes virtuális gép elindul, ha a jegyzetfüzet vagy más fájl futtatását. A kiszolgáló automatikusan menti a fájlokat, és 60 perc inaktivitás után leáll. A kiszolgálót bármikor leállíthatja a **leállítási** parancs használatával (billentyűparancs: h).

## <a name="compute-tier"></a>Számítási szintek

Alapértelmezés szerint a projektek az **ingyenes számítási** szinten futnak, amely legfeljebb 4 GB memóriával és 1 GB-nyi adattal használható a visszaélések megelőzése érdekében. Megkerülheti ezeket a korlátozásokat, és növelheti a számítási teljesítményt az Azure-előfizetésben üzembe helyezett másik virtuális géppel. További információ: az [Adatelemzési Virtual Machines használata](use-data-science-virtual-machine.md).

## <a name="edit-project-metadata"></a>Projekt metaadatainak szerkesztése

A projekt irányítópultján válassza a **projekt beállításai**lehetőséget, majd válassza az **információ** fület, amely tartalmazza a projekt metaadatait a következő táblázatban leírtak szerint. Projekt metaadatok bármikor módosíthatja.

| Beállítás | Leírás |
| --- | --- |
| Projektnév | Egy rövid nevet a projekthez, amely az Azure-jegyzetfüzetek megjelenítési célokra használja. Például "Hello World a Python". |
| Projektazonosító | Egyéni azonosítója, amely a projekt megosztása használatával URL-cím részévé válik. Ez az azonosító csak betűket, számokat és kötőjeleket használhat, legfeljebb 30 karakterből állhat, és nem lehet [foglalt projekt-azonosító](create-clone-jupyter-notebooks.md#reserved-project-ids). Ha Ön nem tudja, hogy melyiket érdemes használni, a common konvenciónak, hogy a projekt neve kisbetűs verzióját, szóközöket, kötőjeleket, például a "my-jegyzetfüzet-projekt" (ha szükséges, hogy illeszkedjen a hosszra vonatkozó korlátot csonkolt) vannak kapcsolva. |
| Nyilvános projekt | Ha a beállítása, lehetővé teszi, hogy bárki a hivatkozást a projekt eléréséhez. Privát projekt létrehozásakor törölje ezt a beállítást. |
| Klónok elrejtése | Ha a beállított, más felhasználók nem látják, amelyek a projekt klónok listáját. Elrejtés klónok akkor hasznos, ha sok számára, akiknek nem részei ugyanazon a szervezeten belül, például megosztott projektek a pedagógiai osztály a notebook használatakor. |

> [!Important]
>
> A projekt Azonosítóját módosítása érvényteleníti a projektet, előfordulhat, hogy megosztott korábban mutató hivatkozásokat.

## <a name="manage-project-files"></a>Soubory projektu kezelése

A projekt irányítópultján a projekt mapparendszer tartalmát jeleníti meg. Különböző parancsok segítségével kezelheti ezeket a fájlokat.

### <a name="create-new-files-and-folders"></a>Új fájlok és mappák létrehozása

Az **+ új** parancs (billentyűparancs: n) új fájlokat vagy mappákat hoz létre. A parancs használatakor először válassza ki a létrehozandó elem típusát:

| Elemtípus | Leírás | A parancs viselkedését |
| --- | --- | --- |
| **Notebook** | Jupyter notebook | Egy előugró ablak, amelyben adja meg a notebook fájlnévvel és a nyelvet jeleníti meg. |
| **Mappa** | Almappa | Létrehoz egy szerkesztőmezőben, amelybe, adja meg a mappa nevét a projekt-fájlok listája. |
| **Üres fájl** | Egy fájlt, amelybe tárolhatja bármilyen tartalmat, például szöveg, adatok, stb. | Létrehoz egy szerkesztőmezőben, amelybe a fájlnév megadása a projekt-fájlok listája. |
| **Markdown** | Egy Markdown-fájlt. | Létrehoz egy szerkesztőmezőben, amelybe a fájlnév megadása a projekt-fájlok listája. |

### <a name="upload-files"></a>Fájlok feltöltése

A **feltöltési** parancs két lehetőséget kínál az adatok más helyekről történő importálására: **az URL** -címről és **a számítógépről**. További információ: [adatfájlok használata az Azure notebook projects](work-with-project-data-files.md)szolgáltatásban.

### <a name="select-file-specific-commands"></a>Válassza ki a fájl-specifikus parancsok

A projekt fájl lista minden eleme egy kattintson a jobb gombbal a helyi menü keresztül parancsokat kínálja:

![Egy fájl helyi menü parancsai](media/project-file-commands.png)

| Parancs | Billentyűparancs | Műveletek |
| --- | --- | --- |
| Futtassa a következőt: | az r (vagy kattintson) | A notebook fájl fut. Más fájltípusok megtekintésre nyílnak meg.  |
| Hivatkozás másolása | y | Másolja a vágólapra a fájl egy hivatkozást. |
| Futtatása a Jupyter-tesztkörnyezet | J | Fut egy jegyzetfüzetet JupyterLab, azaz Jupyter általában biztosít, mint egy több fejlesztő-orientált felületet. |
| Előzetes verzió | p | Megnyílik egy HTML-előnézetet a fájl; az előzetes verzió notebookokat, a jegyzetfüzet egy csak olvasható leképezési. További információ: [előnézet](#preview) szakasz. |
| Fájl szerkesztése | I | Megnyitja a fájlt szerkesztésre. |
| Letöltés | d | Letölti a fájl vagy mappa tartalmát tartalmazó zip-fájlt. |
| Átnevezés | a | Egy új nevet a fájlhoz vagy mappához tartozó utasításokat. |
| Törlés | x | Megerősítést kér, majd véglegesen eltávolítja a fájlt a projektben. Törlés nem vonható vissza. |
| Áthelyezés | p | A fájl ugyanabban a projektben egy másik mappába helyezi át. |

#### <a name="preview"></a>Előzetes verzió

Előzetes verziója egy fájl vagy a jegyzetfüzet; tartalma írásvédett nézete notebook cellák fut le van tiltva. Mindenki számára, aki rendelkezik a fájl vagy a jegyzetfüzet mutató hivatkozást, de nem jelentkezett be az Azure-jegyzetfüzetek előnézete látható. Miután bejelentkezett, a felhasználó a saját fiókhoz a notebookot klónozhat, vagy a jegyzetfüzet letölthetik a helyi számítógépre.

Az előzetes verziójú szolgáltatásainak weblapján különböző eszköztár parancsokat a billentyűparancsok használatát támogatja:

| Parancs | Billentyűparancs | Műveletek |
| --- | --- | --- |
| Megosztás | s | Megjeleníti a megosztási előugró ablak, amelyről hivatkozás beszerzése, közösségi megosztás, HTML beszerzése a beágyazáshoz és e-mail küldése. |
| Klónozás | c  | Klónozza a fiókhoz a notebookot. |
| Futtassa a következőt: | R | Ha Ön jogosult-e ehhez a notebook fut. |
| Letöltés | d | A notebook másolatát tölti le. |

## <a name="configure-the-project-environment"></a>A project-környezet konfigurálása

A mögöttes virtuális gép saját jegyzetfüzetek futtatásához, amelyben a környezet beállításához három módja van:

- Egy egyszeri inicializációs szkriptet tartalmazza.
- A projekt környezeti beállítások segítségével adja meg a beállítási lépéseket
- Egy terminál keresztül érik el a virtuális gépet.

A projektkonfiguráció minden formája, amikor a virtuális gép elindul, és így hatással van az összes jegyzetfüzet a projekten belül érvényesek.

### <a name="one-time-initialization-script"></a>Egyszeri inicializálási parancsfájlja

Az első Azure Notebooks létrehoz egy kiszolgálót a projekthez, amely a *aznbsetup.sh*nevű projektben keres egy fájlt. Ha a fájl megtalálható, Azure Notebooks futtatja. A parancsfájl kimenetét a Project mappában, a *. aznbsetup. log*néven tárolja a rendszer.

### <a name="environment-setup-steps"></a>Környezet beállítási lépéseket

A projekt környezeti beállítások segítségével hozzon létre az egyes lépések, amelyek a környezet beállításához.

A projekt irányítópultján válassza a **projekt beállításai**lehetőséget, majd válassza a **környezet** fület a projekt telepítési lépéseinek hozzáadásához, eltávolításához és módosításához:

![Projekt beállítások felugró kiválasztott környezet lap](media/project-settings-environment-steps.png)

Lépés hozzáadásához először válassza a **+ Hozzáadás**lehetőséget, majd válassza ki a lépés típusát a **művelet** legördülő listában:

![Az új környezet beállítása lépés műveletet-választó](media/project-settings-environment-details.png)

Az adatokat, majd a projekt választott művelet típusától függ:

- **Követelmények. txt**: a második legördülő listában válasszon ki egy, a projektben már szereplő *követelmény. txt* fájlt. Ezután válassza ki a harmadik legördülő listában megjelenő egy Python-verzió. A *követelmények. txt* fájl használatával Azure Notebooks a notebook-kiszolgáló indításakor a *követelmények. txt* fájllal futtatja a `pip install -r`. Nincs explicit módon telepíteni szeretné a notebook magát a csomagok.

- **Rendszerhéj-parancsfájl**: a második legördülő listában válasszon ki egy bash rendszerhéj-parancsfájlt a projektben (jellemzően egy *. sh* kiterjesztésű fájl), amely a környezet inicializálásához futtatni kívánt parancsokat tartalmazza.

- **Environment. YML**: a második legördülő listában válasszon ki egy *környezetet. YML* -fájlt a Python-projektekhez Conda-környezet használatával.

Ha végzett a lépések hozzáadásával, válassza a **Mentés**lehetőséget.

### <a name="use-the-terminal"></a>A terminál használata

A projekt irányítópultján a **Terminal** parancs egy Linux-terminált nyit meg, amely közvetlen hozzáférést biztosít a kiszolgálóhoz. A terminálban belül, is adatok letöltése, szerkesztése vagy fájlok kezelése, vizsgálja meg a folyamatok és is használhatja, vi és nano.

> [!Note]
> Ha az indítási parancsfájlok a projekt környezetében, nyissa meg a terminált megjeleníthetnek egy üzenetet, jelezve, hogy a telepítő még folyamatban van.

Minden standard szintű Linux-parancsok billentyűparancsot a terminálon adhat ki. A Kezdőlap mappában található `ls` is megtekintheti a virtuális gépen létező különböző környezeteket, például a *anaconda2_501*, a *anaconda3_420*, a *anaconda3_501*, a *IfSharp*és az *R*-t, valamint a projektet tartalmazó *Project* mappát:

![Projekt Terminálszolgáltatások Azure-jegyzetfüzetekben](media/project-terminal.png)

Befolyásolják egy adott környezetben, módosítsa a könyvtárakat a környezet mappába először.

A Python-környezetek esetében az egyes környezetekhez tartozó *bin* mappában `pip` és `conda` található. Beépített aliasok a környezetek esetében is használhatja:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

A kiszolgálón végrehajtott módosítások csak az aktuális munkamenetre érvényesek, kivéve a *projekt* mappában létrehozott fájlokat és mappákat. Például a Project mappában található fájl szerkesztése a munkamenetek között megmarad, de a `pip install` csomagok nem.

> [!Note]
> Ha `python` vagy `python3`használ, meghívja a Python rendszer telepített verzióit, amelyek nem használatosak jegyzetfüzetekhez. Nincs engedélye olyan műveletekhez, mint a `pip install` vagy, ezért ügyeljen arra, hogy a verzióra vonatkozó aliasokat használja.

## <a name="access-notebook-logs"></a>A jegyzetfüzet-naplók elérése

Ha a jegyzetfüzetek futtatása során problémákba ütközik, a Jupyter kimenetét egy *. NB. log*nevű mappában tárolja a rendszer. Ezeket a naplókat a **Terminal** parancs vagy a projekt irányítópultja segítségével érheti el.

Általában amikor helyileg futtatja a Jupyter, előfordulhat, hogy megkezdte azt egy terminálablakból. A terminálablakban látható kimenet például a kernel állapotát.

A naplók megtekintéséhez használja a következő parancsot a terminálon:

```bash
cat .nb.log
```

A parancs egy Python-jegyzetfüzetet egyik kódcellájába is használja:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Következő lépések

- [Útmutató: Project-adatfájlok használata](work-with-project-data-files.md)
- [Felhőbeli adat elérése jegyzetfüzetben](access-data-resources-jupyter-notebooks.md)
