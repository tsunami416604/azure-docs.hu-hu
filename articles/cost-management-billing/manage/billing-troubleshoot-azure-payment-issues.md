---
title: Fizetéssel kapcsolatos problémák elhárítása az Azure-ban
description: A fizetési adatok Microsoft Azure Portalon vagy a Fiókközpontban történő frissítése során felmerülő problémák elhárítása.
author: v-miegge
ms.reviewerr: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 08/20/2020
ms.author: jaserano
ms.openlocfilehash: 77ca8e8425326cec31fce345600bdd0174ab5f4e
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131853"
---
# <a name="troubleshoot-azure-payment-issues"></a>Fizetéssel kapcsolatos problémák elhárítása az Azure-ban

Előfordulhat, hogy problémába vagy hibába ütközik fizetési adatai frissítése során a Microsoft Azure Portalon vagy az Azure Fiókközpontban.

A probléma megoldásához válassza ki az alábbi témák közül azt, amelyik leginkább hasonlít a hibára.

## <a name="my-credit-card-was-declined-when-i-tried-to-sign-up-for-azure"></a>A rendszer nem fogadja el a hitelkártyám, amikor az Azure-ra regisztrálok

Elutasított kártyával kapcsolatos hibák elhárításáért tekintse meg [az Azure-regisztráció során elutasított bankkártyával kapcsolatos probléma elhárítását](troubleshoot-declined-card.md) ismertető cikket.

## <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>A fiókhoz tartozó előfizetések nem láthatók a fizetési mód frissítése során

Előfordulhat, hogy az előfizetésekhez használttól eltérő e-mail-azonosítót használ.

A hiba elhárításához tekintse meg a [Nem található előfizetés bejelentkezési hiba az Azure Portalon vagy az Azure Fiókközpontban](no-subscriptions-found.md) című témakört.

## <a name="unable-to-use-a-virtual-or-prepaid-credit-or-debit-card-as-a-payment-method"></a>Virtuális hitelkártyák vagy bankkártyák és a feltöltőkártyák nem használhatók fizetési módként.

*   A virtuális hitelkártyák és a feltöltőkártyák nem használhatók fizetőeszközként Azure-előfizetésekhez.
*   A bankkártyák nem használhatók fizetőeszközként Azure-előfizetésekhez.

További információkért tekintse meg [az Azure-regisztráció során elutasított bankkártyával kapcsolatos problémák elhárítását](troubleshoot-declined-card.md) ismertető cikket.

## <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>Nem sikerült eltávolítani a hitelkártyát egy mentett számlázásos fizetési módból

A rendszer úgy van kialakítva, hogy az aktív előfizetéshez kapcsolt hitelkártyát ne lehessen eltávolítani.

Ha törölni szeretne egy meglévő kártyát, hozzá kell adnia egy új kártyát az előfizetéshez, és csak azt követően távolíthatja el a korábbi fizetőeszközt, vagy le kell mondania az előfizetést. Ez véglegesen törli az előfizetést, és eltávolítja a kártyát.

## <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>Nem lehet törölni a régi fizetési módot egy új hozzáadása után

Előfordulhat, hogy az új fizetési eszköz nincs az előfizetéshez társítva. Segítség a fizetőeszköz előfizetéshez való hozzárendeléséhez: [Hitelkártya hozzáadása, frissítése vagy eltávolítása az Azure-ban](change-credit-card.md).

## <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>A fizetési mód törlése meghiúsul a *Nem lehet törölni a fizetési módot* hiba miatt

Ennek oka egy fennálló tartozás. A fizetési mód törlése előtt egyenlítse ki tartozását.

## <a name="unable-to-make-payment-for-a-subscription"></a>Az előfizetés díjának befizetése meghiúsul

Előfordulhat, hogy a következő hibaüzeneteket kapja: *Lejárt a kifizetés esedékessége. Probléma van a fizetési móddal* vagy *Az információ nem menthető. Zárja be a böngészőt, majd próbálkozzon újra.* Ez azt jelenti, hogy a kártyán függőben lévő fizetési kérelem van, mert a kifizetést a pénzintézet megtagadta.

Győződjön meg arról, hogy az egyenlege elegendő a kifizetéshez. Ha nem, használjon egy másik kártyát a fizetéshez, vagy lépjen kapcsolatba a pénzintézettel a probléma megoldása érdekében.

A következő problémákról érdeklődjön bankjánál:

- Nincsenek engedélyezve a nemzetközi tranzakciók.
- A kártyáján beállított hitelkeret nem elegendő a kifizetéshez.
- Egy rendszeres fizetési megbízás van engedélyezve a kártyán.

## <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>A fizetési mód böngészőproblémák miatt nem módosítható (a böngésző nem reagál, nem töltődik be stb.)

Jelentkezzen ki az összes aktív Azure munkamenetből, majd [a Microsoft Edge-ben való InPrivate-böngészéssel kapcsolatos cikk](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate) lépéseit követve indítson egy InPrivate-munkamenetet Microsoft Edge-ben vagy Internet Explorerben.

A privát munkamenetben kövesse a [hitelkártya módosításával](change-credit-card.md) kapcsolatos témakör utasításait a hitelkártyaadatok frissítéséhez vagy módosításához.

Ezenkívül a következőket is kipróbálhatja:

- A böngésző frissítése
- Másik böngésző használata
- A gyorsítótárazott cookie-k törlése

## <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>Az előfizetésem továbbra is le van tiltva a fizetési mód frissítése után.

A probléma oka egy fennálló tartozás. A fizetési mód törlése előtt egyenlítse ki tartozását.

## <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>A fizetési módot nem lehet módosítani egy XML-hiba válaszlapja miatt

Akkor jelenik meg ez az üzenet, ha az [Azure Portalon](https://portal.azure.com/) próbál új hitelkártyát megadni.

A kártyaadatok megadásához jelentkezzen be az Azure-fiókportálra a fiókadminisztrátor e-mail-címével.

## <a name="additional-help-resources"></a>További támogatási forrásanyagok

Az Azure-számlázással és -előfizetésekkel kapcsolatos további hibaelhárítási cikkek

- [Elutasított bankkártya](troubleshoot-declined-card.md)
- [Bejelentkezéssel kapcsolatos problémák az előfizetésben](troubleshoot-sign-in-issue.md)
- [Nem található előfizetés](no-subscriptions-found.md)
- [Vállalati költségek nézet letiltva](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Ha segítségre van szüksége, vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- [Az Azure-számlázás dokumentációja](../index.yml)