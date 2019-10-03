---
title: További Azure-előfizetés létrehozása a számlázási fiókban
description: Megtudhatja, hogyan vehet fel új Azure-előfizetést az Azure Portalon.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: e1fafafe5f67d1775ca80a1f7c2aff4dec9e0bc4
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709585"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>További Azure-előfizetés létrehozása a Microsoft-ügyfélszerződésben

További előfizetéseket hozhat létre a számlázási fiókhoz önálló környezetek fejlesztéshez és teszteléshez történő beállítása, a biztonság érdekében vagy az adatok megfelelőségi okokból történő izolálásához.

Ez a cikk a Microsoft-ügyfélszerződéshez tartozó számlázási fiókokra vonatkozik. [Ellenőrizze, hogy rendelkezik-e hozzáféréssel a Microsoft-ügyfélszerződéshez](#check-access). Előfizetések más típusú számlázási fiókokhoz történő létrehozásával kapcsolatban lásd a [további előfizetések az Azure Portalon történő létrehozását](billing-create-subscription.md).

Előfizetés létrehozásához a **számlázási szakasz tulajdonosának**, a **számlázási szakasz közreműködőjének** vagy az **Azure-előfizetés létrehozójának** kell lennie. További információkért lásd [az előfizetés számlázási szerepköreit és azok feladatait](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks). Ha engedélyt szeretne adni másoknak arra, hogy Azure-előfizetéseket hozzanak létre a számlázási fiókjában, tekintse meg az [Azure-előfizetések létrehozására szolgáló engedélyek másoknak történő biztosítását](#give-others-permission) ismertető cikket.

## <a name="create-a-subscription"></a>Előfizetés létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá az **Előfizetések** kifejezésre.

   ![Képernyőkép az „előfizetések” kifejezés a portálon történő kereséséről](./media/billing-mca-create-subscription/billing-search-subscriptions.png)

3. Válassza a **Hozzáadás** lehetőséget

4. Ha több számlázási fiókhoz is hozzáféréssel rendelkezik, válassza ki a Microsoft-ügyfélszerződéshez tartozó számlázási fiókot.

   ![Képernyőkép az előfizetés-létrehozási lapról](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. Válasszon ki egy számlázási profilt. Az előfizetés díjai a kiválasztott számlázási profilra lesznek kiszámlázva. Ha csak egy előfizetéshez rendelkezik hozzáféréssel, a választási lehetőség ki van szürkítve.

6. Válasszon ki egy számlázási szakaszt. Az előfizetés díjai a számlázási profil e számlázási szakaszára lesznek számlázva. Ha csak egy számlázási szakaszhoz rendelkezik hozzáféréssel, a választási lehetőség ki van szürkítve.

7. Válasszon ki egy csomagot az előfizetéshez. Ha az előfizetést fejlesztésre vagy számítási feladatok tesztelésére szeretné használni, válassza a **fejlesztéshez és teszteléshez készült Microsoft Azure-csomagot**, minden más esetben használja a **Microsoft Azure-csomag** lehetőséget. Ha csak egy csomaghoz rendelkezik hozzáféréssel, a választási lehetőség ki van szürkítve.

8. Adjon egy nevet az előfizetésnek. A név segítségével egyszerűen beazonosíthatja az előfizetést az Azure Portalon.

9. Kattintson a **Létrehozás** gombra.

## <a name="give-others-permission"></a>Engedély biztosítása másoknak

Ha szeretne engedélyt adni bizonyos felhasználóknak Azure-előfizetések létrehozására, hozzáadhatja őket az Azure-előfizetések létrehozóihoz a számlázási szakaszban.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az „előfizetések” kifejezés a portálon történő kereséséről](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Lépjen a számlaszakaszra. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot vagy számlázási profilt. A számlázási fiókon vagy profilon belül válassza a **Számlázási szakaszok** lehetőséget, majd válasszon ki egy számlázási szakaszt a listából. A felhasználók által létrehozott előfizetések a számlázási szakaszra lesznek kiszámlázva.
   
   ![Képernyőkép a számlázási szakaszok kiválasztásáról](./media/billing-mca-create-subscription/mca-select-invoice-sections.png)        

4. A bal oldal felső részén válassza a **Hozzáférés-kezelés (IAM)** lehetőséget.

5. Válassza a lap tetején a **Hozzáadás** gombot.

6. Válassza ki szerepkörként az **Azure-előfizetés létrehozója** lehetőséget.

7. Adja meg annak a felhasználónak az e-mail-címét, aki számára hozzáférést kíván biztosítani.

8. Kattintson a **Mentés** gombra.

## <a name="check-access"></a>Hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [Engedély biztosítása másoknak Azure-erőforrások beépített szerepkörökkel történő létrehozásához](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [Windows rendszerű virtuális gép létrehozása](../virtual-machines/windows/quick-create-portal.md)
- [Linux rendszerű virtuális gép létrehozása](../virtual-machines/linux/quick-create-portal.md)
- [Felügyeleti csoportok létrehozása az erőforrások rendszerezéséhez és kezeléséhez](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
