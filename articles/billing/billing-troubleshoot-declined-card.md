---
title: Az Azure-regisztráció során elutasított bankkártyával kapcsolatos probléma elhárítása
description: Az Azure Portalon vagy a Fiókközpontban az Azure-regisztráció során elutasított hitelkártyával kapcsolatos probléma megoldása.
author: v-miegge
manager: adpick
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: banders
ms.openlocfilehash: 730238d62e4ee4aad1807a4461c9b26ee1c8485d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "69657059"
---
# <a name="troubleshoot-a-declined-card-at-azure-sign-up"></a>Az Azure-regisztráció során elutasított bankkártyával kapcsolatos probléma elhárítása

Ez a cikk segít megoldani azt a problémát, amikor a rendszer elutasít egy hitelkártyát az Azure-regisztráció során az Azure Portalon vagy az Azure Fiókközpontban. Mielőtt megkezdi a hiba elhárítását, ellenőrizze a következőket:

- Győződjön meg arról, hogy az Azure-fiók profiljában megadott adatok, például a kapcsolattartási e-mail-cím, a cím és a telefonszám helyesek.
- Győződjön meg arról, hogy a hitelkártyaadatok helyesek.
- Győződjön meg arról, hogy ugyanezekkel az adatokkal még nem hozott létre Microsoft-fiókot.
- A bankkártyát nem fogadja el a rendszer

## <a name="issues"></a>Hibák

Az alábbiakban ismertetünk néhány gyakori problémát, amelyek a hitelkártya elutasításához vezethetnek az Azure-regisztráció során.

### <a name="the-credit-card-provider-is-not-accepted-for-your-country"></a>Nem elfogadott a hitelkártya-kibocsátó az adott ország esetében

A kártya kiválasztásakor az Azure megjeleníti a választott országban elfogadott kártyatípusokat. Lépjen kapcsolatba bankjával vagy a kártya kibocsátójával, és ellenőrizze, hogy a hitelkártya esetében engedélyezettek-e a nemzetközi tranzakciók. További információ a támogatott országokról és pénznemekről: [Azure-vásárlással kapcsolatos gyakori kérdések](https://azure.microsoft.com/pricing/faq/).

>[!Note]
>Az American Express hitelkártyák jelenleg nem minősülnek elfogadott fizetőeszköznek Indiában. Nem áll módunkban tudni, mikor válnak elfogadott fizetőeszközzé.

### <a name="youre-using-a-virtual-or-prepaid-card"></a>Virtuális kártya vagy feltöltőkártya használata 

A virtuális hitelkártyák vagy bankkártyák és a feltöltőkártyák nem használhatók fizetőeszközként Azure-előfizetésekhez.

### <a name="your-credit-information-is-inaccurate-or-incomplete"></a>Pontatlan vagy hiányos hitelkártyaadatok 

A névnek, címnek és a CVV-kódnak pontosan meg kell egyeznie a kártyán szereplő adatokkal.

### <a name="the-card-is-inactive-or-blocked"></a>A kártya nincs aktiválva vagy le van tiltva 

Lépjen kapcsolatba a bankkal, és ellenőrizze, hogy a kártyája aktiválva van-e.

Egyéb problémák is jelentkezhetnek a regisztrációval kapcsolatban. 

További információt az Azure-ba történő regisztráció során adódó problémák hibaelhárításával kapcsolatban a következő tudásbázisbeli cikkben talál: 

[Nem tud regisztrálni az Azure-ba az Azure Portalon vagy az Azure Fiókközpontban](billing-troubleshoot-azure-sign-up.md)

### <a name="you-represent-a-business-that-doesnt-want-to-pay-by-card"></a>Olyan vállalkozást képvisel, amely nem kíván kártyával fizetni 

Egy vállalkozás képviselőjeként más fizetési módok használatával, például csekken és banki átutalással is kiegyenlítheti az Azure-előfizetés díját. Ha a számlás fizetést választotta a fiók létrehozásakor, később nem válthat másik fizetési módra, kivéve, ha rendelkezik Microsoft-ügyfélszerződéssel, és az Azure webhelyén regisztrált az Azure-ba.

További információ a számlás fizetésről: [Az Azure-előfizetés számlás kifizetésére vonatkozó kérés elküldése](billing-how-to-pay-by-invoice.md).

### <a name="your-credit-card-information-is-outdated"></a>A hitelkártyaadatok lejártak 

További információ a kártyaadatok kezelésével kapcsolatban, beleértve a kártyák módosítását vagy eltávolítását: [Hitelkártya hozzáadása, frissítése vagy eltávolítása az Azure-ban](billing-how-to-change-credit-card.md).

## <a name="additional-help-resources"></a>További támogatási forrásanyagok

Az Azure-számlázással és -előfizetésekkel kapcsolatos további hibaelhárítási cikkek

- [Regisztrálással kapcsolatos problémák](billing-troubleshoot-azure-sign-up.md)
- [Bejelentkezéssel kapcsolatos problémák az előfizetésben](billing-troubleshoot-sign-in-issue.md)
- [Nem található előfizetés](billing-no-subscriptions-found.md)
- [Vállalati költségek nézet letiltva](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Ha segítségre van szüksége, vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- [Az Azure-számlázás dokumentációja](index.md)
