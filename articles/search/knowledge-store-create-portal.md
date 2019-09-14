---
title: Hozzon létre egy Knowledge Store-t a Azure Portal-Azure Search
description: Hozzon létre egy Azure Search tudásbázist a kognitív keresési folyamattal való megőrzéshez a Azure Portal adatok importálása varázslójával.
author: lisaleib
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: 14996d0ac9ee4e086a5dccd9275ef694adca06ca
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963021"
---
# <a name="create-an-azure-search-knowledge-store-in-the-azure-portal"></a>Hozzon létre egy Azure Search Knowledge Store-t a Azure Portal

> [!Note]
> A Knowledge Store előzetes verzióban érhető el, és nem használható éles környezetben. A [Azure Search REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs .NET SDK-támogatás.
>

A Knowledge Store a Azure Search egyik funkciója, amely egy mesterséges intelligencia-bővítési folyamat kimenetét őrzi meg későbbi elemzések vagy más alsóbb rétegbeli feldolgozás céljából. Egy mesterséges intelligenciával rendelkező folyamat képfájlokat vagy strukturálatlan szövegfájlokat fogad, indexeli őket a Azure Search használatával, alkalmazza a mesterséges intelligenciát Cognitive Services (például a képelemzést és a természetes nyelvi feldolgozást), majd menti az eredményeket egy Azure-beli tudásbázisba Storage. Ezután használhatja a Power BI vagy a Storage Explorer eszközöket a Knowledge Store megismeréséhez.

Ebben a cikkben a Azure Portal az adatimportálás varázsló használatával végezheti el, indexelheti és alkalmazhatja az AI-bővítéseket a szállodai felülvizsgálatok egy készletére. A szállodai felülvizsgálatok az Azure blog Storage-ba lesznek importálva, és az eredményeket a rendszer az Azure Table Storage tudásbázisában tárolja.

Miután létrehozta a tudásbázist, megtudhatja, hogyan érheti el ezt a Knowledge áruházat Storage Explorer vagy Power BI használatával.

## <a name="prerequisites"></a>Előfeltételek

+ [Hozzon létre egy Azure Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez az oktatóanyaghoz használhatja az ingyenes szolgáltatást.

+ [Hozzon létre egy Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) a mintaadatok és a Tudásbázis tárolására. A Storage-fióknak ugyanazt a helyet kell használnia (például az USA-WEas a Azure Search szolgáltatást, és a *Fiók típusa* csak *StorageV2 lehet (általános célú v2)* (alapértelmezett) vagy *Storage (általános célú v1)* .

## <a name="load-the-data"></a>Az adatok betöltése

Töltse be a Hotel a CSV-fájlt az Azure Blob Storage-ba, hogy az egy Azure Search indexelő számára legyen elérhető, és a mesterséges intelligencia-bővítési folyamaton keresztül is elérhető legyen.

### <a name="create-an-azure-blob-container-with-the-data"></a>Azure Blob-tároló létrehozása az adattal

1. [Töltse le a CSV-fájlban (HotelReviews_Free. csv) mentett, a szállodai felülvizsgálati](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)adatfájlt. Ezek az adatok a Kaggle.com származnak, és a felhasználói visszajelzéseket tartalmazzák a szállodákról.
1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com), és navigáljon az Azure Storage-fiókjához.
1. [Blob-tároló létrehozása](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) Ehhez a Storage-fiók bal oldali navigációs sávján kattintson a **Blobok**elemre, majd a parancssáv **+ tároló** elemére.
1. Az új tároló **neve**mezőbe írja be `hotel-reviews`a következőt:.
1. Válassza ki a **nyilvános hozzáférési szintet**. Az alapértelmezett értéket használtuk.
1. Az Azure Blob-tároló létrehozásához kattintson **az OK** gombra.
1. Nyissa meg `hotels-review` az új tárolót, kattintson a **feltöltés**gombra, és válassza ki az első lépésben letöltött **HotelReviews-Free. csv** fájlt.

    ![Adatok feltöltése](media/knowledge-store-create-portal/upload-command-bar.png "A szállodai értékelések feltöltése")

1. Kattintson a **feltöltés** gombra a CSV-fájl Azure Blob Storageba való importálásához. Ekkor megjelenik az új tároló.

    ![Az Azure Blob-tároló létrehozása](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Az Azure Blob-tároló létrehozása")

### <a name="get-the-azure-storage-account-connection-string"></a>Az Azure Storage-fiókhoz tartozó kapcsolatok karakterláncának beolvasása

1. A portálon navigáljon az Azure Storage-fiókjához.
1. A szolgáltatás bal oldali navigációs sávján kattintson a **hozzáférési kulcsok**elemre.
1. Az **1. kulcs**alatt másolja és mentse a *kapcsolatok karakterláncát*. A sztring a karakterlánccal `DefaultEndpointsProtocol=https`kezdődik. A Storage-fiók neve és kulcsa be van ágyazva a karakterláncba. A karakterlánc megtartása praktikus. A későbbi lépésekben szüksége lesz rá.

## <a name="create-and-run-ai-enrichments"></a>AI-bővítések létrehozása és futtatása

Az adatimportálás varázsló segítségével hozza létre a tudásbázist. Hozzon létre egy adatforrást, válassza a gazdagodás, a Tudásbázis konfigurálása és az index lehetőséget, majd hajtsa végre a parancsot.

### <a name="start-the-import-data-wizard"></a>Az adatimportálás varázsló elindítása

1. A Azure Portal keresse meg a [keresési szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. A parancssorban kattintson az **adatimportálás** gombra az Importálás varázsló elindításához.

### <a name="connect-to-your-data-import-data-wizard"></a>Kapcsolódás az adataihoz (az adatimportálás varázsló)

A varázsló lépésében létre fog hozni egy adatforrást az Azure-Blobból a saját szállodák adataival.

1. Az **adatforrás** listában válassza az **Azure Blob Storage**elemet.
1. A **név**mezőbe írja be `hotel-reviews-ds`a következőt:.
1. **Elemzési mód**esetén válassza a **tagolt szöveg**lehetőséget, majd jelölje be az **első sor fejléce** jelölőnégyzetet. Győződjön meg arról, hogy az elválasztó **karakter** egy vessző (,).
1. Adja meg az előző lépésben mentett Storage szolgáltatásbeli **kapcsolatok sztringjét** .
1. A **tároló neve**mezőbe írja `hotel-reviews`be a következőt:.
1. Kattintson **a Tovább gombra: Kognitív keresés hozzáadása (nem kötelező**).

      ![Adatforrás-objektum létrehozása](media/knowledge-store-create-portal/hotel-reviews-ds.png "Adatforrás-objektum létrehozása")

## <a name="add-cognitive-search-import-data-wizard"></a>Kognitív keresés hozzáadása (az adatimportálás varázsló)

A varázsló lépésében egy készségkészlet hoz létre a kognitív képességek gazdagítása révén. Az ebben a mintában használt szaktudás Kinyeri a legfontosabb kifejezéseket, és felismeri a nyelvet és a véleményét. Ezeket a bővítéseket a rendszer az Azure Tables (Tudásbázisban) a Knowledge Store-ba helyezi el.

1. Bontsa ki a **csatolás Cognitive Services**elemet. Alapértelmezés szerint az **ingyenes (korlátozott alkoholtartalom-növelés)** beállítás van kiválasztva. Ezt az erőforrást használhatja, mert a HotelReviews-Free. csv fájlban lévő rekordok száma 19, és ez az ingyenes erőforrás naponta legfeljebb 20 tranzakciót tesz lehetővé.
1. Bontsa ki a bővítések **hozzáadása**elemet.
1. A **készségkészlet neve**mezőbe írja `hotel-reviews-ss`be a következőt:.
1. A **forrásadatok mezőben**válassza a **reviews_text*elemet.
1. A **dúsítás részletességi szintjének**kiválasztásához válassza a **lapok (5000 karakteres tömbök) elemet.**
1. Válassza ki ezeket a kognitív képességeket:
    + **Kulcsszavak kinyerése**
    + **Nyelv felismerése**
    + **Érzelmek észlelése**

      ![Készségkészlet létrehozása](media/knowledge-store-create-portal/hotel-reviews-ss.png "Készségkészlet létrehozása")

1. Bontsa ki a bővítések **mentése a Knowledge Store-** ba lehetőséget.
1. Adja meg az előző lépésben mentett **Storage-fiókhoz tartozó kapcsolatok sztringjét** .
1. Válassza ki ezeket az **Azure Table-kivetítéseket**:
    + **Dokumentumok**
    + **Oldalak**
    + **Legfontosabb kifejezések**

    A ![Knowledge Store konfigurálása] A (media/knowledge-store-create-portal/hotel-reviews-ks.png "Knowledge Store konfigurálása")

1. Kattintson **a Tovább gombra: A célként megadott**index testreszabása

### <a name="import-data-import-data-wizard"></a>Adatimportálás (az adatimportálás varázsló)

Ebben a varázslóban a választható teljes szöveges keresési lekérdezések indexét kell konfigurálnia. A varázsló felveszi az adatforrást a mezők és adattípusok lekötésére. Csak ki kell választania a kívánt viselkedés attribútumait. A lekérdezhető **attribútum például** lehetővé teszi, hogy a Search szolgáltatás a mező értékét visszaállítsa, miközben a **kereshetőség** lehetővé teszi a teljes szöveges keresést a mezőben.

1. Az **index neve**mezőbe írja `hotel-reviews-idx`be a következőt:.
1. Attribútumok esetén hajtsa végre a következő beállításokat:
    + Válassza ki a **beolvasható** lehetőséget az összes mezőnél.
    + Jelölje ki a következő mezők **szűrhetők** és **sokrétűek** : *Érzelmek*, *nyelv*, *alkifejezések*
    + Válassza a **kereshető** lehetőséget a következő mezőknél: *város*, *név*, *reviews_text*, *nyelv*, *kifejezés*

    Az indexnek az alábbi képhez hasonlóan kell kinéznie. Mivel a lista hosszú, nem minden mező látható a képen.

    ![Index konfigurálása](media/knowledge-store-create-portal/hotel-reviews-idx.png "Index konfigurálása")

1. Kattintson **a Tovább gombra: Hozzon létre egy**indexelő.

### <a name="create-an-indexer"></a>Indexelő létrehozása

A varázsló lépésében olyan indexelő fog konfigurálni, amely összefogja az adatforrást, a készségkészlet és a varázsló előző lépéseiben megadott indexet.

1. A **név**mezőbe írja `hotel-reviews-idxr`be a következőt:.
1. Az **ütemterv** **beállításnál**tartsa meg az alapértelmezett értéket.
1. Az indexelő futtatásához kattintson a **Submit (elküldés** ) gombra. Az adatgyűjtés, az indexelés, a kognitív képességek alkalmazása mindez ebben a lépésben történik.

### <a name="monitor-the-notifications-queue-for-status"></a>Az értesítési várólista állapotának figyelése

1. A Azure Portalban figyelje az értesítési tevékenység naplóját egy kattintható **Azure Search értesítési** állapot hivatkozására. A végrehajtás több percet is igénybe vehet.

## <a name="next-steps"></a>További lépések

Most, hogy bővítette az adatait a kognitív szolgáltatásokkal, és kivetítette az eredményeket egy tudásbázisba, használhatja Storage Explorer vagy Power BIt a dúsított adatkészletek megismeréséhez.

Az alábbi útmutatóban megismerheti, hogyan derítheti fel ezt a tudásbázist a Storage Explorer használatával.

> [!div class="nextstepaction"]
> [Megtekintés Storage Explorer](knowledge-store-view-storage-explorer.md)

A következő útmutatóban megismerheti, hogyan csatlakoztatható a Power BI a Knowledge Store-hoz.

> [!div class="nextstepaction"]
> [Kapcsolódás PowerBI-jal](knowledge-store-connect-power-bi.md)

Ha szeretné megismételni ezt a gyakorlatot, vagy próbáljon meg egy másik AI-bővítési bemutatót használni, törölje a *Hotel-Reviews-idxr* indexelő. Az indexelő törlése visszaállítja az ingyenes napi tranzakció számlálóját nullára.
