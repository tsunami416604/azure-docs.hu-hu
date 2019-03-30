---
title: Megtekintése és letöltése az Azure-használat és költségek |} A Microsoft Docs
description: Ismerteti, hogyan töltse le, vagy megtekintheti az Azure napi használat és költségek.
keywords: felhasználás számlázásának, használati díjak, használati, használati adatok megtekintése az azure-számla, az azure használati adatok letöltése
services: billing
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 520d3f6a45b44ba2023dee34642f796689f48221
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648846"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Megtekintése és letöltése az Azure-használat és költségek

Ha nagyvállalati szerződéssel rendelkező ügyfelek vagy rendelkezik egy [Microsoft Ügyfélszerződéséhez](#check-your-access-to-a-microsoft-customer-agreement), letöltheti az Azure-használat és a díjak a [az Azure portal](https://portal.azure.com/). Másik előfizetés esetén nyissa meg a [Azure Account Center](https://account.azure.com/Subscriptions) a használati adatok letöltése.

Csak egyes szerepkörök lekérése az Azure-használati adatok, például a a fiók rendszergazdája vagy vállalati rendszergazdai jogosultsága. További információ a számlázási információhoz való hozzáférésről: [Az Azure-beli számlázáshoz való hozzáférés kezelése szerepkörök használatával](billing-manage-access.md).

Ha rendelkezik egy [Microsoft Ügyfélszerződéséhez](#check-your-access-to-a-microsoft-customer-agreement), csak egy számlázási profilja tulajdonos, közreműködő, olvasó, és a számlához manager megtekintése az Azure-használat és költségek. További számlázási szerepkörökkel kapcsolatban a Microsoft ügyfél-megállapodások esetén, lásd: [számlázási profil szerepkörök és feladatok](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="download-usage-from-the-account-center-csv"></a>Az Account Center webhelyen (.csv) a használati adatok letöltése

1. Jelentkezzen be a [Azure Account Center](https://account.windowsazure.com/subscriptions) fiók rendszergazdaként.

2. Válassza ki az előfizetést, amelyek esetében szeretne a számlák és használati adatokat.

3. Válassza ki **BILLING HISTORY**.

    ![Képernyőkép a Számlázási előzmények lehetőséget](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Láthatja az utasítások az előző hat elszámolási időszakok és a jelenlegi elszámolási időszak díjadatai.

    ![Elszámolási időszakok, töltse le a számla és a napi használat és a teljes díj minden elszámolási időszakban lehetőségeket bemutató képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Válassza az **Aktuális számla megtekintése** lehetőséget a költségek azon becslésének megtekintéséhez, amely a becslés létrehozásakor jött létre. Ez az információ csak naponta frissíti, és minden használata nem feltétlenül tartalmazzák. A havi számla eltérhet ettől a becsléstől.

    ![A nézet jelenlegi utasítást beállítás bemutató képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![A becsült esedékes díjak bemutató képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Válassza a **Használati adatok letöltése** lehetőséget a napi használati adatok letöltéséhez CSV-fájlként. Ha két elérhető verziót lát, a 2. verziót töltse le.

    ![A használati adatok letöltése lehetőséget bemutató képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Csak a fiók rendszergazdája hozzáférhet az Azure fiókkezelési központjába. Egyéb számlázási adminisztrátorok, tulajdonosa, például a használati információk használatával lekérheti a [számlázási API-k](billing-usage-rate-card-overview.md).

A napi használati adatokra vonatkozó további információért lásd: [Microsoft Azure számlájának megértése](billing-understand-your-bill.md). A költségek kezelésével kapcsolatos útmutatásért lásd: [az Azure-elszámolással és költségkezeléssel váratlan költségek megelőzése](billing-getting-started.md).

## <a name="download-usage-for-ea-customers"></a>Nagyvállalati szerződéssel rendelkező ügyfeleink a használati adatok letöltése

Megtekintéséhez és a egy nagyvállalati szerződéssel rendelkező ügyfél, a használati adatok letöltése, egy vállalati rendszergazda engedélyezte, a fiók tulajdonosa, kell lennie, vagy a nézet a részleg rendszergazdai díjat házirend engedélyezve van.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg *Cost Management és számlázás*.

    ![Az Azure portál keresési bemutató képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Válassza ki **használat és költségek**.
1. Válassza ki a letölteni kívánt hónapban, **letöltése**.

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>A Microsoft ügyfél-szerződését a használati adatok letöltése

Ha a Microsoft vevői szerződéssel rendelkezik, az Azure-használat és költségek letöltheti a számlázási profiljához. Számlázási profil tulajdonos, közreműködő, olvasó, vagy töltse le az Azure-használat és költségek CSV-kezelőt a számlához.

### <a name="download-usage-for-billed-charges"></a>Számlázott díjak a használati adatok letöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A Keresés **Cost Management és számlázás**.
3. Válassza ki a számlázási profilt. A hozzáférést, attól függően szükség lehet először ki kell választania egy számlázási fiókot.
4. Válassza ki **számlákat**.
5. A számla rács megkeresi a letölteni kívánt használati tartozó számla.
6. Kattintson a három (`...`) a sor végén található.

    ![A sor végén található három pontra bemutató képernyőkép](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. A letöltési helyi menüben válassza ki a **Azure-használat és költségek**.

     ![Képernyőkép az Azure-használat és a kiválasztott díj](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>Függőben lévő díjat a használati adatok letöltése

Hónap elejétől számított használati is letöltheti az aktuális elszámolási időszakban. Ezek a használati díjak, amely már a még nem számlázzuk.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A Keresés **Cost Management és számlázás**.
3. Válassza ki a számlázási profilt. A hozzáférést, attól függően szükség lehet először ki kell választania egy számlázási fiókot.
4. Az a **áttekintése** panelen keresse meg a letöltési hivatkozások a hónap elejétől számított díjakat alatt.
5. Válassza ki **Azure-használat és költségek**.

    ![Képernyőkép a letöltési az áttekintésből](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>A Microsoft vevői Szerződéssel való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítség Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

A számlák és használati díjak kapcsolatos további információkért lásd:

- [A feltételeinek értelmezése a Microsoft Azure részletes használat](billing-understand-your-usage.md)
- [A Microsoft Azure-hoz kapcsolódó számlák magyarázata](billing-understand-your-bill.md)
- [Megtekintheti, és a Microsoft Azure-számla letöltése](billing-download-azure-invoice.md)
- [Megtekintheti és letöltheti a szervezet az Azure díjszabása](billing-ea-pricing.md)

Ha a Microsoft vevői szerződéssel rendelkezik, tekintse meg:

- [A Microsoft vevői szerződés program Azure feltételeinek értelmezése részletes használat](billing-mca-understand-your-usage.md)
- [A Microsoft Ügyfélszerződéséhez számlán a költségek ismertetése](billing-mca-understand-your-bill.md)
- [Megtekintheti, és a Microsoft Azure-számla letöltése](billing-download-azure-invoice.md)
- [Megtekintheti, és töltse le a Microsoft Ügyfélszerződéséhez adó dokumentumok](billing-mca-download-tax-document.md)
- [Megtekintheti és letöltheti a szervezet az Azure díjszabása](billing-ea-pricing.md)
