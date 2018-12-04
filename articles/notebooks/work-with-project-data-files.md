---
title: Az Azure-jegyzetfüzeteket a projektek adatok importálása és exportálása |} A Microsoft Docs
description: Hogyan lehet egy Azure-jegyzetfüzetek projekt külső forrásból származó adatokat importálnak, és a egy projektet az adatok exportálása.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 586b423b-6668-4bdd-9592-4c237d7458fb
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 84b238ee287d6b47421788d14235abceb03fafa7
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856031"
---
# <a name="work-with-data-files-in-azure-notebook-projects"></a>Az adatfájlokat az Azure-jegyzetfüzet projektek használata

Adatok a lifeblood, számos, a Jupyter notebooks, különösen akkor használja az adatelemzéshez notebookok el. Az Azure-jegyzetfüzetek egyszerűen importálhatja a különböző forrásokból egy projektbe, és majd az adatokat a notebookok. Jegyzetfüzetek létrehozása a projektben, majd töltheti használatra máshol tárolt adatokat is rendelkezhet.

A **adatok** belül futó Jegyzetfüzet menü arra is biztosít **feltöltése** és **letöltése** parancsokat, amelyek a projektben lévő fájlok, valamint az ideiglenes fájlokat az aktuális használata a jegyzetfüzet-munkamenet.

Is segítségével kód egy jegyzetfüzetet belül, közvetlenül hozzáférni az adatforrások széles többek között a fájlok egy projektben. Emellett tetszőleges adatokat egy kódcellába parancsok használatával. Mivel az ilyen adatok tárolása változókban jegyzetfüzet-munkameneten belül, azt nem menthető a projektben, kivéve, ha a kifejezetten a projektfájlok kódot használja.

Adatok a kód használatának előnyeit legjobban belül futó jegyzetfüzet magát: erre a célra, tekintse meg a [az adatait a Azure notebookok minta notebook első](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

Ez a cikk további részében projekt szintű fájlműveletek részletesen.

## <a name="import-data"></a>Adatok importálása

Közzétehet fájlokat a projektbe a projekt irányítópultján, illetve egy futó notebook használatával a **adatok** menüben, vagy mint `curl`.

### <a name="import-files-from-the-project-dashboard"></a>Fájlok importálása a projekt-irányítópult

1. A projektben keresse meg a mappát, ahol a fájlokat importálni szeretné.

1. Válassza ki a **feltöltése** parancsot, majd vagy **URL-CÍMRŐL** vagy **számítógépről** és a projekt az importálni kívánt adatok a szükséges adatokat:

    - **URL-címről**: Adja meg a forrás-címet a **fájl URL-cím** mező, és a fájlnevet a notebookot a projekt a hozzárendelése a **Fájlnév** mező. Válassza ki **+ fájl hozzáadása** az URL-cím hozzáadása a feltöltési listájához. Ismételje meg a folyamatot minden olyan további URL-címet, majd válassza a **kész**.

    ![Töltse fel az URL-cím előugró ablak](media/quickstarts/upload-from-url-popup.png)

    - **A számítógép**: húzza és fájlok betett az előugró ablak, vagy bejelölheti **fájlok kiválasztása**, majd keresse meg és válassza ki az importálni kívánt adatfájlokat. Dobja el, vagy fájlokat, bármilyen típusú és formátumú tetszőleges számú választható, mert a kódhoz a notebook nyissa meg a fájlt, és elemezni az adatokat a szolgáltatás.

    ![Töltse fel a számítógép helyi menü](media/quickstarts/upload-from-computer-popup.png)

1. Importálás után a notebook kód tartalmazó mappába relatív forráskódfájlok használatával érhető el, és fájlokat a projekt irányítópultján jelenik meg.

### <a name="import-files-from-the-data-menu-in-a-notebook"></a>Importálja a fájlokat egy jegyzetfüzetet az adatok menüjéből

1. Belül futó jegyzetfüzet, válassza ki a **fájl** > **feltöltése** parancsot:

    ![A fájl feltöltése menü parancs egy jegyzetfüzetet belül](media/file-menu-upload.png)

1. A megnyíló párbeszédpanelen keresse meg és válassza ki a feltölteni kívánt fájlokat. Kiválaszthatja, hogy bármilyen fájltípus tetszőleges számú. Válassza ki **nyílt** végeztével.

1. Az a **feltöltési állapot** előugró ablak, amely akkor jelenik meg, válassza ki a **célmappa** a legördülő listából:

    - Munkamenet-mappa (*~/* ): feltölti az aktuális munkamenetbe notebook fájlokat, de nem hoz létre fájlokat a projektben. A munkamenet mappa társ a mappájára, de nem tárol, miután a munkamenet azért ér véget. A kódban munkamenet fájlok eléréséhez, a fájlneveket, a következő relatív elérési előtag *... /*.

        A munkamenet mappa használata Kísérletezési hasznos, és elkerülhető, hogy a projekt elárasztanák rendelkező fájlokat is, illetve előfordulhat, hogy nem kell a hosszú távon. A munkamenet-mappába, amelyeket azonos nevek a projekt fájlokat anélkül, hogy ez ütközéseket, és nevezze át a fájlokat anélkül is feltölthet fájlokat. Tegyük fel például, a egy verziójával rendelkezik *data.csv* a projektben már, de szeretné kísérletezhet a különböző verzióit *data.csv*. A fájl feltöltésével a munkamenet-mappába, a jegyzetfüzet-adatok a feltöltött fájl a futtathatja (az arra való hivatkozás a kód használatával *... /Data.csv*) ahelyett, hogy a projekt fájlban lévő adatokat.

    - Projektmappa (*/project*): feltölti a fájlokat a projektbe, ahol el lehet elérni a relatív elérési használata a kódban. Ha fájlt tölt fel a mappa ugyanaz, mint a projekt irányítópultján fájlt feltölteni. A fájl is mentve lesz a projektet, és későbbi munkamenetek érhető el.

        Ha megpróbálja feltölteni egy fájlt a projektben már létezik egy azonos nevű, feltöltése sikertelen. Felülírja a fájlt, töltse fel az új fájlt a projekt irányítópultján, amely lehetővé teszi felülírásához.

1. Válassza ki **Start feltöltése** a folyamat befejezéséhez.

### <a name="create-or-import-files-using-commands"></a>Hozzon létre vagy importáljon parancsokkal fájlok

Használhatja a parancsokat egy terminált vagy a Python kódcella belül a projekt és a munkamenet-fájlok létrehozása. Ha például parancsok, például `curl` és `wget` közvetlenül letöltheti a fájlokat az internetről.

A terminálban fájlok letöltéséhez, válassza ki a **terminál** a projekt irányítópultján parancsot, majd adja meg a megfelelő parancsokat:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Egy Python kódcellát a notebook használatakor az előtag a parancsokat `!`.

A projekt mappa nem az alapértelmezett mappát, így a hasonló cél fájlnév megadása *oil_price.csv* hoz létre a fájlt a projektben. Hozzon létre egy munkamenet-fájlt, hogy a elé előtagot *... /* hasonlóan a *... /oil_price.csv*.

### <a name="create-files-in-code"></a>Fájlok létrehozása a code-ban

Ha a kódot, amely létrehoz egy fájlt, például a pandas használatával `write_csv` forráskódfájlok függvény mindig a projektmappa fájllistájának képest relatív használ. Használatával *... /* létrehoz egy munkamenet-fájlt, amely a rendszer elveti, amikor a notebook leállt, és lezárt.

## <a name="export-files"></a>Fájlok exportálása

A projekt irányítópultján vagy belül is exportálhat adatokat egy notebookot.

## <a name="export-files-from-the-project-dashboard"></a>A projekt irányítópultján fájlok exportálása

A projekt irányítópultján kattintson a jobb gombbal egy fájlt, és válassza ki **letöltése**:

![Töltse le a parancs a projekt helyi menüje](media/download-command.png)

Is válasszon ki egy fájlt, és használja a **letöltése** parancsot (billentyűparancs: d) az irányítópulton:

![Töltse le az eszköztár parancs a projekt-irányítópult](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Az adatok menüből jegyzetfüzetlapot fájlok exportálása

1. Válassza ki a **fájl** > **letöltése** parancs:

    ![Adatok letöltése parancs egy jegyzetfüzetet belül](media/file-menu-download.png)

1. Megjelenik egy előugró ablak, amely megjeleníti a mappákat a munkamenet; a *projekt* mappát a projekt fájlokat tartalmazza:

    ![Adatok letöltése paranccsal előugró fájlok és mappák kiválasztása](media/file-menu-download-popup.png)

1. Válassza ki a mezőket, a fájlokat és mappákat szeretné letölteni, majd válassza a bal oldali **töltse le a kiválasztott**.

1. A notebook előkészít egy *.zip* fájlt, amely tartalmazza a kijelölt fájlokat, amelyek ezután menti, normál esetben hajtsa végre a böngészőben. A notebook létrehoz egy *.zip* fájl, akkor is, ha egyetlen fájl letöltése.

## <a name="next-steps"></a>További lépések

- [Hozzáférés felhőbeli adatok történő használatát](access-data-resources-jupyter-notebooks.md)
