---
title: Azure VMware-megoldás a CloudSimple-től – A 2.
description: Ez a témakör azt ismerteti, hogy miként állítható be 2.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1f5ff48f4d5a658a1bbb4e6b9fb4b3f0f3fb190f
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81602688"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>Számítási feladatok migrálása a 2. rétegbeli kiterjesztett hálózatok használatával

Ebben az útmutatóban megtudhatja, hogyan használhatja a Layer 2 VPN -t (L2VPN) a Layer 2 hálózat nyújtásához a helyszíni környezetből a CloudSimple private cloud-ra. Ez a megoldás lehetővé teszi a helyszíni VMware-környezetben futó számítási feladatok áttelepítését az Azure magánfelhőbe ugyanazon az alhálózati címterületen belül anélkül, hogy újra el kellene osztania a számítási feladatokat.

L2VPN alapú nyújtása Layer 2 hálózatok működhet, vagy anélkül NSX-alapú hálózatok a helyszíni VMware környezetben. Ha nem rendelkezik NSX-alapú hálózatokkal a helyszíni számítási feladatokhoz, használhat önálló NSX Edge Services Gateway-t.

> [!NOTE]
> Ez az útmutató ismerteti a forgatókönyvet, ahol a helyszíni és a magánfelhő-adatközpontok kapcsolódnak a helyek közötti VPN-en keresztül.

## <a name="deployment-scenario"></a>Központi telepítési forgatókönyv

A helyszíni hálózat L2VPN használatával történő nyújtásához konfigurálnia kell egy L2VPN-kiszolgálót (cél NSX-T Tier0 útválasztó) és egy L2VPN ügyfelet (forrás önálló ügyfél).  

Ebben a telepítési forgatókönyvben a privát felhő a helyszíni környezethez csatlakozik egy helyek közötti VPN-alagúton keresztül, amely lehetővé teszi a helyszíni felügyelet és a vMotion alhálózatok számára a privát felhőfelügyeleti és a vMotion-alhálózatokkal való kommunikációt. Ez az elrendezés szükséges cross vCenter vMotion (xVC-vMotion). Az NSX-T Tier0 útválasztó l2VPN-kiszolgálóként van telepítve a magánfelhőben.

Az önálló NSX Edge l2VPN-ügyfélként van telepítve a helyszíni környezetben, majd párosítva van az L2VPN-kiszolgálóval. A GRE-alagút végpont mindkét oldalon létrejön, és úgy van beállítva, hogy "kinyújtja" a helyszíni Layer 2 hálózatot a magánfelhőhöz. Ez a konfiguráció az alábbi ábrán látható.

![Központi telepítési forgatókönyv](media/l2vpn-deployment-scenario.png)

Az L2 VPN használatával történő áttelepítésről a [Virtuális magánhálózatok](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) című témakörben olvashat bővebben a VMware dokumentációjában.

## <a name="prerequisites-for-deploying-the-solution"></a>A megoldás üzembe helyezésének előfeltételei

A megoldás telepítése és konfigurálása előtt ellenőrizze, hogy az alábbiak vannak-e érvényben:

* A helyszíni vSphere verzió 6.7U1+ vagy 6.5P03+.
* A helyszíni vSphere licenc az Enterprise Plus szintjén van (vSphere distributed switch esetén).
* Azonosítsa a réteg 2-es hálózatának a privát felhőhöz nyújtandó munkaterhelését.
* Azonosítsa a Layer 2 hálózatot a helyszíni környezetben az L2VPN-ügyfélberendezés üzembe helyezéséhez.
* [A magánfelhő már létrejött.](create-private-cloud.md)
* Az önálló NSX-T Edge készülék verziója kompatibilis a privát felhőkörnyezetben használt NSX-T Manager verzióval (NSX-T 2.3.0).
* A helyszíni vCenterben létrehozott egy törzsport-csoport, amelyengedélyezve van a hamisított átvitelek engedélyezésével.
* Nyilvános IP-cím van fenntartva az NSX-T önálló ügyfél uplink IP-címéhez, és 1:1 NAT van érvényben a két cím közötti fordításhoz.
* A DNS-továbbítás a helyszíni DNS-kiszolgálókon van beállítva, hogy a az.cloudsimple.io tartomány a magánfelhő DNS-kiszolgálóira mutasson.
* RtT késés kisebb vagy egyenlő, mint 150 ms, ahogy a vMotion dolgozni a két helyen.

## <a name="limitations-and-considerations"></a>Korlátozások és megfontolások

Az alábbi táblázat a támogatott vSphere-verziókat és hálózati adaptertípusokat sorolja fel.  

| vSphere verzió | Forrás vSwitch típusa | Virtuális NIC-illesztőprogram | Cél vSwitch típusa | Támogatott? |
------------ | ------------- | ------------ | ------------- | ------------- 
| Összes | Dvs | Összes | Dvs | Igen |
| vSphere 6.7UI vagy újabb, 6.5P03 vagy magasabb | Dvs | VMXNET3 | N-VD-k | Igen |
| vSphere 6.7UI vagy újabb, 6.5P03 vagy magasabb | Dvs | E1000 | N-VD-k | [VWware nem támogatott](https://kb.vmware.com/s/article/56991) |
| vSphere 6.7UI vagy 6.5P03, NSX-V vagy NSX-T2.2, 6.5P03 vagy újabb verziók | Összes | Összes | N-VD-k | [VWware nem támogatott](https://kb.vmware.com/s/article/56991) |

A VMware NSX-T 2.3 kiadástól:

* A logikai kapcsoló a Private Cloud oldalon, amely a helyszíni L2VPN felett nem irányítható egyszerre. A kifeszített logikai kapcsoló nem csatlakoztatható logikai útválasztóhoz.
* Az L2VPN és az útvonalalapú IPSEC VPN-ek csak API-hívások használatával konfigurálhatók.

További információt a [Virtuális magánhálózatok](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) a VMware dokumentációjában talál.

### <a name="sample-l2-vpn-deployment-addressing"></a>Minta L2 VPN-telepítés címzése

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>Helyszíni hálózat, ahol az önálló ESG (L2 VPN-ügyfél) telepítve van

| **Elem** | **Érték** |
|------------|-----------------|
| Hálózati név | MGMT_NET_VLAN469 |
| Vlan | 469 |
| Cidr| 10.250.0.0/24 |
| Önálló Edge készülék IP-címe | 10.250.0.111 |
| Önálló Edge készülék NAT IP-címe | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>A helyszíni hálózat kinyújtandó

| **Elem** | **Érték** |
|------------|-----------------|
| Vlan | 472 |
| Cidr| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>Privát felhőalapú IP-séma Az NSX-T Tier0 útválasztóhoz (L2 VPN-szolgáltatás)

| **Elem** | **Érték** |
|------------|-----------------|
| Visszacsatolási felület | 192.168.254.254/32 |
| Alagút kapcsolódási pontja | 5.5.5.1/29 |
| Logikai kapcsoló (feszített) | Stretch_LS |
| Visszacsatolási illesztő (NAT IP-cím) | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>A magánfelhő-hálózat leképezése a kifeszített hálózatra

| **Elem** | **Érték** |
|------------|-----------------|
| Vlan | 712 |
| Cidr| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>Az L2VPN-hez szükséges logikai útválasztó-azonosító beolvasása

A következő lépések bemutatják, hogyan lehet lekérni az IPsec és l2VPN szolgáltatások 0. A logikai-útválasztó azonosítója később szükséges az L2VPN megvalósításakor.

1. Jelentkezzen be az NSX-T `https://*nsx-t-manager-ip-address*` kezelőbe, és válassza a **Hálózati** > **útválasztók** > **szolgáltató-LR** > **áttekintése lehetőséget.** **Magas rendelkezésre állás módban**válassza az **Aktív-Készenlét**lehetőséget. Ez a művelet megnyit egy előugró ablakot, amely azt az edge virtuális gépet jeleníti meg, amelyen a Tier0 útválasztó jelenleg aktív.

    ![Aktív készenléti állapot kiválasztása](media/l2vpn-fetch01.png)

2. Válassza **a Szövetcsomópontok** > **szélei** > **lehetőséget.** Jegyezze fel az előző lépésben azonosított aktív edge virtuális gép (Edge VM1) felügyeleti IP-címét.

    ![Megjegyzéskezelés IP-címe](media/l2vpn-fetch02.png)

3. Nyisson meg egy SSH-munkamenetet az Edge virtuális gép felügyeleti IP-címéhez. Futtassa a ```get logical-router``` parancsot felhasználónév **admin** és jelszó **CloudSimple 123!**.

    ![logikai-útválasztó kimenet bekése](media/l2vpn-fetch03.png)

4. Ha nem látja a "DR-Provider-LR" bejegyzést, hajtsa végre az alábbi lépéseket.

5. Hozzon létre két átfedés-háttérített logikai kapcsolót. Egy logikai kapcsoló van nyújtva a helyszíni, ahol az áttelepített számítási feladatok találhatók. Egy másik logikus kapcsoló egy dummy kapcsoló. További információt a [Logikai kapcsoló létrehozása](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html) a VMware dokumentációjában című témakörben talál.

    ![Logikai kapcsoló létrehozása](media/l2vpn-fetch04.png)

6. Csatlakoztassa a dummy kapcsolót a Tier1 útválasztóhoz egy helyi IP-címmel vagy a helyszíni vagy a magánfelhőből származó, nem átfedő alhálózattal. Lásd: [Downlink port hozzáadása 1.](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html)

    ![Dummy kapcsoló csatlakoztatása](media/l2vpn-fetch05.png)

7. Futtassa újra a `get logical-router` parancsot az Edge virtuális gép SSH munkamenetében. Megjelenik a "DR-Provider-LR" logikai útválasztó UUID azonosítója. Jegyezze fel az UUID-t, amely az L2VPN konfigurálásakor szükséges.

    ![logikai-útválasztó kimenet bekése](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>Az L2VPN-hez szükséges logikai kapcsoló azonosító jának beolvasása

1. Jelentkezzen be az [NSX-T kezelőbe.](https://nsx-t-manager-ip-address)
2. Válassza **a Hálózati** > **kapcsolóváltás** > ** \><\Logikai****Switches**  >  kapcsoló > **– áttekintés lehetőséget.**
3. Jegyezze fel a stretch logikai kapcsoló UUID azonosítóját, amely az L2VPN konfigurálásakor szükséges.

    ![logikai-útválasztó kimenet bekése](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>Útválasztás és biztonsági szempontok az L2VPN-hez

Az NSX-T Tier0 útválasztó és az önálló NSX Edge-ügyfél közötti IPsec útvonalalapú VPN létrehozásához az NSX-T Tier0 útválasztó visszacsatolási felületének képesnek kell lennie az 500/4500-as SZÁMÚ NSX-ügyfél nyilvános IP-címével való kommunikációra.

### <a name="allow-udp-5004500-for-ipsec"></a>UdP 500/4500 engedélyezése IPsec esetén

1. [Hozzon létre egy nyilvános IP-címet](public-ips.md) az NSX-T Tier0 visszacsatolási felülethez a CloudSimple portálon.

2. [Hozzon létre egy tűzfaltáblát](firewall.md) állapotalapú szabályokkal, amelyek lehetővé teszik az UDP 500/ 4500 bejövő forgalmat, és csatolja a tűzfaltáblát az NSX-T HostTransport alhálózathoz.

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>A visszacsatolási kapcsolat IP-címének hirdetése az alávetítési hálózatra

1. Null útvonal létrehozása a visszacsatolási illesztőhálózathoz. Jelentkezzen be az NSX-T-kezelőbe, és válassza **a Hálózati** > **útválasztási** > **útválasztók** > **szolgáltató-LR** > **Útválasztási** > **statikus útvonalak lehetőséget.** Kattintson a **Hozzáadás** parancsra. A **Hálózat**mezőbe írja be a visszacsatolási csatoló IP-címét. A **Next Hops**esetében kattintson a **Hozzáadás**gombra, adja meg a "Null" értéket a következő ugráshoz, és tartsa meg az alapértelmezett 1 értéket a Felügyeleti távolság mezőben.

    ![Statikus útvonal hozzáadása](media/l2vpn-routing-security01.png)

2. IP-előtaglista létrehozása. Jelentkezzen be az NSX-T-kezelőbe, és válassza **a Hálózati** > **útválasztási** > **útválasztók** > **szolgáltató-LR** > **útválasztási** > **IP-előtaglistáit.** Kattintson a **Hozzáadás** parancsra. Adjon meg egy nevet a lista azonosításához. Az **előtagok**esetében kattintson a **Hozzáadás** kétszer gombra. Az első sorban írja be a "0.0.0.0/0" értéket a **Hálózat** és a "Megtagadás" értéket a **Művelet**mezőbe. A második sorban válassza a **Bármely** lehetőséget a **Hálózat** és a Cselekvési **Engedély** **beállításhoz**lehetőséget.
3. Az IP-előtaglista csatolása mindkét BGP-szomszédhoz (TOR). Ha az IP-előtaglistát a BGP-szomszédhoz csatolja, akkor az alapértelmezett útvonal a BGP-ben a TOR-kapcsolókhoz nem lesz meghirdetve. Azonban minden más útvonal, amely tartalmazza a null útvonalat, meghirdeti a visszacsatolási kapcsolat IP-címét a TOR kapcsolóknak.

    ![IP-előtaglista létrehozása](media/l2vpn-routing-security02.png)

4. Jelentkezzen be az NSX-T-kezelőbe, és válassza **a Hálózati** > **útválasztási** > **útválasztók** > **szolgáltató-LR** > **Routing** > **BGP** > szomszédok**lehetőséget.** Válassza ki az első szomszédot. Kattintson a Címcsaládok **szerkesztése** > **gombra.** Az IPv4-család esetében adja meg a **Kiszűrő oszlopot,** és jelölje ki a létrehozott IP-előtaglistát. Kattintson a **Save** (Mentés) gombra. Ismételje meg ezt a lépést a második szomszéd.

    ![IP-előtaglista](media/l2vpn-routing-security03.png) ![csatolása 1 IP-előtaglista csatolása 2](media/l2vpn-routing-security04.png)

5. A null statikus útvonal újraelosztása a BGP-ben. A visszacsatolási kapcsolat útvonalának az alávetítéshez való hirdetéséhez újra el kell osztania a null statikus útvonalat a BGP-ben. Jelentkezzen be az NSX-T-kezelőbe, és válassza **a Hálózati** > **útválasztási** > **útválasztók** > **szolgáltató-LR** > **Útválasztási** > útvonal**újraelosztási** > szomszédok**lehetőséget.** Válassza **a Provider-LR-Route_Redistribution lehetőséget,** majd kattintson **a Szerkesztés gombra.** Jelölje be a **Statikus** jelölőnégyzetet, és kattintson a **Mentés gombra.**

    ![Null statikus útvonal újraelosztása a BGP-ben](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>Útvonalalapú VPN konfigurálása az NSX-T Tier0 útválasztón

Az alábbi sablon segítségével töltse ki az összes részletet az útvonalalapú VPN konfigurálásához az NSX-T Tier0 útválasztón. Az UUID-k minden POST hívás szükséges a következő POST hívások. Az L2VPN visszacsatolási és bújtatási összeköttetéseinek IP-címei egyediek lehetnek, és nem lehetnek átfedésben a helyszíni vagy a magánfelhő-hálózatokkal.

Az L2VPN-hez használt visszacsatolási és bújtatási összeköttetéshez választott IP-címeknek egyedinek kell lenniük, és nem lehetnek átfedésben a helyszíni vagy a magánfelhő-hálózatokkal. A visszacsatolási illesztőhálózatnak mindig /32-nek kell lennie.

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section "Steps to fetch Logical-Router ID needed for L2VPN"
Logical-switch ID(Stretch) : UUID of Stretch Logical Switch obtained earlier
IPSec Service ID :
IKE profile ID :
DPD profile ID :
Tunnel Profile ID :
Local-endpoint ID :
Peer end-point ID :
IPSec VPN session ID (route-based) :
L2VPN service ID :
L2VPN session ID :
Logical-Port ID :
Peer Code :
```

Az alábbi API-hívások mindegyikénál cserélje le az IP-címet az NSX-T-Kezelő IP-címére. Ezeket az API-hívásokat futtathatja a `curl` POSTMAN ügyféltől vagy parancsok használatával.

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>Az IPSec VPN-szolgáltatás engedélyezése a logikai útválasztón

```
POST   https://192.168.110.201/api/v1/vpn/ipsec/services/
{
"resource_type": "IPSecVPNService",
"description": "Manage VPN service",
"display_name": "IPSec VPN service",
"logical_router_id": "Logical-router ID",
"ike_log_level": "INFO",
"enabled": true
}
```

### <a name="create-profiles-ike"></a>Profilok létrehozása: IKE

```
POST https://192.168.110.201/api/v1/vpn/ipsec/ike-profiles
 
{
"resource_type": "IPSecVPNIKEProfile",
"description": "IKEProfile for siteA",
"display_name": "IKEProfile siteA",
"encryption_algorithms": ["AES_128"],
"ike_version": "IKE_V2",
"digest_algorithms": ["SHA2_256"],
"sa_life_time":21600,
"dh_groups": ["GROUP14"]
}
```

### <a name="create-profiles-dpd"></a>Profilok létrehozása: DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>Profilok létrehozása: Alagút

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/tunnel-profiles
 
{
"resource_type": "IPSecVPNTunnelProfile",
"display_name": "nsx-default-tunnel-profile",
"enable_perfect_forward_secrecy": true,
"encryption_algorithms": ["AES_GCM_128"],
"digest_algorithms": [],
"sa_life_time":3600,
"dh_groups": ["GROUP14"],
"encapsulation_mode": "TUNNEL_MODE",
"transform_protocol": "ESP",
"df_policy": "COPY"
}
```

### <a name="create-a-local-endpoint"></a>Helyi végpont létrehozása

``` 
POST https://192.168.110.201/api/v1/vpn/ipsec/local-endpoints
 
{
"resource_type": "IPSecVPNLocalEndpoint",
"description": "Local endpoint",
"display_name": "Local endpoint",
"local_id": "<Public IP of Loopback interface>",
"ipsec_vpn_service_id": {
"target_id": "IPSec VPN service ID"},
"local_address": "<IP of Loopback interface>",
"trust_ca_ids": [],
"trust_crl_ids": []
}
```

### <a name="create-a-peer-endpoint"></a>Társvégpont létrehozása

```
POST https://192.168.110.201/api/v1/vpn/ipsec/peer-endpoints

{
"resource_type": "IPSecVPNPeerEndpoint",
"description": "Peer endpoint for site B",
"display_name": "Peer endpoint for site B",
"connection_initiation_mode": "INITIATOR",
"authentication_mode": "PSK",
"ipsec_tunnel_profile_id": "IPSec Tunnel profile ID",
"dpd_profile_id": "DPD profile ID",
"psk":"nsx",
"ike_profile_id": "IKE profile ID",
"peer_address": "<Public IP of Standalone client",
"peer_id": "<Public IP of Standalone client>"
}
```

### <a name="create-a-route-based-vpn-session"></a>Útvonalalapú VPN-munkamenet létrehozása

```
POST :  https://192.168.110.201/api/v1/vpn/ipsec/sessions
 
{
"resource_type": "RouteBasedIPSecVPNSession",
"peer_endpoint_id": "Peer Endpoint ID",
"ipsec_vpn_service_id": "IPSec VPN service ID",
"local_endpoint_id": "Local Endpoint ID",
"enabled": true,
"tunnel_ports": [
{
"ip_subnets": [
{
"ip_addresses": [
 "5.5.5.1"
],
"prefix_length": 24
}
  ]
}
]
}
```

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>Az L2VPN konfigurálása NSX-T Tier0 útválasztón

Minden POST-hívás után töltse ki a következő információkat. A későbbi POST-hívásokban az azonosítók szükségesek.

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>Az L2VPN szolgáltatás létrehozása

A következő GET parancs kimenete üres lesz, mert a konfiguráció még nem fejeződött be.

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

A következő POST parancs esetében a logikai útválasztó azonosítója a korábban beszerzett Tier0 DR logikai útválasztó UUID azonosítója.

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>Az L2VPN munkamenet létrehozása

A következő POST parancs esetében az L2VPN szolgáltatás azonosítója az id, amelyet most kapott, és az IPsec VPN-munkamenet-azonosító az előző szakaszban kapott azonosító.

```    
POST: https://192.168.110.201/api/v1/vpn/l2vpn/sessions

{
"l2vpn_service_id" : "L2VPN service ID",
"transport_tunnels" : [
    {
    "target_id" : "IPSec VPN session ID"
    }]
}
```

Ezek a hívások egy GRE-alagút végpontot hoznak létre. Az állapot ellenőrzéséhez futtassa a következő parancsot.

```
edge-2> get tunnel-port
Tunnel      : 44648dae-8566-5bc9-a065-b1c4e5c3e03f
IFUID       : 328
LOCAL       : 169.254.64.1
REMOTE      : 169.254.64.2
ENCAP       : GRE

Tunnel      : cf950ca1-5cf8-5438-9b1a-d2c8c8e7229b
IFUID       : 318
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.152
ENCAP       : GENEVE

Tunnel      : 63639321-87c5-529e-8a61-92c1939799b2
IFUID       : 304
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.156
ENCAP       : GENEVE
```

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>Logikai port létrehozása a megadott alagútazonosítóval

```
    POST https://192.168.110.201/api/v1/logical-ports/

{
"resource_type": "LogicalPort",
"display_name": "Extend logicalSwitch, port for service",
"logical_switch_id": "Logical switch ID",
"admin_state" : "UP",
"attachment": {
"attachment_type":"L2VPN_SESSION",
"id":"L2VPN session ID",
"context" : {
"resource_type" : "L2VpnAttachmentContext",
    "tunnel_id" : 10
}
    }
        }
```

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>Szerezze be az L2VPN társkódját az NSX-T oldalon

Szerezze be az NSX-T végpont társkódját. A társkód szükséges a távoli végpont konfigurálásakor. Az L2VPN <session-id> az előző szakaszból szerezhető be. További információt az [NSX-T 2.3 API Guide című témakörben talál.](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html)

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>Az NSX-T önálló ügyfél telepítése (helyszíni)

A telepítés előtt ellenőrizze, hogy a helyszíni tűzfalszabályok engedélyezik-e a bejövő és kimenő UDP 500/4500 forgalmat/a CloudSimple nyilvános IP-címére, amely korábban az NSX-T0 útválasztó visszacsatolási összeköttetéséhez volt fenntartva. 

1. [Az önálló NSX Edge-ügyfél letöltése](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230) OVF és a fájlok kibontása a letöltött csomagból egy mappába.

    ![Önálló NSX Edge-ügyfél letöltése](media/l2vpn-deploy-client01.png)

2. Nyissa meg az összes kibontott fájlt tartalmazó mappát. Válassza ki az összes vmdks (NSX-l2t-client-large.mf és NSX-l2t-client-large.ovf a nagy készülék mérete vagy NSX-l2t-client-Xlarge.mf és NSX-l2t-client-Xlarge.ovf extra nagy méretű készülék mérete). Kattintson a **Tovább** gombra.

    ![Sablon](media/l2vpn-deploy-client02.png) ![kiválasztása Sablon kijelölése](media/l2vpn-deploy-client03.png)

3. Adja meg az NSX-T önálló ügyfél nevét, és kattintson a **Tovább**gombra.

    ![Sablon nevének megadása](media/l2vpn-deploy-client04.png)

4. Kattintson a **Tovább** gombra az adattár beállításainak eléréséhez. Válassza ki a megfelelő adattanyájt az NSX-T önálló ügyfélhez, és kattintson a **Tovább**gombra.

    ![Adattár kijelölése](media/l2vpn-deploy-client06.png)

5. Válassza ki a megfelelő portcsoportokat trunk (Trunk PG), Public (Uplink PG) és HA felület (Uplink PG) az NSX-T önálló ügyfél. Kattintson a **Tovább** gombra.

    ![Portcsoportok kijelölése](media/l2vpn-deploy-client07.png)

6. Töltse ki az alábbi részleteket a **Sablon testreszabása** képernyőn, és kattintson a **Tovább**gombra:

    L2T kibontása:

    * **Társcíme**. Adja meg az Azure CloudSimple portálon az NSX-T Tier0 visszacsatolási felülethez fenntartott IP-címet.
    * **Társkód .** Illessze be az L2VPN-kiszolgáló telepítésének utolsó lépéséből beszerzett társkódot.
    * **Sub interfaces VLAN (Tunnel ID)**. Adja meg a megnyújtandó VLAN-azonosítót. Zárójelben (írja be a korábban konfigurált bújtatási azonosítót).

    A kapcsolati kapcsolat felületének kibontása:

    * **DNS IP-cím**. Adja meg a helyszíni DNS-IP-címet.
    * **Alapértelmezett átjáró**.  Adja meg a VLAN alapértelmezett átjáróját, amely az ügyfél alapértelmezett átjárójaként fog működni.
    * **IP-cím**. Adja meg az önálló ügyfél kapcsolati IP-címét.
    * **Előtag hossza**. Adja meg az uplink VLAN/alhálózat előtaghosszát.
    * **CLI admin / enable / root felhasználói jelszó**. Állítsa be a jelszót admin /enable /root account.

      ![Sablon](media/l2vpn-deploy-client08.png)
      ![testreszabása Sablon testreszabása – további](media/l2vpn-deploy-client09.png)

7. Tekintse át a beállításokat, és kattintson a **Befejezés gombra.**

    ![Teljes konfiguráció](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>Helyszíni fogadóport konfigurálása

Ha az egyik VPN-hely nem rendelkezik NSX-el, konfigurálhat egy L2 VPN-t egy önálló NSX Edge telepítésével az adott helyen. Az önálló NSX Edge egy OVF-fájl használatával van telepítve egy olyan állomáson, amelyet nem az NSX kezel. Ez egy NSX Edge Services Gateway-berendezést telepít, amely L2 VPN-ügyfélként működik.

Ha egy önálló él törzs vNIC csatlakozik egy vSphere elosztott kapcsoló, vagy válogatás nélkül mód, vagy egy mosogató port van szükség az L2 VPN-funkció. A válogatásos mód használata ismétlődő pingeket és ismétlődő válaszokat okozhat. Ezért használja a fogadóport módot az L2 VPN önálló NSX Edge konfigurációjában. Tekintse meg a [Fogadóport konfigurálása](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html) a VMware dokumentációban.

## <a name="ipsec-vpn-and-l2vpn-verification"></a>IPsec VPN és L2VPN-ellenőrzés

A következő parancsokkal ellenőrizheti az IPsec és l2VPN munkameneteket az önálló NSX-T Edge-ből.

```
nsx-l2t-edge> show service ipsec
-----------------------------------------------------------------------
vShield Edge IPSec Service Status:
IPSec Server is running.
AESNI is enabled.
Total Sites: 1, 1 UP, 0 Down
Total Tunnels: 1, 1 UP, 0 Down
----------------------------------
Site:  10.250.0.111_0.0.0.0/0-104.40.21.81_0.0.0.0/0
Channel: PeerIp: 104.40.21.81    LocalIP: 10.250.0.111  Version: IKEv2  Status: UP
Tunnel: PeerSubnet: 0.0.0.0/0    LocalSubnet: 0.0.0.0/0   Status: UP
----------------------------------
```

```
nsx-l2t-edge> show service l2vpn
L2 VPN is running
----------------------------------------
L2 VPN type: Client/Spoke

SITENAME                       IPSECSTATUS          VTI                  GRE
1ecb00fb-a538-4740-b788-c9049e8cb6c6 UP                   vti-100              l2t-1
```

Az alábbi parancsokkal ellenőrizheti az NSX-T Tier0 útválasztó IPsec és L2VPN munkameneteit.

```
edge-2> get ipsecvpn session
Total Number of Sessions: 1

IKE Session ID : 3
UUID           : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Type           : Route

Local IP       : 192.168.254.254      Peer IP        : 192.227.85.167
Local ID       : 104.40.21.81         Peer ID        : 192.227.85.167
Session Status : Up

Policy Rules
    VTI UUID       : 4bf96e3b-e50b-49cc-a16e-43a6390e3d53
    ToRule ID      : 560874406            FromRule ID    : 2708358054
    Local Subnet   : 0.0.0.0/0            Peer Subnet    : 0.0.0.0/0
    Tunnel Status  : Up
```

```
edge-2> get l2vpn session
Session       : f7147137-5dd0-47fe-9e53-fdc2b687b160
Tunnel        : b026095b-98c8-5932-96ff-dda922ffe316
IPSEC Session : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Status        : UP
```

A következő parancsokkal ellenőrizze a fogadóportot az ESXi gazdagépen, ahol az NSX-T önálló ügyfél virtuális gép a helyszíni környezetben található.

```
 [root@esxi02:~] esxcfg-vswitch -l |grep NSX
  53                  1           NSXT-client-large.eth2
  225                1           NSXT-client-large.eth1
  52                  1           NSXT-client-large.eth0
```

```
[root@esxi02:~] net-dvs -l | grep "port\ [0-9]\|SINK\|com.vmware.common.alias"
                com.vmware.common.alias = csmlab2DS ,   propType = CONFIG
        port 24:
        port 25:
        port 26:
        port 27:
        port 13:
        port 19:
        port 169:
        port 54:
        port 110:
        port 6:
        port 107:
        port 4:
        port 199:
        port 168:
        port 201:
        port 0:
        port 49:
        port 53:
        port 225:
                com.vmware.etherswitch.port.extraEthFRP =   SINK
        port 52:
```
