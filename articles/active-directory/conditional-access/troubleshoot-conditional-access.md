---
title: Bejelentkezéssel kapcsolatos problémák elhárítása feltételes hozzáféréssel – Azure Active Directory
description: Ez a cikk azt ismerteti, mi a teendő, ha a feltételes hozzáférési szabályzatok váratlan eredményeket eredményeznek
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6778b556795f4e079100f1a7bcbb8b9465e9e315
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88032968"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>A feltételes hozzáféréssel kapcsolatos bejelentkezési problémák elhárítása

A cikkben található információk segítségével a feltételes hozzáféréssel kapcsolatos váratlan bejelentkezési eredmények a hibaüzenetek és az Azure AD bejelentkezési naplójának használatával is orvosolhatók.

## <a name="conditional-access-sign-in-interrupt"></a>Feltételes hozzáférés bejelentkezési megszakítása

Az első módszer a megjelenő hibaüzenet áttekintése. A webböngészők használatakor bejelentkező problémák esetén a hibaüzenetet tartalmazó oldal részletes információkkal rendelkezik. Ez az információ önmagában is leírja, hogy mi a probléma, és hogy lehetséges-e megoldást javasolni.

![Bejelentkezési hiba-kompatibilis eszköz szükséges](./media/troubleshoot-conditional-access/image1.png)

A fenti hiba esetén az üzenet azt jelzi, hogy az alkalmazás csak olyan eszközökről vagy ügyfélalkalmazások számára érhető el, amelyek megfelelnek a vállalat mobileszköz-kezelési házirendjének. Ebben az esetben az alkalmazás és az eszköz nem felel meg az adott szabályzatnak.

## <a name="azure-ad-sign-in-events"></a>Azure AD bejelentkezési események

A második módszer, amellyel részletes információkhoz juthat a bejelentkezési megszakításról, az Azure AD bejelentkezési eseményeinek áttekintésével ellenőrizheti, hogy melyik feltételes hozzáférési szabályzatot vagy házirendeket alkalmazták, és miért.

A problémáról további információt a kezdeti hiba oldalon található **További részletek** hivatkozásra kattintva találhat. A **További részletek** lehetőségre kattintva megtalálhatja azokat a hibaelhárítási információkat, amelyek hasznosak lehetnek az Azure ad bejelentkezési eseményeinek keresésekor a felhasználó által látott, illetve a Microsofttal kapcsolatos támogatási incidens megnyitásakor.

![További információ a feltételes hozzáférés megszakított webböngészőből való bejelentkezésről.](./media/troubleshoot-conditional-access/image2.png)

Annak megállapítása, hogy milyen feltételes hozzáférési szabályzatot vagy házirendeket alkalmaztak, és miért nem az alábbiakat.

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy globális olvasóként.
1. Tallózással keresse meg **Azure Active Directory**a  >  **bejelentkezéseket**.
1. Keresse meg az eseményt, amelyről át szeretné tekinteni a bejelentkezést. Szűrők és oszlopok hozzáadása vagy eltávolítása a szükségtelen információk kiszűréséhez.
   1. Szűrők hozzáadása a hatókör szűkítéséhez:
      1. **Korrelációs azonosító** , ha egy konkrét eseményt kell kivizsgálni.
      1. **Feltételes hozzáférés** a házirend hibájának és sikerességének megtekintéséhez. A szűrő hatóköre csak az eredmények korlátozására vonatkozó hibákat jeleníti meg.
      1. A **felhasználónevet** az adott felhasználókkal kapcsolatos információk megjelenítéséhez.
      1. A kérdéses időszakra **vonatkozó hatókör.**

   ![A feltételes hozzáférési szűrő kiválasztása a bejelentkezések naplójában](./media/troubleshoot-conditional-access/image3.png)

1. Ha a felhasználó bejelentkezési hibájának megfelelő bejelentkezési esemény található, válassza a **feltételes hozzáférés** lapot. A feltételes hozzáférés lapon megjelenik a bejelentkezési megszakítást eredményező házirend vagy szabályzat.
   1. A **Hibaelhárítás és támogatás** lapon található információk egyértelmű indoklást adhatnak arról, hogy miért nem sikerült a bejelentkezés, például olyan eszköz, amely nem felelt meg a megfelelőségi követelményeknek.
   1. Ha további vizsgálatot szeretne végezni, tekintse meg a házirendek konfigurációját a **házirend nevére**kattintva. A **szabályzat nevére** kattintva megtekintheti a házirend-konfiguráció felhasználói felületét a kiválasztott házirend felülvizsgálatához és szerkesztéséhez.
   1. A feltételes hozzáférési szabályzat értékeléséhez használt **ügyfél-felhasználó** és- **eszköz adatai** az **alapinformációk**, a **hely**, az **eszköz adatai**, a **hitelesítés részletei**és a bejelentkezési esemény **További részletek** lapjain is elérhetők.

### <a name="policy-details"></a>Szabályzat részletei

Ha kijelöli a házirend jobb oldalán lévő három pontot a bejelentkezési eseményben, a szabályzat részletei jelennek meg. Ez további információkat ad a rendszergazdáknak arról, hogy miért volt sikeres a szabályzat alkalmazása.

   ![Bejelentkezés az Event feltételes hozzáférés lapra](./media/troubleshoot-conditional-access/image5.png)

   ![Szabályzat részletei (előzetes verzió)](./media/troubleshoot-conditional-access/policy-details.png)

A bal oldali oldalon a bejelentkezéskor gyűjtött adatok szerepelnek, és a jobb oldalon részletesen ismertetjük, hogy az adatok megfelelnek-e az alkalmazott feltételes hozzáférési szabályzatok követelményeinek. A feltételes hozzáférési szabályzatok csak akkor érvényesek, ha az összes feltétel teljesül vagy nincs konfigurálva.

Ha az eseményben szereplő információk nem elegendőek a bejelentkezési eredmények megismeréséhez, vagy a szabályzatot úgy állítja be, hogy a kívánt eredményt kapja, akkor előfordulhat, hogy egy támogatási incidens is megnyitható. Navigáljon a bejelentkezési esemény **hibaelhárítási és támogatási** lapjára, és válassza az **új támogatási kérelem létrehozása**lehetőséget.

![A bejelentkezési esemény hibaelhárítás és támogatás lapja](./media/troubleshoot-conditional-access/image6.png)

Az incidens elküldésekor adja meg a kérelem AZONOSÍTÓját, valamint az időt és a dátumot az incidens beküldési adataiban található bejelentkezési eseményből. Ez az információ lehetővé teszi, hogy a Microsoft támogatási szolgálata megkeresse az érintett eseményt.

### <a name="conditional-access-error-codes"></a>Feltételes hozzáférési hibakódok

| Bejelentkezési hibakód | Hiba karakterlánca |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="next-steps"></a>További lépések

- [Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon](../reports-monitoring/concept-sign-ins.md)
- [Feltételes hozzáférés hibaelhárítása a What If eszköz használatával](troubleshoot-conditional-access-what-if.md)
- Ajánlott eljárások a [feltételes hozzáféréshez Azure Active Directory](best-practices.md)
