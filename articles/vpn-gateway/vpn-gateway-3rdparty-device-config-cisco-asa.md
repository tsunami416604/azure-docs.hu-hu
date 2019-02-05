---
title: Cisco ASA-eszközök csatlakoztatása az Azure VPN-átjárók mintakonfiguráció |} A Microsoft Docs
description: Ez a cikk a Cisco ASA-eszközök csatlakoztatása az Azure VPN-átjárók egy példa konfigurációja.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/19/2018
ms.author: yushwang
ms.openlocfilehash: f51a97e1493803998cfbdd81523e4e479b50346d
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55697105"
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Minta-konfiguráció: Minta: Cisco ASA-eszköz (IKEv2/nincs BGP)
Ez a cikk az Azure VPN Gateway átjárókhoz kapcsolódó Cisco adaptív biztonsági berendezés (ASA) eszközöknél minta konfigurációi. A példa az IKEv2 nélkül a Border Gateway Protocol (BGP) futtató Cisco ASA-eszközökre vonatkozik. 

## <a name="device-at-a-glance"></a>Egyetlen pillantással eszköz

|                        |                                   |
| ---                    | ---                               |
| Eszköz gyártója          | Cisco                             |
| Eszközmodell           | ASA                               |
| Célverzió         | 8.4 és újabb verziók                     |
| A tesztelt modell           | ASA 5505                          |
| A tesztelt verzió         | 9.2                               |
| IKE verziószám            | IKEv2                             |
| BGP                    | Nem                                |
| Az Azure VPN gateway típusa | Útvonalalapú VPN-átjáró           |
|                        |                                   |

> [!NOTE]
> A minta konfigurációs a Cisco ASA-eszköz csatlakozik az Azure-beli **útvonalalapú** VPN-átjárót. A kapcsolat használja az egyéni IPsec/IKE-házirendet a **UsePolicyBasedTrafficSelectors** paraméterrel, ahogyan a [Ez a cikk](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> A minta a ASA eszközök használatát igényli a **IKEv2** access-lista-alapú konfigurációk esetén a szabályzat nem VTI-alapú. Tekintse meg a VPN szállítói műszaki ellenőrizze, hogy az IKEv2, a helyszíni VPN-eszközök az szabályzat támogatott.


## <a name="vpn-device-requirements"></a>VPN-eszközkövetelmények
Az Azure VPN-átjárók IPsec/IKE protokoll a standard csomagok használatával Site-to-Site (S2S) VPN-alagutakat. A részletes IPsec/IKE protokoll-paraméterek és titkosítási algoritmusok alapértelmezett Azure VPN gatewayek: [tudnivalók a VPN-eszközök](vpn-gateway-about-vpn-devices.md).

> [!NOTE]
> Opcionálisan megadhat egy titkosítási algoritmusokat és kulcserősségeket egy adott kapcsolathoz tartozó pontos kombinációja a leírtak szerint [információ a kriptográfiai követelményekről](vpn-gateway-about-compliance-crypto.md). Ha megadja a pontos kombinációja algoritmusokkal és kulcserősségekkel, mindenképpen a VPN-eszközök használata a vonatkozó előírásokat.

## <a name="single-vpn-tunnel"></a>Egyetlen VPN-alagút
Ez a konfiguráció az Azure VPN gateway és a egy helyszíni VPN-eszköz között egyetlen S2S VPN-alagút áll. Igény szerint konfigurálható a [a VPN-alagúton keresztül BGP](#bgp).

![Egyetlen S2S VPN-alagút](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Az Azure-konfigurációk létrehozásához részletes utasításokért lásd: [egyetlen VPN-alagút telepítő](vpn-gateway-3rdparty-device-config-overview.md#singletunnel).

### <a name="virtual-network-and-vpn-gateway-information"></a>Virtuális hálózat és VPN-átjáró adatait
Ez a szakasz felsorolja a minta a paramétereket.

| **A paraméter**                | **Érték**                    |
| ---                          | ---                          |
| Virtuális hálózat címelőtagjainak        | 10.11.0.0/16<br>10.12.0.0/16 |
| Az Azure VPN gateway-IP         | Azure_Gateway_Public_IP      |
| A helyszíni címelőtagokat | 10.51.0.0/16<br>10.52.0.0/16 |
| A helyszíni VPN-eszköz IP    | OnPrem_Device_Public_IP     |
| * A virtuális hálózat BGP ASN-je                | 65010                        |
| * Az azure BGP társ IP-Címét           | 10.12.255.30                 |
| * A helyszíni BGP ASN-je         | 65050                        |
| * A helyszíni BGP-Társgép IP     | 10.52.255.254                |
|                              |                              |

\* Nem kötelező paraméter a BGP csak.

### <a name="ipsecike-policy-and-parameters"></a>IPsec/IKE-szabályzat és a paraméterek
A következő táblázat felsorolja az IPsec/IKE-algoritmusokat és paramétereket, a minta. Tekintse meg a VPN-eszköz specifikációja ellenőrzése az algoritmusokat a VPN-eszköz modelleket és belsővezérlőprogram-verziói által támogatott.

| **IPsec/IKEv2**  | **Érték**                            |
| ---              | ---                                  |
| IKEv2-titkosítás | AES256                               |
| IKEv2-integritás  | SHA384                               |
| DH-csoport         | DHGroup24                            |
| * IPsec-titkosítás | AES256                               |
| * IPsec-integritás  | SHA1                                 |
| PFS-csoport        | PFS24                                |
| Gyorsmódú biztonsági társítás élettartama   | a 7200 másodperc                         |
| Forgalomválasztó | UsePolicyBasedTrafficSelectors $True |
| Előre megosztott kulcs   | PreSharedKey                         |
|                  |                                      |

\* Néhány eszközön IPsec-integritás kell lennie null értékű, ha az IPsec titkosítási algoritmus az AES-GCM.

### <a name="asa-device-support"></a>ASA-eszköz támogatása

* Az IKEv2 támogatása ASA 8.4 és újabb verzió szükséges.

* DH-csoport és a PFS-csoport meghaladja a csoport 5 támogatásához szükséges ASA verzió 9.x.

* AES-GCM-IPsec-titkosítás és az SHA-256, SHA-384 és SHA-512 IPsec-integritás támogatása, ASA verzióra van szükség 9.x. A támogatási követelmények újabb ASA-eszközökre vonatkozik. A közzététel időpontjában 5505, 5510, 5520, 5540, 5550 és 5580 ASA modellek nem támogatják ezeket az algoritmusokat. Tekintse meg a VPN-eszköz specifikációja ellenőrzése az algoritmusokat a VPN-eszköz modelleket és belsővezérlőprogram-verziói által támogatott.


### <a name="sample-device-configuration"></a>Mintául szolgáló eszközök konfigurálása
A szkript egy minta alapján a konfiguráció és a korábbi szakaszokban ismertetett paramétereket biztosít. Az S2S VPN-alagút konfigurálására a következő részekből áll:

1. Felületek és útvonalak
2. Hozzáférés-listák
3. IKE-szabályzat és a paraméterek (1. fázis vagy alapmódú)
4. IPSec-házirend és (2. fázis vagy gyors mód) paraméterei
5. További paraméterek, például TCP MSS korlátozását.

> [!IMPORTANT]
> A minta parancsfájl használata előtt, kövesse az alábbi lépéseket. A parancsfájl a helyőrző értékeket cserélje le a konfigurációs eszköz beállításait.

* Adja meg a hálózatiadapter-konfigurációja, az adapterek kívül és belül is.
* Azonosítsa az útvonalakat a belső vagy privát és a külső/nyilvános hálózatok számára.
* Győződjön meg arról, neveket és a házirend számok egyediek-e az eszközön.
* Győződjön meg arról, hogy a titkosítási algoritmusok támogatottak-e az eszközön.
* Cserélje le a következő **helyőrzőértékeket** a konfigurációra vonatkozó tényleges értékekre:
  - Kapcsolat neve kívül: **kívül**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - IKE: **Pre_Shared_Key**
  - Virtuális hálózat és a helyi hálózati átjáró neve: **VNetName** és **LNGName**
  - Virtuális hálózat és a helyszíni hálózati cím **előtagok**
  - Megfelelő **netmasks**

#### <a name="sample-script"></a>Példaszkript

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
!     > Set access list & traffic selectors, PFS, IPsec proposal, SA lifetime
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

## <a name="simple-debugging-commands"></a>Egyszerű hibakeresési parancs

Hibakeresési célra használja a következő ASA-parancsok:

* Az IPsec vagy az IKE biztonsági társítás (SA) megjelenítése:
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* Adja meg a hibakeresési mód:
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    A `debug` parancsokkal hozhat létre jelentős kimenetet a konzolon.

* A jelen konfigurációkat megjelenítése az eszközön:
    ```
    show run
    ```
    Használat `show` alparancs lista az eszköz konfigurálása meghatározott részeit, például:
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>További lépések
Aktív-aktív létesítmények közötti és VNet – VNet kapcsolatokhoz beállítása: [aktív-aktív VPN-átjárók konfigurálása](vpn-gateway-activeactive-rm-powershell.md).
