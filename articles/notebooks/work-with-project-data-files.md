---
title: Adatok importálása és exportálása projektekkel az Azure Notebookelőzetes verzióval
description: Megtudhatja, hogyan hozhat létre adatokat egy Azure Notebook előzetes verzióprojektbe külső forrásokból, és hogyan exportálhat adatokat egy projektből.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: e1d4a52ab7f4ad2ca3438af4bc87bec0b79f34d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646976"
---
# <a name="work-with-data-files-in-azure-notebooks-preview-projects"></a>Adatfájlok kal való kapcsolat az Azure Notebookelőzetes-projektekben

Az adatok számos Jupyter-jegyzetfüzet éltető eleme, különösen az adatelemzéshez használt jegyzetfüzetek. Az Azure Notebooks segítségével könnyedén importálhat különböző forrásokból egy projektbe, majd használhatja ezeket az adatokat a jegyzetfüzetekből. A jegyzetfüzetek a projektben tárolt adatokat is létrehozhatnak, amelyeket aztán letölthet máshol való használatra.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

A futó jegyzetfüzet **Adatok** menüje **feltöltési** és **letöltési** parancsokat is biztosít, amelyek a projektben lévő fájlokkal, valamint az aktuális jegyzetfüzet-munkamenet ideiglenes fájljaival működnek.

A jegyzetfüzetben lévő kód segítségével közvetlenül is hozzáférhet a különböző adatforrásokhoz, beleértve a projekten belüli fájlokat is. Tetszőleges adatokat is elérhet a kódcella parancsaival. Mivel az ilyen adatok a jegyzetfüzetmunkamenetváltozókban tárolódnak, csak akkor lesznek mentve a projektben, ha kód használatával konkrétan projektfájlokat hoz létre.

Az adatokban lévő kódokkal való munka a legjobb, ha egy futó jegyzetfüzetben van: ebből a célból tekintse meg az [Adatok beszerzése az Azure Notebookok mintajegyzetfüzetében című területet.](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb)

A cikk további részében a projektszintű fájlműveletek részleteiről olvashat.

## <a name="import-data"></a>Adatok importálása

Fájlokat hozhat be a projektbe a projekt irányítópultjáról, vagy egy futó jegyzetfüzetben az **Adatok** menü vagy egy parancs, például `curl`segítségével.

### <a name="import-files-from-the-project-dashboard"></a>Fájlok importálása a projekt irányítópultjáról

1. A projektben keresse meg azt a mappát, ahová importálni szeretné a fájlokat.

1. Válassza ki a **Feltöltés** parancsot, majd az **URL-címről** vagy a **számítógépről** parancsot, és vetítse ki az importálni kívánt adatok szükséges adatait:

   - **ForrásURL:** Írja be a forráscímet a **Fájl URL-címe** mezőbe, és a fájlnevet, amelyet a projektben lévő jegyzetfüzethez rendel a **Fájlnév** mezőben. Ezután válassza **a + Fájl hozzáadása lehetőséget,** ha hozzá szeretné adni az URL-címet a feltöltési listához. Ismételje meg a folyamatot a további URL-címeknél, majd válassza a **Kész gombot.**

     ![Feltöltés URL-felugró ablakból](media/quickstarts/upload-from-url-popup.png)

   - **Számítógépről:** Húzza a fájlokat az előugró ablakba, vagy válassza a **Fájlok kiválasztása**lehetőséget, majd keresse meg és jelölje ki az importálni kívánt adatfájlokat. Tetszőleges számú fájlt eldobhat vagy választhat, bármilyen típusú és formátumú, mert a jegyzetfüzetben lévő kódig tart a fájl megnyitása és az adatok elemzése.

     ![Feltöltés a számítógép előugró ablakából](media/quickstarts/upload-from-computer-popup.png)

1. Importálás után a fájlok megjelennek a projekt irányítópultján, és a jegyzetfüzetkódban keresztül a tartalmazó mappa relatív elérési útjainak használatával érhetők el.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Fájlok importálása jegyzetfüzet Fájl menüjéből

1. Egy futó jegyzetfüzeten belül válassza a > **Fájlfeltöltés** parancsot: **File**

    ![Fájlfeltöltés menüparancs jegyzetfüzetben](media/file-menu-upload.png)

1. A megnyíló párbeszédpanelen keresse meg a feltölteni kívánt fájlokat, és jelölje ki azokat. Tetszőleges számú fájlt választhat ki bármilyen típusú. Válassza a **Megnyitás** lehetőséget, ha végzett.

1. A **megjelenő Feltöltési állapot** előugró ablakban válasszon ki egy **célmappát** a legördülő listából:

    - Munkamenet mappa*~/* ( ): Fájlok feltöltése az aktuális jegyzetfüzet-munkamenetbe, de nem hoz létre fájlokat a projektben. A munkamenetmappa a projektmappa társa, de nem marad meg a munkamenet befejezése után. A kódban lévő munkamenetfájlok eléréséhez előtagválassza előa fájlneveket a relatív elérési úttal *.. /*.

        A munkamenet-mappa használata hasznos a kísérletezéshez, és elkerüli, hogy a projekt olyan fájlokkal legyen túlzsúfolt, amelyekre hosszú távon szüksége lehet vagy nincs. A munkamenet-mappába olyan fájlokat is feltölthet, amelyek neve megegyezik a projekt fájljaival anélkül, hogy ütközést okozna, és át kellene neveznie a fájlokat. Tegyük fel például, hogy a *data.csv* egy verziója már van a projektben, de a *data.csv*egy másik verziójával szeretne kísérletezni. A fájl munkamenet-mappába való feltöltésével a jegyzetfüzetet a feltöltött fájlban lévő adatok alapján futtathatja (a .. kódhasználatával *hivatkozva. /data.csv)* helyett a projekt fájljában lévő adatokat.

    - Projektmappa (*/project*): fájlokat tölt fel a projektbe, ahol azok relatív elérési útnevek használatával érhetők el a kódban. Fájl feltöltése ebbe a mappába ugyanaz, mint egy fájl feltöltése a projekt irányítópultjára. A fájl a projekttel együtt kerül mentésre, és későbbi munkamenetekben érhető el.

        A feltöltés sikertelen, ha olyan nevű fájlt próbál feltölteni, mint amely már létezik a projektben. Fájl felülírásához töltse fel az új fájlt a projekt irányítópultjáról, amely lehetővé teszi a felülírást.

1. A folyamat befejezéséhez válassza a **Feltöltés indítása** lehetőséget.

### <a name="create-or-import-files-using-commands"></a>Fájlok létrehozása és importálása parancsokkal

A terminálon vagy egy Python-kódcellán belüli parancsok segítségével fájlokat hozhat létre a projekt- és munkamenetmappákban. Például a `curl` parancsok, mint és `wget` töltse le a fájlokat az internetről közvetlenül.

Fájlok terminálon való letöltéséhez válassza a **Terminál** parancsot a projekt irányítópultján, majd adja meg a megfelelő parancsokat:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Ha Python-kódcellát használ egy jegyzetfüzetben, `!`a parancsait előtagolja a segítségével.

A projektmappa az alapértelmezett mappa, ezért a *oil_price.csv* hez hasonló célfájlnév megadásával létre kell adnia a fájlt a projektben. Munkamenetfájl létrehozásához előtagítsa meg a nevet *a .. /* mint *.. /oil_price.csv*.

### <a name="create-files-in-code"></a>Fájlok létrehozása kódban

Ha olyan kódot használ, amely létrehoz `write_csv` egy fájlt, például a pandák függvényt, az elérési út nevek mindig a projektmappához viszonyítva lesznek. Használata *.. /* létrehoz egy munkamenet fájlt, amely eldobja, ha a notebook levan állítva és zárva.

## <a name="export-files"></a>Fájlok exportálása

Az adatokat exportálhatja a projekt irányítópultjáról vagy egy jegyzetfüzetből.

## <a name="export-files-from-the-project-dashboard"></a>Fájlok exportálása a projekt irányítópultjáról

A projekt irányítópultján kattintson a jobb gombbal egy fájlra, és válassza a **Letöltés parancsot:**

![Letöltés parancs a projektelem helyi menüjén](media/download-command.png)

Kiis választhat egy fájlt, és **használhatja** a Letöltés parancsot (billentyűparancs: d) az irányítópulton:

![Eszköztár parancs letöltése a projekt irányítópultján](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Fájlok exportálása jegyzetfüzet Adatok menüjéből

1. Válassza a **Fájl** > **letöltése** menüparancsot:

    ![Az Adatok letöltése menü parancs a jegyzetfüzetben](media/file-menu-download.png)

1. Megjelenik egy előugró ablak, amely a munkamenet mappáit jeleníti meg; a *projektmappa* tartalmazza a projektfájlokat:

    ![Adatletöltés parancs előugró ablak, amelyben fájlokat és mappákat jelöl ki](media/file-menu-download-popup.png)

1. Jelölje be a letölteni kívánt fájlok és mappák bal oldalán lévő jelölőnégyzeteket, majd válassza a **Levan jelölve**a Letöltés lehetőséget.

1. A jegyzetfüzet egyetlen *.zip* fájlt készít elő, amely tartalmazza a kijelölt fájlokat, amelyeket aztán a böngészőből szokásos módon ment. A jegyzetfüzet akkor is létrehoz egy *.zip* fájlt, ha egyetlen fájlt tölt le.

## <a name="next-steps"></a>További lépések

- [Felhőbeli adatok elérése egy jegyzetfüzetben](access-data-resources-jupyter-notebooks.md)
