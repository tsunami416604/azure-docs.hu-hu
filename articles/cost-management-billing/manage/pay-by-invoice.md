---
title: Azure-előfizetések fizetése számla alapján
description: Leírja, hogyan kell kifizetni az Azure-előfizetéseket számla alapján.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: banders
ms.openlocfilehash: 8552b6732736d1d10711479639fa45114f8b039b
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85298004"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>Azure-előfizetés fizetése számla alapján

Ha számlás fizetésre vált, az azt jelenti, hogy számláját a számlázási dátumtól számított 30 napon belül egyenlíti ki csekken vagy átutalással. Ahhoz, hogy jogosult legyen Azure-előfizetését számlás fizetéssel kifizetni, küldjön egy kérést az Azure ügyfélszolgálatának. Miután jóváhagyták a kérését, az [Azure Portalon](https://portal.azure.com) válthat számlás fizetésre (csekk/átutalás).

> [!IMPORTANT]
> * A számlás fizetés (csekk/átutalás) csak az Azure-t egy vállalata nevében használó ügyfelek számára érhető el.
> * A számlás fizetésre való áttérés előtt minden ki nem egyenlített díjat rendeznie kell.
> * A számlás fizetés jelenleg nem támogatott Kínában a globális Azure szolgáltatás esetében.

## <a name="request-to-pay-by-invoice"></a>Számlás fizetés kérelmezése

1. Navigáljon az [Azure Portalra](https://portal.azure.com) egy támogatási kérelem küldéséhez. Keresse meg és válassza ki a **Súgó és támogatás** elemet.

    ![Segítség és támogatás keresése, Microsoft Azure Portal](./media/pay-by-invoice/search-for-help-and-support.png)

2. Válassza az **Új támogatási kérelem** lehetőséget.

    ![Új támogatási kérés hivatkozás, Súgó és támogatás képernyő, Microsoft Azure Portal](./media/pay-by-invoice/help-and-support.png)

2. Válassza ki a **Számlázás** lehetőséget a **probléma típusaként**. A *problématípus* a támogatási kérelem kategóriája. Válassza ki azt az előfizetést, amelynél számlával szeretne fizetni, válasszon egy támogatási csomagot, majd válassza a **Tovább** lehetőséget.

3. Válassza a **Fizetés** lehetőséget a **Probléma típusa** legördülő listából. A *probléma típusa* a támogatási kérelem alkategóriája.

4. Válassza a **Váltás számlás fizetésre** lehetőséget a **Probléma altípusa** legördülő listából.

5. Adja meg a következő információkat a **Részletek** mezőben, majd válassza a **Tovább** lehetőséget.

         New or existing customer:
         If existing, current payment method:
         Order ID (requesting for invoice option):
         Account Admins Live ID (or Org ID) (should be company domain):
         Commerce Account ID:
         Company Name (as registered under VAT or Government Website):
         Company Address (as registered under VAT or Government Website):
         Company Website:
         Country:
         TAX ID/ VAT ID:
         Company Established on (Year):
         Any prior business with Microsoft:
         Contact Name:
         Contact Phone:
         Contact Email:
         Justification on why you prefer Invoice option over credit card:

         For cores increase, provide the following additional information:

         (Old quota) Existing Cores:
         (New quota) Requested cores:
         Specific region & series of Subscription:

    - A **Vállalat neve** és a **Vállalat címe** értékének meg kell egyeznie az Azure-fiókhoz megadott adatokkal. További információkat az adatok megtekintéséről és frissítéséről [az Azure-fiók profiladatainak módosításával](change-azure-account-profile.md) kapcsolatos részben találhat.
    - A hitelkeret jóváhagyása előtt meg kell adnia számlázási kapcsolattartási adatait az Azure Portalon. A kapcsolattartási adatoknak a bérszámfejtési vagy a pénzügyi részleg elérhetőségeit kell tartalmazniuk. A számlázási kapcsolattartási adatok frissítéséhez lépjen az [Azure Fiókközpontba](https://account.azure.com/Profile).

6. Ellenőrizze a kapcsolattartási adatokat és a kívánt kapcsolatfelvételi módot, majd válassza a **Létrehozás** lehetőséget.

Ha hitelképesség-vizsgálatra van szükség az igényelendő hitel mennyisége miatt, küldünk Önnek egy hitelképesség-vizsgálati kérelmet.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>Váltás számlás fizetésre (csekk/banki átutalás)

Miután jóváhagyják a kérelmét, a fizetési módot számlás fizetésre (csekk/átutalás) válthatja az Azure Portalon.

Ha rendelkezik Microsoft Online Services Program-fiókkal, Azure-előfizetését átválthatja csekkel/banki átutalással történő fizetésre. Ha rendelkezik Microsoft-ügyfélszerződéssel, átválthatja a számlázási profilját csekkel/banki átutalással történő fizetésre. [További tudnivalók a fiók típusának megtekintéséről](#check-access-to-a-microsoft-customer-agreement).

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Azure-előfizetés váltása csekkel/átutalással való fizetésre

Kövesse az alábbi lépéseket Azure-előfizetése számlás fizetésre (csekk/átutalás) váltásához. *Ha számlás fizetésre vált (csekk/banki átutalás), nem válthat vissza hitelkártyás fizetésre*.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be fiókadminisztrátorként. Keresse meg és válassza ki a **Költségkezelés + számlázás** lehetőséget.

    ![Keresse meg a Költségkezelés és számlázás elemet a Microsoft Azure Portalon.](./media/pay-by-invoice/search.png)

1. Válassza ki azt az előfizetést, amelyet számlás fizetésre kíván átváltani.
1. Válassza a **Fizetési módok** elemet.
1. Válassza a **Fizetés számlával** gombot a parancssávon.

    ![Fizetés számlával gomb, Fizetési módok, Microsoft Azure Portal](./media/pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>Számlázási profil váltása csekk/átutalás használatára

Kövesse az alábbi lépéseket a számlázási profil csekkes/átutalásos fizetési módra váltásához. Csak az Azure-ban regisztrált személy módosíthatja a számlázási profil alapértelmezett fizetési módját.

1. Számlázási információinak megtekintéséhez navigáljon az [Azure Portalra](https://portal.azure.com). Keresse meg és válassza ki a **Költségkezelés + számlázás** lehetőséget.
1. A menüben válassza ki a **Számlázási profilok** elemet.

    ![Számlázási profilok menüpont, Költségkezelés és számlázás, Microsoft Azure Portal](./media/pay-by-invoice/billing-profile.png)

1. Válasszon ki egy számlázási profilt.
1. A **Számlázási profilok** menüben válassza ki a **Fizetési módok** elemet.

   ![Fizetési módok menüpont, Számlázási profilok, Költségkezelés, Microsoft Azure Portal](./media/pay-by-invoice/billing-profile-payment-methods.png)

1. Kattintson arra a szalagra, amely szerint jogosult a csekkes/átutalások fizetési mód használatára.

    ![A csekkes/átutalásos fizetési mód közötti váltásra szolgáló szalag, Fizetési módok, Microsoft Azure Portal](./media/pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="frequently-asked-questions"></a>Gyakori kérdések

*Miért kaptam jogi dokumentumra vonatkozó kérést?*

A Microsoftnak alkalmanként jogi dokumentációra van szüksége, ha az Ön által megadott adatok hiányosak vagy nem ellenőrizhetők. Példák erre vonatkozóan:

* Néveltérés a fióknév és a vállalatnév között
* Név változása

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

* Ha szükséges, frissítse a számlázási kapcsolattartási adatokat az [Azure Fiókközpontban](https://account.azure.com/Profile).
