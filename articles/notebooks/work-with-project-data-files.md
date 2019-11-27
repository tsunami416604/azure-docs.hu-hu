---
title: Az Azure-jegyzetfüzeteket a projektek adatok importálása és exportálása
description: Hogyan lehet egy Azure-jegyzetfüzetek projekt külső forrásból származó adatokat importálnak, és a egy projektet az adatok exportálása.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: bd7ba27859e9d05c0d57c2f78b6449c2bc48ca33
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277382"
---
# <a name="work-with-data-files-in-azure-notebook-projects"></a>Az adatfájlokat az Azure-jegyzetfüzet projektek használata

Adatok a lifeblood, számos, a Jupyter notebooks, különösen akkor használja az adatelemzéshez notebookok el. Az Azure-jegyzetfüzetek egyszerűen importálhatja a különböző forrásokból egy projektbe, és majd az adatokat a notebookok. Jegyzetfüzetek létrehozása a projektben, majd töltheti használatra máshol tárolt adatokat is rendelkezhet.

A futó jegyzetfüzetben található **adatok** menü **feltöltési** és **letöltési** parancsokat is biztosít, amelyek a projekt fájljaival, valamint az aktuális jegyzetfüzet-munkamenet ideiglenes fájljaival működnek.

Is segítségével kód egy jegyzetfüzetet belül, közvetlenül hozzáférni az adatforrások széles többek között a fájlok egy projektben. Emellett tetszőleges adatokat egy kódcellába parancsok használatával. Mivel az ilyen adatok tárolása változókban jegyzetfüzet-munkameneten belül, azt nem menthető a projektben, kivéve, ha a kifejezetten a projektfájlok kódot használja.

A kód az adatban való használata a legalkalmasabb egy futó jegyzetfüzeten belül: erre a célra tekintse át az [adatait a Azure Notebooks minta jegyzetfüzetben](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

Ez a cikk további részében projekt szintű fájlműveletek részletesen.

## <a name="import-data"></a>Adatok importálása

A fájlokat a projekt irányítópultján vagy egy futó jegyzetfüzetben, az **adatok** menüből vagy egy olyan paranccsal hozhatja be a projektbe, mint a `curl`.

### <a name="import-files-from-the-project-dashboard"></a>Fájlok importálása a projekt-irányítópult

1. A projektben keresse meg a mappát, ahol a fájlokat importálni szeretné.

1. Válassza ki a **feltöltés** parancsot, majd az **URL** -címről vagy a **számítógépről** , és a projekthez az importálni kívánt adatokhoz szükséges adatokat:

   - **Feladó URL-** címe: írja be a forrás címét a **fájl URL** -címe mezőbe, és a **Fájlnév mezőben a** projektben lévő jegyzetfüzethez rendelendő fájlnevet. Ezután válassza a **+ fájl hozzáadása** lehetőséget, és adja hozzá az URL-címet a feltöltési listához. Ismételje meg a folyamatot bármely további URL-cím esetében, majd válassza a **kész**lehetőséget.

     ![Töltse fel az URL-cím előugró ablak](media/quickstarts/upload-from-url-popup.png)

   - **Számítógépről**: húzza a fájlokat a felugró ablakba, vagy válassza a **fájlok kiválasztása**lehetőséget, majd keresse meg és válassza ki az importálni kívánt adatfájlokat. Dobja el, vagy fájlokat, bármilyen típusú és formátumú tetszőleges számú választható, mert a kódhoz a notebook nyissa meg a fájlt, és elemezni az adatokat a szolgáltatás.

     ![Töltse fel a számítógép helyi menü](media/quickstarts/upload-from-computer-popup.png)

1. Importálás után a notebook kód tartalmazó mappába relatív forráskódfájlok használatával érhető el, és fájlokat a projekt irányítópultján jelenik meg.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Fájlok importálása egy jegyzetfüzet fájl menüjéből

1. Egy futó jegyzetfüzetben válassza a **fájl** > **feltöltés** parancsot:

    ![A fájl feltöltése menü parancs egy jegyzetfüzetet belül](media/file-menu-upload.png)

1. A megnyíló párbeszédpanelen keresse meg és válassza ki a feltölteni kívánt fájlokat. Kiválaszthatja, hogy bármilyen fájltípus tetszőleges számú. Ha elkészült, válassza a **Megnyitás** lehetőséget.

1. A megjelenő **feltöltési állapot** előugró ablakban válasszon ki egy **célmappát** a legördülő listából:

    - Munkamenet mappája ( *~/* ): fájlokat tölt fel az aktuális jegyzetfüzet-munkamenetbe, de nem hoz létre fájlokat a projektben. A munkamenet mappa társ a mappájára, de nem tárol, miután a munkamenet azért ér véget. Ha a munkamenet-fájlokat a kódban szeretné elérni, előtagként a fájlneveket a relatív elérési úttal *. /* .

        A munkamenet mappa használata Kísérletezési hasznos, és elkerülhető, hogy a projekt elárasztanák rendelkező fájlokat is, illetve előfordulhat, hogy nem kell a hosszú távon. A munkamenet-mappába, amelyeket azonos nevek a projekt fájlokat anélkül, hogy ez ütközéseket, és nevezze át a fájlokat anélkül is feltölthet fájlokat. Tegyük fel például, hogy a projektben már van egy, a *. csv fájl* egy verziója, de a *. csv fájl*egy másik verziójával szeretne kísérletezni. A fájlnak a munkamenet mappájába való feltöltésével a feltöltött fájlban lévő adat használatával futtathatja a jegyzetfüzetet (ez a következővel hivatkozik a kódban: *.. /Data.csv*), nem pedig a projekt fájljában található adatértékeket.

    - Project Folder ( */Project*): fájlok feltöltése a projektbe, ahol a kód relatív elérési útjainak használatával érhetők el. Ha fájlt tölt fel a mappa ugyanaz, mint a projekt irányítópultján fájlt feltölteni. A fájl is mentve lesz a projektet, és későbbi munkamenetek érhető el.

        Ha megpróbálja feltölteni egy fájlt a projektben már létezik egy azonos nevű, feltöltése sikertelen. Felülírja a fájlt, töltse fel az új fájlt a projekt irányítópultján, amely lehetővé teszi felülírásához.

1. A folyamat befejezéséhez válassza a **feltöltés megkezdése** lehetőséget.

### <a name="create-or-import-files-using-commands"></a>Hozzon létre vagy importáljon parancsokkal fájlok

Használhatja a parancsokat egy terminált vagy a Python kódcella belül a projekt és a munkamenet-fájlok létrehozása. Például a `curl` és az `wget` közvetlenül az internetről töltheti le a fájlokat.

Ha fájlokat szeretne letölteni a terminálon, válassza a **terminál** parancsot a projekt irányítópultján, majd adja meg a megfelelő parancsokat:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Ha egy jegyzetfüzetben Python-kód cellát használ, a parancsokat a `!`.

A Project mappa az alapértelmezett mappa, ezért a cél fájlnevének (például *oil_price. csv)* megadásával létrehozza a fájlt a projektben. Egy munkamenet-fájl létrehozásához előtagként adja meg a nevet *. /* a-ben *. /oil_price. csv*.

### <a name="create-files-in-code"></a>Fájlok létrehozása a code-ban

Ha olyan kódot használ, amely létrehoz egy fájlt, például a pandák `write_csv` függvényt, az elérési út mindig a projekt mappájához viszonyítva van. A *.. /* létrehoz egy, a jegyzetfüzet leállításakor és bezárásakor elvetett munkamenet-fájlt.

## <a name="export-files"></a>Fájlok exportálása

A projekt irányítópultján vagy belül is exportálhat adatokat egy notebookot.

## <a name="export-files-from-the-project-dashboard"></a>A projekt irányítópultján fájlok exportálása

A projekt Irányítópultján kattintson a jobb gombbal a fájlra, és válassza a **Letöltés**lehetőséget:

![Töltse le a parancs a projekt helyi menüje](media/download-command.png)

Kiválaszthat egy fájlt is, és használhatja a **Letöltés** parancsot (billentyűparancs: d) az irányítópulton:

![Töltse le az eszköztár parancs a projekt-irányítópult](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Az adatok menüből jegyzetfüzetlapot fájlok exportálása

1. Válassza ki a **fájl** > **letöltési** menü parancsát:

    ![Adatok letöltése parancs egy jegyzetfüzetet belül](media/file-menu-download.png)

1. Megjelenik egy előugró ablak, amely megjeleníti a munkamenetben lévő mappákat. a *Project* mappában található projektfájlok a következőket tartalmazzák:

    ![Adatok letöltése paranccsal előugró fájlok és mappák kiválasztása](media/file-menu-download-popup.png)

1. Jelölje be a letölteni kívánt fájlok és mappák bal oldalán található jelölőnégyzeteket, majd válassza a **kijelöltek letöltése**lehetőséget.

1. A jegyzetfüzet előkészít egy *. zip* fájlt, amely tartalmazza a kiválasztott fájlokat, amelyeket aztán a böngészőből szokásos módon menthet. A jegyzetfüzet egyetlen fájl letöltésekor is létrehoz egy *. zip* fájlt.

## <a name="next-steps"></a>Következő lépések

- [Felhőbeli adat elérése jegyzetfüzetben](access-data-resources-jupyter-notebooks.md)
