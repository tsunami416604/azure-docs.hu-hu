---
title: Oktatóanyag – Azure Active Directory Domain Services-példány létrehozása | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és konfigurálhat egy Azure Active Directory Domain Services-példányt, és hogyan adhat meg speciális konfigurációs beállításokat a Azure Portal használatával.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: f2d7f1725623dcc031f3c2b36bacd6dbc9ad339d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81639952"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance-with-advanced-configuration-options"></a>Oktatóanyag: Azure Active Directory Domain Services-példány létrehozása és konfigurálása speciális konfigurációs beállításokkal

Azure Active Directory Domain Services (Azure AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint például a tartományhoz való csatlakozás, a csoportházirend, az LDAP, a Kerberos/NTLM hitelesítés, amely teljes mértékben kompatibilis a Windows Server Active Directoryekkel. Ezeket a tartományi szolgáltatásokat a tartományvezérlők üzembe helyezése, kezelése és javítása nélkül használhatja fel. Az Azure AD DS integrálható a meglévő Azure AD-Bérlővel. Ez az integráció lehetővé teszi, hogy a felhasználók a vállalati hitelesítő adataikkal jelentkezzenek be, és meglévő csoportokat és felhasználói fiókokat is használhatnak az erőforrásokhoz való hozzáférés biztosításához.

Felügyelt tartományt a Hálózatkezelés és a szinkronizálás [alapértelmezett konfigurációs beállításaival hozhat létre][tutorial-create-instance] , vagy manuálisan is megadhatja ezeket a beállításokat. Ez az oktatóanyag bemutatja, hogyan határozhatja meg ezeket a speciális konfigurációs beállításokat Azure AD DS-példányok létrehozásához és konfigurálásához a Azure Portal használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Felügyelt tartomány DNS-és virtuális hálózati beállításainak konfigurálása
> * Azure AD DS-példány létrehozása
> * Rendszergazda felhasználók hozzáadása a tartományi felügyelethez
> * Jelszókivonat szinkronizálásának engedélyezése

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Az Azure AD DS engedélyezéséhez *globális rendszergazdai* jogosultságok szükségesek az Azure ad-bérlőben.
* Az Azure-előfizetésben *közreműködő* jogosultságokkal kell rendelkeznie a szükséges Azure AD DS-erőforrások létrehozásához.

Bár az Azure AD DS esetében nem szükséges, az Azure AD-bérlő számára ajánlott az önkiszolgáló [jelszó-visszaállítás (SSPR) konfigurálása][configure-sspr] . A felhasználók SSPR nélkül is módosíthatják a jelszavukat, de a SSPR segít, ha elfelejtik a jelszavukat, és vissza kell állítani.

> [!IMPORTANT]
> Miután létrehozta az Azure AD DS felügyelt tartományt, nem helyezheti át a példányt másik erőforráscsoporthoz, virtuális hálózatba, előfizetésbe stb. Ügyeljen arra, hogy a legmegfelelőbb előfizetést, erőforráscsoportot, régiót és virtuális hálózatot válassza ki az Azure AD DS-példány telepítésekor.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ebben az oktatóanyagban az Azure Portal használatával hozza létre és konfigurálja az Azure AD DS-példányt. Első lépésként jelentkezzen be a [Azure Portalba](https://portal.azure.com).

## <a name="create-an-instance-and-configure-basic-settings"></a>Példány létrehozása és alapszintű beállítások konfigurálása

A **Azure ad Domain Services engedélyezése** varázsló elindításához hajtsa végre a következő lépéseket:

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.
1. Adja meg a *tartományi szolgáltatásokat* a keresési sávban, majd válassza a *Azure ad Domain Services* lehetőséget a keresési javaslatok közül.
1. A Azure AD Domain Services lapon válassza a **Létrehozás**lehetőséget. Elindul a **Azure ad Domain Services engedélyezése** varázsló.
1. Válassza ki azt az Azure- **előfizetést** , amelyben létre szeretné hozni a felügyelt tartományt.
1. Válassza ki azt az **erőforráscsoportot** , amelyhez a felügyelt tartománynak tartoznia kell. Válassza az **új létrehozása** lehetőséget, vagy válasszon ki egy meglévő erőforráscsoportot.

Azure AD DS-példány létrehozásakor meg kell adnia egy DNS-nevet. A DNS-név kiválasztásakor a következő szempontokat kell figyelembe venni:

* **Beépített tartomány neve:** Alapértelmezés szerint a rendszer a könyvtár beépített tartománynevét használja (a *. onmicrosoft.com* utótagot). Ha engedélyezni szeretné a biztonságos LDAP-hozzáférést a felügyelt tartományhoz az interneten keresztül, nem hozhat létre digitális tanúsítványt a kapcsolat védelméhez ezzel az alapértelmezett tartománnyal. A Microsoft tulajdonosa a *. onmicrosoft.com* tartomány, így a hitelesítésszolgáltató (CA) nem bocsát ki tanúsítványt.
* **Egyéni tartománynevek:** A leggyakoribb módszer egy egyéni tartománynév megadása, amely általában az egyik, amelyet már birtokol, és irányítható. Ha irányítható, egyéni tartományt használ, a forgalom az alkalmazások támogatásához szükséges módon képes megfelelően átáramlani.
* **Nem irányítható tartomány utótagja:** Általában azt javasoljuk, hogy kerülje a nem átirányítható tartománynevek utótagját, például a *contoso. local*nevet. A *. local* utótag nem irányítható, és a DNS-feloldással kapcsolatos problémákat okozhat.

> [!TIP]
> Ha egyéni tartománynevet hoz létre, gondoskodjon a meglévő DNS-névterekről. Azt javasoljuk, hogy a tartománynevet a meglévő Azure-beli vagy helyszíni DNS-névtértől elkülönítve használja.
>
> Ha például meglévő DNS- *contoso.com*rendelkezik, hozzon létre egy Azure AD DS felügyelt tartományt a *aaddscontoso.com*egyéni tartománynevével. Ha biztonságos LDAP-t kell használnia, regisztrálnia kell az egyéni tartománynevet a szükséges tanúsítványok létrehozásához.
>
> Előfordulhat, hogy létre kell hoznia néhány további DNS-rekordot a környezetében lévő más szolgáltatásokhoz, vagy feltételes DNS-továbbítókat a környezet meglévő DNS-neve között. Ha például olyan webkiszolgálót futtat, amely a gyökér DNS-nevet használja, akkor olyan elnevezési ütközések lehetnek, amelyek további DNS-bejegyzéseket igényelnek.
>
> Ezekben az oktatóanyagokban és útmutatókban a *aaddscontoso.com* egyéni tartományát használjuk rövid példaként. Az összes parancsban adja meg a saját tartománynevét.

A DNS-név következő korlátozásai is érvényesek:

* **Tartományi előtagra vonatkozó korlátozások:** Nem hozhat létre 15 karakternél hosszabb előtaggal rendelkező felügyelt tartományt. A megadott tartománynév előtagja (például a *aaddscontoso.com* tartomány *aaddscontoso* ) legalább 15 karakterből állhat.
* **Hálózati név ütközése:** A felügyelt tartomány DNS-tartományneve még nem létezik a virtuális hálózaton. Konkrétan a következő helyzetekben érdemes megkeresni a nevek ütközését:
    * Ha már rendelkezik egy Active Directory tartománnyal ugyanazzal a DNS-tartománynévvel az Azure-beli virtuális hálózaton.
    * Ha a virtuális hálózat, amelyen engedélyezni szeretné a felügyelt tartományt, VPN-kapcsolattal rendelkezik a helyszíni hálózattal. Ebben az esetben győződjön meg arról, hogy nem rendelkezik ugyanazzal a DNS-tartománynévvel rendelkező tartománnyal a helyszíni hálózaton.
    * Ha az Azure-beli virtuális hálózaton már van ilyen nevű Azure Cloud Service-szolgáltatás.

Az Azure AD DS-példány létrehozásához a Azure Portal *alapismeretek* ablakában végezze el a következő mezőket:

1. Adja meg a felügyelt tartományhoz tartozó **DNS-tartománynevet** , figyelembe véve az előző pontokat.
1. Válassza ki azt az Azure- **helyet** , amelyben létre kívánja hozni a felügyelt tartományt. Ha olyan régiót választ, amely támogatja az Availability Zones-t, az Azure AD DS erőforrásai a további redundancia érdekében a zónák között oszlanak meg.

    A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosítása érdekében minden engedélyezett régióban legalább három elkülönített zóna található.

    Nem kell konfigurálnia az Azure AD DS a zónák közötti elosztására. Az Azure platform automatikusan kezeli az erőforrások zónájának eloszlását. További információért és a régiók rendelkezésre állásának megtekintéséhez lásd: [Mi a Availability Zones az Azure-ban?][availability-zones]

1. Az **SKU** meghatározza a létrehozható erdőszintű megbízhatósági kapcsolatok teljesítményét, biztonsági mentési gyakoriságát és maximális számát. Az SKU-t a felügyelt tartomány létrehozása után is módosíthatja, ha az üzleti igények vagy követelmények megváltoznak. További információ: [Azure AD DS SKU-fogalmak][concepts-sku].

    Ebben az oktatóanyagban válassza ki a *szabványos* SKU-t.
1. Az *erdők* egy Active Directory tartományi szolgáltatások által az egy vagy több tartomány csoportosításához használt logikai szerkezetek. Alapértelmezés szerint az Azure AD DS felügyelt tartomány *felhasználói* erdőként jön létre. Ez a típusú erdő az Azure AD összes objektumát szinkronizálja, beleértve a helyszíni AD DS környezetben létrehozott felhasználói fiókokat is. Az *erőforrás* -erdő csak a közvetlenül az Azure ad-ben létrehozott felhasználókat és csoportokat szinkronizálja. Az erőforrás-erdők jelenleg előzetes verzióban érhetők el. Az *erőforrás* -erdőkkel kapcsolatos további információkért, beleértve az egyiket, és hogyan hozhat létre erdőszintű megbízhatósági kapcsolatot a helyszíni AD DS-tartományokkal, tekintse meg az [Azure AD DS Resource Forests – áttekintés][resource-forests]című témakört.

    Ebben az oktatóanyagban válassza a *felhasználói* erdő létrehozása lehetőséget.

    ![Azure AD Domain Services-példány alapvető beállításainak konfigurálása](./media/tutorial-create-instance-advanced/basics-window.png)

1. További beállítások manuális konfigurálásához válassza a **következő hálózat**lehetőséget. Ellenkező esetben válassza a **felülvizsgálat + létrehozás** lehetőséget az alapértelmezett konfigurációs beállítások elfogadásához, majd ugorjon a [felügyelt tartomány üzembe helyezését](#deploy-the-managed-domain)ismertető szakaszra. A létrehozási lehetőség kiválasztása esetén a következő alapértelmezett beállítások vannak konfigurálva:

    * Létrehoz egy *aadds-vnet* nevű virtuális hálózatot, amely a *10.0.1.0/24*IP-címtartományt használja.
    * Létrehoz egy *aadds-alhálózat* nevű alhálózatot a *10.0.1.0/24*IP-címtartomány használatával.
    * Szinkronizálja az Azure AD *összes* felhasználóját az Azure AD DS felügyelt tartományba.

## <a name="create-and-configure-the-virtual-network"></a>A virtuális hálózat létrehozása és konfigurálása

A kapcsolat biztosításához egy Azure-beli virtuális hálózatra és egy dedikált alhálózatra van szükség. Az Azure AD DS ebben a virtuális hálózati alhálózatban engedélyezve van. Ebben az oktatóanyagban létrehoz egy virtuális hálózatot, de ehelyett egy meglévő virtuális hálózatot is használhat. Mindkét megközelítésben létre kell hoznia egy dedikált alhálózatot az Azure AD DS használatához.

A dedikált virtuális hálózati alhálózat néhány szempontja a következő területeket tartalmazza:

* Az alhálózatnak legalább 3-5 rendelkezésre álló IP-címmel kell rendelkeznie a címtartományból az Azure AD DS-erőforrások támogatásához.
* Ne válassza ki az *átjáró* alhálózatát az Azure AD DS telepítéséhez. Az Azure AD DS üzembe helyezése *átjáró* -alhálózaton nem támogatott.
* Ne helyezzen üzembe más virtuális gépeket az alhálózaton. Az alkalmazások és a virtuális gépek gyakran hálózati biztonsági csoportokat használnak a kapcsolatok biztonságossá tételéhez. Ezen számítási feladatok különálló alhálózatokon való futtatása lehetővé teszi, hogy ezeket a hálózati biztonsági csoportokat a felügyelt tartományhoz való kapcsolódás megszakítása nélkül alkalmazza.
* Az Azure AD DS engedélyezése után nem helyezheti át a felügyelt tartományt egy másik virtuális hálózatra.

A virtuális hálózat megtervezésével és konfigurálásával kapcsolatos további információkért lásd: [Azure Active Directory Domain Services hálózati szempontjai][network-considerations].

A következő módon hajtsa végre a mezőket a *hálózat* ablakban:

1. A **hálózat** lapon válassza ki a virtuális hálózatot az Azure-AD DS üzembe helyezéséhez a legördülő menüből, vagy válassza az **új létrehozása**lehetőséget.
    1. Ha úgy dönt, hogy létrehoz egy virtuális hálózatot, adjon meg egy nevet a virtuális hálózatnak (például *myVnet*), majd adjon meg egy címtartományt, például *10.0.1.0/24*.
    1. Hozzon létre egy dedikált alhálózatot egy tiszta névvel, például *DomainServices*. Adjon meg egy címtartományt, például *10.0.1.0/24*.

    [![](./media/tutorial-create-instance-advanced/create-vnet.png "Create a virtual network and subnet for use with Azure AD Domain Services")](./media/tutorial-create-instance-advanced/create-vnet-expanded.png#lightbox)

    Ügyeljen arra, hogy olyan címtartományt válasszon, amely a magánhálózati IP-címtartomány része. Azok az IP-címtartományok, amelyek nem a nyilvános címtartomány alá tartoznak, hibákat okozhatnak az Azure AD DSon belül.

1. Válasszon ki egy virtuális hálózati alhálózatot, például *DomainServices*.
1. Ha elkészült, válassza a **következő – felügyelet**lehetőséget.

## <a name="configure-an-administrative-group"></a>Felügyeleti csoport konfigurálása

Az Azure AD DS-tartomány felügyeletéhez egy *HRE DC-rendszergazdák* nevű speciális felügyeleti csoportot használ a rendszer. Ennek a csoportnak a tagjai rendszergazdai jogosultságokat kapnak a felügyelt tartományhoz csatlakoztatott virtuális gépeken. A tartományhoz csatlakozó virtuális gépeken ez a csoport a helyi rendszergazdák csoportba kerül. Ennek a csoportnak a tagjai is használhatják a Távoli asztalt a tartományhoz csatlakoztatott virtuális gépekhez való távoli csatlakozáshoz.

Nem rendelkezik *tartományi rendszergazdai* vagy *vállalati rendszergazdai* engedélyekkel felügyelt tartományon az Azure AD DS használatával. Ezeket az engedélyeket a szolgáltatás foglalta le, és a bérlőn belüli felhasználók számára nem érhető el. Ehelyett a *HRE DC-rendszergazdák* csoport bizonyos jogosultsági szintű műveletek elvégzését teszi lehetővé. Ezek a műveletek magukban foglalják a tartományhoz csatlakoztatott virtuális gépek adminisztrációs csoportját, valamint a Csoportházirend konfigurálását.

A varázsló automatikusan létrehozza az *HRE DC rendszergazdák* csoportot az Azure ad-címtárban. Ha már létezik ilyen nevű csoport az Azure AD-címtárban, a varázsló kiválasztja ezt a csoportot. A telepítési folyamat során további felhasználókat is hozzáadhat ehhez a *HRE DC-rendszergazdák* csoporthoz. Ezeket a lépéseket később is elvégezheti.

1. Ha további felhasználókat szeretne hozzáadni ehhez a *HRE-tartományvezérlő rendszergazdák* csoportjához, válassza a **csoporttagság kezelése**lehetőséget.

    ![A HRE DC-rendszergazdák csoport csoporttagság konfigurálása](./media/tutorial-create-instance-advanced/admin-group.png)

1. Válassza a **Tagok hozzáadása** gombot, majd keresse meg és válassza ki a felhasználókat az Azure ad-címtárból. Például keresse meg a saját fiókját, és adja hozzá a *HRE DC-rendszergazdák* csoporthoz.
1. Ha szeretné, módosítsa vagy vegyen fel további címzetteket az értesítésekhez, ha az Azure AD DS felügyelt tartományában riasztások vannak, amelyeknek figyelmet igényelnek.
1. Ha elkészült, válassza a **következő szinkronizálás**lehetőséget.

## <a name="configure-synchronization"></a>A szinkronizálás konfigurálása

Az Azure AD DS lehetővé teszi az Azure AD-ben elérhető *összes* felhasználó és csoport szinkronizálását, vagy csak bizonyos csoportok *hatókörön* belüli szinkronizálását. Ha úgy dönt, hogy az *összes* felhasználót és csoportot szinkronizálja, később nem dönthet úgy, hogy csak hatókörön belüli szinkronizálást hajt végre. A hatókörön belüli szinkronizálással kapcsolatos további információkért lásd: [Azure ad Domain Services hatókörön belüli szinkronizálás][scoped-sync].

1. Ebben az oktatóanyagban válassza az **összes** felhasználó és csoport szinkronizálása lehetőséget. Ez a szinkronizálási lehetőség az alapértelmezett beállítás.

    ![A felhasználók és csoportok teljes szinkronizálásának végrehajtása az Azure AD-ből](./media/tutorial-create-instance-advanced/sync-all.png)

1. Válassza az **Áttekintés + létrehozás** lehetőséget.

## <a name="deploy-the-managed-domain"></a>A felügyelt tartomány üzembe helyezése

A varázsló **Összefoglalás** lapján tekintse át a felügyelt tartomány konfigurációs beállításait. A varázsló bármelyik lépésére visszatérhet a módosítások elvégzéséhez. Egy Azure AD DS felügyelt tartomány egy másik Azure AD-bérlőre való **újbóli**üzembe helyezéséhez, ha ezeket a konfigurációs beállításokat használja, le is töltheti az Automation-sablont.

1. A felügyelt tartomány létrehozásához válassza a **Létrehozás**lehetőséget. Megjelenik egy megjegyzés, amely szerint bizonyos konfigurációs beállítások (például a DNS-név vagy a virtuális hálózat) nem módosíthatók, ha az Azure-AD DS felügyelve lett létrehozva. A folytatáshoz kattintson **az OK gombra**.
1. A felügyelt tartomány kiépítés folyamata akár egy órát is igénybe vehet. Egy értesítés jelenik meg a portálon, amely az Azure AD DS üzemelő példányának állapotát mutatja. Válassza ki az értesítést, és tekintse meg az üzembe helyezés részletes folyamatát.

    ![Értesítés a telepítés Azure Portal folyamatban](./media/tutorial-create-instance-advanced/deployment-in-progress.png)

1. Válassza ki az erőforráscsoportot, például *myResourceGroup*, majd válassza ki az Azure AD DS példányát az Azure-erőforrások listájából, például *aaddscontoso.com*. Az **Áttekintés** lapon látható, hogy a felügyelt tartomány jelenleg *telepítve*van. A felügyelt tartományt nem lehet a teljes kiépítés előtt konfigurálni.

    ![Tartományi szolgáltatások állapota a kiépítési állapotban](./media/tutorial-create-instance-advanced/provisioning-in-progress.png)

1. Ha a felügyelt tartomány teljes mértékben kiépítve van, az **Áttekintés** lapon a tartomány állapota *fut*értékre látható.

    ![Tartományi szolgáltatások állapota sikeres kiépítés után](./media/tutorial-create-instance-advanced/successfully-provisioned.png)

A felügyelt tartomány társítva van az Azure AD-bérlőhöz. A kiépítési folyamat során az Azure AD DS két, *tartományvezérlői szolgáltatásokat* és *AzureActiveDirectoryDomainControllerServices* nevű vállalati alkalmazást hoz létre az Azure ad-bérlőben. Ezek a vállalati alkalmazások a felügyelt tartomány kiszolgálásához szükségesek. Ne törölje ezeket az alkalmazásokat.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Az Azure virtuális hálózat DNS-beállításainak frissítése

Az Azure AD DS sikeres üzembe helyezése után konfigurálja úgy a virtuális hálózatot, hogy engedélyezze más csatlakoztatott virtuális gépek és alkalmazások számára a felügyelt tartomány használatát. A kapcsolat megadásához frissítse a virtuális hálózat DNS-kiszolgálójának beállításait úgy, hogy azok a két IP-címre mutassanak, ahol az Azure AD DS telepítve van.

1. A felügyelt tartomány **Áttekintés** lapján néhány **szükséges konfigurációs lépés**látható. Az első konfigurációs lépés a DNS-kiszolgáló beállításainak frissítése a virtuális hálózathoz. A DNS-beállítások megfelelő konfigurálása után ez a lépés már nem jelenik meg.

    A felsorolt címek a virtuális hálózatban használt tartományvezérlők. Ebben a példában ezek a címek a *10.1.0.4* és a *10.1.0.5*. Később megkeresheti ezeket az IP-címeket a **Tulajdonságok** lapon.

    ![A virtuális hálózat DNS-beállításainak konfigurálása a Azure AD Domain Services IP-címekkel](./media/tutorial-create-instance-advanced/configure-dns.png)

1. A DNS-kiszolgáló virtuális hálózatra vonatkozó beállításainak frissítéséhez kattintson a **Konfigurálás** gombra. A DNS-beállítások konfigurálása automatikusan megtörténik a virtuális hálózathoz.

> [!TIP]
> Ha az előző lépésekben egy meglévő virtuális hálózatot jelölt ki, a hálózathoz csatlakozó virtuális gépek csak újraindítás után kapják meg az új DNS-beállításokat. A virtuális gépeket a Azure Portal, Azure PowerShell vagy az Azure CLI használatával indíthatja újra.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Felhasználói fiókok engedélyezése az Azure AD DS

A felügyelt tartományon lévő felhasználók hitelesítéséhez az Azure AD DS a jelszó-kivonatokat az NT LAN Manager (NTLM) és a Kerberos-hitelesítéshez megfelelő formátumban kell megadni. Az Azure AD az NTLM-vagy Kerberos-hitelesítéshez szükséges formátumban nem állítja elő vagy tárolja a jelszó-kivonatokat, amíg nem engedélyezi az Azure-AD DS a bérlő számára. Biztonsági okokból az Azure AD nem tárolja a jelszó hitelesítő adatait a tiszta szöveges formában. Ezért az Azure AD nem tudja automatikusan előállítani ezeket az NTLM-vagy Kerberos-jelszó-kivonatokat a felhasználók meglévő hitelesítő adatai alapján.

> [!NOTE]
> A megfelelő konfigurálást követően a rendszer a használható jelszavak kivonatait az Azure AD DS felügyelt tartományában tárolja. Ha törli az Azure AD DS felügyelt tartományt, az adott ponton tárolt jelszó-kivonatok is törlődnek. Az Azure AD-ben szinkronizált hitelesítő adatok nem használhatók újra, ha később Azure AD DS felügyelt tartományt hoz létre – újra kell konfigurálnia a jelszó-kivonatolási szinkronizálást, hogy a jelszó-kivonatok újra tárolva legyenek. Korábban a tartományhoz csatlakoztatott virtuális gépek vagy felhasználók nem tudják azonnal hitelesíteni magukat – az Azure AD-nek az új Azure AD DS felügyelt tartományban kell megadnia és tárolnia a jelszó-kivonatokat. További információ: jelszó- [kivonatolási szinkronizálási folyamat az Azure AD DS és Azure ad Connect][password-hash-sync-process].

A jelszó-kivonatok létrehozásához és tárolásához szükséges lépések eltérnek az Azure AD-ben létrehozott kizárólag felhőalapú felhasználói fiókok és a helyszíni címtárból a Azure AD Connect használatával szinkronizált felhasználói fiókok esetében. A csak felhőalapú felhasználói fiókok olyan fiókok, amelyek az Azure AD-címtárban lettek létrehozva az Azure Portal vagy Azure AD PowerShell-parancsmagok használatával. Ezek a felhasználói fiókok nem szinkronizálhatók a helyszíni címtárból. Ebben az oktatóanyagban egy egyszerű, csak felhőalapú felhasználói fiókkal dolgozunk. További információ a Azure AD Connect használatához szükséges további lépésekről: [jelszó-kivonatolások szinkronizálása a helyszíni ad-ből a felügyelt tartományba szinkronizált felhasználói fiókokhoz][on-prem-sync].

> [!TIP]
> Ha az Azure AD-bérlő csak felhőalapú felhasználókat és felhasználókat tartalmaz a helyszíni AD-ből, mindkét lépést végre kell hajtania.

A csak felhőalapú felhasználói fiókok esetében a felhasználóknak meg kell változtatniuk a jelszavukat, mielőtt használniuk tudják az Azure AD DS-t. Ez a jelszó-módosítási folyamat okozza a Kerberos és az NTLM hitelesítés jelszavas kivonatait az Azure AD-ben való létrehozásához és tárolásához. A fiók nincs szinkronizálva az Azure AD-ből az Azure AD DSig, amíg meg nem változtatják a jelszót. Vagy járjon le a bérlő összes olyan felhőalapú felhasználójának jelszavával, akinek az Azure AD DS-t kell használnia, amely a következő bejelentkezéskor kényszeríti a jelszót, vagy utasítja a Felhőbeli felhasználókat, hogy manuálisan módosítsák a jelszavukat. Ebben az oktatóanyagban manuálisan módosítunk egy felhasználói jelszót.

Mielőtt egy felhasználó visszaállíthatja a jelszavát, az Azure AD-bérlőt [konfigurálni kell az önkiszolgáló jelszó-visszaállításhoz][configure-sspr].

A csak felhőalapú felhasználók jelszavának módosításához a felhasználónak a következő lépéseket kell elvégeznie:

1. Nyissa meg az Azure AD hozzáférési panel lapját a következő címen: [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. A jobb felső sarokban válassza ki a nevét, majd válassza a **profil** lehetőséget a legördülő menüből.

    ![Profil kiválasztása](./media/tutorial-create-instance-advanced/select-profile.png)

1. A **profil** lapon válassza a **jelszó módosítása**lehetőséget.
1. A **jelszó módosítása** lapon adja meg a meglévő (régi) jelszavát, majd írja be és erősítse meg az új jelszót.
1. Válassza a **Küldés** lehetőséget.

Néhány percet vesz igénybe, miután módosította az új jelszóhoz tartozó jelszót, hogy az Azure AD DS használható legyen, és hogy sikeresen bejelentkezzen a felügyelt tartományhoz csatlakoztatott számítógépekre.

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
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
