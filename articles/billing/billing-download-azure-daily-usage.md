---
title: Az Azure használati adatainak és díjainak megtekintése és letöltése
description: A cikk azt ismerteti, hogyan töltheti le vagy tekintheti meg az Azure használati adatait és díjait.
keywords: számlázási használat, használati díjak, használat letöltése, használat megtekintése, azure számla, azure használat
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: dcf4e3b9d85909c8f1d149c9d1940a6755b431a1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224011"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Az Azure használati adatainak és díjainak megtekintése és letöltése

Letöltheti az Azure-beli használati adatok és költségek napi bontását az Azure Portalon. Csak egyes szerepkörök, például a fiókadminisztrátorok és a vállalati rendszergazdák rendelkeznek engedéllyel az Azure-használattal kapcsolatos adatok lekérdezéséhez. További információ a számlázási információhoz való hozzáférésről: [Az Azure-beli számlázáshoz való hozzáférés kezelése szerepkörök használatával](billing-manage-access.md).

Ha rendelkezik Microsoft-ügyfélszerződéssel (MCA), számlázási profil tulajdonosának, közreműködőjének, olvasójának vagy számlakezelőjének kell lennie az Azure-használattal és a díjakkal kapcsolatos adatok megtekintéséhez.  Ha Microsoft-partnerszerződéssel (MPA) rendelkezik, csak a partnerszervezet globális rendszergazdája és rendszergazdai ügynöke tekintheti meg és töltheti le az Azure-beli használati adatokat és díjakat. [A számlázási fiók típusának ellenőrzése az Azure Portalon](#check-your-billing-account-type).

## <a name="download-usage-from-the-azure-portal-csv"></a>Használati adatok letöltése az Azure Portalról (.csv)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá a *Költségkezelés + számlázás* kifejezésre.

    ![Képernyőkép az Azure Portal keresőmezőjéről](./media/billing-download-azure-usage/portal-cm-billing-search.png)

1. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot vagy számlázási profilt.
1. A bal oldali menüben válassza a **Számlázás** területen lévő **Számlák** elemet.
1. A számlák rácsos elrendezésében keresse meg a letölteni kívánt használati adatnak megfelelő számlázási időszak sorát.
1. Kattintson a jobb oldalon található letöltés ikonra vagy három pontra (`...`).
1. Válassza ki az **Azure használati adatainak és díjainak letöltése** lehetőséget a letöltési menüből.

## <a name="download-usage-for-ea-customers"></a>Használati adatok letöltése EA-ügyfelek esetén

A használati adatok EA-ügyfélként történő megtekintéséhez és letöltéséhez vállalati rendszergazdának, fióktulajdonosnak vagy részlegszintű rendszergazdának kell lennie, és engedélyezni kell a költségek megtekintésére vonatkozó szabályzatot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá a *Költségkezelés + számlázás* kifejezésre.

    ![Képernyőkép az Azure Portal keresőmezőjéről](./media/billing-download-azure-usage/portal-cm-billing-search.png)

1. Válassza a **Felhasználás + díjak** lehetőséget.
1. Válassza ki a **Letöltés** elemet a letölteni kívánt hónapnál.

## <a name="download-usage-for-pending-charges"></a>Használati adatok letöltése függőben lévő díjak esetén

Ha rendelkezik Microsoft-ügyfélszerződéssel, letöltheti az aktuális hónap a jelenlegi számlázási időszakra vonatkozó használati adatait. Ezek a használati díjak még nem lettek kiszámlázva.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Keressen rá a *Költségkezelés + számlázás* kifejezésre.
3. Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy először ki kell választania egy számlázási fiókot.
4. Az **Áttekintés** területen keresse meg a letöltési hivatkozásokat az aktuális havi díjak alatt.
5. Válassza ki az **Azure-használat és -díjak** elemet.

    ![Képernyőkép az Áttekintés területen található letöltési lehetőségről](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-billing-account-type"></a>A számlázási fiók típusának ellenőrzése
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

A számlájával és a használati díjakkal kapcsolatos további információkért lásd:

- [A Microsoft Azure részletes használati feltételeinek értelmezése](billing-understand-your-usage.md)
- [Microsoft Azure-elszámolások értelmezése](billing-understand-your-bill.md)
- [A Microsoft Azure-számla megtekintése és letöltése](billing-download-azure-invoice.md)
- [A cég vagy intézmény Azure-díjszabásának megtekintése és letöltése](billing-ea-pricing.md)

Ha Microsoft-ügyfélszerződéssel rendelkezik, tekintse meg a következő cikkeket:

- [A Microsoft-ügyfélszerződéshez tartozó részletes Azure-használati feltételek ismertetése](billing-mca-understand-your-usage.md)
- [A Microsoft-ügyfélszerződés számláján szereplő díjak ismertetése](billing-mca-understand-your-bill.md)
- [A Microsoft Azure-számla megtekintése és letöltése](billing-download-azure-invoice.md)
- [A Microsoft-ügyfélszerződés adózási dokumentumainak megtekintése és letöltése](billing-mca-download-tax-document.md)
- [A cég vagy intézmény Azure-díjszabásának megtekintése és letöltése](billing-ea-pricing.md)
