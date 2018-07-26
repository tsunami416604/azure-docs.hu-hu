---
title: A Power BI oktatóanyag az Azure Cosmos DB-összekötő |} A Microsoft Docs
description: Ebben az oktatóanyagban a Power BI használatával JSON importálása, hozzon létre részletes jelentéseket és jelenítheti meg az Azure Cosmos DB és a Power BI-összekötő segítségével adatokat.
keywords: a Power bi-oktatóanyag, adatok, a power bi-összekötő megjelenítése
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: sngun
ms.openlocfilehash: 3734c30dad0735164d4501d1d63d8ffe301396a3
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249114"
---
# <a name="power-bi-tutorial-for-azure-cosmos-db-visualize-data-using-the-power-bi-connector"></a>Az Azure Cosmos DB-hez készült Power BI oktatóanyag: adatok megjelenítése Power BI-összekötő használatával
[PowerBI.com](https://powerbi.microsoft.com/) egy online szolgáltatás, ahol létrehozhat és irányítópultok és jelentések megosztása, és a szervezet számára fontos adatokkal.  A Power BI Desktop egy olyan dedikált jelentés, amely lehetővé teszi, hogy a különféle adatforrásokból származó adatok lekéréséhez, egyesítése és átalakíthatja az adatokat, hatékony jelentések és Vizualizációk létrehozása és a jelentések közzététele a Power bi-bA a szerkesztőeszköz.  A Power BI Desktop legújabb verzióját mostantól csatlakozhat az Azure Cosmos DB-fiókot az Azure Cosmos DB-összekötő használatával a Power bi-hoz.   

Az oktatóanyag a Power BI ismerteti azokat a lépéseket az Azure Cosmos DB-fiókot, a Power BI Desktopban csatlakozhat egy gyűjteményhez, szeretnénk az adatokat, a kezelő használatával lépjen, alakítsa át JSON-adatok a Power BI Desktop Lekérdezésszerkesztő használatával táblázatos formátumú lépéseit , és hozhat létre, és a egy jelentés közzététele a powerbi.com-on.

Miután befejezte az oktatóanyag a Power bi-ban, is elérheti az alábbi kérdések megválaszolásához:  

* Hogyan készíthetek adatokkal jelentések az Azure Cosmos DB használatával a Power BI Desktopban?
* Hogyan lehet Azure Cosmos DB-fiókot, a Power BI Desktopban csatlakozni?
* Hogyan beolvashatom az adatok a Power BI Desktop-gyűjteményből?
* Hogyan alakíthatja át a beágyazott JSON-adatok a Power BI Desktopban?
* Hogyan tehet közzé és megosztás saját jelentéseket a powerbi.com webhelyen?

> [!NOTE]
> Az Azure Cosmos DB-hez készült Power BI-összekötő kinyerésére és az adatok átalakítása a Power BI Desktop csatlakozik. A Power BI Desktopban létrehozott jelentések közzétehetők a powerbi.com webhelyre. Közvetlen kinyerésére és az Azure Cosmos DB-adatok átalakítása nem hajtható végre a powerbi.com webhelyen. 

> [!NOTE]
> A Power BI-összekötő az Azure Cosmos DB csatlakoztatása Azure Cosmos DB SQL- és MongoDB API-fiókok csak a jelenleg támogatott. Az Azure Cosmos DB csatlakoztatása a Power bi-bA a MongoDB API használatával, meg kell használnia a [Simba MongoDB ODBC-illesztő](http://www.simba.com/drivers/mongodb-odbc-jdbc/).

## <a name="prerequisites"></a>Előfeltételek
Power BI-oktatóanyagban szereplő utasítások követése, előtt, hogy a következő erőforrásokhoz való hozzáférés biztosítása:

* [A Power BI Desktop legújabb verzióját](https://powerbi.microsoft.com/desktop).
* A bemutató fiók vagy az Azure Cosmos DB-fiókban lévő adatok elérésére.
  * A bemutató fiók van feltöltve a Mexikói ebben az oktatóanyagban látható. Ez a bemutató fiók bármely SLA-k nem kötődik, és csak bemutató célokra tervezték.  Azt fenntartjuk a jogot arra, hogy hajtsa végre a módosításokat, ez a bemutató fiók többek között, de nem korlátozódik, a fiók megszüntetéséről, a kulcs módosítása, korlátozzuk, módosítás, és törölni szeretné az adatokat, bármikor, előzetes értesítés, vagy akár indoklás nélkül.
    * URL: https://analytics.documents.azure.com
    * Read-only key: MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==
  * Vagy a saját fiók létrehozásához lásd: [az Azure portal használatával egy Azure Cosmos DB-adatbázisfiók létrehozása](https://azure.microsoft.com/documentation/articles/create-account/). Ezt követően beolvasni a minta mexikói mi hasonló adatok használatban van ebben az oktatóanyagban (de nem tartalmazza a GeoJSON-blokkok), lásd: a [NOAA hely](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5) , majd importálja az adatokat az a [Azure Cosmos DB adatáttelepítési eszköz](import-data.md).

A jelentéseket a powerbi.com webhelyen megosztásához egy fiókkal kell rendelkeznie a powerbi.com webhelyen.  További információ a Power BI ingyenes és a Power BI Pro a, a Microsoft [ https://powerbi.microsoft.com/pricing ](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Első lépések
Ebben az oktatóanyagban tegyük fel, hogy egy geologist tanulmányozása vulkánok alakították szerte a világon.  A Mexikói adatokat az Azure Cosmos DB-fiók tárolva van, és a következő mintadokumentumot kinézni a JSON-dokumentumok.

    {
        "Volcano Name": "Rainier",
           "Country": "United States",
          "Region": "US-Washington",
          "Location": {
            "type": "Point",
            "coordinates": [
              -121.758,
              46.87
            ]
          },
          "Elevation": 4392,
          "Type": "Stratovolcano",
          "Status": "Dendrochronology",
          "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
    }

Szeretné a Mexikói adatok lekérését az Azure Cosmos DB-fiókot, és megjelenítheti például a következő jelentés egy interaktív Power BI-jelentés adatait.

![A Power BI-összekötő a Power BI oktatóanyag elvégzésével is elérheti a Power BI Desktop mexikói jelentést az adatok megjelenítése](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)

Próbálja ki most! Első lépések.

1. A Power BI Desktop a munkaállomáson futnak.
2. A Power BI Desktop indul el, miután egy *üdvözlő* képernyő jelenik meg.
   
    ![A Power BI Desktop üdvözlő képernyő – Power BI-összekötő](./media/powerbi-visualize/power_bi_connector_welcome.png)
3. Is **adatok lekérése**, lásd: **legutóbbi források**, vagy **más jelentések megnyitása** közvetlenül a *üdvözlő* képernyő.  Kattintson az X gombra kattintva zárja be a képernyő jobb felső sarokban található. A **jelentés** Power BI Desktop nézet jelenik meg.
   
    ![A Power BI Desktop jelentés nézet – Power BI-összekötő](./media/powerbi-visualize/power_bi_connector_pbireportview.png)
4. Válassza ki a **kezdőlap** menüszalagra, majd kattintson a **adatok lekérése**.  A **adatok lekérése** ablak meg kell jelennie.
5. Kattintson a **Azure**válassza **Azure Cosmos DB (bétaverzió)**, és kattintson a **Connect**. 

    ![A Power BI Desktop - adatok beolvasása a Power BI-összekötő](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   
6. Az a **összekötő előnézete** kattintson **Folytatás**. A **Azure Cosmos DB** ablak jelenik meg.
7. Adja meg az Azure Cosmos DB fiók végponti URL-cím lenne, ha az adatok beolvasásához, ahogy az alábbi, és kattintson **OK**. A saját fiók használatára, az URI azonosító mezőjében található lehet lekérdezni az URL-cím a **[kulcsok](manage-account.md#keys)** panel az Azure Portal. A bemutató fiók használatához adja meg `https://analytics.documents.azure.com` URL-címe. 
   
    Az adatbázisnév, a gyűjtemény neve és az SQL-utasítás üresen hagyja, ezek a mezők kitöltése nem kötelező.  Ehelyett használjuk a kezelőben válassza ki az adatbázist és gyűjteményt az adatok forrását azonosításához.
   
    ![A Power BI oktatóanyag az Azure Cosmos DB a Power BI-összekötő – asztali csatlakoztatása ablakot](./media/powerbi-visualize/power_bi_connector_pbiconnectwindow.png)
8. Ha első alkalommal csatlakozik ennek a végpontnak, kéri a fiókkulcsot. Saját fiók lekérni a kulcsot a a **elsődleges kulcs** párbeszédpanel a **[csak olvasható kulcsok](manage-account.md#keys)** panel az Azure Portal. A bemutató fiók, a kulcs: `MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==`. Adja meg a megfelelő kulcsot, és kattintson a **Connect**.
   
    Azt javasoljuk, hogy a csak olvasható kulcsot használja, jelentések készítése során.  Ez megakadályozza a biztonsági kockázatok a főkulcs szükségtelen kitettségtől. A csak olvasható kulcs érhető el a [kulcsok](manage-account.md#keys) panel az Azure Portalon, vagy Ön használhatja a fenti demo-fiók adatait.
   
    ![A Power bi-ban az oktatóanyag az Azure Cosmos DB a Power BI-összekötő - Fiókkulcs](./media/powerbi-visualize/power_bi_connector_pbidocumentdbkey.png)
    
    > [!NOTE] 
    > Ha a hiba, amely szerint a "a megadott adatbázis nem található." Ez a megoldás lépéseit lásd [Power BI probléma](https://community.powerbi.com/t5/Issues/Document-DB-Power-BI/idi-p/208200).
    
9. Ha a fiók sikeresen csatlakozott, a **kezelő** ablaktáblán jelenik meg.  A **kezelő** a fiókhoz tartozó adatbázisok listája látható.
10. Kattintson, és bontsa ki az adatbázist, ahol az adatokat a jelentés származik, ha a bemutató fiókot használ a jelölje ki a **volcanodb**.   
11. Most válassza ki egy gyűjteményt, amelyben az adatok lekéréséhez. Ha a bemutató fiókot használ, válassza ki a **volcano1**.
    
    Az előnézeti ablaktáblában láthatja a listája látható **rekord** elemek.  A dokumentum jelenik meg egy **rekord** írja be a Power bi-ban. Hasonlóképpen, egy beágyazott JSON-kódblokkot a dokumentumon belül is van egy **rekord**.
    
    ![A Power bi-ban az oktatóanyag az Azure Cosmos DB a Power BI-összekötő - kezelő ablak](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Kattintson a **szerkesztése** elindíthatja a Lekérdezésszerkesztő az adatok átalakításához egy új ablakban.

## <a name="flattening-and-transforming-json-documents"></a>Az egybesimítás és JSON-dokumentumok átalakítása
1. A Power BI Query-szerkesztő ablakában váltson ahol a **dokumentum** oszlop a középső ablaktáblán.
   ![A Power BI Desktop Lekérdezésszerkesztő](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Kattintson a jobb oldalán a bővítő a **dokumentum** oszlop fejlécére.  A helyi menü mezők listája jelenik meg.  Válassza ki például mexikói nevét, ország, régió, hely, jogosultságszint-emelési, típusát, állapot és utolsó ismert határozzák a jelentéshez szükséges mezők. Törölje a jelet a **eredeti oszlopnév használata előtagként** mezőbe, majd kattintson a **OK**.
   
    ![A Power BI-oktatóanyag az Azure Cosmos DB Power BI-összekötő – bontsa ki a dokumentumok](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. A középső ablaktáblán a kiválasztott mezőkkel előzetes verziója az eredmény megjelenítése.
   
    ![A Power BI-oktatóanyag az Azure Cosmos DB Power BI-összekötő – a eredményeket Egybesimítására](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. Ebben a példában a Location tulajdonság egy dokumentum egy GeoJSON letiltása.  Amint láthatja, a hely jelenik meg egy **rekord** írja be a Power BI Desktopban.  
5. Kattintson a jobb oldali Document.Location záhlaví a bővítő.  A helyi menü típusa és koordináták mezőkkel jelennek meg.  Most válassza ki a koordináták mezőt, ellenőrizze, **eredeti oszlopnév használata előtagként** nincs bejelölve, és kattintson a **OK**.
   
    ![A Power BI oktatóanyag az Azure Cosmos DB a Power BI-összekötő – bejegyzése](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. A középső ablaktáblán most látható egy koordináták oszlop **lista** típusa.  Ahogy látható, az oktatóanyag elején, ebben az oktatóanyagban a GeoJSON-adatok pont típusa nem rögzíti a koordináták tömb szélességi és hosszúsági értékeket.
   
    A [0] koordináták elem hosszúsági jelöli, miközben koordináták [1] szélességi jelöli.
    ![A Power BI oktatóanyag az Azure Cosmos DB a Power BI-összekötő – koordináták listája](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. A koordináták tömb simítják, hozzon létre egy **egyéni oszlop** LatLong nevezik.  Válassza ki a **oszlop hozzáadása** menüszalagra, majd kattintson a **egyéni oszlop**.  A **egyéni oszlop** ablak jelenik meg.
8. Adjon meg egy nevet az új oszlop, például LatLong.
9. Ezután adja meg az egyéni képlet az új oszlop számára.  . A példa kedvéért azt lesz összefűzhet a szélességi és hosszúsági értékeket vesszővel elválasztva, ahogy az alábbi, az alábbi képlettel: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Kattintson az **OK** gombra.
   
    További információt a Data Analysis Expressions (DAX) többek között a DAX-függvények, [a Power BI Desktopban a DAX alapszintű](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop).
   
    ![A Power BI oktatóanyag az Azure Cosmos DB a Power BI-összekötő – egyéni oszlop hozzáadása](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. A középső ablaktáblán megjelenik az új LatLong oszlopok kitölti értékekkel.
    
    ![A Power BI oktatóanyag az Azure Cosmos DB a Power BI-összekötő – egyéni LatLong oszlop](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Ha hibaüzenetet kap az új oszlopba, győződjön meg arról, hogy az alkalmazott lépések a lekérdezési beállítások egyeznek-e az alábbi ábra:
    
    ![Alkalmazott lépések kell lennie a forrás, navigációs, a dokumentum kibontva, kibontva Document.Location, egyéni oszlop hozzáadva](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Ha a lépéseket különböző, a további lépést törlés, és próbálja meg újra az egyéni oszlop hozzáadása. 

11. Kattintson a **bezárásához és a alkalmazni** menteni az adatmodellbe.
    
    ![A Power BI oktatóanyag az Azure Cosmos DB a Power BI-összekötő – zárja be és alkalmazása](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>A jelentések készítése
A Power BI Desktop jelentés nézet, ahol elkezdheti a jelentések adatainak megjelenítéséhez.  Húzással történő mezők jelentéseket hozhat létre a **jelentés** vásznán.

![A Power BI Desktop jelentés nézet – Power BI-összekötő](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

A jelentés nézetben keresse meg:

1. A **mezők** ablaktáblán, ez az, ahol megtekintheti a mezők a jelentésekben használható adatmodellek listáját.
2. A **Vizualizációk** ablaktáblán. A jelentés egy vagy több Vizualizáció is tartalmazhat.  Válassza ki az igényeihez illeszkedő vizualizációtípusok a **Vizualizációk** ablaktáblán.
3. A **jelentés** vászonra – Ez az, ahol a Vizualizációk létrehozása a jelentés.
4. A **jelentés** lapot. Többoldalas jelentést a Power BI Desktopban is hozzáadhat.

Az alábbiakban látható egy egyszerű interaktív térkép jelentés megtekintése létrehozásának alapvető lépéseit.

1. A példánkban hozunk létre minden egyes mexikói helyét megjelenítő térképet nézetet.  Az a **Vizualizációk** panelen kattintson a térkép vizualizációtípust, mint a fenti képernyőfelvételen kiemelt a.  A térkép kerül meghívásra a Látványelem típusát kell megjelennie a **jelentés** vásznán.  A **Vizualizáció** panelen is megjelennek a térkép vizualizációtípust kapcsolódó tulajdonságait.
2. Most áthúzása a LatLong mezőt a **mezők** ablaktábla a **hely** tulajdonság **Vizualizációk** ablaktáblán.
3. Ezután áthúzása a Mexikói neve mező a **jelmagyarázat** tulajdonság.  
4. Ezt követően áthúzása a jogosultságszint-emelési mező a **mérete** tulajdonság.  
5. Meg kell jelennie a térkép visual jelző használatával történik, a jogok kiterjesztését a Mexikói a buborék mérete az egyes mexikói helyét buborékok készlete van megjelenítve.
6. Most már létrehozott egy egyszerű jelentést.  További Vizualizációk hozzáadásával szabhatja a jelentést.  A mi esetünkben hozzáadtunk egy mexikói típusú szeletelőt, hogy a jelentés interaktív.  
   
    ![Képernyőkép az Azure Cosmos DB a Power bi-ban az oktatóanyag befejezése után a végső Power BI Desktop-jelentés](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)
7. A Fájl menüben kattintson a **mentése** és PowerBITutorial.pbix mentse a fájlt.

## <a name="publish-and-share-your-report"></a>Közzététel és a jelentés megosztása
A jelentés megosztásához egy fiókkal kell rendelkeznie a powerbi.com webhelyen.

1. A Power BI desktopban kattintson a a **kezdőlap** menüszalagon.
2. Kattintson a **Publish** (Közzététel) gombra.  Lehet kéri a felhasználónevét és jelszavát adja meg a PowerBI.com-fiók.
3. Ha a hitelesítő adatok hitelesítése után a jelentés közzététele a megadott helyre.
4. Kattintson a **nyílt "PowerBITutorial.pbix" Power BI-ban** megtekintéséhez és a PowerBI.com webhelyen a jelentés megosztásához.
   
    ![Közzététel a Power BI sikeres! Nyissa meg az oktatóanyag a Power bi-ban](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Hozzon létre egy irányítópultot a powerbi.com webhelyen
Most, hogy egy jelentést, lehetővé teszi, hogy ossza meg a PowerBI.com webhelyen

Amikor közzéteszi a jelentést a Power BI Desktopból a powerbi.com webhelyen, létrehoz egy **jelentés** és a egy **adatkészlet** a PowerBI.com-bérlőben. Például után, közzétett egy jelentést nevű **PowerBITutorial** a powerbi.com webhelyen, látni fogja PowerBITutorial is a **jelentések** és **adatkészletek** a szakaszok A powerbi.com webhelyen.

   ![Képernyőfelvétel az új jelentés és adatkészlet a powerbi.com webhelyen](./media/powerbi-visualize/powerbi-reports-datasets.png)

A megosztható irányítópultok létrehozásához kattintson a **élő oldal rögzítése** a powerbi.com-on a jelentés gombra.

   ![Képernyőfelvétel az új jelentés és adatkészlet a powerbi.com webhelyen](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Ezután kövesse a [jelentésből származó csempe rögzítése](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) hozhat létre egy új irányítópultot. 

Emellett érdemes jelentés alkalmi módosítások egy irányítópult létrehozása előtt. Ajánlott azonban a Power BI Desktop használhat hajtsa végre a módosításokat, és tegye közzé újra a jelentést a powerbi.com webhelyre.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

![Screenshot of Refresh Now in PowerBI.com](./media/powerbi-visualize/power-bi-refresh-now.png)

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    ![Screenshot of the Schedule Refresh in PowerBI.com](./media/powerbi-visualize/power-bi-schedule-refresh.png)
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>További lépések
* A Power BI kapcsolatos további információkért lásd: [Power bi-ban – első lépések](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Azure Cosmos DB kapcsolatos további információkért tekintse meg a [Azure Cosmos DB-dokumentációk kezdőlapját](https://azure.microsoft.com/documentation/services/cosmos-db/).

