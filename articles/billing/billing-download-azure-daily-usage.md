---
title: Az Azure használati adatainak és díjainak megtekintése és letöltése
description: A cikk azt ismerteti, hogyan töltheti le vagy tekintheti meg az Azure használati adatait és díjait.
keywords: számlázási használat,használati díjak,használat letöltése,használat megtekintése,azure számla,azure használat
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
ms.openlocfilehash: 7d2d7be562eaaa7dd21e63735f5697ffe5a62f8a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491447"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Az Azure használati adatainak és díjainak megtekintése és letöltése

Amennyiben Ön EA-ügyfél, vagy rendelkezik [Microsoft-ügyfélszerződéssel](#check-your-access-to-a-microsoft-customer-agreement), az Azure használati adatait és a díjakat letöltheti az [Azure Portal](https://portal.azure.com/) felületéről. Ha más előfizetéssel rendelkezik, lépjen az [Azure Fiókközpontba](https://account.azure.com/Subscriptions) a használati adatok letöltéséhez.

Csak egyes szerepkörök, például a fiókadminisztrátorok és a vállalati rendszergazdák rendelkeznek engedéllyel az Azure-használattal kapcsolatos adatok lekérdezéséhez. További információ a számlázási információhoz való hozzáférésről: [Az Azure-beli számlázáshoz való hozzáférés kezelése szerepkörök használatával](billing-manage-access.md).

Ha rendelkezik [Microsoft-ügyfélszerződéssel](#check-your-access-to-a-microsoft-customer-agreement), számlázási profil tulajdonosának, közreműködőjének, olvasójának vagy számlakezelőjének kell lennie az Azure-használattal és a díjakkal kapcsolatos adatok megtekintéséhez. A Microsoft-ügyfélszerződések számlázási szerepköreivel kapcsolatos további információért lásd a [számlázási profil szerepköreit és feladatait](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="download-usage-from-the-account-center-csv"></a>Használati adatok letöltése a Fiókközpontból (.csv)

1. Jelentkezzen be fiókadminisztrátorként az [Azure Fiókközpontba](https://account.windowsazure.com/subscriptions).

2. Válassza ki azt az előfizetést, amelynek a számláját és a használattal kapcsolatos adatait le szeretné kérni.

3. Válassza a **SZÁMLATÖRTÉNET** lehetőséget.

    ![A számlatörténet lehetőséget megjelenítő képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Megtekintheti az utolsó hat számlázási időszakra és az aktuális, nem számlázott időszakra vonatkozó kivonatokat.

    ![A számlázási időszakokat, a számlák és napi használati adatok letöltési lehetőségeit és az egyes számlázási időszakok teljes díját megjelenítő képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Válassza az **Aktuális számla megtekintése** lehetőséget a költségek azon becslésének megtekintéséhez, amely a becslés létrehozásakor jött létre. A becslések csak naponta egyszer frissülnek, ezért nem feltétlenül tartalmazzák az összes használati adatot. A havi számlája eltérhet ettől a becsléstől.

    ![Az aktuális kivonat megtekintésére szolgáló lehetőséget megjelenítő képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![A becsült jelenlegi költségeket megjelenítő képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Válassza a **Használati adatok letöltése** lehetőséget a napi használati adatok letöltéséhez CSV-fájlként. Ha két elérhető verziót lát, a 2. verziót töltse le.

    ![A használati adatok letöltésére szolgáló lehetőséget megjelenítő képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Csak a fiókadminisztrátor férhet hozzá az Azure Fiókközponthoz. A többi számlázási adminisztrátor, például a tulajdonosok a [Billing API-k](billing-usage-rate-card-overview.md) segítségével kaphatják meg a használati adatokat.

A napi használati adatokra vonatkozó további információért lásd: [Microsoft Azure számlájának megértése](billing-understand-your-bill.md). A költségek kezelésével kapcsolatos segítségért lásd [az Azure-számlázással és -költségkezeléssel kapcsolatos váratlan költségek megelőzését](billing-getting-started.md) ismertető szakaszt.

## <a name="download-usage-for-ea-customers"></a>Használati adatok letöltése EA-ügyfelek esetén

A használati adatok EA-ügyfélként történő megtekintéséhez és letöltéséhez vállalati rendszergazdának, fióktulajdonosnak vagy részlegszintű rendszergazdának kell lennie, és engedélyezni kell a költségek megtekintésére vonatkozó szabályzatot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá a *Költségkezelés + számlázás* kifejezésre.

    ![Képernyőkép az Azure Portal keresőmezőjéről](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Válassza a **Felhasználás + díjak** lehetőséget.
1. Válassza ki a **Letöltés** elemet a letölteni kívánt hónapnál.

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>A Microsoft-ügyfélszerződéshez tartozó használati adatok letöltése

Ha rendelkezik Microsoft-ügyfélszerződéssel, az Azure-beli használati adatait és díjait a számlázási profiljából töltheti le. Az Azure-használati adatok és díjak CSV-fájljának az Azure Portalról történő letöltéséhez a számlázási profil tulajdonosának, közreműködőjének, olvasójának, illetve számlakezelőjének kell lennie.

### <a name="download-usage-for-billed-charges"></a>Használati adatok letöltése kiszámlázott díjak esetén

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Keressen rá a *Költségkezelés + számlázás* kifejezésre.
3. Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy először ki kell választania egy számlázási fiókot.
4. Válassza ki a **Számlák** elemet.
5. A számlák rácsos elrendezésében keresse meg a letölteni kívánt használati adatnak megfelelő számla sorát.
6. Kattintson a sor végén található három pontra (`...`).

    ![A sor végén található három pontot megjelenítő képernyőkép](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. A helyi letöltési menüben válassza az **Azure-használat és -díjak** lehetőséget.

     ![A kiválasztott Azure-beli használati fájlokat és díjakat megjelenítő képernyőkép](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>Használati adatok letöltése függőben lévő díjak esetén

Letöltheti az aktuális hónap a jelenlegi számlázási időszakra vonatkozó használati adatait is. Ezek a használati díjak még nem lettek kiszámlázva.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Keressen rá a *Költségkezelés + számlázás* kifejezésre.
3. Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy először ki kell választania egy számlázási fiókot.
4. Az **Áttekintés** területen keresse meg a letöltési hivatkozásokat az aktuális havi díjak alatt.
5. Válassza ki az **Azure-használat és -díjak** elemet.

    ![Képernyőkép az Áttekintés területen található letöltési lehetőségről](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

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
