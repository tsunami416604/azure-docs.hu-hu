---
title: "A virtuális gépek és a Szerepkörpéldányok felbontás"
description: "Megoldási forgatókönyvek nevet Azure IaaS, hibrid megoldások között különböző felhőalapú szolgáltatások, az Active Directory és a saját DNS-kiszolgáló használatával "
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 5d73edde-979a-470a-b28c-e103fcf07e3e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2016
ms.author: jdial
ms.openlocfilehash: 5a298f535308cff90ddd249594b7bb5e36909867
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="name-resolution-for-vms-and-role-instances"></a>A virtuális gépek és szerepkörpéldányok névfeloldása
Attól függően, hogyan használja a Azure hibrid megoldások, IaaS és PaaS üzemeltetéséhez szükség lehet ahhoz, hogy a virtuális gépek és a szerepkörpéldányok, amelyekkel kommunikálhatnak egymással. Bár ez a kommunikáció IP-címek használatával teheti meg, akkor jóval egyszerűbb, használja a nevét, amely jól megjegyezhető, és ne módosítsa. 

Ha a szerepkörpéldányok és a virtuális gépek Azure-ban üzemeltetett kell tartománynevek belső IP-címek, használhatnak módszerek egyikét:

* [Azure által biztosított névfeloldás](#azure-provided-name-resolution)
* [Névfeloldás a saját DNS-kiszolgáló segítségével](#name-resolution-using-your-own-dns-server) (ami előfordulhat, hogy továbbítsa az Azure által biztosított DNS-kiszolgálók) 

A névfeloldás használata típusa attól függ, hogyan a virtuális gépek és a szerepkörpéldányok kommunikálnia kell egymáshoz.

**A következő táblázat bemutatja a forgatókönyvek és a megfelelő név feloldása megoldások:**

| **Scenario** | **Megoldás** | **Suffix** |
| --- | --- | --- |
| Névfeloldás szerepkörpéldányokat vagy a felhőalapú szolgáltatás- vagy virtuális hálózat található virtuális gépek között |[Azure által biztosított névfeloldás](#azure-provided-name-resolution) |állomásnév vagy teljes Tartományneve |
| Nevek feloldása az Azure App Service (Web App, Function, Botot, stb.) a szerepkörpéldányok vagy a virtuális gépek virtuális hálózat segítségével található, az azonos virtuális hálózatban |Ügyfél által felügyelt DNS-kiszolgálók között a névfeloldás az Azure-ban (DNS-proxy) vnetek lekérdezések továbbítása.  Lásd: [névfeloldáshoz a saját DNS-kiszolgáló](#name-resolution-using-your-own-dns-server) |Kizárólag az FQDN esetében |
| Névfeloldás szerepkörpéldányokat vagy különböző virtuális hálózatokon lévő virtuális gépek között |Ügyfél által felügyelt DNS-kiszolgálók között a névfeloldás az Azure-ban (DNS-proxy) vnetek lekérdezések továbbítása.  Lásd: [névfeloldáshoz a saját DNS-kiszolgáló](#name-resolution-using-your-own-dns-server) |Kizárólag az FQDN esetében |
| A helyi számítógép és a szolgáltatás neve a szerepkörpéldányok vagy az Azure virtuális gépek feloldását. |Ügyfél által felügyelt DNS-kiszolgálók (például a helyi tartományvezérlő, helyi írásvédett tartományvezérlő vagy másodlagos DNS zónaletöltés használatával szinkronizálva).  Lásd: [névfeloldáshoz a saját DNS-kiszolgáló](#name-resolution-using-your-own-dns-server) |Kizárólag az FQDN esetében |
| A helyszíni számítógépek Azure állomásnevének feloldása |Az ügyfél által felügyelt DNS-proxy kiszolgáló a megfelelő virtuális továbbítási kérelmek a proxykiszolgáló továbbítja lekérdezések Azure a feloldásához. Lásd: [névfeloldáshoz a saját DNS-kiszolgáló](#name-resolution-using-your-own-dns-server) |Kizárólag az FQDN esetében |
| Névkeresési DNS, a belső IP-címek |[Névfeloldás a saját DNS-kiszolgáló használatával](#name-resolution-using-your-own-dns-server) |n/a |
| Névfeloldás virtuális gépekre vagy szerepkörpéldányokra található különböző felhőszolgáltatások, nem a virtuális hálózat között |Nem alkalmazható. Virtuális gépek és a szerepkörpéldányok különböző felhőszolgáltatások közötti kapcsolat nem támogatja a virtuális hálózatokon kívül. |n/a |

## <a name="azure-provided-name-resolution"></a>Azure által biztosított névfeloldás
Nyilvános DNS-nevek feloldása, valamint Azure belső névfeloldást biztosít a virtuális gépek és a szerepkörpéldányok, amelyek az azonos virtuális hálózatban vagy a felhőalapú szolgáltatás.  Virtuális gépek/példány egy felhőalapú szolgáltatás megosztani az azonos DNS-utótag (így egyedül állomásnév is elegendő), de a klasszikus virtuális hálózatok másik felhőt a szolgáltatások más DNS-utótag van, ezért a teljesen minősített Tartományneve nincs szükség másik felhőszolgáltatások közötti névfeloldás.  A virtuális hálózatokon, a Resource Manager üzembe helyezési modellel a DNS-utótag összhangban a virtuális hálózaton (így a teljes tartománynév nem kötelező), és a DNS-nevek rendelhetők hozzá hálózati adapter és a virtuális gépek. Bár az Azure által biztosított névfeloldás kell konfigurálni, akkor nem a megfelelő választás az összes központi telepítési forgatókönyve esetén az előző táblázatban látható módon.

> [!NOTE]
> Webes és feldolgozói szerepkörök esetén a belső IP-címek szerepkörpéldányt beállítani az Azure Service Management REST API használatával szerepkör nevét és példányszámát száma alapján is elérhető. További információkért lásd: [Service Management REST API-referencia](https://msdn.microsoft.com/library/azure/ee460799.aspx).
> 
> 

### <a name="features-and-considerations"></a>Szolgáltatások és szempontok
**Szolgáltatások:**

* Könnyű használat: nincs konfiguráció szükséges ahhoz, hogy az Azure által biztosított névfeloldás használata.
* Az Azure által biztosított névfeloldási szolgáltatás magas rendelkezésre állású, így meg lehet spórolni létrehozásához és fürtöket a saját DNS-kiszolgálók felügyeletéhez szükség.
* Használható együtt a saját DNS-kiszolgálók a helyszíni és az Azure állomásnevek.
* Névfeloldás szerepkör példányok/virtuális gépek közötti belül az azonos felhőalapú szolgáltatás teljes tartománynévhez anélkül valósul meg.
* A Resource Manager üzembe helyezési modellben, anélkül, hogy a teljes tartománynévhez szükség használó virtuális hálózatok közötti névfeloldás valósul meg. Virtuális hálózatok a klasszikus üzembe helyezési modellben a teljes Tartománynevet igényelnek, különböző felhőszolgáltatások a nevek feloldásakor. 
* Használhatja a központi telepítések jellemző állomásnevek ahelyett, hogy működik-e automatikusan létrehozott névvel.

**Szempontok:**

* Nem lehet módosítani az Azure által létrehozott DNS-utótagot.
* Nem lehet manuálisan regisztrálni a saját rögzíti.
* WINS- és NetBIOS nem támogatottak. (A Windows Intézőben a virtuális gépek nem látható.)
* Állomásnevek kell lennie a DNS-kompatibilis (kell használniuk, csak 0-9, a – z és a "-", és nem kezdődhet vagy végződhet egy "-". Című rész RFC 3696 2.)
* DNS-lekérdezés forgalom az egyes virtuális gépek folyamatban van. Ez nem befolyásolja a legtöbb alkalmazást.  Ha kérelem szabályozása, győződjön meg arról, hogy engedélyezve van-e az ügyféloldali gyorsítótárazás.  További információkért lásd: [lekérése a legtöbb az Azure által biztosított névfeloldás](#Getting-the-most-from-Azure-provided-name-resolution).
* Csak az első 180 felhőszolgáltatások a virtuális gépek minden virtuális hálózathoz, a klasszikus üzembe helyezési modellben van regisztrálva. Ez nem vonatkozik a Resource Manager üzembe helyezési modellel virtuális hálózatokra.

### <a name="getting-the-most-from-azure-provided-name-resolution"></a>A legtöbb Azure által biztosított névfeloldás lekérése
**Ügyféloldali gyorsítótárazás:**

Nem minden DNS-lekérdezést kell küldhető el a hálózaton keresztül.  Ügyféloldali gyorsítótárazás segít a késés csökkentésére, és növelheti a hálózati blips rugalmasság feloldása ismétlődő DNS-lekérdezések a helyi gyorsítótárból.  DNS-rekordokat tartalmaz egy Time-Live (TTL), amely lehetővé teszi, hogy a gyorsítótár nélkül érintő rekord frissesség, így a legtöbb esetben a megfelelő ügyféloldali gyorsítótárazás, amíg a rekord tárolására.

Az alapértelmezett Windows DNS-ügyfél a DNS-gyorsítótár beépített rendelkezik.  Egyes Linux disztribúciókkal kihagyása alapértelmezés szerint gyorsítótárazás, javasolt, hogy egy vehető fel minden egyes Linux virtuális gép (miután ellenőrizte, hogy a helyi gyorsítótárba még nem).

Számos különböző DNS-, gyorsítótárazás elérhető csomagok. Például dnsmasq. Az alábbi lépéseket a leggyakrabban használt disztribúciókkal dnsmasq telepítése listában:

* **Ubuntu (használ resolvconf)**:
  * csak a telepítéséhez dnsmasq ("sudo apt-get-telepítés dnsmasq").
* **SUSE (használ netconf)**:
  * a dnsmasq telepítéséhez ("sudo zypper telepítés dnsmasq") 
  * Engedélyezze a dnsmasq szolgáltatást ("systemctl engedélyezése dnsmasq.service") 
  * Indítsa el a dnsmasq szolgáltatást ("systemctl start dnsmasq.service") 
  * Szerkesztés "/ etc/sysconfig/hálózati/config", és módosítsa a NETCONFIG_DNS_FORWARDER = "" a "dnsmasq"
  * a helyi DNS-feloldási frissíti a resolv.conf ("netconfig update"), a gyorsítótár beállítása
* **OpenLogic (használja a NetworkManager)**:
  * a dnsmasq telepítéséhez ("sudo yum telepítés dnsmasq")
  * Engedélyezze a dnsmasq szolgáltatást ("systemctl engedélyezése dnsmasq.service")
  * Indítsa el a dnsmasq szolgáltatást ("systemctl start dnsmasq.service")
  * add “prepend domain-name-servers 127.0.0.1;” to “/etc/dhclient-eth0.conf”
  * Indítsa újra a hálózati szolgáltatást ("szolgáltatás hálózati restart"), a gyorsítótár állítja be a helyi DNS-feloldó

> [!NOTE]
> A "dnsmasq" csomag nincs csak az egyik elérhető Linux számos DNS gyorsítótárat.  Annak használata előtt ellenőrizze az adott igényeknek megfelelően való alkalmasságát, és nincs más gyorsítótár telepítve van.
> 
> 

**Ügyféloldali újrapróbálkozások:**

DNS elsősorban UDP protokoll.  Mivel az UDP protokoll üzenetkézbesítést nem garantálja, újrapróbálkozási logika kezelése a DNS protokoll.  Minden DNS-ügyfél (operációs rendszer) is virágot különböző újrapróbálkozási logika attól függően, hogy a creators beállítások:

* Windows operációs rendszerek újrapróbálkozás után 1 második és majd újra egy másik 2, 4, és egy másik 4 másodperc. 
* Az alapértelmezett Linux telepítő újrapróbálkozások öt másodperc múlva.  Javasoljuk, hogy ezen 5-ször, 1 második időközönként megpróbálja majd újból.  

A "cat /etc/resolv.conf" parancs segítségével ellenőrizze a Linux virtuális gép aktuális beállításokat, majd tekintse meg a "beállítások" sor, például:

    options timeout:1 attempts:5

A resolv.conf fájl van általában automatikusan létrehozott, és nem szerkeszthető.  Hozzáadása a "beállítások" sor lépései eltérőek lehetnek, a distro függően:

* **Ubuntu** (használja a resolvconf):
  * Adja a beállítások sort "/ etc/resolveconf/resolv.conf.d/head" 
  * Futtassa a 'resolvconf -u' frissítése
* **SUSE** (használja a netconf):
  * Adja hozzá a NETCONFIG_DNS_RESOLVER_OPTIONS 'timeout:1 kísérletek: 5' = "" a "/ etc/sysconfig/hálózati/config" paraméter 
  * Futtassa a "netconfig update" frissítése
* **OpenLogic** (használja a NetworkManager):
  * hozzáadása "echo"beállítások timeout:1 kísérletek: 5"" "/ etc/NetworkManager/dispatcher.d/11-dhclient" 
  * Futtassa a "szolgáltatás hálózati újraindítás" frissítése

## <a name="name-resolution-using-your-own-dns-server"></a>Névfeloldás a saját DNS-kiszolgáló használatával
Számos olyan helyzetekben, ahol a névfeloldási szükségleteit lehet, hogy nyissa meg a szolgáltatások, Azure által biztosított, például ha túl használja az Active Directory-tartományok, vagy ha DNS-feloldás virtuális hálózatok közötti.  Ezek a forgatókönyvek fedik le, Azure lehetővé teszi a saját DNS-kiszolgálók használata.  

A virtuális hálózaton belül DNS-kiszolgálók DNS-lekérdezések továbbíthatja Azure rekurzív feloldókat megoldásához állomásnevek adott virtuális hálózaton belül.  A tartományvezérlőn (DC) Azure-beli például a tartomány DNS-lekérdezései válaszolnak, és a további lekérdezések továbbítása az Azure-bA.  Ez lehetővé teszi a virtuális gépek, a helyszíni erőforrások (keresztül a tartományvezérlő) és az Azure által biztosított állomásnevek (keresztül a továbbító).  A virtuális IP-cím 168.63.129.16 Azure rekurzív feloldókat hozzáférést biztosítja.

DNS-továbbítást is lehetővé teszi az Inter-virtuális hálózat DNS-feloldás, és lehetővé teszi, hogy a helyszíni gépeket Azure által biztosított állomásnevek megoldásához.  Ahhoz, hogy oldja meg a virtuális gép állomásnevét, a DNS-kiszolgáló virtuális gép ugyanazon a virtuális hálózaton kell lennie, és úgy, hogy az Azure előre állomásnév lekérdezések.  Mivel a DNS-utótag minden egyes virtuális különböző, feltételes továbbítás szabályok használatával a névfeloldás a megfelelő virtuális hálózat DNS-lekérdezéseket küldjön.  Az alábbi ábrán két virtuális hálózatok és az Inter-virtuális hálózat DNS-feloldás ezzel a módszerrel történt a helyi hálózaton.  Egy példa DNS-továbbító érhető el a [Azure gyors üzembe helyezés sablontárban](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) és [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

![Inter-virtual network DNS](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Ha Azure által biztosított névfeloldás, olyan belső DNS-utótagot használ (*. internal.cloudapp.net) DHCP használatával virtuális gépek számára.  Ez lehetővé teszi az állomásnév-feloldási, az állomásnév-rekordok vannak a internal.cloudapp.net zónában.  A saját nevet névfeloldási megoldás használata esetén az IDN-utótag nincs megadva a virtuális gépekhez, mert azt más DNS-architektúrák (például a tartományhoz csatlakoztatott forgatókönyvek) gátolja.  Ehelyett azt adja meg a nem működő helyőrző (reddog.microsoft.com).  

Ha szükséges, a belső DNS-utótag lehet meghatározni a PowerShell vagy a API:

* Virtuális hálózatok a Resource Manager üzembe helyezési modellel, az utótaghoz érhető el keresztül a [hálózati kártya](https://msdn.microsoft.com/library/azure/mt163668.aspx) erőforrás keresztül vagy a [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) parancsmag.    
* A klasszikus üzembe helyezési modellek utótag megtalálható a [telepítési API beszerzése](https://msdn.microsoft.com/library/azure/ee460804.aspx) hívás keresztül vagy a [Get-AzureVM-Debug](https://msdn.microsoft.com/library/azure/dn495236.aspx) parancsmag.

Ha a lekérdezések továbbítása az Azure-ba nem megfelel az igényeinek, szüksége lesz a saját DNS-megoldást nyújt.  A DNS-megoldást kell:

* Segítségével például adja meg a megfelelő állomásnév-feloldási [DDNS](virtual-networks-name-resolution-ddns.md).  Ne feledje, ha letiltja a DNS rekord kitakarítási vagy Azure DHCP-bérletek sem nagyon hosszú és kitakarítási szeretne DDNS használatával távolíthatja el a DNS-rekordok idő előtt. 
* Adja meg a megfelelő rekurziót külső tartomány nevek feloldását engedélyezéséhez.
* Elérhető (TCP és UDP 53-as porton) szolgál az ügyfelekről és fogja tudni érni az internetet.
* Védhető szemben az internetről, külső ügynökök által jelentett veszélyek mérséklése.

> [!NOTE]
> A legjobb teljesítmény érdekében Azure virtuális gépek használata a DNS-kiszolgálóként, IPv6 le kell tiltani és egy [példányszintű nyilvános IP-cím](virtual-networks-instance-level-public-ip.md) minden DNS-kiszolgáló virtuális gép legyen hozzárendelve.  Ha úgy dönt, hogy a Windows Server a DNS-kiszolgálóként használni [Ez a cikk](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) biztosít további Teljesítményelemzés és optimalizálási lehetőségeit.
> 
> 

### <a name="specifying-dns-servers"></a>DNS-kiszolgálók megadása
A saját DNS-kiszolgálók, az Azure lehetővé teszi a virtuális hálózati időszakonkénti hálózati illesztőt (Resource Manager), vagy több DNS-kiszolgálók / felhőalapú szolgáltatás (klasszikus).  Egy felhőalapú szolgáltatás/hálózati illesztőhöz megadott DNS-kiszolgálók elsőbbséget élveznek a virtuális hálózathoz megadott beolvasása.

> [!NOTE]
> Hálózati kapcsolat tulajdonságai, például a DNS-kiszolgáló IP-címek, nem lehet szerkeszteni közvetlenül a Windows virtuális gépeken belül, akkor előfordulhat, hogy első újramegfeleltetés során szolgáltatás javítandó a virtuális hálózati adapter lekérdezi cseréje. 
> 
> 

Használata a Resource Manager üzembe helyezési modellel, DNS-kiszolgálók adható meg a portál API/sablonok ([vnet](https://msdn.microsoft.com/library/azure/mt163661.aspx), [nic](https://msdn.microsoft.com/library/azure/mt163668.aspx)) vagy a PowerShell használatával ([vnet](https://msdn.microsoft.com/library/mt603657.aspx), [nic](https://msdn.microsoft.com/library/mt619370.aspx)).

Ha a klasszikus üzembe helyezési modellt használ, a virtuális hálózat DNS-kiszolgálók adható meg a portálon vagy [a *hálózati konfiguráció* fájl](https://msdn.microsoft.com/library/azure/jj157100).  A felhőszolgáltatások, a DNS-kiszolgálókon keresztül megadott [a *szolgáltatáskonfiguráció* fájl](https://msdn.microsoft.com/library/azure/ee758710) vagy a PowerShellben ([New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)).

> [!NOTE]
> Ha egy virtuális hálózat vagy virtuális gépet, amely már telepítve van a DNS-beállításainak módosításához kell egyes érintett virtuális gépek a módosítások életbe léptetéséhez indítsa újra.
> 
> 

## <a name="next-steps"></a>További lépések
Erőforrás-kezelő telepítési modell:

* [A virtuális hálózat létrehozása vagy módosítása](https://msdn.microsoft.com/library/azure/mt163661.aspx)
* [Létrehozni vagy frissíteni a hálózati kártya](https://msdn.microsoft.com/library/azure/mt163668.aspx)
* [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx)
* [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx)

Klasszikus telepítési modell:

* [Az Azure szolgáltatás konfigurációs séma](https://msdn.microsoft.com/library/azure/ee758710)
* [Virtuális hálózati konfiguráció séma](https://msdn.microsoft.com/library/azure/jj157100)
* [Virtuális hálózat konfigurálása a hálózati konfigurációs fájl segítségével](virtual-networks-using-network-configuration-file.md) 

