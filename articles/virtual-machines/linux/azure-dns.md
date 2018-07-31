---
title: Linux rendszerű virtuális gépek az Azure DNS-neve-névfeloldási beállítások
description: Név feloldása forgatókönyvek az Azure IaaS, beleértve a Linuxos virtuális gépek a megadott DNS-szolgáltatások, a hibrid külső DNS- és a Bring Your Own DNS-kiszolgáló.
services: virtual-machines
documentationcenter: na
author: RicksterCDN
manager: jeconnoc
editor: tysonn
ms.assetid: 787a1e04-cebf-4122-a1b4-1fcf0a2bbf5f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: rclaus
ms.openlocfilehash: 93614d4889c9c884f25c5e05cd620e8303226323
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39357766"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Linux rendszerű virtuális gépek az Azure DNS-névfeloldás beállításai
Az Azure DNS-névfeloldás alapértelmezés szerint egyetlen virtuális hálózaton lévő összes virtuális gépekhez biztosít. Valósítható meg a saját DNS-név feloldása megoldást a saját DNS-szolgáltatások konfigurálásával a virtuális gépeken az Azure futtató. A következő esetekben kell segítségével eldöntheti, amelyik az adott helyzetben használható.

* [Azure által biztosított névfeloldást](#name-resolution-that-azure-provides)
* [Névfeloldás saját DNS-kiszolgáló használatával](#name-resolution-using-your-own-dns-server)

Névfeloldás használt típusa attól függ, hogy a virtuális gépek és szerepkörpéldányok kell egymással kommunikálni.

Az alábbi táblázat azt mutatja be, forgatókönyvek és a megfelelő név feloldása megoldások:

| **Forgatókönyv** | **Megoldás** | **Suffix** |
| --- | --- | --- |
| Szerepkör példányai vagy az azonos virtuális hálózatban lévő virtuális gépek közötti névfeloldás |[Azure által biztosított névfeloldást](#azure-provided-name-resolution) |állomásneve vagy teljes tartománynév (FQDN) |
| Szerepkörpéldányok vagy a különböző virtuális hálózatokban lévő virtuális gépek közötti névfeloldás |Ügyfél által felügyelt DNS-kiszolgálók (DNS-proxy) az Azure által a névfeloldáshoz virtuális hálózatok közötti lekérdezéseket. Lásd: [névfeloldás saját DNS-kiszolgáló használatával](#name-resolution-using-your-own-dns-server). |Csak teljes tartománynév |
| A helyszíni számítógépek és a szerepkör példányai vagy az Azure virtual machines szolgáltatás nevek feloldása |Ügyfél által felügyelt DNS-kiszolgálók (például a helyi tartományvezérlő, helyi írásvédett tartományvezérlő vagy másodlagos DNS zónaletöltés használatával szinkronizált). Lásd: [névfeloldás saját DNS-kiszolgáló használatával](#name-resolution-using-your-own-dns-server). |Csak teljes tartománynév |
| A helyi számítógépek Azure gazdanév feloldása |Továbbítsa az ügyfél által felügyelt DNS-proxy kiszolgáló a megfelelő virtuális hálózatban. A proxykiszolgáló lekérdezések az Azure-bA továbbítja a feloldásához. Lásd: [névfeloldás saját DNS-kiszolgáló használatával](#name-resolution-using-your-own-dns-server). |Csak teljes tartománynév |
| Fordított DNS belső IP-címek |[Névfeloldás saját DNS-kiszolgáló használatával](#name-resolution-using-your-own-dns-server) |n/a |

## <a name="name-resolution-that-azure-provides"></a>Azure által biztosított névfeloldást
Nyilvános DNS-nevek feloldása, valamint az Azure belső névfeloldást biztosít a virtuális gépek és szerepkörpéldányok, amelyek azonos virtuális hálózatban. Az Azure Resource Manageren alapuló virtuális hálózatok a DNS-utótag összhangban a virtuális hálózaton; a teljes tartománynév nincs szükség. DNS-nevek a hálózati adapterek (NIC) és a virtuális gépek is rendelhetők. Bár az Azure által biztosított névfeloldást kell konfigurálni, akkor sem a megfelelő választás az összes központi telepítési forgatókönyvek esetén az előző táblázatban látható módon.

### <a name="features-and-considerations"></a>Funkciók és szempontok
**Funkciók:**

* Használja az Azure által biztosított névfeloldást semmilyen beállítást nem szükséges.
* Az Azure által biztosított névfeloldási szolgáltatás a magas rendelkezésre állású. Nem kell a saját DNS-kiszolgálók fürtök létrehozása és felügyelete.
* Az Azure által biztosított névfeloldási szolgáltatás a helyszíni és az Azure állomásnevek használható együtt a saját DNS-kiszolgálók.
* Virtuális gépek a teljes nélkül virtuális hálózatok közötti névfeloldás biztosítunk.
* Használhatja a központi telepítések jellemző állomásnevek automatikusan generált nevek használata helyett.

**Megfontolandó szempontok:**

* Nem lehet módosítani a DNS-utótagot, amely az Azure-hoz létre.
* A saját rekordok manuálisan nem regisztrálható.
* WINS- és NetBIOS nem támogatottak.
* Állomásnevek kell lennie a DNS-kompatibilis.
    Neveket kell használnia csak 0-9, a – z, és a "-", és nem kezdődhet vagy végződhet egy "-". Lásd a 2. RFC 3696 szakasz.
* A DNS-lekérdezés forgalom folyamatban van az egyes virtuális gépekhez. Szabályozás nem érinti a legtöbb alkalmazás.  Ha kérelemszabályozás, győződjön meg arról, hogy engedélyezve van-e az ügyféloldali gyorsítótárazást.  További információkért lásd: [Bevezetés az Azure által biztosított névfeloldást](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>A végfelhasználókkal névfeloldás, hogy az Azure biztosít.
**Ügyféloldali gyorsítótárazás:**

Néhány DNS nem küldi el a hálózaton keresztül. Ügyféloldali gyorsítótárazás a késés csökkentése érdekében, és növelheti a hálózat inkonzisztenciát hibatűrést ismétlődő DNS-lekérdezések a helyi gyorsítótárból feloldása nyújt segítséget. DNS-rekordokat tartalmaz egy idő-az-élettartam (TTL), amely lehetővé teszi a gyorsítótárban tárolja, amíg a rekord rekord frissessége befolyásolása nélkül. Ennek eredményeképpen ügyféloldali gyorsítótárazás ideális választás a legtöbb esetben.

Egyes Linux-disztribúciókon gyorsítótárazás alapértelmezés szerint nem tartalmazzák. Azt javasoljuk, hogy hozzáadása egy gyorsítótár minden egyes Linux virtuális géphez, ellenőrizze, hogy még nem található a helyi gyorsítótárat.

Számos különböző DNS-gyorsítótárazást dnsmasq, például a csomagok érhetők el. A rendszerek leggyakoribb disztribúciói dnsmasq telepítése lépései a következők:

**Ubuntu (használ resolvconf)**
  * Telepítse a dnsmasq csomagot ("sudo apt-get install dnsmasq").

**SUSE (használ netconf)**:
1. Telepítse a dnsmasq csomagot ("sudo zypper telepítés dnsmasq").
2. Engedélyezze a dnsmasq szolgáltatást ("systemctl engedélyezése dnsmasq.service").
3. Indítsa el a dnsmasq szolgáltatást ("systemctl kezdő dnsmasq.service").
4. Szerkesztése "/ etc/sysconfig/hálózati/config", és módosítsa a NETCONFIG_DNS_FORWARDER = "", "dnsmasq".
5. A helyi DNS-feloldási frissíti a resolv.conf ("netconfig frissítés") beállítása a gyorsítótárban.

**CentOS Rogue Wave Software-től (korábbi nevén OpenLogic; NetworkManager használja)**
1. Telepítse a dnsmasq csomagot ("sudo yum install dnsmasq").
2. Engedélyezze a dnsmasq szolgáltatást ("systemctl engedélyezése dnsmasq.service").
3. Indítsa el a dnsmasq szolgáltatást ("systemctl kezdő dnsmasq.service").
4. Adja hozzá a "illesztenie a tartomány-name-kiszolgálók 127.0.0.1;", "/etc/dhclient-eth0.conf".
5. Indítsa újra a hálózati szolgáltatás ("szolgáltatás hálózati restart") számára, hogy a gyorsítótár állítja be a helyi DNS-feloldó

> [!NOTE]
> : A "dnsmasq" csomag nincs csak az egyiket a számos DNS-gyorsítótárak Linux rendszeren elérhető. Ahhoz, hogy azt, ellenőrizze az igényeinek való alkalmasságát, és nincs más gyorsítótárazás telepített.
>
>

**Ügyféloldali újrapróbálkozások**

DNS egy elsősorban olyan UDP protokoll. Az UDP protokoll nem garantálja az üzenetek kézbesítését, mert a DNS protokoll maga kezeli az újrapróbálkozási logikát. Minden DNS-ügyfél (operációs rendszer) a létrehozó szabályozó függően eltérő újrapróbálkozási logikát is mutat:

* Windows operációs rendszerek újrapróbálkozás egy második, és újra majd egymás után kettő, négy és a egy másik négy másodperc.
* Az alapértelmezett Linux telepítő újrapróbálkozások öt másodperc múlva.  Módosítsa ezt a ötször egy másodperces időközönként próbálkozzon újra.  

Az aktuális beállítások egy Linux rendszerű virtuális gép, "cat /etc/resolv.conf fájlból", és tekintse meg a "beállítások" sort, például:

    options timeout:1 attempts:5

A resolv.conf fájlt automatikusan létrehozott, és nem szerkeszthetők. Adja hozzá a "beállítások" sort a konkrét lépésekből terjesztési régiónként eltérő:

**Ubuntu** (resolvconf használja)
1. Adja hozzá a beállítások sort "/ etc/resolveconf/resolv.conf.d/head".
2. Futtassa a 'resolvconf -u' frissíteni.

**SUSE** (netconf használja)
1. A NETCONFIG_DNS_RESOLVER_OPTIONS hozzáadása "timeout:1 kísérletek: 5" = "" a "/ etc/sysconfig/hálózati/config" paramétert.
2. Futtassa a "netconfig update" frissíteni.

**CentOS Rogue Wave Software-től (korábbi nevén OpenLogic)** (NetworkManager használja)
1. Adja hozzá a(z) RES_OPTIONS "timeout:1 kísérletek: 5" = ""/ etc/sysconfig/hálózati".
2. Futtassa a "szolgáltatás hálózati újraindítás" frissíteni.

## <a name="name-resolution-using-your-own-dns-server"></a>Névfeloldás saját DNS-kiszolgáló használatával
A névfeloldási szükségleteit előfordulhat, hogy nemcsak az Azure által biztosított funkciókat. Ha például szükség lehet a DNS-feloldási virtuális hálózatok között. Tartalmazza ezt a forgatókönyvet, használhatja a saját DNS-kiszolgálók.  

DNS-kiszolgálók virtuális hálózaton belüli továbbíthatja a DNS-lekérdezések az Azure-megoldása az azonos virtuális hálózatban lévő állomásnevek rekurzív feloldók. Az Azure-ban futó DNS-kiszolgáló például DNS-lekérdezést a saját DNS-zónafájlok válaszolni, és a minden más lekérdezés továbbítása az Azure-bA. Ez a funkció lehetővé teszi, hogy a virtuális gépek mindkét a bejegyzéseket a zóna fájlok és az Azure által biztosított (keresztül a továbbító) gazdanév megtekintéséhez. Az Azure rekurzív feloldók hozzáférést biztosítunk 168.63.129.16 virtuális IP-címen keresztül.

DNS-továbbítást is lehetővé teszi a DNS-feloldási virtuális hálózatok között, és lehetővé teszi, hogy a helyszíni gépek az Azure által biztosított gazdanév feloldása. Egy virtuálisgép-állomásnév feloldása, a DNS-kiszolgáló virtuális gép ugyanazon a virtuális hálózaton kell lennie, és úgy konfigurálni, hogy előre állomásnév lekérdezések az Azure-bA. A DNS-utótagja eltér az egyes virtuális hálózatok, mert a feloldásához a megfelelő virtuális hálózat DNS-lekérdezéseket küldjön feltételes továbbítás szabályok használatával. Az alábbi képen látható a két virtuális hálózat és a egy helyszíni hálózat DNS-feloldás ezzel a módszerrel a virtuális hálózatok közötti ezzel:

![DNS-feloldási virtuális hálózatok között](./media/azure-dns/inter-vnet-dns.png)

Azure által biztosított névfeloldást használata esetén a belső DNS-utótag biztosítják az egyes virtuális gépek DHCP használatával. Ha saját név feloldása megoldást használja, ennek az utótagnak nincs megadva a virtuális gépek, mert rontja az utótag más DNS-architektúrák. Tekintse meg a teljes tartománynév alapján gépekhez, vagy az utótag konfigurálása a virtuális gépeken, PowerShell vagy az API-t használhatja az utótag meghatározásához:

* Az Azure Resource Manager által felügyelt virtuális hálózatok, az utótag keresztül érhető el a [hálózati kártya](https://msdn.microsoft.com/library/azure/mt163668.aspx) erőforrás. Is futtathatja a `azure network public-ip show <resource group> <pip name>` parancs megjelenítéséhez a részletek a nyilvános IP-cím, amely tartalmazza a teljes Tartománynevét, a hálózati adaptert.

Lekérdezések továbbítása az Azure-ba nem felel meg igényeinek, ha meg kell adnia a saját DNS-megoldást.  A DNS-megoldást kell:

* Keresztül például adja meg a megfelelő állomásnév feloldása, [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). Ha DDNS-t használja, szüksége lehet letiltani a DNS-rekord kitakarítási. Az Azure DHCP-bérleteket nagyon hosszú, és kitakarítási túl korán DNS-rekordok eltávolíthatja.
* Adja meg a megfelelő rekurzív megoldás, hogy a külső tartománynevek feloldását.
* Elérhető-e (TCP és UDP 53-as porton) szolgál az ügyfelek lehet, és hozzáférhet az interneten.
* Védhető szemben elhárítani a külső ügynökök által jelentett fenyegetéseket az internetről.

> [!NOTE]
> A legjobb teljesítmény érdekében használata virtuális gépek az Azure DNS-kiszolgálók, tiltsa le az IPv6-alapú, és rendelje hozzá egy [példányszintű nyilvános IP-cím](../../virtual-network/virtual-networks-instance-level-public-ip.md) minden DNS-kiszolgáló virtuális géphez.  
>
>
