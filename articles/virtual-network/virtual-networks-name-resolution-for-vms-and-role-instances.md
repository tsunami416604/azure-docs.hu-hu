---
title: "A virtuális gépek és a szerepkörpéldányok névfeloldás"
description: "Név feloldása forgatókönyvek Azure IaaS, hibrid megoldások között különböző felhőalapú szolgáltatások, az Active Directory és a saját DNS-kiszolgálót használ."
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
ms.date: 02/14/2018
ms.author: jdial
ms.openlocfilehash: 5ea3e4ad68fd37ccaa6e081febe4827bdb2e196d
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="name-resolution-for-vms-and-role-instances"></a>A virtuális gépek és a szerepkörpéldányok névfeloldás

Attól függően, hogyan használja a Azure hibrid megoldások, IaaS és PaaS üzemeltetéséhez szükség lehet ahhoz, hogy a virtuális gépek és a szerepkörpéldányok, amelyekkel kommunikálhatnak egymással. Bár ez a kommunikáció IP-címek használatával teheti meg, akkor jóval egyszerűbb, használja a nevét, amely jól megjegyezhető, és ne módosítsa. 

Ha a szerepkörpéldányok és a virtuális gépek Azure-ban üzemeltetett kell tartománynevek belső IP-címek, használhatnak módszerek egyikét:

* [Azure által biztosított névfeloldás](#azure-provided-name-resolution)
* [Névfeloldás a saját DNS-kiszolgáló segítségével](#name-resolution-using-your-own-dns-server) (ami előfordulhat, hogy továbbítsa az Azure által biztosított DNS-kiszolgálók) 

A névfeloldás használata típusa attól függ, hogyan a virtuális gépek és a szerepkörpéldányok kommunikálnia kell egymáshoz. A következő táblázat bemutatja a forgatókönyvek és a megfelelő név feloldása megoldások:

| **Scenario** | **Megoldás** | **Suffix** |
| --- | --- | --- |
| Név feloldása szerepkörpéldányokat vagy a felhőalapú szolgáltatás- vagy virtuális hálózat található virtuális gépek között. |[Azure által biztosított névfeloldás](#azure-provided-name-resolution) |állomásnév vagy teljes Tartományneve |
| Nevek feloldása az Azure App Service (Web App, függvény vagy Botot) a szerepkörpéldányok vagy a virtuális gépek virtuális hálózati integráció használatával található, az azonos virtuális hálózatban. |Ügyfél által felügyelt DNS-kiszolgálók (DNS-proxy) Azure általi megoldása virtuális hálózatok közötti lekérdezések továbbítása. Lásd: [névfeloldáshoz a saját DNS-kiszolgáló](#name-resolution-using-your-own-dns-server). |Kizárólag az FQDN esetében |
| Névfeloldás a szerepkörpéldányok vagy különböző virtuális hálózatokon lévő virtuális gépek között. |Ügyfél által felügyelt DNS-kiszolgálók (DNS-proxy) Azure általi megoldása virtuális hálózatok közötti lekérdezések továbbítása. Lásd: [névfeloldáshoz a saját DNS-kiszolgáló](#name-resolution-using-your-own-dns-server). |Kizárólag az FQDN esetében |
| Hogy a névfeloldás az App Service Web Apps az azonos virtuális hálózaton található virtuális gépek. |Ügyfél által felügyelt DNS-kiszolgálók (DNS-proxy) Azure általi megoldása virtuális hálózatok közötti lekérdezések továbbítása. Lásd: [névfeloldáshoz a saját DNS-kiszolgáló](#name-resolution-using-your-own-dns-server). |Kizárólag az FQDN esetében |
| Névfeloldás az App Service Web Apps a különböző virtuális hálózatban lévő virtuális gépekhez. |Ügyfél által felügyelt DNS-kiszolgálók (DNS-proxy) Azure általi megoldása virtuális hálózatok közötti lekérdezések továbbítása. Lásd: [névfeloldáshoz a saját DNS-kiszolgáló](#name-resolution-using-your-own-dns-server-for-web-apps). |Kizárólag az FQDN esetében |
| A helyi számítógép és a szolgáltatás neve a szerepkörpéldányok vagy az Azure virtuális gépek feloldását. |Ügyfél által felügyelt DNS-kiszolgálók (a helyi tartományvezérlő, helyi írásvédett tartományvezérlő vagy másodlagos DNS zónaletöltés, például használata szinkronizálva). Lásd: [névfeloldáshoz a saját DNS-kiszolgáló](#name-resolution-using-your-own-dns-server). |Kizárólag az FQDN esetében |
| A helyszíni számítógépek Azure állomásnevének feloldása. |Az ügyfél által felügyelt DNS-proxy kiszolgáló a megfelelő virtuális hálózat továbbítási kérelmek a proxykiszolgáló továbbítja lekérdezések Azure a feloldásához. Lásd: [névfeloldáshoz a saját DNS-kiszolgáló](#name-resolution-using-your-own-dns-server). |Kizárólag az FQDN esetében |
| Névkeresési DNS belső IP-címekről. |[Névfeloldás a saját DNS-kiszolgáló segítségével](#name-resolution-using-your-own-dns-server). |Nem alkalmazható |
| Névfeloldás közötti virtuális gépekre vagy szerepkörpéldányokra különböző felhőszolgáltatások, nem a virtuális hálózat található. |Nem alkalmazható. Virtuális gépek és a szerepkörpéldányok különböző felhőszolgáltatások közötti kapcsolat nem támogatja a virtuális hálózatokon kívül. |Nem alkalmazható|

## <a name="azure-provided-name-resolution"></a>Azure által biztosított névfeloldás

Nyilvános DNS-nevek feloldása, valamint Azure belső névfeloldást biztosít a virtuális gépek és a szerepkörpéldányok, amelyek az azonos virtuális hálózatban vagy a felhőalapú szolgáltatás. Virtuális gépek/példány egy felhőalapú szolgáltatás megosztani az azonos DNS-utótag (így egyedül állomásnév is elegendő), de a klasszikus virtuális hálózatok másik felhőt a szolgáltatások más DNS-utótag van, ezért a teljesen minősített Tartományneve nincs szükség másik felhőszolgáltatások közötti névfeloldás. A virtuális hálózatokon, a Resource Manager üzembe helyezési modellel a DNS-utótag összhangban a virtuális hálózaton (így a teljes tartománynév nem kötelező), és a DNS-nevek rendelhetők hozzá hálózati adapter és a virtuális gépek. Bár az Azure által biztosított névfeloldás kell konfigurálni, akkor nem a megfelelő választás az összes központi telepítési forgatókönyve esetén az előző táblázatban látható módon.

> [!NOTE]
> Webes és feldolgozói szerepkörök használata esetén a belső IP-címek szerepkörpéldányt beállítani az Azure Service Management REST API használatával szerepkör nevét és példányszámát száma alapján is elérhető. További információkért lásd: [Service Management REST API-referencia](https://msdn.microsoft.com/library/azure/ee460799.aspx).
> 
> 

### <a name="features"></a>Szolgáltatások

* Könnyű használat: nincs konfiguráció szükséges ahhoz, hogy az Azure által biztosított névfeloldás használata.
* Az Azure által biztosított névfeloldási szolgáltatás magas rendelkezésre állású, így meg lehet spórolni létrehozásához és fürtöket a saját DNS-kiszolgálók felügyeletéhez szükség.
* Használható együtt a saját DNS-kiszolgálók a helyszíni és az Azure állomásnevek.
* Névfeloldás szerepkör példányok/virtuális gépek közötti belül az azonos felhőalapú szolgáltatás FQDN-t nélkül valósul meg.
* A Resource Manager üzembe helyezési modellben nélkül FQDN-t használó virtuális hálózatok közötti névfeloldás valósul meg. Virtuális hálózatok a klasszikus üzembe helyezési modellel egy teljes Tartománynevet igényelnek, abban az esetben, ha a különböző felhőszolgáltatások nevek feloldása. 
* Használhatja a központi telepítések jellemző állomásnevek ahelyett, hogy működik-e automatikusan létrehozott névvel.

### <a name="considerations"></a>Megfontolandó szempontok

* Nem lehet módosítani az Azure által létrehozott DNS-utótagot.
* Nem lehet manuálisan regisztrálni a saját rögzíti.
* WINS- és NetBIOS nem támogatott (a Windows Intézőben a virtuális gépek nem látható).
* Állomásnevek kell lennie a DNS-kompatibilis. Neveket kell használnia csak 0-9, a – z és a "-", és nem kezdődhet vagy végződhet egy "-". Lásd: RFC 3696 szakasz 2.
* DNS-lekérdezés forgalom az egyes virtuális gépek folyamatban van. Sávszélesség-szabályozás nem befolyásolja a legtöbb alkalmazást. Ha kérelem szabályozása, győződjön meg arról, hogy engedélyezve van-e az ügyféloldali gyorsítótárazás. További információkért lásd: [lekérése a legtöbb az Azure által biztosított névfeloldás](#Getting-the-most-from-Azure-provided-name-resolution).
* Csak az első 180 felhőszolgáltatások a virtuális gépek minden virtuális hálózathoz, a klasszikus üzembe helyezési modellben van regisztrálva. Ez a korlátozás nem vonatkozik a virtuális hálózatok a Resource Manager üzembe helyezési modellel.

## <a name="dns-client-configuration"></a>DNS-ügyfél konfigurációja

### <a name="client-side-caching"></a>Ügyféloldali gyorsítótárazás

Nem minden DNS-lekérdezést kell küldhető el a hálózaton keresztül. Ügyféloldali gyorsítótárazás segít a késés csökkentésére, és növelheti a hálózati blips rugalmasság feloldása ismétlődő DNS-lekérdezések a helyi gyorsítótárból. DNS-rekordokat tartalmaz egy idő-live (TTL) lehetővé teszi a gyorsítótár, amíg a rekord tárolására, ügyféloldali gyorsítótárazás alkalmas a legtöbb esetben a rekord frissesség gyakorolt hatás nélkül.

Az alapértelmezett Windows DNS-ügyfél a DNS-gyorsítótár beépített rendelkezik. Egyes Linux disztribúciókkal gyorsítótárazás alapértelmezés szerint nem tartalmaznak. Ad hozzá egy DNS-gyorsítótár egyes Linux virtuális gépek (miután ellenőrizte, hogy a helyi gyorsítótárba még nem), ajánlott.

Számos különböző DNS-, gyorsítótárazás elérhető csomagok. Például dnsmasq. Az alábbi lépéseket a leggyakrabban használt disztribúciókkal dnsmasq telepítése listában:

* **Ubuntu (használ resolvconf)**:
  * Telepítse a dnsmasq csomag `sudo apt-get install dnsmasq`.
* **SUSE (használ netconf)**:
  * Telepítse a dnsmasq csomag `sudo zypper install dnsmasq`.
  * Engedélyezze a dnsmasq szolgáltatást a `systemctl enable dnsmasq.service`. 
  * Indítsa el a dnsmasq szolgáltatást a `systemctl start dnsmasq.service`. 
  * Szerkesztés **/etc/sysconfig/network/config** , és módosítsa *NETCONFIG_DNS_FORWARDER = ""* való *dnsmasq*.
  * Frissítse a resolv.conf `netconfig update` beállítása a gyorsítótár, a helyi DNS-feloldási.
* **OpenLogic (használja a NetworkManager)**:
  * Telepítse a dnsmasq csomag `sudo yum install dnsmasq`.
  * Engedélyezze a dnsmasq szolgáltatást a `systemctl enable dnsmasq.service`.
  * Indítsa el a dnsmasq szolgáltatást a `systemctl start dnsmasq.service`.
  * Adja hozzá *-neve-tartománykiszolgálók 127.0.0.1; illesztenie* való **/etc/dhclient-eth0.conf**.
  * Indítsa újra a hálózati szolgáltatást a `service network restart` beállítása a gyorsítótár, a helyi DNS-feloldó.

> [!NOTE]
> A "dnsmasq" csomag nincs csak az egyik elérhető Linux számos DNS gyorsítótárat. Annak használata előtt ellenőrizze az adott igényeknek megfelelően való alkalmasságát, és nincs más gyorsítótár telepítve van.
> 
> 
    
### <a name="client-side-retries"></a>Ügyféloldali újrapróbálkozások

DNS elsősorban UDP protokoll. Mivel az UDP protokoll üzenetkézbesítést nem garantálja, újrapróbálkozási logika kezelése a DNS protokoll. Minden DNS-ügyfél (operációs rendszer) is virágot különböző újrapróbálkozási logika attól függően, hogy a létrehozásához használt beállítások:

* Windows operációs rendszerek újrapróbálkozás után egy második, és újra majd egy másik 2, 4, és egy másik négy másodperc. 
* Az alapértelmezett Linux telepítő újrapróbálkozások öt másodperc múlva. Az újrapróbálkozási módosítása ötször a 1 másodperces időközönként, ajánlott.

Ellenőrizze az aktuális beállításait a Linux virtuális gép és `cat /etc/resolv.conf`. Tekintse meg a *beállítások* . sor, például:

```bash
options timeout:1 attempts:5
```

A resolv.conf fájl van általában automatikusan létrehozott, és nem szerkeszthető. A megadott történő hozzáadásának lépései a *beállítások* distro változhat sor:

* **Ubuntu** (használja a resolvconf):
  * Adja a beállítások sort **/etc/resolveconf/resolv.conf.d/head**.
  * Futtatás `resolvconf -u` frissítéséhez.
* **SUSE** (használja a netconf):
  * Adja hozzá *timeout:1 kísérletek: 5* számára a **NETCONFIG_DNS_RESOLVER_OPTIONS = ""** paraméterének **/etc/sysconfig/network/config**. 
  * Futtatás `netconfig update` frissítéséhez.
* **OpenLogic** (használja a NetworkManager):
  * Adja hozzá *echo "lehetőségek timeout:1 kísérletek: 5"* való **/etc/NetworkManager/dispatcher.d/11-dhclient**. 
  * Frissítse `service network restart`.

## <a name="name-resolution-using-your-own-dns-server"></a>Névfeloldás a saját DNS-kiszolgáló használatával

### <a name="vms-and-role-instances"></a>Virtuális gépek és a szerepkör-példányoknál

Számos olyan helyzetekben, ahol a névfeloldási szükségleteit lehet, hogy nyissa meg a szolgáltatások, Azure által biztosított, például ha túl használja az Active Directory-tartományok, vagy ha DNS-feloldás virtuális hálózatok közötti. Ezek a forgatókönyvek fedik le, Azure lehetővé teszi a saját DNS-kiszolgálók használata.

A virtuális hálózaton belül DNS-kiszolgálók DNS-lekérdezések továbbíthatja Azure rekurzív feloldókat megoldásához állomásnevek adott virtuális hálózaton belül. Az Azure-t futtató tartományvezérlő (DC) például a tartomány DNS-lekérdezései válaszolnak, és a további lekérdezések továbbítása az Azure-bA. Lekérdezések továbbítása lehetővé teszi, hogy a virtuális gépek, a helyszíni erőforrások (keresztül a tartományvezérlő) és az Azure által biztosított állomásnevek (keresztül a továbbító). A virtuális IP-cím 168.63.129.16 Azure rekurzív feloldókat hozzáférést biztosítja.

DNS-továbbítást is lehetővé teszi az Inter-virtuális hálózat DNS-feloldás, és lehetővé teszi, hogy a helyszíni gépeket Azure által biztosított állomásnevek megoldásához. Ahhoz, hogy oldja meg a virtuális gép állomásnevét, a DNS-kiszolgáló virtuális gép ugyanazon a virtuális hálózaton kell lennie, és úgy, hogy az Azure előre állomásnév lekérdezések. A DNS-utótagja eltér az egyes virtuális hálózati, mert feltételes továbbítás szabályok használatával DNS-lekérdezéseket küldjön a megfelelő virtuális hálózat a feloldásához. Az alábbi ábrán két virtuális hálózatok és az Inter-virtuális hálózat DNS-feloldás ezzel a módszerrel történt a helyi hálózaton. Egy példa DNS-továbbító érhető el a [Azure gyors üzembe helyezés sablontárban](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) és [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> Szerepkörpéldányokat képes végrehajtani a névfeloldást virtuális gépek belül az azonos virtuális hálózatban, használja a teljes Tartománynevét, amely a virtuális gép nevét, a "internal.cloudapp.net" DNS-utótaggal együtt használja. Ebben az esetben névfeloldás azonban csak sikeres, ha a szerepkör példánya meghatározott Virtuálisgép neve a [szerepkör séma (.cscfg fájl)](https://msdn.microsoft.com/library/azure/jj156212.aspx). 
>    <Role name="<role-name>" vmName="<vm-name>">
> 
> Szerepkörpéldányt beállítani, amely kell végrehajtani a névfeloldást a virtuális gépeket egy másik virtuális hálózaton (teljes tartománynév használatával a **internal.cloudapp.net** utótag) leírtak a két virtuális hálózatok közötti továbbítás egyéni DNS-kiszolgálókon keresztül ehhez rendelkezik Ebben a szakaszban.
>

![Inter-virtual network DNS](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Ha Azure által biztosított névfeloldás, olyan belső DNS-utótagot használ (`*.internal.cloudapp.net`) az egyes virtuális Azure által biztosított DHCP. Ez a utótag lehetővé teszi, hogy állomásnév-feloldási, az állomásnév rekordjainak a *internal.cloudapp.net* zóna. A saját nevet névfeloldási megoldás használata esetén ez utótag nincs megadva a virtuális gépekhez, mert azt más DNS-architektúrák (például a tartományhoz csatlakoztatott forgatókönyvek) gátolja. Ehelyett az Azure biztosít a nem működő helyőrző (*reddog.microsoft.com*).

Ha szükséges, a belső DNS-utótag lehet meghatározni a PowerShell vagy a API:

* Virtuális hálózatok a Resource Manager üzembe helyezési modellel, az utótaghoz érhető el keresztül a [hálózati kártya](virtual-network-network-interface.md) erőforrás keresztül vagy a [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) parancsmag.
* A klasszikus üzembe helyezési modellek utótag megtalálható a [telepítési API beszerzése](https://msdn.microsoft.com/library/azure/ee460804.aspx) hívás keresztül vagy a [Get-AzureVM-Debug](/powershell/module/azure/get-azurevm) parancsmag.

Lekérdezések továbbítása az Azure-ba nem a igényeinek megfelelően, ha meg kell adnia a saját DNS-megoldást. A DNS-megoldást kell megfelelnie:

* Adja meg a megfelelő állomásnév-feloldási keresztül [DDNS](virtual-networks-name-resolution-ddns.md), például. Ne feledje, ha túl korán használatával tiltsa le a DNS rekord kitakarítási Azure DHCP-bérletek hosszú, és kitakarítási távolíthatja el a DNS szeretne DDNS rögzíti. 
* Adja meg a megfelelő rekurziót külső tartomány nevek feloldását engedélyezéséhez.
* Elérhető (TCP és UDP 53-as porton) szolgál az ügyfelekről és fogja tudni érni az internetet.
* Védhető szemben az internetről, külső ügynökök által jelentett veszélyek mérséklése.

> [!NOTE]
> A legjobb teljesítmény érdekében Azure virtuális gépek használata a DNS-kiszolgálóként, IPv6 le kell tiltani és egy [példányszintű nyilvános IP-cím](virtual-networks-instance-level-public-ip.md) minden DNS-kiszolgáló virtuális gép legyen hozzárendelve. További Teljesítményelemzés és optimalizálás használata a Windows Server a DNS-kiszolgálóként, lásd: [nevet névfeloldási teljesítményét egy rekurzív Windows DNS Server 2012 R2](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx).
> 
> 

### <a name="web-apps"></a>Web Apps
Ha szeretné végrehajtani a névfeloldást a alkalmazás Service webalkalmazásból csatolva a virtuális hálózat, a virtuális gépeknek ugyanazon virtuális hálózatban, majd egy egyéni DNS-kiszolgáló, amely rendelkezik egy DNS-továbbító beállítása mellett, amely továbbítja a lekérdezések az Azure-ba (virtuális IP-cím 168.63.129.16) , szükség hajtsa végre a következő lépéseket:
* Az App Service webalkalmazás virtuális hálózati integráció engedélyezése, ha ezt még nem tette, a [integrációt az alkalmazás a virtuális hálózati](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Az Azure portálon, az App Service-csomagot a webalkalmazás üzemeltetéséhez válassza **szinkronizálási hálózati** alatt **hálózati**, **virtuális hálózati integráció**, ahogy az az alábbi Kép:

    ![Web Apps virtuális hálózati névfeloldás](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Az App Service Web App a névfeloldás csatolva a virtuális hálózat, a másik virtuális hálózatot a virtuális gépek használatát igényli az egyéni DNS-kiszolgálók mindkét virtuális hálózat, az alábbiak szerint:
* Állítsa be a virtuális gép, amely szintén továbbíthatja lekérdezések Azure rekurzív feloldó (virtuális IP-cím 168.63.129.16) a cél virtuális hálózat DNS-kiszolgáló. Egy példa DNS-továbbító érhető el a [Azure gyors üzembe helyezés sablontárban](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) és [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Állítsa be a DNS-továbbítók a forrás virtuális hálózatban a virtuális gép. Konfigurálja a DNS-továbbító továbbítják a lekérdezéseket a DNS-kiszolgálót a cél virtuális hálózaton.
* A forrás virtuális hálózati beállításainak a forrás DNS-kiszolgáló konfigurálása
* Az App Service webalkalmazás összekapcsolása a forrás virtuális hálózat utasításait követve virtuális hálózati integráció engedélyezése [integrációt az alkalmazás a virtuális hálózati](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Az Azure portálon, az App Service-csomagot a webalkalmazás üzemeltetéséhez válassza **szinkronizálási hálózati** alatt **hálózati**, **virtuális hálózati integráció**. 

## <a name="specifying-dns-servers"></a>DNS-kiszolgálók megadása
A saját DNS-kiszolgálók, az Azure lehetővé teszi a virtuális hálózati időszakonkénti hálózati illesztőt (Resource Manager), vagy több DNS-kiszolgálók / felhőalapú szolgáltatás (klasszikus). Egy felhőalapú szolgáltatás/hálózati illesztőhöz megadott DNS-kiszolgálók sorrendjének beolvasása a virtuális hálózathoz megadott DNS-kiszolgálókon keresztül.

> [!NOTE]
> Hálózati kapcsolat tulajdonságai, például a DNS-kiszolgáló IP-címek, nem lehet szerkeszteni közvetlenül a Windows virtuális gépeken belül, akkor előfordulhat, hogy első újramegfeleltetés során szolgáltatás javítandó a virtuális hálózati adapter lekérdezi cseréje. 
> 
> 

A Resource Manager üzembe helyezési modellel használatakor DNS-kiszolgálók adható meg a portál API/sablonok: [virtuális hálózati](https://msdn.microsoft.com/library/azure/mt163661.aspx) és [hálózati illesztő](https://msdn.microsoft.com/library/azure/mt163668.aspx), vagy a PowerShell: [virtuális hálózat ](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetwork) és [hálózati illesztő](/powershell/module/azurerm.network/new-azurermnetworkinterface).

Ha a klasszikus üzembe helyezési modellt használ, a virtuális hálózat DNS-kiszolgálók adható meg a portálon vagy [a *hálózati konfiguráció* fájl](https://msdn.microsoft.com/library/azure/jj157100). A felhőszolgáltatások, DNS-kiszolgálókon keresztül megadott [a *szolgáltatáskonfiguráció* fájl](https://msdn.microsoft.com/library/azure/ee758710) vagy a PowerShell, használatával [New-AzureVM](/powershell/module/azure/new-azurevm).

> [!NOTE]
> Ha egy virtuális hálózat vagy virtuális gépet, amely már telepítve van a DNS-beállításainak módosításához kell egyes érintett virtuális gépek a módosítások életbe léptetéséhez indítsa újra.
> 
> 

## <a name="next-steps"></a>További lépések

Erőforrás-kezelő telepítési modell:

* [A virtuális hálózat létrehozása vagy módosítása](https://msdn.microsoft.com/library/azure/mt163661.aspx)
* [Létrehozni vagy frissíteni a hálózati kártya](https://msdn.microsoft.com/library/azure/mt163668.aspx)
* [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)
* [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface)

Klasszikus telepítési modell:

* [Az Azure szolgáltatás konfigurációs séma](https://msdn.microsoft.com/library/azure/ee758710)
* [Virtuális hálózati konfiguráció séma](https://msdn.microsoft.com/library/azure/jj157100)
* [Virtuális hálózat konfigurálása a hálózati konfigurációs fájl segítségével](virtual-networks-using-network-configuration-file.md)
