---
title: Megtekintheti és letöltheti a szervezet az Azure díjszabása |} A Microsoft Docs
description: Útmutató megtekintése és letöltése a díjszabás vagy végezzen költségbecslést a szervezet díjszabása.
services: ''
documentationcenter: ''
author: adpick
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 88d94492cb6ed3191e146f117a648293bc9e50f9
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243720"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>Megtekintheti és letöltheti a szervezet az Azure díjszabása

Az Azure-ügyfelek az egy nagyvállalati szerződés (EA) vagy [Microsoft Ügyfélszerződéséhez](#check-your-access-to-a-microsoft-customer-agreement) előfordulhat, hogy megtekintése és letöltése a díjszabás az Azure Portalról. Ha a Microsoft vevői szerződéssel rendelkezik, tekintse meg [megtekintése és letöltése a Microsoft vevői szerződés díjszabása](#View-and-download-pricing-for-your-billing-profile).

## <a name="view-and-download-ea-pricing"></a>Megtekintheti és letöltheti a nagyvállalati szerződés díjszabása

A szervezet számára a vállalati rendszergazda által megadott házirendtől, függően egyes felügyeleti szerepkörök a szervezet nagyvállalati szerződéssel rendelkező díjszabási információit hozzáférést biztosítanak. További információkért lásd: [rendszergazdai szerepkörök az Azure nagyvállalati szerződés ismertetése az Azure-ban](billing-understand-ea-roles.md).

1. Vállalati rendszergazdaként jelentkezzen be a [az Azure portal](https://portal.azure.com/).
1. A Keresés **Cost Management és számlázás**.

   ![Az Azure portál keresési bemutató képernyőkép](./media/billing-ea-pricing/portal-cm-billing-search.png)

1. Válassza ki a számlázási fiók alatt **használat és költségek**.

   ![Képernyőkép a használati és számlázási vonhatnak maguk után](./media/billing-ea-pricing/ea-pricing-usage-charges-nav.png)

1. Válassza ki ![Képernyőkép az Azure portál keresési](./media/billing-ea-pricing/download-icon.png) **letöltése** hónapban.
1. A **árlista**válassza **csv letöltése**.

   ![Az árlista képernyőkép letöltése csv-gomb](./media/billing-ea-pricing/download-ea-price-sheet.png)

## <a name="view-and-download-pricing-for-your-microsoft-customer-agreement"></a>Megtekintheti és letöltheti a Microsoft vevői szerződés díjszabása

A számlázási profil tulajdonos, közreműködő, olvasó vagy számla manager megtekintése és letöltése a díjszabás kell lennie. További számlázási szerepkörökkel kapcsolatban a Microsoft ügyfél-megállapodások esetén, lásd: [számlázási profil szerepkörök és feladatok](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="download-price-sheets-for-the-current-billing-period"></a>Töltse le az árlisták az aktuális elszámolási időszakban

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A Keresés **Cost Management és számlázás**.
1. Válassza ki a számlázási profilt. A hozzáférést, attól függően szükség lehet először ki kell választania egy számlázási fiókot.
1. Az a **áttekintése** panelen keresse meg a letöltési hivatkozások a hónap elejétől számított díjakat alatt.
1. Válassza ki **Azure árlista**.
![Képernyőkép a letöltési az áttekintésből](./media/billing-ea-pricing/open-pricing.png)

### <a name="download-price-sheets-for-billed-charges"></a>Töltse le az árlisták számlázott díjak

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A Keresés **Cost Management és számlázás**.
1. Válassza ki a számlázási profilt. A hozzáférést, attól függően szükség lehet először ki kell választania egy számlázási fiókot.
1. Válassza ki **számlákat**.
1. A számla rács megkeresi a letölteni kívánt árlistát tartozó számla.
1. Kattintson a három (`...`) a sor végén található.
![A három pontot kiválasztva bemutató képernyőkép](./media/billing-ea-pricing/billingprofile-invoicegrid.png)

1. Ha azt szeretné, a szolgáltatások a kiválasztott számlán árait szeretné megtekinteni, válassza ki a **számla árlista**.
1. Ha Azure-szolgáltatásokhoz az adott elszámolási időszakban árakat, jelölje be **Azure árlista**.

![Helyi menü az árlisták bemutató képernyőkép](./media/billing-ea-pricing/contextmenu-pricesheet.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Becsült költség a az Azure díjkalkulátor

Is használhatja a szervezet díjszabása alapján az az Azure díjkalkulátorát.

1. Nyissa meg a [Azure díjkalkulátorát](https://azure.microsoft.com/pricing/calculator).
1. A jobb felső sarokban, válassza ki a **jelentkezzen be a**.
1. A **programok és ajánlat** > **licencelési Program**válassza **nagyvállalati szerződés (EA)**.
1. A **programok és ajánlat** > **kiválasztott szerződés**válassza **egy elem sincs kijelölve**.

    ![Az árlista képernyőkép letöltése csv-gomb](./media/billing-ea-pricing/ea-pricing-calculator-estimate.png)

1. Válassza ki a szervezetben.
1. Kattintson az **Alkalmaz** gombra.
1. Keresés és a termékek felvétele a becslésbe.
1. Becsült árak a kiválasztott szervezeti díjszabása alapulnak.

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>A Microsoft vevői Szerződéssel való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>További lépések

Ha Ön nagyvállalati szerződéssel rendelkező ügyfelek, lásd:

- [A nagyvállalati szerződés számlázási információk az Azure-hoz való hozzáférés kezelése](billing-manage-access.md)
- [Megtekintheti, és a Microsoft Azure-számla letöltése](billing-download-azure-invoice.md)
- [Megtekintheti és letöltheti a Microsoft Azure-használat és a díj](billing-download-azure-daily-usage.md)
- [Nagyvállalati szerződéssel rendelkező ügyfeleknek kapcsolódó számlák magyarázata](billing-understand-your-bill-ea.md)

Ha a Microsoft vevői szerződéssel rendelkezik, tekintse meg:

- [A Microsoft vevői szerződés feltételeit az árlista ismertetése](billing-mca-understand-pricesheet.md)
- [Megtekintheti, és a Microsoft Azure-számla letöltése](billing-download-azure-invoice.md)
- [Megtekintheti és letöltheti a Microsoft Azure-használat és a díj](billing-download-azure-daily-usage.md)
- [Megtekintheti, és a számlázási profiljához adó dokumentumok letöltése](billing-mca-download-tax-document.md)
- [A számlázási profilja számlán a költségek ismertetése](billing-mca-understand-your-bill.md)
