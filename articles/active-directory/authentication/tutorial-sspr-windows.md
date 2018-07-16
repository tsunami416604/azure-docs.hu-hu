---
title: Azure AD SSPR a Windows 10 bejelentkezési képernyőjéről | Microsoft Docs
description: A Windows 10 bejelentkezési képernyő Azure AD-jelszóvisszaállítási és „Elfelejtettem a PIN-kódom” funkcióinak konfigurálása
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 04/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 3b767da394f6337a1110ad171f19ad789fa044d2
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972981"
---
# <a name="azure-ad-password-reset-from-the-login-screen"></a>Azure AD-jelszó visszaállítása a bejelentkezési képernyőről

Már üzembe helyezte az Azure AD önkiszolgáló jelszó-visszaállítás (SSPR) szolgáltatását, de a felhasználók még mindig az ügyfélszolgálathoz fordulnak, ha elfelejtették a jelszavukat. Azért tesznek így, mert nem tudnak hozzáférni a böngészőhöz az SSPR eléréséhez.

A Windows 10 2018. áprilisi frissítésében az **Azure AD-hez csatlakoztatott** vagy **hibrid Azure AD-hez csatlakoztatott** eszközökkel rendelkező felhasználók a bejelentkezési képernyőn egy „Jelszó visszaállítása” hivatkozást láthatnak és használhatnak. Erre kattintva eljuthatnak a már ismert önkiszolgáló jelszó-visszaállítási (SSPR) felülethez.

Ahhoz, hogy a felhasználók visszaállíthassák az Azure AD-jelszavukat a Windows 10 bejelentkezési képernyőjéről, a következő követelményeknek kell teljesülniük:

* A Windows 10 2018. áprilisi verziója, vagy egy újabb, az [Azure AD-hez csatlakoztatott](../device-management-azure-portal.md) vagy a [hibrid Azure AD-hez csatlakoztatott](../device-management-hybrid-azuread-joined-devices-setup.md) ügyfél.
* Engedélyezni kell az Azure AD önkiszolgáló jelszó-visszaállítását.
* A Jelszó visszaállítása hivatkozás használatát engedélyező beállítást a következő módokon lehet konfigurálni és üzembe helyezni:
   * [Intune eszközkonfigurációs profil](tutorial-sspr-windows.md#configure-reset-password-link-using-intune). Ehhez a metódushoz regisztrálni kell az Intune-ba az eszközt.
   * [Beállításkulcs](tutorial-sspr-windows.md#configure-reset-password-link-using-the-registry)

## <a name="configure-reset-password-link-using-intune"></a>Jelszó-visszaállítási hivatkozás konfigurálása az Intune használatával

### <a name="create-a-device-configuration-policy-in-intune"></a>Eszközkonfigurációs szabályzat létrehozása az Intune-ban

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com) és kattintson az **Intune** elemre.
2. Hozzon létre egy új eszközkonfigurációs profilt az **Eszközkonfiguráció** > **Profilok** > **Profil létrehozása** paranccsal.
   * Adjon kifejező nevet a profilnak
   * Ha kívánja, megadhat egy kifejező leírást is a profilhoz
   * Platform: **Windows 10 és újabb**
   * Profiltípus: **Egyéni**

   ![Profil létrehozása][CreateProfile]

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

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com) és kattintson az **Azure Active Directory** elemre.
2. Lépjen a **Felhasználók és csoportok** > **Minden csoport** > **Új csoport** területre.
3. Adja meg a csoport nevét, majd a **Tagság típusa** beállításnál válassza a **Hozzárendelt** lehetőséget.
   * A **Tagok** területen válassza ki azokat az Azure AD-hoz csatlakoztatott Windows 10-eszközöket, amelyekre alkalmazni szeretné a szabályzatot.
   * Kattintson a **Kiválasztás** gombra.
4. Kattintson a **Create** (Létrehozás) gombra

A csoportok létrehozásával kapcsolatos további információkért lásd: [Erőforrások hozzáférésének kezelése Azure Active Directory-csoportokkal](../fundamentals/active-directory-manage-groups.md).

#### <a name="assign-device-configuration-policy-to-device-group"></a>Eszközkonfigurációs szabályzat hozzárendelése az eszközcsoporthoz

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com) és kattintson az **Intune** elemre.
2. Keresse meg a korábban létrehozott eszközkonfigurációs profilt az **Eszközkonfiguráció** > **Profilok** elemre, majd a létrehozott profilra kattintva.
3. A profil hozzárendelése egy eszközcsoporthoz 
   * Kattintson a **Hozzárendelések** > **Belefoglalás** > **Belefoglalandó csoportok kiválasztása** elemre.
   * Válassza ki a korábban létrehozott csoportot, majd kattintson a **Kiválasztás** gombra.
   * Kattintson a **Mentés** gombra.

   ![Hozzárendelés][Assignment]

Ezzel az Intune használatával létrehozott és hozzárendelt egy eszközkonfigurációs szabályzatot, amely lehetővé teszi a bejelentkezési képernyő Jelszó-visszaállítási hivatkozás használatát.

## <a name="configure-reset-password-link-using-the-registry"></a>Jelszó-visszaállítási hivatkozás konfigurálása a Beállításjegyzék használatával

Javasoljuk, hogy ezt a módszert csak a beállítás módosításának teszteléséhez használja.

1. Jelentkezzen be az Azure AD-hez csatlakoztatott eszközre rendszergazdai hitelesítő adatokkal
2. Futtassa a **regedit** parancsot rendszergazdaként
3. Állítsa be a következő beállításkulcsot
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>Mit látnak a felhasználók?

Milyen változást tapasztalnak a felhasználók a szabályzat konfigurálása és hozzárendelése után? Honnan fogják tudni, hogy a bejelentkezési képernyőn visszaállíthatják a jelszavukat?

![Bejelentkezési képernyő][LoginScreen]

Amikor a felhasználók megpróbálnak bejelentkezni, a bejelentkezési képernyőn mostantól egy Jelszó visszaállítása hivatkozást láthatnak, amely megnyitja az önkiszolgáló jelszó-visszaállítási felületet. Ezzel a funkcióval a felhasználók visszaállíthatják a jelszavukat anélkül, hogy egy másik eszközt kellene használniuk egy webböngésző eléréséhez.

A felhasználók a funkcióval kapcsolatban a [Munkahelyi vagy iskolai jelszó visszaállítása](../active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in) témakörben találhatnak útmutatást.

## <a name="common-issues"></a>Gyakori problémák

A funkció a Hyper-V használatával történő tesztelésekor a „Jelszó visszaállítása” hivatkozás nem jelenik meg.

* Lépjen a teszteléshez használt virtuális géphez, kattintson a **Nézet** lehetőségre, majd távolítsa el a **Bővített munkamenet** lehetőség jelölőnégyzetét.

A funkció a Távoli asztal használatával történő tesztelésekor a „Jelszó visszaállítása” hivatkozás nem jelenik meg.

* A Távoli asztalokról jelenleg nem támogatott a jelszó-visszaállítás.

## <a name="next-steps"></a>További lépések

Az alábbi hivatkozásokat követve az Azure AD jelszóátállításáról olvashat további információkat.

* [Az SSPR üzembe helyezése](howto-sspr-deployment.md)
* [Új PIN-kód kérésének engedélyezése a bejelentkezési képernyőről](https://docs.microsoft.com/intune/device-windows-pin-reset)
* [További információk az MDM hitelesítési szabályzatokról](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-authentication)

[CreateProfile]: ./media/tutorial-sspr-windows/create-profile.png "Intune eszközkonfigurációs profil létrehozása jelszó-visszaállítási hivatkozása engedélyezéséhez a Windows 10 bejelentkezési képernyőjén"
[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "Intune eszközkonfigurációs szabályzat hozzárendelése Windows 10-eszközök egy csoportjához"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "Jelszó-visszaállítási hivatkozás a Windows 10 bejelentkezési képernyőjén"
