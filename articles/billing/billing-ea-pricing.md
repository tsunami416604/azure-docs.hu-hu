---
title: A cég vagy intézmény Azure-díjszabásának megtekintése és letöltése
description: Megtudhatja, hogyan tekintheti meg és töltheti le a szervezete díjszabását vagy becsült költségeit.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: a7f7da197a06dbbb730a7386882e534b8381cf9e
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491351"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>A cég vagy intézmény Azure-díjszabásának megtekintése és letöltése

Az Azure Nagyvállalati Szerződéssel (EA) vagy [Microsoft-ügyfélszerződéssel](#check-your-access-to-a-microsoft-customer-agreement) rendelkező Azure-ügyfelek az Azure Portalon tekinthetik meg és tölthetik le a rájuk érvényes díjszabást.

## <a name="ea-pricing"></a>EA-díjszabás

A vállalati rendszergazda által a szervezet számára beállított szabályzatoktól függően csak bizonyos felügyeleti szerepkörök biztosítanak hozzáférést a szervezet EA-díjszabási adataihoz. További információkért lásd [az Azure Nagyvállalati Szerződés Azure-beli felügyeleti szerepköreinek ismertetését](billing-understand-ea-roles.md).

1. Jelentkezzen be vállalati rendszergazdaként az [Azure Portalra](https://portal.azure.com/).
1. Keressen rá a *Költségkezelés + számlázás* kifejezésre.

   ![Képernyőkép az Azure Portal keresőmezőjéről](./media/billing-ea-pricing/portal-cm-billing-search.png)

1. A számlázási fiókon belül válassza ki a **Felhasználás + díjak** lehetőséget.

   ![Képernyőkép a Számlázás területen szereplő használati adatokról és díjakról](./media/billing-ea-pricing/ea-pricing-usage-charges-nav.png)

1. Válassza a ![Képernyőkép az Azure Portal keresőmezőjéről](./media/billing-ea-pricing/download-icon.png) **Letöltés** lehetőséget az adott hónapban.

1. Az **Árlistán** belül válassza a **CSV letöltésére** szolgáló elemet.

   ![Képernyőkép az Árlistán belüli, a CSV letöltésére szolgáló gombról](./media/billing-ea-pricing/download-ea-price-sheet.png)

## <a name="microsoft-customer-agreement-pricing"></a>A Microsoft-ügyfélszerződés díjszabása

A díjszabás megtekintéséhez és letöltéséhez a számlázási profil tulajdonosának, közreműködőjének, olvasójának, illetve számlakezelőjének kell lennie. A Microsoft-ügyfélszerződések számlázási szerepköreivel kapcsolatos további információért lásd a [számlázási profil szerepköreit és feladatait](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="download-price-sheets-for-the-current-billing-period"></a>Az aktuális számlázási időszak árlistáinak letöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá a *Költségkezelés + számlázás* kifejezésre.
1. Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy először ki kell választania egy számlázási fiókot.
1. Az **Áttekintés** területen keresse meg a letöltési hivatkozásokat az aktuális havi díjak alatt.
1. Válassza az **Azure-árlista** lehetőséget.
![Képernyőkép az Áttekintés területen található letöltési lehetőségről](./media/billing-ea-pricing/open-pricing.png)

### <a name="download-price-sheets-for-billed-charges"></a>A számlázott díjak árlistáinak letöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá a *Költségkezelés + számlázás* kifejezésre.
1. Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy először ki kell választania egy számlázási fiókot.
1. Válassza ki a **Számlák** elemet.
1. A számlák rácsos elrendezésében keresse meg a letölteni kívánt árlistának megfelelő számla sorát.
1. Kattintson a sor végén található három pontra (`...`).
![Képernyőkép a kiválasztott három pontról](./media/billing-ea-pricing/billingprofile-invoicegrid.png)

1. Ha a kiválasztott számlán szereplő szolgáltatások árát szeretné megtekinteni, válassza a **Számla árlistája** lehetőséget.
1. Ha az adott számlázási időszakban használt összes Azure-szolgáltatás árát szeretné megtekinteni, válassza az **Azure-árlista** lehetőséget.

![Képernyőkép az árlistákat tartalmazó helyi menüről](./media/billing-ea-pricing/contextmenu-pricesheet.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Költségek kiszámítása az Azure-díjkalkulátorral

A szervezete díjszabásának használatával megbecsülheti a költségeket az Azure-díjkalkulátorral.

1. Lépjen az [Azure-díjkalkulátor](https://azure.microsoft.com/pricing/calculator) lapjára.
1. A jobb felső részen válassza a **Bejelentkezés** elemet.
1. A **Programok és ajánlat** > **Licencprogram** alatt válassza a **Nagyvállalati Szerződés (EA)** lehetőséget.
1. A **Programok és ajánlat** > **Kiválasztott szerződés** alatt válassza az **Egy elem sincs kijelölve** lehetőséget.

    ![Képernyőkép az Árlistán belüli, a CSV letöltésére szolgáló gombról](./media/billing-ea-pricing/ea-pricing-calculator-estimate.png)

1. Válassza ki a szervezetet.
1. Kattintson az **Alkalmaz** gombra.
1. Keresse meg a számításhoz használni kívánt termékeket, majd adja hozzá azokat.
1. A megjelenített becsült összegek a kiválasztott szervezet díjszabásán alapulnak.

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>További lépések

Ha Ön EA-ügyfél, tekintse meg a következő cikkeket:

- [A Nagyvállalati Szerződés számlázási adataihoz való hozzáférés kezelése](billing-manage-access.md)
- [A Microsoft Azure-számla megtekintése és letöltése](billing-download-azure-invoice.md)
- [A Microsoft Azure használati adatainak és díjainak megtekintése és letöltése](billing-download-azure-daily-usage.md)
- [A számla értelmezése Nagyvállalati Szerződéssel rendelkező ügyfelek számára](billing-understand-your-bill-ea.md)

Ha Microsoft-ügyfélszerződéssel rendelkezik, tekintse meg a következő cikkeket:

- [A Microsoft-ügyfélszerződés árlistáján szereplő kifejezések értelmezése](billing-mca-understand-pricesheet.md)
- [A Microsoft Azure-számla megtekintése és letöltése](billing-download-azure-invoice.md)
- [A Microsoft Azure használati adatainak és díjainak megtekintése és letöltése](billing-download-azure-daily-usage.md)
- [A számlázási profil adózási dokumentumainak megtekintése és letöltése](billing-mca-download-tax-document.md)
- [A számlázási profil számláján szereplő díjak ismertetése](billing-mca-understand-your-bill.md)
