---
title: Kapcsolatokkal és hálózatkezeléssel kapcsolatos problémák a Microsoft Azure Cloud Services – gyakori kérdések |} A Microsoft Docs
description: Ez a cikk a kapcsolatokkal és hálózatkezeléssel, a Microsoft Azure Cloud Services – gyakori kérdések listája.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2018
ms.author: genli
ms.openlocfilehash: 034d59c39628a08c389c5ceb67c5872bbea10d59
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223168"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Az Azure Cloud Services kapcsolatokkal és hálózatkezeléssel kapcsolatos problémák: gyakran ismételt kérdések (GYIK)

Ez a cikk tartalmazza a kapcsolatokkal és hálózatkezeléssel kapcsolatos gyakori kérdésekre [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Méret információkért lásd: a [Cloud Services Virtuálisgép-méret lap](cloud-services-sizes-specs.md).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Nem foglalható le egy IP-cím több virtuális IP-felhőszolgáltatásban.
Győződjön meg róla, hogy be van kapcsolva a virtuálisgép-példányt, amely megpróbálja lefoglalni az IP-címeként. A második győződjön meg arról, hogy az átmeneti és éles környezetet egyaránt használjon fenntartott IP-címek. *Ne* módosítsa a beállításokat, amíg az üzemelő példány frissítése folyamatban van.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Hogyan használhatom a távoli asztal, ha van egy NSG-t?
Adjon hozzá szabályokat az NSG-hez, amelyek lehetővé teszik a forgalom a portokon **3389-es** és **20000**. A távoli asztal-portot használja **3389-es**. Felhőalapú szolgáltatás példányai terhelésű, így közvetlenül nem szabályozhatja, mely példányhoz való csatlakozáshoz. A *RemoteForwarder* és *RemoteAccess* ügynökök kezelése a távoli asztal protokoll (RDP) forgalmat, és az ügyfél egy RDP-cookie küldhet, és adjon meg egy egyéni példány való csatlakozáshoz. A *RemoteForwarder* és *RemoteAccess* ügynökök szükséges port **20000** ahhoz, hogy nyitott-e, amely blokkol, ha van egy NSG-t.

## <a name="can-i-ping-a-cloud-service"></a>Megpingelheti a cloud service?

Nem, nem a normál "ping" használatával vagy az ICMP protokollt. Az Azure load balanceren keresztül nem engedélyezett az ICMP protokollt.

Kapcsolat tesztelése, azt javasoljuk, hogy végrehajtja-e a port pingelésre. Ping.exe az ICMP, míg más eszközökkel, például a PSPing Nmap és telnet, használhatja egy adott TCP-portot a kapcsolat teszteléséhez.

További információkért lásd: [port pingelésre használata ICMP helyett az Azure-beli Virtuálisgép-kapcsolat tesztelése](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Hogyan akadályozható meg forgalmat fogadó ismeretlen IP-címek, amelyek esetleg jelzik a rosszindulatú támadások, a felhőszolgáltatás-találatok ezer?
Az Azure platform szolgáltatásaiból elosztott-szolgáltatásmegtagadásos (DDoS) támadások elleni védelme érdekében többrétegű hálózati biztonsági valósítja meg. Az Azure DDoS elleni védelmi rendszert az Azure folyamatos figyelési folyamat, amely folyamatosan fejlesztjük és keresztül behatolásvizsgálat részét képezi. A DDoS elleni védelmi rendszert úgy tervezték, hogy nem csak a kívülről, hanem a többi Azure bérlőtől támadások ellen. További információkért lásd: [Azure hálózati biztonság](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Emellett létrehozhat egy indítási feladat néhány konkrét IP-cím szelektív letiltása. További információkért lásd: [letilthatja egy adott IP-cím](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Ha a saját felhőszolgáltatás-példányok meg az RDP-hez, az üzenet jelenik meg "a felhasználói fiók lejárt."
Az "Ez a felhasználói fiók lejárt" hibaüzenet fordulhatnak elő, ha a lejárati dátum, az RDP-beállítások között megadott megkerülése. A lejárati dátumot a következő lépésekkel módosíthatja a portálról:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com)nyissa meg a felhőalapú szolgáltatás, és válassza ki a **a távoli asztal** fülre.

2. Válassza ki a **éles** vagy **átmeneti** üzembe helyezési pont.

3. Módosítsa a **lejárati dátuma** dátum, és mentse a konfigurációt.

Most tudnia kell az RDP-hez a gépre.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Miért érdemes az Azure Load Balancer nem osztja el forgalom egyaránt?
Belső terheléselosztó működésével kapcsolatos további információkért lásd: [Azure Load Balancer új terjesztési mód](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

A használt terjesztési algoritmus egy 5 rekordos (forrás IP-címe, forrásport, cél IP-cím, céloldali port és protokoll típusát) forgalmat leképezése a rendelkezésre álló kiszolgálók kivonat. Csak egy átviteli munkamenet belül biztosít a tartós használat. Ugyanabban a TCP vagy UDP-munkamenetben csomagok mögött az elosztott terhelésű végpont ugyanazon adatközpontban IP (DIP) példányon irányítja. Amikor az ügyfél bezárja és újra megnyitja a kapcsolatot, vagy egy új munkamenet indul az azonos forrás IP-címe, Forrásport módosítja, és hatására a forgalom egy másik DIP-végpont ugorhat.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Hogyan lehet a bejövő forgalom átirányítása alapértelmezett URL-címét a felhőszolgáltatás, amely egy egyéni URL-cím? 

Az IIS URL-újraíró modult átirányítja a forgalmat, amely az alapértelmezett URL-címet a felhőszolgáltatás számára is használható (például \*. cloudapp.net) néhány egyéni nevet/URL-címre. Az URL-újraíró modult a webes szerepkörök alapértelmezés szerint engedélyezve van, és a szabályok úgy vannak konfigurálva, az alkalmazás Web.config, mert, mindig elérhető, a virtuális gép újraindítása vagy különbséglemezt függetlenül. További információkért lásd:

- [Az URL-újraíró modult újraírási szabályok létrehozása](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Alapértelmezett hivatkozás eltávolítása](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Hogyan lehet I letiltása vagy letiltását bejövő forgalom alapértelmezett URL-címét a felhőszolgáltatásban? 

Az alapértelmezett URL-címe vagy neve a felhőszolgáltatás képes megakadályozni a bejövő forgalom (például \*. cloudapp.net). Állítsa be az állomásfejléc egyedi DNS-nevet (például www.MyCloudService.com), a hely kötésének konfigurációja a felhőalapú szolgáltatás definíciós (*.csdef) fájlban jelzett módon: 
 

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
 
A gazdagép fejléc kötés csdef fájl ki, mert a szolgáltatás nem csak elérhetők az egyéni név "www.MyCloudService.com." Az összes beérkező kérések a "*. cloudapp.net" tartomány mindig sikertelen. Egyéni mintavétel SLB vagy a belső terheléselosztó a szolgáltatást használja, ha blokkolja az alapértelmezett szolgáltatás URL-címe vagy neve zavarhatják ellenőrzési viselkedését. 

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Hogyan biztosítható egy felhőalapú szolgáltatás nyilvános IP-címét soha nem változik?

Győződjön meg arról, hogy a felhőszolgáltatás (más néven VIP) nyilvános IP-címét soha nem változik, hogy azok a szokásos néhány konkrét ügyfelek szerepel az engedélyezési listán, azt javasoljuk, hogy rendelkezik-e a foglalt IP-címhez társítva. Ellenkező esetben a virtuális IP-cím az Azure által biztosított fel van szabadítva az előfizetésből a központi telepítés törlése. A sikeres virtuális IP-címeket cserélő művelet szüksége lesz a termelési és az előkészítési pont az egyes fenntartott IP-címek. Anélkül, hogy azok a címeket cserélő művelet sikertelen lesz. Fenntartott IP-címet, és társíthatja azt a cloud service, tanulmányozza a következő cikkeket:
 
- [Egy létező felhőszolgáltatás IP-címének fenntartása](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [Felhőszolgáltatás fenntartott IP társítása a szolgáltatás konfigurációs fájl segítségével](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file) 

Ha egynél több példánnyal rendelkezik a szerepkörök, RIP társítása a felhőszolgáltatás nem ajánlott bármilyen üzemkimaradást eredményezhet. Azt is megteheti akkor az az IP-címtartományt, az Azure-adatközpont engedélyezett. Az összes Azure IP-címtartományok, annak a [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=41653). 

Ez a fájl tartalmazza az IP-címtartományokat (beleértve compute, SQL és storage tartományokat) használt Azure-adatközpontokban. A frissített, amely tükrözi a jelenleg üzembe helyezett tartományokat és minden jövőbeni változtatásokról, az IP tartományokat heti tesznek közzé. A fájlban megjelenő új tartományokat legalább egy hétig nem használják az adatközpontokban. Töltse le az új .xml-fájlt minden héten, és hajtsa végre a szükséges változtatásokat oldalán, hogy helyesen azonosítsa az Azure-ban futó szolgáltatásokat. Az Azure ExpressRoute-felhasználók előfordulhat, hogy vegye figyelembe, hogy ezt a fájlt használja, minden hónap első hetében Azure címterületek BGP hirdetésből frissíteni. 

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Hogyan használhatom az Azure Resource Managerbeli virtuális hálózat a cloud services? 

A cloud services az Azure Resource Managerbeli virtuális hálózat nem helyezhető el. Virtuális hálózatok Resource Manager és klasszikus üzembe helyezés virtuális hálózatok társviszony-létesítésen keresztül csatlakozhatnak. További információkért lásd: [virtuális hálózatok közötti társviszony](../virtual-network/virtual-network-peering-overview.md).


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Hogyan kaphatok a Cloud Services által használt nyilvános IP-címek listáját?

PS-parancsprogram a következő használatával a Cloud Services nyilvános IP-címek listájának lekérése az előfizetéséhez

    $services = Get-AzureService  | Group-Object -Property ServiceName

    foreach ($service in $services) 
    {
        "Cloud Service '$($service.Name)'"

        $deployment = Get-AzureDeployment -ServiceName $service.Name 
        "VIP - " +  $deployment.VirtualIPs[0].Address
        "================================="
    }
