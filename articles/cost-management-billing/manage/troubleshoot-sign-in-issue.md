---
title: Az Azure-előfizetés bejelentkezési hibáinak elhárítása
description: Segítség az Azure Portalra vagy az Azure Fiókközpontba való bejelentkezéssel kapcsolatos hibák megoldásához.
services: azure
author: v-miegge
manager: dcscontentpm
editor: na
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: 74cf3f197895823bcbd94db0bf3c7bd8065df767
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988135"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Az Azure-előfizetés bejelentkezési hibáinak elhárítása

Ez az útmutató segítséget nyújt az Azure Portalra vagy az Azure Fiókközpontba való bejelentkezéssel kapcsolatos hibák megoldásában.

## <a name="issues"></a>Problémák

### <a name="page-hangs-in-the-loading-status"></a>Az oldal betöltési állapotban lefagy

Ha az internetböngésző oldala lefagy, próbálkozzon az alábbi lépésekkel, amíg el nem jut az Azure Portalra.

- Frissítse az oldalt.
- Használjon másik internetböngészőt.
- Használjon privát böngészési módot a böngészőben. Internet Explorer esetén: kattintson az **eszközök** > **biztonsági** > **InPrivate-böngészés**lehetőségre, majd keresse meg és jelentkezzen be a [Azure Portal](https://portal.azure.com/) vagy az [Azure Account Center webhelyre](https://account.azure.com/Subscriptions).

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>Automatikusan egy másik felhasználóként jelentkezik be

A probléma akkor jelentkezik, ha egynél több felhasználói fiókot használ egy internetböngészőben.

A hiba elhárításához próbálkozzon az alábbi módszerekkel:

- Ürítse ki a gyorsítótárat, és törölje az internetes cookie-kat. Az Internet Explorerben kattintson **Eszközök** > **Internetbeállítások** > **Törlés** elemre. Ügyeljen arra, hogy az átmeneti fájlok, cookie-k, a jelszó és a böngészési előzmények jelölőnégyzete be legyen jelölve, majd kattintson a Törlés gombra.
- Az Internet Explorer beállításainak alaphelyzetbe állításával visszaállíthatja az Ön által megadott személyes beállításokat. Kattintson az **Eszközök** > **Internetbeállítások** > **Speciális**, elemre, majd válassza a **Személyes beállítások törlése** >  **Alaphelyzetbe állítás** elemet.
- Használjon privát böngészési módot a böngészőben. Internet Explorer esetén: kattintson az **eszközök** > **biztonsági** > **InPrivate-böngészés**lehetőségre, majd keresse meg és jelentkezzen be a [Azure Portal](https://portal.azure.com/) vagy az [Azure Account Center webhelyre](https://account.azure.com/Subscriptions).

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>Be tudok jelentkezni, de a *Nem található előfizetés* hiba jelenik meg

Ez a probléma akkor fordul elő, ha nem a megfelelő címtárat választotta ki, vagy ha a fiókja nem rendelkezik megfelelő engedélyekkel.

**1. forgatókönyv:** Hibaüzenet érkezik a [Azure Portal](https://portal.azure.com/)

A probléma megoldása:

- A jobb felső sarokban kattintson a fiókjára, és ellenőrizze, hogy a megfelelő Azure-címtárat választotta-e ki.
- Ha a megfelelő Azure-címtár van kiválasztva, de továbbra is megjelenik a hibaüzenet, adja hozzá a fiókot [Tulajdonosként](add-change-subscription-administrator.md).

**2. forgatókönyv:** Hibaüzenet érkezik a [Azure Fiókközpont](https://account.windowsazure.com/Subscriptions)

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

## <a name="next-steps"></a>Következő lépések

- [Az Azure-számlázás dokumentációja](../../billing/index.md)
