---
title: Az Azure számlázási és napi használati adatok letöltése |} A Microsoft Docs
description: Útmutató letöltéséhez vagy az Azure számlázási és napi használati adatok megtekintéséhez.
keywords: Hitelkártyás, számla letöltése, azure-számlát, azure-használat
services: billing
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: cwatson
ms.openlocfilehash: 9067162c6f1bd95b0a1c25bef17836b19ecf6946
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52276109"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Töltse le, vagy az Azure számlázási számlázási és napi használati adatok megtekintése
Letöltheti a számlát a [az Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) vagy e-mailben küldött nincs. A napi használati adatok letöltése, lépjen a [Azure Account Center](https://account.azure.com/Subscriptions). Csak egyes szerepkörök hitelkártyás és használati adatokat, például a fiók rendszergazdája engedélye. További információ a számlázási információhoz való hozzáférésről: [Az Azure-beli számlázáshoz való hozzáférés kezelése szerepkörök használatával](billing-manage-access.md).

Ez a cikk nem vonatkozik az ügyfelek a nagyvállalati szerződés (EA) számára. Ha Ön nagyvállalati szerződéssel rendelkező ügyfelek, a számlákat közvetlenül a regisztrációs rendszergazdák érkeznek.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

> [!div class="nextstepaction"]
> [Segítsen az Azure számlázási dokumentumok fejlesztésében](https://go.microsoft.com/fwlink/p/?linkid=2010091)

## <a name="get-your-invoice-in-email-pdf"></a>A számla kapni e-mailben (.pdf)
Is engedélyezve van a hibajelentések, valamint fogadni az Azure további címzettek egy e-mailben számla. Ez a funkció nem lehet például a támogatási ajánlatok, a nagyvállalati szerződés keretében vagy az Azure in Open licencprogram bizonyos előfizetésekhez érhető el.

1. Válassza ki az előfizetését a [előfizetések lapon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Feliratkozás az egyes saját előfizetését. Kattintson a **számlákat** majd **E-mail-a számla**. 

    ![A szolgáltatás aktiválása folyamatot bemutató képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. Kattintson a **engedélyezve van a hibajelentések** és fogadja el a feltételeket.

    ![Képernyőkép a választható flow 2. lépés](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Ha Ön már elfogadta a szerződést, további címzetteket is beállíthatja. Amikor eltávolít egy címzettet, az e-mail-cím már nem tárolja. Ha meggondolja magát, újra hozzá kell őket.

    ![Képernyőkép a választható folyamat 3. lépés](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
Ha a lépések végrehajtása után nem kap egy e-mailt, győződjön meg arról, az e-mail-címét a megfelelő a [profilját a kommunikációs beállítások](https://account.windowsazure.com/profile).

### <a name="opt-out-from-getting-your-invoice-in-email"></a>Kikapcsolhatja az e-mailben a számla ki
Ha nem szeretne kapni a számla e-mailben, kattintson a választható ki a számlákat e-mailben. Ez eltávolítja az összes e-mail-címeket úgy, hogy fogadják a számlákat e-mailben. Ha úgy dönt, a biztonsági kell konfigurálja újra a címzetteket.

 ![A letiltási folyamat bemutató képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

## <a name="download-invoice-from-azure-portal-pdf"></a>Számla letöltése az Azure Portal használatával (.pdf)

1. Válassza ki az előfizetését a [előfizetések lapon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) az Azure Portalon, [számlákat hozzáféréssel rendelkező felhasználó](billing-manage-access.md).

2. Válassza ki **számlákat**. 

    ![A számlázás- és használati beállítás bemutató képernyőkép](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

3. Kattintson a **számla letöltése** megtekintéséhez a PDF formátumú számlákat másolatát. Ugyanakkor **nem érhető el**, lásd: [miért nem látom számla alapján az előző elszámolási időszakban?](#noinvoice)

    ![Képernyőkép a elszámolási időszakok, a letöltési lehetőséget és a teljes díj minden elszámolási időszakban](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. A napi használat a számlázási időszak kattintva is megtekintheti. 

A számla kapcsolatos további információkért lásd: [Microsoft Azure-hoz kapcsolódó számlák magyarázata](billing-understand-your-bill.md). Költségek kezelése kapcsolatos útmutatásért lásd: [az Azure-elszámolással és költségkezeléssel váratlan költségek megelőzése](billing-getting-started.md).

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

A napi használati adatokra vonatkozó további információért lásd: [Microsoft Azure számlájának megértése](billing-understand-your-bill.md). Költségek kezelése kapcsolatos útmutatásért lásd: [az Azure-elszámolással és költségkezeléssel váratlan költségek megelőzése](billing-getting-started.md).

## <a name="noinvoice"></a> Miért nem látom számla alapján az előző elszámolási időszakban?

Számos oka lehet annak, hogy nem jelenik meg számla:

- Nem haladta meg az előfizetésében foglalt jóváírás havi keretét vagy ingyenes próbaverziót használ. A rendszer csak tartozás esetén állít ki számlát.

- 30 napnál kevesebb telt el az Azure-előfizetés életbe lépése óta.

- A rendszer még nem hozta létre a számlát. Várja meg a számlázási időszak végét.

- Ha nem Ön a fiókadminisztrátor, akkor előfordulhat, hogy a régebbi számlákhoz nem fér hozzá.

## <a name="need-help-contact-support"></a>Segítség Forduljon az ügyfélszolgálathoz.
Ha további kérdése van, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

