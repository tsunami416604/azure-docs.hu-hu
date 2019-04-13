---
title: Erőforrások az Azure virtual networkök névfeloldása
titlesuffix: Azure Virtual Network
description: Adjon nevet a megoldási forgatókönyvek az Azure IaaS, a hibrid megoldások között különböző felhőszolgáltatások, az Active Directory és a saját DNS-kiszolgáló használatával.
services: virtual-network
documentationcenter: na
author: rohinkoul
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/25/2019
ms.author: rohink
ms.openlocfilehash: fe63b76589c841706ae335c61e56a57c3c33fb3e
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527183"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Erőforrások az Azure virtual networkök névfeloldása

Attól függően, hogyan használhatja az Azure IaaS, PaaS és a hibrid megoldások üzemeltetéséhez szükség lehet, hogy a virtuális gépek (VM), és más erőforrásokat helyeztek üzembe egy virtuális hálózaton kommunikálni egymással. IP-címek használatával engedélyezheti a kommunikációt, bár jóval egyszerűbb nevekkel, amelyek egyszerűen veszni, és ne módosítsa. 

A virtuális hálózatokon üzembe helyezett erőforrások két metódust használhatnak tartománynevek belső IP-címekre történő feloldásához:

* [Az Azure által biztosított névfeloldást](#azure-provided-name-resolution)
* [Névfeloldás saját DNS-kiszolgálót használó](#name-resolution-that-uses-your-own-dns-server) (ami előfordulhat, hogy továbbítsa a az Azure által biztosított DNS-kiszolgálók)

A névfeloldás használata típusa attól függ, hogyan kell az erőforrások kommunikálni egymással. Az alábbi táblázat azt mutatja be, forgatókönyvek és a megfelelő név feloldása megoldások:

> [!NOTE]
> A forgatókönyvtől függően előfordulhat, hogy szeretné használni az Azure DNS saját zónák funkció, amely jelenleg nyilvános előzetes verzióban érhető el. További információkat az [Azure DNS privát tartományokhoz való használatát](../dns/private-dns-overview.md) ismertető cikkben olvashat.
>

| **Forgatókönyv** | **Megoldás** | **Suffix** |
| --- | --- | --- |
| Az azonos virtuális hálózatban, vagy az Azure Cloud Services ugyanazon a felhőszolgáltatáson belül található virtuális gépek közötti névfeloldás. | [Az Azure DNS Private Zones](../dns/private-dns-overview.md) vagy [Azure által biztosított névfeloldást](#azure-provided-name-resolution) |Állomásnév vagy teljes tartománynév |
| Névfeloldás a különböző virtuális hálózatokban lévő virtuális gépek vagy szerepkörpéldányok a különböző felhőszolgáltatások között. |[Az Azure DNS Private Zones](../dns/private-dns-overview.md) vagy ügyfél által felügyelt DNS-kiszolgálók (DNS-proxy) az Azure által a névfeloldáshoz virtuális hálózatok közötti lekérdezések továbbítása. Lásd: [névfeloldás saját DNS-kiszolgáló használatával](#name-resolution-that-uses-your-own-dns-server). |Csak teljes tartománynév |
| Az Azure App Service (Web App, függvény vagy Bot) a névfeloldás virtuális hálózati integráció alkalmazásával szerepkörpéldányok vagy a virtuális gép ugyanazon a virtuális hálózaton. |Ügyfél által felügyelt DNS-kiszolgálók (DNS-proxy) az Azure által a névfeloldáshoz virtuális hálózatok közötti lekérdezések továbbítása. Lásd: [névfeloldás saját DNS-kiszolgáló használatával](#name-resolution-that-uses-your-own-dns-server). |Csak teljes tartománynév |
| Név feloldása az App Service Web Apps virtuális gépekhez az azonos virtuális hálózatba. |Ügyfél által felügyelt DNS-kiszolgálók (DNS-proxy) az Azure által a névfeloldáshoz virtuális hálózatok közötti lekérdezések továbbítása. Lásd: [névfeloldás saját DNS-kiszolgáló használatával](#name-resolution-that-uses-your-own-dns-server). |Csak teljes tartománynév |
| Neve az App Service Web Apps egy virtuális hálózaton belüli virtuális gépeken lévő megoldást egy másik virtuális hálózatot. |Ügyfél által felügyelt DNS-kiszolgálók (DNS-proxy) az Azure által a névfeloldáshoz virtuális hálózatok közötti lekérdezések továbbítása. Tekintse meg a névfeloldás saját DNS-kiszolgáló használatával. |Csak teljes tartománynév |
| A helyszíni számítógép és a szolgáltatás nevének a virtuális gépeket vagy szerepkörpéldányokat az Azure-beli feloldása. |Ügyfél által felügyelt DNS-kiszolgálók (a helyi tartományvezérlő, helyi írásvédett tartományvezérlő vagy másodlagos DNS zónaletöltés, például használatával szinkronizált). Lásd: [névfeloldás saját DNS-kiszolgáló használatával](#name-resolution-that-uses-your-own-dns-server). |Csak teljes tartománynév |
| A helyi számítógépek Azure gazdanév feloldása. |Az ügyfél által felügyelt DNS-proxy kiszolgáló a megfelelő virtuális hálózat továbbítási kérelmek a proxykiszolgáló továbbítja a lekérdezéseket az Azure-bA a feloldásához. Lásd: [névfeloldás saját DNS-kiszolgáló használatával](#name-resolution-that-uses-your-own-dns-server). |Csak teljes tartománynév |
| Fordított DNS belső IP-címek. |[Névfeloldás saját DNS-kiszolgáló használatával](#name-resolution-that-uses-your-own-dns-server). |Nem alkalmazható |
| A névfeloldás virtuális gépek vagy a különböző felhőszolgáltatások, a virtuális hálózat nem található szerepkör példányai között. |Nem alkalmazható. Virtuális gépek és szerepkörpéldányok a különböző felhőszolgáltatások közötti kapcsolat nem támogatott a virtuális hálózatokon kívül. |Nem alkalmazható|

## <a name="azure-provided-name-resolution"></a>Az Azure által biztosított névfeloldást

Nyilvános DNS-nevek feloldása, valamint az Azure belső névfeloldást biztosít a virtuális gépek és szerepkörpéldányok az azonos virtuális hálózatban vagy a felhőalapú szolgáltatás található. Virtuális gépek és a egy felhőszolgáltatás-példányok osztoznak azonos DNS-utótaggal, így egyedül az állomásnév elegendő. Azonban a klasszikus üzemi modellel üzembe helyezett virtuális hálózatokon, különböző felhőszolgáltatások különböző DNS-utótagot. Ebben a helyzetben van szükség a különböző felhőszolgáltatások közötti feloldani a teljes Tartománynevet. Az Azure Resource Manager üzemi modell használatával üzembe helyezett virtuális hálózatokon a DNS-utótag összhangban a virtuális hálózaton, így nincs szükség a teljes Tartománynevet. DNS-nevek a virtuális gépek és a hálózati adaptereket is hozzárendelhető. Bár az Azure által biztosított névfeloldást kell konfigurálni, már nem a megfelelő választás az összes központi telepítési forgatókönyvek esetén az előző táblázatban leírtaknak megfelelően.

> [!NOTE]
> Ha használja a cloud services webes és feldolgozói szerepkörök, szerepkör példányai az Azure Service Management REST API használatával a belső IP-címei is elérheti. További információkért lásd: a [Service Management REST API-referencia](https://msdn.microsoft.com/library/azure/ee460799.aspx). A cím a szerepkör nevét és számán alapul. 
>
>

### <a name="features"></a>Szolgáltatások

Az Azure által biztosított névfeloldást a következő szolgáltatásokat tartalmazza:
* A könnyű használatra. Semmilyen beállítást nem kell megadni.
* Magas rendelkezésre állás. Nem kell a saját DNS-kiszolgálók fürtök létrehozása és felügyelete.
* Használhatja a szolgáltatást együtt a saját DNS-kiszolgálókkal, a helyszíni és az Azure-gazdagép nevét.
* Névfeloldás között a virtuális gépek és szerepkörpéldányok ugyanazon a felhőszolgáltatáson belül, egy teljes nélkül is használhatja.
* Virtuális hálózatok, amelyek a teljes Tartománynevet kell az Azure Resource Manager üzemi modell virtuális gépek közötti névfeloldás is használhatja. A klasszikus üzemi modellben lévő virtuális hálózatok egy teljes Tartománynevet igényelnek, a különböző felhőszolgáltatások nevek feloldásakor. 
* Használhatja a központi telepítések jellemző állomásnevek automatikusan generált nevek használata helyett.

### <a name="considerations"></a>Megfontolandó szempontok

Tudnivalók az Azure által biztosított névfeloldást használata esetén:
* Az Azure által létrehozott DNS-utótagja nem lehet módosítani.
* A saját rekordok manuálisan nem regisztrálható.
* WINS- és NetBIOS nem támogatottak. Nem látható a virtuális gépek, a Windows Intézőben.
* Gazdagép nevének kell lennie a DNS-kompatibilis. Neveket kell használnia csak 0-9, a – z, és a "-", és nem kezdődhet vagy végződhet egy "-".
* A DNS-lekérdezés forgalom folyamatban van az egyes virtuális Gépekhez. Szabályozás nem érinti a legtöbb alkalmazás. Ha kérelemszabályozás, győződjön meg arról, hogy engedélyezve van-e az ügyféloldali gyorsítótárazást. További információkért lásd: [DNS-ügyfél konfigurációjának](#dns-client-configuration).
* Csak az első 180 felhőalapú szolgáltatások a virtuális gépek a klasszikus üzemi modellben minden egyes virtuális hálózat van regisztrálva. Ez a korlátozás nem vonatkozik az Azure Resource Manager virtuális hálózatokhoz.
* Az Azure DNS IP-cím a 168.63.129.16. Ez egy statikus IP-címet, és nem változik.

## <a name="dns-client-configuration"></a>DNS-ügyfél konfigurációja

Ez a szakasz ismertet ügyféloldali gyorsítótárazás és ügyféloldali újrapróbálkozásokat.

### <a name="client-side-caching"></a>Ügyféloldali gyorsítótárazás

Nem minden DNS-lekérdezést kell küldeni a hálózaton keresztül. Ügyféloldali gyorsítótárazás a késés csökkentése érdekében, és növelheti a hálózati jelekből, hibatűrést ismétlődő DNS-lekérdezések a helyi gyorsítótárból feloldása nyújt segítséget. DNS-rekordok tartalmaz a time-to-live (Élettartam TTL) mechanizmust, amely lehetővé teszi, hogy a gyorsítótárban tárolja, amíg a rekord rekord frissessége befolyásolása nélkül. Ebből kifolyólag ügyféloldali gyorsítótárazás ideális választás a legtöbb esetben.

Az alapértelmezett Windows DNS-ügyfél rendelkezik egy beépített DNS-gyorsítótárában. Egyes Linux-disztribúciókon gyorsítótárazás alapértelmezés szerint nem tartalmazzák. Ha azt tapasztalja, hogy még nem található a helyi gyorsítótárat, a DNS-gyorsítótár hozzá minden egyes Linux virtuális gép.

Számos különböző DNS-gyorsítótárazást (például dnsmasq) elérhető csomagokat. A következő rendszerek leggyakoribb disztribúciói dnsmasq telepítése:

* **Ubuntu (használ resolvconf)**:
  * Telepítse a dnsmasq csomagot `sudo apt-get install dnsmasq`.
* **SUSE (használ netconf)**:
  * Telepítse a dnsmasq csomagot `sudo zypper install dnsmasq`.
  * Engedélyezze a dnsmasq szolgáltatást a `systemctl enable dnsmasq.service`. 
  * Indítsa el a dnsmasq szolgáltatást a `systemctl start dnsmasq.service`. 
  * Szerkesztés **/etc/sysconfig/network/config**, és módosítsa *NETCONFIG_DNS_FORWARDER = ""* való *dnsmasq*.
  * A resolv.conf frissítése `netconfig update`, hogy a gyorsítótár állítja be a helyi DNS-feloldási.
* **OpenLogic (NetworkManager használ)**:
  * Telepítse a dnsmasq csomagot `sudo yum install dnsmasq`.
  * Engedélyezze a dnsmasq szolgáltatást a `systemctl enable dnsmasq.service`.
  * Indítsa el a dnsmasq szolgáltatást a `systemctl start dnsmasq.service`.
  * Adjon hozzá *illesztenie a tartomány-name-kiszolgálók 127.0.0.1;* való **/etc/dhclient-eth0.conf**.
  * Indítsa újra a hálózati szolgáltatást a `service network restart`, hogy a gyorsítótár állítja be a helyi DNS-feloldási.

> [!NOTE]
> A dnsmasq csomag nincs sok DNS gyorsítótárak érhető el Linux rendszeren csak az egyiket. Mielőtt használja, az adott igényeknek való alkalmasságát, és ellenőrizze, hogy telepítve van-e más gyorsítótár.
>
>
    
### <a name="client-side-retries"></a>Ügyféloldali újrapróbálkozások

DNS egy elsősorban olyan UDP protokoll. Az UDP protokoll nem garantálja az üzenetek kézbesítését, mert újrapróbálkozási logikát kezeli a DNS protokoll magát. Minden DNS-ügyfél (operációs rendszer) eltérő újrapróbálkozási logika, a létrehozó szabályozó függően is mutat:

* Windows operációs rendszerek újrapróbálkozás egy második, és ezután újra egy másik két másodpercen, négy másodperc és a egy másik négy másodperc után. 
* Az alapértelmezett Linux telepítő újrapróbálkozások öt másodperc múlva. Javasoljuk, hogy váltson az újrapróbálkozási előírások ötször, hogy egy másodperces időközönként.

Ellenőrizze a jelenlegi beállításait a Linuxos virtuális gépre `cat /etc/resolv.conf`. Tekintse meg a *beállítások* . sor, például:

```bash
options timeout:1 attempts:5
```

A resolv.conf fájlt általában automatikusan létrehozott, és nem szerkeszthetők. Az adott hozzáadásának lépései a *beállítások* terjesztési kisszolgáló sor:

* **Ubuntu** (resolvconf használja):
  1. Adja hozzá a *beállítások* a sor **/etc/resolvconf/resolv.conf.d/tail**.
  2. Futtatás `resolvconf -u` frissíteni.
* **SUSE** (netconf használja):
  1. Adjon hozzá *timeout:1 kísérletek: 5* , a **NETCONFIG_DNS_RESOLVER_OPTIONS = ""** paraméter **/etc/sysconfig/network/config**.
  2. Futtatás `netconfig update` frissíteni.
* **OpenLogic** (NetworkManager használja):
  1. Adjon hozzá *echo "beállítások timeout:1 kísérletek: 5"* való **/etc/NetworkManager/dispatcher.d/11-dhclient**.
  2. Frissítse `service network restart`.

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Névfeloldás saját DNS-kiszolgálót használó

Ez a szakasz ismerteti a virtuális gépek, a szerepkör példányai és a web apps.

### <a name="vms-and-role-instances"></a>Virtuális gépek és szerepkörpéldányok

A névfeloldási szükségleteit előfordulhat, hogy nemcsak az Azure által kínált szolgáltatásokkal. Szüksége lehet például a Microsoft Windows Server Active Directory-tartományok DNS-nevek a virtuális hálózatok között. Ezeket a forgatókönyveket fednek le, az Azure lehetővé teszi a saját DNS-kiszolgálók használatához.

DNS-kiszolgálók virtuális hálózaton belüli továbbíthatja a DNS-lekérdezések, az Azure-ban a rekurzív feloldók. Ez lehetővé teszi a gazdagépnevekhez adott virtuális hálózaton belül. Például egy Azure-ban futó tartományvezérlő (DC) is a tartomány DNS-lekérdezései válaszolni, és minden más lekérdezés továbbítása az Azure-bA. Lekérdezések továbbítása lehetővé teszi, hogy a virtuális gép, mind a helyszíni erőforrásokhoz (keresztül a tartományvezérlő) és az Azure által biztosított állomásnevek (keresztül a továbbító). Az Azure-ban a rekurzív feloldók hozzáférést biztosítunk 168.63.129.16 virtuális IP-címen keresztül.

DNS-továbbítást is lehetővé teszi a DNS-feloldási virtuális hálózatok között, és lehetővé teszi, hogy a helyszíni gépek Azure által biztosított állomásneveket. Annak érdekében, hogy a virtuális gép állomásnevét feloldani, a DNS-kiszolgáló virtuális gép ugyanazon a virtuális hálózaton kell lennie, és úgy konfigurálni, hogy előre állomás-lekérdezések az Azure. A DNS-utótagja eltér az egyes virtuális hálózatok, mert a feloldásához a megfelelő virtuális hálózat DNS-lekérdezéseket küldjön feltételes továbbítás szabályok használatával. Az alábbi képen látható a két virtuális hálózat és a egy helyszíni hálózattal, ennek során a DNS-feloldási virtuális hálózatok között, ez a módszer használatával. Egy példa DNS-továbbító érhető el a [Azure gyorsindítási sablonok katalógusában](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) és [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> Szerepkörpéldányok névfeloldása a virtuális gépek az adott virtuális hálózaton belül hajthat végre. A teljes tartománynév, amely tartalmazza a virtuális gép állomásnevét használatával hajtja végre, és **internal.cloudapp.net** DNS-utótagot. Azonban ebben az esetben névfeloldás csak akkor sikeres, ha a szerepkörpéldány rendelkezik a megadott virtuális gép nevét a [szerepkör sémáját (.cscfg fájl)](https://msdn.microsoft.com/library/azure/jj156212.aspx).
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> Szeretne végrehajtani a névfeloldást a virtuális gépek egy másik virtuális hálózatban lévő szerepkörpéldányok (teljes tartománynév használatával a **internal.cloudapp.net** utótag) van ehhez a (egyéni DNS-kiszolgálók közötti továbbítás ebben a szakaszban leírt módszer használatával a két virtuális hálózat).
>

![Virtuális hálózatok közötti DNS ábrája](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Az Azure által biztosított névfeloldást használ, amikor Azure Dynamic Host Configuration Protocol (DHCP) biztosít olyan belső DNS-utótagot (**. internal.cloudapp.net**) minden egyes virtuális géphez. Ennek az utótagnak állomásnév-feloldás lehetővé teszi, mivel a gazdagép neve rekordokat a **internal.cloudapp.net** zóna. Ha saját név feloldása megoldást használ, ennek az utótagnak nincs megadva virtuális gépekhez, mivel ez rontja más DNS-architektúrákat (például a tartományhoz csatlakoztatott forgatókönyv). Ehelyett az Azure biztosít a nem működő helyőrző (*reddog.microsoft.com*).

Szükség esetén a PowerShell vagy az API segítségével meghatározhatja a belső DNS-utótag:

* Az Azure Resource Manager üzemi modellekben található virtuális hálózatok, az utótag keresztül érhető el a [REST API-t hálózati adapter](https://docs.microsoft.com/rest/api/virtualnetwork/networkinterfaces), a [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) PowerShell-parancsmagot, és a [ az network nic show](/cli/azure/network/nic#az-network-nic-show) Azure CLI-parancsot.
* A klasszikus üzemi modellel, az utótag keresztül érhető el a [első üzembe helyezési API](https://msdn.microsoft.com/library/azure/ee460804.aspx) hívás vagy a [Get-AzureVM-Debug](/powershell/module/servicemanagement/azure/get-azurevm) parancsmagot.

Lekérdezések továbbítása az Azure-ba nem felel meg igényeinek, ha meg kell adnia a saját DNS-megoldást. A DNS-megoldást kell:

* Névfeloldásához megfelelő gazdagépre, keresztül [DDNS](virtual-networks-name-resolution-ddns.md), például. Ha DDNS-t használja, szüksége lehet letiltani a DNS-rekord kitakarítási. Az Azure DHCP-bérleteket hosszú, és kitakarítási túl korán DNS-rekordok elvesztésével. 
* Adja meg a megfelelő rekurzív megoldás, hogy a külső tartománynevek feloldását.
* Elérhető-e (TCP és UDP 53-as porton) az ügyfelek szolgál, és hozzáférhet az interneten.
* Védhető szemben az internetről, külső ügynökök által jelentett fenyegetéseket csökkentése érdekében.

> [!NOTE]
> A legjobb teljesítmény érdekében az Azure virtuális gépek DNS-kiszolgálóként használata esetén IPv6 le kell tiltani. A [nyilvános IP-cím](virtual-network-public-ip-address.md) minden DNS-kiszolgáló virtuális Géphez hozzá kell rendelni. További Teljesítményelemzés és optimalizálási lehetőségek a Windows Server, a DNS-kiszolgáló használata esetén lásd [név feloldása teljesítményét egy rekurzív Windows DNS Server 2012 R2](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx).
> 
> 

### <a name="web-apps"></a>Webalkalmazások
Tegyük fel, hogy kell végrehajtani a névfeloldást a webalkalmazást az App Service-ben, egy virtuális hálózatban, az azonos virtuális hálózatban lévő virtuális gépekhez csatolva a. Egy egyéni DNS beállítása mellett kiszolgálót, egy DNS-továbbító, amely továbbítja a lekérdezéseket az Azure-ba (168.63.129.16 virtuális IP) hajtsa végre az alábbi lépéseket:
1. A webalkalmazás virtuális hálózati integráció engedélyezése, ha még nem adta, leírtak szerint [az alkalmazás és a egy virtuális hálózat integrációja](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Az Azure Portalon, az App Service-csomagban a webalkalmazás üzemeltetési válassza **hálózat szinkronizálása** alatt **hálózatkezelés**, **virtuális hálózati integráció**.

    ![Képernyőkép a virtuális hálózati névfeloldás](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Ha kell végrehajtani a névfeloldást a webalkalmazást az App Service-ben, egy virtuális hálózatot, egy másik virtuális hálózatot a virtuális gépekhez kapcsolódik, hogy egyéni DNS-kiszolgálókat használja mindkét virtuális hálózat, a következőképpen:

* Állítsa be a virtuális gép, amely lekérdezéseket is továbbíthat, a rekurzív feloldó, az Azure-ban (a 168.63.129.16 IP virtuális) a cél virtuális hálózat DNS-kiszolgáló. Egy példa DNS-továbbító érhető el a [Azure gyorsindítási sablonok katalógusában](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) és [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Állítsa be a DNS-továbbítók a forrás virtuális hálózatban a virtuális gép. Konfigurálja a DNS-továbbító továbbítják a lekérdezéseket a cél virtuális hálózat DNS-kiszolgálóra.
* A forrás virtuális hálózati beállításokat a forráskiszolgáló DNS konfigurálása
* Engedélyezze a virtuális hálózat integrációja a webes alkalmazás összekapcsolása a forrás virtuális hálózaton lévő utasítások követése [az alkalmazás és a egy virtuális hálózat integrációja](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Az Azure Portalon, az App Service-csomagban a webalkalmazás üzemeltetési válassza **hálózat szinkronizálása** alatt **hálózatkezelés**, **virtuális hálózati integráció**.

## <a name="specify-dns-servers"></a>Adja meg a DNS-kiszolgálók
Amikor a saját DNS-kiszolgálókat használ, az Azure biztosít több DNS-kiszolgálók száma virtuális hálózatonként megadásának lehetőségét. Megadhat több DNS-kiszolgálók száma hálózati adapterenként (az Azure Resource Manager) vagy egy (esetén a klasszikus üzemi modellhez) is. Egy hálózati adapter vagy a felhőben szolgáltatás számára megadott DNS-kiszolgálók elsőbbséget készítsen a virtuális hálózat számára megadott DNS-kiszolgálók.

> [!NOTE]
> Hálózati kapcsolat tulajdonságai, például a DNS-kiszolgáló IP-címek, a Windows virtuális gépek közvetlenül belül nem szerkeszthetők. Ennek az az oka, előfordulhat, hogy első törlése során a szolgáltatás lekérdezi a virtuális hálózati adapterek szövegelemekben javítása.
>
>

Ha az Azure Resource Manager üzembe helyezési modellt használ, a virtuális hálózat és a egy hálózati adapter DNS-kiszolgálók is megadhat. További információkért lásd: [virtuális hálózat kezelése](manage-virtual-network.md) és [egy hálózati adapter kezelése](virtual-network-network-interface.md).

> [!NOTE]
> Amennyiben egyéni DNS-kiszolgáló a virtuális hálózathoz, meg kell adnia legalább egy DNS kiszolgálón IP-cím; Ellenkező esetben virtuális hálózat figyelmen kívül hagyja a konfigurációt, és használja helyette az Azure által biztosított DNS.
>
>

Ha a klasszikus üzemi modellt használja, megadhatja az Azure Portalon a virtuális hálózathoz tartozó DNS-kiszolgálók vagy a [hálózati konfigurációs fájlt](https://msdn.microsoft.com/library/azure/jj157100). A cloud services esetében megadhatja a DNS-kiszolgálókon keresztül a [szolgáltatás konfigurációs fájlja](https://msdn.microsoft.com/library/azure/ee758710) vagy PowerShell-lel, az [New-AzureVM](/powershell/module/servicemanagement/azure/new-azurevm).

> [!NOTE]
> Ha módosítja egy virtuális hálózat vagy a virtuális gépet, amely már telepítve van a DNS-beállításait, meg kell minden érintett virtuális gép a módosítások érvénybe léptetéséhez indítsa újra.
>
>

## <a name="next-steps"></a>További lépések

Az Azure Resource Manager üzemi modell:

* [Virtuális hálózat kezelése](manage-virtual-network.md)
* [Hálózati adapter kezelése](virtual-network-network-interface.md)

Klasszikus üzemi modell:

* [Az Azure szolgáltatás konfigurációs sémáját](https://msdn.microsoft.com/library/azure/ee758710)
* [Virtual Network konfigurációs séma](https://msdn.microsoft.com/library/azure/jj157100)
* [Virtuális hálózat konfigurálása hálózati konfigurációs fájl használatával](virtual-networks-using-network-configuration-file.md)
