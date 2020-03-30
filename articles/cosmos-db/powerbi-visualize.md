---
title: Power BI oktatóanyag az Azure Cosmos DB-összekötőhöz
description: Ezzel a Power BI-oktatóanyaggal importálhatja a JSON-t, éleslátó jelentéseket hozhat létre, és az Azure Cosmos DB- és Power BI-összekötővel jelenítheti meg az adatokat.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: 1dbdd428a54ebf38c7b880bb9530935c0f748226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69616804"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Azure Cosmos DB-adatok vizualizációja a Power BI-összekötő használatával

[A Power BI](https://powerbi.microsoft.com/) egy online szolgáltatás, ahol irányítópultokat és jelentéseket hozhat létre és oszthat meg. A Power BI Desktop egy jelentéskészítő eszköz, amely lehetővé teszi a különböző adatforrásokból származó adatok lekérését. Az Azure Cosmos DB a Power BI Desktoppal használható egyik adatforrás. A Power BI Desktopot a Power BI-hoz készült Azure Cosmos DB-összekötővel csatlakoztathatja az Azure Cosmos DB-fiókhoz.  Miután importálta az Azure Cosmos DB-ben található adatokat a Power BI-ba, átalakíthatja őket, valamint jelentéseket hozhat létre, amelyeket közzé tehet a Power BI-ban.   

Ez a cikk az Azure Cosmos DB-fiók a Power BI Desktophoz való csatlakozásához szükséges lépéseket ismerteti. A csatlakoztatás után egy gyűjteményhez léphet, kinyerheti az adatok, átalakíthatja a JSON-adatokat táblázatos formátumba, majd közzétehet egy jelentést a Power BI-ban.

> [!NOTE]
> Az Azure Cosmos DB Power BI-csatlakozója csatlakozik a Power BI Desktophoz. A Power BI Desktopban létrehozott jelentések közzétehetők PowerBI.com. Az Azure Cosmos DB-adatok közvetlen kinyerése nem hajtható végre PowerBI.com. 

> [!NOTE]
> Az Azure Cosmos DB-hez a Power BI-összekötővel való csatlakozás jelenleg csak az Azure Cosmos DB SQL API és a Gremlin API-fiókok számára támogatott.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt követene a Power BI-oktatóanyag utasításait, győződjön meg arról, hogy hozzáfér a következő erőforrásokhoz:

* [Töltse le a Power BI Desktop legújabb verzióját.](https://powerbi.microsoft.com/desktop)

* Töltse le a [minta vulkán adatait](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/master/SampleData/VolcanoData.json) a GitHubról.

* [Hozzon létre egy Azure Cosmos-adatbázis-fiókot,](https://azure.microsoft.com/documentation/articles/create-account/) és importálja a vulkán adatait az [Azure Cosmos DB adatáttelepítési eszközzel.](import-data.md) Adatok importálásakor vegye figyelembe a forrás és a célhelyek alábbi beállításait az adatáttelepítési eszközben:

   * **Forrásparaméterek** 

       * **Importálás innen:** JSON-fájl(ok)

   * **Célparaméterek** 

      * **Kapcsolati karakterlánc:**`AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Partíciókulcs:** /Ország 

      * **Gyűjtemény átviteli:** 1000 

Ahhoz, hogy a jelentéseket PowerBI.com oszthassa meg, fiókkal kell rendelkeznie PowerBI.com.  Ha többet szeretne megtudni a Power [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing)BI-ról és a Power BI Pro-ról, olvassa el az Témakört.

## <a name="lets-get-started"></a>Kezdjük el.
Ebben a bemutatóban képzeljük el, hogy geológus vagy, aki a világ minden tájáról tanulmányozza a vulkánokat. A vulkán adatok egy Azure Cosmos DB-fiókban tárolódnak, és a JSON-dokumentum formátuma a következő:

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

A vulkánadatokat az Azure Cosmos DB-fiókjából fogja lekérni, és egy interaktív Power BI-jelentésben jelenítheti meg az adatokat.

1. Futtassa a Power BI Desktop ot.

2. Az **üdvözlőképernyőről adatokat kaphat,** a **Legutóbbi források**vagy az **Egyéb jelentések megnyitása című** témakört közvetlenül az üdvözlőképernyőről kaphat. A képernyő bezárásához válassza az "X" gombot a jobb felső sarokban. Megjelenik a Power BI Desktop **Jelentés** nézete.
   
   ![Power BI Desktop jelentésnézet – Power BI-csatlakozó](./media/powerbi-visualize/power_bi_connector_pbireportview.png)

3. Válassza a **Kezdőlap** menüszalagot, majd kattintson az **Adatok bekésése gombra.**  Meg kell jelennie **az Adatok bekésése** ablaknak.

4. Kattintson az **Azure**,válassza **az Azure Cosmos DB (Béta)** lehetőséget, majd kattintson a **Csatlakozás gombra.** 

    ![Power BI Desktop Adatok beszedése – Power BI-csatlakozó](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   

5. Az **Összekötő megtekintése** lapon kattintson a **Folytatás gombra.** Megjelenik **az Azure Cosmos DB** ablaka.

6. Adja meg az Azure Cosmos DB-fiók végpontjának URL-címét, amelyből az alábbi módon szeretné beolvasni az adatokat, majd kattintson az **OK**gombra. Saját fiókjának használatához lekérheti az URL-címet az Azure Portal **Keys paneluridában** lévő URI-mezőből. Megadhatja az adatbázis nevét, a gyűjtemény nevét, vagy a navigátor segítségével kiválaszthatja az adatbázist és a gyűjteményt az adatok származásának azonosításához.
   
7. Ha első alkalommal csatlakozik ehhez a végponthoz, a rendszer kéri a fiókkulcsot. Saját fiókjához olvassa be a kulcsot az **Elsődleges kulcs** mezőből az Azure **Portal írásvédett kulcsok** panelén. Írja be a megfelelő kulcsot, majd kattintson a **Csatlakozás gombra.**
   
   Azt javasoljuk, hogy a jelentések készítéséhez használja az írásvédett kulcsot. Ez megakadályozza, hogy a főkulcs szükségtelenmértékben ki legyen téve a lehetséges biztonsági kockázatoknak. A csak olvasható kulcs érhető el az Azure Portal **Keys** panelen érhető el. 
    
8. A fiók sikeres csatlakoztatása után megjelenik a **Navigátor** ablaktábla. A **Navigátor** megjeleníti a fiók alatti adatbázisok listáját.

9. Kattintson és bontsa ki az adatbázist, ahonnan a jelentés adatai származnak, válassza **volcanodb** (az adatbázis neve eltérő lehet).   

10. Most válasszon ki egy gyűjteményt, amely tartalmazza a lekérni kívánt adatokat, válassza a **volcano1** elemet (a gyűjtemény neve eltérő lehet).
    
    A Betekintő ablaktábla a Rekordelemek listáját jeleníti **meg.**  A Power BI-ban egy dokumentum **rekordtípusként** jelenik meg. Hasonlóképpen, a dokumentumon belüli beágyazott JSON-blokk is **rekord.**
    
    ![Power BI oktatóanyag az Azure Cosmos DB Power BI-csatlakozójához – Navigátor ablak](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Kattintson a **Szerkesztés gombra** az adatok átalakításához egy új ablakban a Lekérdezésszerkesztő elindításához.

## <a name="flattening-and-transforming-json-documents"></a>JSON-dokumentumok összeolvasztása és átalakítása
1. Váltson a Power BI Lekérdezésszerkesztő ablakára, ahol a középső ablaktábla **Dokumentum** oszlopa található.
   ![Power BI Desktop Lekérdezésszerkesztő](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Kattintson a **Dokumentum** oszlopfejléc jobb oldalán található kibontóra.  Megjelenik a mezők listáját tartalmazó helyi menü.  Válassza ki a jelentéshez szükséges mezőket, például a vulkán nevét, országát, területét, helyét, magasságát, típusát, állapotát és utolsó ismert kitörését. Törölje a jelet az **Eredeti oszlopnév használata előtagként** jelölőnégyzetből, majd kattintson az **OK**gombra.
   
    ![Power BI oktatóanyag az Azure Cosmos DB Power BI-összekötőhöz – Dokumentumok kibontása](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. A középső ablaktábla megjeleníti az eredmény előnézetét a kijelölt mezőkkel.
   
    ![Power BI oktatóanyag az Azure Cosmos DB Power BI-összekötőhöz – Eredmények simítása](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. A példánkban a Location tulajdonság egy GeoJSON blokk egy dokumentumban.  Amint láthatja, a Hely **bejegyzéstípusként** jelenik meg a Power BI Desktopban.  
5. Kattintson a Document.Location oszlopfejléc jobb oldalán található kibontóra.  Megjelenik a szöveg- és koordináták mezőt tartalmazó helyi menü.  Jelöljük ki a koordináták mezőt, győződjünk meg arról, hogy **az Eredeti oszlopnév használata előtagként** nincs kijelölve, majd kattintsunk az **OK**gombra.
   
    ![Power BI oktatóanyag az Azure Cosmos DB Power BI-összekötőhöz – Helyrekord](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. A középső ablaktáblán megjelenik a **Lista** típusú koordináták oszlopa.  Amint az az oktatóanyag elején látható, az oktatóanyag GeoJSON-adatai pont típusúak, és a koordináták tömbjében rögzített szélességi és hosszúsági értékek vannak rögzítve.
   
    A koordináták[0] elem a hosszúság, míg a koordináták[1] a Szélességt jelölik.
    ![Power BI oktatóanyag az Azure Cosmos DB Power BI-összekötőhöz – Koordináták listája](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. A koordináta-tömb összeolvasztásához hozzon létre egy LatLong nevű **egyéni oszlopot.**  Jelölje ki az **Oszlop hozzáadása** menüszalagot, és kattintson az Egyéni **oszlop gombra.**  Megjelenik **az Egyéni oszlop** ablak.
8. Adja meg az új oszlop nevét, pl. LatLong.
9. Ezután adja meg az új oszlop egyéni képletét.  Példánkban a szélességi és hosszúsági értékeket vesszővel elválasztjuk, amint az az `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`alábbi képletet mutatja: . Kattintson az **OK** gombra.
   
    Az adatelemzési kifejezésekről (DAX) és a DAX-függvényeket is beleértve további információt a Dax BI Alapismeretek a [Power BI Desktop ban talál.](https://docs.microsoft.com/power-bi/desktop-quickstart-learn-dax-basics)
   
    ![Power BI oktatóanyag az Azure Cosmos DB Power BI-csatlakozójához – Egyéni oszlop hozzáadása](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. Most a középső ablaktábla az új LatLong oszlopokat jeleníti meg, amelyek et az értékekkel töltötték fel.
    
    ![Power BI oktatóanyag az Azure Cosmos DB Power BI-csatlakozójához – Egyéni LatLong oszlop](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Ha hibaüzenet jelenik meg az új oszlopban, győződjön meg arról, hogy a Lekérdezési beállítások csoportban alkalmazott lépések megegyeznek az alábbi ábrával:
    
    ![Alkalmazott lépéseket kell forrás, navigáció, kibontott dokumentum, bővített document.Location, Hozzáadott Egyéni](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Ha a lépések eltérőek, törölje a további lépéseket, és próbálja meg újra hozzáadni az egyéni oszlopot. 

11. Az adatmodell mentéséhez kattintson a **Bezárás és** az Alkalmaz gombra.
    
    ![Power BI oktatóanyag az Azure Cosmos DB Power BI-csatlakozójához – & bezárása](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>A jelentések összeállítása
A Power BI Asztali Jelentés nézetben elkezdheti az adatok megjelenítéséhez szükséges jelentések létrehozását.  Jelentéseket úgy hozhat létre, hogy mezőket húz a **Jelentés** vászonra.

![Power BI Desktop jelentésnézet – Power BI-csatlakozó](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

A Jelentés nézetben a következőket kell találnia:

1. A **Mezők** ablaktábla itt láthatja a jelentésekhez használható mezőket tartalmazó adatmodellek listáját.
2. A **Vizualizációk** ablaktábla. A jelentések egy vagy több vizualizációt tartalmazhatnak.  A **Képi megjelenítések** ablaktáblából kiválaszthatja az igényeinek megfelelő vizuális típusokat.
3. A **Jelentés** vászon, itt hozhat létre a jelentés vizualizációit.
4. A **Jelentés** lap. A Power BI Desktopban több jelentéslapot is hozzáadhat.

Az alábbiakban egy egyszerű interaktív térképnézet-jelentés létrehozásának alapvető lépéseit mutatja be.

1. Példánkban egy térképnézetet hozunk létre, amely megmutatja az egyes vulkánok helyét.  A **Vizualizációk** ablaktáblában kattintson a Térkép vizualizációtípusra a fenti képernyőképen kiemelt módon.  A **Jelentés** vásznon meg kell jelennie a Térkép vizualizációs típusnak.  A **Vizualizációs** ablaktáblán a Térkép vizualizáció típusához kapcsolódó tulajdonságok at is meg kell jelenítenie.
2. Most húzza a LatLong mezőt a **Mezők** ablaktáblából a **Megjelenítési** ablaktábla **Hely** tulajdonságára.
3. Ezután húzza a Volcano Name mezőt a **Legend** tulajdonságra.  
4. Ezután húzza a Magasság mezőt a **Méret** tulajdonságra.  
5. Most látnia kell a Térkép vizualizációt, amely buborékokat jelenít meg, jelezve az egyes vulkánok helyét a buborék méretével, amely a vulkán magasságához kapcsolódik.
6. Most létrehozott egy alapjelentést.  További vizualizációk hozzáadásával tovább testreszabhatja a jelentést.  A mi esetünkben, mi hozzá egy volcano típusú szeletelő, hogy a jelentés interaktív.  
   
7. Kattintson a Fájl menü **Mentés parancsára,** és mentse a fájlt PowerBITutorial.pbix fájlként.

## <a name="publish-and-share-your-report"></a>A jelentés közzététele és megosztása
A jelentés megosztásához fiókkal kell rendelkeznie a PowerBI.com.

1. A Power BI Desktopban kattintson a **Kezdőlap** menüszalagra.
2. Kattintson a **Publish** (Közzététel) gombra.  A rendszer kéri, hogy adja meg a PowerBI.com fiók felhasználónevét és jelszavát.
3. A hitelesítő adatok hitelesítése után a jelentés közzé lesz téve a kiválasztott úti cél számára.
4. Kattintson **a Power BI "PowerBITutorial.pbix" megnyitása** elemre, ha megtekintheti és megoszthatja jelentését a PowerBI.com.
   
    ![Közzététel a Power BI sikerességére! Oktatóanyag megnyitása a Power BI-ban](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Hozzon létre egy irányítópultot a PowerBI.com webhelyen
Most, hogy már van egy jelentést, lehetővé teszi, ossza meg a PowerBI.com

Amikor közzéteszi a jelentést a Power BI Desktopról PowerBI.com, **az létrehoz** egy jelentést és egy **adatkészletet** a PowerBI.com-bérlőben. Például miután közzétett egy **PowerBITutorial** nevű jelentést PowerBI.com, a PowerBITutorial a **PowerBI.com Jelentések** és **adatkészletek** szakaszában is megjelenik.

   ![Képernyőkép az új jelentésről és adatkészletről a PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Megosztható irányítópult létrehozásához kattints az élő **oldal rögzítése** gombra a PowerBI.com jelentésedben.

   ![Képernyőkép az új jelentésről és adatkészletről a PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Ezután kövesse a [Jelentés csempéjének rögzítése](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) című részben található utasításokat egy új irányítópult létrehozásához. 

Az irányítópult létrehozása előtt eseti módosításokat is elvégezhet a jelentéskészítéshez. Javasoljuk azonban, hogy a Power BI Desktop segítségével hajtsa végre a módosításokat, és tegye közzé újra a jelentést a PowerBI.com.

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
* A Power BI-ról az [Első lépések a Power BI-val](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)való ismerkedés .
* Ha többet szeretne megtudni az Azure Cosmos DB-ről, tekintse meg az [Azure Cosmos DB dokumentációjának céloldalát.](https://azure.microsoft.com/documentation/services/cosmos-db/)

