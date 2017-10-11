---
title: "Az Azure Active Directory tartományi szolgáltatások: Az Azure Active Directory-proxyt üzembe helyezni |} Microsoft Docs"
description: "Az Azure AD-alkalmazásproxy használata a felügyelt tartományok Azure Active Directory tartományi szolgáltatások"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: c158c67a82e12501386179e19bc75fd852d7e308
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Központi telepítése az Azure AD-alkalmazásproxyval oldható meg az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz
Támogatja a távoli dolgozók által az interneten keresztül érhető el a helyszíni alkalmazások közzététele az Azure Active Directory (AD) alkalmazásproxy segítségével. Az Azure AD tartományi szolgáltatásokat helyszíni Azure infrastruktúra-szolgáltatásokat futtató örökölt alkalmazások most növekedési-és-shift lehetővé teszi. Majd közzéteheti ezeket az alkalmazásokat az Azure AD alkalmazásproxy segítségével biztonságos távoli hozzáférés biztosítása a szervezetében lévő felhasználók számára.

Ha most ismerkedik az Azure AD-alkalmazásproxy, kapcsolatos további Ez a szolgáltatás a következő cikkben: [hogyan biztosíthat biztonságos távoli hozzáférést a helyszíni alkalmazások](../active-directory/active-directory-application-proxy-get-started.md).


## <a name="before-you-begin"></a>Előkészületek
A cikkben szereplő feladatok elvégzéséhez szüksége:

1. Egy érvényes **Azure-előfizetés**.
2. Egy **Azure AD-címtár** -vagy egy helyszíni címtár vagy egy csak felhőalapú directory szinkronizálva.
3. Egy **Azure AD alapszintű vagy Premium licenc** az Azure AD-alkalmazásproxy használatához szükséges.
4. **Azure AD tartományi szolgáltatások** az Azure AD-címtár engedélyezni kell. Ha még nem tette meg, az összes ismertetett feladatok végrehajtásával a [első lépések útmutató](active-directory-ds-getting-started.md).

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>1 - engedélyezése az Azure AD alkalmazásproxy feladatot az Azure AD-címtár
Hajtsa végre az alábbi lépéseket az Azure AD-címtár Azure AD alkalmazásproxy engedélyezéséhez.

1. Jelentkezzen be rendszergazdaként a a [Azure-portálon](http://portal.azure.com).

2. Kattintson a **Azure Active Directory** elindítani a directory áttekintése. Kattintson a **vállalati alkalmazások**.

    ![Válassza ki az Azure AD-címtár](./media/app-proxy/app-proxy-enable-start.png)
3. Kattintson a **alkalmazásproxy**. Ha nem rendelkezik az Azure AD alapszintű vagy Azure AD Premium előfizetéssel, lásd: a próbaverziójának engedélyezni. Váltás **alkalmazásproxy engedélyezése?** való **engedélyezése** kattintson **mentése**.

    ![Alkalmazás Proxy engedélyezése](./media/app-proxy/app-proxy-enable-proxy-blade.png)
4. Az összekötő letöltéséhez kattintson a **összekötő** gombra.

    ![Összekötő letöltése](./media/app-proxy/app-proxy-enabled-download-connector.png)
5. A letöltési oldalon fogadja el a licencfeltételeket és az adatvédelmi szerződést, majd kattintson a **letöltése** gombra.

    ![Erősítse meg a letöltési](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>2. feladat – kiépíteni a tartományhoz csatlakoztatott Windows Server rendszerű kiszolgálók az Azure AD alkalmazásproxy-összekötő üzembe helyezése
A tartományhoz csatlakoztatott Windows Server virtuális gépek is telepíthető, amely az Azure AD alkalmazásproxy-összekötő van szüksége. A közzétett alkalmazások, attól függően választhatja, hogy több kiszolgáló, amelyen telepítve van az összekötő telepítéséhez. Központi telepítési lehetőséget ad nagyobb rendelkezésre állása, és segít nehezebb hitelesítési terhelés kezelésére.

Az összekötő-kiszolgálókon ugyanazt a virtuális hálózatot (vagy a csatlakoztatott/társviszonyban virtuális hálózaton), amelyben engedélyezte az Azure AD tartományi szolgáltatások által kezelt tartomány kiépítéséhez. Ehhez hasonlóan az alkalmazások közzététele az alkalmazásproxy keresztül üzemeltető kiszolgálók telepítve kell lennie az Azure virtuális hálózaton.

Összekötő kiszolgálók ellátását a című cikkben ismertetett feladatok végrehajtásával [egy Windows rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>3. feladat – az Azure AD alkalmazásproxy-összekötő regisztrálása és telepítése
Korábban a Windows Server virtuális gép kiépítése, és felügyelt tartományhoz csatlakozik. Ebben a feladatban telepíti az Azure AD alkalmazásproxy-összekötő a virtuális gépen.

1. Az összekötő-telepítési csomag másolása a virtuális gép, amelyre telepíti az Azure AD-webalkalmazás-Proxy connector.

2. Futtatás **AADApplicationProxyConnectorInstaller.exe** a virtuális gépen. Fogadja el a szoftverlicenc-szerződést.

    ![A telepítés feltételek elfogadása](./media/app-proxy/app-proxy-install-connector-terms.png)
3. A telepítés során kéri az összekötő regisztrálására az Azure Active directory Alkalmazásproxyjával.
    * Adja meg a **az Azure AD globális rendszergazda hitelesítő adatait**. A globális rendszergazdai bérlő adatai eltérhetnek a Microsoft Azure rendszerre vonatkozó hitelesítő adatoktól.
    * A rendszergazdai fiók segítségével regisztrálhat az összekötő ugyanabban a könyvtárban, ahol az alkalmazásproxy engedélyezte kell tartoznia. Például, ha a bérlő tartománya a contoso.com, a rendszergazdának kell lennie admin@contoso.com vagy más érvényes alias abban a tartományban.
    * Ha az Internet Explorer fokozott biztonsági beállítások be van kapcsolva a kiszolgáló az összekötőt telepíti, a regisztrációs képernyő blokkolhatja. Engedélyezi a hozzáférést, kövesse az utasításokat a hibaüzenet. Győződjön meg arról, hogy az Internet Explorer fokozott biztonsági beállításai ki vannak kapcsolva.
    * Ha az összekötő regisztrálása meghiúsul, tekintse meg a [Troubleshoot Application Proxy](../active-directory/active-directory-application-proxy-troubleshoot.md) (Alkalmazásproxy hibaelhárítása) című anyagot.

    ![Összekötő telepítve](./media/app-proxy/app-proxy-connector-installed.png)
4. Az összekötő biztosításához működik megfelelően, futtassa az Azure AD Application Proxy Connector hibaelhárító. Egy sikeres jelentésben a hibaelhárító futtatása után kell megjelennie.

    ![Hibaelhárító sikeres](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. Az újonnan telepített összekötő az Azure AD-címtár Application proxy oldalon felsorolt kell megjelennie.

    ![](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> Összekötők telepítése több kiszolgálón hitelesítéséhez az Azure AD-alkalmazásproxy keresztül közzétett alkalmazás magas rendelkezésre állás biztosításához választhatja. Hajtsa végre az összekötő telepítéséhez más kiszolgálókon a felügyelt tartományhoz fent ismertetett lépéseket.
>
>

## <a name="next-steps"></a>Következő lépések
Állítsa be az Azure AD-alkalmazásproxy, és azt az Azure AD tartományi szolgáltatások által kezelt tartomány integrálva.

* **Az alkalmazások az Azure virtuális gépek áttelepítése:** növekedési-és-shift a felügyelt tartományra csatlakozott az alkalmazásait, a helyszíni kiszolgálók és az Azure virtuális gépek is. Így segítséget nyújt az infrastruktúra költségek a helyszíni kiszolgálók futó selejtezni.

* **Alkalmazások közzététele az Azure AD-alkalmazásproxy használatával:** közzététele az Azure AD-alkalmazásproxy használata a Azure virtuális gépek futó alkalmazások. További információkért lásd: [az Azure AD-alkalmazásproxy használó alkalmazások közzététele](../active-directory/application-proxy-publish-azure-portal.md)


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>Központi telepítés Megjegyzés - közzététel integrált Windows-Hitelesítést (integrált Windows-hitelesítés) alkalmazások az Azure AD-alkalmazásproxy használatával
Egyszeri bejelentkezés az alkalmazások által Application Proxy összekötők engedély integrált Windows-hitelesítéssel (IWA) segítségével megszemélyesíthet felhasználókat, küldésére és fogadására a nevében tokenek engedélyezése. Kerberos által korlátozott delegálás (KCD) konfigurálása az összekötő és a felügyelt tartományra lévő erőforrások eléréséhez szükséges engedélyeket. Az erőforrás-alapú Kerberos által korlátozott Delegálás mechanizmust használni a felügyelt tartományok a biztonság fokozása érdekében.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Erőforrás-alapú kerberos által korlátozott delegálás az Azure AD alkalmazásproxy-összekötő engedélyezése
Az Azure Application Proxy connector konfigurálnia kell a kerberos által korlátozott delegálás (KCD), ezért esetében megszemélyesíthet felhasználókat a felügyelt tartományra. Az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz, a nem tartományi rendszergazdai jogosultságokkal rendelkezik. Ezért **hagyományos fiók szintű Kerberos által korlátozott Delegálás nem konfigurálható egy felügyelt tartomány**.

Erőforrás-alapú Kerberos által korlátozott Delegálás használata, ez a [cikk](active-directory-ds-enable-kcd.md).

> [!NOTE]
> Felügyelheti a felügyelt tartományra AD PowerShell-parancsmagok használatával "AAD DC rendszergazdák" csoport tagjának lennie kell.
>
>

A Get-ADComputer PowerShell-parancsmag használatával a számítógép, amelyen telepítve van az Azure AD alkalmazásproxy-összekötő beállításainak beolvasása.
```
$ConnectorComputerAccount = Get-ADComputer -Identity contoso100-proxy.contoso100.com
```

Ezt követően a Set-ADComputer parancsmag segítségével erőforrás-alapú az erőforrás-kiszolgáló Kerberos által korlátozott Delegálás beállítása.
```
Set-ADComputer contoso100-resource.contoso100.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

Ha több alkalmazásproxy-összekötő a felügyelt tartomány telepítette, akkor erőforrás-alapú minden ilyen összekötő-példánynak a Kerberos által korlátozott Delegálás konfigurálása.


## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [Kerberos által korlátozott delegálás konfigurálása a felügyelt tartományhoz](active-directory-ds-enable-kcd.md)
* [A Kerberos általi korlátozott delegálás áttekintése](https://technet.microsoft.com/library/jj553400.aspx)
