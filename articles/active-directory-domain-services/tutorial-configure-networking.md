---
title: Oktatóanyag – Virtuális hálózat konfigurálása az Azure AD tartományi szolgáltatásokhoz | Microsoft dokumentumok
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre és konfigurálhat egy Azure virtuális hálózati alhálózatot vagy hálózati társviszony-létesítést egy Azure Active Directory tartományi szolgáltatások példányához az Azure Portalhasználatával.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: af284e4c10487123c8c2a2105a25a2285ae0aa99
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474344"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-instance"></a>Oktatóanyag: Virtuális hálózat konfigurálása egy Azure Active Directory tartományi szolgáltatások példányához

A felhasználók és az alkalmazások közötti kapcsolat biztosítása érdekében az Azure Active Directory tartományi szolgáltatások (Azure AD DS) felügyelt tartománya egy Azure virtuális hálózati alhálózatba kerül. Ezt a virtuális hálózati alhálózatot csak az Azure platform által biztosított felügyelt tartományi erőforrásokhoz szabad használni. Saját virtuális gépek és alkalmazások létrehozásakor nem kell őket ugyanabba a virtuális hálózati alhálózatba telepíteni. Ehelyett hozzon létre és telepítse az alkalmazásokat egy külön virtuális hálózati alhálózat, vagy egy külön virtuális hálózat, amely az Azure AD DS virtuális hálózat társviszonyviszonyt.

Ez az oktatóanyag bemutatja, hogyan hozhat létre és konfigurálhat egy dedikált virtuális hálózati alhálózatot, vagy hogyan társviszonyt létesíthet egy másik hálózattal az Azure AD DS felügyelt tartomány virtuális hálózatához.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Ismerje meg az Azure AD DS tartományhoz csatlakozó erőforrások virtuális hálózati csatlakozási lehetőségeit
> * IP-címtartomány és további alhálózat létrehozása az Azure AD DS virtuális hálózatában
> * Virtuális hálózati társviszony-létesítés konfigurálása az Azure AD DS-től különálló hálózatra

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az Azure AD DS engedélyezéséhez *globális rendszergazdai* jogosultságokra van szüksége az Azure AD-bérlőben.
* A szükséges Azure AD DS-erőforrások létrehozásához *közreműködői* jogosultságokra van szüksége az Azure-előfizetésben.
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány a konfigurált és konfigurált az Azure AD-bérlő.
    * Szükség esetén az első oktatóanyag [létrehoz és konfigurál egy Azure Active Directory tartományi szolgáltatások példányát.][create-azure-ad-ds-instance]

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ebben az oktatóanyagban hozza létre és konfigurálja az Azure AD DS-példányt az Azure Portalhasználatával. Első lépésekhez először jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="application-workload-connectivity-options"></a>Alkalmazás-munkaterhelés-kapcsolódási lehetőségek

Az előző oktatóanyagban egy Azure AD DS felügyelt tartomány jött létre, amely néhány alapértelmezett konfigurációs lehetőséget használt a virtuális hálózathoz. Ezek az alapértelmezett beállítások létrehozott egy Azure virtuális hálózat és a virtuális hálózati alhálózat. Az Azure AD DS tartományvezérlők, amelyek a felügyelt tartományi szolgáltatások csatlakozik ehhez a virtuális hálózati alhálózathoz.

Amikor olyan virtuális gépeket hoz létre és futtat, amelyeknek az Azure AD DS felügyelt tartományát kell használniuk, hálózati kapcsolatot kell biztosítani. Ez a hálózati kapcsolat az alábbi módokon biztosítható:

* Hozzon létre egy további virtuális hálózati alhálózatot az alapértelmezett Azure AD DS felügyelt tartomány virtuális hálózatában. Ez a további alhálózat, ahol hozza létre és csatlakoztassa a virtuális gépek.
    * Mivel a virtuális gépek ugyanannak a virtuális hálózatnak a részét képezik, automatikusan elvégezhetik a névfeloldást, és kommunikálhatnak az Azure AD DS tartományvezérlőivel.
* Konfigurálja az Azure virtuális hálózati társviszony-létesítését az Azure AD DS által felügyelt tartomány virtuális hálózatáról egy vagy több különálló virtuális hálózatra. Ezek a különálló virtuális hálózatok, ahol hozza létre és csatlakoztassa a virtuális gépeket.
    * A virtuális hálózati társviszony-létesítés konfigurálásakor a DNS-beállításokat is be kell állítania, hogy névfeloldást használjanak vissza az Azure AD DS tartományvezérlőkhöz.

Általában csak a hálózati csatlakozási lehetőségek egyikét használja. A választás gyakran le, hogy hogyan szeretné kezelni az Azure-erőforrások elkülönítése. Ha az Azure AD DS-t és a csatlakoztatott virtuális gépeket egyetlen erőforráscsoportként szeretné kezelni, létrehozhat egy további virtuális hálózati alhálózatot a virtuális gépekhez. Ha el szeretné különválasztani az Azure AD DS, majd a csatlakoztatott virtuális gépek felügyeletét, használhatja a virtuális hálózati társviszony-létesítést. Dönthet úgy is, hogy virtuális hálózati társviszony-létesítést használ, hogy kapcsolatot biztosítson az Azure-környezetben meglévő virtuális gépekhez, amelyek egy meglévő virtuális hálózathoz csatlakoznak.

Ebben az oktatóanyagban csak egy ilyen virtuális hálózati kapcsolati lehetőséget kell konfigurálnia.

A virtuális hálózat tervezéséről és konfigurálásáról további információt a [Hálózati szempontok az Azure Active Directory tartományi szolgáltatásokhoz] [hálózati szempontok] című témakörben talál.

## <a name="create-a-virtual-network-subnet"></a>Virtuális hálózati alhálózat létrehozása

Alapértelmezés szerint az Azure AD DS felügyelt tartománnyal létrehozott Azure virtuális hálózat egyetlen virtuális hálózati alhálózatot tartalmaz. Ezt a virtuális hálózati alhálózatot csak az Azure platform nak kell használnia felügyelt tartományi szolgáltatások nyújtásához. Saját virtuális virtuális hálózat létrehozása és használata ebben az Azure-beli virtuális hálózatban hozzon létre és használjon saját virtuális hálózatot, hozzon létre egy további alhálózatot.

Virtuális hálózati alhálózat létrehozásához virtuális gépekhez és alkalmazás-munkaterhelésekhez hajtsa végre az alábbi lépéseket:

1. Az Azure Portalon válassza ki az Azure AD DS felügyelt tartományának erőforráscsoportját, például *a myResourceGroup.* Az erőforrások listájából válassza ki az alapértelmezett virtuális hálózatot, például az *aadds-vnet*.
1. A virtuális hálózat ablakának bal oldali menüjében válassza a **Címterület lehetőséget.** A virtuális hálózat egyetlen címterülettel jön *létre: 10.0.2.0/24*, amelyet az alapértelmezett alhálózat használ.

    Adjon hozzá egy további IP-címtartományt a virtuális hálózathoz. A címtartomány mérete és a ténylegesen használandó IP-címtartomány a már üzembe helyezett egyéb hálózati erőforrásoktól függ. Az IP-címtartomány nem fedheti át az Azure-ban vagy a helyszíni környezetben meglévő címtartományokat. Győződjön meg arról, hogy az IP-címtartomány mérete elég nagy ahhoz, hogy az alhálózatba telepíteni kívánt virtuális gépek száma.

    A következő példában egy további *10.0.3.0/24* IP-címtartomány kerül hozzáadásra. Ha készen áll, válassza a **Mentés gombot.**

    ![További virtuális hálózati IP-címtartomány hozzáadása az Azure Portalon](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Ezután a virtuális hálózati ablak bal oldali menüjében válassza az **Alhálózatok**lehetőséget, majd válassza a **+ Alhálózat** lehetőséget az alhálózat hozzáadásához.
1. Adja meg az alhálózat nevét, például *a munkaterheléseket.* Ha szükséges, frissítse a **címtartományt,** ha az előző lépésekben a virtuális hálózathoz konfigurált IP-címtartomány egy részét szeretné használni. Egyelőre hagyja meg az alapértelmezett beállításokat, például a hálózati biztonsági csoportot, az útvonaltáblát, a szolgáltatásvégpontokat.

    A következő példában létrejön egy *10.0.3.0/24* IP-címtartományt használó, számítási *feladatok* nevű alhálózat:

    ![További virtuális hálózati alhálózat hozzáadása az Azure Portalon](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. Ha készen áll, válassza **az OK gombot.** A virtuális hálózati alhálózat létrehozása néhány percet vesz igénybe.

Amikor olyan virtuális gépet hoz létre, amelynek az Azure AD DS felügyelt tartományát kell használnia, győződjön meg arról, hogy ezt a virtuális hálózati alhálózatot választja. Ne hozzon létre virtuális gépeket az alapértelmezett *aadds-alhálózatban.* Ha másik virtuális hálózatot választ, nincs hálózati kapcsolat és DNS-feloldás az Azure AD DS felügyelt tartományeléréséhez, hacsak nem konfigurálja a virtuális hálózati társviszony-létesítést.

## <a name="configure-virtual-network-peering"></a>Virtuális hálózati társviszony-létesítés konfigurálása

Előfordulhat, hogy rendelkezik egy meglévő Azure virtuális hálózat virtuális gépek, vagy szeretné megtartani az Azure AD DS felügyelt tartományi virtuális hálózat külön. A felügyelt tartomány használatához a más virtuális hálózatokban lévő virtuális gépeknek módot kell adniuk az Azure AD DS-tartományvezérlők közötti kommunikációra. Ez a kapcsolat az Azure virtuális hálózati társviszony-létesítésével biztosítható.

Az Azure virtuális hálózati társviszony-létesítés, két virtuális hálózatok csatlakoznak egymáshoz, anélkül, hogy egy virtuális magánhálózati (VPN) eszköz. A hálózati társviszony-létesítés lehetővé teszi a virtuális hálózatok gyors csatlakoztatását és az Azure-környezetben zajló forgalom meghatározását. A társviszony-létesítésről az [Azure virtuális hálózati társviszony-létesítésének áttekintése című témakörben olvashat bővebben.][peering-overview]

Ha virtuális hálózatot szeretne társviszonyba lépni az Azure AD DS által felügyelt tartományi virtuális hálózattal, hajtsa végre az alábbi lépéseket:

1. Válassza ki az Azure AD DS-példányhoz létrehozott alapértelmezett virtuális hálózatot, amelyet *aadds-vnet*néven hoznak létre.
1. A virtuális hálózat ablakának bal oldali menüjében válassza a **Társviszony-létesítések**lehetőséget.
1. Társviszony-létesítés létrehozásához válassza a **+ Hozzáadás lehetőséget.** A következő példában az alapértelmezett *aadds-vnet* egy *myVnet*nevű virtuális hálózatra mutat. Konfigurálja a következő beállításokat a saját értékeivel:

    * **Az aadds-vnet és a távoli virtuális hálózat között való társviszony-létesítés neve**: A két hálózat leíró azonosítója, például *aadds-vnet-to-myvnet*
    * **Virtuális hálózat központi telepítése :** *Erőforrás-kezelő*
    * **Előfizetés**: Annak a virtuális hálózatnak az előfizetése, amelyhez társviszonyba szeretne lépni, például *az Azure*
    * **Virtuális hálózat**: Az a virtuális hálózat, amelyhez társviszonyt szeretne létesíteni, például *a myVnet*
    * **A csevegés neve a myVnet-től az aadds-vnet-** ig: A két hálózat leíró azonosítója, például *a myvnet-to-aadds-vnet*

    ![Virtuális hálózati társviszony-létesítés konfigurálása az Azure Portalon](./media/tutorial-configure-networking/create-peering.png)

    Hagyja meg a virtuális hálózati hozzáférés vagy a továbbított forgalom egyéb alapértelmezett beállításait, kivéve, ha a környezetre vonatkozó speciális követelményekkel rendelkezik, majd válassza az **OK gombot.**

1. Néhány percet vesz igénybe, hogy létrehozza a társviszony-létesítésaz Azure AD DS virtuális hálózat és a kiválasztott virtuális hálózat létrehozása. Ha készen áll, a **Társviszony-létesítési állapot** *jelentések Csatlakoztatva*, ahogy az a következő példában látható:

    ![Sikeresen csatlakozott társviszony-létesítési hálózatok az Azure Portalon](./media/tutorial-configure-networking/connected-peering.png)

Mielőtt a virtuális gépek a társviszony-létesített virtuális hálózat használhatja az Azure AD DS felügyelt tartományban, konfigurálja a DNS-kiszolgálók, hogy a megfelelő névfeloldás.

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>DNS-kiszolgálók konfigurálása a társviszony-társított virtuális hálózatban

A virtuális gépek és alkalmazások a társviszony-létesített virtuális hálózat sikeresen beszélni az Azure AD DS felügyelt tartományban, a DNS-beállításokat frissíteni kell. Az Azure AD DS tartományvezérlők IP-címét a társviszonyt létesített virtuális hálózat DNS-kiszolgálóiként kell konfigurálni. A tartományvezérlőket kétféleképpen konfigurálhatja DNS-kiszolgálóként a társviszony-létesített virtuális hálózathoz:

* Konfigurálja az Azure virtuális hálózati DNS-kiszolgálókat az Azure AD DS tartományvezérlők használatára.
* Konfigurálja a társviszonyt létesített virtuális hálózaton használt meglévő DNS-kiszolgálót feltételes DNS-továbbítással az Azure AD DS felügyelt tartományába való közvetlen továbbításhoz. Ezek a lépések a meglévő DNS-kiszolgálótól függően változnak.

Ebben az oktatóanyagban konfiguráljuk az Azure virtuális hálózati DNS-kiszolgálókat úgy, hogy az összes lekérdezést az Azure AD DS tartományvezérlőkre irányítsák.

1. Az Azure Portalon válassza ki a társviszonyba vett virtuális hálózat erőforráscsoportját, például a *myResourceGroup.* Az erőforrások listájából válassza ki a társviszonyba vett virtuális hálózatot, például a *myVnet*.
1. A virtuális hálózat ablakának bal oldali menüjében válassza a **DNS-kiszolgálók**lehetőséget.
1. Alapértelmezés szerint a virtuális hálózat a beépített Azure által biztosított DNS-kiszolgálókat használja. Egyéni **Custom** DNS-kiszolgálókat válasszon. Adja meg az Azure AD DS tartományvezérlők IP-címét, amelyek általában *10.0.2.4* és *10.0.2.5.* Erősítse meg ezeket az IP-címeket az Azure AD DS által felügyelt tartomány **áttekintése** ablakban a portálon.

    ![A virtuális hálózati DNS-kiszolgálók konfigurálása az Azure AD DS tartományvezérlők használatára](./media/tutorial-configure-networking/custom-dns.png)

1. Ha készen áll, válassza a **Mentés gombot.** A virtuális hálózat DNS-kiszolgálóinak frissítése néhány percet vesz igénybe.
1. Ha a frissített DNS-beállításokat a virtuális gépekre szeretné alkalmazni, indítsa újra a társviszonyt létesített virtuális hálózathoz csatlakozó virtuális gépeket.

Amikor olyan virtuális gépet hoz létre, amelynek az Azure AD DS felügyelt tartományát kell használnia, győződjön meg arról, hogy ezt a társviszonyt létesített virtuális hálózatot választja. Ha másik virtuális hálózatot választ, nincs hálózati kapcsolat és DNS-feloldás az Azure AD DS felügyelt tartományának eléréséhez.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Ismerje meg az Azure AD DS tartományhoz csatlakozó erőforrások virtuális hálózati csatlakozási lehetőségeit
> * IP-címtartomány és további alhálózat létrehozása az Azure AD DS virtuális hálózatában
> * Virtuális hálózati társviszony-létesítés konfigurálása az Azure AD DS-től különálló hálózatra

A felügyelt tartomány működés közbeni megtekintéséhez hozzon létre egy virtuális gépet a tartományhoz, és csatlakozzon hozzá.

> [!div class="nextstepaction"]
> [Csatlakozás Windows Server rendszerű virtuális géphez a felügyelt tartományhoz](join-windows-vm.md)

<!-- INTERNAL LINKS --> 
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md
