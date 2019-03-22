---
title: Projekt Akusztika az Azure Batch-fiók beállítása
titlesuffix: Azure Cognitive Services
description: Ebben az útmutatóban a projekt Akusztika Unity és az Unreal engine Integrációk segítségével Azure Batch-fiók beállításának a módját ismerteti.
services: cognitive-services
author: ashtat
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 7a7f5f6738b4bc96b6248deb062c7b3f63048148
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58309673"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Projekt Akusztika az Azure Batch-fiók beállítása
Ebben az útmutatóban a projekt Akusztika Unity és az Unreal engine Integrációk segítségével Azure Batch-fiók beállításának a módját ismerteti.

## <a name="get-an-azure-subscription"></a>Azure-előfizetés beszerzése
Egy [Azure-előfizetés](https://azure.microsoft.com/free/) Batch- és tárfiókok beállítása előtt meg kell adni. Ha első alkalommal szolgáltatásba, az Azure néhány időben korlátozott ingyenes forrásokkal és 200 USD értékű kreditet biztosít.

## <a name="create-azure-batch-and-storage-accounts"></a>Azure Batch- és storage-fiókok létrehozása
Ezután hajtsa végre a [ezek az utasítások](https://docs.microsoft.com/azure/batch/batch-account-create-portal) , állítsa be az Azure Batch és a társított Azure Storage-fiókokat.

Válassza ki az alapértelmezett beállítások a Batch- és Storage-fiókok:
  
  ![Képernyőkép az Azure Batch új fiókok beállítások alapértelmezett beállításainak megjelenítése:](media/new-batch-account-create.png)

  ![Képernyőkép az Azure Storage új fiókok beállítások alapértelmezett beállításainak megjelenítése:](media/batch-storage-account-create.png)

Az Azure-hoz, a fiókok üzembe helyezése néhány percet vesz igénybe. Keresse meg a portál jobb felső sarkában befejezéséről értesítést.
  
  ![Képernyőkép az Azure-fiókok üzembe helyezett értesítés](media/batch-accounts-deploy-notification.png)

A fiókok már megjelennek az irányítópulton.
  
  ![Képernyőkép az Azure portal irányítópultján egy Batch- és Storage-fiók megjelenítése](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Az Azure hitelesítő adatait a felhasználói felület Akusztika bake beállítása
Kattintson a az irányítópulton a Batch-fiók hivatkozására, majd kattintson a a **kulcsok** hivatkozásra az a Batch-fiók oldalon a hitelesítő adatok elérésére.
  
  ![Képernyőkép az Azure Batch-fiók a kiemelt kulcsok-oldalának hivatkozása](media/batch-access-keys.png)

  ![Képernyőkép az Azure Batch fiók kulcsok oldalán a hozzáférési kulcsok](media/batch-keys-info.png)

Kattintson a **Tárfiók** hivatkozása a lap eléréséhez az Azure Storage-fiókja hitelesítő adataival.
  
  ![Képernyőkép az Azure Storage fiók kulcsok oldalán a hozzáférési kulcsok](media/storage-keys-info.png)

Adja meg ezeket a hitelesítő adatait a [Unity bake beépülő modul](unity-baking.md) vagy [Unreal bake beépülő modul](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Csomóponttípusok és régió támogatása
Projekt Akusztika szükséges Fsv2 és H-sorozatú számítási erőforrás optimalizált, amely nem az összes Azure-régióban támogatott Azure-beli Virtuálisgép-csomópontok. Ellenőrizze a [Ez a táblázat](https://azure.microsoft.com/global-infrastructure/services) , győződjön meg arról, hogy a megfelelő helyet exportálná a Batch-fiók.
![Képernyőfelvétel: az Azure Virtual Machines régió szerint](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>A kvóta frissítése
Az Azure Batch-fiókok vannak üzembe helyezve, fiók létrehozása és a egy korlátja 20 a számítási magok. Előfordulhat, hogy szeretnénk gyorsabb bake a kvótahatár növelését, mert a Akusztika számítási feladat párhuzamosítható a számos csomópont, akár a jelenet mintavételi pontok száma. Kattintva kérheti a kvóta növelését a **kvóta** az Azure Batch-portál oldalán hivatkozásra, és kattintson a **kérelmet a kvóta növelése**:

![Képernyőkép az Azure-kvóta lap](media/azure-quotas.png)

## <a name="next-steps"></a>További lépések
* A projekt Akusztika beépülő modult be integrálása a [Unity](unity-integration.md) vagy [Unreal](unreal-integration.md) projekt

