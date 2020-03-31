---
title: 'Rövid útmutató: Privát felhő létrehozása'
titleSuffix: Azure VMware Solutions by CloudSimple
description: Ismerje meg, hogyan hozhat létre és konfigurálhat magánfelhőt a CloudSimple Azure VMware-megoldásaival
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7460490dbd45862f4269d25e3910373700ec9a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564720"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Rövid útmutató – Privát felhőkörnyezet konfigurálása

Ebből a cikkből megtudhatja, hogyan hozhat létre egy CloudSimple private cloudot, és hogyan állíthatja be a privát felhőbeli környezetet.

## <a name="before-you-begin"></a>Előkészületek

Tekintse át [a hálózati előfeltételeket.](cloudsimple-network-checklist.md)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .

## <a name="create-a-private-cloud"></a>Magánfelhő létrehozása

A privát felhő egy elkülönített VMware verem, amely támogatja az ESXi gazdagépek, vCenter, vSAN és NSX.

A privát felhők kezelése a CloudSimple portálon keresztül történik. Saját vCenter-kiszolgálóval rendelkeznek a saját felügyeleti tartományban. A verem dedikált csomópontokon és elkülönített, csupasz fém hardvercsomópontokon fut.

1. Válassza az **Összes szolgáltatás** elemet.
2. Keresse meg a **CloudSimple szolgáltatásokat.**
3. Válassza ki azt a CloudSimple szolgáltatást, amelyen létre szeretné hozni a magánfelhőt.
4. Az **áttekintés területen**kattintson a **Magánfelhő létrehozása** elemre a CloudSimple portál új böngészőlapjának megnyitásához.  Ha a rendszer kéri, jelentkezzen be az Azure bejelentkezési hitelesítő adataival.  

    ![Privát felhő létrehozása az Azure-ból](media/create-private-cloud-from-azure.png)

5. A CloudSimple portálon adja meg a privát felhő nevét.
6. Válassza ki a privát felhő **helyét.**
7. Válassza ki **a Csomópont típusát,** amely összhangban van az Azure-ban kiépített vel.
8. Adja meg **a csomópontszám számát**.  A magánfelhő létrehozásához legalább három csomópont szükséges.

    ![Privát felhő létrehozása - Alapvető adatok](media/create-private-cloud-basic-info.png)

9. Kattintson a **Tovább: Speciális beállítások gombra.**
10. Adja meg a CIDR-tartományt a vSphere/vSAN alhálózatokhoz. Győződjön meg arról, hogy a CIDR-tartomány nem fedi át a helyszíni vagy más Azure-alhálózatok (virtuális hálózatok) vagy az átjáró alhálózat.

    **CIDR tartomány beállításai:** /24, /23, /22 vagy /21. A /24 CIDR tartomány legfeljebb 26 csomópontot támogat, a /23 CIDR tartomány legfeljebb 58 csomópontot, a /22 és /21 CIDR tartomány pedig 64 csomópontot támogat (a privát felhőben lévő csomópontok maximális száma).  További információért és VLAN-okért és alhálózatokért olvassa el [a VLAN-ok és alhálózatok áttekintését.](cloudsimple-vlans-subnets.md)

      > [!IMPORTANT]
      > A vSphere/vSAN CIDR tartományban lévő IP-címek a private cloud infrastruktúra számára vannak fenntartva.  Ne használja az IP-címet ebben a tartományban semmilyen virtuális gépen.

11. Kattintson a **Tovább gombra: Véleményezés és létrehozás.**
12. Tekintse át a beállításokat. Ha módosítania kell a beállításokat, kattintson **az Előző**gombra.
13. Kattintson **a Létrehozás gombra.**

A privát felhőlétesítési folyamat elindul.  A magánfelhő kiépítése akár két órát is igénybe vehet.

## <a name="launch-cloudsimple-portal"></a>A CloudSimple portál elindítása

A CloudSimple portál az Azure Portalon érhető el.  A CloudSimple portál indul az Azure bejelentkezési hitelesítő adatok használatával Egyszeri bejelentkezés (SSO).  A CloudSimple portál eléréséhez engedélyeznie kell a **CloudSimple szolgáltatásengedélyezési** alkalmazást.  Az engedélyek megadásáról a Hozzájárulás a [CloudSimple szolgáltatásengedélyezési alkalmazásban](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application)talál további információt.

1. Válassza az **Összes szolgáltatás** elemet.
2. Keresse meg a **CloudSimple szolgáltatásokat.**
3. Válassza ki azt a CloudSimple szolgáltatást, amelyen létre szeretné hozni a magánfelhőt.
4. Az áttekintésből kattintson **az Ugrás a CloudSimple portálra** egy új böngészőlap megnyitásához a CloudSimple portálhoz.  Ha a rendszer kéri, jelentkezzen be az Azure bejelentkezési hitelesítő adataival.  

    ![A CloudSimple portál elindítása](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Pont-hely VPN létrehozása

A point-to-site VPN-kapcsolat a legegyszerűbb módja annak, hogy a számítógépről csatlakozzon a magánfelhőhöz. Ha távolról csatlakozik a magánfelhőhöz, használjon pont-hely VPN-kapcsolatot.  A privát felhőhöz való gyors hozzáféréshez kövesse az alábbi lépéseket.  A helyszíni hálózatról a CloudSimple régióhoz való hozzáférés a helyek közötti [VPN vagy](vpn-gateway.md) az [Azure ExpressRoute](on-premises-connection.md)használatával végezhető el.

### <a name="create-gateway"></a>Átjáró létrehozása

1. Indítsa el a CloudSimple portált, és válassza a **Hálózat**lehetőséget.
2. Válassza a **VPN-átjáró lehetőséget.**
3. Kattintson **az Új VPN-átjáró**elemre.

    ![VPN-átjáró létrehozása](media/create-vpn-gateway.png)

4. Az **Átjáró konfigurációja**párbeszédpanelen adja meg a következő beállításokat, majd kattintson a **Tovább**gombra.

    * Válassza **a pont-hely VPN** átjárótípust.
    * Adjon meg egy nevet az átjáró azonosításához.
    * Válassza ki azt az Azure-helyet, ahol a CloudSimple szolgáltatás telepítve van.
    * Adja meg a pont-hely átjáró ügyfél-alhálózatát.  A DHCP-címek a csatlakozáskor erről az alhálózatról lesznek megadva.

5. A **Kapcsolat/felhasználó**csoportban adja meg a következő beállításokat, majd kattintson a **Tovább**gombra.

    * Ha azt szeretné, hogy az összes jelenlegi és jövőbeli felhasználó automatikusan hozzáférhessen a magánfelhőhöz ezen a pont-webhely átjárón keresztül, válassza **az Összes felhasználó automatikus hozzáadása**lehetőséget. Ha ezt a beállítást választja, a Felhasználó lista összes felhasználója automatikusan ki lesz jelölve. Az automatikus beállítást felülírhatja, ha törli a lista egyes felhasználóinak jelölőnégyzetjelölését.
    * Ha csak az egyes felhasználókat szeretné bejelölni, jelölje be a Felhasználó lista jelölőnégyzetét.

6. A VLAN-ok/Alhálózatok szakasz lehetővé teszi az átjáró és a kapcsolatok felügyeleti és felhasználói VLAN-ok/alhálózatok megadását.

    * Az **Automatikus hozzáadási** beállítások az átjáró globális házirendjének beállítására vannak beállítva. A beállítások az aktuális átjáróra vonatkoznak. A beállítások felülbírálhatók a **Kijelölés** területen.
    * Válassza **a Felügyeleti VLAN-ok hozzáadása/magánfelhők alhálózatainak hozzáadása**lehetőséget.
    * Az összes felhasználó által definiált VLAN/alhálózat hozzáadásához kattintson **a Felhasználó által definiált VLAN-ok/alhálózatok hozzáadása**elemre.
    * A **Beállítások kiválasztása** felülírja a globális beállításokat az Automatikus **hozzáadás csoportban.**

7. A beállítások áttekintéséhez kattintson a **Tovább** gombra. A módosítások módosításához kattintson a Szerkesztés ikonokra.
8. A VPN-átjáró létrehozásához kattintson a **Létrehozás** gombra.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Csatlakozás a CloudSimple szolgáltatáshoz a point-to-site VPN használatával

VPN-ügyfél szükséges a CloudSimple-hez való csatlakozáshoz a számítógépről.  Töltse le az [OpenVPN klienst](https://openvpn.net/community-downloads/) Windows rendszerre, vagy [a Viscosity-t](https://www.sparklabs.com/viscosity/download/) macOS-re és OS X-re.

1. Indítsa el a CloudSimple portált, és válassza a **Hálózat**lehetőséget.
2. Válassza a **VPN-átjáró lehetőséget.**
3. A VPN-átjárók listájában kattintson a pont-hely VPN átjáróra.
4. Válassza a **Felhasználók** lehetőséget.
5. Kattintson **a VPN-konfiguráció letöltése gombra.**

    ![VPN-konfiguráció letöltése](media/download-p2s-vpn-configuration.png)

6. Importálja a konfigurációt a VPN-ügyfélen.

    * A [Windows-ügyfél konfigurációjának importálására](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program) vonatkozó utasítások
    * A [konfiguráció macOS vagy OS X rendszeren történő importálásának](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection) utasításai

7. Csatlakozzon a CloudSimple szolgáltatáshoz.

## <a name="create-a-vlan-for-your-workload-vms"></a>VlAN létrehozása a számítási feladatok virtuális gépeiszámára

A privát felhő létrehozása után hozzon létre egy VLAN-t, ahol üzembe helyezheti a számítási feladatok/alkalmazás virtuális gépeit.

1. A CloudSimple portálon válassza a **Hálózat**lehetőséget.
2. Kattintson **a VLAN/Alhálózatok parancsra.**
3. Kattintson **a VLAN/Alhálózat létrehozása gombra.**

    ![VLAN/Alhálózat létrehozása](media/create-new-vlan-subnet.png)

4. Válassza ki a **privát felhő** az új VLAN/alhálózat.
5. Válasszon vlan-azonosítót a listából.  
6. Adja meg az alhálózat azonosításához egy alhálózat nevét.
7. Adja meg az alhálózati CIDR-tartományt és maszkot.  Ez a tartomány nem lehet átfedésben egyetlen meglévő alhálózattal sem.
8. Kattintson a **Küldés gombra.**

    ![VLAN/Alhálózat részleteinek létrehozása](media/create-new-vlan-subnet-details.png)

Létrejön a VLAN/alhálózat.  Most már használhatja ezt a VLAN-azonosítót egy elosztott portcsoportot a private cloud vCenter en.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>A környezet csatlakoztatása egy Azure virtuális hálózathoz

A CloudSimple egy ExpressRoute-áramkört biztosít a privát felhőhöz. Az Azure-beli virtuális hálózatot az ExpressRoute-kapcsolatcsoporthoz csatlakoztathatja. A kapcsolat beállításával kapcsolatos részletekért kövesse az [ExpressRoute használatával az Azure virtuális hálózati kapcsolat című](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/)részben leírt lépéseket.

## <a name="sign-in-to-vcenter"></a>Bejelentkezés a vCenterbe

Most már bejelentkezhet a vCenterbe a virtuális gépek és szabályzatok beállításához.

1. A vCenter eléréséhez indítsa el a CloudSimple portálon. A Kezdőlap Gyakori **feladatok területén**kattintson a **VSphere-ügyfél indítása gombra.**  Válassza a magánfelhőt, majd kattintson a **VSphere-ügyfél indítása** a magánfelhőben parancsra.

    ![VSphere kliens indítása](media/launch-vcenter-from-cloudsimple-portal.png)

2. Válassza ki a kívánt vSphere kliens t a vCenter eléréséhez, és jelentkezzen be a felhasználónevével és jelszavával.  Az alapértelmezett értékek a következők:
    * Felhasználónév: **CloudOwner\@cloudsimple.local**
    * Jelszó: **CloudSimple123!**  

A vCenter képernyők a következő eljárások a vSphere (HTML5) kliens.

## <a name="change-your-vcenter-password"></a>A vCenter-jelszó módosítása

A CloudSimple azt javasolja, hogy változtassa meg a jelszavát, amikor először jelentkezik be a vCenterbe.  
A beállított jelszónak meg kell felelnie az alábbi követelményeknek:

* Maximális élettartam: A jelszót 365 naponta kell módosítani
* Az újrafelhasználás korlátozása: A felhasználók nem használhatják fel újra az előző öt jelszót
* Hossza: 8 - 20 karakter
* Különleges karakter: Legalább egy különleges karakter
* Alfabetikus karakterek: Legalább egy nagybetűs, A-Z és legalább egy kisbetűs karakter, a-z
* Számok: Legalább egy numerikus karakter, 0-9
* Maximális azonos szomszédos karakterek: Három

    Példa: A CC vagy a CCC a jelszó részeként elfogadható, de a CCCC nem.

Ha olyan jelszót állít be, amely nem felel meg a követelményeknek:

* ha a vSphere Flash Client, azt jelenti, hiba
* Ha a HTML5-ügyfélprogramot használja, az nem jelent hibát. Az ügyfél nem fogadja el a módosítást, és a régi jelszó továbbra is működik.

## <a name="access-nsx-manager"></a>Access NSX-kezelő

Az NSX-kezelő alapértelmezett jelszóval van telepítve. 

* Felhasználónév: **admin**
* Jelszó: **CloudSimple123!**

Az NSX-kezelő teljesen minősített tartománynevét (FQDN) és IP-címét a CloudSimple portálon találja.

1. Indítsa el a CloudSimple portált, és válassza **az Erőforrások**lehetőséget.
2. Kattintson a használni kívánt magánfelhőre.
3. VSphere **felügyeleti hálózat** kiválasztása
4. Használja az **NSX-kezelő** Teljes qdn-címét vagy IP-címét, és csatlakozzon egy webböngészőn keresztül.

    ![NSX-kezelő FQDN keresése](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>Portcsoport létrehozása

Elosztott portcsoport létrehozása a vSphere-ben:

1. Kövesse a vSphere hálózati útmutató "Elosztott portcsoport hozzáadása" című útmutatójának [utasításait.](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf)
2. Az elosztott portcsoport beállításakor adja meg a VLAN-azonosítót, amelyet a Virtuális gép létrehozása a [számítási feladatok virtuális gépeihez létrehozott VLAN-azonosítóban](#create-a-vlan-for-your-workload-vms)hozott létre.

## <a name="next-steps"></a>További lépések

* [VMware rendszerű virtuális gépek felhasználása az Azure-ban](quickstart-create-vmware-virtual-machine.md)
* [Csatlakozás helyszíni hálózathoz az Azure ExpressRoute használatával](on-premises-connection.md)
* [Helyek közötti VPN beállítása a helyszínen](vpn-gateway.md)
