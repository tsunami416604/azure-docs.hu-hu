---
title: 'Az Azure Active Directory tartományi szolgáltatások: Az Azure Active Directory Application Proxy üzembe helyezése |} A Microsoft Docs'
description: Az Azure AD-alkalmazásproxy használata az Azure Active Directory Domain Services felügyelt tartomány
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: e787d946aa62f0bcb6615d89192e7d9e4d91ffe4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197042"
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Üzembe helyezése az Azure AD-alkalmazásproxy a az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz
Az Azure Active Directory (AD) alkalmazásproxy segítségével támogatja a távoli dolgozók tegye közzé a helyszíni alkalmazások számára az interneten keresztül érhető el. Az Azure AD tartományi szolgáltatásokat most lift-and-shift régi alkalmazásait az Azure infrastruktúra-szolgáltatások a helyszínen futó segítségével. Ezután közzéteheti ezeket az alkalmazásokat az Azure AD-alkalmazásproxy segítségével biztonságos távoli hozzáférést biztosít a felhasználók számára a szervezetben.

Ha most ismerkedik az Azure AD-alkalmazásproxy a, tudjon meg többet ez a szolgáltatás a következő cikket: [Útmutató a biztonságos távelérés biztosítása helyszíni alkalmazásokhoz](../active-directory/manage-apps/application-proxy.md).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek
A cikkben szereplő feladatok elvégzéséhez szüksége:

1. Egy érvényes **Azure-előfizetés**.
2. Egy **Azure AD-címtár** -vagy az egy helyszíni címtár vagy egy csak felhőalapú címtárral szinkronizálja.
3. Egy **alapszintű Azure AD- vagy prémium szintű licenc** az Azure AD-alkalmazásproxy használatához szükséges.
4. **Az Azure AD Domain Services** engedélyezve kell lennie az Azure AD-címtárban. Ha még nem tette, minden ismertetett feladatok végrehajtásával a [a kezdeti lépések útmutatóban](active-directory-ds-getting-started.md).

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>1 – engedélyezése az Azure AD-alkalmazásproxy feladat az Azure AD címtárhoz
Hajtsa végre az alábbi lépéseket az Azure AD-címtár az Azure AD-alkalmazásproxy engedélyezéséhez.

1. Jelentkezzen be rendszergazdaként a a [az Azure portal](https://portal.azure.com).

2. Kattintson a **Azure Active Directory** viszi, megjelenik a directory áttekintése. Kattintson a **vállalati alkalmazások**.

    ![Válassza ki az Azure AD-címtár](./media/app-proxy/app-proxy-enable-start.png)
3. Kattintson a **alkalmazásproxy**. Alapszintű Azure AD vagy az Azure AD Premium-előfizetés nem rendelkezik, megjelenik egy próbaverzióra engedélyezni. Váltógomb **alkalmazásproxy engedélyezése?** való **engedélyezése** kattintson **mentése**.

    ![Alkalmazásproxy engedélyezése](./media/app-proxy/app-proxy-enable-proxy-blade.png)
4. Az összekötő letöltéséhez kattintson a **összekötő** gombra.

    ![-Összekötő letöltése](./media/app-proxy/app-proxy-enabled-download-connector.png)
5. A letöltési oldalon fogadja el a licencfeltételeket és az adatvédelmi szerződés, és kattintson a **letöltése** gombra.

    ![Erősítse meg a letöltési](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>2. feladat – tartományhoz csatlakoztatott Windows-kiszolgálók üzembe helyezéséhez az Azure AD-alkalmazásproxy-összekötő üzembe helyezése
Tartományhoz csatlakoztatott Windows Serveres virtuális gép, amelyen az Azure AD-alkalmazásproxy-összekötő telepítése szükséges. Egyes alkalmazások esetében dönthet úgy, hogy több kiszolgálóra, amelyre telepítve van az összekötő üzembe helyezése. Üzembe helyezési lehetőséget ad magasabb rendelkezésre állás, és segít fejlesztettük hitelesítés kezeléséhez.

Az összekötő-kiszolgálók azonos virtuális hálózatban (vagy egy csatlakoztatott/társviszonyban álló virtuális hálózat), amelyiken engedélyezte az Azure AD tartományi szolgáltatásokkal felügyelt tartományban üzembe. Ehhez hasonlóan az alkalmazásproxyn keresztül közzétett alkalmazások üzemeltető kiszolgálók azonos Azure virtuális hálózathoz kell telepíteni kell.

Összekötő-kiszolgálók kiépítéséhez, című cikkben ismertetett feladatok végrehajtásával [Windows virtuális gépek csatlakoztatása felügyelt tartományhoz](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>3. feladat – telepítése és regisztrálása az Azure AD alkalmazásproxy-összekötő
Korábban a Windows Server virtuális gép kiépítése, és azt a felügyelt tartományhoz kapcsolódó. Ebben a feladatban telepíti ezt a virtuális gépet az Azure AD-alkalmazásproxy-összekötő.

1. Másolja a virtuális gép, amelyen az Azure AD Web Application Proxy connector telepítése az összekötő telepítőcsomagját.

2. Futtatás **AADApplicationProxyConnectorInstaller.exe** a virtuális gépen. Fogadja el a szoftverlicenc-szerződést.

    ![Telepítse a feltételek elfogadása](./media/app-proxy/app-proxy-install-connector-terms.png)
3. A telepítés során kell megadnia az összekötő regisztrálására az Azure AD Directory alkalmazásproxyjával.
    * Adja meg a **az Azure AD globális rendszergazdai hitelesítő adatokkal**. A globális rendszergazdai bérlő adatai eltérhetnek a Microsoft Azure rendszerre vonatkozó hitelesítő adatoktól.
    * Ugyanabban a könyvtárban, ahol az alkalmazásproxy szolgáltatás engedélyezve van a rendszergazdai fiókhoz az összekötő regisztrálására használni kell tartoznia. Ha például a bérlő tartománya a contoso.com, a rendszergazdának kell lennie admin@contoso.com vagy más érvényes alias abban a tartományban.
    * Ha az Internet Explorer – fokozott biztonsági beállítások be van kapcsolva a kiszolgálón az összekötőt telepíti, a regisztrációs képernyő hozzáférése. Engedélyezi a hozzáférést, kövesse az utasításokat a hibaüzenetben. Győződjön meg arról, hogy az Internet Explorer fokozott biztonsági beállításai ki vannak kapcsolva.
    * Ha az összekötő regisztrálása meghiúsul, tekintse meg a [Troubleshoot Application Proxy](../active-directory/manage-apps/application-proxy-troubleshoot.md) (Alkalmazásproxy hibaelhárítása) című anyagot.

    ![Összekötő telepítve van](./media/app-proxy/app-proxy-connector-installed.png)
4. Ahhoz, hogy az összekötő működik megfelelően, az Azure AD Application Proxy Connector hibaelhárító futtatásához. Sikeres jelentés a hibaelhárító futtatása után kell megjelennie.

    ![Hibaelhárító sikeres](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. Az újonnan telepített összekötő az Azure Active directory Application proxy oldalon felsorolt kell megjelennie.

    ![](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> Összekötők telepítése több kiszolgálón történő hitelesítésére az Azure AD alkalmazásproxyn keresztül közzétett alkalmazások magas rendelkezésre állás biztosítása érdekében választhatja. Hajtsa végre az összekötő telepítéséhez más kiszolgálókon a felügyelt tartományhoz kapcsolódó fent ismertetett lépéseket.
>
>

## <a name="next-steps"></a>További lépések
Állítsa be az Azure AD-alkalmazásproxy rendelkezik, és azt integrálhatók az Azure AD tartományi szolgáltatásokkal felügyelt tartományban.

* **Az alkalmazások Azure-beli virtuális gépek áttelepítése:** Lift-and-shift az alkalmazások a helyszíni kiszolgálók Azure-beli virtuális gépek a felügyelt tartományhoz kapcsolódó is. Így segítségével távolíthatja el a helyszíni kiszolgálók infrastrukturális költségek.

* **Alkalmazások közzététele az Azure AD-alkalmazásproxy:** Tegye közzé az Azure virtuális gépek az Azure AD-alkalmazásproxy használatával a futó alkalmazások. További információkért lásd: [alkalmazások közzététele az Azure AD-alkalmazásproxy használatával](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>Központi telepítési Megjegyzés - közzététel IWA (integrált Windows-hitelesítés) alkalmazásokat az Azure AD-alkalmazásproxy használatával
Engedélyezze az egyszeri bejelentkezést az alkalmazásproxy-összekötők jogosultság megadásával integrált Windows-hitelesítés (IWA) segítségével megszemélyesíthet felhasználókat, és küldhet, és a felhasználók nevében jogkivonatokat fogadni alkalmazások. Kerberos által korlátozott delegálás (KCD) konfigurálja az összekötőt a felügyelt tartományban lévő erőforrások eléréséhez szükséges engedélyeket. Az erőforrás-alapú KCD mechanizmus használata felügyelt tartományokban a biztonság fokozása érdekében.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Erőforrás-alapú Kerberos általi korlátozott delegálás az Azure AD-alkalmazásproxy-összekötő engedélyezése
Az Azure Application Proxy connector kell konfigurálni a Kerberos által korlátozott delegálás (KCD), így azt a felhasználók megszemélyesítésére a felügyelt tartományon. Az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz, az nem kell tartományi rendszergazdai jogosultságokkal. Ezért **hagyományos fiókszintű kcd Szolgáltatáshoz nem lehet konfigurálni a felügyelt tartomány**.

Ez ismertetett módon kihasználhassák a KCD erőforrás-alapú [cikk](active-directory-ds-enable-kcd.md).

> [!NOTE]
> A felügyelt tartomány AD PowerShell-parancsmagokkal felügyelheti a "AAD DC rendszergazdák" csoport tagjának lennie kell.
>
>

A Get-ADComputer PowerShell-parancsmag használatával lekérheti a számítógépen, amelyre telepítve van az Azure AD-alkalmazásproxy-összekötő beállításait.
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso100-proxy.contoso100.com
```

Ezt követően a Set-ADComputer parancsmag segítségével állítsa be az erőforrás-kiszolgáló a kcd Szolgáltatáshoz erőforrás-alapú.
```powershell
Set-ADComputer contoso100-resource.contoso100.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

Ha a felügyelt tartományban lévő több Application Proxy-összekötők telepített, akkor kell erőforrás-alapú KCD minden ilyen connector-példány konfigurálása.


## <a name="related-content"></a>Kapcsolódó tartalom
* [Az Azure AD tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [A Kerberos által korlátozott delegálás konfigurálása a felügyelt tartományon](active-directory-ds-enable-kcd.md)
* [A Kerberos általi korlátozott delegálás áttekintése](https://technet.microsoft.com/library/jj553400.aspx)
