---
title: 'Azure Active Directory Domain Services: Az Azure AD Application Proxy üzembe helyezése | Microsoft Docs'
description: Az Azure AD Application Proxy használata Azure Active Directory Domain Services felügyelt tartományokban
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: eca421697081310b1bf245172b3ff125e11c8728
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234173"
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Azure-AD Application Proxy üzembe helyezése Azure AD Domain Services felügyelt tartományon
A Azure Active Directory (AD) alkalmazásproxy segítséget nyújt a távoli dolgozók támogatásához azáltal, hogy közzéteszi a helyszíni alkalmazásokat az interneten keresztül. A Azure AD Domain Services segítségével mostantól a helyszínen futó örökölt alkalmazások áthelyezhetők az Azure-infrastruktúra szolgáltatásaiba. Ezeket az alkalmazásokat az Azure AD Application Proxy használatával teheti közzé, így biztonságos távoli hozzáférést biztosíthat a szervezet felhasználói számára.

Ha még nem ismeri az Azure AD Application Proxy, további információt a szolgáltatásról a következő cikkben talál: [Biztonságos távoli hozzáférés biztosítása a](../active-directory/manage-apps/application-proxy.md)helyszíni alkalmazásokhoz.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek
A cikkben felsorolt feladatok elvégzéséhez a következőkre lesz szüksége:

1. Érvényes **Azure-előfizetés**.
2. Egy **Azure ad-címtár** – szinkronizálva van egy helyszíni címtárral vagy egy csak felhőalapú címtárral.
3. Az Azure-AD Application Proxy használatához **alapszintű Azure ad vagy prémium** szintű licencre van szükség.
4. **Azure ad Domain Services** engedélyezni kell az Azure ad-címtárat. Ha még nem tette meg, kövesse az [első lépések útmutatóban](create-instance.md)ismertetett összes feladatot.

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>1\. feladat – Azure AD Application Proxy engedélyezése az Azure AD-címtárhoz
A következő lépések végrehajtásával engedélyezheti az Azure-AD Application Proxy az Azure AD-címtárban.

1. Jelentkezzen be rendszergazdaként a [Azure Portal](https://portal.azure.com).

2. A címtár áttekintéséhez kattintson **Azure Active Directory** gombra. Kattintson a **vállalati alkalmazások**elemre.

    ![Azure AD-címtár kiválasztása](./media/app-proxy/app-proxy-enable-start.png)
3. Kattintson a **alkalmazásproxy**elemre. Ha nem rendelkezik alapszintű Azure AD-vagy prémium szintű Azure AD-előfizetéssel, a próbaverzió engedélyezésének lehetősége jelenik meg. Be kell kapcsolni az **alkalmazásproxy engedélyezése beállítást?** az **engedélyezéshez** és a **Mentés**gombra.

    ![Alkalmazásproxy engedélyezése](./media/app-proxy/app-proxy-enable-proxy-blade.png)
4. Az összekötő letöltéséhez kattintson az **összekötő** gombra.

    ![Összekötő letöltése](./media/app-proxy/app-proxy-enabled-download-connector.png)
5. A letöltési oldalon fogadja el a licencfeltételeket és az adatvédelmi szerződést, majd kattintson a **Letöltés** gombra.

    ![Letöltés megerősítése](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>2\. feladat – tartományhoz csatlakoztatott Windows-kiszolgálók kiépítése az Azure AD Application Proxy-összekötő üzembe helyezéséhez
A tartományhoz csatlakoztatott Windows Server-alapú virtuális gépekre van szükség, amelyeken telepítheti az Azure AD Application Proxy-összekötőt. Egyes alkalmazások esetében dönthet úgy, hogy több kiszolgálót is kiépít, amelyeken az összekötő telepítve van. Ez a központi telepítési lehetőség nagyobb rendelkezésre állást biztosít, és segíti a nehezebb hitelesítési terhelések kezelését.

Helyezze üzembe az összekötő-kiszolgálókat ugyanazon a virtuális hálózaton (vagy egy csatlakoztatott/társ virtuális hálózaton), amelyben engedélyezte a Azure AD Domain Services felügyelt tartományt. Hasonlóképpen, az alkalmazásproxy használatával közzétett alkalmazásokat üzemeltető kiszolgálókat ugyanarra az Azure-beli virtuális hálózatra kell telepíteni.

Összekötő-kiszolgálók kiépítéséhez kövesse a [Windows rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz](active-directory-ds-admin-guide-join-windows-vm.md)című cikkben ismertetett feladatokat.


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>3\. feladat – az Azure AD Application Proxy-összekötő telepítése és regisztrálása
Korábban kiépített egy Windows Server rendszerű virtuális gépet, és csatlakoztatta a felügyelt tartományhoz. Ebben a feladatban telepíti az Azure AD Application Proxy-összekötőt ezen a virtuális gépen.

1. Másolja az összekötő-telepítési csomagot arra a virtuális gépre, amelyre az Azure AD webalkalmazás-proxy összekötőt telepíti.

2. Futtassa a **AADApplicationProxyConnectorInstaller. exe fájlt** a virtuális gépen. A szoftverlicenc-szerződés elfogadása.

    ![A telepítés feltételeinek elfogadása](./media/app-proxy/app-proxy-install-connector-terms.png)
3. A telepítés során a rendszer felszólítja, hogy regisztrálja az összekötőt az Azure AD-címtár alkalmazásproxy használatával.
   * Adja meg az **Azure ad globális rendszergazdai hitelesítő adatait**. A globális rendszergazdai bérlő adatai eltérhetnek a Microsoft Azure rendszerre vonatkozó hitelesítő adatoktól.
   * Az összekötő regisztrálásához használt rendszergazdai fióknak ugyanahhoz a címtárhoz kell tartoznia, ahol engedélyezte az alkalmazásproxy szolgáltatást. Ha például a bérlő tartománya contoso.com, akkor a rendszergazdának vagy bármely admin@contoso.com más érvényes aliasnak kell lennie az adott tartományban.
   * Ha az Internet Explorer fokozott biztonsági beállításai be vannak kapcsolva azon a kiszolgálón, amelyen az összekötőt telepíti, lehetséges, hogy a regisztrációs képernyő blokkolva van. A hozzáférés engedélyezéséhez kövesse a hibaüzenet utasításait. Győződjön meg arról, hogy az Internet Explorer fokozott biztonsági beállításai ki vannak kapcsolva.
   * Ha az összekötő regisztrálása meghiúsul, tekintse meg a [Troubleshoot Application Proxy](../active-directory/manage-apps/application-proxy-troubleshoot.md) (Alkalmazásproxy hibaelhárítása) című anyagot.

     ![Összekötő telepítve](./media/app-proxy/app-proxy-connector-installed.png)
4. Az összekötő megfelelő működésének biztosítása érdekében futtassa az Azure AD Application Proxy Connector-hibakeresőt. A hibakereső futtatása után a sikeres jelentésnek kell megjelennie.

    ![A hibakereső sikeres](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. Az újonnan telepített összekötőt az Azure AD-címtár alkalmazásproxy oldalán találja.

    ![A Azure Portal a telepített összekötő az elérhetőként jelenik meg](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> Dönthet úgy, hogy több kiszolgálóra is telepít összekötőket, hogy magas rendelkezésre állást biztosítson az Azure AD Application Proxy által közzétett alkalmazások hitelesítéséhez. A fent felsorolt lépések végrehajtásával telepítse az összekötőt a felügyelt tartományhoz csatlakoztatott más kiszolgálókra.
>
>

## <a name="next-steps"></a>További lépések
Beállította az Azure AD Application Proxyt, és integrálta azt a Azure AD Domain Services felügyelt tartományával.

* **Alkalmazások migrálása az Azure Virtual Machines szolgáltatásba:** A felügyelt tartományhoz csatlakoztatott Azure-beli virtuális gépekre az alkalmazásokat a helyszíni kiszolgálókról is átirányíthatja. Így könnyebben megszabadulhat a helyszíni kiszolgálók üzemeltetésének infrastrukturális költségeitől.

* **Alkalmazások közzététele az Azure AD Application Proxy használatával:** Azure-beli virtuális gépeken futó alkalmazások közzététele az Azure AD Application Proxy használatával. További információ: [alkalmazások közzététele az Azure ad Application proxy használatával](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>Üzembe helyezési Megjegyzés – IWA (integrált Windows-hitelesítési) alkalmazások közzététele az Azure AD Application Proxy használatával
Engedélyezze az egyszeri bejelentkezést az alkalmazásokba integrált Windows-hitelesítés (IWA) használatával, ha engedélyezi az alkalmazásproxy-összekötők számára a felhasználók megszemélyesítését, valamint a jogkivonatok küldését és fogadását a nevükben. Konfigurálja a Kerberos által korlátozott delegálást (KCD) az összekötő számára, hogy megadja a szükséges engedélyeket a felügyelt tartomány erőforrásainak eléréséhez. A fokozott biztonsághoz használja a felügyelt tartományok erőforrás-alapú KCD mechanizmusát.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Erőforrás-alapú Kerberos által korlátozott delegálás engedélyezése az Azure AD Application Proxy-összekötőhöz
Az Azure alkalmazásproxy-összekötőt be kell állítani a Kerberos által korlátozott delegálásra (KCD), így megszemélyesítheti a felhasználókat a felügyelt tartományon. Azure AD Domain Services felügyelt tartományon nem rendelkezik tartományi rendszergazdai jogosultságokkal. Ezért a **hagyományos fiók szintű KCD nem konfigurálható felügyelt tartományon**.

Használjon erőforrás-alapú KCD a jelen [cikkben](deploy-kcd.md)leírtak szerint.

> [!NOTE]
> A felügyelt tartomány AD PowerShell-parancsmagokkal való felügyeletéhez az "HRE DC rendszergazdák" csoport tagjának kell lennie.
>
>

Használja a Get-ADComputer PowerShell-parancsmagot azon számítógép beállításainak beolvasásához, amelyen az Azure AD Application Proxy Connector telepítve van.
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso100-proxy.contoso100.com
```

Ezután a set-ADComputer parancsmag használatával állítsa be az erőforrás-alapú KCD az erőforrás-kiszolgáló számára.
```powershell
Set-ADComputer contoso100-resource.contoso100.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

Ha több alkalmazásproxy-összekötőt telepített a felügyelt tartományhoz, az egyes összekötő-példányokhoz erőforrás-alapú KCD kell konfigurálnia.


## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD Domain Services – Első lépések útmutató](create-instance.md)
* [Kerberos által korlátozott delegálás konfigurálása felügyelt tartományon](deploy-kcd.md)
* [Kerberos által korlátozott delegálás – áttekintés](https://technet.microsoft.com/library/jj553400.aspx)
