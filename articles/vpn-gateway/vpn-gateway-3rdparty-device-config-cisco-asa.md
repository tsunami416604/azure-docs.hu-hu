---
title: Mintakonfiguráció a Cisco ASA-eszközök Azure VPN-átjárókhoz való csatlakoztatásához
description: Ez a cikk mintakonfigurációt biztosít a Cisco ASA-eszközök Azure-beli VPN-átjárókhoz való csatlakoztatásához.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/19/2018
ms.author: yushwang
ms.openlocfilehash: 96e5c26ea7b5f1baa33fd8830491ee3aa1e60221
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75778082"
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Mintakonfiguráció: Cisco ASA-eszköz (IKEv2/no BGP)
Ez a cikk mintakonfigurációk at cisco adaptive security appliance (ASA) eszközök csatlakoztatásához az Azure VPN-átjárók. A példa azIKEv2-t a Border Gateway Protocol (BGP) nélkül futó Cisco ASA-eszközökre vonatkozik. 

## <a name="device-at-a-glance"></a>Eszköz egy pillantással

|                        |                                   |
| ---                    | ---                               |
| Eszköz szállítója          | Cisco                             |
| Eszközmodell           | ASA                               |
| Célverzió         | 8.4 és újabb                     |
| Tesztelt modell           | ASA 5505                          |
| Tesztelt verzió         | 9.2                               |
| Internetes kulcscsere verziója            | IKEv2                             |
| BGP                    | Nem                                |
| Azure VPN-átjáró típusa | Útvonalalapú VPN-átjáró           |
|                        |                                   |

> [!NOTE]
> A mintakonfiguráció egy Cisco ASA-eszközt kapcsol össze egy **Azure-útvonalalapú** VPN-átjáróval. A kapcsolat egyéni IPsec/IKE-házirendet használ a **UsePolicyBasedTrafficSelectors** beállítással, a [jelen cikkben](vpn-gateway-connect-multiple-policybased-rm-ps.md)leírtak szerint.
>
> A minta megköveteli, hogy az ASA-eszközök az **IKEv2-házirendet** hozzáférésilista-alapú konfigurációkkal használják, ne VTI-alapúkonfigurációkkal. A VPN-eszköz gyártójának specifikációiból ellenőrizheti, hogy az IKEv2 házirend támogatott-e a helyszíni VPN-eszközökön.


## <a name="vpn-device-requirements"></a>A VPN-eszközökre vonatkozó követelmények
Az Azure VPN-átjárók a szabványos IPsec/IKE protokollcsomagok használatával hozzák létre a helyek közötti (S2S) VPN-alagutakat. Az IPsec/IKE protokoll részletes paramétereit és az Azure VPN-átjárók alapértelmezett kriptográfiai algoritmusait a [VPN-eszközök – ismertető](vpn-gateway-about-vpn-devices.md)című témakörben olvashat.

> [!NOTE]
> Megadhatja a kriptográfiai algoritmusok és a kulcserősségek pontos kombinációját egy adott kapcsolathoz, a [Kriptográfiai követelmények – ismertetés](vpn-gateway-about-compliance-crypto.md)e szerint. Ha az algoritmusok és a kulcserősségek pontos kombinációját adja meg, ügyeljen arra, hogy a megfelelő specifikációkat használja a VPN-eszközökön.

## <a name="single-vpn-tunnel"></a>Egy VPN-alagút
Ez a konfiguráció egyetlen S2S VPN-alagútból áll egy Azure VPN-átjáró és egy helyszíni VPN-eszköz között. A BGP-t a VPN-alagúton keresztül is konfigurálhatja.

![Egyetlen S2S VPN-alagút](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Az Azure-konfigurációk létrehozásának lépésenkénti útmutatása az [egyVPN-alagút beállítása című témakörben található.](vpn-gateway-3rdparty-device-config-overview.md#singletunnel)

### <a name="virtual-network-and-vpn-gateway-information"></a>Virtuális hálózat és VPN-átjáró adatai
Ez a szakasz a minta paramétereit sorolja fel.

| **Paraméter**                | **Érték**                    |
| ---                          | ---                          |
| Virtuális hálózati cím előtagok        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN-átjáró IP-címe         | Azure_Gateway_Public_IP      |
| Helyszíni címelőtagok | 10.51.0.0/16<br>10.52.0.0/16 |
| Helyszíni VPN-eszköz IP-címe    | OnPrem_Device_Public_IP     |
| * Virtuális hálózat BGP ASN                | 65010                        |
| * Az Azure BGP-társ IP-címe           | 10.12.255.30                 |
| * Helyszíni BGP ASN         | 65050                        |
| * Helyszíni BGP-társ IP-címe     | 10.52.255.254                |
|                              |                              |

\*Csak a BGP-hez választható paraméter.

### <a name="ipsecike-policy-and-parameters"></a>IPsec/IKE házirend és paraméterek
Az alábbi táblázat a mintában használt IPsec/IKE algoritmusokat és paramétereket sorolja fel. A VPN-eszköz specifikációiból ellenőrizheti a VPN-eszközmodellek és a belső vezérlőprogram-verziók által támogatott algoritmusokat.

| **IPsec/IKEv2**  | **Érték**                            |
| ---              | ---                                  |
| IKEv2-titkosítás | AES256                               |
| IKEv2-integritás  | SHA384                               |
| DH-csoport         | DHGroup24                            |
| * IPsec titkosítás | AES256                               |
| * IPsec integritás  | SHA1                                 |
| PFS-csoport        | PFS24                                |
| Gyorsmódú biztonsági társítás élettartama   | 7200 másodperc                         |
| Forgalomválasztó | UsePolicyBasedTrafficSelectors $True |
| Előre megosztott kulcs   | PreSharedKey                         |
|                  |                                      |

\*Egyes eszközökön az IPsec-integritásnak null értékűnek kell lennie, ha az IPsec titkosítási algoritmus AES-GCM.

### <a name="asa-device-support"></a>ASA-eszköz támogatás

* Az IKEv2 támogatásához az ASA 8.4-es és újabb verziója szükséges.

* A DH-csoport és a PFS-csoport 5-ös csoportján túli támogatása az ASA 9.x verzióját igényli.

* Az IPsec-titkosítás sHA-256, SHA-384 vagy SHA-512 titkosítással való támogatása az ASA 9.x-es verzióját igényli. Ez a támogatási követelmény az újabb ASA-eszközökre vonatkozik. A közzététel időpontjában az ASA 5505, 5510, 5520, 5540, 5550 és 5580 modellek nem támogatják ezeket az algoritmusokat. A VPN-eszköz specifikációiból ellenőrizheti a VPN-eszközmodellek és a belső vezérlőprogram-verziók által támogatott algoritmusokat.


### <a name="sample-device-configuration"></a>Mintaeszköz konfigurációja
A parancsfájl egy minta, amely az előző szakaszokban ismertetett konfiguráción és paramétereken alapul. Az S2S VPN-alagút konfigurációja a következő részekből áll:

1. Kapcsolódási pontok és útvonalak
2. Hozzáférési listák
3. IkE-házirend és paraméterek (1. fázis vagy alapmód)
4. IPsec-házirend és paraméterek (2. fázis vagy gyorsmód)
5. Egyéb paraméterek, mint például a TCP MSS befogás

> [!IMPORTANT]
> A mintaparancsfájl használata előtt hajtsa végre a következő lépéseket. Cserélje le a parancsfájl helyőrző értékeit a konfiguráció eszközbeállításaira.

* Adja meg a kapcsolat konfigurációját mind a belső, mind a külső összeköttetésekhez.
* Azonosítsa a belső/magán- és külső/nyilvános hálózatok útvonalait.
* Győződjön meg arról, hogy minden név és házirendszám egyedi az eszközön.
* Győződjön meg arról, hogy a kriptográfiai algoritmusok támogatottak az eszközön.
* Cserélje le a következő **helyőrző értékeket** a konfiguráció tényleges értékeire:
  - Külső kapcsolat neve: **kívül**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - IKE: **Pre_Shared_Key**
  - Virtuális hálózat és helyi hálózati átjárónevek: **VNetName** és **LNGName**
  - Virtuális hálózati és helyszíni hálózati **címelőtagok**
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

## <a name="simple-debugging-commands"></a>Egyszerű hibakeresési parancsok

Hibakereséscéljából használja az alábbi ASA-parancsokat:

* Az IPsec vagy az IKE biztonsági társításának (SA) megjelenítése:
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* Hibakeresési mód megadása:
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    A `debug` parancsok jelentős kimenetet generálhatnak a konzolon.

* Az eszköz aktuális konfigurációinak megjelenítése:
    ```
    show run
    ```
    Az `show` alparancsokkal az eszköz konfigurációjának egyes részeit sorolhat fel, például:
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>További lépések
Az aktív-aktív létesítmények közötti és a virtuális hálózat és a virtuális hálózat közötti kapcsolatok konfigurálása az [aktív-aktív VPN-átjárók konfigurálása](vpn-gateway-activeactive-rm-powershell.md)című témakörben található.
