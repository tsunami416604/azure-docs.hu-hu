---
title: Az Ön számlázási fiókjához további Azure-előfizetés létrehozása
description: Ismerje meg az új Azure-előfizetés hozzáadása az Azure Portalon.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 558410d980d261780f7287d1e27ed704b356fc2b
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490946"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>További Azure-előfizetés létrehozása a Microsoft ügyfél-szerződés

Hozzon létre külön környezeteket, fejlesztési és tesztelési, biztonsági vagy megfelelőségi okokból adatait elszigetelni egy számlázási fiókjához további előfizetéseket.

Ez a cikk a Microsoft vevői szerződés számlázási fiók vonatkozik. [Ellenőrizze, hogy egy Microsoft Ügyfélszerződéséhez hozzáférést](#check-access). Ha szeretne létrehozni a számlázási fiókok más típusú előfizetések, tekintse meg [egy kiegészítő előfizetés létrehozása az Azure Portalon](billing-create-subscription.md).

Hozzon létre egy előfizetést, kell lennie egy **számla szakasz tulajdonosa**, **számla szakasz közreműködői**, vagy **Azure-előfizetés létrehozója**. További információkért lásd: [előfizetés számlázási szerepkörök és feladatok](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks). Ahhoz, hogy más Azure-előfizetések Számlázási-fiókja létrehozásához szükséges engedéllyel, lásd: [engedélyezze a más Azure-előfizetések létrehozására](#give-others-permission).

## <a name="create-a-subscription"></a>Előfizetés létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg **előfizetések**.

   ![Képernyőkép a Keresés az előfizetések a portálon](./media/billing-mca-create-subscription/billing-search-subscriptions.png)

3. Válassza ki **hozzáadása**

4. Ha több számlázási fiók hozzáféréssel rendelkezik, válassza ki a Microsoft ügyfél-szerződéshez tartozó számlázási fiókjához.

   ![Képernyőkép a előfizetés-lap létrehozása](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. Válassza ki a számlázási profilt. Az előfizetési díjai a kiválasztott számlázási profilja, számlázunk ki. Ha csak egy számlázási profilja hozzáféréssel rendelkezik, a kijelölt szürkén jelennek meg.

6. Válassza ki a számlát szakaszt. Az előfizetési díjai ebben a szakaszban a számlázási profilja számla, számlázunk ki. Ha rendelkezik hozzáféréssel egyetlen számlán szakaszra, a kijelölt szürkén jelennek meg.

7. Válassza ki az előfizetés díjcsomagot. Válassza ki **a Microsoft Azure-csomagot DevTest**, ha azt tervezi, fejlesztési célra ez az előfizetés vagy más tesztelési számítási feladatokat **a Microsoft Azure csomag**. Ha rendelkezik hozzáféréssel egyetlen csomagra, a kijelölt szürkén jelennek meg.

8. Adjon egy nevet az előfizetésnek. A név segítségével könnyen azonosíthatja azokat az előfizetés az Azure Portalon.

9. Kattintson a **Létrehozás** gombra.

## <a name="give-others-permission"></a>Engedélyezheti az mások számára

Adja hozzá a felhasználókat, az Azure-előfizetés és közös kiindulópontként szolgálhat egy számla szakaszban tegye lehetővé számukra az Azure-előfizetések létrehozásához szükséges engedéllyel.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg **Cost Management és számlázás**.

   ![Képernyőkép a Keresés az előfizetések a portálon](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Lépjen a számlaszakaszra. A hozzáférést, attól függően szükség lehet jelöljön ki egy számlázási fiók vagy a számlázási profilja. A számlázási fiók vagy a profil, válassza ki a **szakaszok számlázása** és majd a listából egy számla szakaszban. Ez a szakasz számla jön létre a felhasználók által foglalt olyan előfizetéseket díjköteles.
   
   ![Képernyőkép a kiválasztásával szakaszok számlázása](./media/billing-mca-create-subscription/mca-select-invoice-sections.png)        

4. Válassza ki **hozzáférés-kezelés (IAM)** bal felső oldalán.

5. Válassza a lap tetején a **Hozzáadás** gombot.

6. Válassza ki **Azure-előfizetés létrehozója** szerepkörhöz.

7. Adja meg, akiknek hozzáférést szeretne a felhasználó e-mail-címét.

8. Kattintson a **Mentés** gombra.

## <a name="check-access"></a>Hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [Engedélyezze a többi beépített szerepkörök használatával Azure-erőforrások létrehozása](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [Windows rendszerű virtuális gép létrehozása](../virtual-machines/windows/quick-create-portal.md)
- [Linux rendszerű virtuális gép létrehozása](../virtual-machines/linux/quick-create-portal.md)
- [Erőforrás-szervezet és felügyeleti felügyeleti csoportok létrehozása](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
