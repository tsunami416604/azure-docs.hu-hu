---
title: Oktatóanyag – Erdőszintű bizalmi kapcsolat létrehozása az Azure AD tartományi szolgáltatásokban | Microsoft dokumentumok
description: Megtudhatja, hogy miként hozhat létre egy egyirányú kimenő erdőt egy helyszíni AD DS-tartományhoz az Azure AD tartományi szolgáltatásokhoz létrehozott Azure AD-s portálon
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: eb96cb32c05d2ba3fbd38e72c16540d947436117
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519078"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services-preview"></a>Oktatóanyag: Kimenő erdőszintű bizalmi kapcsolat létrehozása egy helyszíni tartományba az Azure Active Directory tartományi szolgáltatásokban (előzetes verzió)

Olyan környezetekben, ahol nem szinkronizálhatja a jelszókivét, vagy olyan felhasználókkal rendelkezik, akik kizárólag intelligens kártyákkal jelentkeznek be, így nem ismerik a jelszavukat, használhat egy erőforráserdőt az Azure Active Directory tartományi szolgáltatásokban (AD DS). Az erőforrás-erdő egyirányú kimenő megbízhatósági kapcsolatot használ az Azure AD DS-ből egy vagy több helyszíni AD DS-környezetbe. Ez a megbízhatósági kapcsolat lehetővé teszi a felhasználók, alkalmazások és számítógépek hitelesítését az Azure AD DS felügyelt tartományból származó helyszíni tartományban. Az Azure AD DS erőforrás-erdők jelenleg előzetes verzióban.

![Az Azure AD DS és a helyszíni AD DS közötti erdőszintű bizalmi kapcsolat diagramja](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A DNS konfigurálása helyszíni Active AD DS-környezetben az Azure AD DS-kapcsolat támogatásához
> * Egyirányú bejövő erdőszintű bizalmi kapcsolat létrehozása helyszíni AD DS-környezetben
> * Egyirányú kimenő erdőszintű bizalmi kapcsolat létrehozása az Azure AD DS-ben
> * A hitelesítéshez és az erőforrás-hozzáféréshez szükséges megbízhatósági kapcsolat tesztelése és ellenőrzése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány egy erőforráserdő használatával létrehozott és az Azure AD-bérlőben konfigurált.
    * Szükség esetén [hozzon létre és konfiguráljon egy Azure Active Directory tartományi szolgáltatások példányát.][create-azure-ad-ds-instance-advanced]
    
    > [!IMPORTANT]
    > Győződjön meg arról, hogy hozzon létre egy Azure AD DS felügyelt tartomány t *erőforráserdő* használatával. Az alapértelmezett beállítás *felhasználói* erdőt hoz létre. Csak az erőforrás-erdők hozhatnak létre megbízhatósági kapcsolatokat az ad ds-környezetekben. A felügyelt tartományhoz legalább egy *vállalati* termékváltozatot is használnia kell. Szükség esetén [módosítsa az Azure AD DS felügyelt tartományának termékváltozatát.][howto-change-sku]

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ebben az oktatóanyagban hozza létre és konfigurálja a kimenő erdőszintű bizalmi kapcsolatot az Azure AD DS-ből az Azure Portalhasználatával. Első lépésekhez először jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="networking-considerations"></a>Hálózati megfontolások

Az Azure AD DS-erőforráserdőt üzemeltető virtuális hálózatnak hálózati kapcsolatra van szüksége a helyszíni Active Directoryval. Az alkalmazásoknak és szolgáltatásoknak is hálózati kapcsolatra van szükségük az Azure AD DS erőforráserdőt üzemeltető virtuális hálózathoz. Az Azure AD DS erőforráserdő hálózati kapcsolatának mindig bekapcsolva és stabilnak kell lennie, különben a felhasználók nem hitelesíthetik vagy nem férhetnek hozzá az erőforrásokhoz.

Mielőtt erdőszintű megbízhatósági kapcsolatot konfigurálna az Azure AD DS szolgáltatásban, győződjön meg arról, hogy az Azure és a helyszíni környezet közötti hálózatkezelés megfelel az alábbi követelményeknek:

* Használjon privát IP-címeket. Ne hagyatkozzon dinamikus IP-cím-hozzárendeléssel rendelkező DHCP-re.
* Kerülje az egymást átfedő IP-címterek, hogy a virtuális hálózati társviszony-létesítés és útválasztás sikeresen kommunikálni az Azure és a helyszíni.
* Az Azure virtuális hálózatának átjáróalhálózatra van szüksége az [Azure-helyek közötti (S2S) VPN-][vpn-gateway] vagy [ExpressRoute-kapcsolat][expressroute] konfigurálásához
* Hozzon létre olyan alhálózatokat, amelyek elegendő IP-címmel támogatják a forgatókönyvet.
* Győződjön meg arról, hogy az Azure AD DS saját alhálózattal rendelkezik, ne ossza meg ezt a virtuális hálózati alhálózatot az alkalmazás virtuális gépeivel és szolgáltatásaival.
* A társviszonyba vett virtuális hálózatok nem tranzitívak.
    * Az Azure virtuális hálózati társviszony-létesítések létre kell hozni az összes virtuális hálózatok szeretné használni az Azure AD DS erőforrás-erdő szintű bizalmi kapcsolatot a helyszíni AD DS-környezetben.
* Folyamatos hálózati kapcsolat biztosítása a helyszíni Active Directory-erdőhöz. Ne használjon igény szerinti kapcsolatokat.
* Győződjön meg arról, hogy az Azure AD DS-erőforrás neve és a helyszíni Active Directory erdőneve között folyamatos névfeloldás (DNS) van.

## <a name="configure-dns-in-the-on-premises-domain"></a>A DNS konfigurálása a helyszíni tartományban

Az Azure AD DS felügyelt tartomány ának a helyszíni környezetből történő helyes feloldásához előfordulhat, hogy továbbítókat kell hozzáadnia a meglévő DNS-kiszolgálókhoz. Ha még nem konfigurálta a helyszíni környezetet az Azure AD DS felügyelt tartományával való kommunikációra, hajtsa végre a következő lépéseket a helyszíni ActiveÁd-tartományi szolgáltatások tartományfelügyeleti munkaállomásáról:

1. Válassza **a Start | Adminisztratív eszközök | DNS**
1. Válassza ki a jobb oldali DNS-kiszolgálót, például *a myAD01-et,* válassza a **Tulajdonságok lehetőséget.**
1. Válassza **a Továbbítók**lehetőséget, majd **a Szerkesztés lehetőséget** további továbbítók hozzáadásához.
1. Adja hozzá az Azure AD DS felügyelt tartományÁNAK IP-címét, például *a 10.0.2.4* és *a 10.0.2.5.*

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Bejövő erdőszintű bizalmi kapcsolat létrehozása a helyszíni tartományban

A helyszíni AD DS-tartománynak bejövő erdőszintű megbízhatósági kapcsolatra van szüksége az Azure AD DS felügyelt tartományában. Ezt a megbízhatósági kapcsolatot manuálisan kell létrehozni a helyszíni AD DS-tartományban, nem hozható létre az Azure Portalon.

A helyszíni Active Ád DS-tartomány bejövő bizalmi kapcsolatának konfigurálásához hajtsa végre a helyszíni Active Ád Tartományi szolgáltatások tartomány felügyeleti munkaállomásának következő lépéseit:

1. Válassza **a Start | Adminisztratív eszközök | Active Directory – tartományok és megbízhatósági kapcsolatok**
1. Válassza ki a jobb oldali tartományt, például *onprem.contoso.com,* válassza a **Tulajdonságok lehetőséget.**
1. Válassza **a Megbízhatóság ok a** lapot, majd az Új **megbízhatósági kapcsolatok lapot**
1. Adja meg a nevet az Azure AD DS tartománynevében, például *aaddscontoso.com,* majd válassza a **Tovább gombot.**
1. Válassza ki az **erdőszintű bizalmi kapcsolat**létrehozásának lehetőségét, majd hozzon létre egy **egyirányú: bejövő** bizalmi kapcsolatot.
1. Válassza ki, hogy csak ehhez a **tartományhoz hozza-e**létre a bizalmi kapcsolatot. A következő lépésben hozza létre a megbízhatóságot az Azure AD DS felügyelt tartományban az Azure AD DS felügyelt tartományban.
1. Válassza az **erdőszintű hitelesítés**t, majd adja meg és erősítse meg a megbízhatósági jelszót. Ugyanez a jelszó is megvan adva az Azure Portalon a következő szakaszban.
1. Lépjen végig a következő néhány ablakon az alapértelmezett beállításokkal, majd válassza a **Nem lehetőséget, ne erősítse meg a kimenő bizalmi kapcsolatot**.
1. **Befejezés** kiválasztása

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Kimenő erdőszintű bizalmi kapcsolat létrehozása az Azure AD DS szolgáltatásban

A helyszíni AD DS-tartomány konfigurálva az Azure AD DS felügyelt tartomány és egy bejövő erdő megbízhatósági kapcsolat létre, most létrehozta a kimenő erdő megbízhatósági kapcsolat. Ez a kimenő erdőszintű bizalmi kapcsolat befejezi a helyszíni AD DS-tartomány és az Azure AD DS felügyelt tartomány közötti megbízhatósági kapcsolatot.

Az Azure AD DS által felügyelt tartomány kimenő megbízhatósági kapcsolatának létrehozásához hajtsa végre az alábbi lépéseket:

1. Az Azure Portalon keresse meg és válassza ki az **Azure AD tartományi szolgáltatásokat,** majd válassza ki a felügyelt tartományt, például *aaddscontoso.com*
1. Az Azure AD DS felügyelt tartomány bal oldalán található menüben válassza a **Megbízhatóságok**lehetőséget, majd válassza a + Bizalmi kapcsolat **hozzáadása lehetőséget.**

   > [!NOTE]
   > Ha nem látható a **Megbízhatóságok** menü, jelölje be az Erdő típus **tulajdonságai** *csoportjában.* Csak *az erőforráserdők* hozhatnak létre bizalmi kapcsolatokat. Ha az erdő típusa *Felhasználó,* nem hozhat létre bizalmi kapcsolatokat. Jelenleg nincs mód az Azure AD DS felügyelt tartomány erdőtípusának módosítására. A felügyelt tartományt erőforráserdőként kell törölnie, majd újra létrehoznia.

1. Adjon meg egy megjelenítendő nevet, amely azonosítja a megbízhatóságot, majd a helyszíni megbízható erdő DNS-nevét, például *onprem.contoso.com*
1. Adja meg ugyanazt a megbízhatósági jelszót, amelyet a helyszíni AD DS-tartomány bejövő erdőszintű megbízhatósági kapcsolatának konfigurálásakor használt az előző szakaszban.
1. Legalább két DNS-kiszolgáló biztosítása a helyszíni AD DS-tartományhoz, például *a 10.1.1.4* és *a 10.1.1.5*
1. Ha készen áll, **mentse** a kimenő erdőszintű bizalmi kapcsolatot

    ![Kimenő erdőszintű bizalmi kapcsolat létrehozása az Azure Portalon](./media/tutorial-create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>Erőforrás-hitelesítés ellenőrzése

Az alábbi gyakori esetekben ellenőrizheti, hogy az erdőszintű bizalmi kapcsolat megfelelően hitelesíti-e a felhasználókat és az erőforrásokhoz való hozzáférést:

* [Helyszíni felhasználói hitelesítés az Azure AD DS erőforráserdőből](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Erőforrások elérése az Azure AD DS erőforráserdőben a helyszíni felhasználó használatával](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Fájl- és nyomtatómegosztás engedélyezése](#enable-file-and-printer-sharing)
    * [Biztonsági csoport létrehozása és tagok hozzáadása](#create-a-security-group-and-add-members)
    * [Fájlmegosztás létrehozása erdőközi hozzáféréshez](#create-a-file-share-for-cross-forest-access)
    * [Erdőközi hitelesítés ellenőrzése erőforráson](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Helyszíni felhasználói hitelesítés az Azure AD DS erőforráserdőből

A Windows Server virtuális gép csatlakozott az Azure AD DS erőforrás-tartományhoz. Ezzel a virtuális géppel tesztelheti a helyszíni felhasználó hitelesítést egy virtuális gépen.

1. Csatlakozzon a Windows Server virtuális gép csatlakozott az Azure AD DS erőforráserdő az [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview) és az Azure AD DS rendszergazdai hitelesítő adatait.
1. Nyisson meg egy `whoami` parancssort, és a paranccsal jelenítse meg az aktuálisan hitelesített felhasználó megkülönböztető nevét:

    ```console
    whoami /fqdn
    ```

1. A `runas` parancs segítségével hitelesítheti magát felhasználóként a helyszíni tartományból. A következő parancsban `userUpn@trusteddomain.com` cserélje le a megbízható helyszíni tartományból származó felhasználó upn-jét. A parancs a felhasználó jelszavát kéri:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Ha a hitelesítés sikeres, új parancssor nyílik meg. Az új parancssor címe `running as userUpn@trusteddomain.com`tartalmazza a .
1. Az `whoami /fqdn` új parancssorban a hitelesített felhasználó megkülönböztető nevének megtekintéséhez a helyszíni Active Directoryból.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Erőforrások elérése az Azure AD DS erőforráserdőben a helyszíni felhasználó használatával

Az Azure AD DS erőforráserdőhöz csatlakozott Windows Server virtuális gép használatával tesztelheti azt a forgatókönyvet, amelyben a felhasználók hozzáférhetnek az erőforráserdőben tárolt erőforrásokhoz, amikor a helyszíni tartomány számítógépeiről hitelesítik magukat a helyszíni tartomány felhasználóival. Az alábbi példák bemutatják, hogyan hozhat létre és tesztelheti a különböző gyakori forgatókönyveket.

#### <a name="enable-file-and-printer-sharing"></a>Fájl- és nyomtatómegosztás engedélyezése

1. Csatlakozzon a Windows Server virtuális gép csatlakozott az Azure AD DS erőforráserdő az [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview) és az Azure AD DS rendszergazdai hitelesítő adatait.

1. Nyissa meg **a Windows beállításai t,** majd keresse meg a **Hálózati és megosztási központot.**
1. Válassza a **Speciális megosztási** beállítások módosítása lehetőséget.
1. A **Domain Profil csoportban**jelölje be **a Fájl- és nyomtatómegosztás bekapcsolása,** majd **a Módosítások mentése lehetőséget.**
1. A **Hálózati és megosztási központ bezárása**.

#### <a name="create-a-security-group-and-add-members"></a>Biztonsági csoport létrehozása és tagok hozzáadása

1. Nyissa meg az **Active Directory – felhasználók és számítógépek** beépülő modult.
1. Válassza ki jobbra a tartománynevet, válassza az **Új**lehetőséget, majd a **Szervezeti egység parancsot.**
1. A névmezőbe írja be a *LocalObjects (Névobjektum)* parancsot, majd kattintson **az OK gombra.**
1. A navigációs ablakban jelölje ki a **Helyi objektumok** elemet, és kattintson a jobb gombbal. Válassza az **Új,** majd **a Csoport lehetőséget.**
1. Írja be a *FileServerAccess* nevet a **Csoport név** mezőbe. A **Csoporthatókör csoportban**válassza a **Tartományon belüli**lehetőséget, majd az **OK lehetőséget.**
1. A tartalomablaktáblán kattintson duplán a **FileServerAccess**elemre. Válassza a **Tagok**lehetőséget, válassza a **Hozzáadás**lehetőséget, majd a **Helyek**lehetőséget.
1. Válassza ki a helyszíni Active Directoryt a **Hely** nézetből, majd kattintson az **OK gombra.**
1. Írja be *a Tartományfelhasználók parancsot* a **Kijelölendő objektumok nevének megadása** mezőbe. Válassza **a Nevek ellenőrzése lehetőséget,** adja meg a hitelesítő adatokat a helyszíni Active Directoryszámára, majd kattintson az OK **gombra.**

    > [!NOTE]
    > Meg kell adnia a hitelesítő adatokat, mert a bizalmi kapcsolat csak egy irányú. Ez azt jelenti, hogy az Azure AD DS-ből származó felhasználók nem férhetnek hozzá az erőforrásokhoz, és nem kereshetnek felhasználókat vagy csoportokat a megbízható (helyszíni) tartományban.

1. A helyszíni Active Directory **tartományi felhasználók** csoportjának a **FileServerAccess** csoport tagjának kell lennie. A csoport mentéséhez és az ablak bezárásához válassza az **OK gombot.**

#### <a name="create-a-file-share-for-cross-forest-access"></a>Fájlmegosztás létrehozása erdőközi hozzáféréshez

1. Az Azure AD DS erőforráserdőhöz csatlakozott Windows Server virtuális gépen hozzon létre egy mappát, és adja meg a nevet, például *a CrossForestShare nevet.*
1. Jelölje ki a jobb gombot a mappában, és válassza **a Tulajdonságok parancsot.**
1. Kattintson a **Biztonság** fülre, majd a **Szerkesztés gombra.**
1. A *CrossForestShare engedélyek* párbeszédpanelen válassza a **Hozzáadás lehetőséget.**
1. Írja be *a FileServerAccess* parancsot **a Jelölje be a kijelölendő objektumneveket,** majd kattintson az **OK gombra.**
1. Válassza a *FileServerAccess* elemet a **Csoportok vagy felhasználónevek** listából. A **FileServerAccess engedélyei** listában válassza a **Modify** and **Write** engedélyek *engedélyezése lehetőséget,* majd kattintson az **OK gombra.**
1. Válassza a **Megosztás** lapot, majd a **Speciális megosztás lehetőséget...**
1. Válassza **a Mappa megosztása**lehetőséget, majd adja meg a fájlmegosztás emlékezetes nevét a Megosztás **névben,** például *a CrossForestShare névben.*
1. Válassza **az Engedélyek lehetőséget.** Az **Engedélyek mindenkinek** listában válassza az **Engedélyezés** a **módosítási** engedély engedélyezése lehetőséget.
1. Kattintson az **OK** gombra kétszer, majd zárja be a **gombot.**

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Erdőközi hitelesítés ellenőrzése erőforráson

1. Jelentkezzen be egy Windows-számítógéphez, amely a helyszíni Active Directoryhoz csatlakozott a helyszíni Active Directoryfelhasználói fiókjával.
1. A **Windows Intéző**segítségével csatlakozzon a teljesen minősített állomásnévvel `\\fs1.aaddscontoso.com\CrossforestShare`létrehozott megosztáshoz és a megosztáshoz, például .
1. Az írási engedély érvényesítéséhez válassza ki a jobb gombot a mappában, válassza az **Új**lehetőséget, majd válassza a **Szöveges dokumentum lehetőséget.** Használja az alapértelmezett nevet **Új szövegdokumentum**.

    Ha az írási engedélyek megfelelően vannak beállítva, új szöveges dokumentum jön létre. A következő lépések ekkor megnyitják, szerkesztik és törlik a fájlt.
1. Az olvasási engedély érvényesítéséhez nyissa meg az **Új szöveges dokumentumot**.
1. A módosítási engedély érvényesítéséhez adjon szöveget a fájlhoz, és zárja be a **Jegyzettömböt**. Amikor a program a módosítások mentését kéri, válassza a **Mentés gombot.**
1. A törlési engedély érvényesítéséhez válassza a jobb oldali új **szövegdokumentum** lehetőséget, majd válassza a **Törlés parancsot.** A fájl törlésének megerősítéséhez válassza az **Igen** lehetőséget.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A DNS konfigurálása helyszíni Active AD DS-környezetben az Azure AD DS-kapcsolat támogatásához
> * Egyirányú bejövő erdőszintű bizalmi kapcsolat létrehozása helyszíni AD DS-környezetben
> * Egyirányú kimenő erdőszintű bizalmi kapcsolat létrehozása az Azure AD DS-ben
> * A hitelesítéshez és az erőforrás-hozzáféréshez szükséges megbízhatósági kapcsolat tesztelése és ellenőrzése

Az Azure AD DS erdőtípusaira vonatkozó további információkért lásd: [Mik az erőforráserdők és][concepts-forest] [hogyan működnek az erdőszintű bizalmi kapcsolatok az Azure AD DS-ben?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[expressroute]: ../expressroute/expressroute-introduction.md
