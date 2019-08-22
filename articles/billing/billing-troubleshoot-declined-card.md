---
title: Elutasított kártya hibáinak megoldása az Azure-regisztráció során
description: Az elutasított hitelkártya feloldása az Azure-regisztrációban az Azure Portal vagy a fiók központban.
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
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657059"
---
# <a name="troubleshoot-a-declined-card-at-azure-sign-up"></a>Elutasított kártya hibáinak megoldása az Azure-regisztráció során

Ez a cikk segít feloldani azokat a problémákat, amelyekben a Azure Portal vagy az Azure Account Center szolgáltatásban az Azure-regisztráció során a hitelkártyát elutasították. A probléma megoldásának megkezdése előtt tekintse át a következő szempontokat:

- Győződjön meg arról, hogy az Azure-fiók profiljához megadott információ, például a kapcsolattartási e-mail, a cím és a telefonszám helyes.
- Győződjön meg arról, hogy a hitelkártyaadatok helyesek.
- Győződjön meg arról, hogy ugyanezekkel az adatokkal még nem hozott létre Microsoft-fiókot.
- Bankkártyák nem fogadhatók el

## <a name="issues"></a>Problémák

Az alábbi gyakori problémák a hitelkártyák az Azure-regisztrációban való elutasításához vezethetnek.

### <a name="the-credit-card-provider-is-not-accepted-for-your-country"></a>Az Ön országa nem fogadja el a hitelkártya-szolgáltatót

Ha kártyát választ, az Azure megjeleníti a kiválasztott országban érvényes kártya beállításait. Lépjen kapcsolatba a bank vagy kártya kiállítóval annak ellenőrzéséhez, hogy a bankkártya engedélyezve van-e a nemzetközi tranzakciókhoz. A támogatott országokkal és pénznemekkel kapcsolatos további információkért tekintse meg az [Azure vásárlásával kapcsolatos gyakori kérdéseket](https://azure.microsoft.com/pricing/faq/).

>[!Note]
>Az American Express hitelkártyák jelenleg nem támogatottak az indiai fizetési eszközként. Nem rendelkezünk időkerettel, hogy mikor lehet elfogadható fizetési forma.

### <a name="youre-using-a-virtual-or-prepaid-card"></a>Virtuális vagy előre fizetett kártyát használ 

Az Azure-előfizetések esetében a virtuális vagy előre fizetett kreditet vagy bankkártyát nem fogadjuk el fizetőeszközként.

### <a name="your-credit-information-is-inaccurate-or-incomplete"></a>A kredit adatai pontatlanok vagy hiányosak 

A beírt névnek, címnek és CVV-kódnak pontosan meg kell egyeznie a kártyán kinyomtatott névvel.

### <a name="the-card-is-inactive-or-blocked"></a>A kártya inaktív vagy le van tiltva 

Lépjen kapcsolatba a bankkal, és ellenőrizze, hogy a kártya aktív-e.

Előfordulhat, hogy más regisztrációs problémák léptek fel 

Az Azure regisztrációs problémák elhárításával kapcsolatos további információkért tekintse meg a Tudásbázis következő cikkét: 

[Az Azure-ra nem regisztrálhat a Azure Portal vagy az Azure Account Center webhelyen](billing-troubleshoot-azure-sign-up.md)

### <a name="you-represent-a-business-that-doesnt-want-to-pay-by-card"></a>Olyan vállalatot képvisel, amely nem szeretne kártyán fizetni 

Ha Ön vállalatot képvisel, fizetési módokat, például csekkeket, egynapos ellenőrzéseket vagy átutalásokat használhat az Azure-előfizetése kifizetéséhez. Miután beállította a fiókot a számla számlázására, nem válthat másik fizetési lehetőségre, hacsak nem rendelkezik Microsoft-ügyféli szerződéssel, és az Azure-on keresztül regisztrált az Azure-ra.

További információ a számla fizetéséről: [kérelem beküldése az Azure-előfizetés számlázására](billing-how-to-pay-by-invoice.md).

### <a name="your-credit-card-information-is-outdated"></a>A bankkártya adatai elavultak 

A kártya adatainak kezelésével, például a kártyák módosításával vagy eltávolításával kapcsolatos további információkért lásd: [kreditek hozzáadása, frissítése vagy eltávolítása az Azure](billing-how-to-change-credit-card.md)-hoz.

## <a name="additional-help-resources"></a>További Súgó-erőforrások

Egyéb hibaelhárítási cikkek az Azure-számlázáshoz és-előfizetésekhez

- [Regisztrálással kapcsolatos problémák](billing-troubleshoot-azure-sign-up.md)
- [Előfizetés-bejelentkezési problémák](billing-troubleshoot-sign-in-issue.md)
- [Nem található előfizetés](billing-no-subscriptions-found.md)
- [Vállalati költségek nézet letiltva](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Segítségért forduljon hozzánk

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- [Az Azure számlázási dokumentációja](index.md)
