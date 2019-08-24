---
title: Oktatóanyag – Azure Active Directory Domain Services-példány létrehozása | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és konfigurálhat egy Azure Active Directory Domain Services példányt a Azure Portal használatával.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: iainfou
ms.openlocfilehash: 56cb9a17054faace65ae1b65ed0ddbedf1e8af99
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70011362"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance"></a>Oktatóanyag: Azure Active Directory Domain Services-példány létrehozása és konfigurálása

Azure Active Directory Domain Services (Azure AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint például a tartományhoz való csatlakozás, a csoportházirend, az LDAP, a Kerberos/NTLM hitelesítés, amely teljes mértékben kompatibilis a Windows Server Active Directoryekkel. Ezeket a tartományi szolgáltatásokat a tartományvezérlők üzembe helyezése, kezelése és javítása nélkül használhatja fel. Az Azure AD DS integrálható a meglévő Azure AD-Bérlővel. Ez az integráció lehetővé teszi, hogy a felhasználók a vállalati hitelesítő adataikkal jelentkezzenek be, és meglévő csoportokat és felhasználói fiókokat is használhatnak az erőforrásokhoz való hozzáférés biztosításához.

Ez az oktatóanyag bemutatja, hogyan hozhat létre és konfigurálhat egy Azure AD DS-példányt a Azure Portal használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Felügyelt tartomány DNS-és virtuális hálózati beállításainak konfigurálása
> * Azure AD DS-példány létrehozása
> * Rendszergazda felhasználók hozzáadása a tartományi felügyelethez
> * Jelszókivonat-szinkronizálás engedélyezése

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure][associate-azure-ad-tenant]-előfizetést a fiókjához.
* Az Azure AD DS engedélyezéséhez *globális rendszergazdai* jogosultságok szükségesek az Azure ad-bérlőben.
* Az Azure-előfizetésben *közreműködő* jogosultságokkal kell rendelkeznie a szükséges Azure AD DS-erőforrások létrehozásához.
* Az Azure AD-bérlőt [konfigurálni kell az önkiszolgáló jelszó][configure-sspr]-visszaállításhoz.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ebben az oktatóanyagban az Azure Portal használatával hozza létre és konfigurálja az Azure AD DS-példányt. Első lépésként jelentkezzen be a Azure Portalba [](https://portal.azure.com).

## <a name="create-an-instance-and-configure-basic-settings"></a>Példány létrehozása és alapszintű beállítások konfigurálása

A **Azure ad Domain Services engedélyezése** varázsló elindításához hajtsa végre a következő lépéseket:

1. A Azure Portal bal felső sarkában válassza az **+ erőforrás létrehozása**lehetőséget.
1. Adja meg a *tartományi szolgáltatásokat* a keresési sávban, majd válassza a *Azure ad Domain Services* lehetőséget a keresési javaslatok közül.
1. A Azure AD Domain Services lapon válassza a **Létrehozás**lehetőséget. Elindul a **Azure ad Domain Services engedélyezése** varázsló.

Azure AD DS-példány létrehozásakor meg kell adnia egy DNS-nevet. A DNS-név kiválasztásakor a következő szempontokat kell figyelembe venni:

* **Beépített tartomány neve:** Alapértelmezés szerint a rendszer a könyvtár beépített tartománynevét használja (a *. onmicrosoft.com* utótagot). Ha engedélyezni szeretné a biztonságos LDAP-hozzáférést a felügyelt tartományhoz az interneten keresztül, nem hozhat létre digitális tanúsítványt a kapcsolat védelméhez ezzel az alapértelmezett tartománnyal. A Microsoft tulajdonosa a *. onmicrosoft.com* tartomány, így a hitelesítésszolgáltató (CA) nem bocsát ki tanúsítványt.
* **Egyéni tartománynevek:** A leggyakoribb módszer egy egyéni tartománynév megadása, amely általában az egyik, amelyet már birtokol, és irányítható. Ha irányítható, egyéni tartományt használ, a forgalom az alkalmazások támogatásához szükséges módon képes megfelelően átáramlani.
* **Nem irányítható tartomány utótagja:** Általában azt javasoljuk, hogy kerülje a nem átirányítható tartománynevek utótagját, például a *contoso. local*nevet. A *. local* utótag nem irányítható, és a DNS-feloldással kapcsolatos problémákat okozhat.

A DNS-név következő korlátozásai is érvényesek:

* **Tartományi előtagra vonatkozó korlátozások:** Nem hozhat létre 15 karakternél hosszabb előtaggal rendelkező felügyelt tartományt. A megadott tartománynév előtagja (például a *contoso* a *contoso.com* tartománynévben) legfeljebb 15 karaktert tartalmazhat.
* **Hálózati név ütközése:** A felügyelt tartomány DNS-tartományneve még nem létezik a virtuális hálózaton. Konkrétan a következő helyzetekben érdemes megkeresni a nevek ütközését:
    * Ha már rendelkezik egy Active Directory tartománnyal ugyanazzal a DNS-tartománynévvel az Azure-beli virtuális hálózaton.
    * Ha a virtuális hálózat, amelyen engedélyezni szeretné a felügyelt tartományt, VPN-kapcsolattal rendelkezik a helyszíni hálózattal. Ebben az esetben győződjön meg arról, hogy nem rendelkezik ugyanazzal a DNS-tartománynévvel rendelkező tartománnyal a helyszíni hálózaton.
    * Ha az Azure-beli virtuális hálózaton már van ilyen nevű Azure Cloud Service-szolgáltatás.

Az Azure AD DS-példány létrehozásához a Azure Portal alapismeretek ablakában végezze el a következő mezőket:

1. Adja meg a felügyelt tartományhoz tartozó **DNS-tartománynevet** , figyelembe véve az előző pontokat.
1. Válassza ki azt az Azure-előfizetést, amelyben létre szeretné hozni a felügyelt tartományt.
1. Válassza ki azt az **erőforráscsoportot** , amelyhez a felügyelt tartománynak tartoznia kell. Válassza az **új létrehozása** lehetőséget, vagy válasszon ki egy meglévő erőforráscsoportot.
1. Válassza ki azt az Azure- **helyet** , amelyben létre kívánja hozni a felügyelt tartományt.
1. A **hálózat** szakaszra való áttéréshez kattintson **az OK** gombra.

![Azure AD Domain Services-példány alapvető beállításainak konfigurálása](./media/tutorial-create-instance/basics-window.png)

## <a name="create-and-configure-the-virtual-network"></a>A virtuális hálózat létrehozása és konfigurálása

A kapcsolat biztosításához egy Azure-beli virtuális hálózatra és egy dedikált alhálózatra van szükség. Az Azure AD DS ebben a virtuális hálózati alhálózatban engedélyezve van. Ebben az oktatóanyagban létrehoz egy virtuális hálózatot, de ehelyett egy meglévő virtuális hálózatot is használhat. Mindkét megközelítésben létre kell hoznia egy dedikált alhálózatot az Azure AD DS használatához.

A dedikált virtuális hálózati alhálózat néhány szempontja a következő területeket tartalmazza:

* Az alhálózatnak legalább 3-5 rendelkezésre álló IP-címmel kell rendelkeznie a címtartományból az Azure AD DS-erőforrások támogatásához.
* Ne válassza ki az *átjáró* alhálózatát az Azure AD DS telepítéséhez. Az Azure AD DS üzembe helyezése *átjáró* -alhálózaton nem támogatott.
* Ne helyezzen üzembe más virtuális gépeket az alhálózaton. Az alkalmazások és a virtuális gépek gyakran hálózati biztonsági csoportokat használnak a kapcsolatok biztonságossá tételéhez. Ezen számítási feladatok különálló alhálózatokon való futtatása lehetővé teszi, hogy ezeket a hálózati biztonsági csoportokat a felügyelt tartományhoz való kapcsolódás megszakítása nélkül alkalmazza.
* Az Azure AD DS engedélyezése után nem helyezheti át a felügyelt tartományt egy másik virtuális hálózatra.

A virtuális hálózat megtervezésével és konfigurálásával kapcsolatos további információkért lásd: [Azure Active Directory Domain Services hálózati szempontjai][network-considerations].

A következő módon hajtsa végre a mezőket a *hálózat* ablakban:

1. A **hálózat** ablakban válassza a **virtuális hálózat kiválasztása**lehetőséget.
1. Ebben az oktatóanyagban válassza az **új** virtuális hálózat létrehozása lehetőséget az Azure AD DS üzembe helyezéséhez a alkalmazásban.
1. Adja meg a virtuális hálózat nevét (például *myVnet*), majd adjon meg egy címtartományt, például *10.1.0.0/16*.
1. Hozzon létre egy dedikált alhálózatot egy tiszta névvel, például *DomainServices*. Adjon meg egy címtartományt, például *10.1.0.0/24*.

    ![Virtuális hálózat és alhálózat létrehozása a Azure AD Domain Services-vel való használatra](./media/tutorial-create-instance/create-vnet.png)

    Ügyeljen arra, hogy olyan címtartományt válasszon, amely a magánhálózati IP-címtartomány része. Azok az IP-címtartományok, amelyek nem a nyilvános címtartomány alá tartoznak, hibákat okozhatnak az Azure AD DSon belül.

    > [!TIP]
    > A **virtuális hálózat választása** lapon a meglévő virtuális hálózatok jelennek meg, amelyek a korábban kiválasztott erőforráscsoporthoz és Azure-helyhez tartoznak. Az Azure AD DS üzembe helyezése előtt [létre kell hoznia egy dedikált][create-dedicated-subnet] alhálózatot.

1. Ha a virtuális hálózatot és az alhálózatot létrehozta, a rendszer automatikusan kijelöli az alhálózatot, például a *DomainServices*. Ehelyett kiválaszthat egy másik meglévő alhálózatot is, amely a kiválasztott virtuális hálózat része:

    ![A virtuális hálózaton belül válassza ki a dedikált alhálózatot](./media/tutorial-create-instance/choose-subnet.png)

1. A virtuális hálózat konfigurációjának megerősítéséhez kattintson **az OK gombra** .

## <a name="configure-an-administrative-group"></a>Felügyeleti csoport konfigurálása

Az Azure AD DS-tartomány felügyeletéhez egy *HRE DC-rendszergazdák* nevű speciális felügyeleti csoportot használ a rendszer. Ennek a csoportnak a tagjai rendszergazdai jogosultságokat kapnak a felügyelt tartományhoz csatlakoztatott virtuális gépeken. A tartományhoz csatlakozó virtuális gépeken ez a csoport a helyi rendszergazdák csoportba kerül. Ennek a csoportnak a tagjai is használhatják a Távoli asztalt a tartományhoz csatlakoztatott virtuális gépekhez való távoli csatlakozáshoz.

Nem rendelkezik *tartományi rendszergazdai* vagy *vállalati rendszergazdai* engedélyekkel felügyelt tartományon az Azure AD DS használatával. Ezeket az engedélyeket a szolgáltatás foglalta le, és a bérlőn belüli felhasználók számára nem érhető el. Ehelyett a *HRE DC-rendszergazdák* csoport bizonyos jogosultsági szintű műveletek elvégzését teszi lehetővé. Ezek a műveletek magukban foglalják a számítógépek tartományhoz való csatlakoztatását, a tartományhoz csatlakoztatott virtuális gépek adminisztrációs csoportjának és a Csoportházirend konfigurálásának.

A varázsló automatikusan létrehozza az *HRE DC rendszergazdák* csoportot az Azure ad-címtárban. Ha már létezik ilyen nevű csoport az Azure AD-címtárban, a varázsló kiválasztja ezt a csoportot. A telepítési folyamat során további felhasználókat is hozzáadhat ehhez a *HRE DC-rendszergazdák* csoporthoz. Ezeket a lépéseket később is elvégezheti.

1. Ha további felhasználókat szeretne hozzáadni ehhez a *HRE-tartományvezérlő rendszergazdák* csoportjához, válassza a **csoporttagság kezelése**lehetőséget.
1. Válassza a **Tagok hozzáadása** gombot, majd keresse meg és válassza ki a felhasználókat az Azure ad-címtárból. Például keresse meg a saját fiókját, és adja hozzá a *HRE DC-rendszergazdák* csoporthoz.

    ![A HRE DC-rendszergazdák csoport csoporttagság konfigurálása](./media/tutorial-create-instance/admin-group.png)

1. Amikor elkészült, válassza az **OK** lehetőséget.

## <a name="configure-synchronization"></a>Szinkronizálás konfigurálása

Az Azure AD DS lehetővé teszi az Azure AD-ben elérhető *összes* felhasználó és csoport szinkronizálását, vagy csak bizonyos csoportok *hatókörön* belüli szinkronizálását. Ha úgy dönt, hogy az *összes* felhasználót és csoportot szinkronizálja, később nem dönthet úgy, hogy csak hatókörön belüli szinkronizálást hajt végre. A hatókörön belüli szinkronizálással kapcsolatos további információkért lásd: [Azure ad Domain Services hatókörön belüli szinkronizálás][scoped-sync].

1. Ebben az oktatóanyagban válassza az **összes** felhasználó és csoport szinkronizálása lehetőséget. Ez a szinkronizálási lehetőség az alapértelmezett beállítás.

    ![A felhasználók és csoportok teljes szinkronizálásának végrehajtása az Azure AD-ből](./media/tutorial-create-instance/sync-all.png)

1. Kattintson az **OK** gombra.

## <a name="deploy-your-managed-domain"></a>A felügyelt tartomány üzembe helyezése

A varázsló **Összefoglalás** lapján tekintse át a felügyelt tartomány konfigurációs beállításait. A varázsló bármelyik lépésére visszatérhet a módosítások elvégzéséhez.

1. A felügyelt tartomány létrehozásához kattintson **az OK gombra**.
1. A felügyelt tartomány kiépítés folyamata akár egy órát is igénybe vehet. Egy értesítés jelenik meg a portálon, amely az Azure AD DS üzemelő példányának állapotát mutatja. Válassza ki az értesítést, és tekintse meg az üzembe helyezés részletes folyamatát.

    ![Értesítés a telepítés Azure Portal folyamatban](./media/tutorial-create-instance/deployment-in-progress.png)

1. Válassza ki az erőforráscsoportot, például *myResourceGroup*, majd válassza ki az Azure AD DS példányát az Azure-erőforrások listájából, például *contoso.com*. Az **Áttekintés** lapon látható, hogy a felügyelt tartomány jelenleg *telepítve*van. A felügyelt tartományt nem lehet a teljes kiépítés előtt konfigurálni.

    ![Tartományi szolgáltatások állapota a kiépítési állapotban](./media/tutorial-create-instance/provisioning-in-progress.png)

1. Ha a felügyelt tartomány teljes mértékben kiépítve van, az **Áttekintés** lapon a tartomány állapota *fut*értékre látható.

    ![Tartományi szolgáltatások állapota sikeres kiépítés után](./media/tutorial-create-instance/successfully-provisioned.png)

A kiépítési folyamat során az Azure AD DS két, *tartományvezérlői szolgáltatásokat* és *AzureActiveDirectoryDomainControllerServices* nevű vállalati alkalmazást hoz létre a címtárában. Ezek a vállalati alkalmazások a felügyelt tartomány kiszolgálásához szükségesek. Fontos, hogy ezeket az alkalmazásokat bármikor ne törölje.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Az Azure virtuális hálózat DNS-beállításainak frissítése

Az Azure AD DS sikeres üzembe helyezése után konfigurálja úgy a virtuális hálózatot, hogy engedélyezze más csatlakoztatott virtuális gépek és alkalmazások számára a felügyelt tartomány használatát. A kapcsolat megadásához frissítse a virtuális hálózat DNS-kiszolgálójának beállításait úgy, hogy azok a két IP-címre mutassanak, ahol az Azure AD DS telepítve van.

1. A felügyelt tartomány **Áttekintés** lapján néhány **szükséges konfigurációs lépés**látható. Az első konfigurációs lépés a DNS-kiszolgáló beállításainak frissítése a virtuális hálózathoz. A DNS-beállítások megfelelő konfigurálása után ez a lépés már nem jelenik meg.

    A felsorolt címek a virtuális hálózatban használt tartományvezérlők. Ebben a példában ezek a címek a *10.1.0.4* és a *10.1.0.5*. Később megkeresheti ezeket az IP-címeket a **Tulajdonságok** lapon.

    ![A virtuális hálózat DNS-beállításainak konfigurálása a Azure AD Domain Services IP-címekkel](./media/tutorial-create-instance/configure-dns.png)

1. A DNS-kiszolgáló virtuális hálózatra vonatkozó beállításainak frissítéséhez kattintson a **Konfigurálás** gombra. A DNS-beállítások konfigurálása automatikusan megtörténik a virtuális hálózathoz.

> [!TIP]
> Ha az előző lépésekben egy meglévő virtuális hálózatot jelölt ki, a hálózathoz csatlakozó virtuális gépek csak újraindítás után kapják meg az új DNS-beállításokat. A virtuális gépeket a Azure Portal, Azure PowerShell vagy az Azure CLI használatával indíthatja újra.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Felhasználói fiókok engedélyezése az Azure AD DS

A felügyelt tartományon lévő felhasználók hitelesítéséhez az Azure AD DS a jelszó-kivonatokat az NT LAN Manager (NTLM) és a Kerberos-hitelesítéshez megfelelő formátumban kell megadni. Az Azure AD az NTLM-vagy Kerberos-hitelesítéshez szükséges formátumban nem állítja elő vagy tárolja a jelszó-kivonatokat, amíg nem engedélyezi az Azure-AD DS a bérlő számára. Biztonsági okokból az Azure AD nem tárolja a jelszó hitelesítő adatait a tiszta szöveges formában. Ezért az Azure AD nem tudja automatikusan előállítani ezeket az NTLM-vagy Kerberos-jelszó-kivonatokat a felhasználók meglévő hitelesítő adatai alapján.

> [!NOTE]
> A megfelelő konfigurálást követően a rendszer a használható jelszavak kivonatait az Azure AD DS felügyelt tartományában tárolja. Ha törli az Azure AD DS felügyelt tartományt, az adott ponton tárolt jelszó-kivonatok is törlődnek. Az Azure AD-ben szinkronizált hitelesítő adatok nem használhatók újra, ha később Azure AD DS felügyelt tartományt hoz létre – újra kell konfigurálnia a jelszó-kivonatolási szinkronizálást, hogy a jelszó-kivonatok újra tárolva legyenek. Korábban a tartományhoz csatlakoztatott virtuális gépek vagy felhasználók nem tudják azonnal hitelesíteni magukat – az Azure AD-nek az új Azure AD DS felügyelt tartományban kell megadnia és tárolnia a jelszó-kivonatokat. További információ: jelszó- [kivonatolási szinkronizálási folyamat az Azure AD DS és Azure ad Connect][password-hash-sync-process].

A jelszó-kivonatok létrehozásához és tárolásához szükséges lépések eltérnek az Azure AD-ben létrehozott kizárólag felhőalapú felhasználói fiókok és a helyszíni címtárból a Azure AD Connect használatával szinkronizált felhasználói fiókok esetében. A csak felhőalapú felhasználói fiókok olyan fiókok, amelyek az Azure AD-címtárban lettek létrehozva az Azure Portal vagy Azure AD PowerShell-parancsmagok használatával. Ezek a felhasználói fiókok nem szinkronizálhatók a helyszíni címtárból. Ebben az oktatóanyagban egy egyszerű, csak felhőalapú felhasználói fiókkal dolgozunk. További információ a Azure AD Connect használatához szükséges további lépésekről: jelszó-kivonatolások szinkronizálása a helyszíni [ad-ből a felügyelt tartományba szinkronizált felhasználói fiókokhoz][on-prem-sync].

> [!TIP]
> Ha az Azure AD-bérlő csak felhőalapú felhasználókat és felhasználókat tartalmaz a helyszíni AD-ből, mindkét lépést végre kell hajtania.

A csak felhőalapú felhasználói fiókok esetében a felhasználóknak meg kell változtatniuk a jelszavukat, mielőtt használniuk tudják az Azure AD DS-t. Ez a jelszó-módosítási folyamat okozza a Kerberos és az NTLM hitelesítés jelszavas kivonatait az Azure AD-ben való létrehozásához és tárolásához. A bérlő minden olyan felhasználójának jelszavait lemondhatja, akinek az Azure AD DSt kell használnia, amely a következő bejelentkezéskor a jelszó megváltoztatását kényszeríti, vagy arra utasítja őket, hogy manuálisan módosítsa a jelszavukat. Ebben az oktatóanyagban manuálisan módosítunk egy felhasználói jelszót.

Mielőtt egy felhasználó visszaállíthatja a jelszavát, az Azure AD-bérlőt [konfigurálni kell az önkiszolgáló jelszó][configure-sspr]-visszaállításhoz.

A csak felhőalapú felhasználók jelszavának módosításához a felhasználónak a következő lépéseket kell elvégeznie:

1. Nyissa meg az Azure AD hozzáférési panel lapját a következő címen: [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. A jobb felső sarokban válassza ki a nevét, majd válassza a **profil** lehetőséget a legördülő menüből.

    ![Profil kiválasztása](./media/tutorial-create-instance/select-profile.png)

1. A **profil** lapon válassza a **jelszó módosítása**lehetőséget.
1. A **jelszó módosítása** lapon adja meg a meglévő (régi) jelszavát, majd írja be és erősítse meg az új jelszót.
1. Válassza ki **elküldése**.

Néhány percet vesz igénybe, miután módosította a jelszavát, hogy az új jelszót használni lehessen az Azure AD DSban. Körülbelül 20 perc elteltével az új jelszó használatával bejelentkezhet a felügyelt tartományhoz csatlakoztatott számítógépekre.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Felügyelt tartomány DNS-és virtuális hálózati beállításainak konfigurálása
> * Azure AD DS-példány létrehozása
> * Rendszergazda felhasználók hozzáadása a tartományi felügyelethez
> * Felhasználói fiókok engedélyezése az Azure AD DS számára és jelszó-kivonatok előállítása

A felügyelt tartomány működés közbeni megtekintéséhez hozzon létre egy virtuális gépet, és csatlakozzon a tartományhoz.

> [!div class="nextstepaction"]
> [Windows Server rendszerű virtuális gép csatlakoztatása a felügyelt tartományhoz](join-windows-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
