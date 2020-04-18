---
title: Oktatóanyag – Azure Active Directory tartományi szolgáltatások példányának létrehozása | Microsoft dokumentumok
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre és konfigurálhat egy Azure Active Directory tartományi szolgáltatások példányát az Azure Portal használatával.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 7652bacdebec19f8a5d55874cfb903e8748cef4d
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639709"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance"></a>Oktatóanyag: Azure Active Directory tartományi szolgáltatások példányának létrehozása és konfigurálása

Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint a tartományhoz való csatlakozás, a csoportházirend, az LDAP, a Kerberos/NTLM-hitelesítés, amelyek teljes mértékben kompatibilisek a Windows Server Active Directoryval. Ezeket a tartományi szolgáltatásokat a tartományvezérlők telepítése, kezelése és javítása nélkül használhatja fel. Az Azure AD DS integrálható a meglévő Azure AD-bérlővel. Ez az integráció lehetővé teszi a felhasználók számára, hogy a vállalati hitelesítő adataikkal jelentkezzenek be, és a meglévő csoportok és felhasználói fiókok segítségével biztosíthatja az erőforrásokhoz való hozzáférést.

Felügyelt tartományt a hálózatkezelés és a szinkronizálás alapértelmezett beállítási beállításaival hozhat létre, vagy [manuálisan is megadhatja ezeket a beállításokat.][tutorial-create-instance-advanced] Ez az oktatóanyag bemutatja, hogyan használhatja az alapértelmezett beállításokat egy Azure AD DS-példány létrehozásához és konfigurálásához az Azure Portalon.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A felügyelt tartomány DNS-követelményeinek ismertetése
> * Azure AD DS-példány létrehozása
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

## <a name="create-an-instance"></a>Példány létrehozása

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

    A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosítása érdekében minden engedélyezett régióban legalább három elkülönített zóna található.

    Nincs mit beállítani az Azure AD DS zónák között elosztott. Az Azure platform automatikusan kezeli az erőforrások zónaelosztását. További információért és a régió elérhetőségéről a [Mik azok a rendelkezésre állási zónák az Azure-ban?][availability-zones]

1. A **termékváltozat** határozza meg a létrehozható erdőszintű bizalmi kapcsolatok teljesítményét, biztonsági mentési gyakoriságát és maximális számát. A felügyelt tartomány létrehozása után módosíthatja a termékváltozatot, ha az üzleti igények vagy követelmények megváltoznak. További információ: [Azure AD DS Termékváltozat fogalmak.][concepts-sku]

    Ebben az oktatóanyagban válassza ki a *szabványos* termékváltozatot.
1. Az *erdő* egy logikai konstrukció, amelyet az Active Directory tartományi szolgáltatások egy vagy több tartomány csoportosítására használnak. Alapértelmezés szerint egy Azure AD DS felügyelt tartomány *jön létre,* mint egy felhasználói erdő. Ez az erdőtípus szinkronizálja az Azure AD összes objektumát, beleértve a helyszíni Activeád-ds környezetben létrehozott felhasználói fiókokat is. Az *erőforráserdő* csak az Azure AD-ben közvetlenül létrehozott felhasználókat és csoportokat szinkronizálja. Az erőforráserdők jelenleg előzetes verzióban jelennek meg. Az *erőforráserdőkkel* kapcsolatos további információkért, például arról, hogy miért használhat egyet, és hogyan hozhat létre erdőszintű bizalmi kapcsolatokat a helyszíni AD DS-tartományokkal, olvassa el az [Azure AD DS-erőforráserdők áttekintését.][resource-forests]

    Ebben az oktatóanyagban válassza a *Felhasználói* erdő létrehozását.

    ![Azure AD tartományi szolgáltatások példányának alapbeállításainak konfigurálása](./media/tutorial-create-instance/basics-window.png)

Az Azure AD DS felügyelt tartomány gyors létrehozásához válassza **a Véleményezés + létrehozás** lehetőséget további alapértelmezett konfigurációs beállítások elfogadásához. A létrehozási beállítás kiválasztásakor a következő alapértelmezések vannak konfigurálva:

* Létrehoz egy *aadds-vnet* nevű virtuális hálózatot, amely a *10.0.2.0/24*IP-címtartományt használja.
* Létrehoz egy *aadds-alhálózat* nevű alhálózatot a *10.0.2.0/24*IP-címtartomány használatával.
* Szinkronizálja *az Azure* AD összes felhasználóját az Azure AD DS felügyelt tartományába.

Válassza **a Véleményezés + létrehozás** lehetőséget az alapértelmezett konfigurációs beállítások elfogadásához.

## <a name="deploy-the-managed-domain"></a>A felügyelt tartomány telepítése

A varázsló **Összegzés** lapján tekintse át a felügyelt tartomány konfigurációs beállításait. A módosításokhoz visszatérhet a varázsló bármely lépéséhez. Ha egy Azure AD DS-felügyelt tartományt egységes módon szeretne újratelepíteni egy másik Azure AD-bérlőre, ezeket a konfigurációs beállításokat használva, akkor egy sablont is **letölthet az automatizáláshoz.**

1. A felügyelt tartomány létrehozásához válassza a **Létrehozás gombot.** Egy megjegyzés jelenik meg, hogy bizonyos konfigurációs beállítások, például a DNS-név vagy a virtuális hálózat nem módosítható, ha az Azure AD DS felügyelt felügyelt lett létrehozva. A folytatáshoz kattintson az **OK gombra.**
1. A felügyelt tartomány kiépítésének folyamata akár egy órát is igénybe vehet. Egy értesítés jelenik meg a portálon, amely az Azure AD DS üzembe helyezésének előrehaladását jeleníti meg. Válassza ki az értesítést a központi telepítés részletes előrehaladásának megtekintéséhez.

    ![Értesítés az Azure Portalon a folyamatban lévő üzembe helyezésről](./media/tutorial-create-instance/deployment-in-progress.png)

1. A lap betöltődik a telepítési folyamat frissítéseivel, beleértve az új erőforrások létrehozását a címtárban.
1. Válassza ki az erőforráscsoportot, például *a myResourceGroup,* majd válassza ki az Azure AD DS-példányt az Azure-erőforrások listájából, például *aaddscontoso.com.* Az **Áttekintés** lapon látható, hogy a felügyelt tartomány *telepítése*folyamatban van. A felügyelt tartomány nem konfigurálható, amíg teljesen ki van építve.

    ![Tartományi szolgáltatások állapota a létesítési állapot alatt](./media/tutorial-create-instance/provisioning-in-progress.png)

1. Ha a felügyelt tartomány teljesen ki van építve, az **Áttekintés** lapon futó állapot jelenik *meg.*

    ![A tartományi szolgáltatások állapota a sikeres kiépítés után](./media/tutorial-create-instance/successfully-provisioned.png)

A felügyelt tartomány az Azure AD-bérlőhöz van társítva. A kiépítési folyamat során az Azure AD DS két nagyvállalati alkalmazást hoz létre, a *tartományvezérlő-szolgáltatásokat* és az *AzureActiveDirectoryDomainControllerServices-t* az Azure AD-bérlőben. Ezek a vállalati alkalmazások szükségesek a felügyelt tartomány kiszolgálásához. Ne törölje ezeket az alkalmazásokat.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Az Azure virtuális hálózat DNS-beállításainak frissítése

Az Azure AD DS sikeres üzembe helyezésével most konfigurálja a virtuális hálózatot úgy, hogy más csatlakoztatott virtuális gépek és alkalmazások használhassák a felügyelt tartományt. A kapcsolat biztosításához frissítse a virtuális hálózat DNS-kiszolgálójának beállításait úgy, hogy az az Ip-címek, ahol az Azure AD DS telepítve van.

1. A felügyelt tartomány **Áttekintés** lapja néhány **szükséges konfigurációs lépést**jelenít meg. Az első konfigurációs lépés a virtuális hálózat DNS-kiszolgálói beállításainak frissítése. A DNS-beállítások megfelelő konfigurálása után ez a lépés már nem jelenik meg.

    A felsorolt címek a virtuális hálózatban használható tartományvezérlők. Ebben a példában ezek a címek *10.0.2.4* és *10.0.2.5*. Ezeket az IP-címeket később a **Tulajdonságok** lapon találhatja meg.

    ![A virtuális hálózat DNS-beállításainak konfigurálása az Azure AD tartományi szolgáltatások IP-címeivel](./media/tutorial-create-instance/configure-dns.png)

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

Csak felhőalapú felhasználói fiókok esetén a felhasználóknak módosítaniuk kell a jelszavukat, mielőtt használhatnák az Azure AD DS-t. Ez a jelszómódosítási folyamat a Kerberos- és NTLM-hitelesítés jelszókivonatait hozza létre és tárolja az Azure AD-ben. A fiók nincs szinkronizálva az Azure AD-ről az Azure AD DS-re, amíg a jelszó nem változik. Vagy lejár a jelszavakat az összes felhőbeli felhasználók a bérlőben, akiknek szükségük van az Azure AD DS, amely kényszeríti a jelszó módosítása a következő bejelentkezés, vagy utasítsa a felhőbeli felhasználók manuálisan módosíthatja a jelszavakat. Ebben az oktatóanyagban változtassuk meg manuálisan a felhasználói jelszót.

Ahhoz, hogy a felhasználó alaphelyzetbe állíthassa a jelszavát, az Azure [AD-bérlőt konfigurálni kell az önkiszolgáló jelszó-visszaállításhoz.][configure-sspr]

A csak felhőalapú felhasználók jelszavának módosításához a felhasználónak a következő lépéseket kell végrehajtania:

1. Nyissa meg az Azure AD [https://myapps.microsoft.com](https://myapps.microsoft.com)access panel oldalát a ban.
1. A jobb felső sarokban jelölje ki a nevét, majd válassza a **Profil** lehetőséget a legördülő menüből.

    ![Profil kiválasztása](./media/tutorial-create-instance/select-profile.png)

1. A **Profil** lapon válassza a **Jelszó módosítása**lehetőséget.
1. A **Jelszó módosítása** lapon adja meg meglévő (régi) jelszavát, majd adjon meg egy új jelszót, és erősítse meg azt.
1. Válassza a **Küldés** lehetőséget.

Néhány percet vesz igénybe, miután megváltoztatta a jelszavát, hogy az új jelszó használható legyen az Azure AD DS-ben, és sikeresen jelentkezzen be a felügyelt tartományhoz csatlakozott számítógépekre.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A felügyelt tartomány DNS-követelményeinek ismertetése
> * Azure AD DS-példány létrehozása
> * Rendszergazdai felhasználók hozzáadása a tartománykezeléshez
> * Az Azure AD DS felhasználói fiókjainak engedélyezése és jelszókiírások létrehozása

Mielőtt tartományhoz csatlakozik a virtuális gépekhez, és telepíti az Okat, amelyek az Azure AD DS felügyelt tartományt használó alkalmazásokat telepít, konfiguráljon egy Azure virtuális hálózatot az alkalmazásszámítási feladatokhoz.

> [!div class="nextstepaction"]
> [Az Azure virtuális hálózat konfigurálása az alkalmazás-munkaterhelések számára a felügyelt tartomány használatához](tutorial-configure-networking.md)

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
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[skus]: overview.md
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain#selecting-a-prefix
