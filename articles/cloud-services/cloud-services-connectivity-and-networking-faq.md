---
title: Kapcsolatokkal és hálózatkezeléssel kapcsolatos problémák
titleSuffix: Azure Cloud Services
description: Ez a cikk a Microsoft Azure Cloud Services kapcsolatával és hálózatával kapcsolatos gyakori kérdéseket sorolja fel.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/23/2018
ms.author: genli
ms.openlocfilehash: 7caeba0e88f63106eae80f7142b5d65463f8d7a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019400"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Kapcsolódási és hálózati problémák az Azure Cloud Services szolgáltatáshoz: gyakori kérdések (gyakori kérdések)

Ez a cikk az Azure Cloud Services kapcsolódási és hálózati problémáival kapcsolatos gyakori kérdéseket [tartalmazza.](https://azure.microsoft.com/services/cloud-services) A méretről a [Felhőszolgáltatások virtuális gépének méretlapján](cloud-services-sizes-specs.md)talál.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Nem tudok IP-címet foglalni egy több VIP-alapú felhőszolgáltatásban.
Először győződjön meg arról, hogy a virtuális gép példánya, amely megpróbálja lefoglalni az IP-cím be van kapcsolva. Másodszor győződjön meg arról, hogy a fenntartott IP-k használata az átmeneti és az éles környezetben. *Ne* módosítsa a beállításokat, amíg a központi telepítés frissítés.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Hogyan használhatom a Távoli asztalt, ha NSG-m van?
Adjon hozzá olyan szabályokat az NSG-hez, amelyek engedélyezik a forgalmat a **3389-es** és **a 20000-es**porton. A Távoli asztal a **3389-es portot**használja. A felhőszolgáltatás-példányok terheléselosztásosak, így nem szabályozhatja közvetlenül, hogy melyik példányhoz csatlakozzon. A *RemoteForwarder* és *a RemoteAccess* ügynökök kezelik az RDP-forgalmat, és lehetővé teszik az ügyfél számára, hogy RDP cookie-t küldjön, és megadjon egy egyedi példányt, amelyhez csatlakozni szeretne. A *RemoteForwarder* és *a RemoteAccess* ügynökök megkövetelik port **20000** meg kell nyitni, amely lehet blokkolni, ha van egy NSG.

## <a name="can-i-ping-a-cloud-service"></a>Pingelhetek egy felhőszolgáltatást?

Nem, nem a normál "ping"/ICMP protokoll használatával. Az ICMP protokoll nem engedélyezett az Azure terheléselosztón keresztül.

A kapcsolat teszteléséhez azt javasoljuk, hogy a port pingelése. Míg a Ping.exe az ICMP-t használja, más eszközökkel is használhat, például a PSPing, az Nmap és a telnet egy adott TCP-porthoz való kapcsolódás teszteléséhez.

További információ: [Port pingelések használata az ICMP helyett az Azure virtuális gép kapcsolatának teszteléséhez.](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/)

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Hogyan akadályozhatom meg, hogy több ezer ismeretlen IP-címről érkező lekérést kapjon, ami rosszindulatú támadást jelezhet a felhőszolgáltatás ellen?
Az Azure többrétegű hálózati biztonságot valósít meg, hogy megvédje platformszolgáltatásait az elosztott szolgáltatásmegtagadási (DDoS) támadásoktól. Az Azure DDoS védelmi rendszer része az Azure folyamatos figyelési folyamatának, amely a penetrációs tesztelés révén folyamatosan javul. Ez a DDoS védelmi rendszer nem csak a külső, hanem más Azure-bérlők támadásainak is ellenáll. További információ: [Azure network security](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Indítási feladatot is létrehozhat, hogy szelektíven blokkoljon bizonyos IP-címeket. További információ: [Egy adott IP-cím letiltása.](cloud-services-startup-tasks-common.md#block-a-specific-ip-address)

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Amikor megpróbálom az RDP-t a felhőalapú szolgáltatáspéldányra, a következő üzenet jelenik meg: "A felhasználói fiók lejárt."
Az RDP-beállításokban konfigurált lejárati dátum megkerülésekénél az "Ez a felhasználói fiók lejárt" hibaüzenet jelenhet meg. A lejárati dátumot a portálról az alábbi lépésekkel módosíthatja:

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)nyissa meg a felhőszolgáltatást, és válassza a **Távoli asztal** lapot.

2. Válassza ki az **éles** vagy **átmeneti** központi telepítési helyet.

3. Módosítsa a **Lejár a dátumot,** majd mentse a konfigurációt.

Most már képesnek kell lennie arra, hogy RDP a gépre.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Miért nem egyenlően osztja el a forgalmat az Azure Load Balancer?
A belső terheléselosztó működéséről az [Azure Load Balancer új terjesztési módjában](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/)talál további információt.

A használt terjesztési algoritmus egy 5-t, forrás IP, forrásport, cél IP, célport és protokolltípus) kivonata a forgalom nak az elérhető kiszolgálókhoz való hozzárendeléséhez. Csak a szállítási munkameneten belül biztosítja a ragacsosságot. Az azonos TCP- vagy UDP-munkamenetben lévő csomagok a terheléselosztási végpont mögött idopontként lévő adatközpontI IP-példányra (DIP) irányulnak. Amikor az ügyfél bezárja és újra megnyitja a kapcsolatot, vagy új munkamenetet indít ugyanabból a forrás IP-címről, a forrásport megváltozik, és a forgalmat egy másik DIP-végpontra eredményezi.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Hogyan irányíthatom át a bejövő forgalmat a felhőszolgáltatásom alapértelmezett URL-címére egy egyéni URL-re?

Az IIS URL-újraírási modulja segítségével átirányíthatja a felhőszolgáltatás alapértelmezett URL-címére (például \*.cloudapp.net) irányuló forgalmat valamilyen egyéni névre/URL-re. Mivel az URL-újraírási modul alapértelmezés szerint engedélyezve van a webes szerepkörökön, és a szabályok az alkalmazás web.config-jában vannak konfigurálva, mindig elérhető a virtuális gépen, függetlenül az újraindításoktól/újraképektől. További információ:

- [Újraírási szabályok létrehozása az URL-újraírási modulhoz](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Alapértelmezett hivatkozás eltávolítása](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Hogyan tudom blokkolni/letiltani a bejövő forgalmat a felhőszolgáltatás omlatai alapértelmezett URL-címére?

Megakadályozhatja a bejövő forgalmat a felhőszolgáltatás alapértelmezett URL-címére/nevére (például \*.cloudapp.net). Állítsa az állomásfejlécet egyéni DNS-névre\.(például www MyCloudService.com) a felhőszolgáltatás-definíciós (*.csdef) fájl helykötési konfigurációja alatt, ahogy az jelezt:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
    <WebRole name="MyWebRole" vmsize="Small">
        <Sites>
            <Site name="Web">
            <Bindings>
                <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" />
            </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
</ServiceDefinition>
```

Mivel ez az állomásfejléc-kötés a csdef fájlon keresztül van kényszerítve, a szolgáltatás csak a "www.MyCloudService.com" egyéni névvel érhető el. A "*.cloudapp.net" tartományba érkező összes bejövő kérelem mindig sikertelen. Ha egyéni SLB-mintavételt vagy belső terheléselosztót használ a szolgáltatásban, a szolgáltatás alapértelmezett URL-címének/nevének letiltása akadályozhatja a szondázási viselkedést.

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Hogyan győződhetek meg arról, hogy a felhőszolgáltatás nyilvános IP-címe soha nem változik?

Annak érdekében, hogy a felhőszolgáltatás nyilvános IP-címe (más néven VIP) soha ne változzon meg, hogy néhány konkrét ügyfél rendszerint engedélyezési listára kerüljön, javasoljuk, hogy egy fenntartott IP-címet társítson hozzá. Ellenkező esetben az Azure által biztosított virtuális IP-cím fellesz terhelve az előfizetésből, ha törli a központi telepítést. A sikeres VIP-csereművelethez egyéni fenntartott IP-kre van szükség mind az éles, mind az átmeneti tárolóhelyekhez. Nélkülük a csereművelet sikertelen. Ha le szeretne foglalni egy IP-címet, és hozzá szeretné rendelni a felhőszolgáltatáshoz, olvassa el az alábbi cikkeket:

- [Meglévő felhőszolgáltatás IP-címének lefoglalása](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
- [Fenntartott IP-cím társítása felhőszolgáltatáshoz szolgáltatáskonfigurációs fájl használatával](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

Ha egynél több példány a szerepkörök, a RIP társítása a felhőalapú szolgáltatás nem okoz leállást. Azt is megteheti, hogy hozzáadja az Azure-adatközpont IP-tartományát egy engedélyezési listához. Az összes Azure IP-tartománymegtalálható a [Microsoft letöltőközpontjában.](https://www.microsoft.com/en-us/download/details.aspx?id=41653)

Ez a fájl tartalmazza az Azure adatközpontokban használt IP-címtartományokat (beleértve a számítási, SQL- és tárolási tartományokat). Egy frissített fájl kiadása hetente, amely tükrözi az aktuálisan telepített tartományok és a közelgő változások az IP-tartományok. A fájlban megjelenő új tartományoklegalább egy hétig nem használatosak az adatközpontokban. Töltse le az új .xml fájlt minden héten, és hajtsa végre a szükséges módosításokat a webhelyen az Azure-ban futó szolgáltatások helyes azonosításához. Az Azure ExpressRoute-felhasználók megjegyezhetik, hogy ez a fájl az Azure-tárhely BGP-hirdetésének frissítésére használt minden hónap első hetében.

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Hogyan használhatom az Azure Resource Manager virtuális hálózatait felhőszolgáltatásokkal?

A felhőszolgáltatások nem helyezhetők el az Azure Resource Manager virtuális hálózataiban. Az Erőforrás-kezelő virtuális hálózatai és a klasszikus üzembe helyezési virtuális hálózatok társviszony-létesítéssel is összekapcsolhatók. További információt a [Virtuális hálózati társviszony-létesítés](../virtual-network/virtual-network-peering-overview.md)című témakörben talál.


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Hogyan szerezhetem be a Felhőszolgáltatásaim által használt nyilvános IP-k listáját?

A következő PS-parancsfájl segítségével lekaphatja az előfizetése alatt a felhőszolgáltatások nyilvános IP-csomagjának listáját

```powershell
$services = Get-AzureService  | Group-Object -Property ServiceName

foreach ($service in $services)
{
    "Cloud Service '$($service.Name)'"

    $deployment = Get-AzureDeployment -ServiceName $service.Name
    "VIP - " +  $deployment.VirtualIPs[0].Address
    "================================="
}
```
