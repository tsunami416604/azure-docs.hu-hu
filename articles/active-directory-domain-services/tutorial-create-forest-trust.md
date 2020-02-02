---
title: Oktatóanyag – erdőszintű megbízhatósági kapcsolat létrehozása a Azure AD Domain Servicesban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egyirányú kimenő erdőt helyszíni AD DS tartományba a Azure Portalban Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: 3637a11724c1f0bab049077c5abbd817e168bd44
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76931232"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services-preview"></a>Oktatóanyag: kimenő erdőszintű megbízhatósági kapcsolat létrehozása helyi tartományhoz Azure Active Directory Domain Services (előzetes verzió)

Olyan környezetekben, ahol nem lehet szinkronizálni a jelszavakat, vagy ha olyan felhasználókkal rendelkezik, akik kizárólag intelligens kártyákkal jelentkeznek be, ezért nem ismerik a jelszavukat, használhat Azure Active Directory Domain Services (AD DS) erőforrás-erdőt. Az erőforrás-erdő egyirányú kimenő bizalmi kapcsolatot használ az Azure AD DS egy vagy több helyszíni AD DS környezetbe. Ez a megbízhatósági kapcsolat lehetővé teszi a felhasználók, az alkalmazások és a számítógépek számára a helyszíni tartományon belüli hitelesítést az Azure AD DS felügyelt tartományból. Az Azure AD DS erőforrás-erdők jelenleg előzetes verzióban érhetők el.

![Az Azure AD DS és a helyszíni AD DS közötti erdőszintű megbízhatóság diagramja](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A DNS konfigurálása helyszíni AD DS környezetben az Azure AD DS-kapcsolat támogatásához
> * Egyirányú bejövő erdőszintű megbízhatósági kapcsolat létrehozása helyszíni AD DS környezetben
> * Egyirányú kimenő erdőszintű megbízhatósági kapcsolat létrehozása az Azure-ban AD DS
> * A hitelesítés és az erőforrás-hozzáférés megbízhatósági kapcsolatának tesztelése és ellenőrzése

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány, amely egy erőforrás-erdő használatával lett létrehozva, és az Azure AD-bérlőben van konfigurálva.
    * Szükség esetén [hozzon létre és konfiguráljon egy Azure Active Directory Domain Services példányt][create-azure-ad-ds-instance-advanced].
    
    > [!IMPORTANT]
    > Győződjön meg arról, hogy Azure AD DS felügyelt tartományt hoz létre egy *erőforrás* -erdő használatával. Az alapértelmezett beállítás egy *felhasználói* erdőt hoz létre. Csak az erőforrás-erdők hozhatnak létre megbízhatósági kapcsolatot a helyszíni AD DS környezetekben. Emellett legalább a *vállalati* SKU-t kell használnia a felügyelt tartományhoz. Ha szükséges, [módosítsa az SKU-t egy Azure AD DS felügyelt tartományhoz][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ebben az oktatóanyagban a Azure Portal használatával hozza létre és konfigurálja a kimenő erdőszintű megbízhatósági kapcsolatot az Azure-AD DS. Első lépésként jelentkezzen be a [Azure Portalba](https://portal.azure.com).

## <a name="networking-considerations"></a>Hálózati szempontok

Az Azure AD DS erőforrás-erdőt üzemeltető virtuális hálózatnak a helyszíni Active Directory hálózati kapcsolatra van szüksége. Az alkalmazásoknak és a szolgáltatásoknak hálózati kapcsolatra van szükségük az Azure AD DS erőforrás-erdőt üzemeltető virtuális hálózathoz is. Az Azure AD DS erőforrás-erdőhöz való hálózati kapcsolatnak mindig és stabilnak kell lennie, különben előfordulhat, hogy a felhasználók nem tudják hitelesíteni vagy elérni az erőforrásokat.

Mielőtt erdőszintű megbízhatósági kapcsolatot konfigurál az Azure AD DSban, győződjön meg arról, hogy az Azure és a helyszíni környezet közötti hálózatkezelés megfelel a következő követelményeknek:

* Magánhálózati IP-címek használata. Ne használja a DHCP-t dinamikus IP-címek hozzárendelésével.
* Kerülje az átfedésben lévő IP-címek használatát, hogy a virtuális hálózatok és az Útválasztás sikeresen kommunikáljon az Azure-ban és a helyszínen.
* Egy Azure-beli virtuális hálózatnak szüksége van egy átjáró-alhálózatra a helyek közötti (S2S) VPN-vagy ExpressRoute-kapcsolat konfigurálásához.
* Hozzon létre elegendő IP-címmel rendelkező alhálózatokat a forgatókönyv támogatásához.
* Győződjön meg arról, hogy az Azure AD DS rendelkezik saját alhálózattal, ne ossza meg ezt a virtuális hálózati alhálózatot az Application VM és a Services szolgáltatással.
* A társ virtuális hálózatok nem tranzitívak.
    * Az Azure-beli virtuális hálózatokat az összes olyan virtuális hálózat között létre kell hozni, amely az Azure AD DS erőforrás-erdő megbízhatóságát szeretné használni a helyszíni AD DS környezetben.
* Folyamatos hálózati kapcsolatot biztosít a helyszíni Active Directory erdőben. Ne használjon igény szerinti kapcsolatokat.
* Győződjön meg arról, hogy folyamatos névfeloldás (DNS) van az Azure AD DS erőforrás-erdő neve és a helyszíni Active Directory erdő neve között.

## <a name="configure-dns-in-the-on-premises-domain"></a>A DNS konfigurálása a helyszíni tartományban

Ha az Azure AD DS felügyelt tartományát a helyszíni környezetből szeretné megfelelően feloldani, lehetséges, hogy továbbítókat kell hozzáadnia a meglévő DNS-kiszolgálókhoz. Ha nem konfigurálja a helyszíni környezetet az Azure AD DS felügyelt tartományával folytatott kommunikációra, hajtsa végre a következő lépéseket a helyszíni AD DS tartomány felügyeleti munkaállomásáról:

1. Válassza a **Start | Felügyeleti eszközök | DNS**
1. Kattintson a jobb gombbal a DNS-kiszolgáló, például a *myAD01*lehetőségre, majd válassza a **Tulajdonságok** lehetőséget.
1. Válassza a **továbbítók**, majd a **Szerkesztés** lehetőséget a további továbbítók hozzáadásához.
1. Adja hozzá az Azure AD DS felügyelt tartomány IP-címeit, például a *10.0.1.4* és a *10.0.1.5*.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Bejövő erdőszintű megbízhatóság létrehozása a helyszíni tartományban

A helyszíni AD DS tartománynak szüksége van egy bejövő erdőszintű megbízhatóságra az Azure AD DS felügyelt tartományhoz. Ezt a megbízhatóságot manuálisan kell létrehozni a helyszíni AD DS tartományban, nem hozható létre a Azure Portalból.

A helyi AD DS tartomány bejövő megbízhatóságának konfigurálásához hajtsa végre az alábbi lépéseket a helyszíni AD DS tartomány felügyeleti munkaállomásáról:

1. Válassza a **Start | Felügyeleti eszközök | Tartományok és megbízhatósági kapcsolatok Active Directory**
1. Kattintson a jobb gombbal a tartomány, például a *onprem.contoso.com*, majd a **Tulajdonságok** elemre.
1. Válassza a **Megbízhatóságok** lapot, majd az **új megbízhatóság** elemet.

   > [!NOTE]
   > Ha nem látja a **megbízhatósági kapcsolatok** menüt, ellenőrizze a **Tulajdonságok** területen az *erdő típusát*. Csak az *erőforrás* -erdők hozhatnak létre megbízhatósági kapcsolatokat. Ha az erdő típusa *felhasználó*, nem hozhat létre megbízhatósági kapcsolatot. Jelenleg nincs lehetőség egy Azure AD DS felügyelt tartomány erdő-típusának módosítására. Törölnie kell, majd újra létre kell hoznia a felügyelt tartományt erőforrás-erdőként.

1. Adja meg a nevet az Azure AD DS tartománynév, például *aadds.contoso.com*, majd kattintson a **tovább** gombra.
1. Válassza az **erdőszintű megbízhatóság**létrehozása lehetőséget, majd hozzon létre egy **módszert: bejövő** megbízhatóság.
1. Válassza **ezt a tartományt csak**a megbízhatósági kapcsolat létrehozásához. A következő lépésben létrehozza a megbízhatóságot az Azure AD DS felügyelt tartományhoz tartozó Azure Portalban.
1. Válassza az **erdőszintű hitelesítés**használata lehetőséget, majd adja meg és erősítse meg a megbízhatósági jelszót. Ugyanezt a jelszót is megadta a Azure Portal a következő szakaszban.
1. Lépjen be a következő néhány Windows alapértelmezett beállításokkal, majd válassza a nem lehetőséget **, ne erősítse meg a kimenő megbízhatóságot**.
1. Válassza a **Befejezés** lehetőséget.

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Kimenő erdőszintű megbízhatósági kapcsolat létrehozása az Azure-ban AD DS

Az Azure AD DS felügyelt tartomány és a létrehozott bejövő erdőszintű megbízhatósági kapcsolat megoldására konfigurált helyszíni AD DS tartománnyal most létrehozta a kimenő erdőszintű megbízhatóságot. Ez a kimenő erdőszintű megbízhatósági kapcsolat befejezi a helyszíni AD DS tartomány és az Azure AD DS felügyelt tartomány közötti megbízhatósági kapcsolatot.

A Azure Portal Azure AD DS felügyelt tartomány kimenő megbízhatóságának létrehozásához hajtsa végre a következő lépéseket:

1. A Azure Portal keresse meg és válassza ki a **Azure ad Domain Services**elemet, majd válassza ki a felügyelt tartományt, például *aadds.contoso.com*
1. Az Azure AD DS felügyelt tartomány bal oldali menüjében válassza a **Megbízhatóságok**lehetőséget, majd válassza a **+ megbízhatóság hozzáadása** lehetőséget.
1. Adja meg a megbízhatóságot azonosító megjelenítendő nevet, majd a helyszíni megbízható erdő DNS-nevét, például *onprem.contoso.com*
1. Adja meg ugyanazt a megbízhatósági jelszót, amelyet a rendszer az előző szakaszban található helyszíni AD DS tartományhoz tartozó bejövő erdő megbízhatóságának konfigurálásakor használt.
1. Adjon meg legalább két DNS-kiszolgálót a helyszíni AD DS tartományhoz, például *10.0.2.4* és *végpontjául szolgáló*
1. Ha elkészült, **mentse** a kimenő erdő megbízhatóságát

    [Kimenő erdőszintű megbízhatóság létrehozása a Azure Portalban](./media/create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>Erőforrás-hitelesítés ellenőrzése

A következő gyakori forgatókönyvekkel ellenőrizheti, hogy az erdőszintű megbízhatóság helyesen hitelesíti-e a felhasználókat és az erőforrásokhoz való hozzáférést:

* [Helyszíni felhasználói hitelesítés az Azure AD DS erőforrás-erdőben](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Erőforrásokhoz való hozzáférés az Azure AD DS erőforrás-erdőben helyszíni felhasználó használatával](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Fájl-és nyomtatómegosztás engedélyezése](#enable-file-and-printer-sharing)
    * [Biztonsági csoport létrehozása és Tagok hozzáadása](#create-a-security-group-and-add-members)
    * [Fájlmegosztás létrehozása erdők közötti hozzáféréshez](#create-a-file-share-for-cross-forest-access)
    * [Erdők közötti hitelesítés ellenőrzése erőforráshoz](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Helyszíni felhasználói hitelesítés az Azure AD DS erőforrás-erdőben

Az Azure AD DS erőforrás-tartományhoz csatlakoztatni kell a Windows Server rendszerű virtuális gépet. Ezzel a virtuális géppel ellenőrizheti, hogy a helyszíni felhasználó tud-e hitelesítést végezni egy virtuális gépen.

1. Kapcsolódjon az Azure AD DS erőforrás-erdőhöz csatlakozó Windows Server rendszerű virtuális géphez Távoli asztal és az Azure AD DS rendszergazdai hitelesítő adataival. Ha hálózati szintű hitelesítés (NLA) hibaüzenetet kap, ellenőrizze, hogy a használt felhasználói fiók nem tartományi felhasználói fiók-e.

    > [!NOTE]
    > A Azure AD Domain Serviceshoz csatlakozó virtuális gépekhez való biztonságos csatlakozáshoz használhatja az Azure-beli [megerősített gazdagép szolgáltatást](https://docs.microsoft.com/azure/bastion/bastion-overview) a támogatott Azure-régiókban.

1. Nyisson meg egy parancssort, és a `whoami` parancs használatával jelenítse meg az aktuálisan hitelesített felhasználó megkülönböztető nevét:

    ```console
    whoami /fqdn
    ```

1. A `runas` parancs használatával hitelesítheti magát a helyszíni tartományból. A következő parancsban cserélje le a `userUpn@trusteddomain.com` elemet a megbízható helyszíni tartomány felhasználójának egyszerű felhasználónevével. A parancs a felhasználó jelszavát kéri:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Ha a hitelesítés sikeres, megnyílik egy új parancssori ablak. Az új parancssor címe `running as userUpn@trusteddomain.com`tartalmaz.
1. A helyi Active Directory a hitelesített felhasználó megkülönböztető nevét az új parancssorban `whoami /fqdn` használatával tekintheti meg.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Erőforrásokhoz való hozzáférés az Azure AD DS erőforrás-erdőben helyszíni felhasználó használatával

Az Azure AD DS erőforrás-erdőhöz csatlakozó Windows Server rendszerű virtuális gép használatával tesztelheti azt a forgatókönyvet, ahol a felhasználók hozzáférhetnek az erőforrás-erdőben tárolt erőforrásokhoz, amikor a helyszíni tartományban lévő számítógépekkel hitelesítik magukat a helyszíni tartomány számítógépeiről. Az alábbi példák bemutatják, hogyan hozhat létre és tesztelheti a különböző gyakori forgatókönyveket.

#### <a name="enable-file-and-printer-sharing"></a>Fájl-és nyomtatómegosztás engedélyezése

1. Kapcsolódjon az Azure AD DS erőforrás-erdőhöz csatlakozó Windows Server rendszerű virtuális géphez Távoli asztal és az Azure AD DS rendszergazdai hitelesítő adataival. Ha hálózati szintű hitelesítés (NLA) hibaüzenetet kap, ellenőrizze, hogy a használt felhasználói fiók nem tartományi felhasználói fiók-e.

    > [!NOTE]
    > A Azure AD Domain Serviceshoz csatlakozó virtuális gépekhez való biztonságos csatlakozáshoz használhatja az Azure-beli [megerősített gazdagép szolgáltatást](https://docs.microsoft.com/azure/bastion/bastion-overview) a támogatott Azure-régiókban.

1. Nyissa meg a **Windows-beállításokat**, majd keresse meg és válassza ki a **hálózati és megosztási központot**.
1. Válassza a **Speciális megosztási beállítások módosítása** lehetőséget.
1. A **tartományi profil**területen válassza a **fájl-és nyomtatómegosztás bekapcsolása** , majd a **módosítások mentése**lehetőséget.
1. **A hálózati és megosztási központ**bezárásához.

#### <a name="create-a-security-group-and-add-members"></a>Biztonsági csoport létrehozása és Tagok hozzáadása

1. Nyissa meg **Active Directory felhasználók és számítógépek beépülő modult**.
1. Kattintson a jobb gombbal a tartománynévre, válassza az **új**, majd a **szervezeti egység**elemet.
1. A név mezőbe írja be a *LocalObjects*nevet, majd kattintson az **OK gombra**.
1. Válassza ki, majd kattintson a jobb gombbal a **LocalObjects** elemre a navigációs ablaktáblán. Válassza az **új** , majd a **csoport**lehetőséget.
1. Írja be a FileServerAccess **nevet a csoport neve** mezőbe. A **Csoport hatóköre**területen válassza a **tartomány helyi**lehetőséget, majd kattintson **az OK gombra**.
1. A tartalom ablaktáblán kattintson duplán a **FileServerAccess**elemre. Válassza a **tagok**lehetőséget, válassza a **Hozzáadás**, majd a **helyszínek**lehetőséget.
1. Válassza ki a helyszíni Active Directory a **hely** nézetből, majd kattintson **az OK gombra**.
1. Írja be a *tartományi felhasználók* értéket az **adja meg a kijelölendő objektumok nevét** mezőbe. Jelölje be a Névellenőrzés **jelölőnégyzetet**, adja meg a helyszíni Active Directory hitelesítő adatait, majd kattintson **az OK gombra**.

    > [!NOTE]
    > Meg kell adnia a hitelesítő adatokat, mert a megbízhatósági kapcsolat csak egy módszer. Ez azt jelenti, hogy az Azure-AD DS felhasználók nem férhetnek hozzá az erőforrásokhoz, vagy megkereshetik a megbízható (helyszíni) tartományban lévő felhasználókat vagy csoportokat.

1. A helyszíni Active Directory **tartományi felhasználók** csoportjának a **FileServerAccess** csoport tagjának kell lennie. A csoport mentéséhez és az ablak bezárásához kattintson **az OK gombra** .

#### <a name="create-a-file-share-for-cross-forest-access"></a>Fájlmegosztás létrehozása erdők közötti hozzáféréshez

1. A Windows Server rendszerű virtuális gépen, amely az Azure AD DS erőforrás-erdőhöz csatlakozik, hozzon létre egy mappát, és adja meg a nevet (például *CrossForestShare*).
1. Kattintson a jobb gombbal a mappára, és válassza a **Tulajdonságok**lehetőséget.
1. Válassza a **Biztonság** fület, majd kattintson a **Szerkesztés**elemre.
1. A *CrossForestShare engedélyei* párbeszédpanelen válassza a **Hozzáadás**lehetőséget.
1. Írja be a *FileServerAccess* **nevet az írja be a kijelölendő objektumok nevét**, majd kattintson **az OK gombra**.
1. A **csoportok vagy a felhasználónevek** listából válassza a *FileServerAccess* lehetőséget. A **FileServerAccess engedélyei** listán válassza az *Engedélyezés lehetőséget* a **módosítási** és **írási** engedélyekhez, majd kattintson **az OK gombra**.
1. Válassza a **megosztás** fület, majd kattintson a speciális megosztás elemre. **..**
1. Válassza a **mappa megosztása**lehetőséget, majd adjon meg egy emlékezetes nevet a fájlmegosztás számára a **megosztás nevében** , például *CrossForestShare*.
1. Válassza az **engedélyek**lehetőséget. Az **engedélyek mindenki** számára listában válassza az **Engedélyezés lehetőséget** a **módosítási** engedélyhez.
1. Kattintson kétszer **az OK** , majd a **Bezárás**gombra.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Erdők közötti hitelesítés ellenőrzése erőforráshoz

1. Jelentkezzen be a helyszíni Active Directoryhoz csatlakoztatott Windows-számítógép használatával a helyszíni Active Directory felhasználói fiókjával.
1. A **Windows Intéző**használatával kapcsolódjon a létrehozott megosztáshoz a teljes állomásnév és a megosztás (például `\\fs1.aadds.contoso.com\CrossforestShare`) használatával.
1. Az írási engedély ellenőrzéséhez kattintson a jobb gombbal a mappára, válassza az **új**, majd a **szöveges dokumentum**lehetőséget. Használja az alapértelmezett név **új szöveges dokumentumot**.

    Ha az írási engedélyek helyesen vannak beállítva, létrejön egy új szöveges dokumentum. A következő lépésekkel megnyithatja, szerkesztheti és szükség szerint törölheti a fájlt.
1. Az olvasási engedély ellenőrzéséhez nyissa meg az **új szöveges dokumentumot**.
1. A módosítási engedély érvényességének ellenőrzéséhez adjon hozzá szöveget a fájlhoz, és lépjen be a **Jegyzettömbbe**. Amikor a rendszer kéri a módosítások mentését, válassza a **Mentés**lehetőséget.
1. A törlési engedély ellenőrzéséhez kattintson a jobb gombbal az **új szöveges dokumentum** elemre, és válassza a **Törlés**lehetőséget. A fájl törlésének megerősítéséhez válassza az **Igen** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A DNS konfigurálása helyszíni AD DS környezetben az Azure AD DS-kapcsolat támogatásához
> * Egyirányú bejövő erdőszintű megbízhatósági kapcsolat létrehozása helyszíni AD DS környezetben
> * Egyirányú kimenő erdőszintű megbízhatósági kapcsolat létrehozása az Azure-ban AD DS
> * A hitelesítés és az erőforrás-hozzáférés megbízhatósági kapcsolatának tesztelése és ellenőrzése

Az Azure AD DS erdők típusaival kapcsolatos további információk: Mik az [erőforrás-erdők?][concepts-forest] és [hogyan működnek az erdőszintű megbízhatósági kapcsolatok az Azure ad DSban?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
