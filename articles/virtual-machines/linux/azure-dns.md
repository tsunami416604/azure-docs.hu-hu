---
title: A Linuxos virtuális gépek DNS-névfeloldási beállításai
description: Névfeloldási forgatókönyvek Linux os virtuális gépek hez az Azure IaaS-ban, beleértve a nyújtott DNS-szolgáltatásokat, a hibrid külső DNS-t és a Bring Your Own DNS-kiszolgálót.
author: RicksterCDN
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/19/2016
ms.author: rclaus
ms.openlocfilehash: 3d5ecaf67dcff182c7dace474b7bda45cdfd5c58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969321"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Dns-névfeloldási beállítások Linuxos virtuális gépekhez az Azure-ban
Az Azure alapértelmezés szerint biztosítja a DNS-névfeloldást az összes virtuális géphez, amelyek egyetlen virtuális hálózatban vannak. Saját DNS-névfeloldási megoldásmegvalósítása saját DNS-szolgáltatások konfigurálásával a virtuális gépeken, hogy az Azure üzemelteti. A következő forgatókönyvek segítségével kiválaszthatja a helyzetnek működő t.

* [Az Azure által biztosított névfeloldás](#name-resolution-that-azure-provides)
* [Névfeloldás a saját DNS-kiszolgálóhasználatával](#name-resolution-using-your-own-dns-server)

A használt névfeloldás típusa attól függ, hogy a virtuális gépeknek és a szerepkörpéldányoknak hogyan kell kommunikálniuk egymással.

Az alábbi táblázat forgatókönyveket és megfelelő névfeloldási megoldásokat mutat be:

| **Forgatókönyv** | **Megoldás** | **Utótag** |
| --- | --- | --- |
| Névfeloldás szerepkörpéldányok vagy virtuális gépek között ugyanabban a virtuális hálózatban |Az Azure által biztosított névfeloldás |állomásnév vagy teljesen minősített tartománynév (FQDN) |
| Névfeloldás szerepkörpéldányok vagy virtuális gépek között különböző virtuális hálózatokban |Ügyfél által felügyelt DNS-kiszolgálók, amelyek továbbítják a virtuális hálózatok közötti lekérdezéseket az Azure (DNS-proxy) általi feloldáshoz. Lásd: [Névfeloldás a saját DNS-kiszolgálóján.](#name-resolution-using-your-own-dns-server) |Csak teljes tartományontartomány |
| Helyszíni számítógépek és szolgáltatásnevek feloldása szerepkörpéldányokból vagy virtuális gépekből az Azure-ban |Ügyfél által felügyelt DNS-kiszolgálók (például helyszíni tartományvezérlő, helyi írásvédett tartományvezérlő vagy zónaletöltéssel szinkronizált másodlagos DNS-tartományvezérlő). Lásd: [Névfeloldás a saját DNS-kiszolgálóján.](#name-resolution-using-your-own-dns-server) |Csak teljes tartományontartomány |
| Az Azure állomásnevek feloldása helyszíni számítógépekről |Lekérdezések továbbítása a megfelelő virtuális hálózat ügyfél által felügyelt DNS-proxykiszolgálójára. A proxykiszolgáló továbbítja a lekérdezéseket az Azure-nak megoldásra. Lásd: [Névfeloldás a saját DNS-kiszolgálóján.](#name-resolution-using-your-own-dns-server) |Csak teljes tartományontartomány |
| A BELSŐ IP-k dns-ának megfordítása |[Névfeloldás a saját DNS-kiszolgálóhasználatával](#name-resolution-using-your-own-dns-server) |n/a |

## <a name="name-resolution-that-azure-provides"></a>Az Azure által biztosított névfeloldás
A nyilvános DNS-nevek feloldása mellett az Azure belső névfeloldást biztosít az azonos virtuális hálózatban lévő virtuális gépek és szerepkörpéldányok számára. Az Azure Resource Manageren alapuló virtuális hálózatokban a DNS-utótag konzisztens a virtuális hálózaton; az FQDN-re nincs szükség. A DNS-nevek hálózati csatolókártyákhoz és virtuális gépekhez is hozzárendelhetők. Bár az Azure által biztosított névfeloldás nem igényel semmilyen konfigurációt, nem a megfelelő választás az összes üzembe helyezési forgatókönyvhez, ahogy az előző táblában látható.

### <a name="features-and-considerations"></a>Jellemzők és szempontok
**Funkciók:**

* Nincs szükség konfigurációra az Azure által biztosított névfeloldás használatához.
* Az Azure által biztosított névfeloldási szolgáltatás magas rendelkezésre állású. Nem kell létrehoznia és kezelnie a saját DNS-kiszolgálóifürtjeit.
* Az Azure által biztosított névfeloldási szolgáltatás a saját DNS-kiszolgálókkal együtt használható a helyszíni és az Azure-állomásnevek feloldásához.
* A virtuális hálózatok virtuális gépei között a teljes tartománynév nélkül érhető el névfeloldás.
* Az automatikusan létrehozott nevek használata helyett olyan állomásneveket is használhat, amelyek a legjobban leírják a központi telepítéseket.

**Szempontok:**

* Az Azure által létrehozott DNS-utótag nem módosítható.
* Saját rekordjait nem lehet manuálisan regisztrálni.
* A WINS és a NetBIOS nem támogatott.
* Az állomásneveknek DNS-kompatibiliseknek kell lenniük.
    A nevek csak 0-9, a-z és "-" szavakat használhatnak, és nem kezdődhetnek vagy végződhetnek "-" kezdetűek vagy végződhetnek. Lásd az RFC 3696 2.
* A DNS-lekérdezési forgalom minden virtuális gépen szabályozható. A szabályozás nem befolyásolhatja a legtöbb alkalmazást.  Ha a kérelem szabályozása figyelhető meg, győződjön meg arról, hogy az ügyféloldali gyorsítótárazás engedélyezve van.  További információt az [Azure által biztosított névfeloldásból nyújt.](#getting-the-most-from-name-resolution-that-azure-provides)

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Az Azure által biztosított névfeloldásból a legtöbbet hozhatja ki
**Ügyféloldali gyorsítótárazás:**

Egyes DNS-lekérdezések nem kerülnek elküldésre a hálózaton keresztül. Az ügyféloldali gyorsítótárazás segít csökkenteni a késést, és javítja a hálózati inkonzisztenciák ellenálló képességét azáltal, hogy feloldja az ismétlődő DNS-lekérdezéseket a helyi gyorsítótárból. A DNS-rekordok egy Élettartam (Time-To-Live) (TTL) tartalmazó ttl-t tartalmaznak, amely lehetővé teszi, hogy a gyorsítótár a rekord frissességének befolyásolása nélkül a lehető legtovább tárolja a rekordot. Ennek eredményeképpen az ügyféloldali gyorsítótárazás a legtöbb helyzetben alkalmas.

Egyes Linux-disztribúciók alapértelmezés szerint nem tartalmazzák a gyorsítótárazást. Azt javasoljuk, hogy minden Linux virtuális géphez adjon hozzá egy gyorsítótárat, miután ellenőrizte, hogy nincs-e már helyi gyorsítótár.

Számos különböző DNS-gyorsítótárazási csomag, például a dnsmasq érhető el. A dnsmasq telepítésének lépései a leggyakoribb disztribúciókon:

**Ubuntu (a resolvconf-ot használja)**
  * Telepítse a dnsmasq csomagot ("sudo apt-get install dnsmasq").

**SUSE (netconf-ot használ):**
1. Telepítse a dnsmasq csomagot ("sudo zypper install dnsmasq").
2. Engedélyezze a dnsmasq szolgáltatást ("systemctl enable dnsmasq.service").
3. Indítsa el a dnsmasq szolgáltatást ("systemctl start dnsmasq.service").
4. Edit "/etc/sysconfig/network/config", és módosítsa NETCONFIG_DNS_FORWARDER="" a "dnsmasq".
5. Frissítse a feloldó.conf ("netconfig update") fájlt, hogy a gyorsítótárat helyi DNS-feloldóként állítsa be.

**CentOS by Rogue Wave Software (korábban OpenLogic; a NetworkManager-t használja)**
1. Telepítse a dnsmasq csomagot ("sudo yum install dnsmasq").
2. Engedélyezze a dnsmasq szolgáltatást ("systemctl enable dnsmasq.service").
3. Indítsa el a dnsmasq szolgáltatást ("systemctl start dnsmasq.service").
4. Adja hozzá a "/etc/dhclient-eth0.conf" fájlhoz a "prepend domain-name-servers 127.0.0.1;" értéket.
5. A hálózati szolgáltatás ("szolgáltatáshálózat újraindítása") újraindítása a gyorsítótár helyi DNS-feloldóként való beállításához

> [!NOTE]
> : A "dnsmasq" csomag csak egyike a sok DNS-gyorsítótárnak, amely Linux on elérhető. Mielőtt használná, ellenőrizze, hogy megfelel-e az igényeinek, és nincs-e más gyorsítótár telepítve.
>
>

**Ügyféloldali újrapróbálkozások**

A DNS elsősorban UDP protokoll. Mivel az UDP protokoll nem garantálja az üzenetek kézbesítését, maga a DNS protokoll kezeli az újrapróbálkozási logikát. Minden DNS-ügyfél (operációs rendszer) különböző újrapróbálkozási logikát mutathat a létrehozó preferenciáitól függően:

* A Windows operációs rendszerek egy másodperc múlva újra próbálkoznak, majd újabb két, négy és újabb négy másodperc után.
* Az alapértelmezett Linux telepítő öt másodperc után újrapróbálkozik.  Ezt úgy kell módosítani, hogy ezt egy másodperces időközönként ötször próbálja meg újra.  

A Linux virtuális gép aktuális beállításainak ellenőrzéséhez a "cat /etc/resolv.conf" és az "options" sor, például:

    options timeout:1 attempts:5

A resolv.conf fájl automatikusan generálódik, és nem szerkeszthető. A "beállítások" sort hozzáadó konkrét lépések eloszlásonként változnak:

**Ubuntu** (a resolvconf-ot használja)
1. Adja hozzá a "/etc/resolveconf/resolv.conf.d/head" beállítássort.
2. A frissítéshez futtassa a 'resolvconf -u' futtassa.

**SUSE** (netconf használatával)
1. Adja hozzá az "időtúloldal:1 kísérlet:5" értéket a NETCONFIG_DNS_RESOLVER_OPTIONS="" paraméterhez a "/etc/sysconfig/network/config" paraméterben.
2. A frissítéshez futtassa a "netconfig update" futtatását.

**CentOS by Rogue Wave Software (korábban OpenLogic)** (a NetworkManager-t használja)
1. Adja hozzá a "RES_OPTIONS="timeout:1 attempts:5"' értéket az '/etc/sysconfig/network' értékhez.
2. A frissítéshez futtassa a "szervizhálózat újraindítása" futtatását.

## <a name="name-resolution-using-your-own-dns-server"></a>Névfeloldás a saját DNS-kiszolgálóhasználatával
A névfeloldási igények túlmutathatnak az Azure által biztosított funkciókon. Előfordulhat például, hogy dns-feloldásra van szükség a virtuális hálózatok között. Ennek a forgatókönyvnek a lefedéséhez használhatja a saját DNS-kiszolgálóit.  

A virtuális hálózaton belüli DNS-kiszolgálók továbbíthatják a DNS-lekérdezéseket az Azure rekurzív feloldóihoz az ugyanazon virtuális hálózatban lévő állomásnevek feloldásához. Az Azure-ban futó DNS-kiszolgáló például válaszolhat a saját DNS-zónafájljaira vonatkozó DNS-lekérdezésekre, és továbbíthatja az összes többi lekérdezést az Azure-ba. Ez a funkció lehetővé teszi, hogy a virtuális gépek mindkét bejegyzést a zónafájlokban és az Azure által biztosított állomásnevekben (a továbbítón keresztül) lássák. Az Azure rekurzív feloldóihoz való hozzáférés a virtuális IP 168.63.129.16-on keresztül érhető el.

A DNS-továbbítás lehetővé teszi a DNS-feloldást a virtuális hálózatok között, és lehetővé teszi a helyszíni gépek számára az Azure által biztosított állomásnevek feloldását. A virtuális gép állomásnevének feloldásához a DNS-kiszolgáló virtuális gépének ugyanabban a virtuális hálózatban kell lennie, és úgy kell konfigurálnia, hogy a gazdagépnév-lekérdezéseket továbbítsa az Azure-ba. Mivel a DNS-utótag minden virtuális hálózatban más, feltételes továbbítási szabályok kal a megfelelő virtuális hálózatba küldhet DNS-lekérdezéseket. Az alábbi képen két virtuális hálózat és egy dns-feloldást végzett helyszíni hálózat látható a virtuális hálózatok között ezzel a módszerrel:

![DNS-feloldás a virtuális hálózatok között](./media/azure-dns/inter-vnet-dns.png)

Ha az Azure által biztosított névfeloldást használ, a belső DNS-utótag minden virtuális gép hez a DHCP használatával érhető el. Ha saját névfeloldási megoldást használ, ez az utótag nem lesz a virtuális gépekszámára, mert az utótag zavarja a többi DNS-architektúrát. Az FQDN által használt gépekre való hivatkozáshoz vagy a virtuális gépek utótagjának konfigurálásához a PowerShell vagy az API segítségével határozhatja meg az utótagot:

* Az Azure Resource Manager által felügyelt virtuális hálózatok esetében az utótag a [hálózati kártya](https://msdn.microsoft.com/library/azure/mt163668.aspx) erőforrásán keresztül érhető el. A `azure network public-ip show <resource group> <pip name>` parancs futtatásával is megjelenítheti a nyilvános IP adatait, amely tartalmazza a hálózati adapter teljes tartománynát.

Ha a lekérdezések továbbítása az Azure-ba nem felel meg az igényeinek, saját DNS-megoldást kell biztosítania.  A DNS-megoldásnak a következőket kell tennie:

* Adja meg a megfelelő állomásnév-feloldást, például a [DDNS-en](../../virtual-network/virtual-networks-name-resolution-ddns.md)keresztül. Ha DDNS-t használ, előfordulhat, hogy le kell tiltania a DNS-rekordkitakarítást. Az Azure DHCP-bérletei nagyon hosszúak, és a takarítás idő előtt eltávolíthatja a DNS-rekordokat.
* A külső tartománynevek feloldásának lehetővé teszi a megfelelő rekurzív felbontást.
* Legyen elérhető (TCP és UDP az 53-as porton) az általa kiszolgálott ügyfelektől, és legyen képes elérni az internetet.
* A külső ügynökök által jelentett fenyegetések csökkentése érdekében védhető az internetről való hozzáférés ellen.

> [!NOTE]
> A legjobb teljesítmény érdekében, ha az Azure DNS-kiszolgálókon virtuális gépeket használ, tiltsa le az IPv6 protokollt, és rendeljen [egy példányszintű nyilvános IP-címet minden DNS-kiszolgáló](../../virtual-network/virtual-networks-instance-level-public-ip.md) virtuális gépéhez.  
>
>
