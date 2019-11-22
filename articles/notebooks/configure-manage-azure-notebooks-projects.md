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

Azure notebookok a mögöttes virtuális gép elindul, ha a jegyzetfüzet vagy más fájl futtatását. A kiszolgáló automatikusan menti a fájlokat, és 60 perc inaktivitás után leáll. A kiszolgáló is bármikor leállíthatja a **leállítási** parancsot (billentyűparancs: h).

## <a name="compute-tier"></a>Számítási szintek

Alapértelmezés szerint a projektek az **ingyenes számítási** szinten futnak, amely legfeljebb 4 GB memóriával és 1 GB-nyi adattal használható a visszaélések megelőzése érdekében. Megkerülheti ezeket a korlátozásokat, és növelheti a számítási teljesítményt az Azure-előfizetésben üzembe helyezett másik virtuális géppel. További információ: az [Adatelemzési Virtual Machines használata](use-data-science-virtual-machine.md).

## <a name="edit-project-metadata"></a>Projekt metaadatainak szerkesztése

A projekt irányítópultján válassza ki a **Projektbeállítások**, majd válassza ki a **információk** fülre, amely a projekt metaadatot tartalmaz, az alábbi táblázatban leírtak szerint. Projekt metaadatok bármikor módosíthatja.

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

A **+ új** parancsot (billentyűparancs: n) hoz létre az új fájlokat vagy mappákat. A parancs használatakor először válassza ki a létrehozandó elem típusát:

| Elemtípus | Leírás | A parancs viselkedését |
| --- | --- | --- |
| **Notebook** | Jupyter notebook | Egy előugró ablak, amelyben adja meg a notebook fájlnévvel és a nyelvet jeleníti meg. |
| **Mappa** | Almappa | Létrehoz egy szerkesztőmezőben, amelybe, adja meg a mappa nevét a projekt-fájlok listája. |
| **Üres fájl** | Egy fájlt, amelybe tárolhatja bármilyen tartalmat, például szöveg, adatok, stb. | Létrehoz egy szerkesztőmezőben, amelybe a fájlnév megadása a projekt-fájlok listája. |
| **Markdown** | Egy Markdown-fájlt. | Létrehoz egy szerkesztőmezőben, amelybe a fájlnév megadása a projekt-fájlok listája. |

### <a name="upload-files"></a>Fájlok feltöltése

A **feltöltése** parancs adatok importálása más két lehetőséget biztosít: **URL-CÍMRŐL** és **a számítógép**. További információkért lásd: [használata az adatfájlokat az Azure-jegyzetfüzet projektek](work-with-project-data-files.md).

### <a name="select-file-specific-commands"></a>Válassza ki a fájl-specifikus parancsok

A projekt fájl lista minden eleme egy kattintson a jobb gombbal a helyi menü keresztül parancsokat kínálja:

![Egy fájl helyi menü parancsai](media/project-file-commands.png)

| Parancs | Billentyűparancs | Műveletek |
| --- | --- | --- |
| Futtassa a következőt: | az r (vagy kattintson) | A notebook fájl fut. Más fájltípusok megtekintésre nyílnak meg.  |
| Hivatkozás másolása | y | Másolja a vágólapra a fájl egy hivatkozást. |
| Futtatása a Jupyter-tesztkörnyezet | J | Fut egy jegyzetfüzetet JupyterLab, azaz Jupyter általában biztosít, mint egy több fejlesztő-orientált felületet. |
| Előzetes verzió | p | Megnyílik egy HTML-előnézetet a fájl; az előzetes verzió notebookokat, a jegyzetfüzet egy csak olvasható leképezési. További információkért lásd: a [előzetes](#preview) szakaszban. |
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

Az első Azure Notebooks létrehoz egy kiszolgálót a projekthez, amely a *aznbsetup.sh*nevű projektben keres egy fájlt. Ha a fájl megtalálható, Azure Notebooks futtatja. A szkript kimenetének tárolva van, a projektmappa fájllistájának *. aznbsetup.log*.

### <a name="environment-setup-steps"></a>Környezet beállítási lépéseket

A projekt környezeti beállítások segítségével hozzon létre az egyes lépések, amelyek a környezet beállításához.

A projekt irányítópultján válassza ki a **Projektbeállítások**, majd válassza ki a **környezet** lap, amelyben hozzáadja, távolítsa el, és módosítsa a projekt beállítási lépéseket:

![Projekt beállítások felugró kiválasztott környezet lap](media/project-settings-environment-steps.png)

Adjon hozzá egy lépést, először válassza **+ Hozzáadás**, majd válassza ki a lépés típusát, a a **művelet** legördülő listából választhatja ki:

![Az új környezet beállítása lépés műveletet-választó](media/project-settings-environment-details.png)

Az adatokat, majd a projekt választott művelet típusától függ:

- **A Requirements.txt**: a második legördülő listában válassza ki a *requirements.txt* fájlt, amely a projektben már van. Ezután válassza ki a harmadik legördülő listában megjelenő egy Python-verzió. Használatával egy *requirements.txt* fájlt, az Azure-jegyzetfüzetek futtatása `pip install -r` együtt a *requirements.txt* fájl a notebook server indítása során. Nincs explicit módon telepíteni szeretné a notebook magát a csomagok.

- **Héjszkript**: a második legördülő listában válassza ki a projekt bash héjparancsfájl (általában egy fájl a *.sh* kiterjesztéssel), amely tartalmazza a környezet inicializálása futtatni kívánt parancs.

- **Environment.yml**: a második legördülő listában válassza ki egy *environments.yml* fájl Pro projekty v Pythonu egy conda-környezetben.

Ha elkészült a lépések hozzáadásával, válassza ki a **mentése**.

### <a name="use-the-terminal"></a>A terminál használata

A projekt irányítópultján a **terminál** parancs nyit meg, amely közvetlen hozzáférést ad a kiszolgáló egy Linux-terminálba. A terminálban belül, is adatok letöltése, szerkesztése vagy fájlok kezelése, vizsgálja meg a folyamatok és is használhatja, vi és nano.

> [!Note]
> Ha az indítási parancsfájlok a projekt környezetében, nyissa meg a terminált megjeleníthetnek egy üzenetet, jelezve, hogy a telepítő még folyamatban van.

Minden standard szintű Linux-parancsok billentyűparancsot a terminálon adhat ki. Is `ls` megtekintéséhez a különböző környezetekben, például a virtuális gépen létező kezdőmappát *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp*, és *R*, valamint egy *projekt* projektet tartalmazó mappa:

![Projekt Terminálszolgáltatások Azure-jegyzetfüzetekben](media/project-terminal.png)

Befolyásolják egy adott környezetben, módosítsa a könyvtárakat a környezet mappába először.

A Python-környezetek esetén annak `pip` és `conda` a *bin* mappában található minden környezethez. Beépített aliasok a környezetek esetében is használhatja:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

A kiszolgáló módosításai csak az aktuális munkamenethez, kivéve a fájlokat és mappákat hoz létre a alkalmazni a *projekt* magát mappát. Például egy fájlt a projektmappa fájllistájának szerkesztési rendszer megőrzi a munkamenetek között, de csomagok `pip install` nem.

> [!Note]
> Ha `python` vagy `python3`, Python, nem használt notebookokhoz rendszert telepített verziók indít el. Nincs engedélye műveleteket hasonló `pip install` vagy, ezért ügyeljen arra, hogy a verzió-specifikus aliasok.

## <a name="access-notebook-logs"></a>A jegyzetfüzet-naplók elérése

A notebook futtatásakor felmerülő hibák, Jupyterről származó kimeneti tárolódnak a nevű mappa *. nb.log*. Ezek a naplók keresztül érheti el a **terminálon** parancsot vagy a projekt irányítópultján.

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

- [Útmutató: adatok soubory projektu használata](work-with-project-data-files.md)
- [Hozzáférés felhőbeli adatok történő használatát](access-data-resources-jupyter-notebooks.md)
