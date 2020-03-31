---
title: Csatlakozás tudástárolóhoz (előzetes verzió) a Power BI-val
titleSuffix: Azure Cognitive Search
description: Csatlakoztasson egy Azure Cognitive Search tudástárolót (előzetes verziót) a Power BI-val elemzéshez és feltáráshoz.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 4fd71a7f322cb2672eb485f17e4de2619a7c2d2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270035"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Tudástár csatlakoztatása a Power BI-val

> [!IMPORTANT] 
> A tudástároló jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verzió funkció szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) A [REST API 2019-05-06-preview verziója](search-api-preview.md) előnézeti funkciókat biztosít. Jelenleg korlátozott a portál támogatása, és nincs .NET SDK-támogatás.

Ebből a cikkből megtudhatja, hogy miként csatlakozhat tudástárolóhoz a Power Query használatával a Power BI Desktop alkalmazásban. A sablonok segítségével gyorsabban kezdheti el a munkát, vagy teljesen új irányítópultot hozhat létre.

+ Kövesse a [Tudástároló létrehozása az Azure Portalon,](knowledge-store-create-portal.md) vagy [hozzon létre egy Azure Cognitive Search tudástároló a REST használatával](knowledge-store-create-rest.md) a forgatókönyvben használt minta tudástároló létrehozásához kövesse. Szüksége lesz a tudástároló létrehozásához használt Azure Storage-fiók nevére, valamint az Azure Portalon található hozzáférési kulcsra is.

+ [A Power BI Desktop telepítése](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>Minta Power BI-sablon – csak az Azure Portalon

Ha tudástárolót hoz létre [az Azure Portalhasználatával,](knowledge-store-create-portal.md)lehetősége van power [BI-sablon](https://github.com/Azure-Samples/cognitive-search-templates) letöltésére az **Adatok importálása** varázsló második lapján. Ez a sablon számos vizualizációt biztosít, például a WordCloudot és a Hálózati navigátort a szövegalapú tartalmakhoz. 

Kattintson a **Power BI-sablon beolvasása** elemre a **Kognitív képességek hozzáadása** lapon a sablon nyilvános GitHub-helyéről való lekéréséhez és letöltéséhez. A varázsló módosítja a sablont, hogy az megfeleljen az adatok alakjának, ahogy azt a varázslóban megadott tudástár-vetületek rögzítik. Ezért a letöltött sablon a varázsló minden egyes futtatásakor eltérő lesz, feltételezve, hogy különböző adatbevitelek és szakértelem-beállítások vannak.

![Minta Az Azure Cognitive Search Power BI-sablon](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Minta Power BI-sablon")

> [!NOTE]
> Bár a sablon letöltése közben a varázsló a repülés közben, meg kell várnia, amíg a tudástároló ténylegesen létre az Azure Table storage-ban, mielőtt használhatja.

## <a name="connect-with-power-bi"></a>Kapcsolódás Power BI-jal

1. Indítsa el a Power BI Desktopot, és kattintson **az Adatok bekapcsolása**gombra.

1. Az **Adatok bekerülése** ablakban válassza az **Azure**lehetőséget, majd az **Azure Table Storage lehetőséget.**

1. Kattintson a **Csatlakozás** gombra.

1. A **fiók neve vagy URL-cím,** írja be az Azure Storage-fiók nevét (a teljes URL-t hozlétre az Ön számára).

1. Ha a rendszer kéri, adja meg a tárfiók kulcsát.

1. Válassza ki az előző forgatókönyvek által létrehozott szállodai értékelésekadatait tartalmazó táblákat. 

   + A portál forgatókönyv, tábla nevek *hotelReviewsSsDocument*, *hotelReviewsSsEntities*, *hotelReviewsSsKeyPhrases*, és *hotelReviewsSsPages*. 
   
   + A REST-forgatókönyvben a táblanevek *a következők: hotelReviewsDocument*, *hotelReviewsPages*, *hotelReviewsKeyPhrases*és *hotelReviewsSentiment*.

1. Kattintson a **Betöltés gombra.**

1. A felső menüszalagon kattintson a **Lekérdezések szerkesztése** gombra a **Power Query Szerkesztő**megnyitásához.

   ![Power Query megnyitása](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Power Query megnyitása")

1. Válassza a *hotelReviewsSsDocument*elemet, majd távolítsa el a *PartitionKey*, *RowKey*és *Timestamp* oszlopokat. 
   ![Táblázatok szerkesztése](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Táblázatok szerkesztése")

1. A Tartalom kibontásához kattintson a táblázat jobb felső részén lévő ellentétes nyilakkal rendelkező *ikonra.* Amikor megjelenik az oszlopok listája, jelölje ki az összes oszlopot, majd törölje a "metaadatok" kezdetű oszlopok kijelölését. A kijelölt oszlopok megjelenítéséhez kattintson az **OK** gombra.

   ![Táblázatok szerkesztése](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Tartalom kibontása")

1. Módosítsa a következő oszlopok adattípusát az oszlop bal felső részén található ABC-123 ikonra kattintva.

   + A *content.latitude* és *a Content.longitude (Tartalom.hosszúság)* esetében válassza **a Decimális szám lehetőséget.**
   + A *Content.reviews_date* és *a Content.reviews_dateAdded*területen válassza a **Dátum/idő**lehetőséget.

   ![Adattípusok módosítása](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Adattípusok módosítása")

1. Válassza a *hotelReviewsSsPages*lehetőséget, majd ismételje meg a *9.*
1. Módosítsa a *Content.SentimentScore* adattípusát **decimális számra.**
1. Válassza ki *a hotelReviewsSsKeyPhrases parancsot,* és ismételje meg a *9.* Ehhez a táblához nincsadattípus-módosítás.

1. A parancssávon kattintson a **Bezárás és az Alkalmaz gombra.**

1. Kattintson a bal oldali navigációs ablak Modell csempéjére, és ellenőrizze, hogy a Power BI mindhárom tábla közötti kapcsolatokat jeleníti-e meg.

   ![Kapcsolatok ellenőrzése](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Kapcsolatok ellenőrzése")

1. Kattintson duplán az egyes kapcsolatokra, és győződjön meg arról, hogy a **keresztszűrő iránya** **Mindkettő.**  Ez lehetővé teszi, hogy a vizualizációk frissüljenek a szűrő alkalmazásakor.

1. Kattintson a bal oldali navigációs ablak Jelentés csempéjére, ha vizualizációkon keresztül szeretné feltárni az adatokat. Szövegmezők esetén a táblázatok és a kártyák hasznos vizualizációk. A táblázat vagy a kártya kitöltéséhez mindhárom táblázatból választhat mezőket. 

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

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

Az erőforrásokat a portálon keresheti meg és kezelheti a bal oldali navigációs ablak **Minden erőforrás** vagy **Erőforráscsoport** hivatkozásával.

Ha ingyenes szolgáltatást használ, ne feledje, hogy három indexelésre, indexelőre és adatforrásra van korlátozva. Törölheti az egyes elemeket a portálon, hogy a korlát alatt maradjon.

## <a name="next-steps"></a>További lépések

Ha meg szeretné tudni, hogyan fedezheti fel ezt a tudástárolót a Storage Explorer használatával, olvassa el az alábbi cikket.

> [!div class="nextstepaction"]
> [Megtekintés a Storage Explorerrel](knowledge-store-view-storage-explorer.md)