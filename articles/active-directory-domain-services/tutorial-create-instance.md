---
title: Oktatóanyag – Azure Active Directory Domain Services-példány létrehozása | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és konfigurálhat egy Azure Active Directory Domain Services példányt a Azure Portal használatával.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 2a1fbe8d47af8a2215b0d0a3d81fbe67a62d4755
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474431"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance"></a>Oktatóanyag: Azure Active Directory Domain Services-példány létrehozása és konfigurálása

Azure Active Directory Domain Services (Azure AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint például a tartományhoz való csatlakozás, a csoportházirend, az LDAP, a Kerberos/NTLM hitelesítés, amely teljes mértékben kompatibilis a Windows Server Active Directoryekkel. Ezeket a tartományi szolgáltatásokat a tartományvezérlők üzembe helyezése, kezelése és javítása nélkül használhatja fel. Az Azure AD DS integrálható a meglévő Azure AD-Bérlővel. Ez az integráció lehetővé teszi, hogy a felhasználók a vállalati hitelesítő adataikkal jelentkezzenek be, és meglévő csoportokat és felhasználói fiókokat is használhatnak az erőforrásokhoz való hozzáférés biztosításához.

Felügyelt tartományt a Hálózatkezelés és a szinkronizálás alapértelmezett konfigurációs beállításaival hozhat létre, vagy [manuálisan is megadhatja ezeket a beállításokat][tutorial-create-instance-advanced]. Ez az oktatóanyag bemutatja, hogyan használható az alapértelmezett beállítások egy Azure AD DS-példány létrehozására és konfigurálására a Azure Portal használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A felügyelt tartomány DNS-követelményeinek megismerése
> * Azure AD DS-példány létrehozása
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

## <a name="create-an-instance"></a>Példány létrehozása

A **Azure ad Domain Services engedélyezése** varázsló elindításához hajtsa végre a következő lépéseket:

1. A Azure Portal menüben vagy a **Kezdőlap** lapon válassza az **erőforrás létrehozása**lehetőséget.
1. Adja meg a *tartományi szolgáltatásokat* a keresési sávban, majd válassza a *Azure ad Domain Services* lehetőséget a keresési javaslatok közül.
1. A Azure AD Domain Services lapon válassza a **Létrehozás**lehetőséget. Elindul a **Azure ad Domain Services engedélyezése** varázsló.
1. Válassza ki azt az Azure- **előfizetést** , amelyben létre szeretné hozni a felügyelt tartományt.
1. Válassza ki azt az **erőforráscsoportot** , amelyhez a felügyelt tartománynak tartoznia kell. Válassza az **új létrehozása** lehetőséget, vagy válasszon ki egy meglévő erőforráscsoportot.

Azure AD DS-példány létrehozásakor meg kell adnia egy DNS-nevet. A DNS-név kiválasztásakor a következő szempontokat kell figyelembe venni:

* **Beépített tartomány neve:** Alapértelmezés szerint a rendszer a könyvtár beépített tartománynevét használja (a *. onmicrosoft.com* utótagot). Ha engedélyezni szeretné a biztonságos LDAP-hozzáférést a felügyelt tartományhoz az interneten keresztül, nem hozhat létre digitális tanúsítványt a kapcsolat védelméhez ezzel az alapértelmezett tartománnyal. A Microsoft tulajdonosa a *. onmicrosoft.com* tartomány, így a hitelesítésszolgáltató (CA) nem bocsát ki tanúsítványt.
* **Egyéni tartománynevek:** A leggyakoribb módszer egy egyéni tartománynév megadása, amely általában az egyik, amelyet már birtokol, és irányítható. Ha irányítható, egyéni tartományt használ, a forgalom az alkalmazások támogatásához szükséges módon képes megfelelően átáramlani.
* **Nem irányítható tartomány utótagja:** Általában azt javasoljuk, hogy kerülje a nem átirányítható tartománynevek utótagját, például a *contoso. local*nevet. A *. local* utótag nem irányítható, és a DNS-feloldással kapcsolatos problémákat okozhat.

> [!TIP]
> Ha egyéni tartománynevet hoz létre, gondoskodjon a meglévő DNS-névterekről. Javasoljuk, hogy adjon meg egy egyedi előtagot a tartománynévhez. Ha például a DNS-gyökér neve *contoso.com*, hozzon létre egy Azure AD DS felügyelt tartományt a *corp.contoso.com* vagy a *DS.contoso.com*egyéni tartománynevével. A helyszíni AD DS környezettel rendelkező hibrid környezetekben ezek az előtagok már használatban vannak. Használjon egyedi előtagot az Azure AD DShoz.
>
> Használhatja az Azure AD DS felügyelt tartományának legfelső szintű DNS-nevét, de előfordulhat, hogy további DNS-rekordokat kell létrehoznia a környezetében lévő más szolgáltatásokhoz. Ha például olyan webkiszolgálót futtat, amely a gyökér DNS-nevet használja, akkor olyan elnevezési ütközések lehetnek, amelyek további DNS-bejegyzéseket igényelnek.
>
> Ezekben az oktatóanyagokban és útmutatókban a *contoso.com* egyéni tartományát használjuk rövid példaként. Az összes parancsban adja meg a saját tartománynevét, amely tartalmazhat egy egyedi előtagot.
>
> További információ: [válasszon egy elnevezési előtagot a tartományhoz][naming-prefix].

A DNS-név következő korlátozásai is érvényesek:

* **Tartományi előtagra vonatkozó korlátozások:** Nem hozhat létre 15 karakternél hosszabb előtaggal rendelkező felügyelt tartományt. A megadott tartománynév előtagja (például a *contoso* a *contoso.com* tartománynévben) legfeljebb 15 karaktert tartalmazhat.
* **Hálózati név ütközése:** A felügyelt tartomány DNS-tartományneve még nem létezik a virtuális hálózaton. Konkrétan a következő helyzetekben érdemes megkeresni a nevek ütközését:
    * Ha már rendelkezik egy Active Directory tartománnyal ugyanazzal a DNS-tartománynévvel az Azure-beli virtuális hálózaton.
    * Ha a virtuális hálózat, amelyen engedélyezni szeretné a felügyelt tartományt, VPN-kapcsolattal rendelkezik a helyszíni hálózattal. Ebben az esetben győződjön meg arról, hogy nem rendelkezik ugyanazzal a DNS-tartománynévvel rendelkező tartománnyal a helyszíni hálózaton.
    * Ha az Azure-beli virtuális hálózaton már van ilyen nevű Azure Cloud Service-szolgáltatás.

Az Azure AD DS-példány létrehozásához a Azure Portal *alapismeretek* ablakában végezze el a következő mezőket:

1. Adja meg a felügyelt tartományhoz tartozó **DNS-tartománynevet** , figyelembe véve az előző pontokat.
1. Válassza ki azt az Azure- **helyet** , amelyben létre kívánja hozni a felügyelt tartományt. Ha olyan régiót választ, amely támogatja az Availability Zones-t, az Azure AD DS erőforrásai a további redundancia érdekében a zónák között oszlanak meg.

    A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosításához legalább három különálló zónának kell lennie az összes engedélyezett régióban.

    Nem kell konfigurálnia az Azure AD DS a zónák közötti elosztására. Az Azure platform automatikusan kezeli az erőforrások zónájának eloszlását. További információért és a régiók rendelkezésre állásának megtekintéséhez lásd: [Mi a Availability Zones az Azure-ban?][availability-zones]

    ![Azure AD Domain Services-példány alapvető beállításainak konfigurálása](./media/tutorial-create-instance/basics-window.png)

Azure AD DS felügyelt tartomány gyors létrehozásához válassza a **felülvizsgálat + létrehozás** lehetőséget a további alapértelmezett konfigurációs beállítások elfogadásához. A létrehozási lehetőség kiválasztása esetén a következő alapértelmezett beállítások vannak konfigurálva:

* Létrehoz egy *aadds-vnet* nevű virtuális hálózatot, amely a *10.0.1.0/24*IP-címtartományt használja.
* Létrehoz egy *aadds-alhálózat* nevű alhálózatot a *10.0.1.0/24*IP-címtartomány használatával.
* Szinkronizálja az Azure AD *összes* felhasználóját az Azure AD DS felügyelt tartományba.

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget az alapértelmezett konfigurációs beállítások elfogadásához.

## <a name="deploy-the-managed-domain"></a>A felügyelt tartomány üzembe helyezése

A varázsló **Összefoglalás** lapján tekintse át a felügyelt tartomány konfigurációs beállításait. A varázsló bármelyik lépésére visszatérhet a módosítások elvégzéséhez. Egy Azure AD DS felügyelt tartomány egy másik Azure AD-bérlőre való **újbóli**üzembe helyezéséhez, ha ezeket a konfigurációs beállításokat használja, le is töltheti az Automation-sablont.

1. A felügyelt tartomány létrehozásához válassza a **Létrehozás**lehetőséget. Megjelenik egy megjegyzés, amely szerint bizonyos konfigurációs beállítások, például a DNS-név vagy a virtuális hálózat nem módosítható, ha az Azure-AD DS felügyelve lett létrehozva. A folytatáshoz kattintson **az OK gombra**.
1. A felügyelt tartomány kiépítés folyamata akár egy órát is igénybe vehet. Egy értesítés jelenik meg a portálon, amely az Azure AD DS üzemelő példányának állapotát mutatja. Válassza ki az értesítést, és tekintse meg az üzembe helyezés részletes folyamatát.

    ![Értesítés a telepítés Azure Portal folyamatban](./media/tutorial-create-instance/deployment-in-progress.png)

1. A lap a telepítési folyamat frissítéseivel töltődik be, beleértve a címtárban lévő új erőforrások létrehozását is.
1. Válassza ki az erőforráscsoportot, például *myResourceGroup*, majd válassza ki az Azure AD DS példányát az Azure-erőforrások listájából, például *contoso.com*. Az **Áttekintés** lapon látható, hogy a felügyelt tartomány jelenleg *telepítve*van. A felügyelt tartományt nem lehet a teljes kiépítés előtt konfigurálni.

    ![Tartományi szolgáltatások állapota a kiépítési állapotban](./media/tutorial-create-instance/provisioning-in-progress.png)

1. Ha a felügyelt tartomány teljes mértékben kiépítve van, az **Áttekintés** lapon a tartomány állapota *fut*értékre látható.

    ![Tartományi szolgáltatások állapota sikeres kiépítés után](./media/tutorial-create-instance/successfully-provisioned.png)

A felügyelt tartomány társítva van az Azure AD-bérlőhöz. A kiépítési folyamat során az Azure AD DS két, *tartományvezérlői szolgáltatásokat* és *AzureActiveDirectoryDomainControllerServices* nevű vállalati alkalmazást hoz létre az Azure ad-bérlőben. Ezek a vállalati alkalmazások a felügyelt tartomány kiszolgálásához szükségesek. Ne törölje ezeket az alkalmazásokat.

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

A jelszó-kivonatok létrehozásához és tárolásához szükséges lépések eltérnek az Azure AD-ben létrehozott kizárólag felhőalapú felhasználói fiókok és a helyszíni címtárból a Azure AD Connect használatával szinkronizált felhasználói fiókok esetében. A csak felhőalapú felhasználói fiókok olyan fiókok, amelyek az Azure AD-címtárban lettek létrehozva az Azure Portal vagy Azure AD PowerShell-parancsmagok használatával. Ezek a felhasználói fiókok nem szinkronizálhatók a helyszíni címtárból. Ebben az oktatóanyagban egy egyszerű, csak felhőalapú felhasználói fiókkal dolgozunk. További információ a Azure AD Connect használatához szükséges további lépésekről: [jelszó-kivonatolások szinkronizálása a helyszíni ad-ből a felügyelt tartományba szinkronizált felhasználói fiókokhoz][on-prem-sync].

> [!TIP]
> Ha az Azure AD-bérlő csak felhőalapú felhasználókat és felhasználókat tartalmaz a helyszíni AD-ből, mindkét lépést végre kell hajtania.

A csak felhőalapú felhasználói fiókok esetében a felhasználóknak meg kell változtatniuk a jelszavukat, mielőtt használniuk tudják az Azure AD DS-t. Ez a jelszó-módosítási folyamat okozza a Kerberos és az NTLM hitelesítés jelszavas kivonatait az Azure AD-ben való létrehozásához és tárolásához. A bérlő minden olyan felhasználójának jelszavait lemondhatja, akinek az Azure AD DSt kell használnia, amely a következő bejelentkezéskor a jelszó megváltoztatását kényszeríti, vagy arra utasítja őket, hogy manuálisan módosítsa a jelszavukat. Ebben az oktatóanyagban manuálisan módosítunk egy felhasználói jelszót.

Mielőtt egy felhasználó visszaállíthatja a jelszavát, az Azure AD-bérlőt [konfigurálni kell az önkiszolgáló jelszó-visszaállításhoz][configure-sspr].

A csak felhőalapú felhasználók jelszavának módosításához a felhasználónak a következő lépéseket kell elvégeznie:

1. Nyissa meg az Azure AD hozzáférési panel lapját [https://myapps.microsoft.com](https://myapps.microsoft.com)címen.
1. A jobb felső sarokban válassza ki a nevét, majd válassza a **profil** lehetőséget a legördülő menüből.

    ![Profil kiválasztása](./media/tutorial-create-instance/select-profile.png)

1. A **profil** lapon válassza a **jelszó módosítása**lehetőséget.
1. A **jelszó módosítása** lapon adja meg a meglévő (régi) jelszavát, majd írja be és erősítse meg az új jelszót.
1. Válassza a **Küldés**lehetőséget.

Néhány percet vesz igénybe, miután módosította az új jelszóhoz tartozó jelszót, hogy az Azure AD DS használható legyen, és hogy sikeresen bejelentkezzen a felügyelt tartományhoz csatlakoztatott számítógépekre.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A felügyelt tartomány DNS-követelményeinek megismerése
> * Azure AD DS-példány létrehozása
> * Rendszergazda felhasználók hozzáadása a tartományi felügyelethez
> * Felhasználói fiókok engedélyezése az Azure AD DS számára és jelszó-kivonatok előállítása

A tartományhoz csatlakozó virtuális gépek és az Azure AD DS felügyelt tartományt használó alkalmazások központi telepítése előtt állítson be egy Azure-beli virtuális hálózatot az alkalmazások számítási feladataihoz.

> [!div class="nextstepaction"]
> [Azure-beli virtuális hálózat konfigurálása alkalmazás-munkaterheléshez a felügyelt tartomány használatához](tutorial-configure-networking.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[availability-zones]: ../availability-zones/az-overview.md

<!-- EXTERNAL LINKS -->
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain#selecting-a-prefix
