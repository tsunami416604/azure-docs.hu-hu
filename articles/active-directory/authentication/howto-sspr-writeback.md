---
title: A jelszó-visszaírási konfigurálása az Azure AD SSPR – Azure Active Directory
description: Az Azure AD és a Azure AD Connect használata jelszavak visszaírási egy helyszíni címtárba
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
ms.openlocfilehash: 9e12fe38ba69f6ac8f27130e01baff0c358aa409
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021773"
---
# <a name="how-to-configure-password-writeback"></a>Útmutató: a jelszó visszaírási konfigurálása

Az alábbi lépések feltételezik, hogy már konfigurálta Azure AD Connect a környezetben az [expressz](../hybrid/how-to-connect-install-express.md) vagy az [Egyéni](../hybrid/how-to-connect-install-custom.md) beállítások használatával.

1. A jelszóvisszaíró konfigurálásához és engedélyezéséhez jelentkezzen be az Azure AD Connect-kiszolgálóra, és indítsa el az **Azure AD Connect** konfigurációs varázslóját.
2. Az **üdvözlőlapon** kattintson a **Konfigurálás** gombra.
3. A **További feladatok** lapon válassza a **Szinkronizálási beállítások testreszabása** elemet, majd kattintson a **Tovább** gombra.
4. A **Csatlakozás az Azure AD szolgáltatáshoz** lapon adja meg egy globális rendszergazda hitelesítő adatait, majd kattintson a **Tovább** gombra.
5. A **Címtárak csatlakoztatása** és a **Tartomány/szervezeti egység** szűrőoldalakon kattintson a **Tovább** gombra.
6. A **Választható funkciók** lapon jelölje be a **Jelszóvisszaíró** melletti jelölőnégyzetet, és kattintson a **Tovább** gombra.
   ![Jelszó visszaírási engedélyezése a Azure AD Connectban][Writeback]
7. A **Konfigurálásra kész** lapon kattintson a **Konfigurálás** gombra, és várja meg, amíg a folyamat véget ér.
8. Ha látja, hogy a konfigurálás befejeződött, kattintson a **Kilépés** gombra.

A jelszó-visszaírási kapcsolatos gyakori hibaelhárítási feladatokért tekintse meg a hibaelhárítási cikkben a [jelszó visszaírási hibaelhárítása](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) című szakaszt.

> [!WARNING]
> A jelszó-visszaírási nem fog működni azon ügyfelek esetében, akik az [Azure Access Control szolgáltatás (ACS) 2018](../develop/active-directory-acs-migration.md)-as 1.0.8641.0-verziójának kivonása után a Azure ad Connect verziót használják. Azure AD Connect a 1.0.8641.0 és régebbi verziók többé nem engedélyezik a jelszavak visszaírási, mert az adott funkcióhoz tartozó ACS-től függenek.
>
> A szolgáltatás megszakadásának elkerülése érdekében frissítse a Azure AD Connect egy korábbi verziójáról egy újabb verzióra, és tekintse meg a [Azure ad Connect: frissítés korábbi verzióról a legújabbra](../hybrid/how-to-upgrade-previous-version.md) című cikket.
>

## <a name="licensing-requirements-for-password-writeback"></a>A jelszó-visszaírási licencelési követelményei

Az **önkiszolgáló jelszó-visszaállítás, illetve a helyszíni visszaírási való módosítás/feloldás az Azure ad prémium funkciója**. A licenceléssel kapcsolatos további információkért tekintse meg a [Azure Active Directory díjszabási webhelyét](https://azure.microsoft.com/pricing/details/active-directory/).

A jelszó-visszaírási használatához a bérlőhöz hozzárendelt következő licencek egyike szükséges:

* Prémium szintű Azure AD P1
* Prémium szintű Azure AD P2
* E3 vagy a3 Enterprise Mobility + Security
* Enterprise Mobility + Security E5 vagy a5
* E3 vagy a3 Microsoft 365
* Microsoft 365 E5 vagy a5
* Microsoft 365 F1
* Microsoft 365 Vállalati verzió

> [!WARNING]
> Az önálló Office 365 licencelési csomagok *nem támogatják az "önkiszolgáló jelszó-visszaállítás/módosítás/zárolás a helyszíni visszaírási"* lehetőséget, és a funkció működéséhez a fenti csomagok egyikét kell megkövetelni.
>

## <a name="active-directory-permissions-and-on-premises-password-complexity-policies"></a>Active Directory engedélyek és a helyszíni jelszó bonyolultságára vonatkozó szabályzatok 

A Azure AD Connect segédprogramban megadott fióknak a következő elemeknek kell megadnia, ha a SSPR hatókörében szeretné használni:

* **Új jelszó létrehozása** 
* **Jelszó módosítása** 
* **Írási engedélyek** `lockoutTime`
* **Írási engedélyek** `pwdLastSet`
* **Kiterjesztett jogosultságok** a következők közül:
   * Az erdő *minden tartományának* legfelső szintű objektuma
   * A SSPR hatókörében használni kívánt felhasználói szervezeti egységek (OU-ket)

Ha nem biztos abban, hogy milyen fiókra hivatkozik a leírt fiók, nyissa meg a Azure Active Directory Connect konfigurációs felhasználói felületet, és válassza a **jelenlegi konfiguráció megtekintése** lehetőséget. Az a fiók, amelyhez engedélyeket kell hozzáadnia, megjelenik a **szinkronizált könyvtárak**területen.

Ha ezeket az engedélyeket állítja be, az egyes erdőkhöz tartozó MA-szolgáltatásfiók az adott erdőben lévő felhasználói fiókok nevében képes kezelni a jelszavakat. 

> [!IMPORTANT]
> Ha nem szeretné hozzárendelni ezeket az engedélyeket, akkor annak ellenére, hogy a visszaírási megfelelően van konfigurálva, a felhasználók hibákat fognak tapasztalni, amikor megkísérlik a felhőből kezelni a helyszíni jelszavukat.
>

> [!NOTE]
> Ezekhez az engedélyekhez akár egy óráig is eltarthat, hogy az engedélyek a címtárban lévő összes objektumra replikálódnak.
>

A jelszó visszaírási vonatkozó megfelelő engedélyek beállításához hajtsa végre a következő lépéseket:

1. Nyisson meg Active Directory felhasználókat és számítógépeket egy olyan fiókkal, amely rendelkezik a megfelelő tartományi rendszergazdai engedélyekkel.
2. A **nézet** menüben ellenőrizze, hogy be van-e kapcsolva a **Speciális funkciók** .
3. A bal oldali panelen kattintson a jobb gombbal arra az objektumra, amely a tartomány gyökerét jelöli, majd válassza a **tulajdonságok** > **biztonsági** > **speciális**lehetőséget.
4. Az **engedélyek** lapon válassza a **Hozzáadás**lehetőséget.
5. Válassza ki azt a fiókot, amelyre az engedélyek vonatkoznak (a Azure AD Connect telepítőből).
6. Az **érintettek** legördülő listában válassza a **leszármazott felhasználói objektumok**elemet.
7. Az **engedélyek**területen jelölje be a következő beállításokhoz tartozó mezőket:
    * **Jelszó módosítása**
    * **Új jelszó létrehozása**
8. A **Tulajdonságok**területen jelölje be a következő beállításokhoz tartozó mezőket:
    * **LockoutTime írása**
    * **PwdLastSet írása**
9. Válassza az **alkalmaz/ok** lehetőséget a módosítások alkalmazásához és a megnyitott párbeszédpanelek bezárásához.

Mivel a hatóság forrása a helyszínen található, a jelszó-összetettségi szabályzatok ugyanarra a csatlakoztatott adatforrásra vonatkoznak. Ellenőrizze, hogy módosította-e a meglévő csoportházirendeket a "jelszó minimális kora" értékre. A csoportházirend nem állítható be 1 értékre, ami azt jelenti, hogy a jelszónak legalább egy nappal korábbinak kell lennie ahhoz, hogy frissíteni lehessen. Győződjön meg arról, hogy 0-ra van beállítva. Ezek a beállítások a `gpmc.msc` **Számítógép konfigurációja > házirendek > Windows beállítások > biztonsági beállítások > fiók házirendek**területen találhatók. `gpupdate /force` futtatásával győződjön meg arról, hogy a módosítás érvénybe lép. 

## <a name="next-steps"></a>Következő lépések

[Mi a jelszó visszaírási?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Jelszó visszaírási engedélyezése a Azure AD Connectban"
