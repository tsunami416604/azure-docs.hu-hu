---
title: Oktatóanyag – Azure Active Directory tartományi szolgáltatások példányának létrehozása | Microsoft dokumentumok
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre és konfigurálhat egy Azure Active Directory tartományi szolgáltatások példányát, és hogyan adhat meg speciális konfigurációs beállításokat az Azure Portal használatával.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: f6817c1ec308e75a4af88825d46848b504775e19
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239146"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance-with-advanced-configuration-options"></a>Oktatóanyag: Azure Active Directory tartományi szolgáltatások példányának létrehozása és konfigurálása speciális konfigurációs beállításokkal

Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint a tartományhoz való csatlakozás, a csoportházirend, az LDAP, a Kerberos/NTLM-hitelesítés, amelyek teljes mértékben kompatibilisek a Windows Server Active Directoryval. Ezeket a tartományi szolgáltatásokat a tartományvezérlők telepítése, kezelése és javítása nélkül használhatja fel. Az Azure AD DS integrálható a meglévő Azure AD-bérlővel. Ez az integráció lehetővé teszi a felhasználók számára, hogy a vállalati hitelesítő adataikkal jelentkezzenek be, és a meglévő csoportok és felhasználói fiókok segítségével biztosíthatja az erőforrásokhoz való hozzáférést.

Felügyelt tartományt létrehozhat a hálózatkezelés és a szinkronizálás [alapértelmezett beállítási beállításaival,][tutorial-create-instance] vagy manuálisan is megadhatja ezeket a beállításokat. Ez az oktatóanyag bemutatja, hogyan határozhatja meg azokat a speciális konfigurációs beállításokat egy Azure AD DS-példány létrehozásához és konfigurálásához az Azure Portalon.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Felügyelt tartomány DNS- és virtuálishálózati beállításainak konfigurálása
> * Azure AD DS-példány létrehozása
> * Rendszergazdai felhasználók hozzáadása a tartománykezeléshez
> * Jelszókivonat szinkronizálásának engedélyezése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az Azure AD DS engedélyezéséhez *globális rendszergazdai* jogosultságokra van szüksége az Azure AD-bérlőben.
* A szükséges Azure AD DS-erőforrások létrehozásához *közreműködői* jogosultságokra van szüksége az Azure-előfizetésben.

Bár nem szükséges az Azure AD DS, ajánlott [konfigurálni önkiszolgáló jelszó-visszaállítás (SSPR)][configure-sspr] az Azure AD-bérlő. A felhasználók sspr nélkül is módosíthatják jelszavukat, de az SSPR segít, ha elfelejtik a jelszavukat, és vissza kell állítaniuk.

> [!IMPORTANT]
> Miután létrehozott egy Azure AD DS felügyelt tartományt, nem helyezheti át a példányt egy másik erőforráscsoportba, virtuális hálózatba, előfizetésbe stb. Az Azure AD DS-példány üzembe helyezésekor ügyeljen a legmegfelelőbb előfizetés, erőforráscsoport, régió és virtuális hálózat kiválasztására.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ebben az oktatóanyagban hozza létre és konfigurálja az Azure AD DS-példányt az Azure Portalhasználatával. Első lépésekhez először jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="create-an-instance-and-configure-basic-settings"></a>Példány létrehozása és alapbeállítások konfigurálása

Az **Azure AD tartományi szolgáltatások engedélyezése** varázsló elindításához hajtsa végre az alábbi lépéseket:

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.
1. Írja be a *tartományszolgáltatásokat* a keresősávba, majd válassza az *Azure AD tartományi szolgáltatások* lehetőséget a keresési javaslatok közül.
1. Az Azure AD tartományi szolgáltatások lapon válassza a **Létrehozás lehetőséget.** Elindul az **Azure AD tartományi szolgáltatások engedélyezése** varázsló.
1. Válassza ki azt az **Azure-előfizetést,** amelyben létre szeretné hozni a felügyelt tartományt.
1. Válassza ki azt az **erőforráscsoportot,** amelyhez a felügyelt tartománynak tartoznia. Válassza az **Új létrehozása vagy** egy meglévő erőforráscsoport kiválasztását.

Amikor létrehoz egy Azure AD DS-példányt, meg kell adnia egy DNS-nevet. A DNS-név kiválasztásakor néhány szempontot figyelembe kell venni:

* **Beépített tartománynév:** Alapértelmezés szerint a rendszer a könyvtár beépített tartománynevét használja (egy *.onmicrosoft.com* utótagot). Ha biztonságos LDAP-hozzáférést szeretne engedélyezni a felügyelt tartományhoz az interneten keresztül, nem hozhat létre digitális tanúsítványt az alapértelmezett tartománnyal való kapcsolat védelmére. A Microsoft rendelkezik az *.onmicrosoft.com* tartománnyal, így a hitelesítésszolgáltató (CA) nem állít ki tanúsítványt.
* **Egyéni tartománynevek:** A leggyakoribb módszer egy egyéni tartománynév megadása, amely általában már a tulajdonában van, és irányítható. Ha egy irányítható, egyéni tartományt használ, a forgalom megfelelően tud folyni az alkalmazások támogatásához.
* **Nem irányítható tartományutótagok:** Általában azt javasoljuk, hogy kerülje a nem irányítható tartománynév-utótagot, például *a contoso.local*nevet. A *.local* utótag nem irányítható, és problémákat okozhat a DNS-feloldással kapcsolatban.

> [!TIP]
> Ha egyéni tartománynevet hoz létre, a meglévő DNS-névtereket is gondot kell fordítania. Javasoljuk, hogy a meglévő Azure-tól vagy helyszíni DNS-névterülettől elkülönülő tartománynevet használjon.
>
> Ha például rendelkezik egy meglévő DNS-névtérrel *contoso.com,* hozzon létre egy Azure AD DS által felügyelt tartományt *a aaddscontoso.com*egyéni tartománynevével. Ha biztonságos LDAP-t kell használnia, regisztrálnia kell és birtokolnia kell ezt az egyéni tartománynevet a szükséges tanúsítványok létrehozásához.
>
> Előfordulhat, hogy további DNS-rekordokat kell létrehoznia a környezet más szolgáltatásaihoz, vagy feltételes DNS-továbbítókat a környezetben meglévő DNS-névterek között. Ha például olyan webkiszolgálót futtat, amely a gyökér DNS-nevet használó webhelyet üzemeltet, előfordulhatnak olyan elnevezési ütközések, amelyek további DNS-bejegyzéseket igényelnek.
>
> Ezekben az oktatóanyagokban és útmutatócikkekben *a aaddscontoso.com* egyéni tartománya rövid példaként szolgál. Minden parancsban adja meg a saját tartománynevét.

A következő DNS-névkorlátozások is érvényesek:

* **Tartományi előtagkorlátozások:** 15 karakternél hosszabb előtaggal nem hozhat létre felügyelt tartományt. A megadott tartománynév előtagnak (például a *aaddscontoso.com* tartománynévben az *aaddscontoso)* 15 vagy kevesebb karaktert kell tartalmaznia.
* **Hálózati névütközések:** A felügyelt tartomány DNS-tartományneve nem létezhet már a virtuális hálózatban. Pontosabban ellenőrizze a következő eseteket, amelyek névütközéshez vezetnek:
    * Ha már rendelkezik ugyanazzal a DNS-tartománynévvel rendelkező Active Directory-tartománnyal az Azure virtuális hálózatán.
    * Ha a felügyelt tartományt engedélyező virtuális hálózat VPN-kapcsolattal rendelkezik a helyszíni hálózattal. Ebben az esetben győződjön meg arról, hogy nincs olyan tartománya, amelynek a helyszíni hálózatán ugyanaz a DNS-tartománynév van.
    * Ha rendelkezik egy ezzel a névvel rendelkező azure-felhőszolgáltatással az Azure virtuális hálózatán.

Töltse ki a mezőket az Azure *Portal Alapjai* ablakában egy Azure AD DS-példány létrehozásához:

1. Adja meg a felügyelt tartomány **DNS-tartománynevét,** figyelembe véve az előző pontokat.
1. Válassza ki azt az **Azure-helyet,** amelyben a felügyelt tartományt létre kell hozni. Ha olyan régiót választ, amely támogatja a rendelkezésre állási zónákat, az Azure AD DS-erőforrások zónák között vannak elosztva a további redundancia érdekében.

    A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosítása érdekében legalább három különálló zóna van az összes engedélyezett régióban.

    Nincs mit beállítani az Azure AD DS zónák között elosztott. Az Azure platform automatikusan kezeli az erőforrások zónaelosztását. További információért és a régió elérhetőségéről a [Mik azok a rendelkezésre állási zónák az Azure-ban?][availability-zones]

1. A **termékváltozat** határozza meg a létrehozható erdőszintű bizalmi kapcsolatok teljesítményét, biztonsági mentési gyakoriságát és maximális számát. A felügyelt tartomány létrehozása után módosíthatja a termékváltozatot, ha az üzleti igények vagy követelmények megváltoznak. További információ: [Azure AD DS Termékváltozat fogalmak.][concepts-sku]

    Ebben az oktatóanyagban válassza ki a *szabványos* termékváltozatot.
1. Az *erdő* egy logikai konstrukció, amelyet az Active Directory tartományi szolgáltatások egy vagy több tartomány csoportosítására használnak. Alapértelmezés szerint egy Azure AD DS felügyelt tartomány *jön létre,* mint egy felhasználói erdő. Ez az erdőtípus szinkronizálja az Azure AD összes objektumát, beleértve a helyszíni Activeád-ds környezetben létrehozott felhasználói fiókokat is. Az *erőforráserdő* csak az Azure AD-ben közvetlenül létrehozott felhasználókat és csoportokat szinkronizálja. Az erőforráserdők jelenleg előzetes verzióban jelennek meg. Az *erőforráserdőkkel* kapcsolatos további információkért, például arról, hogy miért használhat egyet, és hogyan hozhat létre erdőszintű bizalmi kapcsolatokat a helyszíni AD DS-tartományokkal, olvassa el az [Azure AD DS-erőforráserdők áttekintését.][resource-forests]

    Ebben az oktatóanyagban válassza a *Felhasználói* erdő létrehozását.

    ![Azure AD tartományi szolgáltatások példányának alapbeállításainak konfigurálása](./media/tutorial-create-instance-advanced/basics-window.png)

1. További beállítások manuális konfigurálásához válassza a **Tovább - Hálózat lehetőséget.** Ellenkező esetben válassza a **Véleményezés + létrehozás** lehetőséget az alapértelmezett konfigurációs beállítások elfogadásához, majd ugorjon a [felügyelt tartomány központi telepítéséhez](#deploy-the-managed-domain)című szakaszra. A létrehozási beállítás kiválasztásakor a következő alapértelmezések vannak konfigurálva:

    * Létrehoz egy *aadds-vnet* nevű virtuális hálózatot, amely a *10.0.1.0/24*IP-címtartományt használja.
    * Létrehoz egy *aadds-alhálózat* nevű alhálózatot a *10.0.1.0/24*IP-címtartomány használatával.
    * Szinkronizálja *az Azure* AD összes felhasználóját az Azure AD DS felügyelt tartományába.

## <a name="create-and-configure-the-virtual-network"></a>A virtuális hálózat létrehozása és konfigurálása

A kapcsolat biztosításához egy Azure virtuális hálózatra és egy dedikált alhálózatra van szükség. Az Azure AD DS engedélyezve van ebben a virtuális hálózati alhálózatban. Ebben az oktatóanyagban virtuális hálózatot hoz létre, bár ehelyett egy meglévő virtuális hálózat ot használhat. Mindkét megközelítésben létre kell hoznia egy dedikált alhálózatot az Azure AD DS számára.

A dedikált virtuális hálózati alhálózat néhány szempontja a következő területek:

* Az alhálózatnak legalább 3-5 elérhető IP-címmel kell rendelkeznie a címtartományában az Azure AD DS-erőforrások támogatásához.
* Ne válassza ki az *átjáró* alhálózatot az Azure AD DS üzembe helyezéséhez. Az Azure AD DS-t nem támogatja az átjáró-alhálózatban való üzembe helyezése. *Gateway*
* Ne telepítsen más virtuális gépeket az alhálózatra. Az alkalmazások és a virtuális gépek gyakran használnak hálózati biztonsági csoportokat a kapcsolat biztosítására. Ha ezeket a számítási feladatokat külön alhálózaton futtatja, akkor ezeket a hálózati biztonsági csoportokat a felügyelt tartománnyal való kapcsolat megszakítása nélkül alkalmazhatja.
* Az Azure AD DS engedélyezése után nem helyezheti át a felügyelt tartományt egy másik virtuális hálózatba.

A virtuális hálózat tervezéséről és konfigurálásáról az [Azure Active Directory tartományi szolgáltatások hálózati szempontjai][network-considerations]című témakörben talál további információt.

Töltse ki a *Hálózati* ablak mezőit az alábbiak szerint:

1. A **Hálózat** lapon válasszon egy virtuális hálózatot az Azure AD DS üzembe helyezéséhez a legördülő menüből, vagy válassza az **Új létrehozása lehetőséget.**
    1. Ha úgy dönt, hogy virtuális hálózatot hoz létre, adja meg a virtuális hálózat nevét, például a *myVnet*nevet, majd adjon meg egy címtartományt, például *10.0.1.0/24*.
    1. Hozzon létre egy dedikált alhálózatot, amelynek tiszta neve van, például *DomainServices*. Adjon meg egy címtartományt, például *10.0.1.0/24*.

    [![](./media/tutorial-create-instance-advanced/create-vnet.png "Create a virtual network and subnet for use with Azure AD Domain Services")](./media/tutorial-create-instance-advanced/create-vnet-expanded.png#lightbox)

    Ügyeljen arra, hogy olyan címtartományt válasszon, amely a privát IP-címtartományon belül van. A nyilvános címtérben található, nem ön tulajdonában lévő IP-címtartományok hibákat okoznak az Azure AD DS-en belül.

1. Válasszon ki egy virtuális hálózati alhálózatot, például *a DomainServices szolgáltatást.*
1. Ha készen áll, válassza a **Tovább - Adminisztráció**lehetőséget.

## <a name="configure-an-administrative-group"></a>Felügyeleti csoport konfigurálása

Az Azure AD DS-tartomány felügyeletéhez egy *AAD DC rendszergazdák* nevű speciális felügyeleti csoport használatos. A csoport tagjai rendszergazdai engedélyekkel rendelkeznek a felügyelt tartományhoz tartományhoz csatlakozó virtuális gépeken. A tartományhoz csatlakozó virtuális gépeken ez a csoport hozzáadódik a helyi rendszergazdák csoportjához. A csoport tagjai a Távoli asztal segítségével is csatlakozhatnak a tartományhoz csatlakozó virtuális gépekhez.

Az Azure AD DS használatával felügyelt tartományban nincs *tartományi rendszergazdai* vagy *vállalati rendszergazdai* engedély. Ezeket az engedélyeket a szolgáltatás lefoglalta, és nem teszielérhető elérhetővé a bérlőn belüli felhasználók számára. Ehelyett az *AAD DC rendszergazdák* csoport lehetővé teszi bizonyos kiemelt műveletek elvégzését. Ezek a műveletek közé tartozik a tartományhoz csatlakozó virtuális gépek felügyeleti csoportjához való tartozás és a csoportházirend konfigurálása.

A varázsló automatikusan létrehozza az *AAD DC rendszergazdák* csoportot az Azure AD-címtárban. Ha van egy ilyen nevű meglévő csoport az Azure AD-címtárban, a varázsló ezt a csoportot választja. A központi telepítési folyamat során további felhasználókat is hozzáadhat ehhez az *AAD DC rendszergazdák* csoporthoz. Ezeket a lépéseket később is el lehet végezni.

1. Ha további felhasználókat szeretne hozzáadni ehhez az *AAD tartományvezérlő-rendszergazdák* csoporthoz, válassza **a Csoporttagság kezelése lehetőséget.**

    ![Az AAD tartományvezérlő rendszergazdák csoportjának csoporttagságának konfigurálása](./media/tutorial-create-instance-advanced/admin-group.png)

1. Válassza a **Tagok hozzáadása** gombot, majd keresse meg és válassza ki a felhasználókat az Azure AD-címtárból. Keressen például saját fiókot, és adja hozzá az *AAD DC Rendszergazdák* csoportjához.
1. Ha szükséges, módosítsa vagy adjon hozzá további címzetteket az értesítésekhez, ha az Azure AD DS felügyelt tartományban figyelemfelkeltő riasztások vannak.
1. Ha készen áll, válassza a **Tovább - Szinkronizálás lehetőséget.**

## <a name="configure-synchronization"></a>A szinkronizálás konfigurálása

Az Azure AD DS lehetővé teszi az Azure AD-ben elérhető *összes* felhasználó és csoport szinkronizálását, vagy csak adott csoportok *hatókörrel való* szinkronizálását. Ha úgy dönt, hogy *szinkronizálja az összes* felhasználót és csoportot, később nem választhatja úgy, hogy csak hatókör-szinkronizálást hajt végre. A hatókörrel szinkronizált szinkronizálásról az [Azure AD tartományszolgáltatások hatóköre szinkronizáláscímű][scoped-sync]témakörben talál további információt.

1. Ebben az oktatóanyagban válassza az **Összes** felhasználó és csoport szinkronizálását. Ez a szinkronizálási beállítás az alapértelmezett beállítás.

    ![Felhasználók és csoportok teljes szinkronizálásának végrehajtása az Azure AD-ből](./media/tutorial-create-instance-advanced/sync-all.png)

1. Válassza az **Áttekintés + létrehozás** lehetőséget.

## <a name="deploy-the-managed-domain"></a>A felügyelt tartomány telepítése

A varázsló **Összegzés** lapján tekintse át a felügyelt tartomány konfigurációs beállításait. A módosításokhoz visszatérhet a varázsló bármely lépéséhez. Ha egy Azure AD DS-felügyelt tartományt egységes módon szeretne újratelepíteni egy másik Azure AD-bérlőre, ezeket a konfigurációs beállításokat használva, akkor egy sablont is **letölthet az automatizáláshoz.**

1. A felügyelt tartomány létrehozásához válassza a **Létrehozás gombot.** Egy megjegyzés jelenik meg, hogy bizonyos konfigurációs beállítások, például a DNS-név vagy a virtuális hálózat nem módosítható, ha az Azure AD DS felügyelt felügyelt lett létrehozva. A folytatáshoz kattintson az **OK gombra.**
1. A felügyelt tartomány kiépítésének folyamata akár egy órát is igénybe vehet. Egy értesítés jelenik meg a portálon, amely az Azure AD DS üzembe helyezésének előrehaladását jeleníti meg. Válassza ki az értesítést a központi telepítés részletes előrehaladásának megtekintéséhez.

    ![Értesítés az Azure Portalon a folyamatban lévő üzembe helyezésről](./media/tutorial-create-instance-advanced/deployment-in-progress.png)

1. Válassza ki az erőforráscsoportot, például *a myResourceGroup,* majd válassza ki az Azure AD DS-példányt az Azure-erőforrások listájából, például *aaddscontoso.com.* Az **Áttekintés** lapon látható, hogy a felügyelt tartomány *telepítése*folyamatban van. A felügyelt tartomány nem konfigurálható, amíg teljesen ki van építve.

    ![Tartományi szolgáltatások állapota a létesítési állapot alatt](./media/tutorial-create-instance-advanced/provisioning-in-progress.png)

1. Ha a felügyelt tartomány teljesen ki van építve, az **Áttekintés** lapon futó állapot jelenik *meg.*

    ![A tartományi szolgáltatások állapota a sikeres kiépítés után](./media/tutorial-create-instance-advanced/successfully-provisioned.png)

A felügyelt tartomány az Azure AD-bérlőhöz van társítva. A kiépítési folyamat során az Azure AD DS két nagyvállalati alkalmazást hoz létre, a *tartományvezérlő-szolgáltatásokat* és az *AzureActiveDirectoryDomainControllerServices-t* az Azure AD-bérlőben. Ezek a vállalati alkalmazások szükségesek a felügyelt tartomány kiszolgálásához. Ne törölje ezeket az alkalmazásokat.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Az Azure virtuális hálózat DNS-beállításainak frissítése

Az Azure AD DS sikeres üzembe helyezésével most konfigurálja a virtuális hálózatot úgy, hogy más csatlakoztatott virtuális gépek és alkalmazások használhassák a felügyelt tartományt. A kapcsolat biztosításához frissítse a virtuális hálózat DNS-kiszolgálójának beállításait úgy, hogy az az Ip-címek, ahol az Azure AD DS telepítve van.

1. A felügyelt tartomány **Áttekintés** lapja néhány **szükséges konfigurációs lépést**jelenít meg. Az első konfigurációs lépés a virtuális hálózat DNS-kiszolgálói beállításainak frissítése. A DNS-beállítások megfelelő konfigurálása után ez a lépés már nem jelenik meg.

    A felsorolt címek a virtuális hálózatban használható tartományvezérlők. Ebben a példában ezek a címek *10.1.0.4* és *10.1.0.5*. Ezeket az IP-címeket később a **Tulajdonságok** lapon találhatja meg.

    ![A virtuális hálózat DNS-beállításainak konfigurálása az Azure AD tartományi szolgáltatások IP-címeivel](./media/tutorial-create-instance-advanced/configure-dns.png)

1. A virtuális hálózat DNS-kiszolgálójának beállításainak frissítéséhez kattintson a **Konfigurálás gombra.** A DNS-beállítások automatikusan konfigurálva vannak a virtuális hálózathoz.

> [!TIP]
> Ha az előző lépésekben egy meglévő virtuális hálózatot választott, a hálózathoz csatlakozó virtuális gépek csak az újraindítás után kapják meg az új DNS-beállításokat. A virtuális gépek az Azure Portalon, az Azure PowerShell ben vagy az Azure CLI használatával újraindítható.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Felhasználói fiókok engedélyezése az Azure AD DS szolgáltatáshoz

A felügyelt tartomány felhasználóinak hitelesítéséhez az Azure AD DS jelszókivonatokat igényel az NT LAN Manager (NTLM) és a Kerberos-hitelesítéshez megfelelő formátumban. Az Azure AD nem hoz létre és tárol jelszókivonatokat az NTLM- vagy Kerberos-hitelesítéshez szükséges formátumban, amíg nem engedélyezi az Azure AD DS-t a bérlő számára. Biztonsági okokból az Azure AD is nem tárolja a jelszó hitelesítő adatokat a tiszta szöveges formában. Ezért az Azure AD nem tudja automatikusan generálni ezeket az NTLM- vagy Kerberos-jelszókivét a felhasználók meglévő hitelesítő adatai alapján.

> [!NOTE]
> A megfelelően konfigurált, a használható jelszó kikeresések az Azure AD DS felügyelt tartományban tárolja. Ha törli az Azure AD DS felügyelt tartományt, az adott ponton tárolt jelszókikeresések is törlődnek. Szinkronizált hitelesítő adatok az Azure AD nem lehet újra használni, ha később hozzon létre egy Azure AD DS felügyelt tartományban – újra kell konfigurálnia a jelszókivonat-szinkronizálás a jelszó kivonatújra tárolni. Korábban a tartományhoz csatlakozó virtuális gépek vagy felhasználók nem lesznek képesek azonnal hitelesíteni – az Azure AD-nek létre kell hoznia és tárolnia kell a jelszókimondottakat az új Azure AD DS felügyelt tartományban. További információ: [Password hash sync process for Azure AD DS and Azure AD Connect][password-hash-sync-process].

A jelszókitagok létrehozásának és tárolásának lépései eltérnek az Azure AD-ben létrehozott csak felhőalapú felhasználói fiókoktól, illetve az Azure AD Connect használatával a helyszíni címtárból szinkronizált felhasználói fiókoktól. A csak felhőalapú felhasználói fiókok olyan fiókok, amelyek az Azure AD-címtárban lettek létrehozva az Azure Portal vagy Azure AD PowerShell-parancsmagok használatával. Ezek a felhasználói fiókok nincsenek szinkronizálva a helyszíni címtárból. Ebben az oktatóanyagban dolgozzunk egy csak alapvető felhőalapú felhasználói fiókkal. Az Azure AD Connect használatához szükséges további lépésekről a [helyszíni AD-ből a felügyelt tartományba szinkronizált felhasználói fiókok jelszókihezéseinek szinkronizálása című][on-prem-sync]témakörben talál további információt.

> [!TIP]
> Ha az Azure AD-bérlő a helyszíni AD-ből származó csak felhőbeli felhasználók és felhasználók kombinációjával rendelkezik, mindkét lépéskészletet el kell végeznie.

Csak felhőalapú felhasználói fiókok esetén a felhasználóknak módosítaniuk kell a jelszavukat, mielőtt használhatnák az Azure AD DS-t. Ez a jelszómódosítási folyamat a Kerberos- és NTLM-hitelesítés jelszókivonatait hozza létre és tárolja az Azure AD-ben. Lejárathatja a jelszavakat a bérlő összes felhasználója számára, akiknek az Azure AD DS-t kell használniuk, amely jelszómódosítást kényszerít a következő bejelentkezéskor, vagy utasíthatja őket, hogy manuálisan változtassák meg a jelszavukat. Ebben az oktatóanyagban változtassuk meg manuálisan a felhasználói jelszót.

Ahhoz, hogy a felhasználó alaphelyzetbe állíthassa a jelszavát, az Azure [AD-bérlőt konfigurálni kell az önkiszolgáló jelszó-visszaállításhoz.][configure-sspr]

A csak felhőalapú felhasználók jelszavának módosításához a felhasználónak a következő lépéseket kell végrehajtania:

1. Nyissa meg az Azure AD [https://myapps.microsoft.com](https://myapps.microsoft.com)access panel oldalát a ban.
1. A jobb felső sarokban jelölje ki a nevét, majd válassza a **Profil** lehetőséget a legördülő menüből.

    ![Profil kiválasztása](./media/tutorial-create-instance-advanced/select-profile.png)

1. A **Profil** lapon válassza a **Jelszó módosítása**lehetőséget.
1. A **Jelszó módosítása** lapon adja meg meglévő (régi) jelszavát, majd adjon meg egy új jelszót, és erősítse meg azt.
1. Válassza a **Küldés** lehetőséget.

Néhány percet vesz igénybe, miután megváltoztatta a jelszavát, hogy az új jelszó használható legyen az Azure AD DS-ben, és sikeresen jelentkezzen be a felügyelt tartományhoz csatlakozott számítógépekre.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Felügyelt tartomány DNS- és virtuálishálózati beállításainak konfigurálása
> * Azure AD DS-példány létrehozása
> * Rendszergazdai felhasználók hozzáadása a tartománykezeléshez
> * Az Azure AD DS felhasználói fiókjainak engedélyezése és jelszókiírások létrehozása

A felügyelt tartomány működés közbeni megtekintéséhez hozzon létre egy virtuális gépet a tartományhoz, és csatlakozzon hozzá.

> [!div class="nextstepaction"]
> [Csatlakozás Windows Server rendszerű virtuális géphez a felügyelt tartományhoz](join-windows-vm.md)

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
