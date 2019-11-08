---
title: Azure Table a kereskedelmi piactér programban | Azure piactér
description: Az Azure Blobhoz tartozó érdeklődői felügyelet konfigurálása
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: evansma
ms.openlocfilehash: 5da4e0ab315b3f66a477b816f6fc5d27de7aa339
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812365"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Az Azure Blob vezető felügyeleti utasításai

>[!Caution]
>A Piactéri ajánlatból származó érdeklődők feldolgozására szolgáló Azure Blob-beállítás elavult. Ha jelenleg az Azure Blobhoz vezető felügyeleti konfigurációval közzétett ajánlattal rendelkezik, már nem kap ügyfél-érdeklődőket. Frissítse az érdeklődői felügyeleti konfigurációt bármely más vezető felügyeleti lehetőségre. További információ a [vezető felügyeleti kezdőlapon](./commercial-marketplace-get-customer-leads.md)elérhető egyéb lehetőségekről.

Ha az Ügyfélkapcsolat-kezelési (CRM) rendszer nincs kifejezetten támogatva a partner Centerben az Azure Marketplace és a AppSource-érdeklődők fogadásához, akkor az Azure-Blob használatával kezelheti ezeket az érdeklődőket. Ezután exportálhatja az adatait, és importálhatja azt a CRM-rendszerbe. Az ebben a cikkben szereplő utasítások végigvezetik az Azure Storage-fiók létrehozásának folyamatán, valamint az adott fiókhoz tartozó Azure-blobon. Emellett létrehozhat egy új folyamatot Microsoft Flow használatával, amely e-mailben értesítést küld, ha az ajánlat érdeklődőt kap.


## <a name="how-to-configure-azure-blob"></a>Az Azure Blob konfigurálása

1. Ha nem rendelkezik Azure-fiókkal, [létrehozhat egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/pricing/free-trial/).
1. Ha az Azure-fiókja aktív, jelentkezzen be a [Azure Portalba](https://portal.azure.com).
1. A Azure Portal a következő eljárással hozzon létre egy Storage-fiókot.  
    1. Válassza az **+ erőforrás létrehozása** lehetőséget a bal oldali menüsorban.  Ekkor megjelenik az **új** ablaktábla (panel) a jobb oldalon.
    2. Válassza a **tároló** lehetőséget az **új** ablaktáblán.  Egy **Kiemelt** lista jelenik meg a jobb oldalon.
    3. A fiók létrehozásának megkezdéséhez válassza ki a **Storage-fiókot** .  Kövesse a [Storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)című cikk utasításait.

    ![Azure Storage-fiók létrehozásának lépései](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    A Storage-fiókokkal kapcsolatos további információkért válassza a gyors üzembe helyezési [oktatóanyag](https://docs.microsoft.com/azure/storage/)elemet.  További információ a Storage díjszabásáról: [Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).

4. Várjon, amíg megtörténik a Storage-fiók üzembe helyezése, egy folyamat, amely általában néhány percet vesz igénybe.  Ezután nyissa meg a Storage-fiókját a Azure Portal **kezdőlapján** az **összes erőforrás** megjelenítése lehetőség kiválasztásával, vagy a Azure Portal bal oldali navigációs sávján található **összes erőforrás** kiválasztásával.

    ![Hozzáférés az Azure Storage-fiókhoz](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. A Storage-fiók ablaktáblán válassza a **hozzáférési kulcsok** lehetőséget, és másolja a kulcshoz tartozó *kapcsolati sztring* értéket. Mentse ezt az értéket, mivel ez a *Storage-fiók kapcsolódási karakterláncának* értéke, amelyet a közzétételi portálon kell megadnia a Piactéri ajánlathoz tartozó érdeklődők fogadásához.

     A kapcsolatok csípése például a következőket szemlélteti:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Azure Storage-kulcs](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. A Storage-fiók lapon válassza a **Blobok**lehetőséget.

   ![Azure Storage-kulcs](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. A Blobok lapon kattintson a **+ tároló** gombra.

8. Adja meg az új tároló **nevét** . A tároló neve csak kisbetűket tartalmazhat, betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjelet (-) tartalmazhat. A tárolók és a blobok elnevezésével kapcsolatos további információkért tekintse meg a [tárolók, blobok és metaadatok elnevezésével és hivatkozásával foglalkozó cikket](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

    Mentse ezt az értéket, mivel ez a *tároló neve* érték, amelyet a közzétételi portálon kell megadnia, hogy fogadja az érdeklődőket a Piactéri ajánlathoz.

9. Adja meg a tároló nyilvános hozzáférésének szintjét **magánjellegűként (névtelen hozzáférés nélkül)** .

10. A tároló létrehozásához válassza az **OK** gombot.

    ![Új tároló](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>Az ajánlat beállítása az Azure-Blobba való küldésre

Ha készen áll az ajánlathoz tartozó érdeklődői felügyeleti információk konfigurálására a közzétételi portálon, kövesse az alábbi lépéseket:

1. Navigáljon az ajánlat **telepítési** lapjára.
2. Válassza a **kapcsolat** lehetőséget az érdeklődő felügyelete szakaszban.

    ![Csatlakozási ajánlat](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. A kapcsolat részletei előugró ablakban válassza az **Azure Blob** lehetőséget az érdeklődő célhelyéhez.

    ![Részletek összekapcsolása](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Adja meg a **tároló nevét** és a Storage-fiókhoz tartozó, a következő utasításokat követve kapott **hálózati karakterláncot** .

    * Tároló neve példa: `marketplaceleadcontainer`
    * A Storage-fiók kapcsolatok karakterláncának példája: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![a kapcsolatok részletei](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > Be kell fejeznie az ajánlat többi részének konfigurálását, és közzé kell tennie az ajánlathoz tartozó érdeklődők fogadása előtt.


