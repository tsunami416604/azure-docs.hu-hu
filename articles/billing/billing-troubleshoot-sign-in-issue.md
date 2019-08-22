---
title: Az Azure-előfizetés bejelentkezési problémáinak elhárítása
description: Segít feloldani azokat a problémákat, amelyekben nem tud bejelentkezni a Azure Portal vagy az Azure Account Centerbe.
author: v-miegge
manager: na
editor: na
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: ca641813e8b01a39d31a56e3730424b0fa1d6436
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657046"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Az Azure-előfizetés bejelentkezési problémáinak elhárítása

Ez az útmutató segít feloldani azokat a problémákat, amelyekben nem tud bejelentkezni a Azure Portal vagy az Azure Account Centerbe. 

## <a name="issues"></a>Problémák

### <a name="page-hangs-in-the-loading-status"></a>A lap lefagy a betöltési állapotba

Ha az internetböngésző lapja leáll, próbálkozzon az alábbi lépésekkel, amíg el nem jut a Azure Portal.

- Frissítse az oldalt.
- Használjon másik böngészőt.
- Használja a böngészőben a privát böngészési módot. Az Internet Explorerben: Kattintson az **eszközök** > **Biztonság** > InPrivate-**böngészés**elemre, majd keresse meg és jelentkezzen be a [Azure Portal](https://portal.azure.com/) vagy az [Azure Account Center](https://account.azure.com/Subscriptions)webhelyre.

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>Automatikusan más felhasználóként van bejelentkezve

Ez a probléma akkor fordulhat elő, ha egynél több felhasználói fiókot használ egy böngészőben.

A probléma megoldásához próbálja meg az alábbi módszerek egyikét:

- Törölje a gyorsítótárat, és törölje az internetes cookie-kat. Az Internet Explorerben kattintson az **eszközök** > **Internetbeállítások** > **Törlés**elemre. Győződjön meg arról, hogy az ideiglenes fájlok, a cookie-k, a jelszó és a böngészési előzmények jelölőnégyzete be van jelölve, majd kattintson a Törlés gombra.
- Az Internet Explorer beállításainak alaphelyzetbe állításával visszaállíthatja az Ön által létrehozott személyes beállításokat. Kattintson az **eszközök** > **Internetbeállítások** > **speciális** > válassza a **személyes beállítások törlése** jelölőnégyzetet > alaphelyzetbe **állítása**lehetőséget.
- Használja a böngészőben a privát böngészési módot. Az Internet Explorerben:  Kattintson az **eszközök** > **Biztonság** > InPrivate-**böngészés**elemre, majd keresse meg és jelentkezzen be a [Azure Portal](https://portal.azure.com/) vagy az [Azure Account Center](https://account.azure.com/Subscriptions)webhelyre.

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>Tudok bejelentkezni, de *nem* találhatók előfizetések

Ez a probléma akkor fordul elő, ha az nem megfelelő könyvtárba, vagy ha a fiókja nem rendelkezik megfelelő engedélyekkel.

**1. forgatókönyv:** Hibaüzenet érkezik a [Azure Portal](https://portal.azure.com/)

A probléma megoldásához:

- Győződjön meg arról, hogy a megfelelő Azure-címtár kattintson a jobb felső sarokban fiókja van-e kiválasztva.
- Ha a megfelelő Azure-címtár van kiválasztva, de továbbra is megjelenik a hibaüzenet, a fiókját [tulajdonosként adja hozzá](billing-add-change-azure-subscription-administrator.md).

**2. forgatókönyv:** Hibaüzenet érkezik a [Azure Fiókközpont](https://account.windowsazure.com/Subscriptions)

Ellenőrizze, hogy a fiókot, amelynek Ön a fiók rendszergazdája. Annak ellenőrzéséhez, aki a fiók rendszergazdája, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [előfizetések megtekintése az Azure Portalon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Válassza ki az előfizetést, gombra, majd keresse meg a kívánt **beállítások**.

3. Válassza ki **tulajdonságok**. Az előfizetés fiókadminisztrátori fiókjával jelenik meg a **Fiókadminisztrátor** mezőbe.

## <a name="additional-help-resources"></a>További Súgó-erőforrások

Egyéb hibaelhárítási cikkek az Azure-számlázáshoz és-előfizetésekhez

- [Elutasított bankkártya](billing-troubleshoot-declined-card.md)
- [Feliratkozási problémák](billing-troubleshoot-azure-sign-up.md)
- [Nem található előfizetés](billing-no-subscriptions-found.md)
- [Vállalati költségek nézet letiltva](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Segítségért forduljon hozzánk

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- [Az Azure számlázási dokumentációja](index.md)
