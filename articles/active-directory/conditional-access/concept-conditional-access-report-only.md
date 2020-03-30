---
title: Mi a csak feltételes hozzáférésű jelentésmód? - Azure Active Directory
description: Hogyan segíthet a csak jelentéskészítő mód a feltételes hozzáférési házirend telepítésében?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd41e79a1e08c57e806f6ada32faccfa5fdf5792
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295286"
---
# <a name="what-is-conditional-access-report-only-mode"></a>Mi a csak feltételes hozzáférésű jelentésmód?

A feltételes hozzáférést ügyfeleink széles körben használják a biztonság megőrzéséhez a megfelelő hozzáférés-vezérlés alkalmazásával a megfelelő körülmények között. Azonban az egyik kihívás a feltételes hozzáférési szabályzat a szervezetben a végfelhasználókra gyakorolt hatás meghatározása. Nehéz lehet előre jelezni a felhasználók számát és nevét befolyásolja a közös telepítési kezdeményezések, például a régi hitelesítés blokkolása, igénylő többtényezős hitelesítés a felhasználók egy részét, vagy a bejelentkezési kockázati házirendek megvalósítása. 

A csak jelentésmód egy új feltételes hozzáférési házirend-állapot, amely lehetővé teszi a rendszergazdák számára, hogy kiértékeljék a feltételes hozzáférési házirendek hatását, mielőtt engedélyezték volna őket a környezetükben.  A csak jelentésmód kiadásával:

- A feltételes hozzáférési házirendek csak jelentésmódban engedélyezhetők.
- A bejelentkezés során a csak jelentésmódban lévő házirendek kiértékelése, de nem kényszerítve.
- Az eredmények a bejelentkezési napló részleteinek **feltételes hozzáférésés** **csak jelentés (előzetes verzió)** füleibe kerülnek naplózva.
- Az Azure Monitor-előfizetéssel rendelkező ügyfelek figyelhetik a feltételes hozzáférési szabályzatok hatását a feltételes hozzáférés elemzési munkafüzet használatával.

> [!WARNING]
> A csak jelentéskészítő módban lévő, megfelelő eszközöket igénylő házirendek arra késztethetik a maces, iOS- és Android-felhasználókat, hogy a házirend-értékelés során válasszanak ki egy eszköztanúsítványt, még akkor is, ha az eszköz megfelelősége nincs érvényben. Ezek az utasítások addig ismétlődhetnek, amíg az eszköz nem felel meg a követelményeknek. Ha meg szeretné akadályozni, hogy a végfelhasználók a bejelentkezés során utasításokat kapjanak, zárja ki a Mac, iOS és Android eszközplatformokat a csak jelentéskészítő szabályzatokból, amelyek eszközmegfelelőségi ellenőrzéseket hajtanak végre.

![Csak jelentéslap az Azure AD bejelentkezési naplójában](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>Szakpolitikai eredmények

Ha egy házirend csak jelentés módban értékeli ki egy adott bejelentkezés, négy új lehetséges eredményértékek:

| Eredmény | Leírás |
| --- | --- |
| Csak jelentés: Siker | Minden konfigurált házirend-feltétel, a szükséges nem interaktív támogatás-vezérlők és a munkamenet-vezérlők teljesültek. Például egy többtényezős hitelesítési követelmény teljesül egy MFA-jogcím már jelen van a jogkivonatban, vagy egy megfelelő eszköz szabályzat teljesül egy eszköz ellenőrzése egy megfelelő eszközön. |
| Csak jelentés: Hiba | Az összes konfigurált házirend-feltétel teljesült, de nem minden szükséges nem interaktív támogatási vezérlő vagy munkamenet-vezérlő teljesült. Például egy szabályzat egy olyan felhasználóra vonatkozik, ahol egy blokkvezérlő konfigurálva van, vagy egy eszköz nem felel meg a megfelelő eszközszabályzatnak. |
| Csak jelentés: Felhasználói beavatkozás szükséges | Minden konfigurált házirendfeltétel teljesült, de felhasználói beavatkozásra lenne szükség a szükséges támogatási vezérlők vagy munkamenet-vezérlők teljesítéséhez. A csak jelentés módban a rendszer nem kéri a felhasználót a szükséges vezérlők teljesítésére. A felhasználók például nem kérnek többtényezős hitelesítési kihívásokat vagy használati feltételeket.   |
| Csak jelentés: Nincs alkalmazva | Nem minden konfigurált házirendfeltétel teljesült. Például a felhasználó ki van zárva a szabályzatból, vagy a házirend csak bizonyos megbízható névvel ellátott helyekre vonatkozik. |

## <a name="conditional-access-insights-workbook"></a>Feltételes Access Insights-munkafüzet

A rendszergazdák képesek több szabályzatot létrehozni csak jelentés módban, ezért meg kell értenie az egyes házirendek egyéni hatását és a több szabályzat együttes en kiértékelt együttes hatását. Az új feltételes hozzáférés-insights-munkafüzet lehetővé teszi a rendszergazdák számára a feltételes hozzáférési lekérdezések megjelenítését, és figyelheti a házirend ek adott időtartományra, alkalmazáscsoportra és felhasználókra gyakorolt hatását. 
 
## <a name="next-steps"></a>További lépések

[Csak jelentésmód konfigurálása feltételes hozzáférési házirenden](howto-conditional-access-report-only.md)
