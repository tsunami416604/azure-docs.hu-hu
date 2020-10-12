---
title: Adatimportálás és-exportálás Azure Notebooks előzetes verzióval rendelkező projektekkel
description: Megtudhatja, hogyan vihet be az adatok egy Azure Notebooks Preview-projektbe külső forrásokból, és hogyan exportálhatók az adatok egy projektből.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: b3669128582d3bdd6a3c4506a040856ab7b07e9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85834114"
---
# <a name="work-with-data-files-in-azure-notebooks-preview-projects"></a>Adatfájlok használata Azure Notebooks előzetes verziójú projektekben

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Az adat a sok Jupyter-jegyzetfüzet, különösen az adatelemzéshez használt jegyzetfüzetek éltető eleme. A Azure Notebooks segítségével könnyedén importálhat különböző forrásokból egy projektbe, majd az adatok jegyzetfüzetből való használatával. Azt is megteheti, hogy a jegyzetfüzetek a projektben tárolt adatforrásokat is létrehoznak, amelyeket később más célra is letölthet.

A futó jegyzetfüzetben található **adatok** menü **feltöltési** és **letöltési** parancsokat is biztosít, amelyek a projekt fájljaival, valamint az aktuális jegyzetfüzet-munkamenet ideiglenes fájljaival működnek.

A jegyzetfüzeten belül kódot is használhat a különböző adatforrások közvetlen eléréséhez, beleértve a projektben lévő fájlokat is. A kód cellájában lévő parancsokkal is elérheti a tetszőleges adatértékeket. Mivel az ilyen jellegű adat a jegyzetfüzet-munkamenetben változóban van tárolva, nem lesz mentve a projektbe, hacsak nem használ programkódot a projektfájlok konkrét létrehozásához.

A kód az adatban való használata a legalkalmasabb egy futó jegyzetfüzeten belül: erre a célra tekintse át az [adatait a Azure Notebooks minta jegyzetfüzetben](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

A cikk további részében részletesen ismertetjük a projekt szintű fájlokkal kapcsolatos műveleteket.

## <a name="import-data"></a>Adatok importálása

A fájlokat a projekt irányítópultján vagy az **adatok** menüvel vagy egy olyan paranccsal hozhatja be egy projektbe, amely egy futó jegyzetfüzetben található `curl` .

### <a name="import-files-from-the-project-dashboard"></a>Fájlok importálása a projekt irányítópultról

1. A projektben Navigáljon arra a mappára, ahová importálni kívánja a fájlokat.

1. Válassza ki a **feltöltés** parancsot, majd az **URL** -címről vagy a **számítógépről** , és a projekthez az importálni kívánt adatokhoz szükséges adatokat:

   - **Feladó URL-** címe: írja be a forrás címét a **fájl URL** -címe mezőbe, és a **Fájlnév mezőben a** projektben lévő jegyzetfüzethez rendelendő fájlnevet. Ezután válassza a **+ fájl hozzáadása** lehetőséget, és adja hozzá az URL-címet a feltöltési listához. Ismételje meg a folyamatot bármely további URL-cím esetében, majd válassza a **kész**lehetőséget.

     ![Feltöltés URL-felugró ablakból](media/quickstarts/upload-from-url-popup.png)

   - **Számítógépről**: húzza a fájlokat a felugró ablakba, vagy válassza a **fájlok kiválasztása**lehetőséget, majd keresse meg és válassza ki az importálni kívánt adatfájlokat. Bármilyen típusú fájl eldobása vagy kiválasztása bármely típus és formátum alapján elvégezhető, mert a jegyzetfüzetben található kód a fájl megnyitásához és az adatelemzéshez szükséges.

     ![Feltöltés a számítógép előugró ablakból](media/quickstarts/upload-from-computer-popup.png)

1. Az importálás után a fájlok megjelennek a projekt irányítópultján, és a notebook code-on belül is elérhetők a tartalmazó mappához viszonyított elérési út használatával.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Fájlok importálása egy jegyzetfüzet fájl menüjéből

1. Egy futó jegyzetfüzetben válassza a **fájlfeltöltés**  >  **Upload** parancsot:

    ![Fájlfeltöltés menü parancs egy jegyzetfüzeten belül](media/file-menu-upload.png)

1. A megnyíló párbeszédpanelen navigáljon a listához, és válassza ki a feltölteni kívánt fájlokat. Tetszőleges számú fájltípust választhat. Ha elkészült, válassza a **Megnyitás** lehetőséget.

1. A megjelenő **feltöltési állapot** előugró ablakban válasszon ki egy **célmappát** a legördülő listából:

    - Munkamenet mappája ( *~/* ): fájlokat tölt fel az aktuális jegyzetfüzet-munkamenetbe, de nem hoz létre fájlokat a projektben. A munkamenet mappája a projekt mappájához tartozó társ, de a munkamenet vége után nem marad meg. Ha a munkamenet-fájlokat a kódban szeretné elérni, előtagként a fájlneveket a relatív elérési úttal *. /*.

        A munkamenet mappájának használata hasznos a kísérletezéshez, és elkerüli a projekt zsúfoltságát olyan fájlokkal, amelyek hosszú távon esetleg nem szükségesek. Olyan fájlokat is fel lehet tölteni a munkamenet mappájába, amelyek azonos névvel rendelkeznek a projektben lévő fájlokhoz anélkül, hogy ütközéseket okozna, és nem kell átnevezni a fájlokat. Tegyük fel például, hogy már rendelkezik egy *data.csv* -verzióval a projektben, de a *data.csv*egy másik verziójával szeretne kísérletezni. A fájlnak a munkamenet mappájába való feltöltésével a feltöltött fájlban lévő adat használatával futtathatja a jegyzetfüzetet (ez a következővel hivatkozik a kódban: *.. /data.csv*) a projekt fájljában szereplő adathalmazok helyett.

    - Project Folder (*/Project*): fájlok feltöltése a projektbe, ahol a kód relatív elérési útjainak használatával érhetők el. Egy fájlnak a mappába való feltöltése megegyezik a projekt irányítópultján található fájl feltöltésével. A rendszer menti a fájlt a projekttel, és a későbbi munkamenetekben is elérhető.

        A feltöltés sikertelen, ha olyan fájlt próbál feltölteni, amelynek a neve megegyezik a projektben már létezővel. Egy fájl felülírásához töltse fel helyette az új fájlt a projekt irányítópultról, amely lehetővé teszi a felülírás lehetőségét.

1. A folyamat befejezéséhez válassza a **feltöltés megkezdése** lehetőséget.

### <a name="create-or-import-files-using-commands"></a>Fájlok létrehozása vagy importálása parancsok használatával

Egy terminálon belül vagy egy Python-kód cellán belüli parancsokat is használhat a projekt-és munkamenet-mappákban található fájlok létrehozásához. Például `curl` `wget` közvetlenül az internetről származó fájlokra, illetve a fájlok letöltésére.

Ha fájlokat szeretne letölteni a terminálon, válassza a **terminál** parancsot a projekt irányítópultján, majd adja meg a megfelelő parancsokat:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Ha a jegyzetfüzetben Python-kód cellát használ, a parancsokhoz előtagot kell megmutatnia `!` .

A Project mappa az alapértelmezett mappa, ezért a cél fájlnevét kell megadni, például a *oil_price.csv* létrehozza a fájlt a projektben. Egy munkamenet-fájl létrehozásához előtagként adja meg a nevet *. /* a-ben *. /oil_price.csv*.

### <a name="create-files-in-code"></a>Fájlok létrehozása kódban

Ha olyan kódot használ, amely létrehoz egy fájlt, például a pandák `write_csv` függvényt, az elérési út mindig a projekt mappájához viszonyítva van. A *.. /* létrehoz egy, a jegyzetfüzet leállításakor és bezárásakor elvetett munkamenet-fájlt.

## <a name="export-files"></a>Fájlok exportálása

Az adatok a projekt irányítópultján vagy jegyzetfüzetből is exportálhatók.

## <a name="export-files-from-the-project-dashboard"></a>Fájlok exportálása a projekt irányítópultról

A projekt Irányítópultján kattintson a jobb gombbal a fájlra, és válassza a **Letöltés**lehetőséget:

![Parancs letöltése a projekt elemének helyi menüjében](media/download-command.png)

Kiválaszthat egy fájlt is, és használhatja a **Letöltés** parancsot (billentyűparancs: d) az irányítópulton:

![Eszköztár-parancs letöltése a projekt irányítópultján](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Fájlok exportálása egy jegyzetfüzet adat menüjéből

1. Válassza a **fájl**  >  **letöltése** menü parancsot:

    ![Az adatletöltés menü parancsa egy jegyzetfüzeten belül](media/file-menu-download.png)

1. Megjelenik egy előugró ablak, amely megjeleníti a munkamenetben lévő mappákat. a *Project* mappában található projektfájlok a következőket tartalmazzák:

    ![A fájlok és mappák kiválasztására szolgáló adatletöltési parancs előugró ablaka](media/file-menu-download-popup.png)

1. Jelölje be a letölteni kívánt fájlok és mappák bal oldalán található jelölőnégyzeteket, majd válassza a **kijelöltek letöltése**lehetőséget.

1. A jegyzetfüzet előkészít egy *. zip* fájlt, amely tartalmazza a kiválasztott fájlokat, amelyeket aztán a böngészőből szokásos módon menthet. A jegyzetfüzet egyetlen fájl letöltésekor is létrehoz egy *. zip* fájlt.

## <a name="next-steps"></a>További lépések

- [Felhőbeli adat elérése jegyzetfüzetben](access-data-resources-jupyter-notebooks.md)
