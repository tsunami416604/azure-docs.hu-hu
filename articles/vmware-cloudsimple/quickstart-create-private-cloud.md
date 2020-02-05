---
title: Azure VMware Solutions (AVS) gyors útmutató – privát AVS-felhő létrehozása
description: Ismerje meg, hogyan hozhat létre és konfigurálhat egy AVS Private-felhőt Azure VMware-megoldásokkal (AVS)
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cafcf04dac0542f1506980d8b9484b82b558e100
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77018567"
---
# <a name="quickstart---configure-an-avs-private-cloud-environment"></a>Rövid útmutató – az AVS Private Cloud Environment konfigurálása

Ebből a cikkből megtudhatja, hogyan hozhat létre egy AVS Private-felhőt, és hogyan állíthatja be az AVS saját felhőalapú környezetét.

## <a name="before-you-begin"></a>Előzetes teendők

Tekintse át a [hálózati előfeltételeket](cloudsimple-network-checklist.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-an-avs-private-cloud"></a>AVS Private Cloud létrehozása

Az AVS Private Cloud egy elkülönített VMware-verem, amely támogatja az ESXi-gazdagépeket, a vCenter, a vSAN és a NSX.

Az AVS privát felhők kezelése az AVS-portálon keresztül történik. Saját vCenter-kiszolgálóval rendelkeznek a saját felügyeleti tartományában. A verem dedikált csomópontokon és elszigetelt operációs rendszer nélküli hardveres csomópontokon fut.

1. Válassza az **Összes szolgáltatás** elemet.
2. Keressen rá az **AVS-szolgáltatások**kifejezésre.
3. Válassza ki azt az AVS-szolgáltatást, amelyen létre szeretné hozni a saját AVS-Felhőjét.
4. Az **Áttekintés**lapon kattintson az **AVS Private Cloud létrehozása** lehetőségre az AVS Portal új böngésző lapjának megnyitásához. Ha a rendszer kéri, jelentkezzen be az Azure bejelentkezési hitelesítő adataival. 

    ![AVS Private Cloud létrehozása az Azure-ból](media/create-private-cloud-from-azure.png)

5. Az AVS-portálon adja meg az AVS Private-felhő nevét.
6. Válassza ki az AVS Private-felhő **helyét** .
7. Válassza ki a **csomópont típusát**, amely megfelel az Azure-ban kiépített lehetőségeknek.
8. **Csomópontok számának**meghatározása Az AVS privát felhő létrehozásához legalább három csomópont szükséges.

    ![Az AVS Private Cloud létrehozása – alapszintű információ](media/create-private-cloud-basic-info.png)

9. Kattintson a **Tovább: speciális beállítások**elemre.
10. Adja meg a vSphere/vSAN alhálózatok CIDR tartományát. Győződjön meg arról, hogy a CIDR-tartomány nem fedi átfedésben a helyszíni vagy más Azure-alhálózatokkal (virtuális hálózatokkal) vagy az átjáró-alhálózattal.

    **CIDR-tartomány beállításai:** /24,/23,/22, vagy/21. A/24 CIDR-tartomány legfeljebb 26 csomópontot támogat, a/23 CIDR-tartomány akár 58 csomópontot is támogat, a/22 és a/21 CIDR tartomány pedig 64-csomópontokat (a csomópontok maximális számát egy AVS-beli privát felhőben) támogatja. További információ és VLAN-ok és alhálózatok: a [VLAN-ok és az alhálózatok áttekintése](cloudsimple-vlans-subnets.md).

      > [!IMPORTANT]
      > Az vSphere/vSAN CIDR-tartomány IP-címei az AVS Private Cloud Infrastructure használatára vannak fenntartva. Ne használja az IP-címet ebben a tartományban bármely virtuális gépen.

11. Kattintson **a Tovább gombra: Áttekintés és létrehozás**.
12. Tekintse át a beállításokat. Ha módosítania kell a beállításokat, kattintson az **előző**gombra.
13. Kattintson a **Create** (Létrehozás) gombra.

Elindul az AVS Private Cloud kiépítési folyamata. Az AVS Private Cloud üzembe helyezése akár két óráig is eltarthat.

## <a name="launch-avs-portal"></a>Az AVS-portál elindítása

Az AVS-portál Azure Portalból érhető el. Az AVS-portál az Azure bejelentkezési hitelesítő adataival lesz elindítva az egyszeri bejelentkezés (SSO) használatával. Az AVS-portál eléréséhez engedélyeznie kell az **AVS szolgáltatás-engedélyezési** alkalmazást. Az engedélyek megadásával kapcsolatos további információkért lásd: [hozzájárulás az AVS szolgáltatás engedélyezési alkalmazásához](access-cloudsimple-portal.md#consent-to-avs-service-authorization-application).

1. Válassza az **Összes szolgáltatás** elemet.
2. Keressen rá az **AVS-szolgáltatások**kifejezésre.
3. Válassza ki azt az AVS-szolgáltatást, amelyen létre szeretné hozni a saját AVS-Felhőjét.
4. Az Áttekintés lapon kattintson **az Ugrás az AVS-portálra** elemre az AVS-portál új böngésző lapjának megnyitásához. Ha a rendszer kéri, jelentkezzen be az Azure bejelentkezési hitelesítő adataival. 

    ![Az AVS-portál elindítása](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Pont – hely típusú VPN létrehozása

A pont – hely típusú VPN-kapcsolat az a legegyszerűbb módszer, amellyel a számítógépről csatlakozhat az AVS saját felhőhöz. Pont – hely típusú VPN-kapcsolat használata, ha távolról csatlakozik az AVS Private Cloud szolgáltatáshoz. Az AVS Private Cloud gyors eléréséhez kövesse az alábbi lépéseket. A helyszíni hálózatból származó AVS-régióhoz való hozzáférés a [helyek közötti VPN](vpn-gateway.md) vagy az [Azure ExpressRoute](on-premises-connection.md)használatával végezhető el.

### <a name="create-gateway"></a>Átjáró létrehozása

1. Indítsa el az AVS Portalt, és válassza a **hálózat**lehetőséget.
2. Válassza a **VPN Gateway**lehetőséget.
3. Kattintson az **új VPN Gateway**elemre.

    ![VPN-átjáró létrehozása](media/create-vpn-gateway.png)

4. Az **átjáró konfigurálása**lapon írja be a következő beállításokat, majd kattintson a **tovább**gombra.

    * Válassza a **pont – hely típusú VPN** lehetőséget átjáró típusaként.
    * Adjon meg egy nevet az átjáró azonosításához.
    * Válassza ki azt az Azure-helyet, ahol az AVS szolgáltatás telepítve van.
    * A pont – hely átjáróhoz tartozó ügyféloldali alhálózat megadása. A kapcsolódáskor a DHCP-címek ezen az alhálózaton lesznek megadva.

5. A **kapcsolatok/felhasználók**területen válassza a következő beállításokat, majd kattintson a **tovább**gombra.

    * Ha automatikusan engedélyezni szeretné az összes jelenlegi és jövőbeli felhasználó számára az AVS Private Cloud elérését ezen a pont – hely átjárón keresztül, válassza az **összes felhasználó automatikus hozzáadása**lehetőséget. Ha ezt a beállítást választja, a felhasználói listán szereplő összes felhasználó automatikusan ki lesz választva. Az automatikus beállítást felülbírálhatja az egyes felhasználók törlésével a listában.
    * Csak az egyes felhasználók kiválasztásához kattintson a felhasználók listájában található jelölőnégyzetekre.

6. A VLAN-ok/alhálózatok szakaszban megadhatja az átjáróhoz és a kapcsolatokhoz tartozó felügyeleti és felhasználói VLAN-okat/alhálózatokat.

    * Az **automatikus hozzáadási** beállítások az átjáróhoz tartozó globális házirendet határozzák meg. A beállítások az aktuális átjáróra érvényesek. A beállítások felülbírálása a **Select** területen lehetséges.
    * Válassza **a felügyeleti VLAN-ok vagy az AVS privát felhők alhálózatok hozzáadása**lehetőséget.
    * A felhasználó által definiált VLAN-ok/alhálózatok hozzáadásához kattintson a **felhasználó által definiált VLAN-ok/alhálózatok hozzáadása**lehetőségre.
    * A beállítások **kiválasztása** felülbírálja a globális beállításokat az **automatikus Hozzáadás**területen.

7. A beállítások áttekintéséhez kattintson a **tovább** gombra. A módosítások elvégzéséhez kattintson a Szerkesztés ikonra.
8. A VPN-átjáró létrehozásához kattintson a **Létrehozás** gombra.

### <a name="connect-to-avs-using-point-to-site-vpn"></a>Csatlakozás az AVS-hez pont – hely típusú VPN használatával

A VPN-ügyfél szükséges az AVS-hez való csatlakozáshoz a számítógépről. Töltse le a Windowshoz készült [OpenVPN-ügyfelet](https://openvpn.net/community-downloads/) , illetve a MacOS és az OS X [viszkozitását](https://www.sparklabs.com/viscosity/download/) .

1. Indítsa el az AVS Portalt, és válassza a **hálózat**lehetőséget.
2. Válassza a **VPN Gateway**lehetőséget.
3. A VPN-átjárók listájában kattintson a pont – hely VPN-átjáróra.
4. Válassza a **felhasználók**lehetőséget.
5. Kattintson **a saját VPN-konfiguráció letöltése**elemre.

    ![VPN-konfiguráció letöltése](media/download-p2s-vpn-configuration.png)

6. Importálja a konfigurációt a VPN-ügyfélen.

    * Útmutató a [konfiguráció importálásához a Windows-ügyfélen](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Útmutató a [konfiguráció importálásához MacOS vagy OS X rendszeren](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Kapcsolódás az AVS-hez.

## <a name="create-a-vlan-for-your-workload-vms"></a>VLAN létrehozása a számítási feladatok virtuális gépei számára

Az AVS Private Cloud létrehozása után hozzon létre egy VLAN-t, amelyen üzembe helyezheti a számítási feladatokat/alkalmazás virtuális gépeket.

1. Az AVS-portálon válassza a **hálózat**lehetőséget.
2. Kattintson a **VLAN/alhálózatok**elemre.
3. Kattintson a **VLAN/alhálózat létrehozása**lehetőségre.

    ![VLAN/alhálózat létrehozása](media/create-new-vlan-subnet.png)

4. Válassza ki az új VLAN/alhálózat számára a **saját AVS-felhőt** .
5. Válasszon ki egy VLAN-azonosítót a listából. 
6. Adja meg az alhálózat nevét az alhálózat azonosításához.
7. Határozza meg az alhálózat CIDR tartományát és maszkját. Ez a tartomány nem lehet átfedésben a meglévő alhálózatokkal.
8. Kattintson a **Submit (Küldés**) gombra.

    ![VLAN/alhálózat létrehozása – részletek](media/create-new-vlan-subnet-details.png)

A rendszer létrehozza a VLAN-t/alhálózatot. Ezzel a VLAN-AZONOSÍTÓval létrehozhat egy elosztott port csoportot az AVS Private Cloud vCenter.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Környezet összekötése egy Azure-beli virtuális hálózattal

Az AVS egy ExpressRoute áramkört biztosít az AVS Private Cloud-hoz. Az Azure-beli virtuális hálózatot a ExpressRoute áramkörhöz is összekapcsolhatja. A kapcsolat beállításával kapcsolatos részletes információkért kövesse az [Azure Virtual Network-kapcsolat](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/)lépéseit a ExpressRoute használatával.

## <a name="sign-in-to-vcenter"></a>Bejelentkezés a vCenter

Most már bejelentkezhet a vCenter-be a virtuális gépek és házirendek beállításához.

1. A vCenter eléréséhez kezdje az AVS-portálról. A Kezdőlap **Általános feladatok**területén kattintson az **vSphere-ügyfél indítása**lehetőségre. Válassza ki az AVS Private-felhőt, majd kattintson az **vSphere-ügyfél elindítása** elemre az AVS Private Cloud-on.

    ![VSphere-ügyfél elindítása](media/launch-vcenter-from-cloudsimple-portal.png)

2. Válassza ki a kívánt vSphere-ügyfelet a vCenter eléréséhez, és jelentkezzen be a felhasználónevével és jelszavával. Az alapértelmezett értékek a következők:
    * Felhasználónév: **CloudOwner@AVS.local**
    * Jelszó: **AVS123!**  

A következő eljárások vCenter képernyői a vSphere (HTML5) ügyfélből származnak.

## <a name="change-your-vcenter-password"></a>VCenter jelszavának módosítása

Az AVS azt javasolja, hogy a vCenter való első bejelentkezéskor változtassa meg a jelszavát. A beállított jelszónak meg kell felelnie a következő követelményeknek:

* Maximális élettartam: a jelszót 365 naponta módosítani kell
* Újbóli használat korlátozása: a felhasználók nem tudják újra felhasználni az előző öt jelszót
* Hossz: 8-20 karakter
* Speciális karakter: legalább egy speciális karakter
* Alfabetikus karakterek: legalább egy nagybetűt, egy-Z és legalább egy kisbetűs karaktert, a-z karaktert
* Számok: legalább egy numerikus karakter, 0-9
* Azonos szomszédos karakterek maximális száma: három

    Példa: a CC vagy a CCC elfogadható a jelszó részeként, de a CCCC nem.

Ha olyan jelszót állít be, amely nem felel meg a követelményeknek:

* Ha a vSphere Flash-ügyfelet használja, hibát jelez
* Ha a HTML5-ügyfelet használja, nem jelent hibát. Az ügyfél nem fogadja el a változást, és a régi jelszó továbbra is működni fog.

## <a name="access-nsx-manager"></a>Hozzáférés a NSX-kezelőhöz

A NSX Manager alapértelmezett jelszóval van telepítve. 

* Felhasználónév: **rendszergazda**
* Jelszó: **AVS123!**

A NSX Manager teljes tartományneve (FQDN) és IP-címe az AVS-portálon található.

1. Indítsa el az AVS Portalt, és válassza az **erőforrások**lehetőséget.
2. Kattintson a használni kívánt AVS Private-felhőre.
3. **VSphere felügyeleti hálózat** kiválasztása
4. Használja a **NSX Manager** teljes tartománynevét vagy IP-címét, és kapcsolódjon a webböngészővel.

    ![NSX-kezelő teljes tartományneve megkeresése](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>Port csoport létrehozása

Elosztott porttartomány létrehozása a vSphere-ben:

1. Kövesse a [vSphere hálózati útmutató](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf)"elosztott Port hozzáadása" című szakaszának utasításait.
2. Az elosztott port csoport beállításakor adja meg a virtuális [helyi hálózat létrehozása a számítási feladatok virtuális gépei számára](#create-a-vlan-for-your-workload-vms)című részében létrehozott VLAN-azonosítót.

## <a name="next-steps"></a>Következő lépések

* [VMware virtuális gépek használata az Azure-ban](quickstart-create-vmware-virtual-machine.md)
* [Kapcsolódás helyszíni hálózathoz az Azure ExpressRoute](on-premises-connection.md)
* [Helyek közötti VPN beállítása a helyszíni környezetből](vpn-gateway.md)
