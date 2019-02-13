---
title: Útmutató az Azure AD SSPR a jelszóvisszaíró konfigurálása
description: Az Azure AD és a jelszóvisszaíró jelszavait, hogy egy helyszíni címtár Azure AD Connect
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4543352fc87216458aa247f5eea0c8ff5980d0e8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209336"
---
# <a name="how-to-configure-password-writeback"></a>Útmutató: Jelszóvisszaíró konfigurálása

A következő lépések azt feltételezik, hogy már konfigurálta az Azure AD Connect a környezetében az a [Express](../hybrid/how-to-connect-install-express.md) vagy [egyéni](../hybrid/how-to-connect-install-custom.md) beállításait.

1. A jelszóvisszaíró konfigurálásához és engedélyezéséhez jelentkezzen be az Azure AD Connect-kiszolgálóra, és indítsa el az **Azure AD Connect** konfigurációs varázslóját.
2. Az **üdvözlőlapon** kattintson a **Konfigurálás** gombra.
3. A **További feladatok** lapon válassza a **Szinkronizálási beállítások testreszabása** elemet, majd kattintson a **Tovább** gombra.
4. A **Csatlakozás az Azure AD szolgáltatáshoz** lapon adja meg egy globális rendszergazda hitelesítő adatait, majd kattintson a **Tovább** gombra.
5. A **Címtárak csatlakoztatása** és a **Tartomány/szervezeti egység** szűrőoldalakon kattintson a **Tovább** gombra.
6. A **Választható funkciók** lapon jelölje be a **Jelszóvisszaíró** melletti jelölőnégyzetet, és kattintson a **Tovább** gombra.
   ![Az Azure AD Connect a jelszóvisszaírás engedélyezése][Writeback]
7. A **Konfigurálásra kész** lapon kattintson a **Konfigurálás** gombra, és várja meg, amíg a folyamat véget ér.
8. Ha látja, hogy a konfigurálás befejeződött, kattintson a **Kilépés** gombra.

A jelszóvisszaírás, kapcsolódó általános hibaelhárítási feladatokat című [a jelszóvisszaíró hibaelhárítása](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) hibaelhárítási című cikkben.

> [!WARNING]
> A jelszóvisszaíró régebbi során és az Azure AD Connect verziója 1.0.8641.0 használó ügyfeleink számára megszűnnek működni a [Azure Access Control service (ACS) 2018. November 7 kivonják](../develop/active-directory-acs-migration.md). Az Azure AD Connect verziója 1.0.8641.0 és a régebbi engedélyezi többé a jelszóvisszaíró adott időpontban, mert az a funkciók ACS függenek.
>
> A szolgáltatás, az Azure AD Connect egy korábbi verziójáról egy újabb verzióra frissítés elkerülése érdekében tekintse meg a cikket [az Azure AD Connect: frissítés egy előző verzióról a legújabbra](../hybrid/how-to-upgrade-previous-version.md)
>

## <a name="licensing-requirements-for-password-writeback"></a>Jelszóvisszaíró licencelési követelményeket

**Az önkiszolgáló jelszó alaphelyzetbe állítása/módosítás /-Zárolásfeloldás helyszíni visszaírással a rendszer egy Azure ad premium-funkció**. Licenceléssel kapcsolatos további információkért lásd: a [Azure Active Directory díjszabását ismertető a hely](https://azure.microsoft.com/pricing/details/active-directory/).

A jelszóvisszaíró használandó kell rendelkeznie a következő licenccel, a bérlő egyik:

* Prémium szintű Azure AD P1
* Prémium szintű Azure AD P2
* Enterprise Mobility + Security E3 vagy A3
* Enterprise Mobility + Security E5 vagy a5 csomag
* A Microsoft 365 E3 vagy A3
* A Microsoft 365 E5 vagy a5 csomag
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> Önálló Office 365 licencelési csomagok *nem támogatják a "Önkiszolgáló jelszó alaphelyzetbe állítása/módosítás /-Zárolásfeloldás helyszíni visszaírással az"* , és rendelkezik a fenti csomagok keretében, az a funkciók működéséhez szükséges.
>

## <a name="active-directory-permissions"></a>Az Active Directory-engedélyek

A fiók megadva, az Azure AD Connect eszközben a következő elemek be kell, ha az SSPR hatókörébe szeretné:

* **Új jelszó létrehozása** 
* **Jelszó módosítása** 
* **Írási engedélyekkel** a `lockoutTime`
* **Írási engedélyekkel** a `pwdLastSet`
* **Bővített jogosultságokat** a következők:
   * A legfelső szintű objektumához *minden egyes tartományhoz* az adott erdőben
   * Az SSPR hatókörébe kell szeretne a felhasználó szervezeti egységekhez (OU)

Ha nem biztos hivatkozik melyik fiókra az itt ismertetett fiókot, nyissa meg az Azure Active Directory Connect konfigurációs felhasználói Felületet, és válassza ki a **aktuális konfiguráció megtekintése** lehetőséget. A fiókot, amelyet kell hozzáadnia részen **szinkronizált címtárak**.

Ha ezeket az engedélyeket, az egyes erdők MA-szolgáltatásfiókja kezelheti jelszavakat az erdőben lévő felhasználói fiókok nevében. 

> [!IMPORTANT]
> Ha azt elmulasztják, a hozzá ezeket az engedélyeket, majd, annak ellenére, hogy a jelszóvisszaíró megfelelően kell konfigurálni jelenik meg felhasználók hibákat tapasztalnak, amikor azok megkísérlik a helyszíni jelszavak kezeléséhez a felhőben.
>

> [!NOTE]
> Azt is igénybe vehet egy óráig vagy tovább ezen engedélyeket a címtár összes objektumába replikálja.
>

Állítsa be a megfelelő engedélyekkel a jelszóvisszaírást, fordulhat elő, hajtsa végre az alábbi lépéseket:

1. Nyissa meg az Active Directory – felhasználók és számítógépek egy olyan fiókkal, amely a megfelelő tartományi rendszergazdai engedélyekkel rendelkezik.
2. Az a **nézet** menü, győződjön meg arról, **speciális szolgáltatások** be van kapcsolva.
3. A bal oldali panelen kattintson a jobb gombbal a tartomány, és válassza ki a legfelső szintű képviselő objektumot **tulajdonságok** > **biztonsági** > **speciális**.
4. Az a **engedélyek** lapon jelölje be **Hozzáadás**.
5. Válassza ki a fiókot, amelyet engedélyek vannak alkalmazva (az az Azure AD Connect beállítása).
6. Az a **vonatkozik** legördülő listában válassza **leszármazott felhasználó objektumai**.
7. A **engedélyek**, jelölje be a következő beállításokat:
    * **Jelszó módosítása**
    * **Új jelszó létrehozása**
8. A **tulajdonságok**, jelölje be a következő beállításokat:
    * **LockoutTime írása**
    * **PwdLastSet írása**
9. Válassza ki **alkalmaz/OK** alkalmazza a módosításokat, és lépjen ki az összes megnyitott párbeszédpanelen.

## <a name="next-steps"></a>További lépések

[Mi a jelszóvisszaíró?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Az Azure AD Connect a jelszóvisszaírás engedélyezése"
