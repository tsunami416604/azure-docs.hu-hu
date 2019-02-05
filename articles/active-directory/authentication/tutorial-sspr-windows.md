---
title: Azure AD SSPR a Windows 10 bejelentkezési képernyőjéről
description: Az oktatóanyagban engedélyezzük az új jelszó kérését a Windows 10 bejelentkezési képernyőjéről a telefonos ügyfélszolgálatra beérkező hívások számának csökkentése érdekében.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.openlocfilehash: c84d876828ac96bfb44b84e99b13489d51ae3370
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55694023"
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
* Ha a Windows 10 rendszerű eszközök proxykiszolgálót vagy tűzfal mögé, hozzá kell adnia az URL-címeket, `passwordreset.microsoftonline.com` és `ajax.aspnetcdn.com` a HTTPS-forgalom (a 443-as port) engedélyezett URL-címek listájához.
* Tekintse át az alábbi korlátozások előtt ez a funkció a környezetben.

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

1. Jelentkezzen be rendszergazdai hitelesítő adatok használatával a Windows-számítógép
2. Futtassa a **regedit** parancsot rendszergazdaként
3. Állítsa be a következő beállításkulcsot
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>Mit látnak a felhasználók?

Milyen változást tapasztalnak a felhasználók a szabályzat konfigurálása és hozzárendelése után? Honnan fogják tudni, hogy a bejelentkezési képernyőn új jelszót kérhetnek?

![Bejelentkezési képernyő][LoginScreen]

Amikor a felhasználók megpróbálnak bejelentkezni, most már megjelenik egy jelszó-visszaállítási hivatkozás, amely megnyitja az önkiszolgáló jelszó-visszaállítási felületet a bejelentkezési képernyőn. Ezzel a funkcióval a felhasználók visszaállíthatják a jelszavukat anélkül, hogy egy másik eszközt kellene használniuk egy webböngésző eléréséhez.

A felhasználók a funkcióval kapcsolatban a [Munkahelyi vagy iskolai jelszó visszaállítása](../user-help/active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in) témakörben találhatnak útmutatást.

Az Azure AD auditnaplója információkat tartalmaz az IP-címről és az ügyféltípusról, ahol az új jelszó kérése megtörtént.

![Példa új jelszó kérésére a bejelentkezési képernyőn az Azure AD auditnaplójában](media/tutorial-sspr-windows/windows-sspr-azure-ad-audit-log.png)

Amikor a felhasználók új jelszót kérnek egy Windows 10 rendszerű eszköz, a bejelentkezési képernyőről, egy alacsony jogosultsági szintű "defaultuser1" nevű ideiglenes fiók jön létre. Ez a fiók biztonsága érdekében a jelszó-visszállítási folyamatnak szolgál. A fiókért véletlenszerűen létrehozott jelszót tartalmaz, nem jelenik meg az eszköz be, és automatikusan törlődik, miután a felhasználó visszaállítja a jelszavát. Több "defaultuser" profilok előfordulhat, hogy létezik, de biztonságosan figyelmen kívül hagyható.

## <a name="limitations"></a>Korlátozások

A funkció a Hyper-V használatával történő tesztelésekor a „Jelszó visszaállítása” hivatkozás nem jelenik meg.

* Lépjen a teszteléshez használt virtuális géphez, kattintson a **Nézet** lehetőségre, majd távolítsa el a **Bővített munkamenet** lehetőség jelölőnégyzetét.

Ez a funkció a távoli asztal vagy egy virtuális gép bővített munkamenet tesztelésekor a "Jelszó visszaállítása" hivatkozás nem jelenik meg.

* A Távoli asztalokról jelenleg nem támogatott a jelszó-visszaállítás.

Ha a Ctrl + Alt + Del 1809, mielőtt a házirend a Windows 10-es verziói által igényelt **jelszó alaphelyzetbe állítása** nem fog működni.

Ha a zárolási képernyő értesítések be vannak kapcsolva, **jelszó alaphelyzetbe állítása** nem fog működni.

A következő házirend-beállításokat ismert zavarják a jelszavak alaphelyzetbe állítása

   * HideFastUserSwitching engedélyezve értékre van állítva vagy 1
   * DontDisplayLastUserName engedélyezve értékre van állítva vagy 1
   * NoLockScreen engedélyezve értékre van állítva vagy 1
   * EnableLostMode van beállítva, az eszközön
   * Az egyéni shell Explorer.exe váltja fel

Ez a funkció nem működik a hálózatok üzembe helyezett 802.1 x hálózati hitelesítést és az "Azonnali végrehajtás a felhasználó bejelentkezése előtt végezze el" lehetőséget. 802.1 x hálózati hitelesítéssel üzembe helyezett hálózatok ajánlott számítógép-hitelesítés használatával engedélyezze ezt a funkciót.

Csatlakoztatott hibrid forgatókönyvek esetén az SSPR-munkafolyamat sikeresen befejezi anélkül, hogy egy Active Directory-tartományvezérlőhöz. Ha egy felhasználó a jelszó-visszaállítási folyamat befejeződött, ha egy Active Directory-tartományvezérlőhöz való kommunikáció nem érhető el, például amikor távolról dolgozó, a felhasználó nem jelentkezhet be az eszközön addig, amíg az eszköz és a tartományvezérlő közötti kommunikációhoz és a a gyorsítótárazott hitelesítő adat frissítése. **Kapcsolat egy tartományvezérlővel kell használnia az új jelszó először**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az oktatóanyag keretében konfigurált funkciót már nem szeretné tovább használni, törölje az Ön által létrehozott Intune eszközkonfigurációs profilt vagy a beállításkulcsot.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban engedélyezte, hogy a felhasználók új jelszót kérjenek a Windows 10 bejelentkezési képernyőjéről. A következő oktatóanyagból megtudhatja, hogyan integrálható az Azure Identity Protection az új jelszó önkiszolgáló kérésével és a többtényezős hitelesítéssel.

> [!div class="nextstepaction"]
> [Kockázat értékelése bejelentkezéskor](tutorial-risk-based-sspr-mfa.md)

[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "Intune eszközkonfigurációs szabályzat hozzárendelése Windows 10-eszközök egy csoportjához"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "Új jelszó kérésére szolgáló hivatkozás a Windows 10 bejelentkezési képernyőjén"
