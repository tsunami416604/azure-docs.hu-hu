---
title: Azure VMware Solution by CloudSimple rövid útmutató – privát felhő létrehozása
description: Ismerje meg, hogyan hozhat létre és konfigurálhat egy privát felhőt az Azure VMware-megoldással a CloudSimple használatával
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6b68dcd47377ee56c4ebedc94905e1f0a8b70b38
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812340"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Rövid útmutató – privát felhőalapú környezet konfigurálása

Ebből a cikkből megtudhatja, hogyan hozhat létre CloudSimple saját felhővel, és hogyan állíthatja be a saját felhőalapú környezetét.

## <a name="before-you-begin"></a>Előkészületek

CIDR-tartomány kiosztása a vSphere/vSAN alhálózatokhoz a privát felhőben. A privát felhőt egy vCenter-kiszolgáló által kezelt elkülönített VMware stack (ESXi-gazdagépek, vCenter, vSAN és NSX) környezete hozza létre. A felügyeleti összetevők a vSphere/vSAN alhálózatok CIDR kiválasztott hálózatban vannak telepítve. A hálózati CIDR tartománya különböző alhálózatokra van osztva az üzembe helyezés során.  A vSphere/vSAN alhálózati címtartomány egyedinek kell lennie. Nem lehet átfedésben a CloudSimple-környezettel kommunikáló hálózattal.  A CloudSimple kommunikáló hálózatok a helyszíni hálózatokat és az Azure-beli virtuális hálózatokat is tartalmazzák.  A vSphere/vSAN alhálózatokkal kapcsolatos további információkért lásd: a [VLAN-ok és](cloudsimple-vlans-subnets.md)az alhálózatok áttekintése.

* Minimális vSphere/vSAN alhálózatok CIDR tartományának előtagja:/24 
* VSphere/vSAN alhálózatok maximális CIDR-tartományának előtagja:/21

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-private-cloud"></a>Magánfelhő létrehozása

1. Válassza az **Összes szolgáltatás** elemet.
2. Keressen rá a **CloudSimple Services**kifejezésre.
3. Válassza ki azt a CloudSimple-szolgáltatást, amelyen létre szeretné hozni a saját Felhőjét.
4. Az Áttekintés lapon kattintson a **privát felhő létrehozása** elemre a CloudSimple-portál új böngésző lapjának megnyitásához.  Ha a rendszer kéri, jelentkezzen be az Azure bejelentkezési hitelesítő adataival.  

    ![Privát felhő létrehozása az Azure-ból](media/create-private-cloud-from-azure.png)

5. A CloudSimple-portálon adja meg a saját felhő nevét
6. Válassza ki a saját felhő **helyét**
7. Válassza ki az Azure-ban kiépített **csomópont típusát** .  Kiválaszthatja a [CS28 vagy a CS36 lehetőséget](cloudsimple-node.md#vmware-solution-by-cloudsimple-nodes-sku). Az utóbbi lehetőség a maximális számítási és memória-kapacitást is tartalmazza.
8. A **csomópontok számának**meghatározása.  Privát felhő létrehozásához legalább három csomópont szükséges

    ![Privát felhő létrehozása – alapszintű információ](media/create-private-cloud-basic-info.png)

9. Kattintson **a Tovább gombra: Speciális beállítások**.
10. Adja meg a vSphere/vSAN alhálózatok CIDR tartományát. Győződjön meg arról, hogy a CIDR-tartomány nem fedi át a helyszíni vagy más Azure-alhálózatokat.

    ![Privát Felhőbeli speciális beállítások létrehozása](media/create-private-cloud-advanced-options.png)

11. Válassza **a Next (tovább) lehetőséget: Tekintse át**és hozza létre.
12. Tekintse át a beállításokat. Ha módosítania kell a beállításokat, kattintson az **előző**gombra.
13. Kattintson a **Create** (Létrehozás) gombra.

A felhőalapú kiépítési folyamat elindul.  A privát felhő üzembe helyezése akár két óráig is eltarthat.

## <a name="launch-cloudsimple-portal"></a>A CloudSimple-portál elindítása

A CloudSimple-portál a Azure Portalról érhető el.  Az CloudSimple-portál az Azure bejelentkezési hitelesítő adataival lesz elindítva az egyszeri bejelentkezés (SSO) használatával.  A CloudSimple-portál eléréséhez engedélyeznie kell a **CloudSimple szolgáltatás-engedélyezési** alkalmazást.  Az engedélyek megadásával kapcsolatos további információkért lásd: [hozzájárulás a CloudSimple Service Authorization alkalmazáshoz](https://docs.azure.cloudsimple.com/access-cloudsimple-portal/#consent-to-cloudsimple-service-authorization-application)

1. Válassza az **Összes szolgáltatás** elemet.
2. Keressen rá a **CloudSimple Services**kifejezésre.
3. Válassza ki azt a CloudSimple-szolgáltatást, amelyen létre szeretné hozni a saját Felhőjét.
4. Az Áttekintés lapon kattintson **a CloudSimple portálra** , és nyissa meg a CloudSimple-portál új böngésző lapját.  Ha a rendszer kéri, jelentkezzen be az Azure bejelentkezési hitelesítő adataival.  

    ![A CloudSimple-portál elindítása](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Pont – hely típusú VPN létrehozása

A pont – hely típusú VPN-kapcsolat a legegyszerűbb módszer a privát felhőhöz való csatlakozásra a számítógépről. Pont – hely típusú VPN-kapcsolat használata, ha távolról csatlakozik a privát felhőhöz.  A privát felhőhöz való gyors hozzáféréshez kövesse az alábbi lépéseket.  A helyszíni hálózatról CloudSimple-régióhoz való hozzáférés a [helyek közötti VPN](https://docs.azure.cloudsimple.com/vpn-gateway/) vagy az [Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)használatával végezhető el.

### <a name="create-gateway"></a>Átjáró létrehozása

1. Indítsa el a CloudSimple portált, és válassza a **hálózat**lehetőséget.
2. Válassza a **VPN Gateway**lehetőséget.
3. Kattintson az **új VPN Gateway**elemre.

    ![VPN-átjáró létrehozása](media/create-vpn-gateway.png)

4. Az **átjáró konfigurálása**lapon írja be a következő beállításokat, majd kattintson a **tovább**gombra.

    * Válassza a **pont – hely típusú VPN** lehetőséget átjáró típusaként.
    * Adjon meg egy nevet az átjáró azonosításához.
    * Válassza ki azt az Azure-helyet, ahol a CloudSimple szolgáltatás telepítve van.
    * A pont – hely átjáróhoz tartozó ügyféloldali alhálózat megadása.  A kapcsolódáskor a DHCP-címek ezen az alhálózaton lesznek megadva.

5. A **kapcsolatok/felhasználók**területen válassza a következő beállításokat, majd kattintson a **tovább**gombra.

    * Ha automatikusan engedélyezni szeretné az összes jelenlegi és jövőbeli felhasználó számára a privát felhő elérését ezen a pont – hely átjárón keresztül, válassza az **összes felhasználó automatikus hozzáadása**lehetőséget. Ha ezt a beállítást választja, a felhasználói listán szereplő összes felhasználó automatikusan ki lesz választva. Az automatikus beállítást felülbírálhatja az egyes felhasználók törlésével a listában.
    * Csak az egyes felhasználók kiválasztásához kattintson a felhasználók listájában található jelölőnégyzetekre.

6. A VLAN-ok/alhálózatok szakaszban megadhatja az átjáróhoz és a kapcsolatokhoz tartozó felügyeleti és felhasználói VLAN-okat/alhálózatokat.

    * Az **automatikus hozzáadási** beállítások az átjáróhoz tartozó globális házirendet határozzák meg. A beállítások az aktuális átjáróra érvényesek. A beállítások felülbírálása a **Select** területen lehetséges.
    * Válassza a **felügyeleti VLAN-ok vagy a privát felhők alhálózatok hozzáadása**lehetőséget. 
    * A felhasználó által definiált VLAN-ok/alhálózatok hozzáadásához kattintson a **felhasználó által definiált VLAN**-ok/alhálózatok hozzáadása lehetőségre. 
    * A beállítások **kiválasztása** felülbírálja a globális beállításokat az **automatikus Hozzáadás**területen. 

7. A beállítások áttekintéséhez kattintson a **tovább** gombra. A módosítások elvégzéséhez kattintson a Szerkesztés ikonra.
8. A VPN-átjáró létrehozásához kattintson a **Létrehozás** gombra.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Csatlakozás a CloudSimple pont – hely típusú VPN használatával

A CloudSimple a számítógépről való csatlakozáshoz VPN-ügyfél szükséges.  Töltse le a Windowshoz készült [OpenVPN](https://openvpn.net/community-downloads/) -ügyfelet, illetve a MacOS és az OS X [viszkozitását](https://www.sparklabs.com/viscosity/download/) .

1. Indítsa el a CloudSimple portált, és válassza a **hálózat**lehetőséget.
2. Válassza a **VPN Gateway**lehetőséget.
3. A VPN-átjárók listájában kattintson a pont – hely VPN-átjáróra.
4. Válassza a **felhasználók**lehetőséget.
5. Kattintson a **saját VPN-konfiguráció letöltése** elemre.

    ![VPN-konfiguráció letöltése](media/download-p2s-vpn-configuration.png)

6. A konfiguráció importálása a VPN-ügyfélen

    * Útmutató a [konfiguráció importálásához a Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program) -ügyfélen
    * Útmutató a [konfiguráció importálásához MacOS vagy OS X rendszeren](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Kapcsolódás a CloudSimple

## <a name="create-a-vlan-for-your-workload-vms"></a>VLAN létrehozása a számítási feladatok virtuális gépei számára

A privát felhő létrehozása után hozzon létre egy VLAN-t, amelyen üzembe helyezheti a számítási feladatokat/alkalmazás virtuális gépeket.

1. A CloudSimple-portálon válassza a **hálózat**lehetőséget.
2. Kattintson a **VLAN/** alhálózatok elemre.
3. Kattintson a **VLAN/alhálózat létrehozása** lehetőségre.

    ![VLAN/alhálózat létrehozása](media/create-new-vlan-subnet.png)

4. Válassza ki az új VLAN/alhálózat **saját felhőjét** .
5. Válasszon ki egy VLAN-azonosítót a listából.  
6. Adja meg az alhálózat nevét az alhálózat azonosításához.
7. Határozza meg az alhálózat CIDR tartományát és maszkját.  Ez a tartomány nem lehet átfedésben a meglévő alhálózatokkal.
8. Kattintson a **Submit** (Küldés) gombra.

    ![VLAN/alhálózat létrehozása – részletek](media/create-new-vlan-subnet-details.png)

A rendszer létrehozza a VLAN-t/alhálózatot.  Most már használhatja ezt a VLAN-azonosítót egy elosztott porttartomány létrehozásához a saját Felhőbeli vCenter. 

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Környezet összekötése egy Azure-beli virtuális hálózattal

A CloudSimple ExpressRoute áramkört biztosít a saját felhőhöz. Az Azure-beli virtuális hálózatot a ExpressRoute áramkörhöz is összekapcsolhatja. A kapcsolat beállításával kapcsolatos részletes információkért kövesse az [Azure Virtual Network-kapcsolat](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/) lépéseit a ExpressRoute használatával

## <a name="sign-in-to-vcenter"></a>Bejelentkezés a vCenter

Most már bejelentkezhet a vCenter-be a virtuális gépek és házirendek beállításához.

1. A vCenter eléréséhez Kezdje a CloudSimple portálról. A Kezdőlap **Általános feladatok**területén kattintson az **vSphere-ügyfél indítása**lehetőségre.  Válassza ki a privát felhőt, majd kattintson a **vSphere-ügyfél indítása** lehetőségre a privát felhőben.

    ![VSphere-ügyfél elindítása](media/launch-vcenter-from-cloudsimple-portal.png)

2. Válassza ki a kívánt vSphere-ügyfelet a vCenter eléréséhez, és jelentkezzen be a felhasználónevével és jelszavával.  Az alapértelmezett értékek a következők:
    * Felhasználónév: **CloudOwner@cloudsimple.local**
    * Jelszó: **CloudSimple123!**  

A következő eljárások vCenter képernyői a vSphere (HTML5) ügyfélből származnak.

## <a name="change-your-vcenter-password"></a>VCenter jelszavának módosítása

A CloudSimple azt javasolja, hogy a vCenter való első bejelentkezéskor változtassa meg a jelszavát.  
A beállított jelszónak meg kell felelnie a következő követelményeknek:

* Maximális élettartam: A jelszót 365 naponta módosítani kell
* Ismételt használat korlátozása: A felhasználók nem tudják újra felhasználni az előző öt jelszót
* Hossza: 8-20 karakter
* Speciális karakter: Legalább egy speciális karakter
* Alfabetikus karakterek: Legalább egy nagybetűt, A-Z és legalább egy kisbetűs karaktert, a-z
* Számok Legalább egy numerikus karakter, 0-9
* Azonos szomszédos karakterek maximális száma: Három

    Példa: A CC vagy a CCC elfogadható a jelszó részeként, de a CCCC nem.

Ha olyan jelszót állít be, amely nem felel meg a követelményeknek:

* Ha a vSphere Flash-ügyfelet használja, hibát jelez
* Ha a HTML5-ügyfelet használja, nem jelent hibát. Az ügyfél nem fogadja el a változást, és a régi jelszó továbbra is működni fog.

## <a name="change-nsx-administrator-password"></a>NSX rendszergazdai jelszavának módosítása

A NSX Manager alapértelmezett jelszóval van telepítve.  Azt javasoljuk, hogy a privát felhő létrehozása után módosítsa a jelszót.

   * Felhasználónév: **rendszergazda**
   * Jelszó: **CloudSimple123!**

A NSX Manager teljes tartományneve (FQDN) és IP-címe a CloudSimple portálon található.

1. Indítsa el a CloudSimple portált, és válassza az **erőforrások**lehetőséget.
2. Kattintson a használni kívánt privát felhőre.
3. **VSphere felügyeleti hálózat** kiválasztása
4. Használja a **NSX Manager** teljes tartománynevét vagy IP-címét, és kapcsolódjon a webböngészővel. 

    ![NSX-kezelő teljes tartományneve megkeresése](media/private-cloud-nsx-manager-fqdn.png)

A jelszó módosításához kövesse a [felhasználó jelszavának kezelése](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.4/administration/GUID-DB31B304-66A5-4516-9E55-2712D12B4F27.html)című témakör utasításait.

> [!WARNING]
> Alapértelmezés szerint a NSX rendszergazdai jelszava 90 nap után lejár.

## <a name="create-a-port-group"></a>Port csoport létrehozása

Elosztott porttartomány létrehozása a vSphere-ben:

1. Kövesse az [vSphere hálózati útmutató](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf)"elosztott Port hozzáadása" című szakaszának utasításait.
2. Az elosztott port csoport beállításakor adja meg a virtuális [helyi hálózat létrehozása a számítási feladatok virtuális gépei számára](#create-a-vlan-for-your-workload-vms)című részében létrehozott VLAN-azonosítót.

## <a name="next-steps"></a>További lépések

* [VMware virtuális gépek használata az Azure-ban](https://docs.azure.cloudsimple.com/quickstart-create-vmware-virtual-machine)
* [VMware virtuális gépek használata az Azure-ban](quickstart-create-vmware-virtual-machine.md)
* [Kapcsolódás helyszíni hálózathoz az Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Helyek közötti VPN beállítása a helyszíni környezetből](https://docs.azure.cloudsimple.com/vpn-gateway/)
