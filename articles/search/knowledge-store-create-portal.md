---
title: Tudástároló (előzetes verzió) létrehozása az Azure Portalon
titleSuffix: Azure Cognitive Search
description: Az Adatok importálása varázslóval hozzon létre egy tudástárolót, amely a bővített tartalom megőrzéséhez használható. Csatlakozzon egy tudástárolóhoz elemzésre más alkalmazásokból, vagy küldjön bővített tartalmat az alsóbb rétegbeli folyamatokba. Ez a funkció jelenleg nyilvános előzetes verzióban érhető el.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/29/2020
ms.openlocfilehash: 21279b2b4735a25210e8373d76d0d63f9c711bfc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77472366"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Rövid útmutató: Hozzon létre egy Azure Cognitive Search tudástárolót az Azure Portalon

> [!IMPORTANT] 
> A tudástároló jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verzió funkció szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 

A tudástároló az Azure Cognitive Search olyan szolgáltatása, amely a kognitív képességek folyamatából származó kimenetet további elemzésekhez vagy későbbi feldolgozáshoz továbbra is megőrzi. 

A folyamat a strukturálatlan szöveget és a képeket nyers tartalomként fogadja el, a Cognitive Services-en keresztül alkalmazza a a ai-t (például az OCR-t, a képelemzést és a természetes nyelvi feldolgozást), kinyeri az információkat, és új struktúrákat és információkat ad ki. A folyamat által létrehozott fizikai összetevők egyike egy [tudástároló,](knowledge-store-concept-intro.md)amelyet a tartalom elemzéséhez és feltárásához szükséges eszközöksegítségével érhet el.

Ebben a rövid útmutatóban az Azure-felhőben lévő szolgáltatásokat és adatokat kombinálva hozzon létre egy tudástárolót. Ha minden a helyén van, futtatja az **Adatok importálása** varázslót a portálon, hogy összehúzza az egészet. A végeredmény az eredeti szöveges tartalom, valamint a a i-k által létrehozott tartalom, amelyet a portálon ([Storage explorer](knowledge-store-view-storage-explorer.md)) tekinthet meg.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="create-services-and-load-data"></a>Szolgáltatások létrehozása és adatok betöltése

Ez a rövid útmutató az Azure Cognitive Search, az Azure Blob storage és az [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) a ai. 

Mivel a munkaterhelés olyan kicsi, a Cognitive Services a színfalak mögött, hogy ingyenes feldolgozást biztosít legfeljebb 20 tranzakciók naponta. Mivel az adatkészlet olyan kicsi, kihagyhatja a Cognitive Services-erőforrás létrehozását vagy csatolását.

1. [Letöltés HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Ezek az adatok a CSV fájlban mentett hotel-felülvizsgálati adatok (Kaggle.com származnak), és 19 darab vásárlói visszajelzést tartalmaznak egy szállodáról. 

1. [Hozzon létre egy Azure-tárfiókot,](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) vagy [keressen egy meglévő fiókot](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) az aktuális előfizetésében. Az Azure storage-t mind az importálandó nyers tartalomhoz, mind a tudástárolóhoz használni fogja, amely a végeredmény.

   Válassza ki a **StorageV2 (általános célú V2)** fiók típusát.

1. Nyissa meg a Blob-szolgáltatások lapjait, és hozzon létre egy *hotel-vélemények nevű tárolót.*

1. Kattintson a **Feltöltés** gombra.

    ![Az adatok feltöltése](media/knowledge-store-create-portal/upload-command-bar.png "Töltse fel a szállodai értékeléseket")

1. Válassza ki az első lépésben letöltött **HotelReviews-Free.csv** fájlt.

    ![Az Azure Blob-tároló létrehozása](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Az Azure Blob-tároló létrehozása")

1. Ezzel az erőforrással már majdnem végzett, de mielőtt kilépne ezekből a lapokból, a bal oldali navigációs ablakban található hivatkozással nyissa meg a **Hozzáférési kulcsok** lapot. Szerezzen be egy kapcsolati karakterláncot az adatok Blob storage-ból történő lekéréséhez. A kapcsolati karakterlánc a következő példához hasonlóan néz ki:`DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Még mindig a portálon, váltson az Azure Cognitive Search. [Hozzon létre egy új szolgáltatást,](search-create-service-portal.md) vagy [keressen egy meglévő szolgáltatást.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) A rövid útmutatóhoz ingyenes szolgáltatást használhat.

Most már készen áll az Adatok importálása varázslóra való áttérésre.

## <a name="run-the-import-data-wizard"></a>Az Adatok importálása varázsló futtatása

A keresési szolgáltatás áttekintése lapon kattintson az **Adatok importálása** a parancssávon elemre, ha négy lépésben szeretne tudástárolót létrehozni.

  ![Adatok importálása parancs](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>1. lépés: Adatforrás létrehozása

1. A **Csatlakozás az adatokhoz**lehetőséget **válassza az Azure Blob storage**lehetőséget, válassza ki a létrehozott fiókot és tárolót. 
1. A **Név**mezőbe `hotel-reviews-ds`írja be a mezőbe.
1. **Elemzési módban**jelölje be a **Tagolt szöveg**lehetőséget, majd jelölje be az **Első sor fejlécet tartalmaz** jelölőnégyzetet. Győződjön meg arról, hogy a **Határoló karakter** vessző (,)
1. A **Kapcsolati karakterlánc**alkalmazásban illessze be a kapcsolati karakterláncot, amelyet az Azure Storage **Hozzáférési kulcsok** lapjáról másolt.
1. A **tárolókban**adja meg az adatokat tároló blobtároló nevét.

    Az oldalnak az alábbi képernyőképhez hasonlóan kell kinéznie.

    ![Adatforrás-objektum létrehozása](media/knowledge-store-create-portal/hotel-reviews-ds.png "Adatforrás-objektum létrehozása")

1. Folytassa a következő lappal.

### <a name="step-2-add-cognitive-skills"></a>2. lépés: Kognitív képességek hozzáadása

Ebben a varázsló lépésben hozzon létre egy skillset kognitív képesség gazdagítása. A forrásadatok több nyelven végzett vásárlói véleményekből állnak. Az adatkészlet szempontjából releváns szakértelem közé tartozik a kulcskifejezések kinyerése, a hangulatészlelés és a szövegfordítás. Egy későbbi lépésben ezek a dúsítások lesznek "vetítve" egy tudástároló, mint az Azure-táblák.

1. Bontsa ki **a Cognitive Services csatolása csomópontot.** **Az Ingyenes (Korlátozott dúsítások)** alapértelmezés szerint be van jelölve. Ezt az erőforrást azért használhatja, mert a HotelReviews-Free.csv-ben a rekordok száma 19, és ez a szabad erőforrás naponta legfeljebb 20 tranzakciót engedélyez.
1. Bontsa ki **a Hozzáadás identitat .**
1. A **Skillset-név mezőbe**írja be a beírt. `hotel-reviews-ss`
1. A **Forrás adatforrásmezőben**válassza **a reviews_text**lehetőséget.
1. A **Dúsítás részletességi szint esetén**válassza az Oldalak **(5000 karakterből áll) lehetőséget.**
1. Válassza ki ezeket a kognitív képességeket:
    + **Kulcsszavak kinyerése**
    + **Szöveg lefordítása**
    + **Hangulat észlelése**

      ![Képességcsoport létrehozása](media/knowledge-store-create-portal/hotel-reviews-ss.png "Képességcsoport létrehozása")

1. A **Mentés identik a tudástárolóba**elemre.
1. Válassza ki a következő **Azure-tábla-előrejelzéseket:**
    + **Dokumentumok**
    + **Oldalak**
    + **Kulcsfontosságú kifejezések**
1. Adja meg az előző lépésben mentett **tárolási fiók kapcsolati karakterláncát.**

    ![Tudástároló konfigurálása](media/knowledge-store-create-portal/hotel-reviews-ks.png "Tudástároló konfigurálása")

1. Szükség esetén töltsön le egy Power BI-sablont. Amikor a varázslóból éri el a sablont, a helyi .pbit fájl az adatok alakjához igazodik.

1. Folytassa a következő lappal.

### <a name="step-3-configure-the-index"></a>3. lépés: Az index konfigurálása

Ebben a varázslólépésben a választható teljes szöveges keresési lekérdezésekhez indexet kell beállítani. A varázsló mintát vesz az adatforrásból a mezők és adattípusok kikövetkeztetéséhez. Csak ki kell választania a kívánt viselkedés attribútumait. A **Visszakereshető** attribútum például lehetővé teszi, hogy a keresési szolgáltatás mezőértéket ad vissza, míg a **Kereshető** teljes szöveges keresést tesz lehetővé a mezőben.

1. A **Tárgymutató nevéhez**írja be a mezőbe `hotel-reviews-idx`a.
1. Attribútumok esetén fogadja el az alapértelmezett beállításokat: A folyamat által létrehozott új mezők **visszakereshető** és **kereshető.**

    Az indexnek az alábbi képhez hasonlóan kell kinéznie. Mivel a lista hosszú, nem minden mező látható a képen.

    ![Index konfigurálása](media/knowledge-store-create-portal/hotel-reviews-idx.png "Index konfigurálása")

1. Folytassa a következő lappal.

### <a name="step-4-configure-the-indexer"></a>4. lépés: Az indexelő konfigurálása

Ebben a varázslólépésben konfigurálegy indexelőt, amely összeállítja az adatforrást, a skillsetet és az előző varázslólépésekben megadott indexet.

1. A **Név** `hotel-reviews-idxr`mezőbe írja be a mezőbe.
1. Az **Ütemezés**mezőben tartsa meg az alapértelmezett **Egyszer**beállítást.
1. Az indexelő futtatásához kattintson a **Küldés** gombra. Az adatok kinyerése, indexelése, kognitív képességek alkalmazása minden történik ebben a lépésben.

## <a name="monitor-status"></a>Figyelő állapota

Kognitív szakértelem indexelés hosszabb időt vesz igénybe, mint a tipikus szövegalapú indexelés. A varázslónak meg kell nyitnia az Indexelő listát az áttekintő lapon, hogy nyomon követhesse a folyamatot. Az önnavigációhoz lépjen az Áttekintés lapra, és kattintson az **Indexelők gombra.**

Az Azure Portalon is figyelheti az értesítések tevékenységnaplóegy kattintható **Azure Cognitive Search értesítési állapot** linket. A végrehajtás több percig is eltarthat.

## <a name="next-steps"></a>További lépések

Most, hogy bővítette az adatokat a Cognitive Services használatával, és az eredményeket egy tudástárolóba vetítette, a Storage Explorer vagy a Power BI segítségével megismerheti a bővített adatkészletet.

A Storage Explorer ben megtekintheti a tartalmat, vagy egy lépéssel tovább léphet a Power BI segítségével, hogy a vizualizáció révén betekintést nyerjen.

> [!div class="nextstepaction"]
> [Nézet a Tárterület-kezelővel](knowledge-store-view-storage-explorer.md)
> [Csatlakozás a Power BI-val](knowledge-store-connect-power-bi.md)

> [!Tip]
> Ha meg szeretné ismételni ezt a gyakorlatot, vagy ki próbálhatja egy másik AI-bővítési forgatókönyvet, törölje a *hotel-reviews-idxr* indexelőt. Az indexelő törlése visszaállítja az ingyenes napi tranzakciószámláló t nullára a Cognitive Services feldolgozása.
