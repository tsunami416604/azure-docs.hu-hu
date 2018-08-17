---
title: Azure Akusztika – Cognitive Services-fiókok beállítása
description: Ezt az útmutatót Akusztika használata az Azure Batch- és Storage-fiókok beállításához.
services: cognitive-services
author: ashtat
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: d5e78df2cb17e8275aef3694dda90a705ef4bdaa
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181641"
---
# <a name="create-an-azure-batch-account"></a>Az Azure Batch-fiók létrehozása
Ezt az útmutatót Akusztika használata az Azure Batch- és Storage-fiókok beállításához. A Unity beépülő modul projekt Akusztika részeként kifejlesztett kapcsolatos információkért lásd: [Akusztika mi](what-is-acoustics.md). Akusztika beépítheti a Unity-projektjét kapcsolatos információkért lásd: [bevezetés](getting-started.md).  

## <a name="get-an-azure-subscription"></a>Azure-előfizetés beszerzése
Egy [Azure-előfizetés](https://azure.microsoft.com/free/) Batch- és tárfiókok beállítása előtt meg kell adni. Ha első alkalommal szolgáltatásba, az Azure néhány időben korlátozott ingyenes forrásokkal és 200 USD értékű kreditet biztosít.

## <a name="create-azure-batch-and-storage-accounts"></a>Azure Batch- és storage-fiókok létrehozása
Ezután hajtsa végre a [ezek az utasítások](https://docs.microsoft.com/azure/batch/batch-account-create-portal) , állítsa be az Azure Batch és a társított Azure Storage-fiókokat.

Válassza ki az alapértelmezett beállítások a Batch- és Storage-fiókok:
  
  ![Új Batch-fiók](media/NewBatchAccountCreate.png)

  ![Új Tárfiók](media/BatchStorageAccountCreate.png)

Az Azure-hoz, a fiókok üzembe helyezése néhány percet vesz igénybe. Keresse meg a portál jobb felső sarkában befejezéséről értesítést.
  
  ![Üzembe helyezett fiókok](media/BatchAccountsDeployNotification.png)

A fiókok már megjelennek az irányítópulton.
  
  ![Portál irányítópultján](media/AzurePortalDashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Az Azure hitelesítő adatait a felhasználói felület Akusztika bake beállítása
Kattintson a az irányítópulton a Batch-fiók hivatkozására, majd kattintson a a **kulcsok** hivatkozásra az a Batch-fiók oldalon a hitelesítő adatok elérésére.
  
  ![Batch-kulcsok hivatkozás](media/BatchAccessKeys.png)

  ![A Batch-fiók hitelesítő adatai](media/BatchKeysInfo.png)

Kattintson a **Tárfiók** hivatkozása a lap eléréséhez az Azure Storage-fiókja hitelesítő adataival.
  
  ![Tárfiók hitelesítő adatai](media/StorageKeysInfo.png)

Adja meg ezeket a hitelesítő adatokat a Bake lapon ismertetett módon a [os felhasználói felület forgatókönyv](bake-ui-walkthrough.md).

## <a name="node-types-and-region-support"></a>Csomóponttípusok és régió támogatása
Projekt Akusztika szükséges F és a H-sorozatú számítási erőforrás optimalizált, amely nem az összes Azure-régióban támogatott Azure-beli Virtuálisgép-csomópontok. Ellenőrizze a [Ez a táblázat](https://azure.microsoft.com/global-infrastructure/services) , győződjön meg arról, hogy a megfelelő helyet exportálná a Batch-fiók. Ez kis időt a H-sorozat következő virtuális gépek támogatottak, az USA keleti RÉGIÓJA, USA északi középső Régiója, USA déli középső Régiója, USA nyugati RÉGIÓJA, 2. nyugati RÉGIÓJA, Észak-Európa, Nyugat-Európában és Nyugat-japán.

## <a name="upgrading-your-quota"></a>A kvóta frissítése
Az Azure Batch-fiókok vannak üzembe helyezve, fiók létrehozása és a egy korlátja 20 a számítási magok. Előfordulhat, hogy bővíteni szeretné ezt a korlátot, gyorsabb bake, mert a Akusztika számítási feladat párhuzamosítható a számos csomópont, akár a jelenet mintavételi pontok száma. Kattintva kérheti a kvóta növelését a **kvóta** az Azure Batch-portál oldalán hivatkozásra, és kattintson a **kérelmet a kvóta növelése**:

![Az Azure Kvótanövelést](media/azurequotas.png)

## <a name="next-steps"></a>További lépések
* Első lépések [Akusztika integrálása a Unity-projektjét](getting-started.md)
* Fedezze fel a [minta jelenet renderelése;](sample-walkthrough.md)

