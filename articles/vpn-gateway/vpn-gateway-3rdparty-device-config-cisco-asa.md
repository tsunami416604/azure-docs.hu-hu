---
title: "Cisco ASA eszköz csatlakozik az Azure VPN gatewayek mintakonfiguráció |} Microsoft Docs"
description: "Ez a cikk a Cisco ASA eszköz csatlakozik az Azure VPN gatewayek minta konfigurációját tartalmazza."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.openlocfilehash: fbe22b70b4fe3463ffc7b0e9a7ebd683f681117d
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Mintakonfiguráció: Cisco ASA (IKEv2/nem BGP-) eszköz
Ez a cikk ismerteti az Azure VPN gatewayek csatlakozó Cisco adaptív biztonsági készülék (ASA) eszközök minta konfigurációi. A példa IKEv2 nélkül, a Border Gateway Protocol (BGP) futtató Cisco ASA-eszközökre vonatkozik. 

## <a name="device-at-a-glance"></a>Egy pillanat alatt eszköz

|                        |                                   |
| ---                    | ---                               |
| Eszköz gyártója          | Cisco                             |
| Eszközmodell           | ASA                               |
| Célverzió         | 8.4 és újabb verziók                     |
| Tesztelt modellt           | 5505 ASA                          |
| Tesztelt verziója         | 9.2                               |
| IKE-verzió            | IKEv2                             |
| BGP                    | Nem                                |
| Az Azure VPN-átjáró típusa | Útvonalalapú VPN-átjáró           |
|                        |                                   |

> [!NOTE]
> A minta konfigurációs Cisco ASA eszköz csatlakozik az Azure **útválasztó-alapú** VPN-átjáró. A kapcsolat használja az IPsec/IKE egyéni házirendet a **UsePolicyBasedTrafficSelectors** beállítást, a [Ez a cikk](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> Az ASA eszközöket használó igényel a **IKEv2** hozzáférési lista-alapú konfigurációk esetén a szabályzat nem VTI-alapú. Tekintse meg a VPN szállító műszaki ellenőrizze, hogy a házirend a helyszíni VPN-eszközök támogatott IKEv2.


## <a name="vpn-device-requirements"></a>VPN-eszköz követelményei
Az Azure VPN gatewayek a szabványos IPsec/IKE protokoll csomagok használatával szeretne létrehozni a helyek (közötti S2S) VPN-alagutat. A részletes IPsec/IKE protokoll paraméterek és az Azure VPN gatewayek alapértelmezett a titkosítási algoritmusok: [kapcsolatos VPN-eszközök](vpn-gateway-about-vpn-devices.md).

> [!NOTE]
> Opcionálisan megadhat egy titkosítási algoritmusok és egy adott kapcsolat, a kulcs szintjeiről pontos kombinációja a leírtak szerint [titkosítási követelményekkel kapcsolatos](vpn-gateway-about-compliance-crypto.md). Ha megad egy algoritmusok és a kulcs szintjeiről pontos kombinációja, ügyeljen arra, hogy a VPN-eszközök használata a vonatkozó előírásokat.

## <a name="single-vpn-tunnel"></a>Egy VPN-alagút
Ez a konfiguráció áll az Azure VPN gateway és egy helyszíni VPN-eszköz között egyetlen S2S VPN-alagúton. Konfigurálhatja a [a VPN-alagúton keresztül BGP](#bgp).

![Egyetlen S2S VPN-alagút](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Az Azure-konfigurációjának létrehozásához lépésenkénti útmutatót lásd: [egyetlen VPN-alagút beállítást](vpn-gateway-3rdparty-device-config-overview.md#singletunnel).

### <a name="virtual-network-and-vpn-gateway-information"></a>Virtuális hálózat és a VPN-átjáró adatait
Ez a rész felsorolja a minta paramétereit.

| **Parameter**                | **Érték**                    |
| ---                          | ---                          |
| Virtuális hálózati címelőtagokat        | 10.11.0.0/16<br>10.12.0.0/16 |
| Az Azure VPN gateway IP-címet         | Azure_Gateway_Public_IP      |
| A helyszíni címelőtagokat | 10.51.0.0/16<br>10.52.0.0/16 |
| A helyszíni VPN-eszköz IP-címet    | OnPrem_Device_Public_IP     |
| * A virtuális hálózati BGP ASN                | 65010                        |
| * Az azure BGP-Társgép IP-           | 10.12.255.30                 |
| * A helyszíni BGP ASN         | 65050                        |
| * A helyszíni BGP-Társgép IP-     | 10.52.255.254                |
|                              |                              |

\*Nem kötelező paraméter BGP csak.

### <a name="ipsecike-policy-and-parameters"></a>IPsec-/ h.rend és paraméterek
A következő táblázat az IPsec vagy internetes KULCSCSERE-algoritmusok és a mintában használt paraméterek. Tekintse meg a VPN-eszköz specifikációja ellenőrzése a algoritmust, amelyet a VPN-eszköz modellek és belsővezérlőprogram-verziók támogatottak.

| **IPsec/IKEv2**  | **Érték**                            |
| ---              | ---                                  |
| IKEv2-titkosítás | AES256                               |
| IKEv2-integritás  | SHA384                               |
| DH-csoport         | DHGroup24                            |
| * IPsec Encryption | AES256                               |
| * IPSec-integritásának  | SHA1                                 |
| PFS-csoport        | PFS24                                |
| Gyorsmódú biztonsági társítás élettartama   | a 7200 másodperc                         |
| Forgalomválasztó | UsePolicyBasedTrafficSelectors $True |
| Előre megosztott kulcs   | PreSharedKey                         |
|                  |                                      |

\*Egyes eszközökön IPSec-integritásának kell lennie null értékű, ha IPsec titkosítási algoritmus az AES-GCM.

### <a name="asa-device-support"></a>ASA eszköz támogatása

* Az IKEv2 támogatása ASA 8.4 és újabb verzió szükséges.

* DH-csoport és a PFS-csoport meghaladja a csoport 5 támogatása ASA verziója szükséges 9.x.

* IPsec-titkosítást az AES-GCM-mel és az SHA-256, SHA-384 és SHA-512 IPsec integritását, ASA verziója szükséges 9.x. Ez a támogatás követelmény újabb ASA-eszközökre vonatkozik. A kiadvány időpontjában 5505, 5510, 5520, 5540, 5550 és 5580 ASA modellek nem támogatják ezeket az algoritmusokat. Tekintse meg a VPN-eszköz specifikációja ellenőrzése a algoritmust, amelyet a VPN-eszköz modellek és belsővezérlőprogram-verziók támogatottak.


### <a name="sample-device-configuration"></a>A minta eszközök konfigurálása
A parancsfájl biztosít, amelyek konfigurációs és a korábbi szakaszokban ismertetett paraméterek alapján minta. Az S2S VPN-alagút konfiguráció a következő részből áll:

1. Felületek és útvonalak
2. Hozzáférés-listák
3. IKE-házirend és a paraméterek (1. fázis vagy alapmódú)
4. IPSec-házirend és a paraméterek (2. fázis vagy gyorsmódú)
5. Más paraméterek, például TCP MSS hántoló

> [!IMPORTANT]
> Kövesse az alábbi lépéseket, mielőtt a minta parancsfájlt. Cserélje le a helyőrző értékeket, a parancsfájl a konfigurációs eszköz beállításait.

* Adja meg a kapcsolat beállításait felületek kívül és belül is.
* Azonosítsa az útvonalakat a vállalaton belüli és személyes és a külső/nyilvános hálózatokhoz.
* Biztosítsa, hogy az összes nevét és házirend számok egyedi az eszközön.
* Győződjön meg arról, hogy a titkosítási algoritmusok támogatottak-e az eszközön.
* Cserélje le a következő **helyőrző értékeket** tényleges értékeket a konfigurációhoz:
  - A kapcsolat nevét kívül: **kívül**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - IKE: **Pre_Shared_Key**
  - Virtuális hálózati és helyi hálózati átjáró nevek: **VNetName** és **LNGName**
  - Virtuális hálózat és a helyszíni hálózati cím **előtagok**
  - Megfelelő **netmasks**

#### <a name="sample-script"></a>Mintaparancsfájl

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec protposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>Egyszerű hibakeresési parancsok

A következő ASA parancsokkal hibakeresési célra:

* Az IPsec vagy az internetes KULCSCSERE biztonsági társítás (SA) megjelenítése:
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* Adja meg a hibakeresési mód:
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    A `debug` parancsok hozhat létre a jelentős megjelenítése a konzolon.

* A jelen konfigurációkat megjelenítése az eszközön:
    ```
    show run
    ```
    Használjon `show` alparancs lista meghatározott részeire eszközkonfiguráció, például:
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>További lépések
Aktív-aktív létesítmények közötti és VNet – VNet kapcsolatokhoz konfigurálásához lásd: [aktív-aktív VPN-átjárók konfigurálása](vpn-gateway-activeactive-rm-powershell.md).
