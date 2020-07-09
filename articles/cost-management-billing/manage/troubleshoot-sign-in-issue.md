---
title: Az Azure-előfizetés bejelentkezési hibáinak elhárítása
description: Segítség az Azure Portalra vagy az Azure Fiókközpontba való bejelentkezéssel kapcsolatos hibák megoldásához.
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: troubleshooting
ms.date: 06/05/2020
ms.author: v-miegge
ms.openlocfilehash: 15dc25c3092f0bbf1a79f107f9d5ca87fa896bee
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102514"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Az Azure-előfizetés bejelentkezési hibáinak elhárítása

Ez az útmutató segítséget nyújt az Azure Portalra vagy az Azure Fiókközpontba való bejelentkezéssel kapcsolatos hibák megoldásában.

> [!NOTE]
> Ha problémát tapasztal egy új Azure-fiók regisztrálásakor, tekintse meg az [Azure-előfizetés regisztrációjával kapcsolatos hibák elhárítását](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-azure-sign-up) ismertető szakaszt.

## <a name="page-hangs-in-the-loading-status"></a>Az oldal betöltési állapotban lefagy

Ha az internetböngésző oldala lefagy, próbálkozzon az alábbi lépésekkel, amíg el nem jut az Azure Portalra.

- Frissítse az oldalt.
- Használjon másik internetböngészőt.
- Használjon privát böngészési módot a böngészőben:

   - **Edge:** Nyissa meg a **Beállítások** elemet (a profilképe mellett lévő három pont), válassza az **Új InPrivate-ablak** lehetőséget, majd nyissa meg az [Azure Portalt](https://portal.azure.com/) vagy az [Azure Fiókközpontot](https://account.azure.com/Subscriptions), és jelentkezzen be. 
   - **Chrome:** Válassza az **Inkognitó** módot.
   - **Safari:** Válassza a **Fájl**, majd az **Új privát ablak** elemet.

- Ürítse ki a gyorsítótárat, és törölje az internetes cookie-kat:

   - **Edge:** Nyissa meg a **Beállítások** elemet, és válassza az **Adatvédelem és szolgáltatások** lehetőséget. Kövesse a **Böngészési adatok törlése** területen megjelenő lépéseket. Győződjön meg arról, hogy az **Előzmények**, a **Letöltési előzmények** és a **Gyorsítótárazott képek és fájlok** mellett található jelölőnégyzet be van jelölve, majd kattintson a **Törlés** gombra.
   - **Chrome:** Válassza a **Beállítások** lehetőséget, majd az **Adatvédelem és biztonság** területen kattintson a **Böngészési adatok törlése** elemre.

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>Automatikusan egy másik felhasználóként jelentkezik be

A probléma akkor jelentkezik, ha egynél több felhasználói fiókot használ egy internetböngészőben.

A hiba elhárításához próbálkozzon az alábbi módszerekkel:

- Ürítse ki a gyorsítótárat, és törölje az internetes cookie-kat.

   - **Edge:** Nyissa meg a **Beállítások** elemet, és válassza az **Adatvédelem és szolgáltatások** lehetőséget. Kövesse a **Böngészési adatok törlése** területen megjelenő lépéseket. Győződjön meg arról, hogy az **Előzmények**, a **Letöltési előzmények**, a **Cookie-k** és a **Gyorsítótárazott képek és fájlok** mellett található jelölőnégyzet be van jelölve, majd kattintson a **Törlés** gombra.
   - **Chrome:** Válassza a **Beállítások** lehetőséget, majd az **Adatvédelem és biztonság** területen kattintson a **Böngészési adatok törlése** elemre.
- Állítsa vissza a böngésző alapértelmezett beállításait.
- Használjon privát böngészési módot a böngészőben. 
   - **Edge:** Nyissa meg a **Beállítások** elemet (a profilképe mellett lévő három pont), válassza az **Új InPrivate-ablak** lehetőséget, majd nyissa meg az [Azure Portalt](https://portal.azure.com/) vagy az [Azure Fiókközpontot](https://account.azure.com/Subscriptions), és jelentkezzen be. 
   - **Chrome:** Válassza az **Inkognitó** módot.
   - **Safari:** Válassza a **Fájl**, majd az **Új privát ablak** elemet.

## <a name="i-can-sign-in-but-i-see-the-error-no-subscriptions-found"></a>Be tudok jelentkezni, de a Nem található előfizetés hiba jelenik meg

Ez a probléma akkor fordul elő, ha nem a megfelelő címtárat választotta ki, vagy ha a fiókja nem rendelkezik megfelelő engedélyekkel.

**1. forgatókönyv:** Az [Azure Portalra](https://portal.azure.com/) történő bejelentkezéskor jelenik meg a hibaüzenet

A probléma megoldása:

- A jobb felső sarokban kattintson a fiókjára, és ellenőrizze, hogy a megfelelő Azure-címtárat választotta-e ki.
- Ha a megfelelő Azure-címtár van kiválasztva, de továbbra is megjelenik a hibaüzenet, adja hozzá a fiókot [Tulajdonosként](https://docs.microsoft.com/azure/cost-management-billing/manage/add-change-subscription-administrator).

**2. forgatókönyv:** Az [Azure Fiókközpontba](https://account.windowsazure.com/Subscriptions) történő bejelentkezéskor jelenik meg a hibaüzenet

Ellenőrizze, hogy az Ön által használt fiók a Fiókadminisztrátor. Annak ellenőrzéséhez, hogy ki a Fiókadminisztrátor, kövesse az alábbi lépéseket:

1.  Jelentkezzen be az [Azure Portal Előfizetések nézetébe](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1.  Válassza ki az ellenőrizni kívánt előfizetést, majd kattintson a **Beállítások** elemre.
1.  Válassza ki a **Tulajdonságok** elemet. Az előfizetés fiókadminisztrátora a **Fiókadminisztrátor** mezőben látható.

## <a name="additional-help-resources"></a>További támogatási forrásanyagok

Az Azure-számlázással és -előfizetésekkel kapcsolatos további hibaelhárítási cikkek

- [Elutasított bankkártya](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)
- [Az előfizetésre való regisztrációval kapcsolatos problémák](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-azure-sign-up)
- [Nem található előfizetés](https://docs.microsoft.com/azure/cost-management-billing/manage/no-subscriptions-found)
- [Vállalati költségek nézet letiltva](https://docs.microsoft.com/azure/cost-management-billing/manage/enterprise-mgmt-grp-troubleshoot-cost-view)
- [Az Azure-számlázás dokumentációja](https://docs.microsoft.com/azure/cost-management-billing/)

## <a name="contact-us-for-help"></a>Ha segítségre van szüksége, vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
