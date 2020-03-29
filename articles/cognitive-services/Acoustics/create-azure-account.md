---
title: Project Acoustics Azure Batch-fiók beállítása
titlesuffix: Azure Cognitive Services
description: Ez az útmutató egy Azure Batch-fiók beállítását ismerteti a Project Acoustics Unity és az Unreal motorintegrációk számára.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 8f0f726d9d23f20698d3510ad674331ad74fb703
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68855083"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Project Acoustics Azure Batch-fiók beállítása
Ez az útmutató egy Azure Batch-fiók beállítását ismerteti a Project Acoustics Unity és az Unreal motorintegrációk számára.

## <a name="get-an-azure-subscription"></a>Azure-előfizetés beszerzése
A Batch és storage-fiókok beállítása előtt [egy Azure-előfizetés](https://azure.microsoft.com/free/) szükséges. Ha első alkalommal regisztrál, az Azure néhány korlátozott időre szóló szabad erőforrást és $200-os kreditet biztosít.

## <a name="create-azure-batch-and-storage-accounts"></a>Azure Batch- és tárfiókok létrehozása
Ezután kövesse [az alábbi utasításokat](https://docs.microsoft.com/azure/batch/batch-account-create-portal) az Azure Batch és a kapcsolódó Azure Storage-fiókok beállításához.

Válassza az alapértelmezett beállításokat mind a Batch, mind a Storage fiókokhoz:
  
  ![Képernyőkép az Azure Batch új fiókbeállításairól az alapértelmezett beállításokkal](media/new-batch-account-create.png)

  ![Képernyőkép az Azure Storage új fiókbeállításairól az alapértelmezett beállításokkal](media/batch-storage-account-create.png)

Az Azure-nak néhány percet kell igénybe adnia a fiókok üzembe helyezése. Keressen egy befejezési értesítést a portál jobb felső sarkában.
  
  ![Képernyőkép az Azure-fiókok üzembe helyezett értesítéséről](media/batch-accounts-deploy-notification.png)

A fiókoknak most már láthatónak kell lenniük az irányítópulton.
  
  ![Képernyőkép az Azure Portal irányítópultjáról, amelyen egy Batch- és Storage-fiók látható](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Akusztikai bake felhasználói felület beállítása Azure-hitelesítő adatokkal
Kattintson a Batch-fiók hivatkozásra az irányítópulton, majd kattintson a **Kulcsok** hivatkozásra a Kötegfiók lapon a hitelesítő adatok eléréséhez.
  
  ![Képernyőkép az Azure Batch-fiókról, amely a Kulcsok lapra mutató hivatkozással van kiemelve](media/batch-access-keys.png)

  ![Képernyőkép az Azure Batch-fiók kulcsailapról hozzáférési kulcsokkal](media/batch-keys-info.png)

Kattintson a **tárfiók** hivatkozásra az oldalon az Azure Storage-fiók hitelesítő adatainak eléréséhez.
  
  ![Képernyőkép az Azure Storage-fiók kulcsailap hozzáférési kulcsokkal](media/storage-keys-info.png)

Adja meg ezeket a hitelesítő adatokat a [Unity sütni plugin](unity-baking.md) vagy [Unreal sütni plugin](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Csomóponttípusok és régiótámogatás
A Project Acoustics fsv2- és H-sorozatú, optimalizált Azure Virtuálisgép-csomópontokat igényel, amelyek nem minden Azure-régióban támogatottak. Ellenőrizze [ezt a táblázatot,](https://azure.microsoft.com/global-infrastructure/services) és győződjön meg arról, hogy a Batch-fiókmegfelelő helyet választja.
![Az Azure virtuális gépek régiónkénti képe](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>A kvóta frissítése
Az Azure Batch-fiókok a fiók létrehozása 20 számítási magok kiépítésével vannak kiépítve. Érdemes lehet növelni ezt a korlátot a gyorsabb sütési idő érdekében, mert számos csomóponton párhuzamosíthatja az akusztikai munkaterhelést, akár a felderítési pontok számát a jelenetben. Kvótanövelést kérhet, ha az Azure Batch-portál oldalán a **Kvóta** hivatkozásra kattint, majd a **Kvótanövelése kérése**elemre kattint:

![Képernyőkép az Azure Kvóta lapról](media/azure-quotas.png)

## <a name="next-steps"></a>További lépések
* Integrálja a Project Acoustics bővítményt [unity](unity-integration.md) vagy [Unreal](unreal-integration.md) projektjébe

