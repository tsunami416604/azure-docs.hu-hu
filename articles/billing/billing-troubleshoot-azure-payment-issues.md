---
title: Az Azure fizetési problémáinak elhárítása
description: Probléma megoldása a fizetési adatok fiókjának a Microsoft Azure Portal vagy a fiók központban való frissítésekor.
author: v-miegge
manager: na
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: jaserano
ms.openlocfilehash: 45f6f181ddbf7e76662f76c433d8ff68c18f0de1
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657085"
---
# <a name="troubleshoot-azure-payment-issues"></a>Az Azure fizetési problémáinak elhárítása

Problémát tapasztalhat, ha a Microsoft Azure Portal vagy az Azure Account Centerben lévő fizetési adatok fiókját próbálja meg frissíteni. A probléma megoldása előtt vegye figyelembe a következő irányelveket:

- Győződjön meg arról, hogy az Azure-fiók profiljához megadott információ (beleértve a kapcsolattartási e-mail címet, az utca címét és a telefonszámot) helyes.
- Győződjön meg arról, hogy a hitelkártyaadatok helyesek.
- Győződjön meg arról, hogy ugyanezekkel az adatokkal még nem hozott létre Microsoft-fiókot.

## <a name="issues"></a>Problémák

A hibák elhárításához válassza ki azt a problémát, amely az Azure-ba való regisztráció során jelentkezik.

### <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>Nem lehet hitelkártyát eltávolítani egy mentett Számlázási fizetési módszerből

A terv szerint nem távolíthat el bankkártyát az aktív előfizetésből.

Ha egy meglévő kártyát törölni kell, vagy új kártyát kell hozzáadni az előfizetéshez, hogy a régi fizetési eszköz sikeresen törölhető legyen, vagy le kell mondania az előfizetést. Ezzel véglegesen törli az előfizetést, és eltávolítja a kártyát.

### <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>Új fizetési mód hozzáadása után nem lehet törölni a régi fizetési módot

Előfordulhat, hogy az új fizetési eszköz nincs az előfizetéshez társítva. A fizetési eszköz az előfizetéssel való összerendeléséhez lásd: [kredit vagy bankkártya hozzáadása, frissítése vagy eltávolítása az Azure](billing-how-to-change-credit-card.md)-hoz.

Az elutasított kártyával kapcsolatos problémák elhárításáról lásd: [elutasított kártya hibaelhárítása Az Azure-regisztráció](billing-troubleshoot-declined-card.md)során.

### <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>Nem lehet törölni a fizetési módot, mert *nem lehet törölni a fizetési mód* hibáját.

Ez egy függőben lévő egyenleg miatt fordul elő. Törölje a fennmaradó egyenlegeket a fizetési mód törlése előtt.

### <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>Nem láthatók a fiókomban lévő előfizetések a fizetési mód frissítéséhez

Lehet, hogy olyan e-mail-azonosítót használ, amely eltér az előfizetésekhez használttól.

A probléma megoldásához tekintse meg az [Azure Portal vagy az Azure Account Center bejelentkezési hibáját](billing-no-subscriptions-found.md).

### <a name="unable-to-make-payment-for-a-subscription"></a>Nem lehet előfizetést fizetni

Ha a következő hibaüzenet jelenik meg: *A fizetési határidő lejárt. Probléma merült fel a fizetési móddal* kapcsolatban, vagy *Sajnáljuk, az adatok nem menthetők. Zárjuk be a böngészőt, és próbálkozzon újra.* , akkor a kártyán függőben lévő fizetés történik, mivel a kártyát a pénzügyi intézmény megtagadta.

Győződjön meg arról, hogy a hitelkártyának megfelelő egyenlege van a fizetéshez. Ha nem, használjon egy másik kártyát a fizetés elvégzéséhez, vagy forduljon a pénzügyi intézményhez a probléma megoldásához.

A következő problémákkal kapcsolatban érdeklődjön a bankkal:

- A nemzetközi tranzakciók nincsenek engedélyezve.
- A kártyának van hitelkerete, és az egyenleget rendezni kell.
- Az ismétlődő fizetés engedélyezve van a kártyán.

### <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>Nem lehet módosítani a fizetési módot a böngészőalapú problémák miatt (a böngésző nem válaszol, nem töltődik be, és így tovább)

Jelentkezzen ki az összes aktív Azure-munkamenetből, majd kövesse a [InPrivate böngészés a Microsoft Edge-ben című cikkben](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate) ismertetett lépéseket a Microsoft Edge vagy az Internet Explorer alkalmazáson belüli InPrivate-munkamenet elindításához.

A privát munkamenetben kövesse a hitelkártyák [módosítása](billing-how-to-change-credit-card.md) vagy a bankkártya adatainak módosítása című témakör lépéseit.

A következő műveleteket is elvégezheti:

- A böngésző frissítése
- Másik böngésző használata
- Gyorsítótárazott cookie-k törlése

### <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>A fizetési mód frissítése után a saját előfizetés továbbra is le van tiltva.

Ez a probléma a fennmaradó egyenleg miatt fordul elő. Törölje a fennmaradó egyenlegeket a fizetési mód törlése előtt.

### <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>A fizetési mód nem módosítható egy XML-hibaüzenetet tartalmazó oldal miatt

Ez az üzenet akkor jelenik meg, ha [a Azure Portal](https://portal.azure.com/) használatával új bankkártyát ad hozzá.

A kártya adatainak hozzáadásához jelentkezzen be az Azure-fiók portálra a fiók rendszergazdája e-mail-címének használatával.

## <a name="additional-help-resources"></a>További Súgó-erőforrások

Egyéb hibaelhárítási cikkek az Azure-számlázáshoz és-előfizetésekhez

- [Elutasított bankkártya](billing-troubleshoot-declined-card.md)
- [Előfizetés-bejelentkezési problémák](billing-troubleshoot-sign-in-issue.md)
- [Nem található előfizetés](billing-no-subscriptions-found.md)
- [Vállalati költségek nézet letiltva](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Segítségért forduljon hozzánk

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- [Az Azure számlázási dokumentációja](index.md)
