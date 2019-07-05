---
title: A számla a költségeket – Azure rendszerezéséhez szakaszok létrehozása
description: Ismerje meg, a számla szakaszok költségeket rendszerezéséhez.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.topic: conceptual
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: eadaf34dc5bdd93af532362e8f8542de3f17f414
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490782"
---
# <a name="create-sections-on-your-invoice-to-organize-your-costs"></a>A számla a költségek rendszerezéséhez szakaszok létrehozása

A számla egy részleg, a fejlesztési környezetet, a költségek rendszerezéséhez szakaszok létrehozása vagy a szervezet igényeinek megfelelően. Ezután engedélyt mások hozhat létre Azure-előfizetések számlázása a szakaszra. A használati díjak és a vásárlások az előfizetések majd számlázása a szakaszra. Megtekintheti a szakasz összes díjai a számlán, az Azure Portalon vagy tekintsék meg őket az Azure cost analysis. További információkért lásd: [tranzakciók számla szakaszonként megjelenítéséhez](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

Ez a cikk a Microsoft vevői szerződés számlázási fiók vonatkozik. [Ellenőrizze, hogy egy Microsoft Ügyfélszerződéséhez hozzáférést](#check-access-to-a-microsoft-customer-agreement).

## <a name="create-an-invoice-section-in-the-azure-portal"></a>Hozzon létre egy számla szakaszt az Azure Portalon

Hozzon létre egy számla szakaszt, lennie kell egy **számlázási profiltulajdonossal** vagy egy **számlázási profil közreműködője**. További információkért lásd: [számla szakaszok számlázási profil kezelése](billing-understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg **Cost Management és számlázás**.

   ![Az Azure portál keresési bemutató képernyőkép](./media/billing-mca-section-invoice/billing-search-cost-management-billing.png)

3. Válassza ki **szakaszok számlázása** a bal oldali panelen. A hozzáférést, attól függően szükség lehet egy számlázási profilja vagy számlázási fiók válassza, majd **szakaszok számlázása**.

   ![Képernyőkép a számla szakasz listája](./media/billing-mca-section-invoice/mca-select-invoice-sections.png)

4. Válassza a lap tetején a **Hozzáadás** gombot.

5. Adjon meg egy nevet a számla szakaszban, és válasszon ki egy számlázási profilt. Látni fogja a szakasz a számlázási profilja számlán tükröző minden egyes előfizetés és a vásárlások társított a szakasz a használatát. 

   ![Képernyőkép a számla szakasz létrehozása lap](./media/billing-mca-section-invoice/mca-create-invoice-section.png)

6. Kattintson a **Létrehozás** gombra.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>A Microsoft Ügyfélszerződéséhez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha segítségre van szüksége, kell [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

- [További Azure-előfizetés létrehozása a Microsoft ügyfél-szerződés](billing-mca-create-subscription.md)
- [Engedélyezze a más Azure-előfizetés létrehozása](billing-mca-create-subscription.md#give-others-permission)
- [Az Azure-előfizetések számlázási tulajdonjogának kaphat a számlázási fiókok más felhasználóktól](billing-mca-request-billing-ownership.md)
