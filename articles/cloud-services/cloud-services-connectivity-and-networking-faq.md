---
title: Az ügyfélkapcsolódási és hálózati problémák a Microsoft Azure Cloud Services – gyakori kérdések |} Microsoft Docs
description: Ez a cikk felsorolja a kapcsolat és hálózati kapcsolatok a Microsoft Azure Felhőszolgáltatások kapcsolatos gyakori kérdésekre.
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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: ab0fa22e9ba776db3d4af301499545f6e0822478
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34070171"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Az ügyfélkapcsolódási és hálózati problémák Azure-szolgáltatásokhoz: gyakran ismételt kérdések (GYIK)

Ez a cikk tartalmazza a kapcsolat és a hálózati hibákkal kapcsolatos gyakran ismételt kérdések [Azure Felhőszolgáltatások](https://azure.microsoft.com/services/cloud-services). Méret információkért lásd: a [lap a felhőalapú szolgáltatások virtuális gép méretét](cloud-services-sizes-specs.md).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Nem foglalható le több virtuális IP-CÍMES felhőszolgáltatásban olyan IP-címet.
Először is győződjön meg arról, hogy a virtuálisgép-példányt, amely az IP-Címek lefoglalása próbál be van-e kapcsolva. Második győződjön meg arról, hogy az átmeneti és éles központi telepítések használjon fenntartott IP-címek. *Ne* módosíthatja a beállításokat, amíg a telepítés frissítését végzi.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Hogyan használjam távoli asztal, ha egy NSG-t?
Szabályok hozzáadása az NSG-t, amelyek lehetővé teszik a forgalom a portokon **3389-es** és **20000**. A távoli asztal-portot használja **3389-es**. Felhőalapú szolgáltatás példányainak kerül, így nem közvetlenül szabályozhatja, hogy melyik példányt való csatlakozáshoz. A *RemoteForwarder* és *RemoteAccess* ügynökök Remote Desktop Protocol (RDP) forgalom kezelésére, és az ügyfél küldése az RDP cookie, és adjon meg egy egyedi példány való csatlakozáshoz. A *RemoteForwarder* és *RemoteAccess* ügynökök szükséges port **20000** nyitva kell lennie, amely blokkolhatja Ha egy NSG.

## <a name="can-i-ping-a-cloud-service"></a>Megpingelheti a cloud Service szolgáltatásra?

Nem, nem a normál "ping" használatával / ICMP protokollt. Az ICMP protokoll nem engedélyezett az Azure load balancer keresztül.

Kapcsolat tesztelése, azt javasoljuk, hogy egy port ping végezzen. Amíg Ping.exe az ICMP, más eszközök, például a PSPing Nmap vagy telnet, segítségével egy adott TCP-portot kapcsolat teszteléséhez.

További információkért lásd: [port ping-üzenetek használata helyett ICMP Azure Virtuálisgép-kapcsolat tesztelése](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Hogyan akadályozható meg fogadását, amelyek jelezhetik a felhőszolgáltatásba rosszindulatú támadások ismeretlen IP-címekről találatok ezer?
Azure bevezet egy többrétegű hálózati biztonság, a platform szolgáltatásaiból elosztott-szolgáltatásmegtagadásos (DDoS-) támadások elleni védelméhez. Az Azure DDoS védelmi rendszer Azure folyamatos figyelési folyamat része, amely folyamatosan keresztül tesztelés behatolást vagy a biztonság növelése. A DDoS védelmi rendszer célja, hogy nem csak a kívülről, hanem más Azure bérlő támadások ellen. További információkért lásd: [Azure hálózati biztonság](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Egy indítási feladat szelektív letiltása az egyes IP-címek is hozhat létre. További információkért lásd: [blokkolja egy adott IP-cím](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>A cloud service-példány a távoli asztali meg, üzenet jelenik meg "a felhasználói fiók lejárt."
Előfordulhat, hogy le a hibaüzenetet, "ez a felhasználói fiók lejárt", ha a lejárati dátum az RDP-beállítások között megadott megkerülése. Módosíthatja a lejárati dátumot a portálról az alábbiak szerint:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com)nyissa meg a felhőalapú szolgáltatás, és válassza ki a **távoli asztal** fülre.

2. Válassza ki a **éles** vagy **átmeneti** üzembe helyezési pont.

3. Módosítsa a **lejárati dátuma** dátum, és mentse a konfigurációt.

Most kell tudni az RDP a számítógépen.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Miért érdemes az Azure Load Balancer nem terheléselosztási forgalom egyaránt?
A belső terheléselosztók működésével kapcsolatos információért lásd: [Azure terheléselosztó új terjesztési mód](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

A használt algoritmus egy 5 rekordos (forrás IP-címe, forrásport, cél IP-címe, célport és protokoll típusát) kivonatoló forgalom hozzárendelése elérhető kiszolgálón. Csak egy átviteli munkamenet belül tölcsérútvonalak biztosít. Csomagok ugyanabban a TCP vagy UDP-munkamenetben a datacenter IP-címet (DIP) példányt az elosztott terhelésű végpont mögött van irányítva. Amikor az ügyfél bezárja és újra megnyitja a kapcsolatot, vagy új munkamenet indítása a azonos forrás IP-címről, a forrásport módosításai, és egy másik DIP végpont gomba a forgalmat.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Hogyan lehet bejövő forgalom átirányítása egy egyéni URL-címre a felhőalapú szolgáltatás alapértelmezett URL-címét? 

Az URL-újraíró modult az IIS, az alapértelmezett URL-címet a felhőszolgáltatás előre forgalom átirányítására használható (például \*. cloudapp.net) néhány egyéni neve vagy URL-címre. Mivel az URL-újraíró modult a webes szerepkörök alapértelmezés szerint engedélyezve van, és a szabályok úgy vannak konfigurálva, az alkalmazás Web.config fájlban, érhető el mindig újraindítások/reimages függetlenül a virtuális gépen. További információkért lásd:

- [Az URL-újraíró modult átdolgozás szabályok létrehozása](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Alapértelmezett hivatkozás eltávolítása](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Hogyan lehet I blokk vagy letiltását a felhőalapú szolgáltatás alapértelmezett URL-címét a bejövő forgalom? 

Az alapértelmezett URL-címe vagy neve a felhőalapú szolgáltatás képes megakadályozni a bejövő forgalom (például \*. cloudapp.net). Állítsa be az állomásfejlécnek egy egyéni DNS-nevét (például www.MyCloudService.com) webhely kötésének konfigurációja a felhő szolgáltatásdefiníciós fájlban (*.csdef), jelöli: 
 

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
 
A host fejléc kötést a csdef fájlon keresztül, mert a szolgáltatás nem csak az egyéni neve "www.MyCloudService.com." szolgáltatáson keresztül érhető el Beérkező összes kérelmet a "*. cloudapp.net" tartomány mindig sikertelen lesz. Egy egyéni SLB mintavételt vagy belső terheléselosztót a szolgáltatás használja, ha blokkolja az alapértelmezett URL-cím vagy a társított szolgáltatás neve előfordulhat, hogy zavarja a vizsgálathoz használt viselkedését. 

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Hogyan biztosítható a nyilvánosan elérhető IP-cím egy felhőalapú szolgáltatás soha nem változik?

Győződjön meg arról, hogy a felhőszolgáltatás (más néven VIP) a nyilvánosan elérhető IP-cím soha nem változik, hogy azok a szokásos szerepel az engedélyezési listán néhány adott az ügyfelek által, azt javasoljuk, hogy rendelkezik-e a foglalt IP-címhez társítva. Ellenkező esetben az Azure által biztosított virtuális IP-cím felszabadítása az előfizetésből Ha törli a központi telepítést. A sikeres VIP csereművelet egyes fenntartott IP-címek üzemi és átmeneti üzembe helyezési ponti kell. Nélkül, a cserélő művelet sikertelen lesz. Foglaljon le egy IP-címet, és rendelje hozzá azt a felhőalapú szolgáltatással, tekintse meg az alábbi cikkek:
 
- [Foglaljon le egy meglévő felhőszolgáltatáshoz IP-címe](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [A foglalt IP-cím egy felhőszolgáltatásra történő társíthatók a szolgáltatás konfigurációs fájl használatával](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file) 

Ha egynél több példánya van a szerepkörök, és a felhőszolgáltatás közötti társítás RIP nem szabad miatt leállási. Másik lehetőségként is engedélyezett az az Azure Datacenter IP-címtartományt. Minden Azure IP-címtartományok, megtalálhatja a [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=41653). 

Ez a fájl tartalmazza a használt IP-címtartományok (beleértve a tárolási, számítási és SQL tartományok) az Azure adatközpontjaiban. A frissített fáljra visszaküldi heti, amely tükrözi a jelenleg üzemelő tartományok és az IP-címtartományok jövőbeli módosításait. Új tartományok, amelyek szerepelnek a fájlban legalább egy hétig nem szerepel az adatközpontokban. Töltse le az új .xml fájl minden héten, és végezze el a helyet az Azure-ban futó szolgáltatások helyesen azonosítani a szükséges módosításokat. Az Azure ExpressRoute-felhasználók előfordulhat, hogy vegye figyelembe, hogy ezt a fájlt a BGP-hirdetményt Azure terület minden hónap első hetében frissítéséhez használja. 

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Hogyan használható Azure Resource Manager virtuális hálózatok a felhőszolgáltatásokkal? 

Cloud services Azure Resource Manager virtuális hálózatok nem helyezhető el. Erőforrás-kezelő virtuális hálózatok és a klasszikus üzembe helyezési virtuális hálózatok társviszony-létesítés keresztül csatlakozhatnak. További információkért lásd: [virtuális hálózati társviszony-létesítés](../virtual-network/virtual-network-peering-overview.md).
