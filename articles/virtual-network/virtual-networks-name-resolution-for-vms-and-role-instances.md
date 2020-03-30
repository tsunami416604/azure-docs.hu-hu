---
title: Azure virtuális hálózatokon található erőforrások névfeloldása
titlesuffix: Azure Virtual Network
description: Névfeloldási forgatókönyvek az Azure IaaS, hibrid megoldások, a különböző felhőszolgáltatások, az Active Directory és a saját DNS-kiszolgáló használatával.
services: virtual-network
documentationcenter: na
author: rohinkoul
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/2/2020
ms.author: rohink
ms.openlocfilehash: 20a5c4befaa30383c54ac9536a3fd26dce3db4d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059985"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Azure virtuális hálózatokon található erőforrások névfeloldása

Attól függően, hogy az Azure-t hogyan üzemelteti az IaaS, a PaaS és a hibrid megoldások üzemeltetéséhez, előfordulhat, hogy engedélyeznie kell a virtuális gépek (VM-ek) és a virtuális hálózatban üzembe helyezett egyéb erőforrások egymással való kommunikációt. Bár ip-címek használatával engedélyezheti a kommunikációt, sokkal egyszerűbb olyan neveket használni, amelyek könnyen megjegyezhetők, és nem változnak. 

Ha a virtuális hálózatokban üzembe helyezett erőforrásoknak fel kell oldaniuk a tartományneveket belső IP-címekre, három módszer közül választhatnak:

* [Azure DNS-személyes zónák](../dns/private-dns-overview.md)
* [Az Azure által biztosított névfeloldás](#azure-provided-name-resolution)
* [Névfeloldás, amely a saját DNS-kiszolgálóját használja](#name-resolution-that-uses-your-own-dns-server) (amely továbbíthatja a lekérdezéseket az Azure által biztosított DNS-kiszolgálóknak)

A használt névfeloldási típus attól függ, hogy az erőforrásoknak hogyan kell kommunikálniuk egymással. Az alábbi táblázat forgatókönyveket és megfelelő névfeloldási megoldásokat mutat be:

> [!NOTE]
> Az Azure DNS-privát zónák az előnyben részesített megoldás, és rugalmasságot biztosít a DNS-zónák és -rekordok kezelésében. További információkat az [Azure DNS privát tartományokhoz való használatát](../dns/private-dns-overview.md) ismertető cikkben olvashat.

> [!NOTE]
> Ha az Azure Provided DNS-t használja, akkor a rendszer automatikusan alkalmazza a megfelelő DNS-utótagot a virtuális gépekre. Az összes többi beállításhoz vagy teljesen minősített tartományneveket (FQDN) kell használnia, vagy manuálisan kell alkalmaznia a megfelelő DNS-utótagot a virtuális gépekre.

| **Forgatókönyv** | **Megoldás** | **DNS-utótag** |
| --- | --- | --- |
| Névfeloldás az azonos virtuális hálózatban található virtuális gépek vagy az Azure Cloud Services szerepkörpéldányok ugyanazon a felhőszolgáltatásban között. | [Azure DNS-es privát zónák](../dns/private-dns-overview.md) vagy [Az Azure által megadott névfeloldás](#azure-provided-name-resolution) |Állomásnév vagy teljes tartománynév |
| Névfeloldás a virtuális gépek között a különböző virtuális hálózatokban vagy szerepkörpéldányok különböző felhőszolgáltatásokban. |[Az Azure DNS-es magánzónák](../dns/private-dns-overview.md) vagy az Ügyfél által felügyelt DNS-kiszolgálók továbbítják a lekérdezéseket a virtuális hálózatok között az Azure (DNS-proxy) általi feloldásérdekében. Lásd: [Névfeloldás a saját DNS-kiszolgálóján.](#name-resolution-that-uses-your-own-dns-server) |Csak teljes tartományontartomány |
| Névfeloldás egy Azure App Service (Web App, Függvény vagy Bot) virtuális hálózati integráció használatával szerepkör példányok vagy virtuális gépek ugyanabban a virtuális hálózatban. |Ügyfél által kezelt DNS-kiszolgálók továbbítják a lekérdezéseket a virtuális hálózatok között az Azure (DNS-proxy) általi feloldáshoz. Lásd: [Névfeloldás a saját DNS-kiszolgálóján.](#name-resolution-that-uses-your-own-dns-server) |Csak teljes tartományontartomány |
| Névfeloldás az App Service Web Apps a virtuális gépek ugyanabban a virtuális hálózatban. |Ügyfél által kezelt DNS-kiszolgálók továbbítják a lekérdezéseket a virtuális hálózatok között az Azure (DNS-proxy) általi feloldáshoz. Lásd: [Névfeloldás a saját DNS-kiszolgálóján.](#name-resolution-that-uses-your-own-dns-server) |Csak teljes tartományontartomány |
| Névfeloldás az App Service Web Apps egy virtuális hálózatban a virtuális virtuális gépek egy másik virtuális hálózatban. |Ügyfél által kezelt DNS-kiszolgálók továbbítják a lekérdezéseket a virtuális hálózatok között az Azure (DNS-proxy) általi feloldáshoz. Lásd: [Névfeloldás a saját DNS-kiszolgálóján.](#name-resolution-that-uses-your-own-dns-server) |Csak teljes tartományontartomány |
| A helyszíni számítógép- és szolgáltatásnevek feloldása a virtuális gépekről vagy az Azure-beli szerepkörpéldányokból. |Ügyfél által felügyelt DNS-kiszolgálók (helyszíni tartományvezérlő, helyi írásvédett tartományvezérlő vagy például zónaletöltéssel szinkronizált másodlagos DNS-tartományvezérlő). Lásd: [Névfeloldás a saját DNS-kiszolgálóján.](#name-resolution-that-uses-your-own-dns-server) |Csak teljes tartományontartomány |
| Az Azure állomásnevek feloldása a helyszíni számítógépekről. |Lekérdezések továbbítása az ügyfél által felügyelt DNS-proxy kiszolgáló a megfelelő virtuális hálózat, a proxykiszolgáló továbbítja a lekérdezéseket az Azure-ba a megoldás. Lásd: [Névfeloldás a saját DNS-kiszolgálóján.](#name-resolution-that-uses-your-own-dns-server) |Csak teljes tartományontartomány |
| A belső IP-k DNS-jának megfordítása. |[Az Azure DNS-es privát zónák,](../dns/private-dns-overview.md) illetve [az Azure által megadott névfeloldás](#azure-provided-name-resolution) vagy [névfeloldás a saját DNS-kiszolgálóhasználatával.](#name-resolution-that-uses-your-own-dns-server) |Nem alkalmazható |
| Névfeloldás a virtuális gépek vagy a különböző felhőszolgáltatásokban, nem virtuális hálózatban található szerepkörpéldányok között. |Nem alkalmazható. Virtuális gépek és szerepkörpéldányok különböző felhőszolgáltatások szerepkörpéldányok közötti kapcsolat nem támogatott a virtuális hálózaton kívül. |Nem alkalmazható|

## <a name="azure-provided-name-resolution"></a>Az Azure által biztosított névfeloldás

Az Azure által megadott névfeloldás csak alapvető mérvadó DNS-képességeket biztosít. Ha ezt a beállítást használja, a DNS-zónaneveket és -rekordokat az Azure automatikusan kezeli, és nem fogja tudni szabályozni a DNS-zónaneveket vagy a DNS-rekordok életciklusát. Ha teljes körű DNS-megoldásra van szüksége a virtuális hálózatokhoz, [azure DNS-szintű magánzónákat](../dns/private-dns-overview.md) vagy [ügyfél által felügyelt DNS-kiszolgálókat kell használnia.](#name-resolution-that-uses-your-own-dns-server)

A nyilvános DNS-nevek feloldása mellett az Azure belső névfeloldást biztosít az ugyanazon virtuális hálózaton vagy felhőszolgáltatáson belül található virtuális gépek és szerepkörpéldányok számára. A virtuális gépek és a felhőszolgáltatás példányai ugyanazt a DNS-utótagot rendelkeznek, így az állomásnév önmagában elegendő. A klasszikus üzembe helyezési modell használatával telepített virtuális hálózatokban azonban a különböző felhőszolgáltatások különböző DNS-utótagokkal rendelkeznek. Ebben a helyzetben a teljes tartománynévre van szükség a különböző felhőszolgáltatások közötti nevek feloldásához. Az Azure Resource Manager telepítési modelljével telepített virtuális hálózatokban a DNS-utótag konzisztens a virtuális hálózaton belüli összes virtuális gépen, így a teljes tartománynévnem szükséges. A DNS-nevek a virtuális gépekhez és a hálózati csatolókhoz is hozzárendelhetők. Bár az Azure által megadott névfeloldás nem igényel semmilyen konfigurációt, ez nem a megfelelő választás az összes üzembe helyezési forgatókönyvek, az előző táblázatban részletezett.

> [!NOTE]
> A felhőszolgáltatások webes és feldolgozói szerepkörök használatakor is elérheti a belső IP-címek a szerepkörpéldányok az Azure Service Management REST API használatával. További információt a [Service Management REST API-kézikönyvben talál.](https://msdn.microsoft.com/library/azure/ee460799.aspx) A cím a szerepkör nevén és a példányszámán alapul. 
>

### <a name="features"></a>Szolgáltatások

Az Azure által biztosított névfeloldás a következő funkciókat tartalmazza:
* Könnyű használat. Nem igényel konfigurálást.
* Magas rendelkezésre állás. Nem kell létrehoznia és kezelnie a saját DNS-kiszolgálóifürtjeit.
* A szolgáltatás a saját DNS-kiszolgálókkal együtt is használható a helyszíni és az Azure-állomásnevek feloldásához.
* Használhatja a névfeloldás virtuális gépek és szerepkörpéldányok ugyanazon a felhőszolgáltatáson belül, anélkül, hogy egy teljes tartománynév.
* Az Azure Resource Manager telepítési modelljét használó virtuális hálózatokvirtuális virtuális gépek közötti névfeloldást használhat anélkül, hogy teljes tartománynevet kellene igényelnie. A klasszikus üzembe helyezési modellben lévő virtuális hálózatok hoz egy teljes tartománynevet, amikor különböző felhőszolgáltatásokban lévő neveket old fel. 
* Az automatikusan létrehozott nevek használata helyett olyan állomásneveket is használhat, amelyek a legjobban leírják a központi telepítéseket.

### <a name="considerations"></a>Megfontolandó szempontok

Az Azure által megadott névfeloldás használatakor figyelembe veendő szempontok:
* Az Azure által létrehozott DNS-utótag nem módosítható.
* A DNS-keresése hatóköre virtuális hálózatba van. Az egyik virtuális hálózathoz létrehozott DNS-nevek nem oldhatók fel más virtuális hálózatokból.
* Saját rekordjait nem lehet manuálisan regisztrálni.
* A WINS és a NetBIOS nem támogatott. A windows intézőben nem láthatók a virtuális gépek.
* Az állomásneveknek DNS-kompatibiliseknek kell lenniük. A nevek csak 0-9, a-z és "-" szavakat használhatnak, és nem kezdődhetnek vagy végződhetnek "-" kezdetűek vagy végződhetnek.
* DNS-lekérdezési forgalom minden virtuális gép szabályozása. A szabályozás nem befolyásolhatja a legtöbb alkalmazást. Ha a kérelem szabályozása figyelhető meg, győződjön meg arról, hogy az ügyféloldali gyorsítótárazás engedélyezve van. További információt a [DNS-ügyfél konfigurációja](#dns-client-configuration)című témakörben talál.
* Csak az első 180 felhőszolgáltatás ban lévő virtuális gépek vannak regisztrálva minden egyes virtuális hálózathoz egy klasszikus üzembe helyezési modellben. Ez a korlát nem vonatkozik az Azure Resource Manager virtuális hálózataira.
* Az Azure DNS IP-címe: 168.63.129.16. Ez egy statikus IP-cím, és nem fog változni.

### <a name="reverse-dns-considerations"></a>A DNS-sel kapcsolatos szempontok
A reverse DNS minden ARM alapú virtuális hálózatban támogatott. Fordított DNS-lekérdezéseket (PTR-lekérdezéseket) adhat ki a virtuális gépek IP-címeinek a virtuális gépek teljes tartománynaihoz való hozzárendeléséhez.
* A virtuális gépek IP-címére vonatkozó összes PTR-lekérdezés \[az\]űrlap vmname .internal.cloudapp.net teljes tartományn-tartományait adja vissza
* Az űrlap \[vmname\].internal.cloudapp.net teljes tartományneveinek címhívása .internal.cloudapp.net a virtuális géphez rendelt IP-címre oldódik fel.
* Ha a virtuális hálózat regisztrációs virtuális hálózatként egy [Azure DNS-magánzónához](../dns/private-dns-overview.md) kapcsolódik, a fordított DNS-lekérdezések két rekordot adnak vissza. Egy rekord lesz az \[űrlap\]vmname . [priatednszonename] és más lenne \[a forma\]vmname .internal.cloudapp.net
* A névfeloldási DNS-keresése hatóköre egy adott virtuális hálózatra van, még akkor is, ha más virtuális hálózatokra van társviszonyban. A társviszonyban álló virtuális hálózatokban található virtuális gépek IP-címeinek fordított DNS-lekérdezései (PTR-lekérdezések) NXDOMAIN-t adnak vissza.

> [!NOTE]
> Ha azt szeretné, hogy a névfeloldási DNS-keresése kontúrja a virtuális hálózaton, létrehozhat egy névkeresési zónát (in-addr.arpa) [az Azure DNS-zónákat,](../dns/private-dns-overview.md) és több virtuális hálózathoz kapcsolhatja. A virtuális gépek fordított DNS-rekordjait azonban manuálisan kell kezelnie.
>


## <a name="dns-client-configuration"></a>DNS-ügyfél konfigurációja

Ez a szakasz az ügyféloldali gyorsítótárazást és az ügyféloldali újrapróbálkozásokat ismerteti.

### <a name="client-side-caching"></a>Ügyféloldali gyorsítótárazás

Nem minden DNS-lekérdezést kell küldeni a hálózaton keresztül. Az ügyféloldali gyorsítótárazás segít csökkenteni a késést, és javítja a hálózati blips ekkel szembeni ellenálló képességet azáltal, hogy feloldja az ismétlődő DNS-lekérdezéseket a helyi gyorsítótárból. A DNS-rekordok egy élettartamra (TTL) mechanizmust tartalmaznak, amely lehetővé teszi a gyorsítótár számára, hogy a rekord frissességét a lehető legtovább tárolja anélkül, hogy befolyásolnák a rekord frissességét. Így az ügyféloldali gyorsítótárazás a legtöbb helyzetben alkalmas.

Az alapértelmezett Windows DNS-ügyfél beépített DNS-gyorsítótárral rendelkezik. Egyes Linux-disztribúciók alapértelmezés szerint nem tartalmazzák a gyorsítótárazást. Ha úgy találja, hogy nincs már helyi gyorsítótár, adjon hozzá egy DNS-gyorsítótárat minden linuxos virtuális géphez.

Számos különböző DNS-gyorsítótárazási csomag áll rendelkezésre (például a dnsmasq). A dnsmasq telepítése a leggyakoribb disztribúciókra:

* **Ubuntu (a resolvconf-ot használja)**:
  * Telepítse a dnsmasq `sudo apt-get install dnsmasq`csomagot a segítségével.
* **SUSE (netconf-ot használ):**
  * Telepítse a dnsmasq `sudo zypper install dnsmasq`csomagot a segítségével.
  * Engedélyezze a dnsmasq szolgáltatást a segítségével. `systemctl enable dnsmasq.service` 
  * Indítsa el a dnsmasq szolgáltatást a segítségével. `systemctl start dnsmasq.service` 
  * Edit /etc/sysconfig/network/config , and change NETCONFIG_DNS_FORWARDER="" (Az **/etc/sysconfig/network/config)** és *a NETCONFIG_DNS_FORWARDER=""* kapcsoló *tikkasztása imasq*értékre.
  * Frissítse a feloldó.conf fájllal, `netconfig update`hogy a gyorsítótárat helyi DNS-feloldóként állítsa be.
* **CentOS (a NetworkManager-t használja)**:
  * Telepítse a dnsmasq `sudo yum install dnsmasq`csomagot a segítségével.
  * Engedélyezze a dnsmasq szolgáltatást a segítségével. `systemctl enable dnsmasq.service`
  * Indítsa el a dnsmasq szolgáltatást a segítségével. `systemctl start dnsmasq.service`
  * Adja hozzá *a 127.0.0.1;* **/etc/dhclient-eth0.conf**
  * Indítsa újra a `service network restart`hálózati szolgáltatást a segítségével, hogy a gyorsítótárat helyi DNS-feloldóként állítsa be.

> [!NOTE]
> A dnsmasq csomag csak egy a sok DNS-gyorsítótár közül, amely Linuxon érhető el. Használat előtt ellenőrizze, hogy megfelel-e az igényeinek, és ellenőrizze, hogy nincs-e más gyorsítótár telepítve.

    
### <a name="client-side-retries"></a>Ügyféloldali újrapróbálkozások

A DNS elsősorban UDP protokoll. Mivel az UDP protokoll nem garantálja az üzenetek kézbesítését, az újrapróbálkozási logikát maga a DNS-protokoll kezeli. Minden DNS-ügyfél (operációs rendszer) különböző újrapróbálkozási logikát mutathat, a létrehozó preferenciáitól függően:

* A Windows operációs rendszerek egy másodperc után újra próbálkoznak, majd újabb két másodperc, négy másodperc és újabb négy másodperc után. 
* Az alapértelmezett Linux telepítő öt másodperc után újrapróbálkozik. Javasoljuk, hogy az újrapróbálkozási specifikációkat ötször, egy másodperces időközönként módosítsa.

Ellenőrizze az aktuális beállításokat egy `cat /etc/resolv.conf`Linux virtuális gépen a segítségével. Nézze meg a *lehetőségek* vonal, például:

```bash
options timeout:1 attempts:5
```

A resolv.conf fájl általában automatikusan generálódik, és nem szerkeszthető. A *beállítási* vonal hozzáadásának konkrét lépései a disztribúciótól függően változnak:

* **Ubuntu** (resolvconf-ot használ):
  1. Adja hozzá a *kapcsolósort* az **/etc/resolvconf/resolv.conf.d/tail kapcsolóhoz.**
  2. Futtassa `resolvconf -u` a frissítéshez.
* **SUSE** (netconf használatával):
  1. *Időbeli:1 próbálkozások:5* hozzáadása az **/etc/sysconfig/network/config** **paraméterhez NETCONFIG_DNS_RESOLVER_OPTIONS=""** paraméterhez.
  2. Futtassa `netconfig update` a frissítéshez.
* **CentOS** (a NetworkManager-t használja):
  1. Adja hozzá *a echo "options timeout:1 attempts:5"* parancsot az **/etc/NetworkManager/dispatcher.d/11-dhclient könyvtárhoz.**
  2. Frissítés `service network restart`a segítségével.

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Saját DNS-kiszolgálót használó névfeloldás

Ez a szakasz a virtuális gépeket, a szerepkörpéldányokat és a webalkalmazásokat ismerteti.

### <a name="vms-and-role-instances"></a>Virtuális gépek és szerepkörpéldányok

Előfordulhat, hogy a névfeloldási igények túlmutatnak az Azure által biztosított funkciókon. Előfordulhat például, hogy Microsoft Windows Server Active Directory tartományokat kell használnia, fel kell oldania a DNS-neveket a virtuális hálózatok között. Ezeka forgatókönyvek, az Azure lehetővé teszi, hogy saját DNS-kiszolgálók.

A virtuális hálózaton belüli DNS-kiszolgálók továbbíthatják a DNS-lekérdezéseket az Azure rekurzív feloldóinak. Ez lehetővé teszi a virtuális hálózaton belüli állomásnevek feloldását. Az Azure-ban futó tartományvezérlők például válaszolhatnak a tartományai DNS-lekérdezéseire, és továbbíthatják az összes többi lekérdezést az Azure-ba. A továbbítási lekérdezések lehetővé teszik, hogy a virtuális gépek a helyszíni erőforrásokat (a tartományvezérlőn keresztül) és az Azure által biztosított állomásneveket (a továbbítón keresztül) is lássák. Az Azure rekurzív feloldóihoz való hozzáférés a virtuális IP 168.63.129.16-on keresztül érhető el.

A DNS-továbbítás lehetővé teszi a DNS-feloldást a virtuális hálózatok között, és lehetővé teszi a helyszíni gépek számára az Azure által biztosított állomásnevek feloldását. A virtuális gép állomásnevének feloldása érdekében a DNS-kiszolgáló virtuális gépének ugyanabban a virtuális hálózatban kell lennie, és úgy kell konfigurálnia, hogy az állomásnév-lekérdezéseket továbbítsa az Azure-ba. Mivel a DNS-utótag minden virtuális hálózatban más, feltételes továbbítási szabályok kal a megfelelő virtuális hálózatba küldhet DNS-lekérdezéseket. Az alábbi képen két virtuális hálózat és egy helyszíni hálózat látható, amely dns-feloldást végez a virtuális hálózatok között ezzel a módszerrel. Egy példa A DNS-továbbító az [Azure Quickstart Templates galériában](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) és a [GitHubon](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder)érhető el.

> [!NOTE]
> Egy szerepkörpéldány ugyanazon a virtuális hálózaton belül a virtuális gépek névfeloldását hajthatja végre. Ezt a teljes tartománynév használatával teszi, amely a virtuális gép állomásnevéből és **internal.cloudapp.net** DNS-utótagból áll. Ebben az esetben azonban a névfeloldás csak akkor sikeres, ha a szerepkörpéldány a [szerepkörsémében (.cscfg fájl)](https://msdn.microsoft.com/library/azure/jj156212.aspx)definiált virtuálisgép-névvel rendelkezik.
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> Azoknak a szerepkörpéldányoknak, amelyeknek egy másik virtuális hálózatban **(internal.cloudapp.net** fqdn) egy másik virtuális hálózatban (FQDN) kell végrehajtaniuk, ezt az ebben a szakaszban leírt módszerrel kell megtenniük (a két virtuális hálózat között továbbító egyéni DNS-kiszolgálók).
>

![Dns-diagram a virtuális hálózatok között](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Az Azure által megadott névfeloldás használataesetén az Azure Dynamic Host Configuration Protocol (DHCP) minden virtuális géphez belső DNS-utótagot (**.internal.cloudapp.net)** biztosít. Ez az utótag engedélyezi az állomásnév feloldását, mert az állomásnévrekordok **a internal.cloudapp.net** zónában vannak. Ha saját névfeloldási megoldást használ, ez az utótag nem lesz megadva a virtuális gépeknek, mert az zavarja a többi DNS-architektúrát (például a tartományhoz illesztett forgatókönyveket). Ehelyett az Azure nem működő helyőrzőt (*reddog.microsoft.com)* biztosít.

Szükség esetén a belső DNS-utótagot a PowerShell vagy az API használatával határozhatja meg:

* Az Azure Resource Manager telepítési modelljeiben lévő virtuális hálózatok esetében az utótag a [REST API hálózati interfészen,](https://docs.microsoft.com/rest/api/virtualnetwork/networkinterfaces)a [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) PowerShell-parancsmagon és az [azhálózati csatorna](/cli/azure/network/nic#az-network-nic-show) Azure CLI parancson keresztül érhető el.
* A klasszikus üzembe helyezési modellekben az utótag érhető el a [Get Deployment API-hívás](https://msdn.microsoft.com/library/azure/ee460804.aspx) vagy a [Get-AzureVM -Debug](/powershell/module/servicemanagement/azure/get-azurevm) parancsmag.

Ha a lekérdezések továbbítása az Azure-ba nem felel meg az igényeinek, meg kell adnia a saját DNS-megoldás. A DNS-megoldásnak a következőket kell tennie:

* Adja meg például a megfelelő állomásnév-feloldást a [DDNS-en](virtual-networks-name-resolution-ddns.md)keresztül. Ha DDNS-t használ, előfordulhat, hogy le kell tiltania a DNS-rekordkitakarítást. Az Azure DHCP-bérletek hosszúak, és a takarítás idő előtt eltávolíthatja a DNS-rekordokat. 
* A külső tartománynevek feloldásának lehetővé teszi a megfelelő rekurzív felbontást.
* Legyen elérhető (TCP és UDP az 53-as porton) az általa kiszolgálott ügyfelektől, és férjen hozzá az internethez.
* Védelemben kell részesülni az internetről való hozzáféréssel szemben a külső ügynökök által jelentett fenyegetések csökkentése érdekében.

> [!NOTE]
> A legjobb teljesítmény érdekében az Azure virtuális gépeit DNS-kiszolgálóként használja, le kell tiltani az IPv6-ot. Minden DNS-kiszolgáló virtuális gépéhez hozzá kell rendelni egy [nyilvános IP-címet.](virtual-network-public-ip-address.md) 
> 

### <a name="web-apps"></a>Webalkalmazások
Tegyük fel, hogy névfeloldást kell végrehajtania a webalkalmazásból, amelyet egy virtuális hálózathoz kapcsolódó, virtuális hálózathoz kapcsolódó webalkalmazásból kell végrehajtania az ugyanazon virtuális hálózatban lévő virtuális gépekhez. A lekérdezéseket az Azure-ba továbbító DNS-továbbítóval (virtuális IP 168.63.129.16) végző egyéni DNS-kiszolgáló beállítása mellett hajtsa végre a következő lépéseket:
1. Engedélyezze a virtuális hálózati integrációt a webalkalmazásban, ha még nem történt meg, [az Alkalmazás integrálása virtuális hálózattal](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című részben leírtak szerint.
2. Az Azure Portalon a webalkalmazást üzemeltető App Service-csomag esetében válassza a **Hálózat szinkronizálása** lehetőséget a **Hálózat ,** Virtuális **hálózati integráció**csoportban.

    ![Képernyőkép a virtuális hálózat névfeloldásáról](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Ha egy virtuális hálózathoz kapcsolódó, virtuális hálózathoz kapcsolódó webalkalmazásból névfeloldást kell végrehajtania egy másik virtuális hálózatban lévő virtuális gépekhez, mindkét virtuális hálózaton egyéni DNS-kiszolgálókat kell használnia, az alábbiak szerint:

* Állítson be egy DNS-kiszolgálót a célvirtuális hálózatban egy virtuális gépen, amely a lekérdezéseket az Azure rekurzív feloldójának is továbbíthatja (virtuális IP 168.63.129.16). Egy példa A DNS-továbbító az [Azure Quickstart Templates galériában](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) és a [GitHubon](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder)érhető el. 
* Dns-továbbító beállítása a virtuális forráshálózaton egy virtuális gépen. Konfigurálja úgy ezt a DNS-továbbítót, hogy a lekérdezéseket továbbítsa a célvirtuális hálózat DNS-kiszolgálójára.
* Konfigurálja a forrás DNS-kiszolgálót a forrásvirtuális hálózat beállításaiban.
* A virtuális hálózati integráció engedélyezése a webalkalmazás számára a forrás virtuális hálózathoz való csatoláshoz, az [Alkalmazás integrálása virtuális hálózattal](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című útmutató utasításait követve.
* Az Azure Portalon a webalkalmazást üzemeltető App Service-csomag esetében válassza a **Hálózat szinkronizálása** lehetőséget a **Hálózat ,** Virtuális **hálózati integráció**csoportban.

## <a name="specify-dns-servers"></a>DNS-kiszolgálók megadása
Ha saját DNS-kiszolgálóit használja, az Azure lehetővé teszi több DNS-kiszolgáló megadását virtuális hálózatonként. Hálózati adapterenként (az Azure Resource Manager esetében) vagy felhőszolgáltatásonként (a klasszikus üzemi modell esetében) is megadhat több DNS-kiszolgálót. A hálózati adapterhez vagy felhőszolgáltatáshoz megadott DNS-kiszolgálók elsőbbséget élveznek a virtuális hálózathoz megadott DNS-kiszolgálókkal szemben.

> [!NOTE]
> A hálózati kapcsolat tulajdonságait, például a DNS-kiszolgáló IP-címét nem szabad közvetlenül a virtuális gépeken belül szerkeszteni. Ez azért van, mert előfordulhat, hogy törli a szolgáltatás során gyógyítani, amikor a virtuális hálózati adapter lesz cserélni. Ez a Windows és a Linux virtuális gépekre egyaránt vonatkozik.

Az Azure Resource Manager központi telepítési modelljének használatakor megadhatja a DNS-kiszolgálókat egy virtuális hálózathoz és egy hálózati adapterhez. További információt a Virtuális hálózat kezelése és [a Hálózati adapter kezelése (Manage a) (Hálózati adapter kezelése) (Virtuális hálózat kezelése) (Hálózati adapter kezelése) témakörben talál.](virtual-network-network-interface.md) [Manage a virtual network](manage-virtual-network.md)

> [!NOTE]
> Ha egyéni DNS-kiszolgálót választ a virtuális hálózathoz, legalább egy DNS-kiszolgáló IP-címét meg kell adnia; ellenkező esetben a virtuális hálózat figyelmen kívül hagyja a konfigurációt, és az Azure által biztosított DNS-t használja helyette.

A klasszikus telepítési modell használatakor az Azure Portalon vagy a Hálózati [konfiguráció fájlban](https://msdn.microsoft.com/library/azure/jj157100)adhatja meg a virtuális hálózat DNS-kiszolgálóit. A felhőszolgáltatások hoz dns-kiszolgálókat a [Szolgáltatás konfigurációs fájlján](https://msdn.microsoft.com/library/azure/ee758710) keresztül vagy a PowerShell használatával, a [New-AzureVM](/powershell/module/servicemanagement/azure/new-azurevm)segítségével adhat meg.

> [!NOTE]
> Ha módosítja egy már telepített virtuális hálózat vagy virtuális gép DNS-beállításait, az új DNS-beállítások érvénybe léptetéséhez dhcp-címbérlet-megújítást kell végrehajtania a virtuális hálózat összes érintett virtuális gépén. A Windows operációs rendszert futtató virtuális gépek esetén `ipconfig /renew` ezt közvetlenül a virtuális gépbe való beírással teheti meg. A lépések az operációs rendszertől függően változnak. Tekintse meg az operációs rendszer típusának megfelelő dokumentációját.

## <a name="next-steps"></a>További lépések

Az Azure Resource Manager telepítési modellje:

* [Virtuális hálózat kezelése](manage-virtual-network.md)
* [Hálózati adapter kezelése](virtual-network-network-interface.md)

Klasszikus üzembe helyezési modell:

* [Az Azure szolgáltatás konfigurációs sémája](https://msdn.microsoft.com/library/azure/ee758710)
* [Virtuális hálózati konfigurációs séma](https://msdn.microsoft.com/library/azure/jj157100)
* [Virtuális hálózat konfigurálása hálózati konfigurációs fájl használatával](virtual-networks-using-network-configuration-file.md)
