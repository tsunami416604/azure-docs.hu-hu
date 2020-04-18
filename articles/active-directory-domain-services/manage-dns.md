---
title: Az Azure AD tartományi szolgáltatásainak DNS-kezelésé | Microsoft dokumentumok
description: Megtudhatja, hogy miként telepítheti a DNS-kiszolgálóeszközöket a DNS kezeléséhez és feltételes továbbítók létrehozásához egy Azure Active Directory tartományi szolgáltatások által kezelt tartományhoz.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 04/16/2020
ms.author: iainfou
ms.openlocfilehash: f4bd3f75c3246cb11e88dbaae817eba8ac76b394
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603473"
---
# <a name="administer-dns-and-create-conditional-forwarders-in-an-azure-ad-domain-services-managed-domain"></a>DNS felügyelete és feltételes továbbítók létrehozása egy Azure AD tartományi szolgáltatások által kezelt tartományban

Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) egyik kulcsösszetevőa a DNS (tartománynévfeloldás). Az Azure AD DS tartalmaz egy DNS-kiszolgálót, amely névfeloldást biztosít a felügyelt tartományszámára. Ez a DNS-kiszolgáló beépített DNS-rekordokat és frissítéseket tartalmaz a szolgáltatás futtatását lehetővé tenni lehetővé tegyék a kulcsösszetevőkhöz.

Saját alkalmazások és szolgáltatások futtatásakor előfordulhat, hogy dns-rekordokat kell létrehoznia a tartományhoz nem csatlakozó gépekhez, virtuális IP-címeket kell konfigurálnia a terheléselosztók számára, vagy külső DNS-továbbítókat kell beállítania. Az *AAD DC rendszergazdák* csoporthoz tartozó felhasználók DNS-felügyeleti jogosultságokat kapnak az Azure AD DS felügyelt tartományában, és egyéni DNS-rekordokat hozhatnak létre és szerkeszthetnek.

Hibrid környezetben a DNS-zónák és a más DNS-névterekben, például a helyszíni Activeád-ds-környezetben konfigurált rekordok nincsenek szinkronizálva az Azure AD DS-sel. Más DNS-névterekben lévő elnevezett erőforrások feloldásához hozzon létre és használjon feltételes továbbítókat, amelyek a környezetben meglévő DNS-kiszolgálókra mutatnak.

Ez a cikk bemutatja, hogyan telepítheti a DNS-kiszolgáló eszközeit, majd a DNS-konzol segítségével kezelheti a rekordokat, és hozzon létre feltételes továbbítókat az Azure AD DS-ben.

## <a name="before-you-begin"></a>Előkészületek

A cikk végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány a konfigurált és konfigurált az Azure AD-bérlő.
    * Szükség esetén töltse ki az oktatóanyagot [az Azure Active Directory tartományi szolgáltatások példányának létrehozásához és konfigurálásához.][create-azure-ad-ds-instance]
* Az Azure AD DS virtuális hálózatról a többi DNS-névterek üzemeltetési helyéhez való kapcsolódás.
    * Ez a kapcsolat [Azure ExpressRoute-][expressroute] vagy [Azure VPN-átjárókapcsolattal][vpn-gateway] is biztosítható.
* Az Azure AD DS felügyelt tartományához csatlakozott Windows Server felügyeleti virtuális gép.
    * Szükség esetén végezze el az oktatóanyagot [a Windows Server virtuális gép létrehozásához, és csatlakozzon egy felügyelt tartományhoz.][create-join-windows-vm]
* Egy felhasználói fiók, amely az *Azure AD DC rendszergazdák* csoportjának tagja az Azure AD-bérlőben.

## <a name="install-dns-server-tools"></a>A DNS-kiszolgáló eszközeinek telepítése

Dns-rekordok létrehozásához és módosításához az Azure AD DS,telepítenie kell a DNS-kiszolgáló eszközöket. Ezek az eszközök a Windows Server szolgáltatásaként telepíthetők. A felügyeleti eszközök Windows-ügyfélre történő telepítéséről a [Távoli kiszolgálófelügyeleti eszközök (RSAT)][install-rsat]telepítése című témakörben talál további információt.

1. Jelentkezzen be a felügyeleti virtuális gép. Az Azure Portal használatával való csatlakozáslépéseiről a [Csatlakozás Windows Server virtuális géphez][connect-windows-server-vm]című témakörben található.
1. Ha **a Kiszolgálókezelő** alapértelmezés szerint nem nyílik meg, amikor bejelentkezik a virtuális gépre, válassza a **Start** menüt, majd a **Kiszolgálókezelő parancsot.**
1. A **Kiszolgálókezelő** ablak *Irányítópult* ablakában válassza a **Szerepkörök és szolgáltatások hozzáadása**lehetőséget.
1. A *Szerepkörök és szolgáltatások hozzáadása varázsló*Kezdés **előtt** lapján válassza a **Tovább**lehetőséget.
1. A *Telepítés típusmezőben*hagyja bejelölve a **szerepkör- vagy szolgáltatásalapú telepítési** beállítást, és válassza a **Tovább**gombot.
1. A **Kiszolgálókiválasztása** lapon válassza ki az aktuális virtuális gépelemet a kiszolgálókészletből, például *myvm.aaddscontoso.com*, majd válassza a **Tovább**gombot.
1. A **Kiszolgálói szerepkörök** lapon kattintson a **Tovább gombra.**
1. A **Szolgáltatások** lapon bontsa ki a **Távoli kiszolgálófelügyeleti eszközök** csomópontot, majd bontsa ki a **Szerepkör-felügyeleti eszközök** csomópontot. Válassza a **DNS-kiszolgálóeszközök** szolgáltatást a szerepkör-felügyeleti eszközök listájából.

    ![A DNS-kiszolgálóeszközök telepítése a rendelkezésre álló szerepkör-felügyeleti eszközök listájából](./media/manage-dns/install-dns-tools.png)

1. A **Megerősítés** lapon válassza a **Telepítés**lehetőséget. A Csoportházirend-kezelési eszközök telepítése eltarthat egy-két percig.
1. Ha a szolgáltatás telepítése befejeződött, válassza a **Bezárás** lehetőséget a **Szerepkörök és szolgáltatások hozzáadása** varázslóból való kilépéshez.

## <a name="open-the-dns-management-console-to-administer-dns"></a>A DNS felügyeletéhez nyissa meg a DNS-kezelő konzolt

A DNS-kiszolgáló eszközeinek telepítésével felügyelheti a DNS-rekordokat az Azure AD DS felügyelt tartományában.

> [!NOTE]
> A DNS felügyeletéhez egy Azure AD DS felügyelt tartományban, be kell jelentkeznie egy felhasználói fiókba, amely tagja az *AAD DC rendszergazdák* csoportnak.

1. A kezdőképernyőn válassza a **Felügyeleti eszközök lehetőséget.** Megjelenik az elérhető felügyeleti eszközök listája, beleértve az előző szakaszban telepített **DNS-t** is. A **DNS** DNS Management konzol elindításához válassza a DNS-t.
1. A Csatlakozás a **DNS-kiszolgálóhoz** párbeszédpanelen válassza **a Következő számítógép**lehetőséget, majd írja be a felügyelt tartomány DNS-tartománynevét, például *aaddscontoso.com:*

    ![Csatlakozás az Azure AD DS felügyelt tartományához a DNS-konzolban](./media/manage-dns/connect-dns-server.png)

1. A DNS-konzol csatlakozik a megadott Azure AD DS felügyelt tartományhoz. Bontsa ki a **Címkeresési zónák** vagy a **Névkeresési zónák csomópontot** a szükséges DNS-bejegyzések létrehozásához vagy a meglévő rekordok szükség szerint történő szerkesztéséhez.

    ![DNS-konzol - tartomány felügyelete](./media/manage-dns/dns-manager.png)

> [!WARNING]
> Ha a DNS-kiszolgáló eszközök használatával kezeli a rekordokat, győződjön meg arról, hogy nem törli vagy módosítja az Azure AD DS által használt beépített DNS-rekordokat. A beépített DNS-rekordok közé tartoznak a tartomány DNS-rekordjai, a névkiszolgálói rekordok és a tartományvezérlő helyéhez használt egyéb rekordok. Ha módosítja ezeket a rekordokat, a tartományi szolgáltatások megszakadnak a virtuális hálózaton.

## <a name="create-conditional-forwarders"></a>Feltételes továbbítók létrehozása

Az Azure AD DS DNS-zónának csak a felügyelt tartomány zónáját és rekordjait kell tartalmaznia. Ne hozzon létre további zónákat az Azure AD DS-ben a más DNS-névterekben lévő elnevezett erőforrások feloldásához. Ehelyett használja a feltételes továbbítók az Azure AD DS felügyelt tartományban, hogy megmondja a DNS-kiszolgáló, hová kell menni e címek feloldása érdekében az erőforrásokat.

A feltételes továbbító egy DNS-kiszolgáló konfigurációs lehetősége, amely lehetővé teszi egy DNS-tartomány ( például *contoso.com*) definiálását a lekérdezések továbbításához. Ahelyett, hogy a helyi DNS-kiszolgáló megpróbálná feloldani az adott tartomány rekordjainak lekérdezéseit, a DNS-lekérdezéseket a rendszer továbbítja az adott tartományhoz konfigurált DNS-hez. Ez a konfiguráció biztosítja, hogy a megfelelő DNS-rekordok at adja vissza, mivel nem hoz létre egy helyi DNS-zónát ismétlődő rekordokaz Azure AD DS felügyelt tartományban, hogy tükrözze ezeket az erőforrásokat.

Ha feltételes továbbítót szeretne létrehozni az Azure AD DS felügyelt tartományában, hajtsa végre az alábbi lépéseket:

1. Válassza ki az Azure AD DS DNS-zónáját, például *a aaddscontoso.com*.vb
1. Válassza a **Feltételes továbbítók**lehetőséget, majd a jobb oldali kijelölést, és az **Új feltételes továbbító parancsot...**
1. Adja meg a másik **DNS-tartományát**, például *a contoso.com,* majd adja meg a névtér DNS-kiszolgálóinak IP-címét, ahogy az a következő példában látható:

    ![Feltételes továbbító hozzáadása és konfigurálása a DNS-kiszolgálóhoz](./media/manage-dns/create-conditional-forwarder.png)

1. Jelölje be a **Feltételes továbbító tárolása az Active Directoryban jelölőnégyzetét, majd replikálja az alábbiak szerint,** majd válassza a *tartomány összes DNS-kiszolgálójának*beállítását , ahogy az a következő példában látható:

    ![DNS-konzol - tartomány felügyelete](./media/manage-dns/store-in-domain.png)

    > [!IMPORTANT]
    > Ha a feltételes továbbító a *tartomány*helyett az *erdőben* van tárolva, a feltételes továbbító meghibásodik.

1. A feltételes továbbító létrehozásához válassza az **OK gombot.**

Az Azure AD DS felügyelt tartományhoz csatlakoztatott virtuális gépek más névterekben lévő erőforrások névfeloldása mostmár megfelelően feloldható. A feltételes továbbítóban konfigurált DNS-tartomány lekérdezéseit a rendszer továbbítja a megfelelő DNS-kiszolgálóknak.

## <a name="next-steps"></a>További lépések

A DNS kezeléséről a [Technet dns-eszközökről szóló cikkében olvashat bővebben.](https://technet.microsoft.com/library/cc753579.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
