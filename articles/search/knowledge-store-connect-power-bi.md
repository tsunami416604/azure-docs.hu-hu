---
title: Kapcsolódás a Knowledge Store-hoz (előzetes verzió) Power BI
titleSuffix: Azure Cognitive Search
description: Egy Azure Cognitive Search Knowledge Store (előzetes verzió) összekötése az elemzéshez és a feltáráshoz Power BI.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 4fd71a7f322cb2672eb485f17e4de2619a7c2d2c
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270035"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Tudástár összekötése Power BI

> [!IMPORTANT] 
> A Knowledge Store jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) előzetes funkciókat biztosít. Jelenleg korlátozott a portál támogatása, és nincs .NET SDK-támogatás.

Ebből a cikkből megtudhatja, hogyan csatlakozhat a Knowledge Store-hoz, és hogyan deríthető fel a Power Query használatával a Power BI Desktop alkalmazásban. A sablonokkal gyorsabban elsajátíthatja az első lépéseket, vagy létrehozhat egy teljesen új irányítópultot.

+ Kövesse a [Tudásbázis létrehozása a Azure Portalban](knowledge-store-create-portal.md) című témakör lépéseit, vagy [hozzon létre egy Azure Cognitive Search Knowledge Store](knowledge-store-create-rest.md) -t a REST használatával, és hozza létre az ebben az útmutatóban használt, a minta tudásbázist. Szüksége lesz a Knowledge Store létrehozásához használt Azure Storage-fiók nevére, valamint a Azure Portal elérési kulcsára.

+ [Power BI Desktop telepítése](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>Példa Power BI sablonra – csak Azure Portal

Ha [a Azure Portal használatával](knowledge-store-create-portal.md)hoz létre egy tudásbázist, lehetősége van egy [Power bi sablon](https://github.com/Azure-Samples/cognitive-search-templates) letöltésére az **adat importálása** varázsló második oldalán. Ez a sablon több vizualizációt, például WordCloud és hálózati Navigátort biztosít a szöveges tartalomhoz. 

Kattintson a **Beolvasás Power bi sablon** lehetőségre a **kognitív képességek hozzáadása** oldalon a sablon nyilvános GitHub-helyről való lekéréséhez és letöltéséhez. A varázsló módosítja a sablont úgy, hogy az megfeleljen az adatmennyiségnek, ahogy azt a varázslóban megadott Tudásbázis-prognózis rögzíti. Emiatt a letöltött sablon a varázsló minden egyes futtatásakor eltérő lesz, feltételezve, hogy különböző adatbevitelek és szaktudás-kiválasztás történik.

![Példa Azure Cognitive Search Power BI-sablonra](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Minta Power BI sablon")

> [!NOTE]
> Bár a sablon le van töltve, miközben a varázsló a közepén repül, meg kell várnia, amíg az áruház ténylegesen létre nem jön az Azure Table Storage-ban, mielőtt használni lehetne.

## <a name="connect-with-power-bi"></a>Kapcsolódás Power BI-jal

1. Indítsa el Power BI Desktop és kattintson **az adatlekérdezés**gombra.

1. Az **adatlekérdezés** ablakban válassza az **Azure**lehetőséget, majd válassza az **Azure Table Storage**elemet.

1. Kattintson a **Csatlakozás** gombra.

1. A **fiók neve vagy URL-címe**mezőben adja meg az Azure Storage-fiók nevét (a teljes URL-cím lesz létrehozva).

1. Ha a rendszer kéri, adja meg a Storage-fiók kulcsát.

1. Válassza ki azokat a táblákat, amelyek tartalmazzák az előző forgatókönyvek által létrehozott, a Hotel által áttekintett adatelemzési 

   + A portál útmutatójában a *hotelReviewsSsDocument*, a *hotelReviewsSsEntities*, a *hotelReviewsSsKeyPhrases*és a *hotelReviewsSsPages*tábla neve szerepel. 
   
   + A REST-útmutatóban a *hotelReviewsDocument*, a *hotelReviewsPages*, a *hotelReviewsKeyPhrases*és a *hotelReviewsSentiment*tábla neve szerepel.

1. Kattintson a **Betöltés**elemre.

1. A felső menüszalagon kattintson a **lekérdezések szerkesztése** elemre a **Power Query-szerkesztő**megnyitásához.

   ![Power Query megnyitása](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Power Query megnyitása")

1. Válassza a *hotelReviewsSsDocument*lehetőséget, majd távolítsa el a *PartitionKey*, a *RowKey*és az *időbélyegző* oszlopokat. 
   ![Táblák szerkesztése](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Táblák szerkesztése")

1. A *tartalom*kibontásához kattintson a táblázat jobb felső sarkában található, ellentétes nyíllal ellátott ikonra. Amikor megjelenik az oszlopok listája, válassza a minden oszlop lehetőséget, majd törölje a metaadatokkal kezdődő oszlopokat. A kijelölt oszlopok megjelenítéséhez kattintson **az OK** gombra.

   ![Táblák szerkesztése](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Tartalom kibontása")

1. Módosítsa a következő oszlopok adattípusát az oszlop bal felső részén található ABC-123 ikonra kattintva.

   + A *Content. Latitude* és a *Content. hosszúság*esetében válassza a **decimális szám**lehetőséget.
   + A *Content. reviews_date* és a *Content. reviews_dateAdded*esetében válassza a **dátum/idő**lehetőséget.

   ![Adattípusok módosítása](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Adattípusok módosítása")

1. Válassza a *hotelReviewsSsPages*lehetőséget, majd ismételje meg a 9. és a 10. lépést az oszlopok törléséhez és a *tartalom*kibontásához.
1. Módosítsa a *Content. SentimentScore* adattípus adattípusát **decimális számra**.
1. Válassza ki a *hotelReviewsSsKeyPhrases* , és ismételje meg a 9. és a 10. lépést az oszlopok törléséhez és a *tartalom*kibontásához. Ehhez a táblához nincs Adattípus-módosítás.

1. A parancssorban kattintson a **Bezárás és alkalmaz**gombra.

1. A bal oldali navigációs ablaktáblán kattintson a modell csempére, és ellenőrizze, hogy Power BI a három tábla közötti kapcsolatokat jeleníti-e meg.

   ![Kapcsolatok ellenőrzése](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Kapcsolatok ellenőrzése")

1. Kattintson duplán az egyes kapcsolatokra, és győződjön meg arról, hogy a **kereszt-szűrés iránya** **mindkettőre**van beállítva.  Ez lehetővé teszi, hogy a vizualizációk egy szűrő alkalmazása esetén frissüljenek.

1. A bal oldali navigációs panelen kattintson a jelentés csempére az adatvizualizációk használatával való ismerkedéshez. A szöveges mezőkhöz a táblázatok és a kártyák hasznos vizualizációk. A táblázat vagy kártya kitöltéséhez a három tábla mezői közül választhat. 

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) resource, created at the *S0* tier, in the same region as Azure Cognitive Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha saját előfizetésében dolgozik, a projekt végén érdemes megállapítani, hogy továbbra is szüksége van-e a létrehozott erőforrásokra. A már futó erőforrások pénzbe kerülnek. Az erőforrásokat egyenként is törölheti, vagy az erőforráscsoport törlésével törölheti a teljes erőforrás-készletet.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon.

## <a name="next-steps"></a>Következő lépések

Az alábbi cikkből megtudhatja, hogyan derítheti fel ezt a Knowledge áruházat a Storage Explorer használatával.

> [!div class="nextstepaction"]
> [Megtekintés Storage Explorer](knowledge-store-view-storage-explorer.md)