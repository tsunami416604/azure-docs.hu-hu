---
title: A Power BI oktatóanyag Azure Cosmos DB összekötő |} Microsoft Docs
description: A Power BI-oktatóanyag segítségével importálása JSON, osztályon jelentések létrehozása és az Azure Cosmos DB és a Power BI-összekötővel adatainak megjelenítése.
keywords: üzleti intelligencia oktatóanyag Power, az adatok, a kiemelt üzleti intelligencia összekötő megjelenítése
services: cosmos-db
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: ''
ms.assetid: cd1b7f70-ef99-40b7-ab1c-f5f3e97641f7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2018
ms.author: mimig
ms.openlocfilehash: 74be3e5b25401a7811c1af23a0a7e2887f9055c1
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="power-bi-tutorial-for-azure-cosmos-db-visualize-data-using-the-power-bi-connector"></a>A Power BI oktatóanyag az Azure Cosmos DB: a Power BI-összekötővel adatok megjelenítése
[Powerbi.com webhelyen](https://powerbi.microsoft.com/) egy olyan online szolgáltatás, ahol létrehozása és megosztása irányítópultokat és jelentéseket, és a szervezet számára fontos adatokkal.  A Power BI Desktop egy dedikált jelentéskészítési eszköz, amely lehetővé teszi a különféle adatforrásokból származó adatok beolvasása egyesítése és a átalakíthatja az adatokat, hatékony képi megjelenítések létrehozásához, és tegye közzé a jelentéseket a Power bi-bA.  A Power BI Desktop legújabb verziójával most csatlakozhat az Azure Cosmos DB összekötőn keresztül Azure Cosmos DB fiókját Power bi.   

A Power bi-ban az oktatóanyagban azt lépésről lépésre bemutatjuk, csatlakoztathatja egy Azure Cosmos DB-fiókhoz a Power BI Desktop, nyissa meg egy gyűjteményt, amelyben szeretnénk bontsa ki az adatokat a Navigátor, JSON adatok átalakítása a Power BI Desktop lekérdezés-szerkesztő használatával táblázatos formátumban , és build és a jelentés közzététele a powerbi.com webhelyen.

A Power BI-oktatóanyag befejezése után képes lesz a következő kérdések megválaszolásához:  

* Hogyan készíthető jelentések adatokkal az Azure Cosmos Adatbázisból Power BI Desktop használatával?
* Hogyan lehet a Power BI Desktop Azure Cosmos DB fiók csatlakozni?
* Hogyan lehet lekérni a Power BI Desktop egy gyűjteményből adatok?
* Hogyan alakíthatja át a beágyazott JSON-adatokat a Power BI Desktop?
* Hogyan közzététele és megosztása a jelentést a powerbi.com webhelyen?

> [!NOTE]
> A Power BI-összekötő Azure Cosmos DB kivonása és az adatok átalakítása kapcsolódik a Power BI Desktop. A Power BI Desktopban létrehozott jelentések majd a powerbi.com webhelyre tehetők közzé. Közvetlen kivonása és Azure Cosmos DB adatok átalakítása nem hajtható végre a powerbi.com webhelyen. 

> [!NOTE]
> Azure Cosmos DB Power bi-bA a MongoDB API használatával kapcsolódni kell használnia a [Simba MongoDB ODBC-illesztőprogram](http://www.simba.com/drivers/mongodb-odbc-jdbc/).

## <a name="prerequisites"></a>Előfeltételek
Előtt a Power BI-oktatóanyag utasításainak követése, győződjön meg arról, hogy rendelkezik-e a következő erőforrások elérése:

* [A Power BI Desktop legújabb verziójának](https://powerbi.microsoft.com/desktop).
* A bemutató fiók vagy Azure Cosmos DB fiókban tárolt adatok elérésére.
  * A bemutató fiók fel van töltve, ebben az oktatóanyagban szereplő mexikói adatokkal. Ez a bemutató fiók bármely SLA-k nem kötődik, és csak bemutató célokra tervezték.  Azt fenntartja a jogot arra a bemutató fiók beleértve módosításokat, de nem korlátozódik, leállítja a fiókot, módosítja azt a kulcsot, történő hozzáférés, módosítása, és az adatok törléséhez bármikor előzetes közlemény, illetve ok nélkül.
    * URL: https://analytics.documents.azure.com
    * Read-only key: MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==
  * Vagy a saját fiók létrehozásához lásd: [hozzon létre egy Azure Cosmos DB adatbázisfiókot az Azure-portálon](https://azure.microsoft.com/documentation/articles/create-account/). Ezt követően minta mexikói beolvasandó adatok hasonló ahhoz, ami ebben az oktatóanyagban használt (de nem tartalmaz a GeoJSON blokkok), lásd: a [NOAA hely](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5) , majd importálja az adatok használata a [Azure Cosmos DB adatáttelepítési eszköz](import-data.md).

A jelentést a powerbi.com webhelyen megosztásához fiókkal kell rendelkeznie a powerbi.com webhelyen.  További információ a Power BI szabad és a Power BI Pro, látogasson el [ https://powerbi.microsoft.com/pricing ](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Első lépések
Ebben az oktatóanyagban most tegyük fel, hogy Ön egy tanulmányozza a világ különböző vulkánok alakították geologist.  Mexikói adatok Azure Cosmos DB fiók tárolva van, és a JSON-dokumentumokat az alábbi minta dokumentum tűnik.

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

Szeretné a Mexikói adatainak lekérése az Azure Cosmos DB fiók és a például a következő jelentés interaktív Power BI-jelentés adatainak megjelenítése.

![Az oktatóprogram a Power BI a Power BI-összekötőjével, akkor képes lesz az adatok ábrázolása a Power BI Desktop mexikói jelentés a](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)

Most próbálja ki! Lássunk neki.

1. A Power BI Desktop futtassa a munkaállomáson.
2. A Power BI Desktop eszköztárat, egy *üdvözlő* képernyő jelenik meg.
   
    ![A Power BI Desktop üdvözlőképernyő - Power BI connector](./media/powerbi-visualize/power_bi_connector_welcome.png)
3. Is **adatok beolvasása**, lásd: **források**, vagy **megnyitható más jelentések** közvetlenül a *üdvözlő* képernyő.  Kattintson a jobb felső sarokban lévő X bezárhatja a képernyőt. A **jelentés** ábrázolása a Power BI Desktop jelenik meg.
   
    ![A Power BI Desktop jelentés nézet – Power BI connector](./media/powerbi-visualize/power_bi_connector_pbireportview.png)
4. Válassza ki a **Home** menüszalag, majd kattintson a **adatok beolvasása**.  A **adatok beolvasása** ablak meg kell jelennie.
5. Kattintson a **Azure**, jelölje be **Azure Cosmos DB (béta)**, és kattintson a **Connect**. 

    ![A Power BI Desktop - adatok beolvasása a Power BI connector](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   
6. Az a **Preview összekötő** kattintson **Folytatás**. A **Azure Cosmos DB** ablak jelenik meg.
7. Az Azure Cosmos DB fiók végpont URL-cím kívánja az adatokat lekérdezni a lent látható módon, majd kattintson a **OK**. Saját fiók használatára, URI listájából kérheti le az URL-címet a **[kulcsok](manage-account.md#keys)** panel az Azure portálról. A bemutató a fiókot használja, írja be a következőt `https://analytics.documents.azure.com` URL-címe. 
   
    Az adatbázis neve, a gyűjtemény nevét és a SQL-utasítás üresen hagyja, a mezők kitöltése nem kötelező.  A Navigátor használjuk Ehelyett válassza ki az adatbázis és gyűjtemény azonosítására, ha az adatok származik.
   
    ![A Power BI oktatóanyag Azure Cosmos DB Power BI Connector - asztali csatlakozás ablak](./media/powerbi-visualize/power_bi_connector_pbiconnectwindow.png)
8. Ha első alkalommal csatlakozik ehhez a végponthoz, a fiókjához tartozó kéri. A saját fiókját a kulcs lekérését a **elsődleges kulcs** párbeszédpanel a **[írásvédett kulcsok](manage-account.md#keys)** panel az Azure portálról. A bemutató fiók a kulcsot meg kell `MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==`. Adja meg a megfelelő kulcsot, és kattintson a **Connect**.
   
    Azt javasoljuk, hogy a csak olvasható-kulcsot használ, jelentések készítésekor.  Ez megakadályozza, hogy a főkulcs szükségtelen kitettségtől biztonsági kockázatok. A csak olvasható kulcs érhető el a [kulcsok](manage-account.md#keys) panel az Azure-portálon, vagy használhatja a fenti bemutató fiók adatait.
   
    ![A Power BI oktatóanyag Azure Cosmos DB Power BI Connector - fiókjához](./media/powerbi-visualize/power_bi_connector_pbidocumentdbkey.png)
    
    > [!NOTE] 
    > Ha a hiba, amely szerint "a megadott adatbázis nem található." Ez a megoldás lépéseit lásd [Power BI probléma](https://community.powerbi.com/t5/Issues/Document-DB-Power-BI/idi-p/208200).
    
9. Ha a fiók sikeresen csatlakozik, a **Navigator** jelenik meg.  A **Navigator** jelennek meg a fiók alatt az adatbázisok listája.
10. Kattintson, és bontsa ki az adatbázishoz, ahol az adatokat a jelentés határozza meg, ha a bemutató fiók használata válasszon **volcanodb**.   
11. Jelölje ki a gyűjteményt, amelyik be szeretné olvasni az adatokat. Ha a bemutató-fiókot használ, jelölje be **volcano1**.
    
    Az előnézeti ablaktáblában láthatja listája látható **rekord** elemeket.  A dokumentum egy jelenik meg egy **rekord** típus a Power bi-ban. Ehhez hasonlóan belül egy dokumentumot egy beágyazott JSON blokkot is van egy **rekord**.
    
    ![A Power BI oktatóanyag Azure Cosmos DB Power BI Connector - Navigator ablak](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Kattintson a **szerkesztése** elindíthatja a Lekérdezésszerkesztő az adatok átalakítására egy új ablakban.

## <a name="flattening-and-transforming-json-documents"></a>Egybesimítását és JSON-dokumentumok átalakítása
1. Váltás a Power BI Lekérdezésszerkesztő ablak, ahol a **dokumentum** oszlop a középső ablaktáblán.
   ![A Power BI Desktop Lekérdezésszerkesztő](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Kattintson a jobb oldalán a bővítő a **dokumentum** oszlop fejlécére.  A helyi menü mezők listája jelenik meg.  Válassza ki a példány szükséges a jelentés mezők, mexikói neve, ország, régió, hely, jogosultságszint-emelési, típusát, állapota és utolsó tudja határozzák, és kattintson **OK**.
   
    ![A Power BI-oktatóanyag Azure Cosmos DB Power BI Connector - bontsa ki a dokumentumok](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. A középső ablaktáblán az eredmény előnézete megjeleníti a kijelölt mezőket.
   
    ![A Power BI-oktatóanyag Azure Cosmos DB Power BI Connector - eredmények Egybesimítására](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. A példánkban a Location tulajdonság egy GeoJSON letiltása végzését a dokumentumok.  Ahogy látja, a hely egy jelenik meg egy **rekord** a Power BI Desktop típusa.  
5. Kattintson a jobb oldalán a hely oszlopfejléc bővítő.  A helyi menüt, típusa és a mezőben jelenik meg.  Most jelölje ki a koordináták mezőt, majd kattintson **OK**.
   
    ![A Power BI oktatóanyag Azure Cosmos DB Power BI Connector - bejegyzés](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. A középső ablaktáblán megjelenik koordináták oszlop **lista** típusa.  Az oktatóanyag elején látható, a GeoJSON-adatokat ebben az oktatóanyagban pont típusa nem a koordináták a tömb rögzített szélességi és hosszúsági értékeket.
   
    A koordináták [0] elem hosszúság jelöli, amíg koordináták [1] szélesség jelöli.
    ![A Power BI oktatóanyag Azure Cosmos DB Power BI Connector - koordináták listája](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. A koordináták tömb egybesimítására, létrehozunk egy **egyéni oszlop** LatLong nevezik.  Válassza ki a **oszlop hozzáadása** , majd kattintson a menüszalag **egyéni oszlop hozzáadása**.  A **egyéni oszlop hozzáadása** ablak meg kell jelennie.
8. Adja meg a létrehozandó oszlop, például LatLong nevét.
9. Ezt követően adja meg a létrehozandó oszlop értékeit egyéni képlet.  A fenti példában, azt fogja összefűzésére a szélességi és hosszúsági értékeket vesszővel elválasztva, a következő képlettel alább látható módon: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Kattintson az **OK** gombra.
   
    Az adatok elemzése kifejezések (DAX) DAX-funkciók ideértve további információkért látogasson el a [a Power BI Desktop alapvető DAX](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop).
   
    ![A Power BI oktatóanyag Azure Cosmos DB Power BI Connector - egyéni oszlop hozzáadása](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)
10. Most a középső ablaktáblán megjelenik az új LatLong oszlop feltöltve a szélességi és hosszúsági értékeket vesszővel elválasztva.
    
    ![A Power BI oktatóanyag Azure Cosmos DB Power BI Connector - egyéni LatLong oszlop](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Ha az új oszlop arra vonatkozó hibaüzenetet kap, győződjön meg arról, hogy megfelel-e az alábbi ábra a lekérdezés beállításai alkalmazott lépéseket:
    
    ![Alkalmazott lépéseket forrás, a navigációs, a dokumentum kibontva, a Document.Location kibontva, a hozzáadott egyéni kell lennie.](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    A lépések eltérőek, ha törli a további lépéseket, és próbálja meg újból felvenni az egyéni oszlop. 
11. Most már a program kiegészítette egybesimítását az adatok táblázatos formátumban.  Használja ki a lekérdezés-szerkesztőben alakzathoz elérhető funkciók, és az adatok a Cosmos DB átalakítása.  A minta használata, ha módosítja az adattípust a jogosultsági szint emeléséhez **egész számot** módosításával a **adattípus** a a **Home** menüszalagján.
    
    ![A Power BI oktatóanyag Azure Cosmos DB Power BI Connector - oszlop típusának módosítása](./media/powerbi-visualize/power_bi_connector_pbichangetype.png)
12. Kattintson a **zárja be, és alkalmazni** menteni az adatokat az adatmodellbe.
    
    ![A Power BI oktatóanyag az Azure Cosmos DB Power BI connector – zárja be és alkalmazása](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>A jelentések létrehozása
A Power BI Desktop jelentés nézet, ahol készítése jelentéseket adatok megjelenítéséhez.  Húzással mezők jelentéseket hozhat létre a **jelentés** vászonra.

![A Power BI Desktop jelentés nézet – Power BI connector](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

A jelentés nézetben keresse meg:

1. A **mezők** ablaktáblán, ez az adatok modellek is használhatja a jelentések a mezők listája megjelenik.
2. A **képi megjelenítések** ablaktáblán. A jelentés egy vagy több vizuális megjelenítés tartalmazhat.  Válassza ki az igényeinek illeszkedő visual típusok a **képi megjelenítések** ablaktáblán.
3. A **jelentés** vászonra, ahol fog létrehozni a látványelemek a jelentés azt.
4. A **jelentés** lap. A Power BI Desktop jelentés több oldalra is hozzáadhat.

Az alábbiakban látható egy egyszerű interaktív térkép jelentés megtekintése létrehozásának lépéseit.

1. A példa kedvéért létre fogunk hozni egy azt a helyet, az egyes mexikói nézetében.  Az a **képi megjelenítések** ablaktáblában kattintson arra a térkép vizuális típusú, mint a fenti képernyőfelvételen kiemelt.  A térkép festeni a látványelemtípus kell megjelennie a **jelentés** vászonra.  A **képi megjelenítés** ablaktáblán is megjelenjen-e a térkép látványelemtípus kapcsolódó tulajdonságait.
2. Most áthúzása a LatLong mezőt a **mezők** ablaktábla a **hely** tulajdonság **képi megjelenítések** ablaktáblán.
3. A következő áthúzása mexikói név mezőbe a **jelmagyarázat** tulajdonság.  
4. Ezt követően áthúzása a jogosultságszint-emelés mezőt a **mérete** tulajdonság.  
5. Most látnia kell a térképen visual megjelenítő minden mexikói helyét a válaszoknak az összekapcsolása a Mexikói megszerzését buborékot méretének mutató buborékok készlete.
6. Most létrehozott egy egyszerű jelentést.  További további képi megjelenítések való hozzáadásával szabhatja a jelentést.  Ebben az esetben ha a jelentés interaktív mexikói típus szeletelővé hozzáadott.  
   
    ![Képernyőfelvétel az Azure Cosmos DB a Power bi-ban az oktatóanyag befejezése után utolsó Power BI Desktop jelentés](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)

## <a name="publish-and-share-your-report"></a>Közzététele és megosztása a jelentés
A jelentés megosztása fiókkal kell rendelkeznie a powerbi.com webhelyen.

1. A Power BI Desktopban, és kattintson a a **Home** menüszalagján.
2. Kattintson a **Publish** (Közzététel) gombra.  Adja meg a felhasználónevet és jelszót a powerbi.com webhelyen fiók kéri.
3. Ha a hitelesítő adatok hitelesítése, a jelentés a megadott helyre van közzétéve.
4. Kattintson a **nyitott "PowerBITutorial.pbix" Power BI-ban** tekintse meg és osszanak meg a jelentést a powerbi.com webhelyen.
   
    ![A Power BI sikeres közzétételéhez! Nyissa meg az oktatóanyag a Power bi-ban](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Hozzon létre egy irányítópultot a powerbi.com webhelyen
Most, hogy rendelkezik egy jelentéssel, lehetővé teszi, hogy ossza meg a powerbi.com webhelyen

Ha közzéteszi a jelentés a Power BI Desktop a powerbi.com webhelyre, létrehoz egy **jelentés** és egy **Dataset** a powerbi.com webhelyen-bérlőben. Után például a nevű jelentés közzétett **PowerBITutorial** powerbi.com webhelyre, látni fogja PowerBITutorial mind a **jelentések** és **adatkészletek** a szakasz A powerbi.com webhelyen.

   ![Képernyőfelvétel az új jelentést és egy adatkészletet a powerbi.com webhelyen](./media/powerbi-visualize/powerbi-reports-datasets.png)

A megosztható irányítópultok létrehozásához kattintson a **PIN-kód élő oldal** a powerbi.com webhelyen jelentés gombjára.

   ![Képernyőfelvétel az új jelentést és egy adatkészletet a powerbi.com webhelyen](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Ezután kövesse a [rögzítheti egy mozaik jelentésből](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) új irányítópult létrehozása. 

Irányítópult létrehozása előtt is elvégezheti jelentés alkalmi módosításait. Ajánlott azonban, hogy használja-e a Power BI Desktop hajtsa végre a módosításokat, és a jelentést a powerbi.com webhelyre közzé.

## <a name="refresh-data-in-powerbicom"></a>Adatok frissítése a powerbi.com webhelyre
Két módon lehet adatokat, eseti és ütemezett frissítése.

Az ad hoc frissítéshez egyszerűen kattintson a a eclipses (...) által a **Dataset**, pl. PowerBITutorial. Láthatja, beleértve azokat **frissítés most**. Kattintson a **frissítés most** az adatok frissítéséhez.

![Képernyőkép a frissítés most a powerbi.com webhelyen](./media/powerbi-visualize/power-bi-refresh-now.png)

Egy ütemezett frissítés tegye a következőket.

1. Kattintson a **ütemezés frissítése** művelet listájában. 

    ![Képernyőfelvétel a Frissítésütemezés a powerbi.com webhelyen](./media/powerbi-visualize/power-bi-schedule-refresh.png)
2. Az a **beállítások** ablaktáblában bontsa ki a **adatforrás hitelesítő adatai**. 
3. Kattintson a **hitelesítő adatok szerkesztése**. 
   
    A konfigurálás előugró ablak akkor jelenik meg. 
4. Adja meg az adott adatkészlet Azure Cosmos DB fiók kapcsolódni, majd kattintson a kulcs **bejelentkezés**. 
5. Bontsa ki a **ütemezés frissítése** és frissítse az adatkészletet kívánt ütemezést beállítani. 
6. Kattintson a **alkalmaz** és befejezte az ütemezett frissítés beállítása.

## <a name="next-steps"></a>További lépések
* További információ a Power BI, [első lépések a Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Azure Cosmos DB kapcsolatos további tudnivalókért tekintse meg a [Azure Cosmos DB dokumentációjának kezdőlapján](https://azure.microsoft.com/documentation/services/cosmos-db/).

