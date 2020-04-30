---
title: Hozzon létre egy Knowledge Store-t (előzetes verzió) a Azure Portal
titleSuffix: Azure Cognitive Search
description: Az adatimportálás varázsló használatával hozzon létre egy, a dúsított tartalom megőrzésére szolgáló tudásbázist. Kapcsolódjon a Knowledge Store-hoz más alkalmazásokból származó elemzéshez, vagy a dúsított tartalmat küldje el az alsóbb rétegbeli folyamatoknak. Ez a szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/29/2020
ms.openlocfilehash: 21279b2b4735a25210e8373d76d0d63f9c711bfc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77472366"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Gyors útmutató: Azure Cognitive Search Knowledge Store létrehozása a Azure Portal

> [!IMPORTANT] 
> A Knowledge Store jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

A Knowledge Store az Azure Cognitive Search egyik funkciója, amely egy kognitív képességekből származó kimenetet biztosít a későbbi elemzésekhez vagy az alsóbb rétegbeli feldolgozáshoz. 

A folyamatok a strukturálatlan szövegeket és képeket nyers tartalomként fogadják el, a mesterséges intelligenciát Cognitive Services (például OCR, képelemzés, természetes nyelvi feldolgozás), kinyeri az adatokat, és új struktúrákat és információkat eredményeznek. A folyamat által létrehozott fizikai összetevők egyike egy [Tudásbázis](knowledge-store-concept-intro.md), amelyet az eszközökön keresztül érhet el, és elemezheti a tartalmakat.

Ebben a rövid útmutatóban a szolgáltatásait és adatait az Azure-felhőben egyesítve hozhat létre egy tudásbázist. Ha minden megtörtént, a portálon futtathatja az **adatimportálás** varázslót, hogy az összeset együtt kell lekérnie. A végeredmény az eredeti szöveges tartalom, valamint a mesterséges intelligenciával létrehozott tartalom, amelyet a portálon ([Storage Explorer](knowledge-store-view-storage-explorer.md)) tekinthet meg.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="create-services-and-load-data"></a>Szolgáltatások létrehozása és az adatterhelés

Ez a rövid útmutató az Azure Cognitive Search, az Azure Blob Storage és az [azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) használatát használja az AI-hoz. 

Mivel a számítási feladatok olyan kicsik, Cognitive Services a háttérben, hogy naponta legfeljebb 20 tranzakció szabad feldolgozását biztosítsa. Mivel az adatkészlet olyan kicsi, hogy kihagy egy Cognitive Services erőforrás létrehozását vagy csatolását.

1. [Töltse le HotelReviews_Free. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D)fájlt. Ezek az adatok egy CSV-fájlban (Kaggle.com-ből származó) tárolt adatok, amelyek egy adott szállodával kapcsolatban 19 darab ügyfél-visszajelzést tartalmaznak. 

1. [Hozzon létre egy Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) , vagy [keressen egy meglévő fiókot](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) a jelenlegi előfizetése alatt. Az Azure Storage-t fogja használni mind az importálandó nyers tartalomhoz, mind a Tudásbázis végeredményéhez.

   Válassza ki a **StorageV2 (általános célú v2)** fiók típusát.

1. Nyissa meg a blob Services lapjait, és hozzon létre egy " *Hotel-Reviews*" nevű tárolót.

1. Kattintson a **Feltöltés** gombra.

    ![Adatok feltöltése](media/knowledge-store-create-portal/upload-command-bar.png "A szállodai értékelések feltöltése")

1. Válassza ki az első lépésben letöltött **HotelReviews-Free. csv** fájlt.

    ![Az Azure Blob-tároló létrehozása](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Az Azure Blob-tároló létrehozása")

1. Ezzel az erőforrással majdnem elkészült, de mielőtt elhagyja ezeket a lapokat, a bal oldali navigációs ablaktáblán található hivatkozást használva nyissa meg a **hozzáférési kulcsok** lapot. A blob Storage-ból származó adatok lekérésére szolgáló kapcsolódási karakterlánc beolvasása. A kapcsolódási karakterlánc az alábbi példához hasonlóan néz ki:`DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Továbbra is a portálon váltson az Azure Cognitive Searchra. [Hozzon létre egy új szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat.

Most már készen áll az adatimportálás varázsló áthelyezésére.

## <a name="run-the-import-data-wizard"></a>Az adatimportálás varázsló futtatása

A keresési szolgáltatás áttekintése lapon kattintson az **adat importálása** parancsra a parancssorban, és hozzon létre egy tudásbázist négy lépésben.

  ![Adatok importálása parancs](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>1. lépés: Adatforrás létrehozása

1. A **Kapcsolódás az adataihoz**lapon válassza az **Azure Blob Storage**lehetőséget, majd válassza ki a létrehozott fiókot és tárolót. 
1. A **név**mezőbe írja be `hotel-reviews-ds`a következőt:.
1. **Elemzési mód**esetén válassza a **tagolt szöveg**lehetőséget, majd jelölje be az **első sor fejléce** jelölőnégyzetet. Győződjön meg arról, hogy az elválasztó **karakter** egy vessző (,).
1. A **kapcsolati karakterláncban**illessze be az Azure Storage **hozzáférési kulcsok** lapjáról másolt kapcsolati karakterláncot.
1. A **tárolók**mezőben adja meg az adattárolási blob-tároló nevét.

    Az oldalnak az alábbi képernyőképhez hasonlóan kell kinéznie.

    ![Adatforrás-objektum létrehozása](media/knowledge-store-create-portal/hotel-reviews-ds.png "Adatforrás-objektum létrehozása")

1. Folytassa a következő oldallal.

### <a name="step-2-add-cognitive-skills"></a>2. lépés: Kognitív képességek hozzáadása

A varázsló lépésében egy készségkészlet hoz létre a kognitív képességek gazdagítása révén. A forrásadatok több nyelven is különböző felhasználói felülvizsgálatokból állnak. Az ehhez az adatkészlethez kapcsolódó készségek közé tartozik a fő kifejezés kinyerése, a hangulat észlelése és a szöveg fordítása. Egy későbbi lépésben ezeket a bővítéseket az Azure Tables (Tudásbázisban) a Knowledge Store-ba fogjuk felépíteni.

1. Bontsa ki a **csatolás Cognitive Services**elemet. Alapértelmezés szerint az **ingyenes (korlátozott alkoholtartalom-növelés)** beállítás van kiválasztva. Ezt az erőforrást használhatja, mert a HotelReviews-Free. csv fájlban lévő rekordok száma 19, és ez az ingyenes erőforrás naponta legfeljebb 20 tranzakciót tesz lehetővé.
1. Bontsa ki a bővítések **hozzáadása**elemet.
1. A **készségkészlet neve**mezőbe írja `hotel-reviews-ss`be a következőt:.
1. A **forrásadatok mezőben**válassza a **reviews_text**lehetőséget.
1. A **dúsítás részletességi szintjének**kiválasztásához válassza a **lapok (5000 karakteres tömbök) elemet.**
1. Válassza ki ezeket a kognitív képességeket:
    + **Kulcsszavak kinyerése**
    + **Szöveg lefordítása**
    + **Érzelmek észlelése**

      ![Képességcsoport létrehozása](media/knowledge-store-create-portal/hotel-reviews-ss.png "Képességcsoport létrehozása")

1. Bontsa ki a bővítések **mentése a Knowledge Store-** ba lehetőséget.
1. Válassza ki ezeket az **Azure Table-kivetítéseket**:
    + **Dokumentumok**
    + **Oldalak**
    + **Legfontosabb kifejezések**
1. Adja meg az előző lépésben mentett **Storage-fiókhoz tartozó kapcsolatok sztringjét** .

    ![A Knowledge Store konfigurálása](media/knowledge-store-create-portal/hotel-reviews-ks.png "A Knowledge Store konfigurálása")

1. Szükség esetén letöltheti Power BI sablont. Amikor a varázslóból fér hozzá a sablonhoz, a helyi. pbit fájl alkalmazkodik az adatok alakjának megjelenítéséhez.

1. Folytassa a következő oldallal.

### <a name="step-3-configure-the-index"></a>3. lépés: Az index konfigurálása

Ebben a varázslóban a választható teljes szöveges keresési lekérdezések indexét kell konfigurálnia. A varázsló felveszi az adatforrást a mezők és adattípusok lekötésére. Csak ki kell választania a kívánt viselkedés attribútumait. A lekérdezhető **attribútum például** lehetővé teszi, hogy a Search szolgáltatás a mező értékét visszaállítsa, miközben a **kereshetőség** lehetővé teszi a teljes szöveges keresést a mezőben.

1. Az **index neve**mezőbe írja `hotel-reviews-idx`be a következőt:.
1. Attribútumok esetében fogadja el az alapértelmezett beállításokat **Retrievable** : lekérdezhető és **kereshető** a folyamat által létrehozott új mezőkhöz.

    Az indexnek az alábbi képhez hasonlóan kell kinéznie. Mivel a lista hosszú, nem minden mező látható a képen.

    ![Index konfigurálása](media/knowledge-store-create-portal/hotel-reviews-idx.png "Index konfigurálása")

1. Folytassa a következő oldallal.

### <a name="step-4-configure-the-indexer"></a>4. lépés: Az indexelő konfigurálása

A varázsló lépésében olyan indexelő fog konfigurálni, amely összefogja az adatforrást, a készségkészlet és a varázsló előző lépéseiben megadott indexet.

1. A **név**mezőbe írja `hotel-reviews-idxr`be a következőt:.
1. Az **ütemterv** **beállításnál**tartsa meg az alapértelmezett értéket.
1. Az indexelő futtatásához kattintson a **Submit (elküldés** ) gombra. Az adatgyűjtés, az indexelés, a kognitív képességek alkalmazása mindez ebben a lépésben történik.

## <a name="monitor-status"></a>Figyelő állapota

A kognitív képességek indexelése hosszabb időt vesz igénybe, mint a szokásos szöveges indexelés. A varázslónak meg kell nyitnia az indexelő listát az Áttekintés oldalon, hogy nyomon követhesse a folyamat előrehaladását. Az önnavigációhoz lépjen az Áttekintés lapra, és kattintson az **Indexelő**elemre.

A Azure Portal az értesítési tevékenység naplóját is figyelheti egy kattintható **Azure Cognitive Search értesítési** állapot hivatkozására. A végrehajtás több percet is igénybe vehet.

## <a name="next-steps"></a>További lépések

Most, hogy bővítette az adatait a Cognitive Services segítségével, és kivetítette az eredményeket egy tudásbázisba, Storage Explorer vagy Power BI használatával tárja fel a dúsított adatkészletet.

A tartalmat megtekintheti Storage Explorerban, vagy a Power BI további lépéseit követve betekintést nyerhet a vizualizációk segítségével.

> [!div class="nextstepaction"]
> [Megtekintés Storage Explorer](knowledge-store-view-storage-explorer.md)
> [kapcsolattal Power bi](knowledge-store-connect-power-bi.md)

> [!Tip]
> Ha szeretné megismételni ezt a gyakorlatot, vagy próbáljon meg egy másik AI-bővítési bemutatót használni, törölje a *Hotel-Reviews-idxr* indexelő. Az indexelő törlése visszaállítja az ingyenes napi tranzakció számlálóját a Cognitive Services feldolgozáshoz.
