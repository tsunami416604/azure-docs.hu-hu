---
title: Biztonságos távoli virtuálisgép-hozzáférés az Azure AD tartományi szolgáltatásokban | Microsoft dokumentumok
description: Megtudhatja, hogy miként biztosíthat távoli hozzáférést a virtuális gépekhez a Hálózati házirend-kiszolgáló (NPS) és az Azure többtényezős hitelesítés használatával egy Távoli asztali szolgáltatások központi telepítésével egy Azure Active Directory tartományi szolgáltatások felügyelt tartományában.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: f2a222c6a382fa2c316211e293547780a8778177
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80423144"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Biztonságos távhozzáférés a virtuális gépekhez az Azure Active Directory tartományi szolgáltatásokban

Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) felügyelt tartományban futó virtuális gépek (VM-ek) távoli elérésének biztonságos sálatához használhatja a Távoli asztali szolgáltatások (RDS) és a hálózati házirend-kiszolgáló (NPS) szolgáltatást. Az Azure AD DS hitelesíti a felhasználókat, ahogy hozzáférést kérnek az RDS-környezeten keresztül. A fokozott biztonság érdekében integrálhatja az Azure többtényezős hitelesítést, hogy a bejelentkezési események során további hitelesítési kérdést adjon meg. Az Azure többtényezős hitelesítés e szolgáltatás biztosításához a nps bővítményt használ.

> [!IMPORTANT]
> Az Ajánlott módja annak, hogy biztonságosan csatlakozzon a virtuális gépek egy Azure AD DS felügyelt tartományban az Azure Bastion, egy teljesen platform által felügyelt PaaS-szolgáltatás, amely a virtuális hálózaton belül kiépítése. A megerősített állomás biztonságos és zökkenőmentes RDP-kapcsolatot biztosít a virtuális gépekhez közvetlenül az Azure Portalon SSL-en keresztül. Ha megerősített állomáson keresztül csatlakozik, a virtuális gépeknek nincs szükségük nyilvános IP-címre, és nem kell hálózati biztonsági csoportokat használnia az RDP-hez való hozzáférés hez való hozzáférés hez a 3389-es TCP-porton.
>
> Azt javasoljuk, hogy használja az Azure Bastion minden régióban, ahol támogatott. Az Azure-bastion elérhetősége nélküli régiókban kövesse a cikkben ismertetett lépéseket, amíg az Azure Bastion elérhetővé nem válik. Ügyeljen arra, hogy nyilvános IP-címeket rendelaz Azure AD DS-hez csatlakozott virtuális gépekhez, ahol az összes bejövő RDP-forgalom engedélyezett.
>
> További információ: [Mi az Azure-bástya?][bastion-overview]

Ez a cikk bemutatja, hogyan konfigurálhatja az RDS-t az Azure AD DS-ben, és szükség esetén használhatja az Azure többtényezős hitelesítés hálózati kiszolgáló bővítményét.

![Távoli asztali szolgáltatások (RDS) – áttekintés](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>Előfeltételek

A cikk végrehajtásához a következő erőforrásokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány a konfigurált és konfigurált az Azure AD-bérlő.
    * Szükség esetén [hozzon létre és konfiguráljon egy Azure Active Directory tartományi szolgáltatások példányát.][create-azure-ad-ds-instance]
* Az Azure Active Directory tartományi szolgáltatások virtuális hálózatában létrehozott *számítási feladatok* alhálózata.
    * Szükség esetén [konfigurálja a virtuális hálózatot egy Azure Active Directory tartományi szolgáltatások által kezelt tartományhoz.][configure-azureadds-vnet]
* Egy felhasználói fiók, amely az *Azure AD DC rendszergazdák* csoportjának tagja az Azure AD-bérlőben.

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>A Távoli asztal környezet telepítése és konfigurálása

Első lépésként hozzon létre legalább két Windows Server 2016 vagy Windows Server 2019 rendszert futtatni futó Azure-beli virtuális gépet. A távoli asztali környezet redundanciája és magas rendelkezésre állása érdekében később további állomásokat is hozzáadhat és eloszthat.

A távoli asztali telepítő javasolt telepítése a következő két virtuális gépet tartalmazza:

* *RDGVM01* – A Távoli asztali kapcsolatszervező, a Távoli asztali webelérési kiszolgáló és a Távoli asztali átjárókiszolgáló futtatása.
* *RDSHVM01* – A Távoli asztali munkamenetgazda-kiszolgáló futtatása.

Győződjön meg arról, hogy a virtuális gépek az Azure AD DS virtuális hálózat *számítási feladatok* alhálózatába vannak telepítve, majd csatlakozzanak a virtuális gépekhez az Azure AD DS felügyelt tartományához. További információt a [Windows Server virtuális gépek létrehozása és az Azure AD DS által felügyelt tartományhoz való csatlakozása][tutorial-create-join-vm]című témakörben talál.

A Távoli asztali környezet központi telepítése számos lépést tartalmaz. A meglévő Távoli asztali üzembe helyezési útmutató az Azure AD DS felügyelt tartományában használható konkrét módosítások nélkül is használható:

1. Jelentkezzen be a Távoli asztali környezethez létrehozott virtuális gépekre az *Azure AD DC rendszergazdák* csoport részét, például *contosoadmin*fiókkal.
1. A távoli asztali szolgáltatások létrehozásához és konfigurálásához használja a távoli [asztali környezet telepítési útmutatóját.][deploy-remote-desktop] A Távoli asztali kiszolgáló összetevői igény szerint terjeszthetők az Azure-beli virtuális gépekközött.
1. Ha webböngészővel szeretne hozzáférést biztosítani, [állítsa be a Távoli asztal webes klienst a felhasználók számára.][rd-web-client]

Az Azure AD DS felügyelt tartományba telepített Rd használatával kezelheti és használhatja a szolgáltatást, mint egy helyszíni AD DS-tartományban.

## <a name="deploy-and-configure-nps-and-the-azure-mfa-nps-extension"></a>A hálózati kiszolgáló és az Azure MFA hálózati kiszolgáló bővítmény telepítése és konfigurálása

Ha növelni szeretné a felhasználói bejelentkezési élmény biztonságát, szükség esetén integrálhatja a Távoli asztali környezetet az Azure többtényezős hitelesítéssel. Ezzel a konfigurációval a felhasználók egy további kérdést kapnak a bejelentkezés során az identitásuk megerősítéséhez.

Ennek a képességnek a biztosításához egy további hálózati házirend-kiszolgáló (NPS) van telepítve a környezetben az Azure többtényezős hitelesítéshálózati házirend-kiszolgáló bővítményével együtt. Ez a bővítmény integrálható az Azure AD-vel a többtényezős hitelesítési kérések állapotának kéréséhez és visszaadására.

A felhasználóknak regisztrálniuk kell az [Azure többtényezős hitelesítés használatához,][user-mfa-registration]amely további Azure AD-licenceket igényelhet.

Az Azure többtényezős hitelesítésének integrálása az Azure AD DS távoli asztali környezetébe, hozzon létre egy nps-kiszolgálót, és telepítse a bővítményt:

1. Hozzon létre egy további Windows Server 2016 vagy 2019 virtuális gépet, például *az NPSVM01-et,* amely az Azure AD DS virtuális hálózatában lévő *számítási feladatok* alhálózatához csatlakozik. Csatlakozzon a virtuális géphez az Azure AD DS felügyelt tartományához.
1. Jelentkezzen be a hálózati házirend-kiszolgáló virtuális gépébe az *Azure AD DC rendszergazdák* csoport részét, például *contosoadmin*fiókként.
1. A **Kiszolgálókezelőben**válassza a **Szerepkörök és szolgáltatások hozzáadása**lehetőséget, majd telepítse a Hálózati házirend és az Access *Services* szerepkört.
1. Használja a meglévő útmutató [cikket az Azure MFA hálózati kiszolgáló bővítmény telepítéséhez és konfigurálásához.][nps-extension]

Ha a hálózati kiszolgáló és az Azure többtényezős hitelesítéshálózati kiszolgáló bővítménye telepítve van, töltse ki a következő szakaszt a távoli asztali környezettel való használatra való konfiguráláshoz.

## <a name="integrate-remote-desktop-gateway-and-azure-multi-factor-authentication"></a>Távoli asztali átjáró és az Azure többtényezős hitelesítésének integrálása

Az Azure többtényezős hitelesítéshálózati házirend-kiszolgáló bővítményének integrálásához használja a meglévő útmutató cikket [a Távoli asztali átjáró infrastruktúrájának integrálásához a Hálózati házirend-kiszolgáló (NPS) bővítmény és az Azure AD használatával.][azure-mfa-nps-integration]

Az Azure AD DS felügyelt tartományával való integrációhoz a következő további konfigurációs beállításokszükségesek:

1. Ne [regisztrálja a n-ps-kiszolgálót az Active Directoryban.][register-nps-ad] Ez a lépés sikertelen egy Azure AD DS felügyelt tartományban.
1. A [hálózati házirend konfigurálásához][create-nps-policy]a 4. **Ignore user account dial-in properties**
1. Ha a Windows Server 2019-et használja a n-ps-kiszolgálóhoz és az Azure többtényezős hitelesítési hitelesítési hitelesítési bővítményéhez, futtassa a következő parancsot a biztonságos csatorna frissítéséhez, hogy a n-kiszolgáló megfelelően kommunikáljon:

    ```powershell
    sc sidtype IAS unrestricted
    ```

A rendszer most egy további hitelesítési tényezőt kér a bejelentkezéskor, például egy szöveges üzenetet vagy egy kérdést a Microsoft Authenticator alkalmazásban.

## <a name="next-steps"></a>További lépések

A telepítés rugalmasságának javításáról a [Távoli asztali szolgáltatások – Magas rendelkezésre állás][rds-high-availability]című témakörben talál további információt.

A felhasználói bejelentkezés biztonságossá tétele című témakörben további információt az [Azure többtényezős hitelesítéscímű][concepts-mfa]témakörben talál.

<!-- INTERNAL LINKS -->
[bastion-overview]: ../bastion/bastion-overview.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[configure-azureadds-vnet]: tutorial-configure-networking.md
[tutorial-create-join-vm]: join-windows-vm.md
[user-mfa-registration]: ../active-directory/authentication/howto-mfa-nps-extension.md#register-users-for-mfa
[nps-extension]: ../active-directory/authentication/howto-mfa-nps-extension.md
[azure-mfa-nps-integration]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md
[register-nps-ad]:../active-directory/authentication/howto-mfa-nps-extension-rdg.md#register-server-in-active-directory
[create-nps-policy]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md#configure-network-policy
[concepts-mfa]: ../active-directory/authentication/concept-mfa-howitworks.md

<!-- EXTERNAL LINKS -->
[deploy-remote-desktop]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-plan-high-availability
