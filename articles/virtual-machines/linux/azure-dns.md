---
title: "DNS-név feloldása beállítások a Linux virtuális gépek Azure-ban"
description: "Név feloldása forgatókönyvek a Linux virtuális gépek Azure IaaS, beleértve a megadott DNS-szolgáltatásokra, hibrid külső DNS- és Bring Your Own DNS-kiszolgáló."
services: virtual-machines
documentationcenter: na
author: RicksterCDN
manager: timlt
editor: tysonn
ms.assetid: 787a1e04-cebf-4122-a1b4-1fcf0a2bbf5f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: rclaus
ms.openlocfilehash: cc06ee9305b4d3034154a0825c1aea53fe446f80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>A DNS-névfeloldás beállítások a Linux virtuális gépek Azure-ban
Azure alapértelmezés szerint a DNS-névfeloldás biztosít egy virtuális hálózaton lévő összes virtuális gépekhez. Valósíthatja meg a saját DNS-név feloldása megoldást a saját DNS-szolgáltatások konfigurálása a virtuális gépeken, amely az Azure futtatja. A következő forgatókönyvek segítséget válassza azt, amelyik a helyzetnek működik.

* [Azure által biztosított névfeloldás](#azure-provided-name-resolution)
* [Névfeloldás a saját DNS-kiszolgáló használatával](#name-resolution-using-your-own-dns-server)

A névfeloldás, amelyekkel típusa attól függ, hogyan a virtuális gépek és a szerepkörpéldányok kommunikálnia kell egymáshoz.

A következő táblázat bemutatja a forgatókönyvek és a megfelelő név feloldása megoldások:

| **A forgatókönyv** | **Megoldás** | **Utótag** |
| --- | --- | --- |
| Névfeloldás szerepkörpéldányokat vagy az azonos virtuális hálózatban lévő virtuális gépek között |[Azure által biztosított névfeloldás](#azure-provided-name-resolution) |állomásnév vagy teljesen minősített tartománynevét (FQDN) |
| Névfeloldás szerepkörpéldányokat vagy különböző virtuális hálózatokon lévő virtuális gépek között |Ügyfél által felügyelt DNS-kiszolgálók, amelyek az Azure-ban (DNS-proxy) megoldás virtuális hálózatok közötti lekérdezéseket. Lásd: [névfeloldáshoz a saját DNS-kiszolgáló](#name-resolution-using-your-own-dns-server). |Kizárólag az FQDN esetében |
| A helyszíni számítógépek és a szerepkörpéldányok vagy Azure virtuális gépek szolgáltatásnevek feloldását. |Ügyfél által felügyelt DNS-kiszolgálók (például a helyi tartományvezérlő, helyi írásvédett tartományvezérlő vagy másodlagos DNS zónaletöltés használatával szinkronizálva). Lásd: [névfeloldáshoz a saját DNS-kiszolgáló](#name-resolution-using-your-own-dns-server). |Kizárólag az FQDN esetében |
| A helyszíni számítógépek Azure állomásnevének feloldása |Továbbítsa az ügyfél által felügyelt DNS-proxy kiszolgáló a megfelelő virtuális hálózat. A proxykiszolgáló lekérdezések az Azure-bA továbbítja a feloldásához. Lásd: [névfeloldáshoz a saját DNS-kiszolgáló](#name-resolution-using-your-own-dns-server). |Kizárólag az FQDN esetében |
| Névkeresési DNS, a belső IP-címek |[Névfeloldás a saját DNS-kiszolgáló használatával](#name-resolution-using-your-own-dns-server) |n/a |

## <a name="name-resolution-that-azure-provides"></a>Azure által biztosított névfeloldás
Nyilvános DNS-nevek feloldása, valamint Azure belső névfeloldást biztosít a virtuális gépek és a szerepkörpéldányok, az azonos virtuális hálózatban. A virtuális hálózatokon, amelyek az Azure Resource Manager a DNS-utótag összhangban a virtuális hálózaton; nincs szükség a teljes Tartománynevét. DNS-nevek hálózati adapterek (NIC) és a virtuális gépek is hozzárendelhető. Bár az Azure által biztosított névfeloldás kell konfigurálni, nincs a megfelelő választás az összes központi telepítési forgatókönyv szerint az előző táblázatban látható.

### <a name="features-and-considerations"></a>Szolgáltatások és szempontok
**Szolgáltatások:**

* Konfigurálásra névfeloldás Azure által biztosított használatához szükséges.
* Az Azure által biztosított névfeloldási szolgáltatás magas rendelkezésre állású. Nem kell létrehozni és fürtöket a saját DNS-kiszolgálók kezelése.
* Az Azure által biztosított névfeloldási szolgáltatás a helyszíni és az Azure állomásnevek használható együtt a saját DNS-kiszolgálók.
* Virtuális gépek teljes Tartománynevét nélkül virtuális hálózatok közötti névfeloldás valósul meg.
* Használhatja a központi telepítések jellemző állomásnevek ahelyett, hogy működik-e automatikusan létrehozott névvel.

**Szempontok:**

* Nem lehet módosítani a DNS-utótagot, amely Azure-hoz.
* Nem lehet manuálisan regisztrálni a saját rögzíti.
* WINS- és NetBIOS nem támogatottak.
* Állomásnevek kell lennie a DNS-kompatibilis.
    Neveket kell használnia csak 0-9, a – z, és a "-", és nem kezdődhet vagy végződhet egy "-". Című rész RFC 3696 2.
* DNS-lekérdezés forgalom az egyes virtuális gépek folyamatban van. Sávszélesség-szabályozás nem befolyásolja a legtöbb alkalmazást.  Ha kérelem szabályozása, győződjön meg arról, hogy engedélyezve van-e az ügyféloldali gyorsítótárazás.  További információkért lásd: [lekérése a legtöbb az Azure által biztosított névfeloldás](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>A végfelhasználókkal névfeloldás, hogy az Azure biztosít.
**Ügyféloldali gyorsítótárazás:**

Bizonyos DNS-lekérdezéseket a rendszer nem küldi a hálózaton keresztül. Ügyféloldali gyorsítótárazás segít a késés csökkentésére, és javítása a hálózati inkonzisztenciát a rugalmasság feloldása ismétlődő DNS-lekérdezések a helyi gyorsítótárból. DNS-rekordokat tartalmaz egy Time-Live (TTL), amely lehetővé teszi a gyorsítótár, amíg a rekord tárolásához rekordot frissesség befolyásolása nélkül. Ennek eredményeképpen ügyféloldali gyorsítótárazás alkalmas a legtöbb esetben.

Egyes Linux terjesztésekről gyorsítótárazás alapértelmezés szerint nem tartalmaznak. Javasoljuk, hogy adja hozzá a gyorsítótárba való minden egyes Linux virtuális gép után ellenőrizze, hogy a helyi gyorsítótárba még nem.

Számos különböző DNS-, csomagok, például a dnsmasq, gyorsítótárazás érhetők el. A leggyakrabban használt terjesztéseket dnsmasq telepítendő lépései a következők:

**Ubuntu (használ resolvconf)**
  * Telepítse a dnsmasq csomagot ("sudo apt-get-telepítés dnsmasq").

**SUSE (használ netconf)**:
1. Telepítse a dnsmasq csomagot ("sudo zypper telepítés dnsmasq").
2. Engedélyezze a dnsmasq szolgáltatást ("systemctl engedélyezése dnsmasq.service").
3. Indítsa el a dnsmasq szolgáltatást ("systemctl start dnsmasq.service").
4. Szerkesztés "/ etc/sysconfig/hálózati/config", és módosítsa a NETCONFIG_DNS_FORWARDER = "" a "dnsmasq".
5. A helyi DNS-feloldási frissíti a resolv.conf ("netconfig update") beállítása a gyorsítótárban.

**Rosszindulatú Wave szoftver (korábbi nevén OpenLogic; NetworkManager használja) – centOS**
1. Telepítse a dnsmasq csomagot ("sudo yum telepítés dnsmasq").
2. Engedélyezze a dnsmasq szolgáltatást ("systemctl engedélyezése dnsmasq.service").
3. Indítsa el a dnsmasq szolgáltatást ("systemctl start dnsmasq.service").
4. Adja hozzá a "név-tartománykiszolgálók 127.0.0.1; illesztenie" a "/etc/dhclient-eth0.conf".
5. Indítsa újra a hálózati szolgáltatást ("szolgáltatás hálózati restart"), a gyorsítótár állítja be a helyi DNS-feloldó

> [!NOTE]
> : A "dnsmasq" csomag nincs csak az egyik elérhető Linux számos DNS gyorsítótárat. Használatba vétel előtt ellenőrizze az igényeinek alkalmas, és nincs más gyorsítótár telepített.
>
>

**Ügyféloldali újrapróbálkozások**

DNS elsősorban UDP protokoll. Az UDP protokoll üzenetkézbesítést nem garantálja, mert a DNS protokoll kezeli az újrapróbálkozási logika. Minden DNS-ügyfél (operációs rendszer) is virágot különböző újrapróbálkozási logika attól függően, hogy a létrehozásához használt beállítások:

* Windows operációs rendszerek újrapróbálkozás után egy második és majd újra egy másik kettő, négy, és egy másik négy másodperc.
* Az alapértelmezett Linux telepítő újrapróbálkozások öt másodperc múlva.  Ez ötször egy másodperces időközönként megpróbálja majd újból meg kell változtatni.  

Az aktuális beállításokat a Linux virtuális gép, "cat /etc/resolv.conf", és nézze meg a "beállítások" sor például ellenőrzése:

    options timeout:1 attempts:5

A resolv.conf fájl jön létre automatikusan, és nem szerkeszthető. Adja hozzá a "beállítások" sort lépéseivel eltérőek lehetnek, a terjesztési függően:

**Ubuntu** (resolvconf használ)
1. Adja a beállítások sort "/ etc/resolveconf/resolv.conf.d/head".
2. Futtassa a 'resolvconf -u' frissítéséhez.

**SUSE** (netconf használ)
1. Adja hozzá a NETCONFIG_DNS_RESOLVER_OPTIONS 'timeout:1 kísérletek: 5' = "" a "/ etc/sysconfig/hálózati/config" paraméter.
2. Futtassa a "netconfig update" frissítéséhez.

**Az engedélyezetlen Wave szoftver (korábbi nevén OpenLogic) – centOS** (NetworkManager használ)
1. Hozzáadása "echo"beállítások timeout:1 kísérletek: 5"" "/ etc/NetworkManager/dispatcher.d/11-dhclient".
2. Futtassa a "szolgáltatás hálózati újraindítás" frissítéséhez.

## <a name="name-resolution-using-your-own-dns-server"></a>Névfeloldás a saját DNS-kiszolgáló használatával
A névfeloldási szükségleteit előfordulhat, hogy túlmutató az Azure által biztosított funkciókat. Például a DNS-feloldás virtuális hálózatok közötti lehet szükség. Ebben a forgatókönyvben foglalkozik, saját DNS-kiszolgálókat is használhat.  

DNS-kiszolgálók, a virtuális hálózaton belül az azonos virtuális hálózatban állomásnevek megoldásához Azure rekurzív feloldókat továbbíthatja DNS-lekérdezések. Például az Azure-ban futó DNS-kiszolgáló válaszol a DNS-lekérdezésekre a saját DNS-zóna fájlok és további lekérdezések továbbítása az Azure-bA. Ez a funkció lehetővé teszi, hogy a virtuális gépek mindkét a bejegyzéseket a zóna fájlok és az Azure által biztosított (keresztül a továbbító) állomásnevek megjelenítéséhez. A rekurzív feloldókat Azure hozzáférést a virtuális IP-cím 168.63.129.16 keresztül valósul meg.

DNS-továbbítást is lehetővé teszi, hogy a DNS-feloldás virtuális hálózatok között, és lehetővé teszi, hogy a helyszíni gépeket Azure által biztosított állomásnevek megoldásához. A virtuális gép állomásnév feloldása, a DNS-kiszolgáló virtuális gép ugyanazon a virtuális hálózaton kell lennie, és úgy, hogy az Azure előre állomásnév lekérdezések. A DNS-utótagja eltér az egyes virtuális hálózati, mert feltételes továbbítás szabályok segítségével a névfeloldás a megfelelő virtuális hálózat DNS-lekérdezéseket küldjön. Az alábbi ábrán két virtuális hálózatot és egy DNS-feloldás ezt a módszert a virtuális hálózatok közötti történt a helyi hálózaton:

![Virtuális hálózatok közötti DNS-feloldás](./media/azure-dns/inter-vnet-dns.png)

Azure által biztosított névfeloldás használata esetén a belső DNS-utótag biztosítja az egyes virtuális gépek DHCP használatával. A saját nevet névfeloldási megoldás használata esetén ez utótag nincs megadva a virtuális gépek, mert más DNS-architektúrák zavarja a utótag. Tekintse meg a teljes tartománynév alapján gépekhez, vagy utótagjának beállítása a virtuális gépeken, PowerShell vagy az API-t használhatja az utótag meghatározásához:

* Az Azure Resource Manager által kezelt virtuális hálózatok, az utótaghoz érhető el keresztül a [hálózati kártya](https://msdn.microsoft.com/library/azure/mt163668.aspx) erőforrás. Futtathatja a `azure network public-ip show <resource group> <pip name>` parancsot jelennek meg a nyilvános IP-cím, amely tartalmazza a hálózati adaptert. a teljes Tartományneve részletei

Lekérdezések továbbítása az Azure-ba nem a igényeinek megfelelően, ha meg kell adnia a saját DNS-megoldást.  A DNS-megoldást kell megfelelnie:

* Segítségével például adja meg a megfelelő állomásnév-feloldási [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). DDNS használatakor szükség lehet a DNS-rekord kitakarítási letiltása. DHCP-bérletek Azure nagyon hosszú, és kitakarítási távolíthatja el a DNS-rekordok idő előtt.
* Adja meg a megfelelő rekurziót külső tartomány nevek feloldását engedélyezéséhez.
* Elérhető (TCP és UDP 53-as porton) szolgál az ügyfelekről és fogja tudni érni az internetet.
* Védhető szemben az internetről külső ügynökök által jelentett veszélyek mérséklése.

> [!NOTE]
> A legjobb teljesítmény érdekében a virtuális gépek Azure DNS-kiszolgálókat használ, amikor ki IPv6 letiltása, és rendelje hozzá egy [példányszintű nyilvános IP-cím](../../virtual-network/virtual-networks-instance-level-public-ip.md) minden DNS-kiszolgáló virtuális gépe.  
>
>
