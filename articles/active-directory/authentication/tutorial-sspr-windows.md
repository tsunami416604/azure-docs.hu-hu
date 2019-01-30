---
title: Azure AD SSPR a Windows 10 bejelentkezési képernyőjéről
description: Az oktatóanyagban engedélyezzük az új jelszó kérését a Windows 10 bejelentkezési képernyőjéről a telefonos ügyfélszolgálatra beérkező hívások számának csökkentése érdekében.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 12/05/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.openlocfilehash: 9375b042885459ab88b7c1f2a76a471da55ff5c8
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55209536"
---
# <a name="tutorial-azure-ad-password-reset-from-the-login-screen"></a>Oktatóanyag: Azure AD-jelszó visszaállítása a bejelentkezési képernyőről

Ebben az oktatóanyagban engedélyezi, hogy a felhasználók új jelszót kérjenek a Windows 10 bejelentkezési képernyőjéről. A Windows 10 2018. áprilisi frissítésében az **Azure AD-hez csatlakoztatott** vagy **hibrid Azure AD-hez csatlakoztatott** eszközökkel rendelkező felhasználók a bejelentkezési képernyőn egy „Új jelszó kérése” hivatkozást használhatnak. Erre kattintva eljuthatnak a már ismert, új jelszó önkiszolgáló kérésére (SSPR) szolgáló felülethez.

> [!div class="checklist"]
> * Jelszó-visszaállítási hivatkozás konfigurálása az Intune használatával
> * Másik konfigurálási lehetőség a Windows beállításjegyzék használatával
> * A felhasználók által látható elemek ismertetése

## <a name="prerequisites"></a>Előfeltételek

* Meg kell fut legalább Windows 10, a rendszer 2018 április frissítés, és az eszközök vagy kell lennie:
   * [Az Azure AD-hez csatlakoztatott](../device-management-azure-portal.md) vagy
   * [Hibrid Azure AD-hez csatlakoztatott](../device-management-hybrid-azuread-joined-devices-setup.md), a hálózati kapcsolat egy tartományvezérlőhöz.
* Engedélyeznie kell az Azure AD önkiszolgáló jelszó-visszaállítás.
* Ha a Windows 10 rendszerű eszközök proxykiszolgálót vagy tűzfal mögé, hozzá kell adnia az URL-címeket, `passwordreset.microsoftonline.com` és `ajax.aspnet.cdn.com` a HTTPS-forgalom (a 443-as port) engedélyezett URL-címek listájához.

## <a name="configure-reset-password-link-using-intune"></a>Jelszó-visszaállítási hivatkozás konfigurálása az Intune használatával

A legrugalmasabb módszer az, ha az Intune használatával telepíti a konfigurációs módosítást, amely engedélyezi az új jelszó kérését a bejelentkezési képernyőről. Az Intune lehetővé teszi, hogy a konfigurációs módosítást az Ön által meghatározott gépek adott csoportján telepítse. Ehhez a metódushoz regisztrálni kell az Intune-ba az eszközt.

### <a name="create-a-device-configuration-policy-in-intune"></a>Eszközkonfigurációs szabályzat létrehozása az Intune-ban

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) és kattintson az **Intune** elemre.
2. Hozzon létre egy új eszközkonfigurációs profilt az **Eszközkonfiguráció** > **Profilok** > **Profil létrehozása** paranccsal.
   * Adjon kifejező nevet a profilnak
   * Ha kívánja, megadhat egy kifejező leírást is a profilhoz
   * Platform: **Windows 10 és újabb**
   * Profiltípus: **Egyéni**

3. **Beállítások** konfigurálása
   * **Adja hozzá** a következő OMA-URI beállítást a Jelszó-visszaállítási hivatkozás használatának engedélyezéséhez
      * Adjon meg egy kifejező nevet, amelyből kiderül, hogy a beállítás mire szolgál
      * Ha kívánja, megadhat egy kifejező leírást is a beállításhoz
      * Állítsa az **OMA-URI** beállítást `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset` értékre
      * Állítsa az **Adattípus**beállítást **Egész szám** értékre
      * Állítsa az **Érték** beállítást **1** értékre
      * Kattintson az **OK** gombra
   * Kattintson az **OK** gombra
4. Kattintson a **Create** (Létrehozás) gombra

### <a name="assign-a-device-configuration-policy-in-intune"></a>Eszközkonfigurációs szabályzat hozzárendelése az Intune-ban

#### <a name="create-a-group-to-apply-device-configuration-policy-to"></a>Hozzon létre egy csoportot, amelyen alkalmazni szeretné az eszközkonfigurációs szabályzatot

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) és kattintson az **Azure Active Directory** elemre.
2. Lépjen a **Felhasználók és csoportok** > **Minden csoport** > **Új csoport** területre.
3. Adja meg a csoport nevét, majd a **Tagság típusa** beállításnál válassza a **Hozzárendelt** lehetőséget.
   * A **Tagok** területen válassza ki azokat az Azure AD-hoz csatlakoztatott Windows 10-eszközöket, amelyekre alkalmazni szeretné a szabályzatot.
   * Kattintson a **Kiválasztás** gombra.
4. Kattintson a **Create** (Létrehozás) gombra

A csoportok létrehozásával kapcsolatos további információkért lásd: [Erőforrások hozzáférésének kezelése Azure Active Directory-csoportokkal](../fundamentals/active-directory-manage-groups.md).

#### <a name="assign-device-configuration-policy-to-device-group"></a>Eszközkonfigurációs szabályzat hozzárendelése az eszközcsoporthoz

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) és kattintson az **Intune** elemre.
2. Keresse meg a korábban létrehozott eszközkonfigurációs profilt az **Eszközkonfiguráció** > **Profilok** elemre, majd a létrehozott profilra kattintva.
3. A profil hozzárendelése egy eszközcsoporthoz 
   * Kattintson a **Hozzárendelések** > **Belefoglalás** > **Belefoglalandó csoportok kiválasztása** elemre.
   * Válassza ki a korábban létrehozott csoportot, majd kattintson a **Kiválasztás** gombra.
   * Kattintson a **Mentés** gombra.

   ![Hozzárendelés][Assignment]

Ezzel az Intune használatával létrehozott és hozzárendelt egy eszközkonfigurációs szabályzatot, amely lehetővé teszi a bejelentkezési képernyő Új jelszó kérése hivatkozásának használatát.

## <a name="configure-reset-password-link-using-the-registry"></a>Jelszó-visszaállítási hivatkozás konfigurálása a Beállításjegyzék használatával

1. Jelentkezzen be a Windows rendszerű számítógépbe rendszergazdai hitelesítő adatokkal
2. Futtassa a **regedit** parancsot rendszergazdaként
3. Állítsa be a következő beállításkulcsot
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>Mit látnak a felhasználók?

Milyen változást tapasztalnak a felhasználók a szabályzat konfigurálása és hozzárendelése után? Honnan fogják tudni, hogy a bejelentkezési képernyőn új jelszót kérhetnek?

![Bejelentkezési képernyő][LoginScreen]

Amikor a felhasználók megpróbálnak bejelentkezni, a bejelentkezési képernyőn mostantól egy Új jelszó kérése hivatkozást láthatnak, amely megnyitja az önkiszolgáló jelszó-visszaállítási felületet. Ezzel a funkcióval a felhasználók visszaállíthatják a jelszavukat anélkül, hogy egy másik eszközt kellene használniuk egy webböngésző eléréséhez.

A felhasználók a funkcióval kapcsolatban a [Munkahelyi vagy iskolai jelszó visszaállítása](../user-help/active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in) témakörben találhatnak útmutatást.

Az Azure AD auditnaplója információkat tartalmaz az IP-címről és az ügyféltípusról, ahol az új jelszó kérése megtörtént.

![Példa új jelszó kérésére a bejelentkezési képernyőn az Azure AD auditnaplójában](media/tutorial-sspr-windows/windows-sspr-azure-ad-audit-log.png)

## <a name="limitations"></a>Korlátozások

A funkció a Hyper-V használatával történő tesztelésekor a „Jelszó visszaállítása” hivatkozás nem jelenik meg.

* Lépjen a teszteléshez használt virtuális géphez, kattintson a **Nézet** lehetőségre, majd távolítsa el a **Bővített munkamenet** lehetőség jelölőnégyzetét.

Ez a funkció a távoli asztal vagy egy virtuális gép bővített munkamenet tesztelésekor a "Jelszó visszaállítása" hivatkozás nem jelenik meg.

* A Távoli asztalokról jelenleg nem támogatott a jelszó-visszaállítás.

Ha a szabályzat a Ctrl+Alt+Del billentyűparancs használatát követeli meg, vagy a zárolási képernyő értesítései ki vannak kapcsolva, a **Jelszó visszaállítása** nem fog működni.

A következő házirend-beállításokat ismert zavarják a jelszavak alaphelyzetbe állítása

   * HideFastUserSwitching engedélyezve értékre van állítva vagy 1
   * DontDisplayLastUserName engedélyezve értékre van állítva vagy 1
   * NoLockScreen engedélyezve értékre van állítva vagy 1
   * EnableLostMode van beállítva, az eszközön
   * Az egyéni shell Explorer.exe váltja fel

Ez a funkció nem működik a hálózatok üzembe helyezett 802.1 x hálózati hitelesítést és az "Azonnali végrehajtás a felhasználó bejelentkezése előtt végezze el" lehetőséget. 802.1 x hálózati hitelesítéssel üzembe helyezett hálózatok ajánlott számítógép-hitelesítés használatával engedélyezze ezt a funkciót.

Tartományhoz hibrid forgatókönyvek esetén ez a forgatókönyv létezik, ahol végezze el az SSPR munkafolyamat anélkül, hogy egy Active Directory-tartományvezérlőhöz. Kapcsolat egy tartományvezérlőhöz először az új jelszó használata szükséges.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az oktatóanyag keretében konfigurált funkciót már nem szeretné tovább használni, törölje az Ön által létrehozott Intune eszközkonfigurációs profilt vagy a beállításkulcsot.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban engedélyezte, hogy a felhasználók új jelszót kérjenek a Windows 10 bejelentkezési képernyőjéről. A következő oktatóanyagból megtudhatja, hogyan integrálható az Azure Identity Protection az új jelszó önkiszolgáló kérésével és a többtényezős hitelesítéssel.

> [!div class="nextstepaction"]
> [Kockázat értékelése bejelentkezéskor](tutorial-risk-based-sspr-mfa.md)

[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "Intune eszközkonfigurációs szabályzat hozzárendelése Windows 10-eszközök egy csoportjához"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "Új jelszó kérésére szolgáló hivatkozás a Windows 10 bejelentkezési képernyőjén"
