---
title: Azure Stack Edge Storage-fiókok kezelése | Microsoft Docs
description: Ismerteti, hogyan kezelhető a Azure Portal a Storage-fiók kezeléséhez az Azure Stack Edge-ben.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 12/11/2019
ms.author: alkohli
ms.openlocfilehash: 01148fabd6fb5df791ac09090304feee8c72a2d1
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89086616"
---
# <a name="use-the-azure-portal-to-manage-edge-storage-accounts-on-your-azure-stack-edge"></a>Az Edge Storage-fiókok kezelése az Azure Stack Edge-Azure Portal használatával

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Ez a cikk azt ismerteti, hogyan kezelhetők az Edge Storage-fiókok az Azure Stack Edge-ben. Az Azure Stack Edge a Azure Portal vagy a helyi webes felületen keresztül kezelhető. A Azure Portal használatával hozzáadhat vagy törölhet Edge Storage-fiókokat az eszközön.

## <a name="about-edge-storage-accounts"></a>Az Edge Storage-fiókok ismertetése

Az Azure Stack Edge-eszközről az SMB, az NFS vagy a REST protokollok használatával vihet át adatátvitelt. Ha a REST API-k használatával szeretne adatátvitelt végezni a blob Storage-ba, létre kell hoznia egy Edge Storage-fiókot az Azure Stack peremhálózati számítógépen. 

Az Azure Stack peremhálózati eszközön hozzáadott Edge Storage-fiókok az Azure Storage-fiókokra vannak leképezve. A rendszer automatikusan leküldi a felhőbe az Edge Storage-fiókba írt összes adatsort.

Az alábbi ábra a fiókok két típusát részletezi, és az egyes fiókoktól az Azure-ba irányuló adatfolyamatokat mutatja be:

![BLOB Storage-fiókok diagramja](media/azure-stack-edge-j-series-manage-storage-accounts/ase-blob-storage.svg)

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Edge Storage-fiók hozzáadása
> * Edge Storage-fiók törlése


## <a name="add-an-edge-storage-account"></a>Edge Storage-fiók hozzáadása

Edge Storage-fiók létrehozásához hajtsa végre a következő eljárást:

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]

## <a name="delete-an-edge-storage-account"></a>Edge Storage-fiók törlése

A következő lépésekkel törölheti az Edge Storage-fiókot.

1. Nyissa meg az erőforrás **konfigurációs > Storage-fiókjait** . A Storage-fiókok listájából válassza ki a törölni kívánt Storage-fiókot. A felső menüsávban válassza a **Storage-fiók törlése**lehetőséget.

    ![Ugrás a Storage-fiókok listájához](media/azure-stack-edge-j-series-manage-storage-accounts/delete-edge-storage-account-1.png)

2. A **Storage-fiók törlése** panelen erősítse meg a törölni kívánt Storage-fiókot, és válassza a **Törlés**lehetőséget.

    ![Storage-fiók megerősítése és törlése](media/azure-stack-edge-j-series-manage-storage-accounts/delete-edge-storage-account-2.png)

A rendszer frissíti a Storage-fiókok listáját, hogy tükrözze a törlést.


## <a name="add-delete-a-container"></a>Tároló hozzáadása, törlése

Hozzáadhat vagy törölhet is tárolókat ezekhez a Storage-fiókokhoz.

Tároló hozzáadásához hajtsa végre a következő lépéseket:

1. Válassza ki a kezelni kívánt Storage-fiókot. A felső menüsávban válassza a **+ tároló hozzáadása**elemet.

    ![Válassza ki a Storage-fiókot a tároló hozzáadásához](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-1.png)

2. Adja meg a tároló nevét. Ez a tároló a peremhálózati Storage-fiókban, valamint a fiókhoz hozzárendelt Azure Storage-fiókban jön létre. 

    ![Edge-tároló hozzáadása](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-2.png)

A tárolók listája frissítve lett az újonnan hozzáadott tárolónak megfelelően.

![A tárolók frissített listája](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-4.png)

Mostantól kiválaszthat egy tárolót a listából, és kiválaszthatja a felső parancssáv **+ tároló törlése** lehetőségét. 

![Tároló törlése](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-3.png)

## <a name="sync-storage-keys"></a>Tárkulcsok szinkronizálása

Szinkronizálhatja az eszközön található peremhálózati (helyi) Storage-fiókok hozzáférési kulcsait. 

A Storage-fiók elérési kulcsának szinkronizálásához hajtsa végre a következő lépéseket:

1. Az erőforrásban válassza ki a kezelni kívánt Storage-fiókot. A felső menüsávban válassza ki a **Storage-kulcs szinkronizálása**elemet.

    ![Válassza ki a Storage-kulcs szinkronizálása lehetőséget](media/azure-stack-edge-j-series-manage-storage-accounts/sync-storage-key-1.png)

2. Ha a rendszer megerősítést kér, válassza az **Igen**lehetőséget.

    ![Válassza ki a Storage-kulcs szinkronizálása lehetőséget](media/azure-stack-edge-j-series-manage-storage-accounts/sync-storage-key-2.png)

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [kezelheti a felhasználókat az Azure Portalon keresztül](azure-stack-edge-j-series-manage-users.md).
