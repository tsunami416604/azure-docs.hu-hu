---
title: Az Azure AD-alkalmazásproxy telepítése az Azure AD tartományi szolgáltatásokhoz | Microsoft dokumentumok
description: Megtudhatja, hogy miként biztosíthat biztonságos hozzáférést a belső alkalmazásokhoz a távoli dolgozók számára az Azure Active Directory alkalmazásproxy azure Active Directory tartományi szolgáltatások által kezelt tartományban történő telepítésével és konfigurálásával
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 3fb731736941293a82baba86165a1205bd8a05c5
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519036"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-ad-domain-services-managed-domain"></a>Az Azure AD alkalmazásproxy telepítése az Azure AD tartományi szolgáltatások által felügyelt tartomány belső alkalmazásaihoz való biztonságos hozzáférés érdekében

Az Azure AD tartományi szolgáltatásokkal (Azure AD DS) a helyszíni futó örökölt alkalmazásokat felemelheti és áthelyezheti az Azure-ba. Az Azure Active Directory (AD) alkalmazásproxy ezután segít a távoli dolgozók támogatásában azáltal, hogy biztonságosan közzéteszi azokat a belső alkalmazásokat egy Azure AD DS felügyelt tartomány részeként, hogy azok az interneten keresztül is elérhetők legyenek.

Ha most ismerkedik az Azure AD alkalmazásproxyval, és szeretne többet megtudni, olvassa el a Belső alkalmazások biztonságos távoli elérésének biztosítása című [témakört.](../active-directory/manage-apps/application-proxy.md)

Ez a cikk bemutatja, hogyan hozhat létre és konfigurálhat egy Azure AD alkalmazásproxy-összekötőt, amely biztonságos hozzáférést biztosít az Azure AD DS felügyelt tartományban lévő alkalmazásokhoz.

## <a name="before-you-begin"></a>Előkészületek

A cikk végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
    * Az **Azure AD-alkalmazásproxy használatához Azure AD Premium-licenc** szükséges.
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány a konfigurált és konfigurált az Azure AD-bérlő.
    * Szükség esetén [hozzon létre és konfiguráljon egy Azure Active Directory tartományi szolgáltatások példányát.][create-azure-ad-ds-instance]

## <a name="create-a-domain-joined-windows-vm"></a>Tartományhoz csatlakozott Windows virtuális gép létrehozása

A forgalom a környezetben futó alkalmazások, telepítse az Azure AD alkalmazásproxy-összekötő összetevő. Ezt az Azure AD alkalmazásproxy-összekötőt telepíteni kell az Azure AD DS felügyelt tartományához csatlakozott Windows Server virtuális gépekre (VM). Egyes alkalmazások esetében több olyan kiszolgálót is telepíthet, amelyek mindegyike rendelkezik az összekötővel. Ez a telepítési lehetőség nagyobb rendelkezésre állást biztosít, és segít a nagyobb hitelesítési terhelések kezelésében.

Az Azure AD alkalmazásproxy-összekötőt futtató virtuális gépnek ugyanazon a virtuális hálózaton kell lennie, vagy egy társviszony-létesített virtuális hálózaton, amelyben engedélyezte az Azure AD DS-t. Az alkalmazásproxy használatával közzétett alkalmazásokat üzemeltető virtuális gépeket is ugyanen az Azure virtuális hálózaton kell telepíteni.

Virtuális gép létrehozásához az Azure AD alkalmazásproxy-összekötőhöz hajtsa végre az alábbi lépéseket:

1. [Hozzon létre egyéni szervezeti egységet](create-ou.md). Az azure AD DS felügyelt tartományon belüli felhasználóknak az egyéni szervezeti egység kezeléséhez szükséges engedélyeket delegálhatja. Az Azure AD alkalmazásproxy virtuális gépeinek, és amelyek az alkalmazásokat futtatják, az egyéni szervezeti egység részét kell, hogy legyenek, nem pedig az alapértelmezett *AAD DC-számítógépek szervezeti* egységének.
1. [Tartomány-csatlakozzon a virtuális gépek][create-join-windows-vm], mind az egyik, amely az Azure AD alkalmazásproxy-összekötőt futtatja, és az okat, amelyek az alkalmazásokat, az Azure AD DS felügyelt tartományban. Ezeket a számítógépfiókokat az előző lépésből hozza létre az egyéni szervezeti egységben.

## <a name="download-the-azure-ad-application-proxy-connector"></a>Az Azure AD alkalmazásproxy-összekötő letöltése

Hajtsa végre a következő lépéseket az Azure AD alkalmazásproxy-összekötő letöltéséhez. A letöltött telepítőfájl a következő szakaszban az alkalmazásproxy-virtuális gépre kerül.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan felhasználói fiókkal, amely rendelkezik az Azure AD *vállalati rendszergazdai* engedélyekkel.
1. Keresse meg és válassza az **Azure Active Directoryt** a portál tetején, majd válassza a **Vállalati alkalmazások**lehetőséget.
1. Válassza az **Alkalmazásproxy lehetőséget** a bal oldali menüből. Az első összekötő létrehozásához és az alkalmazásproxy engedélyezéséhez válassza a **hivatkozást az összekötő letöltéséhez.**
1. A letöltési lapon fogadja el a licencfeltételeket és az adatvédelmi szerződést, majd válassza **a Feltételek elfogadása & letöltése**lehetőséget.

    ![Az Azure AD App Proxy-összekötő letöltése](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Az Azure AD alkalmazásproxy-összekötő telepítése és regisztrálása

Az Azure AD alkalmazásproxy-összekötőként használható virtuális gép most másolja és futtatja az Azure Portalról letöltött telepítőfájlt.

1. Másolja az Azure AD alkalmazásproxy-összekötő telepítőfájlját a virtuális gépre.
1. Futtassa a telepítőfájlt, például *az AADApplicationProxyConnectorInstaller.exe*fájlt. Fogadja el a szoftverlicenc-feltételeket.
1. A telepítés során a rendszer kéri, hogy regisztrálja az összekötőt az alkalmazásproxy val az Azure AD könyvtárban.
   * Adja meg a hitelesítő adatokat egy globális rendszergazda az Azure AD-címtárban. Az Azure AD globális rendszergazdai hitelesítő adatai eltérhetnek a portálon található Azure-hitelesítő adatoktól

        > [!NOTE]
        > Az összekötő regisztrálásához használt globális rendszergazdai fióknak ugyanahhoz a könyvtárhoz kell tartoznia, ahol engedélyezi az alkalmazásproxy szolgáltatást.
        >
        > Ha például az Azure AD-tartomány *aaddscontoso.com,* `admin@aaddscontoso.com` a globális rendszergazdának kell lennie, vagy egy másik érvényes aliasaz adott tartományban.

   * Ha az Internet Explorer fokozott biztonsági beállításai be van kapcsolva azösszekötőt telepítő virtuális gépnél, előfordulhat, hogy a regisztrációs képernyő le van tiltva. A hozzáférés engedélyezéséhez kövesse a hibaüzenet utasításait, vagy kapcsolja ki az Internet Explorer fokozott biztonságát a telepítési folyamat során.
   * Ha az összekötő regisztrációja nem sikerül, olvassa el az [Alkalmazásproxy hibaelhárítása című témakört.](../active-directory/manage-apps/application-proxy-troubleshoot.md)
1. A telepítés végén egy megjegyzés jelenik meg a kimenő proxyval rendelkező környezetekben. Ha az Azure AD alkalmazásproxy-összekötőt a kimenő proxyn keresztül `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`szeretné konfigurálni, futtassa a megadott parancsfájlt, például a.
1. Az Azure Portal alkalmazásproxy-lapján az új összekötő *aktív*állapotú, a következő példában látható módon jelenik meg:

    ![Az Azure Portalon aktívként megjelenített új Azure AD alkalmazásproxy-összekötő](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Az Azure AD alkalmazásproxyn keresztül hitelesítő alkalmazások magas rendelkezésre állásának biztosítása érdekében több virtuális gépre is telepíthet összekötőket. Ismételje meg az előző szakaszban felsorolt lépéseket az összekötő telepítéséhez az Azure AD DS felügyelt tartományhoz csatlakozott más kiszolgálókra.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>Erőforrásalapú Kerberos-korlátozott delegálás engedélyezése

Ha integrált Windows-hitelesítéssel (IWA) szeretné használni az alkalmazások egyszeri bejelentkezését, adjon engedélyt az Azure AD alkalmazásproxy-összekötőknek a felhasználók megszemélyesítésére, valamint a nevükben küldött és fogadott jogkivonatok küldésére és fogadására. Ezeknek az engedélyeknek a megadásához konfigurálja a Kerberos korlátozott delegálást (KCD) az összekötő számára az Azure AD DS felügyelt tartomány erőforrásainak eléréséhez. Mivel az Azure AD DS által felügyelt tartományban nincs tartományi rendszergazdai jogosultság, a hagyományos fiókszintű KCD nem konfigurálható felügyelt tartományban. Ehelyett használja az erőforrás-alapú KCD.Instead, use resource-based KCD.

További információt a [Kerberos korlátozott delegáláskonfigurálása (KCD) az Azure Active Directory tartományi szolgáltatásokban című témakörben talál.](deploy-kcd.md)

> [!NOTE]
> Az Azure *AD-bérlőben az Azure AD DC-rendszergazdák* csoportjának tagjaként be kell jelentkeznie egy felhasználói fiókba a következő PowerShell-parancsmagok futtatásához.
>
> Az alkalmazásproxy-összekötő virtuális gépének és alkalmazásvirtuális gépeinek számítógép-fiókjainak egy egyéni szervezeti egységben kell lenniük, ahol rendelkezik az erőforrás-alapú KCD konfigurálásához szükséges engedélyekkel. A beépített *AAD DC-számítógépek* tárolóban nem konfigurálható erőforrásalapú KCD egy számítógépfiókhoz.

A [Get-ADComputer][Get-ADComputer] segítségével olvassa be annak a számítógépnek a beállításait, amelyen az Azure AD alkalmazásproxy-összekötő telepítve van. A tartományhoz csatlakozott felügyeleti virtuális gép, és bejelentkezett, mint az *Azure AD DC rendszergazdák* csoportjának tagjaként bejelentkezett, futtassa a következő parancsmagokat.

A következő példa a *appproxy.aaddscontoso.com*nevű számítógépfiókról kap információt. Adja meg a saját számítógépnevét az Azure AD alkalmazás proxy virtuális gép az előző lépésekben konfigurált.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

Minden olyan alkalmazáskiszolgáló, amely az Azure AD alkalmazásproxy mögötti alkalmazásokat futtatja, használja a [Set-ADComputer][Set-ADComputer] PowerShell parancsmalapot az erőforrás-alapú KCD konfigurálásához. A következő példában az Azure AD alkalmazásproxy-összekötő engedélyt kap a *appserver.aaddscontoso.com* számítógép használatára:

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

Ha több Azure AD alkalmazásproxy-összekötőt telepít, minden egyes összekötő példányhoz konfigurálnia kell az erőforrás-alapú KCD-t.

## <a name="next-steps"></a>További lépések

Az Azure AD DS-be integrált Azure AD alkalmazásproxyval közzéteheti a felhasználók számára elérhető alkalmazásokat. További információt az [Alkalmazások közzététele az Azure AD alkalmazásproxy használatával](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)című témakörben talál.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
