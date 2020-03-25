---
title: A cég vagy intézmény Azure-díjszabásának megtekintése és letöltése
description: Megtudhatja, hogyan tekintheti meg és töltheti le a szervezete díjszabását vagy becsült költségeit.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 1dd0dfad0c68e18ae272d5d94fedd67d24be9ae1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77200778"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>A cég vagy intézmény Azure-díjszabásának megtekintése és letöltése

Az Azure Nagyvállalati Szerződéssel (EA), Microsoft-ügyfélszerződéssel (MCA) vagy Microsoft Partnerszerződéssel (MPA) rendelkező Azure-ügyfelek az Azure Portalon tekinthetik meg és tölthetik le a rájuk érvényes díjszabást. [További tudnivalók a számlázási fiók típusának megtekintéséről](#check-your-billing-account-type).

## <a name="download-pricing-for-an-enterprise-agreement"></a>Nagyvállalati Szerződés díjszabásának letöltése

A vállalati rendszergazda által a szervezet számára beállított szabályzatoktól függően csak bizonyos felügyeleti szerepkörök biztosítanak hozzáférést a szervezet EA-díjszabási adataihoz. További információkért lásd [az Azure Nagyvállalati Szerződés Azure-beli felügyeleti szerepköreinek ismertetését](understand-ea-roles.md).

1. Jelentkezzen be vállalati rendszergazdaként az [Azure Portalra](https://portal.azure.com/).
1. Keressen rá a *Költségkezelés + számlázás* kifejezésre.

   ![Képernyőkép az Azure Portal keresőmezőjéről](./media/ea-pricing/portal-cm-billing-search.png)

1. A számlázási fiókon belül válassza ki a **Felhasználás + díjak** lehetőséget.

   ![Képernyőkép a Számlázás területen szereplő használati adatokról és díjakról](./media/ea-pricing/ea-pricing-usage-charges-nav.png)

1. Válassza a ![Képernyőkép az Azure Portal keresőmezőjéről](./media/ea-pricing/download-icon.png) **Letöltés** lehetőséget az adott hónapban.

1. Az **Árlistán** belül válassza a **CSV letöltésére** szolgáló elemet.

   ![Képernyőkép az Árlistán belüli, a CSV letöltésére szolgáló gombról](./media/ea-pricing/download-ea-price-sheet.png)

## <a name="download-pricing-for-an-mca-or-mpa-account"></a>MCA- vagy MPA-fiók díjszabásának letöltése

Ha MCA-val rendelkezik, a díjszabás megtekintéséhez és letöltéséhez a számlázási profil tulajdonosának, közreműködőjének, olvasójának vagy számlakezelőjének kell lennie. Ha MPA-val rendelkezik, a partnerszervezet globális rendszergazdai és rendszergazdai ügynök szerepkörével kell rendelkeznie a díjszabás megtekintéséhez és letöltéséhez.

### <a name="download-price-sheets-for-billed-charges"></a>A számlázott díjak árlistáinak letöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá a *Költségkezelés + számlázás* kifejezésre.
1. Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy először ki kell választania egy számlázási fiókot.
1. Válassza ki a **Számlák** elemet.
1. A számlák rácsos elrendezésében keresse meg a letölteni kívánt árlistának megfelelő számla sorát.
1. Kattintson a sor végén található három pontra (`...`).
![Képernyőkép a kiválasztott három pontról](./media/ea-pricing/billingprofile-invoicegrid-new.png)

1. Ha a kiválasztott számlán szereplő szolgáltatások árát szeretné megtekinteni, válassza a **Számla árlistája** lehetőséget.
1. Ha az adott számlázási időszakban használt összes Azure-szolgáltatás árát szeretné megtekinteni, válassza az **Azure-árlista** lehetőséget.

![Képernyőkép az árlistákat tartalmazó helyi menüről](./media/ea-pricing/contextmenu-pricesheet01.png)

### <a name="download-price-sheets-for-the-current-billing-period"></a>Az aktuális számlázási időszak árlistáinak letöltése

Ha rendelkezik MCA-val, letöltheti az aktuális számlázási időszakra vonatkozó díjszabást.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá a *Költségkezelés + számlázás* kifejezésre.
1. Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy először ki kell választania egy számlázási fiókot.
1. Az **Áttekintés** területen keresse meg a letöltési hivatkozásokat az aktuális havi díjak alatt.
1. Válassza az **Azure-árlista** lehetőséget.
![Képernyőkép az Áttekintés területen található letöltési lehetőségről](./media/ea-pricing/open-pricing01.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Költségek kiszámítása az Azure-díjkalkulátorral

A szervezete díjszabásának használatával megbecsülheti a költségeket az Azure-díjkalkulátorral.

1. Lépjen az [Azure-díjkalkulátor](https://azure.microsoft.com/pricing/calculator) lapjára.
1. A jobb felső részen válassza a **Bejelentkezés** elemet.
1. A **Programok és ajánlat** > **Licencprogram** alatt válassza a **Nagyvállalati Szerződés (EA)** lehetőséget.
1. A **Programok és ajánlat** > **Kiválasztott szerződés** alatt válassza az **Egy elem sincs kijelölve** lehetőséget.

    ![Képernyőkép az Árlistán belüli, a CSV letöltésére szolgáló gombról](./media/ea-pricing/ea-pricing-calculator-estimate.png)

1. Válassza ki a szervezetet.
1. Kattintson az **Alkalmaz** gombra.
1. Keresse meg a számításhoz használni kívánt termékeket, majd adja hozzá azokat.
1. A megjelenített becsült összegek a kiválasztott szervezet díjszabásán alapulnak.

## <a name="check-your-billing-account-type"></a>A számlázási fiók típusának ellenőrzése
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Következő lépések

Ha Ön EA-ügyfél, tekintse meg a következő cikkeket:

- [A Nagyvállalati Szerződés számlázási adataihoz való hozzáférés kezelése](manage-billing-access.md)
- [A Microsoft Azure-számla megtekintése és letöltése](../understand/download-azure-invoice.md)
- [A Microsoft Azure használati adatainak és díjainak megtekintése és letöltése](../understand/download-azure-daily-usage.md)
- [A számla értelmezése Nagyvállalati Szerződéssel rendelkező ügyfelek számára](../understand/review-enterprise-agreement-bill.md)

Ha Microsoft-ügyfélszerződéssel rendelkezik, tekintse meg a következő cikkeket:

- [A Microsoft-ügyfélszerződés árlistáján szereplő kifejezések értelmezése](mca-understand-pricesheet.md)
- [A Microsoft Azure-számla megtekintése és letöltése](../understand/download-azure-invoice.md)
- [A Microsoft Azure használati adatainak és díjainak megtekintése és letöltése](../understand/download-azure-daily-usage.md)
- [A számlázási profil adózási dokumentumainak megtekintése és letöltése](../understand/mca-download-tax-document.md)
- [A számlázási profil számláján szereplő díjak ismertetése](../understand/review-customer-agreement-bill.md)
