---
title: Magánfelhő létrehozása CloudSimple rövid útmutató – Azure VMware-megoldások
description: Ismerje meg, hogyan hozhat létre, és a magánfelhő konfigurálása az Azure VMware megoldás CloudSimple szerint
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e1fc7809ad94d589483b87c638d027a39098164e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209541"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Rövid útmutató – a magánfelhő-környezet konfigurálása

Ebből a cikkből megtudhatja, hogyan hozhat létre CloudSimple magánfelhőt, és állítsa be a magánfelhő-környezetet.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-private-cloud"></a>Magánfelhő létrehozása

1. Válassza az **Összes szolgáltatás** elemet.
2. Keresse meg **CloudSimple szolgáltatások**.
3. Válassza ki a CloudSimple szolgáltatást, amelyen szeretné létrehozni a Magánfelhő.
4. Áttekintés, kattintson **Magánfelhő létrehozása** egy új böngészőlapon CloudSimple portál megnyitásához.  Ha a rendszer kéri, jelentkezzen be az Azure bejelentkezési nevét.  

    ![Magánfelhő létrehozása az Azure-ból](media/create-private-cloud-from-azure.png)

5. CloudSimple portálon adja meg a Magánfelhő nevét
6. Válassza ki a **hely** a privát felhő
7. Válassza ki a **csomóponttípus** vásárolt Azure-ban.  Választhat a [CS28 vagy CS36](cloudsimple-node.md#vmware-solution-by-cloudsimple-nodes-sku). Az utóbbi lehetőséget tartalmaz a maximális számítási és memória-kapacitás.
8. Adja meg a **csomópontok száma**.  Legalább három csomópont szükséges Magánfelhő létrehozása

    ![Hozzon létre a Magánfelhő – alapvető adatok](media/create-private-cloud-basic-info.png)

9. Kattintson a **tovább: Speciális beállítások**.
10. VSphere/vSAN alhálózatokat a CIDR-tartományt adja meg. Győződjön meg arról, hogy a CIDR-tartományt nem átfedésben a helyszíni vagy más Azure-alhálózatokat.

    ![Magánfelhő - speciális beállítások létrehozása](media/create-private-cloud-advanced-options.png)

11. Válassza ki **tovább: Tekintse át, és hozzon létre**.
12. Tekintse át a beállításokat. Ha módosítania kell a beállításokat, kattintson a **előző**.
13. Kattintson a **Create** (Létrehozás) gombra.

Privát felhő kiépítési folyamat indul.  Legfeljebb két órát ki kell építeni a Magánfelhő vehet igénybe.

## <a name="launch-cloudsimple-portal"></a>Indítsa el a CloudSimple portál

Az Azure Portalon elérhető a portál CloudSimple.  CloudSimple portál elindul az Azure-ral jelentkezzen be hitelesítő adatok használata egyszeri bejelentkezéshez (SSO).  A CloudSimple portál eléréséhez szükséges, hogy engedélyezze a **CloudSimple szolgáltatás engedélyezésével** alkalmazás.  Az engedélyek további információkért lásd: [hozzájárulás az alkalmazáshoz CloudSimple szolgáltatás engedélyezése](https://docs.azure.cloudsimple.com/access-cloudsimple-portal/#consent-to-cloudsimple-service-authorization-application)

1. Válassza az **Összes szolgáltatás** elemet.
2. Keresse meg **CloudSimple szolgáltatások**.
3. Válassza ki a CloudSimple szolgáltatást, amelyen szeretné létrehozni a Magánfelhő.
4. Áttekintés, kattintson **a CloudSimple portálon** egy új böngészőlapon CloudSimple portál megnyitásához.  Ha a rendszer kéri, jelentkezzen be az Azure bejelentkezési nevét.  

    ![Launch CloudSimple Portal](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Pont – hely VPN létrehozása

Pont – hely VPN-kapcsolat, a legegyszerűbb módszer a Magánfelhő csatlakozhat a számítógépéről. Használja a pont – hely VPN-kapcsolatot, ha csatlakozik a Magánfelhő távolról.  Gyors hozzáférés a Magánfelhő kövesse az alábbi lépéseket.  A helyszíni hálózathoz való hozzáférés CloudSimple régió teheti meg a [Site-to-Site VPN](https://docs.azure.cloudsimple.com/vpn-gateway/) vagy [Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/).

### <a name="create-gateway"></a>Átjáró létrehozása

1. Indítsa el a Portalon, és válassza a CloudSimple **hálózati**.
2. Válassza ki **VPN-átjáró**.
3. Kattintson a **új VPN-átjáró**.

    ![VPN-átjáró létrehozása](media/create-vpn-gateway.png)

4. A **átjárókonfiguráció**, adja meg a következő beállításokat, és kattintson a **tovább**.

    * Válassza ki **pont – hely VPN** , az átjáró típusa.
    * Adjon meg egy nevet az átjáró azonosításához.
    * Válassza ki az Azure-beli hely, a CloudSimple szolgáltatás telepítési helyét.
    * Adja meg az ügyfél IP-alhálózatot a pont – hely átjáró.  DHCP-címek az alhálózatról kapnak csatlakozáskor.

5. A **kapcsolat/felhasználó**, adja meg a következő beállításokat, és kattintson a **tovább**.

    * Automatikusan engedélyezi a minden jelenlegi és jövőbeli felhasználó számára a Magánfelhő a pont – hely átjárón keresztül, válassza ki a **összes felhasználóinak automatikus hozzáadása**. Ha ezt a lehetőséget választja, a felhasználók listájában minden felhasználó automatikusan ki van jelölve. A listán szereplő egyes felhasználók mértékegységei felül lehet bírálni az automatikus beállítást.
    * Csak egyéni felhasználók kiválasztásához kattintson a Felhasználólista jelölőnégyzeteket.

6. A VLAN-OK/alhálózatok területen adja meg a felügyeleti és felhasználói VLAN-OK/alhálózatok és a kapcsolatok.

    * A **automatikusan hozzáadja a** lehetőségek a globális szabályzatot, az átjáró beállítása. A beállítások érvényesek a jelenlegi átjáróval. A beállítások felülbírálhatók a **kiválasztása** területen.
    * Válassza ki **adja hozzá a felügyeleti VLAN-OK/alhálózatok privát felhők**. 
    * Az összes felhasználó által megadott VLAN-OK/alhálózatok hozzáadásához kattintson **adja hozzá a felhasználó által megadott VLAN-OK/alhálózatok**. 
    * A **kiválasztása** beállítások felülírják a globális beállítások csoportban **automatikusan hozzáadja a**. 

7. Kattintson a **tovább** , tekintse át a beállításokat. Kattintson a Szerkesztés ikonok a módosításokat.
8. Kattintson a **létrehozás** a VPN-átjáró létrehozásához.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Pont – hely típusú VPN-kapcsolattal CloudSimple csatlakozni

VPN-ügyfél a számítógépről CloudSimple való kapcsolódáshoz szükséges.  Töltse le [OpenVPN ügyfél](https://openvpn.net/community-downloads/) for Windows vagy [viszkozitás](https://www.sparklabs.com/viscosity/download/) macOS-és OS X.

1. Indítsa el a Portalon, és válassza a CloudSimple **hálózati**.
2. Válassza ki **VPN-átjáró**.
3. VPN-átjárók a listából kattintson a pont – hely VPN-átjárón.
4. Válassza ki **felhasználók**.
5. Kattintson a **saját VPN-konfiguráció letöltése**

    ![VPN-konfiguráció letöltése](media/download-p2s-vpn-configuration.png)

6. A VPN-ügyfél a konfiguráció importálása

    * Utasítások [Windows-ügyfélen konfiguráció importálása](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Utasítások [macOS- vagy OS X rendszeren konfiguráció importálása](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Csatlakozás CloudSimple

## <a name="create-a-vlan-for-your-workload-vms"></a>Hozzon létre egy VLAN-t a munkaterhelési virtuális gépekhez

Magánfelhő létrehozása után hozzon létre egy VLAN-t a számítási feladatok/alkalmazások virtuális gépek üzembe.

1. Válassza a CloudSimple portál **hálózati**.
2. Kattintson a **VLAN/alhálózatok**.
3. Kattintson a **VLAN-alhálózat létrehozása**

    ![VLAN-alhálózat létrehozása](media/create-new-vlan-subnet.png)

4. Válassza ki a **Magánfelhő** az új VLAN/alhálózat.
5. Válassza ki egy VLAN-Azonosítót a listából.  
6. Adja meg az alhálózati azonosító alhálózati nevet.
7. Adja meg az alhálózat CIDR-tartományt és maszk.  Ez a tartomány nem lehetnek átfedésben meglévő alhálózatokkal.
8. Kattintson a **Submit** (Küldés) gombra.

    ![VLAN, illetve alhálózati adatok létrehozása](media/create-new-vlan-subnet-details.png)

A VLAN/alhálózatot hoz létre.  Mostantól használhatja ezt a VLAN-Azonosítót a Magánfelhő vcenter elosztott port csoport létrehozásához. 

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Csatlakoztassa környezetét az Azure-beli virtuális hálózathoz

CloudSimple biztosít ExpressRoute-kapcsolatcsoport ki a magánfelhője számára. Az ExpressRoute-kapcsolatcsoport csatlakozhat a virtuális hálózat az Azure-ban. További részletek a kapcsolat beállításának, kövesse a [Azure ExpressRoute használatával virtuális hálózati kapcsolat](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/)

## <a name="sign-in-to-vcenter"></a>Jelentkezzen be a vCenter

Most már bejelentkezhet a vCenter virtuális gépek és a szabályzatok beállításához.

1. VCenter eléréséhez, indítsa el a CloudSimple portálról. A kezdőlapon alatt **gyakori feladatok**, kattintson a **indítsa el a vSphere Client**.  Válassza ki a privát felhőt, és kattintson a **indítsa el a vSphere Client** a privát felhő.

    ![Indítsa el a vSphere Client](media/launch-vcenter-from-cloudsimple-portal.png)

2. Válassza ki az előnyben részesített vSphere client vCenter eléréséhez, és jelentkezzen be a felhasználónevét és jelszavát.  Az alapértelmezett érték a következők:
    * Felhasználónév: **CloudOwner@cloudsimple.local**
    * Jelszó: **CloudSimple123!**  

A következő eljárások a vCenter képernyők a vSphere (HTML5) ügyfél származnak.

## <a name="change-your-vcenter-password"></a>A vCenter-jelszó módosítása

CloudSimple azt javasolja, hogy a jelszó módosítása az első alkalommal bejelentkezik a vCenter.  
A jelszót állít be az alábbi követelményeknek kell megfelelnie:

* Maximális élettartama: Módosítania kell minden 365 nap
* Újbóli korlátozása: Felhasználó nem használhat újra bármelyik előző öt jelszó
* Hossza: 8 – 20 karakter
* Speciális karakter: Legalább egy speciális karakter
* Alfabetikus karaktereket: Legalább egy nagybetűt A-Z és legalább egy kisbetűt, a – z
* Számok: Legalább egy numerikus karakter, 0 – 9
* Maximális azonos szomszédos karakter: Három

    Példa: Másolatot kap vagy Másolatot elfogadható a jelszó részeként, de CCCC nem.

Ha egy jelszót, amely nem felel meg a követelményeknek:

* Ha használja a vSphere Flash Client, hibát jelez
* Ha a HTML5-alapú ügyfél használ, azt nem jelzett hibát. Az ügyfél nem fogadja el a módosítást, és a régi jelszó továbbra is működik.

## <a name="change-nsx-administrator-password"></a>NSX rendszergazdai jelszó módosítása

NSX manager alapértelmezett jelszó van telepítve.  Azt javasoljuk, hogy a Magánfelhő létrehozása után módosítja a jelszavát.

   * Felhasználónév: **rendszergazda**
   * Jelszó: **CloudSimple123!**

A teljesen minősített tartománynevét (FQDN) és a NSX manager IP-címét a CloudSimple portálon találja.

1. Indítsa el a Portalon, és válassza a CloudSimple **erőforrások**.
2. Kattintson a használni kívánt Magánfelhő.
3. Válassza ki **vSphere felügyeleti hálózat**
4. A teljes tartománynév vagy IP-címét használja **NSX Manager** , és csatlakozzon a webböngésző használatával. 

    ![Keresse meg a NSX Manager teljes Tartományneve](media/private-cloud-nsx-manager-fqdn.png)

A jelszó megváltoztatásához kövesse a [NSX Manager telepítés](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.2/com.vmware.nsxt.install.doc/GUID-A65FE3DD-C4F1-47EC-B952-DEDF1A3DD0CF.html).

## <a name="create-a-port-group"></a>Port csoport létrehozása

A vSphere elosztott port csoport létrehozása:

1. Kövesse a "Hozzáadás elosztott portcsoportnak," az a [vSphere hálózati útmutató](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Ha beállította az elosztott portcsoportnak, adja meg a létrehozott VLAN-Azonosítót [hozzon létre egy VLAN-t a munkaterhelési virtuális gépek](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>További lépések

* [Az Azure-ban a VMware virtuális gépeket felhasználására](https://docs.azure.cloudsimple.com/quickstart-create-vmware-virtual-machine)
* [Az Azure-ban a VMware virtuális gépeket felhasználására](quickstart-create-vmware-virtual-machine.md)
* [Csatlakozhat a helyszíni hálózatot az Azure ExpressRoute használatával](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [A helyszíni Site-to-Site VPN beállítása](https://docs.azure.cloudsimple.com/vpn-gateway/)
