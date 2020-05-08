---
title: Mi a csak feltételes hozzáférés jelentési módja? – Azure Active Directory
description: Hogyan használható a csak jelentés mód Súgó a feltételes hozzáférési házirend üzembe helyezéséhez
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c5643b0ce9d93c28372b30e6fc114816cafbce6
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690472"
---
# <a name="what-is-conditional-access-report-only-mode"></a>Mi a csak feltételes hozzáférés jelentési módja?

A feltételes hozzáférést ügyfeleink széles körben használják a megfelelő hozzáférés-vezérlés alkalmazásával a megfelelő körülmények között. A szervezet feltételes hozzáférési szabályzatának üzembe helyezése azonban az egyik kihívás, hogy meghatározza a végfelhasználók hatásait. Nehéz lehet előre megállapítani az általános telepítési kezdeményezések által érintett felhasználók számát és nevét, például a régi hitelesítés blokkolását, a többtényezős hitelesítés megkövetelését a felhasználók sokasága számára, vagy a bejelentkezési kockázati házirendek megvalósítását. 

A csak jelentési mód egy új feltételes hozzáférési szabályzat, amely lehetővé teszi a rendszergazdák számára a feltételes hozzáférési házirendek hatásának kiértékelését, mielőtt engedélyezné azokat a környezetében.  A csak jelentési mód kiadásával:

- A feltételes hozzáférési szabályzatok csak jelentési módban engedélyezhetők.
- Bejelentkezéskor a rendszer kiértékeli a szabályzatokat, de nem kényszeríti ki őket.
- Az eredmények naplózása a bejelentkezési napló részleteinek **feltételes hozzáférés** és **csak jelentés** lapjain történik.
- A Azure Monitor-előfizetéssel rendelkező ügyfelek a feltételes hozzáférési szabályzatok segítségével figyelheti a feltételes hozzáférési házirendek hatását.

> [!WARNING]
> A csak jelentési módban lévő szabályzatok, amelyek megfelelő eszközöket igényelnek, kérhetnek Mac, iOS és Android rendszerű felhasználókat, hogy kiválasszák az eszköz tanúsítványát a szabályzat kiértékelése során, annak ellenére, hogy az eszköz megfelelősége nem kényszerített. Ezek a kérések megismételhetők, amíg az eszköz nem megfelelő. Annak megakadályozása érdekében, hogy a végfelhasználók a bejelentkezéskor fogadják a kéréseket, kizárják az eszköz megfelelőségi ellenőrzéseit végző, csak jelentési szabályzatokat tartalmazó Mac, iOS és Android platformot.

![Csak jelentés lap az Azure AD bejelentkezési naplójában](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>Szabályzat eredményei

Ha egy adott bejelentkezésnél egy házirend csak jelentési módban van kiértékelve, akkor négy új lehetséges eredmény érhető el:

| Eredmény | Leírás |
| --- | --- |
| Csak jelentés: sikeres | Az összes konfigurált házirend-feltétel, a szükséges nem interaktív engedélyezési vezérlők és a munkamenet-vezérlők teljesültek. Ha például egy többtényezős hitelesítési követelményt egy olyan MFA-jogcím teljesít, amely már szerepel a jogkivonatban, vagy a megfelelő eszköz szabályzata megfelel a megfelelő eszközön lévő eszközök ellenőrzésének. |
| Csak jelentés: hiba | Az összes konfigurált házirend-feltétel teljesült, de nem az összes szükséges nem interaktív engedélyezési vezérlő vagy munkamenet-vezérlőelem teljesült. Egy szabályzat például egy olyan felhasználóra vonatkozik, amelyen a blokk vezérlőelem konfigurálva van, vagy ha egy eszköz nem felel meg a megfelelő eszköz házirendjének. |
| Csak jelentés: felhasználói beavatkozás szükséges | Az összes konfigurált házirend-feltétel teljesült, de felhasználói beavatkozásra lenne szükség a szükséges engedélyezési vezérlők vagy munkamenet-vezérlők kielégítéséhez. A csak jelentési mód használata esetén a rendszer nem kéri a felhasználót, hogy kielégítse a szükséges vezérlőket. A felhasználóknak például nem kell megadniuk a multi-Factor Authentication kihívásait vagy használati feltételeit.   |
| Csak jelentés: nincs alkalmazva | Nem minden konfigurált házirend-feltétel teljesült. Például a felhasználó ki van zárva a szabályzatból, vagy a házirend csak bizonyos megbízható elnevezett helyekre vonatkozik. |

## <a name="conditional-access-insights-workbook"></a>Feltételes hozzáférési adatellenőrzési munkafüzet

A rendszergazdák több szabályzatot hozhatnak létre csak jelentési módban, ezért az egyes házirendek egyéni hatásának megértéséhez, valamint a több, együttesen értékelt házirendek együttes hatásához is szükség van. Az új feltételes hozzáférési bejelentési munkafüzet lehetővé teszi a rendszergazdák számára a feltételes hozzáférési lekérdezések megjelenítését és a házirendek hatásának figyelését egy adott időtartományra, az alkalmazások és a felhasználók körére. 
 
## <a name="next-steps"></a>További lépések

[Csak jelentési mód konfigurálása feltételes hozzáférési házirendben](howto-conditional-access-report-only.md)
