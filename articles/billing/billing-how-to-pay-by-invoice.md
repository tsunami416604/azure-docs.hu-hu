---
title: Azure-előfizetésekre vonatkozó számlás fizetés
description: Ismerteti, hogyan lehet Azure-előfizetésekre vonatkozó számlás fizetés.
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
ms.openlocfilehash: 9ca726ef737ce4750018d2461bc4bcd6c7ebb5f5
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491228"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>Az Azure-előfizetését a számlás fizetés

Ha számla ellenében, ez azt jelenti, a számla a számla dátuma szerint ellenőrzés/átviteli átviteli számított 30 napon belül kell fizetnie. Lesz jogosult az Azure-előfizetését a számlás fizetés, küldje el az Azure-támogatási kérést. Miután jóváhagyják a kérelmét, átválthat az használatalapú (ellenőrzés/átviteli átvitel) számlázása a a [az Azure portal](https://portal.azure.com).

> [!IMPORTANT]
> * Számlás fizetési (ellenőrzés/átviteli átvitel) csak vállalati fiókok esetében érhető el.
> * Az összes ki nem egyenlített díjakat használatalapú számlához való váltása előtt kell fizetnie.

## <a name="request-to-pay-by-invoice"></a>A számla ellenében irányuló kérelem

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). Válassza ki **súgó + támogatás** > **új támogatási kérelem**.

    ![Súgó és támogatás hivatkozás](./media/billing-how-to-pay-by-invoice/help-and-support.png)

2. Válassza ki **számlázási** , a **Problématípus**. A *Problématípus* a támogatási kérelem kategória. Válassza ki az előfizetést, amelyhez hozzá szeretné számla ellenében, egy támogatási csomagot, majd válassza ki és **tovább**.

3. Válassza a **Számlás fizetés** lehetőséget a **Probléma típusa** mezőben. A *Problématípus* a támogatási kérelem alkategória.

4. Adja meg a következő információkat a **részletek** mezőbe, majd válassza ki **tovább**.

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

    - A **cégnév** és **cím vállalati** meg kell egyeznie az Ön által megadott információkat az Azure-fiók. Megtekintheti, vagy frissítheti az adatokat, lásd: [módosítása az Azure-fiók profiladatait](billing-how-to-change-azure-account-profile.md).
    - Hozzá kell adnia a számlázási kapcsolattartási adatokat az Azure Portalon előtt a kreditkorlát jóvá kell hagyni. A kapcsolattartási adatok kulcsoszlopnak kapcsolódnia kell a Kötelezettségkezelő vagy a pénzügyi részleg a cég. A számlázási adatok frissítése, lépjen a [Azure Account Center](https://account.azure.com/Profile).

5. Ellenőrizze a kapcsolattartási adatokat és a kívánt kapcsolatfelvételi módot, majd kattintson a **Létrehozás** lehetőségre.

Szükséges hitelképességét miatt szükséges hitel összegét, küldünk a kreditet, ellenőrizze az alkalmazás.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>Váltás a számlázás használatalapú (ellenőrzés/átviteli átvitel)

Ha jóváhagyja, a számla ellenében, használatalapú (ellenőrzés/átviteli átvitel) az Azure Portalon a számlához való válthat.

Ha a Microsoft Online Services Program fiókkal rendelkezik, az Azure-előfizetés átviteli ellenőrzés/átviteli válthat. Ha rendelkezik egy Microsoft Ügyfélszerződéséhez, átválthat a számlázási ellenőrzése/átviteli átviteli profilját. [Ismerje meg, hogyan ellenőrizheti a fiók típusa](#check-access-to-a-microsoft-customer-agreement).

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Váltson Azure-előfizetés ellenőrzése/átviteli átvitel

Az Azure-előfizetés fizetési (ellenőrzés/átviteli átvitel) a számlához való váltás az alábbi lépésekkel. **Miután használatalapú (ellenőrzés/átviteli átvitel) a számlázandó nem válthat vissza hitelkártya**.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) fiók rendszergazdaként.
1. Keresse meg **Cost Management és számlázás**.

    ![Keresés bemutató képernyőkép](./media/billing-how-to-pay-by-invoice/search.png)

1. Válassza ki az előfizetést, amelyet szeretne váltani a fizetés számla.
1. Válassza a **Fizetési módok** elemet.
1. A parancssávon kattintson a **számla ellenében** gombra.

    ![Képernyőkép a fizetés számla gomb](./media/billing-how-to-pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>Váltson a számlázási ellenőrzése/átviteli átviteli-profil

Ellenőrzés/átviteli átviteli számlázási profilt Váltás az alábbi lépésekkel. Vegye figyelembe, hogy csak az a személy, aki az Azure-ban regisztrált módosíthatja az alapértelmezett szolgáltatási díj fizetési módjának számlázási profilja.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A Keresés **Cost Management és számlázás**.
1. A bal oldali menüben kattintson a **számlázási profilok**.

    ![Képernyőkép a számlázási profilja menü](./media/billing-how-to-pay-by-invoice/billing-profile.png)

1. Válassza ki a számlázási profilt.
1. A bal oldali menüben válassza **fizetési módok**.

   ![Képernyőkép a fizetési módok menü](./media/billing-how-to-pay-by-invoice/billing-profile-payment-methods.png)

1. Kattintson a kék szalagcím, amely szerint a jogosult ellenőrzés/átviteli átviteli szerint kell fizetnie.

    ![Váltson át az ellenőrzés/átviteli kék szalagcím bemutató képernyőkép](./media/billing-how-to-pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>A Microsoft Ügyfélszerződéséhez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- Ha szükséges, a számlázási kapcsolattartási adatok frissítése a [Azure Account Center](https://account.azure.com/Profile).
