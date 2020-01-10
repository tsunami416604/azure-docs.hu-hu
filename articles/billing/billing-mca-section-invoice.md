---
title: Számlaszakaszok létrehozása a költségek rendszerezéséhez – Azure
description: Megtudhatja, hogyan rendszerezheti költségeit számlázási szakaszokkal.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ff8b2da353d623cd9f05c8d0b0317587d7093ce3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75389281"
---
# <a name="create-sections-on-your-invoice-to-organize-your-costs"></a>Szakaszok létrehozása a számlán a költségek rendszerezéséhez

Szakaszokat hozhat létre a számlán a költségek részlegek, fejlesztési környezet vagy a szervezet igényei alapján történő rendszerezéséhez. Ezután engedélyt adhat másoknak a szakaszon keresztül kiszámlázott Azure-előfizetések létrehozására. Ezután az előfizetések használati díjainak és vásárlásainak számlázása az adott szakaszra történik. A számlázási szakasz összes költségét megtekintheti az Azure Portalon, vagy áttekintheti az Azure költségelemzési szolgáltatásában. További információkért lásd [az egyes számlázási szakaszok tranzakcióinak megtekintését](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections) ismertető szakaszt.

Ez a cikk a Microsoft-ügyfélszerződéshez tartozó számlázási fiókokra vonatkozik. [Ellenőrizze, hogy rendelkezik-e hozzáféréssel egy Microsoft-ügyfélszerződéshez](#check-access-to-a-microsoft-customer-agreement).

## <a name="create-an-invoice-section-in-the-azure-portal"></a>Számlázási szakasz létrehozása az Azure Portalon

Számlázási szakasz létrehozásához a **számlázási profil tulajdonosának** vagy a **számlázási profil közreműködőjének** kell lennie. További információkért lásd [a számlázási profil számlázási szakaszainak kezelését](billing-understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az Azure Portal keresőmezőjéről](./media/billing-mca-section-invoice/billing-search-cost-management-billing.png)

3. A bal oldali panelen válassza a **Számlázási szakaszok** lehetőséget. A hozzáférésétől függően előfordulhat, hogy csak egy számlázási profil vagy számlázási fiók kiválasztása után tudja kiválasztani a **Számlázási szakaszok** lehetőséget.

   ![Képernyőkép a számlázási szakaszok listájáról](./media/billing-mca-section-invoice/mca-select-invoice-sections.png)

4. Válassza a lap tetején a **Hozzáadás** gombot.

5. Adjon egy nevet a számlázási szakasznak, és válasszon ki egy számlázási profilt. Ekkor megjelenik a számlázási profil számlájának szakasza, amely a szakaszhoz rendelt egyes előfizetések és vásárlások használati adatait mutatja.

   ![Képernyőkép a számlázásiszakasz-létrehozási oldalról](./media/billing-mca-section-invoice/mca-create-invoice-section.png)

6. Kattintson a **Létrehozás** gombra.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

- [További Azure-előfizetés létrehozása a Microsoft-ügyfélszerződésben](billing-mca-create-subscription.md)
- [Számlázási szerepkörök kezelése az Azure Portalon](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Azure-előfizetések számlázási tulajdonjogának beszerzése más számlázási fiókok felhasználóitól](billing-mca-request-billing-ownership.md)
