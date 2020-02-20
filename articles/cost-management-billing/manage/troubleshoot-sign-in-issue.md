---
title: Az Azure-előfizetés bejelentkezési hibáinak elhárítása
description: Segítség az Azure Portalra vagy az Azure Fiókközpontba való bejelentkezéssel kapcsolatos hibák megoldásához.
author: v-miegge
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: v-miegge
ms.openlocfilehash: 40a645fe4b74043d84dc770f15d5e3c3ec907f02
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199483"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Az Azure-előfizetés bejelentkezési hibáinak elhárítása

Ez az útmutató segítséget nyújt az Azure Portalra vagy az Azure Fiókközpontba való bejelentkezéssel kapcsolatos hibák megoldásában.

## <a name="issues"></a>Hibák

### <a name="page-hangs-in-the-loading-status"></a>Az oldal betöltési állapotban lefagy

Ha az internetböngésző oldala lefagy, próbálkozzon az alábbi lépésekkel, amíg el nem jut az Azure Portalra.

- Frissítse az oldalt.
- Használjon másik internetböngészőt.
- Használjon privát böngészési módot a böngészőben. Internet Explorer esetén: Kattintson az **Eszközök** > **Biztonság** > **InPrivate-böngészés** elemre, majd a böngészőben lépjen az [Azure Portalra](https://portal.azure.com/) vagy az [Azure Fiókközpontba](https://account.azure.com/Subscriptions), és jelentkezzen be.

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>Automatikusan egy másik felhasználóként jelentkezik be

A probléma akkor jelentkezik, ha egynél több felhasználói fiókot használ egy internetböngészőben.

A hiba elhárításához próbálkozzon az alábbi módszerekkel:

- Ürítse ki a gyorsítótárat, és törölje az internetes cookie-kat. Az Internet Explorerben kattintson **Eszközök** > **Internetbeállítások** > **Törlés** elemre. Ügyeljen arra, hogy az átmeneti fájlok, cookie-k, a jelszó és a böngészési előzmények jelölőnégyzete be legyen jelölve, majd kattintson a Törlés gombra.
- Az Internet Explorer beállításainak alaphelyzetbe állításával visszaállíthatja az Ön által megadott személyes beállításokat. Kattintson az **Eszközök** > **Internetbeállítások** > **Speciális**, elemre, majd válassza a **Személyes beállítások törlése** >  **Alaphelyzetbe állítás** elemet.
- Használjon privát böngészési módot a böngészőben. Internet Explorer esetén:  Kattintson az **Eszközök** > **Biztonság** > **InPrivate-böngészés** elemre, majd a böngészőben lépjen az [Azure Portalra](https://portal.azure.com/) vagy az [Azure Fiókközpontba](https://account.azure.com/Subscriptions), és jelentkezzen be.

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>Be tudok jelentkezni, de a *Nem található előfizetés* hiba jelenik meg

Ez a probléma akkor fordul elő, ha nem a megfelelő címtárat választotta ki, vagy ha a fiókja nem rendelkezik megfelelő engedélyekkel.

**1. forgatókönyv:** A hibaüzenet az [Azure Portalon](https://portal.azure.com/) jelenik meg

A probléma megoldása:

- A jobb felső sarokban kattintson a fiókjára, és ellenőrizze, hogy a megfelelő Azure-címtárat választotta-e ki.
- Ha a megfelelő Azure-címtár van kiválasztva, de továbbra is megjelenik a hibaüzenet, adja hozzá a fiókot [Tulajdonosként](add-change-subscription-administrator.md).

**2. forgatókönyv:** A hibaüzenet az [Azure Fiókközpontban](https://account.windowsazure.com/Subscriptions) jelenik meg

Ellenőrizze, hogy az Ön által használt fiók a Fiókadminisztrátor. Annak ellenőrzéséhez, hogy ki a Fiókadminisztrátor, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portal Előfizetések nézetébe](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Válassza ki az ellenőrizni kívánt előfizetést, majd lépjen a **Beállítások** területre.

3. Válassza ki a **Tulajdonságok** elemet. Az előfizetés fiókadminisztrátora a **Fiókadminisztrátor** mezőben látható.

## <a name="additional-help-resources"></a>További támogatási forrásanyagok

Az Azure-számlázással és -előfizetésekkel kapcsolatos további hibaelhárítási cikkek

- [Elutasított bankkártya](troubleshoot-declined-card.md)
- [Az előfizetésre való regisztrációval kapcsolatos problémák](troubleshoot-azure-sign-up.md)
- [Nem található előfizetés](no-subscriptions-found.md)
- [Vállalati költségek nézet letiltva](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Ha segítségre van szüksége, vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- [Az Azure-számlázás dokumentációja](../../billing/index.md)
