---
title: Kapcsolódás a Power BI-Azure Searchsal rendelkező Knowledge Store-hoz
description: Azure Search Tudásbázis összekötése az elemzés és a feltárás Power BIával.
author: lisaleib
services: search
ms.service: search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: de282213535a2e49f73bc30e476bae02d470fdb2
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265650"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Tudástár összekötése Power BI

> [!Note]
> A Knowledge Store előzetes verzióban érhető el, és nem használható éles környezetben. A [Azure Search REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs .NET SDK-támogatás.
>
Ebből a cikkből megtudhatja, hogyan csatlakozhat és vizsgálhat meg egy tudásbázist a Power BI Desktop alkalmazás Power Query használatával. Az útmutatóban használt Knowledge Store-minta létrehozásával kapcsolatban tekintse meg a [Knowledge Store létrehozása a Azure Portalban](knowledge-store-create-portal.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

+ A jelen útmutatóban használt mintaűrlapok létrehozásához kövesse az [Azure Portal Tudásbázis létrehozása](knowledge-store-create-portal.md) című témakör lépéseit. Szüksége lesz a Knowledge Store létrehozásához használt Azure Storage-fiók nevére, valamint a Azure Portal elérési kulcsára.

+ [Power BI Desktop telepítése](https://powerbi.microsoft.com/downloads/)

## <a name="connect-with-power-bi"></a>Kapcsolódás Power BI-jal

1. Indítsa el Power BI Desktop és kattintson **az adatlekérdezés**gombra.

1. Az **adatlekérdezés** ablakban válassza az **Azure**lehetőséget, majd válassza az **Azure Table Storage**elemet.

1. Kattintson a **Csatlakozás** gombra.

1. A **fiók neve vagy URL-címe**mezőben adja meg az Azure Storage-fiók nevét (a teljes URL-cím lesz létrehozva).

1. Ha a rendszer kéri, adja meg a Storage-fiók kulcsát.

1. Válassza ki a *hotelReviewsSsDocument*, a *HotelReviewsSsKeyPhrases*és a *hotelReviewsSsPages* táblákat. Ezek a táblák az Azure Table-kivetítések, amelyeket a Hotel a Knowledge Store létrehozásakor kiválasztott kognitív szolgáltatás-bővítésekkel kapcsolatos információkat tartalmaz.

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

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) resource, created at the *S0* tier, in the same region as Azure Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha saját előfizetésében dolgozik, a projekt végén érdemes megállapítani, hogy továbbra is szüksége van-e a létrehozott erőforrásokra. A már futó erőforrások pénzbe kerülnek. Az erőforrásokat egyenként is törölheti, vagy az erőforráscsoport törlésével törölheti a teljes erőforrás-készletet.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon.

## <a name="next-steps"></a>További lépések

Az alábbi cikkből megtudhatja, hogyan derítheti fel ezt a Knowledge áruházat a Storage Explorer használatával.

> [!div class="nextstepaction"]
> [Megtekintés Storage Explorer](knowledge-store-view-storage-explorer.md)

A következő cikkből megtudhatja, hogyan hozhat létre egy tudásbázist a REST API-k és a Poster használatával.  

> [!div class="nextstepaction"]
> [Hozzon létre egy Knowledge Store-t a REST-ben](knowledge-store-howto.md)
