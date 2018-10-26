---
title: Útmutató az Azure AD SSPR a jelszóvisszaíró konfigurálása
description: Az Azure AD és a jelszóvisszaíró jelszavait, hogy egy helyszíni címtár Azure AD Connect
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: e8a09a9fc87bff692b5d5b4c54f87839f2591b63
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086947"
---
# <a name="how-to-configure-password-writeback"></a>Útmutató: A jelszóvisszaírás konfigurálása

Azt javasoljuk, hogy az automatikus frissítési funkcióját használja [az Azure AD Connect](../hybrid/how-to-connect-install-express.md) jelszóvisszaíró használata esetén.

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
