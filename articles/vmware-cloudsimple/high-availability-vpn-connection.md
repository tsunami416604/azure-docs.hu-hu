---
title: Azure VMware-megoldás a CloudSimple-től – Magas rendelkezésre állás konfigurálása a helyszíni és a CloudSimple VPN-átjáró között
description: Ez a témakör azt ismerteti, hogy miként konfigurálható a helyszíni környezetből a magas rendelkezésre állásra engedélyezett CloudSimple VPN-átjárókapcsolatra
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e3118814eacc6cc63b5db59bd7f1877c1d347dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025265"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>Magas rendelkezésre állású kapcsolat konfigurálása a helyszíni és a CloudSimple VPN-átjáró között

A hálózati rendszergazdák magas rendelkezésre állású IPsec-hely közötti VPN-kapcsolatot konfigurálhatnak a helyszíni környezetükből egy CloudSimple VPN-átjáróhoz.

Ez az útmutató az IPsec-helyek közötti, magas rendelkezésre állású kapcsolat helyszíni tűzfalának konfigurálásának lépéseit mutatja be. A részletes lépések a helyszíni tűzfal típusára vonatkoznak. Ez az útmutató például kétféle tűzfal lépéseit mutatja be: cisco ASA és Palo Alto Networks.

## <a name="before-you-begin"></a>Előkészületek

A helyszíni tűzfal konfigurálása előtt hajtsa végre a következő feladatokat.

1. Ellenőrizze, hogy a szervezet [kiépített-e](create-nodes.md) a szükséges csomópontokat, és létrehozott-e legalább egy CloudSimple private cloud-ot.
2. [Konfiguráljon egy helyek közötti VPN-átjárót](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) a helyszíni hálózat és a CloudSimple magánfelhő között.

Lásd a [VPN-átjárók áttekintését](cloudsimple-vpn-gateways.md) a támogatott 1.

## <a name="configure-on-premises-cisco-asa-firewall"></a>Helyszíni Cisco ASA tűzfal konfigurálása

Az ebben a szakaszban található utasítások a Cisco ASA 8.4-es és újabb verzióira vonatkoznak. A konfigurációs példában a Cisco Adaptive Security Appliance Software 9.10-es verziója ikEv1 módban van telepítve és konfigurálva.

Ahhoz, hogy a helyek közötti VPN működjön, engedélyeznie kell az UDP 500/4500 és az ESP (50 IP protokoll) protokollt a CloudSimple elsődleges és másodlagos nyilvános IP-címéről (peer IP) a helyszíni Cisco ASA VPN átjáró külső felületén.

### <a name="1-configure-phase-1-ikev1"></a>1. 1. fázis konfigurálása (IKEv1)

A külső felületen az 1.

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2. IKEv1 házirend létrehozása

Hozzon létre egy IKEv1 házirendet, amely meghatározza a kivonatoláshoz, hitelesítéshez, Diffie-Hellman csoporthoz, élettartamhoz és titkosításhoz használandó algoritmusokat és módszereket.

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. Bújtatási csoport létrehozása

Hozzon létre egy bújtatási csoportot az IPsec-attribútumok alatt. Konfigurálja a társ IP-címét és a bújtatás előtti megosztott kulcsot, amelyet a helyek közötti [VPN-átjáró konfigurálásakor](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)állított be.

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. Fázis 2 konfigurálása (IPsec)

A 2. A következő példában a fontos forgalom a helyszíni helyi alhálózattól (10.16.1.0/24) a private cloud távoli alhálózatba (192.168.0.0/24) származó alagútból származik. Az ACL több bejegyzést is tartalmazhat, ha a helyek között több alhálózat van.

A Cisco ASA 8.4-es és újabb verzióiban olyan objektumok vagy objektumcsoportok hozhatók létre, amelyek tárolóként szolgálnak a hálózatok, alhálózatok, gazdaIP-címek vagy több objektum számára. Hozzon létre egy objektumot a helyi és egy objektumot a távoli alhálózatok hoz létre, és használja őket a kriptográfiai acl és a NAT utasításokhoz.

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>Helyszíni helyi alhálózat definiálása objektumként

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>A CloudSimple távoli alhálózat definiálása objektumként

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>Hozzáférési lista konfigurálása az érdeklődésre számot tartó forgalomhoz

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. Az átalakító készlet konfigurálása

Konfigurálja az átalakítókészletet (TS), ```ikev1```amelynek a kulcsszót is magában kell foglalnia. A TS-ben megadott titkosítási és kivonatoló attribútumoknak meg kell egyezniük a [CloudSimple VPN-átjárók alapértelmezett konfigurációjában](cloudsimple-vpn-gateways.md)felsorolt paraméterekkel.

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. Állítsa be a kriptográfiai térképet

Konfigurálja a kriptográfiai térképet, amely a következő összetevőket tartalmazza:

* Társ IP-címe
* Az érdeklődésre számot tartó forgalmat tartalmazó, meghatározott ACL
* Átalakítás készlete

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. Alkalmazza a kriptográfiai térképet

Alkalmazza a kriptográfiai térképet a külső felületen:

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8. Erősítse meg a vonatkozó NAT-szabályokat

A következő a használt NAT-szabály. Győződjön meg arról, hogy a VPN-forgalom nem vonatkozik más NAT-szabály.

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>Minta IPsec helyek közötti VPN létrehozott kimenet cisco ASA

1. fázis kimenete:

![Fázis 1 kimenet a Cisco ASA tűzfalhoz](media/ha-vpn-connection-cisco-phase1.png)

2. fázis kimenete:

![Fázis 2 kimenet a Cisco ASA tűzfalhoz](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>Helyszíni Palo Alto Networks tűzfal konfigurálása

Az ebben a szakaszban található utasítások a Palo Alto Networks 7.1-es és újabb verzióira vonatkoznak. Ebben a konfigurációs példában a Palo Alto Networks VM sorozatú szoftververzió 8.1.0-s verziója ikev1 módban van telepítve és konfigurálva.

Ahhoz, hogy a helyek közötti VPN működjön, engedélyeznie kell az UDP 500/4500 és az ESP (50 IP protokoll) protokollt a CloudSimple elsődleges és másodlagos nyilvános IP-címéről (peer IP) a helyszíni Palo Alto átjáró külső felületén.

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. Elsődleges és másodlagos alagút-összeköttetések létrehozása

Jelentkezzen be a Palo Alto tűzfalba, válassza a **Hálózati** > **adapterek** > **alagút** > **hozzáadása**lehetőséget, konfigurálja a következő mezőket, majd kattintson az **OK**gombra.

* Kapcsolat neve. Az első mező automatikusan kitölti a kulcsszó "alagút". A szomszédos mezőbe írjon be 1 és 9999 közötti számot. Ez az összeköttetés lesz elsődleges alagútinterfész a helyszíni adatközpont és a magánfelhő közötti helyek közötti forgalom átviteléhez.
* Megjegyzést. Adja meg a megjegyzéseket az alagút céljának egyszerű azonosításához
* Netflow profil. Hagyja meg az alapértelmezett értéket.
* Config. Összeköttetés hozzárendelése: Virtuális útválasztó: Válassza az **alapértelmezett**lehetőséget. 
        Biztonsági zóna: Válassza ki a megbízható LAN-forgalom zónáját. Ebben a példában a lan-forgalom zónájának neve "Megbízhatóság".
* IPv4.IPv4. Kattintson **a Hozzáadás** gombra, és adja hozzá a környezetben a nem át nem használt /32 ip-címet, amely az elsődleges alagút-összeköttetéshez lesz hozzárendelve, és az alagutak figyelésére lesz használva (később kifejtve).

Mivel ez a konfiguráció magas rendelkezésre állású VPN-kapcsolat, két bújtatási kapcsolatra van szükség: egy elsődleges és egy másodlagos. Ismételje meg az előző lépéseket a másodlagos alagút összeköttetés létrehozásához. Válasszon másik bújtatási azonosítót és egy másik nem használt /32 ip-címet.

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. Statikus útvonalak beállítása a helyek közötti VPN-en keresztül elérendő magánfelhő-alhálózatokhoz

Útvonalak szükségesek a helyszíni alhálózatok a CloudSimple magánfelhő-alhálózatok eléréséhez.

Válassza a **Hálózati** > **virtuális útválasztók** > *alapértelmezett* > **Statikus útvonalak** > **hozzáadása lehetőséget,** konfigurálja a következő mezőket, majd kattintson az **OK**gombra.

* név. Adjon meg bármilyen nevet az útvonal céljának egyszerű azonosításához.
* Cél. Adja meg a helyszíni S2S-alagút-összeköttetéseken elérendő CloudSimple magánfelhő-alhálózatokat
* Felület. Válassza ki az 1. Ebben a példában ez alagút.20.
* Következő ugrás. Válassza a **Nincs** lehetőséget.
* Admin távolság. Hagyja meg az alapértelmezett értéket.
* Metrikus. Adjon meg egy 1 és 65535 között megadott értéket. A kulcs az elsődleges alagút-összeköttetésnek megfelelő útvonal alacsonyabb metrika megadása az útvonal megfelelő másodlagos alagút-összeköttetéséhez képest, így a korábbi útvonal előnyben részesül. Ha a tunnel.20 metrikus értéke 20, szemben a 30 metrikaértékkel az alagútesetében.30, akkor a tunnel.20 ajánlott.
* Útvonaltábla. Hagyja meg az alapértelmezett értéket.
* BFD profil. Hagyja meg az alapértelmezett értéket.
* Elérési út figyelése. Hagyja üresen.

Ismételje meg az előző lépéseket egy másik útvonal létrehozásához a magánfelhő-alhálózatok számára, amelyet másodlagos/biztonsági mentési útvonalként használhat nak a másodlagos alagútkapcsolaton keresztül. Ezúttal válasszon másik bújtatási azonosítót és magasabb metrikát, mint az elsődleges útvonalhoz.

### <a name="3-define-the-cryptographic-profile"></a>3. Határozza meg a kriptográfiai profilt

Definiáljon egy kriptográfiai profilt, amely meghatározza az IKEv1 1.

Válassza a **Hálózati** > **kapcsolatok** > bővítése**IKE titkosítás** > **hozzáadása lehetőséget,** konfigurálja a következő mezőket, majd kattintson az **OK**gombra.

* név. Adja meg az IKE titkosítási profiljának bármilyen nevét.
* DH csoport. Kattintson a **Hozzáadás** gombra, és válassza ki a megfelelő DH-csoportot.
* Titkosítás. Kattintson a **Hozzáadás** gombra, és válassza ki a megfelelő titkosítási módszert.
* Hitelesítés. Kattintson a **Hozzáadás** gombra, és válassza ki a megfelelő hitelesítési módszert.
* Kulcs élettartama. Hagyja meg az alapértelmezett értéket.
* IKEv2 hitelesítés többszörös. Hagyja meg az alapértelmezett értéket.

### <a name="4-define-ike-gateways"></a>4. IKE-átjárók definiálása

IkE-átjárók definiálása a VPN-alagút mindkét végén a társak közötti kommunikáció létrehozásához.

Válassza **a Hálózati** > **kapcsolatok** > bővítése**internetes hálózatok és -átjárók** > **hozzáadása lehetőséget,** konfigurálja a következő mezőket, majd kattintson az **OK**gombra.

Általános lap:

* név. Adja meg az elsődleges CloudSimple VPN-társsal társviszonyba kerülő IKE-átjáró nevét.
* Verzió felirat mellett megjelenő számot. Válassza **az IKEv1 csak módot**.
* Cím típusa. Válassza az **IPv4** lehetőséget.
* Felület. Válassza ki a nyilvános vagy külső felületet.
* Helyi IP-cím. Hagyja meg az alapértelmezett értéket.
* Társ IP-címtípusa. Válassza az **IP**lehetőséget.
* Társcím. Adja meg az elsődleges CloudSimple VPN-társ IP-címét.
* Hitelesítés. Válassza **az Előmegosztott kulcs lehetőséget**.
* Előmegosztott kulcs / Előmegosztott kulcs megerősítése. Adja meg az előmegosztott kulcsot, hogy megfeleljen a CloudSimple VPN-átjárókulcsnak.
* Helyi azonosítás. Adja meg a helyszíni Palo Alto tűzfal nyilvános IP-címét.
* Társazonosítás. Adja meg az elsődleges CloudSimple VPN-társ IP-címét.

Speciális beállítások lap:

* Engedélyezze a passzív módot. Hagyja üresen.
* NAT-bejárás engedélyezése. Hagyja bejelölve, ha a helyszíni Palo Alto tűzfal nem áll nat-eszköz mögött. Ellenkező esetben jelölje be a jelölőnégyzetet.

IKEv1:

* Exchange mód. Válassza ki **a fő**t.
* IKE titkosítási profil. Jelölje ki a korábban létrehozott IKE titkosítási profilt. Hagyja bejelölve a Töredezettség engedélyezése jelölőnégyzetet.
* Halott társfelismerés. Hagyja bejelölve a dobozt.

Ismételje meg az előző lépéseket a másodlagos IKE-átjáró létrehozásához.

### <a name="5-define-ipsec-crypto-profiles"></a>5. IPSEC titkosítási profilok definiálása

Válassza a **Hálózati** > **kapcsolatok** > bővítése**IPSEC-titkosítás** > **hozzáadása lehetőséget,** konfigurálja a következő mezőket, majd kattintson az **OK**gombra.

* név. Adja meg az IPsec titkosítási profil nevét.
* IPsec protokoll. Válassza **az ESP**lehetőséget.
* Titkosítás. Kattintson a **Hozzáadás** gombra, és válassza ki a megfelelő titkosítási módszert.
* Hitelesítés. Kattintson a **Hozzáadás** gombra, és válassza ki a megfelelő hitelesítési módszert.
* DH csoport. Válassza a **no-pfs lehetőséget**.
* Élettartam. Állítsa be 30 percben.
* Engedélyezi. Hagyja bejelölve a dobozt.

Ismételje meg az előző lépéseket egy másik IPsec kriptográfiai profil létrehozásához, amelyet másodlagos CloudSimple VPN-társként fog használni. Ugyanaz az IPSEC-titkosítási profil az elsődleges és a másodlagos IPsec-alagutakhoz is használható (lásd az alábbi eljárást).

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. Monitorprofilok meghatározása az alagút figyeléséhez

Válassza a Hálózati profilok > **hálózatkibontása** > **figyelő** > **hozzáadása lehetőséget,** konfigurálja a következő mezőket, majd kattintson az **OK**gombra. **Network**

* név. Adja meg a figyelő profil nevét, amelyet az alagút figyeléséhez használ a hiba proaktív reakciójának érdekében.
* Akció. Válassza a **Feladatátvétel**lehetőséget.
* Intervallum. Adja meg a **3**értéket.
* Küszöb. Adja meg a **7**értéket.

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. Elsődleges és másodlagos IPsec-alagutak beállítása.

Válassza **a Hálózati** > **IPsec-alagutak** > hozzáadása**lehetőséget,** konfigurálja a következő mezőket, majd kattintson az **OK**gombra.

Általános lap:

* név. Adja meg az elsődleges CloudSimple VPN-társsal társviszonyba kerülő elsődleges IPSEC-alagút nevét.
* Alagút interfész. Válassza ki az elsődleges bújtatási összeköttetést.
* Típus. Hagyja meg az alapértelmezett értéket.
* Cím típusa. Válassza az **IPv4** lehetőséget.
* IKE átjáró. Jelölje ki az elsődleges IKE-átjárót.
* IPsec titkosítási profil. Jelölje ki az elsődleges IPsec-profilt. Válassza **a Speciális beállítások megjelenítése lehetőséget.**
* Engedélyezze a visszajátszáselleni védelmet. Hagyja meg az alapértelmezett értéket.
* TOS fejléc másolása. Hagyja bejelölve a dobozt.
* Alagút figyelő. Nézd meg a négyzetet.
* Cél IP. Adja meg a CloudSimple private cloud alhálózathoz tartozó bármely IP-címet, amely a helyek közötti kapcsolaton keresztül engedélyezett. Győződjön meg arról, hogy a Palo Alto bújtatási összeköttetései (például tunnel.20 - 10.64.5.2/32 és tunnel.30 - 10.64.6.2/32) a Helyek közötti VPN-en keresztül elérhetik a CloudSimple private cloud IP-címet. A proxyazonosítókhoz az alábbi konfigurációt olvassa el.
* Profil. Válassza ki a monitor profilját.

Proxyazonosítók lap: Kattintson az **IPv4** > **hozzáadása** és konfigurálása gombra:

* Proxyazonosító. Adja meg az érdekes forgalom nevét. Lehet, hogy több proxyazonosító tanusít egy IPsec-alagúton belül.
* Helyi. Adja meg azokat a helyszíni helyi alhálózatokat, amelyek a helyek közötti VPN-en keresztül kommunikálhatnak a magánfelhő alhálózataival.
* Távoli. Adja meg a magánfelhő távoli alhálózatait, amelyek kommunikálhatnak a helyi alhálózatokkal.
* Protokoll. Válassza ki **a bármelyiket.**

Ismételje meg az előző lépéseket egy másik IPsec-alagút létrehozásához, amelyet a másodlagos CloudSimple VPN-társhoz használhat.

## <a name="references"></a>Referencia

A NAT konfigurálása a Cisco ASA-n:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">Cisco ASA 5500 sorozatú konfigurációs útmutató</a>

A Cisco ASA támogatott IKEv1 és IKEv2 attribútumai:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">Cisco ASA sorozatú CLI konfigurációs útmutató</a>

IPsec helyek közötti VPN konfigurálása Cisco ASA-n a 8.4-es és újabb verzióval:

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">Az IKEv1 IPsec helyek közötti bújtatáskonfigurálása az ASA ASDM-jével vagy CLI-jével</a>

A Cisco Adaptive Security Appliance virtuális (ASAv) konfigurálása az Azure-ban:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">A Cisco Adaptive Security Virtual Appliance (ASAv) rövid útmutató</a>

A helyek közötti VPN konfigurálása proxyazonosítókkal palo alto-n:

[Helyek közötti VPN beállítása](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

Bújtatásfigyelő beállítása:

[Alagútfigyelés beállítása](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

Internetes kulcscsere-átjáró vagy IPsec-alagút műveletei:

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">IkE-átjáró vagy IPsec-alagút engedélyezése/letiltása, frissítése vagy újraindítása</a>
