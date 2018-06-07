---
title: Töltse le a számla és a napi használati adatok számlázási Azure |} Microsoft Docs
description: Ismerteti az Azure számlázási számla és a napi használati adatok megtekintése és letöltése.
keywords: számlázási számla, számla letöltési, azure számla, azure kihasználtsága
services: billing
documentationcenter: ''
author: genlin
manager: tonguyen
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 59ee992518b5e529f486415f962c6f26881875f1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607412"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Az Azure számlázási számla és a napi használati adatok megtekintése és letöltése
A számla a programot letöltheti a [Azure-portálon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) vagy annak az e-mailben küldött. A napi használat letöltéséhez keresse fel a [Azure Account Center](https://account.azure.com/Subscriptions). Csak egyes szerepkörök engedélye számlázási számla és használati adatokat, például a fiók rendszergazdájához. Első számlázási információhoz való hozzáféréssel kapcsolatos további információkért lásd: [Azure számlázási szerepkörök használatával való hozzáférés kezelése](billing-manage-access.md).

Ez a cikk nem alkalmazza a nagyvállalati szerződés (EA) vonatkozik. Ha az ügyfél egy EA, a számlák közvetlenül a beléptetési rendszergazdák kerülnek.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="get-your-invoice-in-email-pdf"></a>A számla beolvasni e-mailben (.pdf)
Részt, és konfigurálja az Azure fogadásához további címzetteket számla e-mailben. Ez a szolgáltatás nem az egyes előfizetések például támogatási ajánlatokat, kötött nagyvállalati szerződése vagy Azure in Open licencprogram érhetők el.

1. Válassza ki az előfizetést a [előfizetések oldalán](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Részvétel az egyes előfizetésekhez Ön a tulajdonosa. Kattintson a **számlák** majd **E-mail-a számla**. 

    ![Képernyőkép a szemlélteti a szolgáltatás aktiválása](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. Kattintson a **részt vevő** és fogadja el a feltételeket.

    ![Képernyőkép a szolgáltatás aktiválása folyamat 2. lépés](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. A szerződés elfogadása után további címzetteket is konfigurálhat. Előfizető eltávolítása az e-mail cím már nem tárolja. Ha meggondolja magát, kell újra vegye fel őket.

    ![Képernyőkép a szolgáltatás aktiválása folyamat 3. lépés](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
Ha lépések végrehajtása után nem kap egy e-mailt, győződjön meg arról, hogy az e-mail cím helyes-e a [kapcsolattartási beállítások a profil](https://account.windowsazure.com/profile).

### <a name="opt-out-from-getting-your-invoice-in-email"></a>Nem vesznek részt a számla fog e-mailben
Ha nem szeretne a számla e-mailben, kattintson a kimenő e-mailben elküldött számlák letiltására. Ezzel eltávolítja az e-mail címekhez számlák fogadásra e-mailben. Ha úgy dönt, a biztonsági kell konfigurálja újra a címzetteket.

 ![A letiltási folyamat képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

## <a name="download-invoice-from-azure-portal-pdf"></a>Töltse le a számla az Azure portál (.pdf)

1. Válassza ki az előfizetést a [előfizetések oldalán](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) , Azure-portálon [számlák hozzáféréssel rendelkező felhasználó](billing-manage-access.md).

2. Válassza ki **számlák**. 

    ![Képernyőkép a számlázási és használati beállítás](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

3. Kattintson a **töltse le a számla** a PDF számla megtekintéséhez. Ha a felirat látható **nem érhető el**, lásd: [miért nem látom számla a legutóbbi számlázási időszakban?](#noinvoice)

    ![Képernyőkép a számlázási időszak letöltése beállítás, és az összes díj minden számlázási időszakban](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. A napi használat a számlázott időszak kattintva is megtekintheti. 

A számla kapcsolatos további információkért lásd: [a számlázási megérteni a Microsoft Azure](billing-understand-your-bill.md). Költségek kezelésére, lásd: [Azure számlázás és költség felügyeleti váratlan költségek megakadályozása](billing-getting-started.md).

## <a name="download-usage-from-the-account-center-csv"></a>Használati letöltését az Account Center (.csv)

1. Jelentkezzen be a [Azure Account Center](https://account.windowsazure.com/subscriptions) fiók rendszergazdaként.

2. Válassza ki az előfizetést, legyen a számla és használati adatokat.

3. Válassza ki **számlázási előzmények**. 

    ![Képernyőkép a Számlázási előzmények beállítás](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Az utasítás a legutóbbi hat számlázási időszak és az aktuális teljesüléséig időszak tekintheti meg. 

    ![Képernyőkép a számlázási időszak, töltse le a számla és a napi használat és a teljes költségek minden számlázási időszak lehetőségek](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Válassza ki **aktuális nyilatkozat megtekintése** becsült készítésének időpontjában a díjak becsült megjelenítéséhez. Ezek az adatok naponta csak frissül, és nem tartalmazhatják a használati. A havi számla Ez a becslés eltérhet.

    ![A nézet jelenlegi utasítást beállítás képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![A jelenlegi díjak becsült képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Válassza ki **letöltése használati** CSV-fájlként a napi használati adatok letöltése. Ha elérhető két verzióját látja, töltse le a 2-es verzióját.

    ![A használati letöltése beállítás képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Csak a Fiókadminisztrátor az Azure Account Center férhetnek hozzá. Más számlázási adminisztrátorok, például egy olyan tulajdonost, a használati adatokat a beszerezheti a [számlázási API-k](billing-usage-rate-card-overview.md).

A napi használatával kapcsolatos további információkért lásd: [a számlázási megérteni a Microsoft Azure](billing-understand-your-bill.md). Költségek kezelésére, lásd: [Azure számlázás és költség felügyeleti váratlan költségek megakadályozása](billing-getting-started.md).

## <a name="noinvoice"></a> Miért nem látja a legutóbbi számlázási időszakban számla?

Számla nem látható több oka lehet:

- Ingyenes próbaverzió vagy havi összege van, amely nem túllépi az előfizetéshez. Számla csak akkor keletkezik, amikor pénz akikkel.

- Azure előfizetett napjától számított 30 napon belül.

- A számla még a nem jönnek létre. Várjon, amíg a számlázott időszak végén.

- Ha nem a fiók rendszergazdájához, a korábbi számlák nem lehet elérhető.

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.
Ha további kérdései további, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.

