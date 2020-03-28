---
title: Az Azure Active Directory jelszó-visszaírásának engedélyezése
description: Ebben az oktatóanyagban megtudhatja, hogyan engedélyezheti az Azure AD önkiszolgáló jelszó-visszaállítási visszaírását az Azure AD Connect használatával a módosítások helyszíni Active Directory tartományi szolgáltatások környezetbe való visszakapcsolásához.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3578cb1326ebd701c3f00618c19a501a1476372
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80332126"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>Oktatóanyag: Engedélyezze az Azure Active Directory önkiszolgáló jelszó-visszaállítási visszaírását egy helyszíni környezetbe

Az Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás (SSPR) segítségével a felhasználók frissíthetik jelszavukat, vagy feloldhatják a fiók feloldását egy webböngésző használatával. Egy hibrid környezetben, ahol az Azure AD csatlakozik egy helyszíni Active Directory tartományi szolgáltatások (AD DS) környezetben, ez a forgatókönyv a jelszavak at a két könyvtár közötti eltérő.

A jelszó-visszaírás segítségével szinkronizálhatja a jelszóváltozásokat az Azure AD-ben a helyszíni AD DS-környezetben. Az Azure AD Connect biztonságos mechanizmust biztosít a jelszómódosítások visszaküldéséhez egy meglévő helyszíni címtárba az Azure AD-ből.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A jelszó-visszaíráshoz szükséges engedélyek konfigurálása
> * A jelszó-visszaírás engedélyezése az Azure AD Connectben
> * Jelszó-visszaírás engedélyezése az Azure AD SSPR-ben

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Egy működő Azure AD-bérlő, legalább próbaverziós licenccel.
    * Szükség esetén [hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
    * További információ: [Licensing requirements for Azure AD SSPR](concept-sspr-licensing.md).
* *Globális rendszergazdai* jogosultságokkal rendelkező fiók.
* Az Azure AD önkiszolgáló jelszó-visszaállításhoz konfigurálva.
    * Szükség esetén [végezze el az előző oktatóanyagot az Azure AD SSPR engedélyezéséhez.](tutorial-enable-sspr.md)
* Egy meglévő helyszíni AD DS-környezet, amely az Azure AD Connect aktuális verziójával van konfigurálva.
    * Szükség esetén konfigurálja az Azure AD Connectet az [Express](../hybrid/how-to-connect-install-express.md) vagy az [Egyéni](../hybrid/how-to-connect-install-custom.md) beállítások használatával.
    * A jelszóvisszaírás használatához a tartományvezérlőknek Windows Server 2008 R2 vagy újabb rendszerűeknek kell lenniük.

## <a name="configure-account-permissions-for-azure-ad-connect"></a>Fiókengedélyek konfigurálása az Azure AD Connecthez

Az Azure AD Connect segítségével szinkronizálhatja a felhasználókat, csoportokat és hitelesítő adatokat egy helyszíni Active Angeles-i Active Angeles-i környezet és az Azure AD között. Az Azure AD Connectet általában olyan Windows Server 2012-es vagy újabb számítógépre telepíti, amely a helyszíni AD DS-tartományhoz csatlakozik.

Az SSPR-visszaírás megfelelő működéséhez az Azure AD Connectben megadott fióknak rendelkeznie kell a megfelelő engedélyekkel és beállításokkal. Ha nem biztos abban, hogy melyik fiók van jelenleg használatban, nyissa meg az Azure AD Connect et, és válassza **az Aktuális konfiguráció megtekintése** lehetőséget. Az a fiók, amelyhez engedélyeket kell hozzáadnia, a **Szinkronizált könyvtárak**csoportban található. A fiókban a következő engedélyeket és beállításokat kell beállítani:

* **Új jelszó létrehozása**
* **Engedélyek írása**`lockoutTime`
* **Engedélyek írása**`pwdLastSet`
* **Kiterjesztett jogok** a "Le nem járt jelszó" számára a következő könyvön:
   * Az erdő *egyes tartományainak* gyökérobjektuma
   * Az SSPR hatókörében lenni kívánt felhasználói szervezeti egységek (Szervezeti egységek)

Ha nem rendeli hozzá ezeket az engedélyeket, úgy tűnik, hogy a visszaírás megfelelően van konfigurálva, de a felhasználók hibákat tapasztalnak, amikor a helyszíni jelszavakat a felhőből kezelik.

A jelszó-visszaíráshoz szükséges engedélyek beállításához hajtsa végre az alábbi lépéseket:

1. A helyszíni Active Directory tartományi szolgáltatások környezetében nyissa meg az **Active Directory – felhasználók és számítógépek beépülő modult** a megfelelő tartományi *rendszergazdai* engedélyekkel rendelkező fiókkal.
1. A **Nézet** menüben győződjön meg arról, hogy a **Speciális funkciók** be vannak kapcsolva.
1. A bal oldali panelen jelölje ki a tartomány gyökerét képviselő objektumot, és válassza a **Tulajdonságok** > **biztonsága** > **speciális**lehetőséget.
1. Az **Engedélyek** lapon válassza a **Hozzáadás gombot.**
1. A **Principal**, válassza ki a fiókot, amelyre az engedélyeket alkalmazni kell (az Azure AD Connect által használt fiók).
1. Az **Alkalmaz legördülő** listában válassza **a Leszármazott felhasználói objektumok lehetőséget.**
1. Az *Engedélyek csoportban*jelölje be a következő beállítások jelölőnégyzetét:
    * **Új jelszó létrehozása**
1. A *Tulajdonságok csoportban*jelölje be az alábbi beállítások mezőit. A lista görgetéséhez végig kell görgetnie a listát, hogy megtalálja ezeket a beállításokat, amelyek alapértelmezés szerint már be vannak állítva:
    * **LockoutTime írása**
    * **ÍrjpwdLastSet**

    [![](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png "Set the appropriate permissions in Active Users and Computers for the account that is used by Azure AD Connect")](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. Ha készen áll, válassza **az Alkalmaz / OK** lehetőséget a módosítások alkalmazásához, és lépjen ki a megnyitott párbeszédpanelekből.

Az engedélyek frissítésekor akár egy óra is eltelhet, amíg ezek az engedélyek replikálódnak a címtár összes objektumára.

A helyszíni AD DS környezetben lévő jelszóházirendek megakadályozhatják a jelszó-visszaállítások megfelelő feldolgozását. Ahhoz, hogy a jelszó-visszaírás a leghatékonyabban működjön, a *jelszó minimális élettartamára* vonatkozó csoportházirendet 0-ra kell állítani. Ez a beállítás a **Windows beállítások > > a Biztonsági beállítások > > a fiókházirendek** csoportban `gpedit.msc`található. 

Ha frissíti a csoportházirendet, várja meg, amíg `gpupdate /force` a frissített házirend replikálódik, vagy használja a parancsot.

> [!Note]
> A jelszavak azonnali módosításához a jelszóvisszaírást 0-ra kell állítani. Ha azonban a felhasználók betartják a helyszíni házirendeket, és a *jelszó minimális élettartama* nullánál nagyobb értékre van állítva, a jelszó-visszaírás továbbra is működni fog a helyszíni szabályzatok kiértékelése után. 

## <a name="enable-password-writeback-in-azure-ad-connect"></a>Jelszó-visszaírás engedélyezése az Azure AD Connectben

Az Azure AD Connect egyik konfigurációs lehetősége a jelszó-visszaírás. Ha ez a beállítás engedélyezve van, jelszómódosítási események hatására az Azure AD Connect szinkronizálja a frissített hitelesítő adatokat vissza a helyszíni ActiveÁd-ds-környezetben.

Az önkiszolgáló jelszó-visszaállítási visszaírás engedélyezéséhez először engedélyezze a visszaírási beállítást az Azure AD Connectben. Az Azure AD Connect-kiszolgálóról hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az Azure AD Connect-kiszolgálóra, és indítsa el az **Azure AD Connect** konfigurációs varázslót.
1. Az **üdvözlőlapon** kattintson a **Konfigurálás** gombra.
1. A **További feladatok** lapon válassza a **Szinkronizálási beállítások testreszabása** elemet, majd kattintson a **Tovább** gombra.
1. A Csatlakozás az **Azure AD-hez** lapon adja meg az Azure-bérlő globális rendszergazdai hitelesítő adatait, majd válassza a **Tovább**gombot.
1. A **Címtárak csatlakoztatása** és a **Tartomány/szervezeti egység** szűrőoldalakon kattintson a **Tovább** gombra.
1. A **Választható funkciók** lapon jelölje be a **Jelszóvisszaíró** melletti jelölőnégyzetet, és kattintson a **Tovább** gombra.

    ![Az Azure AD Connect konfigurálása jelszó-visszaíráshoz](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. A **Konfigurálásra kész** lapon kattintson a **Konfigurálás** gombra, és várja meg, amíg a folyamat véget ér.
1. Ha látja, hogy a konfigurálás befejeződött, kattintson a **Kilépés** gombra.

## <a name="enable-password-writeback-for-sspr"></a>Jelszó-visszaírás engedélyezése az SSPR-hez

Ha az Azure AD Connectben engedélyezve van a jelszó-visszaírás, most konfigurálja az Azure AD SSPR-t a visszaíráshoz. Ha engedélyezi az SSPR számára a jelszó-visszaírás használatát, a jelszóvisszaírást módosító vagy alaphelyzetbe állított felhasználók a frissített jelszót is szinkronizálják a helyszíni Active Angeles-beli Active Angeles-i Biztonsági szolgáltatások környezetében.

A jelszó-visszaírás engedélyezéséhez az SSPR-ben hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) egy globális rendszergazdai fiók használatával.
1. Keresse meg és válassza az **Azure Active Directory**lehetőséget, válassza a **Jelszó-visszaállítás**lehetőséget, majd válassza **a Helyszíni integráció lehetőséget.**
1. Állítsa be a **Jelszavak visszaírása a helyszíni címtárba beállítását,** *igen.*
1. Állítsa be a Fiókok zárolásának engedélyezése a felhasználók *Yes*számára a **fiókok zárolásának feloldását a jelszavuk alaphelyzetbe állítása nélkül?**

    ![Az Azure AD önkiszolgáló jelszó-visszaállításának engedélyezése jelszó-visszaíráshoz](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. Ha készen áll, válassza a **Mentés gombot.**

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a továbbiakban nem szeretné használni az oktatóanyag részeként konfigurált SSPR-visszaírási funkciót, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Keresse meg és válassza az **Azure Active Directory**lehetőséget, válassza a **Jelszó-visszaállítás**lehetőséget, majd válassza **a Helyszíni integráció lehetőséget.**
1. Állítsa be a **Jelszavak visszaírása a helyszíni címtárba beállítását,** amely *nem.*
1. Állítsa be a Fiókok zárolásának engedélyezése a felhasználók *No*számára a **fiók zárolásának feloldását a jelszó alaphelyzetbe állítása nélkül?**

Ha a továbbiakban nem szeretne semmilyen jelszófunkciót használni, hajtsa végre az alábbi lépéseket az Azure AD Connect-kiszolgálóról:

1. Jelentkezzen be az Azure AD Connect-kiszolgálóra, és indítsa el az **Azure AD Connect** konfigurációs varázslót.
1. Az **üdvözlőlapon** kattintson a **Konfigurálás** gombra.
1. A **További feladatok** lapon válassza a **Szinkronizálási beállítások testreszabása** elemet, majd kattintson a **Tovább** gombra.
1. A Csatlakozás az **Azure AD-hez** lapon adja meg az Azure-bérlő globális rendszergazdai hitelesítő adatait, majd válassza a **Tovább**gombot.
1. A **Címtárak csatlakoztatása** és a **Tartomány/szervezeti egység** szűrőoldalakon kattintson a **Tovább** gombra.
1. A **Választható szolgáltatások** lapon törölje a jelet a **Jelszó-visszaírás** jelölőnégyzetből, és válassza a **Tovább**gombot.
1. A **Konfigurálásra kész** lapon kattintson a **Konfigurálás** gombra, és várja meg, amíg a folyamat véget ér.
1. Ha látja, hogy a konfigurálás befejeződött, kattintson a **Kilépés** gombra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban engedélyezte az Azure AD SSPR-visszaírást egy helyszíni AD DS-környezetbe. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A jelszó-visszaíráshoz szükséges engedélyek konfigurálása
> * A jelszó-visszaírás engedélyezése az Azure AD Connectben
> * Jelszó-visszaírás engedélyezése az Azure AD SSPR-ben

> [!div class="nextstepaction"]
> [Kockázat értékelése bejelentkezéskor](tutorial-risk-based-sspr-mfa.md)
