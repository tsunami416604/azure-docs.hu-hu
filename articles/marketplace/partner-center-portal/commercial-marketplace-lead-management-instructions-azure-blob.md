---
title: Azure Table a kereskedelmi piactéren program| Azure Piactér
description: Érdeklődőkezelés konfigurálása az Azure Blobhoz
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: dsindona
ms.openlocfilehash: 062252b007e22fcd2644c8b647fc0ecc2f5938cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285248"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Az Azure Blob vezetőkezelési utasításai

>[!Caution]
>Az Azure Blob-beállítás a piactéri ajánlat érdeklődőinek feldolgozásához elavult. Ha jelenleg közzétett egy ajánlatot az Azure Blob érdeklődőkezelési konfigurációjával, akkor már nem kap ügyfélérdeklődőket. Frissítse az érdeklődőkezelési konfigurációt a többi érdeklődőkezelési beállítás bármely más beállítására. További információ az [érdeklődőkezelési céloldalon](./commercial-marketplace-get-customer-leads.md)található egyéb lehetőségekről."

Ha az ügyfélkapcsolat-kezelési (CRM) rendszer nem kifejezetten támogatja a Partner Center az Azure Marketplace-en és AppSource-érdeklődők fogadására, használhatja az Azure Blob kezelni ezeket az érdeklődőket. Ezután exportálhatja és importálhatja az adatokat a CRM rendszerbe. Ebben a cikkben található utasításokat az Azure Storage-fiók létrehozásának folyamata, és egy Azure Blob az adott fiók alatt. Emellett a Microsoft Flow segítségével új folyamatot is létrehozhat, amelyei e-mailértesítést küldhet, ha az ajánlat érdeklődőt kap.


## <a name="how-to-configure-azure-blob"></a>Az Azure Blob konfigurálása

1. Ha nem rendelkezik Azure-fiókkal, [létrehozhat egy ingyenes próbafiókot.](https://azure.microsoft.com/pricing/free-trial/)
1. Miután az Azure-fiók aktív, jelentkezzen be az [Azure Portalon.](https://portal.azure.com)
1. Az Azure Portalon hozzon létre egy tárfiókot az alábbi eljárással.  
    1. Válassza **a +Erőforrás létrehozása** lehetőséget a bal oldali menüsorban.  Az **Új** ablaktábla (panel) jobbra jelenik meg.
    2. Válassza a **Tárolás** lehetőséget az **Új** ablaktáblában.  A **kiemelt** lista jobbra jelenik meg.
    3. Válassza ki a **tárfiók** a fiók létrehozásának megkezdéséhez.  Kövesse a tárfiók létrehozása című [cikkutasításait.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    ![Az Azure-tárfiók létrehozásának lépései](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    A tárfiókokról további információt a [Rövid útmutató lehetőséget](https://docs.microsoft.com/azure/storage/)válassza.  A tárolási díjszabásról a [tárolási díjszabás című](https://azure.microsoft.com/pricing/details/storage/)témakörben talál további információt.

4. Várjon, amíg a tárfiók ki van építve, egy folyamat, amely általában néhány percet vesz igénybe.  Ezután az Azure Portal **kezdőlapjáról** érheti el a tárfiókot az **Összes erőforrás megtekintése** vagy az Azure Portal bal oldali navigációs menüsorának Minden **erőforrás** elemének kiválasztásával.

    ![Az Azure storage-fiók elérése](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. A tárfiók ablaktábláján válassza az **Access-kulcsok** lehetőséget, és másolja a *kulcs kapcsolati* karakterlánc-értékét. Mentse ezt az értéket, mivel ez a *storage-fiók kapcsolati karakterlánc* értéke, amelyet meg kell adnia a közzétételi portálon, hogy érdeklődőket kapjon a piactéri ajánlathoz.

     Egy példa a kapcsolat csípése a következő:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Azure tárolási kulcs](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. A tárfiók lapján válassza a **Blobok**lehetőséget.

   ![Azure tárolási kulcs](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Miután a blobok oldalon, válassza a **+ Tároló** gombot.

8. Írja be az új tároló **nevét.** A tároló neve csak kisbetűket tartalmazhat, betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjelet (-) tartalmazhat. A tároló- és blobnevekről további információt a [tárolók, blobok és metaadatok elnevezése és hivatkozása](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)című témakörben talál.

    Mentse ezt az értéket, mivel ez a *tároló név* értéke, amelyet meg kell adnia a közzétételi portálon a piactéri ajánlat érdeklődőinek fogadásához.

9. Állítsa be a tárolóhoz való nyilvános hozzáférés szintjét **privátként (nincs névtelen hozzáférés).**

10. A tároló létrehozásához válassza az **OK** gombot.

    ![Új tároló](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>Az ajánlat konfigurálása érdeklődők küldésére az Azure Blobba

Ha készen áll az ajánlat érdeklődőkezelési adatainak konfigurálására a közzétételi portálon, kövesse az alábbi lépéseket:

1. Nyissa meg az **ajánlat beállítási** oldalát.
2. Válassza a **Csatlakozás** lehetőséget az Érdeklődőkezelés szakaszban.

    ![Csatlakozás ajánlat](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. A Kapcsolat részletei előugró ablakban válassza az **Azure Blob** az érdeklődő cél.

    ![Részletek összekapcsolása](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Adja meg a **tároló nevét** és **a storage-fiók kapcsolati karakterlánckapott** az alábbi utasításokat követve.

    * Példa a tároló nevére:`marketplaceleadcontainer`
    * Például a tárfiók kapcsolatának karakterlánca: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![Kapcsolat részletei](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > Be kell fejeznie az ajánlat többi részének konfigurálását, és közzé kell tennie, mielőtt érdeklődőket kapna az ajánlathoz.


