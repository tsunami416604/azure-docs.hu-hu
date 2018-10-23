---
title: Azure AD SSPR a Windows 10 bejelentkezési képernyőjéről
description: Az oktatóanyagban engedélyezzük az új jelszó kérését a Windows 10 bejelentkezési képernyőjéről a telefonos ügyfélszolgálatra beérkező hívások számának csökkentése érdekében.
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: fc4b2f153c89d4253bc3fdd9c5a49973baa38752
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310215"
---
# <a name="tutorial-azure-ad-password-reset-from-the-login-screen"></a>Oktatóanyag: Új Azure AD-jelszó kérése a bejelentkezési képernyőről

Ebben az oktatóanyagban engedélyezi, hogy a felhasználók új jelszót kérjenek a Windows 10 bejelentkezési képernyőjéről. A Windows 10 2018. áprilisi frissítésében az **Azure AD-hez csatlakoztatott** vagy **hibrid Azure AD-hez csatlakoztatott** eszközökkel rendelkező felhasználók a bejelentkezési képernyőn egy „Új jelszó kérése” hivatkozást használhatnak. Erre kattintva eljuthatnak a már ismert, új jelszó önkiszolgáló kérésére (SSPR) szolgáló felülethez.

> [!div class="checklist"]
> * Jelszó-visszaállítási hivatkozás konfigurálása az Intune használatával
> * Másik konfigurálási lehetőség a Windows beállításjegyzék használatával
> * A felhasználók által látható elemek ismertetése

## <a name="prerequisites"></a>Előfeltételek

* A Windows 10 2018. áprilisi verziója, vagy egy újabb ügyfél, amely:
   * [csatlakozik az Azure AD-hez](../device-management-azure-portal.md) vagy 
   * [csatlakozik a Hibrid Azure AD-hez](../device-management-hybrid-azuread-joined-devices-setup.md)
* Engedélyezni kell az Azure AD önkiszolgáló jelszó-visszaállítását.

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

## <a name="common-issues"></a>Gyakori problémák

A funkció a Hyper-V használatával történő tesztelésekor a „Jelszó visszaállítása” hivatkozás nem jelenik meg.

* Lépjen a teszteléshez használt virtuális géphez, kattintson a **Nézet** lehetőségre, majd távolítsa el a **Bővített munkamenet** lehetőség jelölőnégyzetét.

A funkció a Távoli asztal használatával történő tesztelésekor a „Jelszó visszaállítása” hivatkozás nem jelenik meg.

* A Távoli asztalokról jelenleg nem támogatott a jelszó-visszaállítás.

Ha a Windows zárolási képernyője egy beállításkulcs vagy csoportszabályzat használatával le lett tiltva, a **Jelszó visszaállítása** hivatkozás nem lesz elérhető.

Ha a szabályzat a Ctrl+Alt+Del billentyűparancs használatát követeli meg, vagy a zárolási képernyő értesítései ki vannak kapcsolva, a **Jelszó visszaállítása** nem fog működni. A Windows 10 19H1 megoldja ezt a követelményt.

Az Azure AD auditnaplója információkat tartalmaz az IP-címről és az ügyféltípusról, ahol az új jelszó kérése megtörtént.

![Példa új jelszó kérésére a bejelentkezési képernyőn az Azure AD auditnaplójában](media/tutorial-sspr-windows/windows-sspr-azure-ad-audit-log.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az oktatóanyag keretében konfigurált funkciót már nem szeretné tovább használni, törölje az Ön által létrehozott Intune eszközkonfigurációs profilt vagy a beállításkulcsot.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban engedélyezte, hogy a felhasználók új jelszót kérjenek a Windows 10 bejelentkezési képernyőjéről. A következő oktatóanyagból megtudhatja, hogyan integrálható az Azure Identity Protection az új jelszó önkiszolgáló kérésével és a többtényezős hitelesítéssel.

> [!div class="nextstepaction"]
> [Kockázat értékelése bejelentkezéskor](tutorial-risk-based-sspr-mfa.md)

[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "Intune eszközkonfigurációs szabályzat hozzárendelése Windows 10-eszközök egy csoportjához"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "Új jelszó kérésére szolgáló hivatkozás a Windows 10 bejelentkezési képernyőjén"
