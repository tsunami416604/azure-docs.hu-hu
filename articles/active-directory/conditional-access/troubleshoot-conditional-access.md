---
title: Feltételes hozzáféréssel kapcsolatos bejelentkezési problémák elhárítása – Azure Active Directory
description: Ez a cikk azt ismerteti, hogy mi a teendő, ha a feltételes hozzáférési házirendek váratlan eredményeket eredményeznek
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 03/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdf1daca79f3ed20d9b7a89af20d74ff5f3148b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337440"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Feltételes hozzáféréssel kapcsolatos bejelentkezési problémák elhárítása

A cikkben található információk segítségével elháríthatja a feltételes hozzáféréssel kapcsolatos váratlan bejelentkezési eredményeket hibaüzenetek és az Azure AD bejelentkezési napló használatával.

## <a name="conditional-access-sign-in-interrupt"></a>Feltételes hozzáférés bejelentkezési megszakítása

Az első módszer a megjelenő hibaüzenet áttekintése. A webböngésző használata kori bejelentkezéssel kapcsolatos problémákról maga a hibalap is részletes információkat tartalmaz. Ez az információ önmagában leírhatja, hogy mi a probléma, és ez megoldást jelenthet.

![Bejelentkezési hiba - megfelelő eszköz szükséges](./media/troubleshoot-conditional-access/image1.png)

A fenti hibaüzenet ben az üzenet azt írja, hogy az alkalmazás csak olyan eszközökről vagy ügyfélalkalmazásokból érhető el, amelyek megfelelnek a vállalat mobileszköz-kezelési szabályzatának. Ebben az esetben az alkalmazás és az eszköz nem felel meg a házirendnek.

## <a name="azure-ad-sign-in-events"></a>Azure AD bejelentkezési események

A második módszer a bejelentkezés megszakításával kapcsolatos részletes információk betekintése az Azure AD bejelentkezési események áttekintése, hogy mely feltételes hozzáférési szabályzat vagy szabályzatok alkalmazva, és miért.

További információ a problémáról a **Kezdeti** hibaoldalon található További részletek gombra kattintva található. A **További részletek** elemre kattintva olyan hibaelhárítási információkat talál, amelyek hasznosak az Azure AD bejelentkezési eseményeinek keresésekor a felhasználó által látott adott hibaeseményre vonatkozóan, vagy amikor támogatási incidenst nyit nak meg a Microsofttal.

![További részletek a feltételes hozzáférés megszakított webböngésző bejelentkezés.](./media/troubleshoot-conditional-access/image2.png)

Annak kiderítése, hogy mely feltételes hozzáférési házirendet vagy házirendeket alkalmazta, és miért az alábbiakat.

1. Jelentkezzen be az **Azure Portalon** globális rendszergazdaként, biztonsági rendszergazdaként vagy globális olvasóként.
1. Tallózással keresse meg az **Azure Active Directory** > **bejelentkezéseit.**
1. Keresse meg az eseményt a bejelentkezéshez, hogy vizsgálja felül. Szűrők és oszlopok hozzáadása vagy eltávolítása a szükségtelen információk kiszűréséhez.
   1. Szűrők hozzáadása a hatókör szűkítéséhez:
      1. **Korrelációs azonosító,** ha egy adott eseményt kell vizsgálnia.
      1. **Feltételes hozzáférés** a házirend sikertelenssé és sikerességéhez. A szűrő hatóköre úgy, hogy csak az eredmények korlátozása érdekében ne jelenjenek meg.
      1. **Felhasználónév** az adott felhasználókkal kapcsolatos információk megtekintéséhez.
      1. **A dátum** a kérdéses időkeretre terjed ki.

   ![A feltételes hozzáférés szűrő jének kiválasztása a bejelentkezési naplóban](./media/troubleshoot-conditional-access/image3.png)

1. Miután a felhasználó bejelentkezési hibájának megfelelő bejelentkezési esemény megtalálható, válassza a **Feltételes hozzáférés** lapot. A Feltételes hozzáférés lap a bejelentkezés megszakítását eredményező konkrét házirendeket vagy házirendeket jeleníti meg.
   1. A **Hibaelhárítás és támogatás** lapon található információk egyértelmű okot adhatnak arra, hogy egy bejelentkezés miért nem sikerült, például egy olyan eszköz, amely nem felelt meg a megfelelőségi követelményeknek.
   1. A további vizsgálathoz részletezze a házirendek konfigurációját a **Házirend neve gombra**kattintva. A **Házirend neve** gombra kattintva megjelenik a kijelölt házirend házirendkonfigurációs felhasználói felülete ellenőrzésre és szerkesztésre.
   1. A feltételes hozzáférési házirend-felméréshez használt **ügyfél-felhasználó** és **eszközadatok** a bejelentkezési esemény **Alapszintű információ**, **Hely**, **Eszközadatok**, **Hitelesítési részletek**és **További részletek** lapon is elérhetők.

   ![Bejelentkezési esemény feltételes hozzáférése lap](./media/troubleshoot-conditional-access/image5.png)

Ha az eseményben szereplő információk nem elegendőek a bejelentkezési eredmények megértéséhez vagy a szabályzat módosításához a kívánt eredmények elérése érdekében, akkor megnyitható egy támogatási esemény. Nyissa meg a bejelentkezési esemény **Hibaelhárítás és támogatás** lapját, és válassza **az Új támogatási kérelem létrehozása**lehetőséget.

![A Bejelentkezési esemény Hibaelhárítás és támogatás lapja](./media/troubleshoot-conditional-access/image6.png)

Az incidens elküldésekor adja meg a kérelem azonosítójának, valamint az incidens beküldése sta- és dátumának időpontját és dátumát. Ez az információ lehetővé teszi a Microsoft-támogatás számára, hogy megtalálja az Önt érdeklő eseményt.

### <a name="conditional-access-error-codes"></a>Feltételes hozzáférés hibakódjai

| Bejelentkezési hibakód | Hiba karakterlánc |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsnotAnApprovedApp |
| 53003 | Feltétel nélküli hozzáférés blokkolva |
| 53004 | ProofUpBlockedDuetorisk |

## <a name="next-steps"></a>További lépések

- [Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon](../reports-monitoring/concept-sign-ins.md)
- [Feltételes hozzáférés hibaelhárítása a Mi lenne, ha eszközzel](troubleshoot-conditional-access-what-if.md)
- Gyakorlati tanácsok a [feltételes hozzáféréshez az Azure Active Directoryban](best-practices.md)
