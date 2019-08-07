---
title: Kapcsolódás a Knowledge Store-hoz a Power BI-Azure Search
description: Hozzon létre egy Knowledge Store-t a Azure Portal adatimportálás varázslójával, majd az elemzéshez és a feltáráshoz kapcsolódjon Power BI.
author: heidisteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.devlang: NA
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: heidist
ms.openlocfilehash: 518a96ae8ace5c9630d594fe70487635b6ec1d2c
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840845"
---
# <a name="create-an-azure-search-knowledge-store-and-connect-using-power-bi"></a>Hozzon létre egy Azure Search Knowledge Store-t, és kapcsolódjon a Power BI

> [!Note]
> A Knowledge Store előzetes verzióban érhető el, és nem használható éles környezetben. A [Azure Search REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs .NET SDK-támogatás.
>

A Knowledge Store a Azure Search egyik funkciója, amely egy AI-bővítési folyamat kimenetét az azt követő elemzésekhez vagy más alsóbb rétegbeli feldolgozáshoz is megőrzi. Egy mesterséges intelligenciával rendelkező folyamat képfájlokat vagy strukturálatlan szövegfájlokat fogad egy támogatott adatforrásból, elküldi Azure Search indexelésre, alkalmazza a mesterséges intelligenciát Cognitive Services (például a képek elemzése és a természetes nyelvi feldolgozás), majd menti az eredményeket egy Azure Storage-beli tudásbázisba. A Knowledge Store-ból olyan eszközöket csatolhat, mint a Power BI vagy a Storage Explorer az eredmények megismeréséhez.

Ebben a cikkben hozzon létre egy tudásbázist a portálon, majd kapcsolódjon és ismerkedjen meg a Power BI Desktop Power Query használatával. 

Ez az útmutató egy olyan adathalmazt használ, amely az ügyfelek által végzett felülvizsgálatok és minősítések, a Cognitive Servicesból származó érzelmek pontozása, majd Power Query a dokumentumok lekérdezéséhez. Az adatok a Hotel Kaggle.com származó adatokból származnak. 

## <a name="prerequisites"></a>Előfeltételek

+ Töltse le a következőt: [Hotel Reviews CSV-fájl (HotelReviews_Free. csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Ez az adathalmaz a szállodákkal kapcsolatos vásárlói visszajelzések rekordjait tartalmazza.

+ [Power BI Desktop](https://powerbi.microsoft.com/downloads/)

+ [Azure Search](search-create-service-portal.md). Ehhez az útmutatóhoz használhat egy ingyenes szolgáltatást. 

+ [Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Győződjön meg arról, hogy a fiók "általános célú", vagy *StorageV2 (általános célú v2)* , amely az alapértelmezett vagy a *Storage (általános célú v1)* . Válassza ki ugyanazt a régiót, mint Azure Search.
 
## <a name="prepare-data"></a>Adatok előkészítése 

Töltse be a. csv-fájlt az Azure Blob Storage-ba, hogy az egy Azure Search indexelő segítségével elérhető legyen.

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com), navigáljon az Azure Storage-fiókjához, kattintson a **Blobok**elemre, majd a **+ tároló**elemre.

1. [Hozzon létre egy blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) -tárolót a mintaadatok tárolására: 

   1. Nevezze el a `hotel-reviews`tárolót. 
   
   1. Állítsa a nyilvános hozzáférési szintet bármelyik érvényes értékére. Az alapértelmezett értéket használtuk.

1. A tároló létrehozása után nyissa meg, majd válassza a parancssáv **feltöltés** elemét.

1. Navigáljon a **HotelReviews-Free. csv**fájlt tartalmazó mappához, válassza ki a fájlt, majd kattintson a **feltöltés**elemre.

   ![Töltse fel a. csv fájlt.](media/knowledge-store-howto-powerbi/hotel-reviews-blob-container.png "Töltse fel a. csv fájlt.")

1. Az Azure Storage-ban a kapcsolódási karakterlánc és a tároló neve olvasható.  Egy adatforrás-objektum létrehozásakor mindkét sztringre szüksége lesz:

   1. Az Áttekintés lapon kattintson a **hozzáférési kulcsok** elemre, és másolja a *kapcsolati karakterláncot*. Ezzel elindul `DefaultEndpointsProtocol=https;` , és a- `EndpointSuffix=core.windows.net`vel zárul. A fiók neve és kulcsa a (z) között van. 

   1. A tároló nevének vagy bármely `hotel-reviews` hozzárendelt névnek kell lennie. 

## <a name="create-and-run-ai-enrichments"></a>AI-bővítések létrehozása és futtatása

Az adatimportálás varázsló segítségével hozza létre a tudásbázist. Importálja az adatkészletet, válassza a gazdagodás, a Knowledge Store és az index konfigurálása lehetőséget, majd hajtsa végre a végrehajtást.

1. [Keresse meg a keresési szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a Azure Portalon.

1. Kattintson az adatimportálás elemre a parancssorban.

1. Hozza létre az adatforrás-objektumot a varázsló első oldalán.

   - Válassza az **Azure Blob Storage**lehetőséget.

   - Adja meg az adatforrás nevét, például: *Hotel-Reviews-DS*.

   - Mivel az adatmennyiség. csv, válassza a **tagolt szöveg** lehetőséget az elemzési mód kiválasztásához, jelölje be az **első sor fejlécet**, és győződjön meg arról, hogy az elválasztó karakter vessző.

   - Az Azure Storage-fiókhoz tartozó kapcsolati karakterlánc a **hozzáférési kulcsok**területen szerezhető be a portálon.

   - A tároló neve az Azure Storage-Blobok listájában is beszerezhető a portálról.

      ![Adatforrás-objektum létrehozása](media/knowledge-store-howto-powerbi/hotel-reviews-ds.png "Adatforrás-objektum létrehozása")

1. Bővítheti a bővítéseket, és konfigurálhat egy tudásbázist a varázsló második oldalán.

   - **Cognitive Services csatolása**esetén használhatja az ingyenes erőforrást, amely naponta akár 20 tranzakciót is lehetővé tesz. A HotelReviews-Free. csv fájlban lévő rekordok száma kevesebb, mint 20.

   - A **bővítések hozzáadása**lapon nevezze el a készségkészlet *Hotel-Reviews-SS*nevet, válassza ki a *reviews_text* mezőt, válassza ki az oldalak részletességi szintjét *(5000 karakteres*adattömbök), majd válassza ki ezt a három kognitív képességet: Kinyerheti a *kulcsfontosságú kifejezéseket*, *felismeri a nyelvet*, és érzékelheti a *véleményét*.

      ![Készségkészlet létrehozása](media/knowledge-store-howto-powerbi/hotel-reviews-ss.png "Készségkészlet létrehozása")

   - A **gazdagodás mentése a Knowledge Store**-ba területen adja meg az általános célú Azure Storage-fiókhoz tartozó kapcsolódási karakterláncot. A rendszer az Azure Table Storage-ban hozza létre a tudásbázist, ha az ebben a szakaszban szereplő *Azure Table projects* lehetőségeket választja.

      A ![Knowledge Store konfigurálása] A (media/knowledge-store-howto-powerbi/hotel-reviews-ks.png "Knowledge Store konfigurálása")
   
   Kattintson **a Tovább gombra: A célként megadott** index testreszabásával folytassa a következő lépéssel.

1. Konfiguráljon egy indexet az opcionális teljes szöveges keresési lekérdezésekhez Azure Searchban. Bár a bemutató fókusza Power BI csatlakozik az Azure Table Storage-hoz, az **adatimportálás** varázsló is létrehozhat egy indexet a teljes szöveges kereséshez a Azure Searchban. 

   A varázsló megtervezi az adatforrást a mezők és adattípusok kikötéséhez, ezért mindössze annyit kell tennie, hogy kiválasztja a szükséges attribútumokat a kívánt viselkedés eléréséhez. Például a lekérdezhető érték azt jelenti, hogy a tartalom a szolgáltatásból kérhető le, a *kereshetőség* pedig lehetővé teszi a teljes szöveges keresést a kijelölt mezőkön.

   - Adjon egy nevet az indexnek, például: *Hotel-Reviews-idx*.
   - Az összes mezőt beolvasható értékre állítsa be.
   - Adja meg a *város*, a *név*, a *reviews_text*, a *nyelv*és a *kifejezéseket* **kereshetőként**.
   - Állítsa be a *hangulat*, a *nyelv*és a *kifejezéseket* szűrhetőként és **sokrétűként**. 
   
    Az indexnek az alábbi képhez hasonlóan kell kinéznie.

     ![Index konfigurálása](media/knowledge-store-howto-powerbi/hotel-reviews-idx.png "Index konfigurálása")

   Kattintson **a Tovább gombra: Hozzon létre egy** indexelő, hogy folytassa a következő lépéssel.

1. Az indexelő konfigurálása a név és a végrehajtás lépésszám megadásával. Ebben a bemutatóban használja a *Hotel-Reviews-idxr* az indexelő neveként, és az alapértelmezett **egyszeres** ütemterv használatával futtassa az indexelő azonnal.

   Az indexelő végrehajtása az összes korábbi konfigurációt áthelyezi a mozgásba. Ebben a lépésben a kinyerési, feldolgozási és betöltési művelet zajlik.

1. Figyelje az indexelést a portál értesítések sorában. A végrehajtás több percet is igénybe vehet.

## <a name="connect-with-power-bi"></a>Kapcsolódás Power BI-jal

1. Indítsa el Power BI Desktop és válassza **az adatlekérdezés**lehetőséget.

1. Az adatlekérdezés területen válassza az **Azure** , majd az **Azure Table Storage**lehetőséget. Kattintson a **Csatlakozás** gombra.

1. A fiók neve vagy URL-címe mezőbe illessze be az Azure Storage-fiók nevét (a teljes URL-cím megoldódik).

1. Adja meg a fiók kulcsát.

1. Válassza ki a dokumentumot, a kifejezéseket és a lapokat. Ezek az adatok importálása varázsló által létrehozott táblázatok, ha ugyanazokat a megnevezett elemeket választja ki a Knowledge Store-konfigurációban. Kattintsona betöltés elemre.

1. A **lekérdezések szerkesztése** parancsra kattintva nyissa meg Power Query.

   ![Power Query megnyitása](media/knowledge-store-howto-powerbi/powerbi-edit-queries.png "Power Query megnyitása")

1. Dokumentumok esetén:

   - Távolítsa el az Azure Table Storage által létrehozott PartitionKey, RowKey és időbélyeg-oszlopokat. A Knowledge Store az elemzés során használt kapcsolatokat biztosít.

   - Bontsa ki a tartalom oszlopot.

     ![Táblák szerkesztése](media/knowledge-store-howto-powerbi/powerbi-edit-table.png "Táblák szerkesztése")

1. Jelölje ki az összes mezőt, majd törölje a jelölést a "metaadatok" kifejezéstől kezdődően.

1. Javítsa ki a következő oszlopok adattípusát az ABC-123 ikon használatával az egyes oszlopokon:

   - A dátum oszlopoknak **datetime** típusúnak kell lenniük
   - A szélességnek **decimális számnak** kell lennie
   - A hosszúságnak **decimális számnak** kell lennie

1. Ismételje meg az előző lépéseket a főkifejezések esetében, távolítsa el a PartitionKey és más oszlopokat, és bontsa ki a tartalom oszlopot, és állítsa a *SentimentScore* **decimális számra**

1. Ismételje meg a lapokat, távolítsa el a PartitionKey és más oszlopokat, és bontsa ki a tartalom oszlopot. Ehhez a táblához nincs Adattípus-módosítás.

1. Kattintson a **Bezárás gombra, és alkalmazza** a Power Query parancssáv bal oldalán.

1. Ellenőrizze, hogy a Power BI felismeri-e a Knowledge Store által az adatain belül létrehozott kapcsolatokat. Kattintson a bal oldali navigációs ablaktábla kapcsolatok csempére. Mindhárom táblának kapcsolatban kell állnia. Szerkessze a kapcsolatokat, és győződjön meg arról, hogy a "kereszt-szűrés iránya" mindkét értékre van beállítva, ezzel biztosíthatja, hogy a rendszer minden vizualizációt frissítsen a szűrő alkalmazásakor.

   ![Kapcsolatok ellenőrzése](media/knowledge-store-howto-powerbi/powerbi-relationships.png "Kapcsolatok ellenőrzése")

## <a name="try-with-larger-data-sets"></a>Nagyobb adatkészletek kipróbálása

Szándékosan tartottuk az adathalmazt, hogy elkerülje a bemutató bemutatójának díját. A reális élmény érdekében számlázható Cognitive Services-erőforrást hozhat létre, majd csatolhat hozzá, hogy nagyobb számú tranzakciót engedélyezzen az keyphrase, a kinyerést és a nyelvi detektort is.

Hozzon létre új tárolókat az Azure Blob Storage-ban, és töltse fel az egyes CSV-fájlokat a saját tárolóba. Az adatimportálás varázsló adatforrás-létrehozási lépésében megjelenő tárolók egyikét kell megadnia.

| Leírás | Összekapcsolás |
|-------------|------|
| Ingyenes csomag   | [HotelReviews_Free. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Kicsi (500 rekord) | [HotelReviews_Small. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Közepes (6000 rekord)| [HotelReviews_Medium. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Nagyméretű (teljes adatkészlet 35000 rekord) | [HotelReviews_Large. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Ügyeljen arra, hogy a nagy mennyiségű adathalmazok feldolgozása költséges. Ez körülbelül $1000 U. S dollárért jár.|

A varázsló dúsítás lépésében csatoljon egy számlázandó [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) -erőforrást, amely a *S0* szinten jön létre, ugyanabban a régióban, mint Azure Search nagyobb adatkészletek használatához. 

  ![Cognitive Services erőforrás létrehozása](media/knowledge-store-howto-powerbi/create-cognitive-service.png "Cognitive Services erőforrás létrehozása")

## <a name="next-steps"></a>További lépések

Ha szeretné megismételni ezt a gyakorlatot, vagy egy másik AI-bővítési útmutatót, törölje az imént létrehozott *Hotel-Reviews-idx-* indexet. Az indexelő törlése visszaállítja az ingyenes napi tranzakció számlálóját nullára. 

A tudásbázist bemutató további lépésenkénti útmutatókért folytassa a következő cikkel, amely bemutatja, hogyan hozhat létre egy tudásbázist a REST API-kkal és a Poster használatával.

> [!div class="nextstepaction"]
> [Ismerkedés a Knowledge Store-ban](knowledge-store-howto.md)
