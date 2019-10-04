---
title: Projekt akusztika Azure Batch fiók beállítása
titlesuffix: Azure Cognitive Services
description: Ez a útmutató leírja, hogyan állítható be egy Azure Batch-fiók a Project akusztikai egység és az Unreal Engine integrációjának használatával.
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
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855083"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Projekt akusztika Azure Batch fiók beállítása
Ez a útmutató leírja, hogyan állítható be egy Azure Batch-fiók a Project akusztikai egység és az Unreal Engine integrációjának használatával.

## <a name="get-an-azure-subscription"></a>Azure-előfizetés igénylése
A Batch-és Storage-fiókok beállítása előtt [Azure](https://azure.microsoft.com/free/) -előfizetésre van szükség. Ha első alkalommal regisztrál, az Azure-ban néhány korlátozott ingyenes erőforrás és $200 kredit áll rendelkezésre.

## <a name="create-azure-batch-and-storage-accounts"></a>Azure Batch-és Storage-fiókok létrehozása
Ezután kövesse az [alábbi utasításokat](https://docs.microsoft.com/azure/batch/batch-account-create-portal) a Azure batch és a társított Azure Storage-fiókok beállításához.

Válassza ki a Batch-és a Storage-fiókok alapértelmezett beállításait:
  
  ![Képernyőkép a Azure Batch új fiókok beállításairól az alapértelmezett beállítások megjelenítéséhez](media/new-batch-account-create.png)

  ![Képernyőkép az Azure Storage új fiókjairól az alapértelmezett beállítások megjelenítéséhez](media/batch-storage-account-create.png)

Az Azure-ban a fiókok üzembe helyezése néhány percet vesz igénybe. A portál jobb felső sarkában keresse meg a befejezési értesítést.
  
  ![Képernyőkép az Azure-fiókok üzembe helyezéséről](media/batch-accounts-deploy-notification.png)

A fiókoknak most már láthatónak kell lenniük az irányítópulton.
  
  ![A Batch-és Storage-fiókot bemutató Azure Portal irányítópult képernyőképe](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Akusztikai sütőipari felhasználói felület beállítása Azure-beli hitelesítő adatokkal
Kattintson a Batch-fiók hivatkozásra az irányítópulton, majd a Batch-fiók lapon található **kulcsok** hivatkozásra kattintva érheti el a hitelesítő adatait.
  
  ![Képernyőkép a Azure Batch-fiókról, amelynek hivatkozása a kulcsok lapra van kijelölve](media/batch-access-keys.png)

  ![Képernyőkép az Azure Batch-fiók kulcsainak oldaláról hozzáférési kulcsokkal](media/batch-keys-info.png)

Kattintson a **Storage-fiók** hivatkozásra az oldalon az Azure Storage-fiók hitelesítő adatainak eléréséhez.
  
  ![Képernyőkép az Azure Storage-fiók kulcsainak oldaláról hozzáférési kulcsokkal](media/storage-keys-info.png)

Adja meg ezeket a hitelesítő adatokat az [Unity sütni beépülő modulban](unity-baking.md) vagy az [Unreal Bake beépülő modulban](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Csomópontok típusai és régió támogatása
A Project akusztikai Fsv2-és H-sorozatú, optimalizált Azure-beli virtuálisgép-csomópontokra van szükség, amelyek esetleg nem támogatottak az összes Azure-régióban. Ebben a [táblázatban](https://azure.microsoft.com/global-infrastructure/services) ellenőrizheti, hogy a Batch-fiókjának megfelelő helyet választotta-e.
![Az Azure Virtual Machines régió szerinti megjelenítését bemutató képernyőfelvétel](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>A kvóta frissítése
Azure Batch fiókokat a fiók létrehozásakor 20 számítási magot tartalmazó korláttal kell kiépíteni. Érdemes lehet ezt a korlátot a gyorsabb sütni-idő növelésére használni, mert számos csomóponton integrálással az akusztikai számítási feladatokat, akár a jelenetben lévő mintavételi pontok számával. A kvóta növeléséhez kattintson a Azure Batch portál oldalán található **kvóta** hivatkozásra, majd kattintson a **kérelmek kvótájának növelésére**:

![Az Azure-kvóta oldalának képernyőképe](media/azure-quotas.png)

## <a name="next-steps"></a>További lépések
* A Project akusztikai beépülő modul integrálása az [Unity](unity-integration.md) vagy az [Unreal](unreal-integration.md) projektbe

