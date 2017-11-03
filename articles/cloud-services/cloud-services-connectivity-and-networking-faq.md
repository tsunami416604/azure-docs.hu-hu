---
title: "Az ügyfélkapcsolódási és hálózati problémák a Microsoft Azure Cloud Services – gyakori kérdések |} Microsoft Docs"
description: "Ez a cikk felsorolja a kapcsolat és hálózati kapcsolatok a Microsoft Azure Felhőszolgáltatások kapcsolatos gyakori kérdésekre."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: genli
ms.openlocfilehash: 7b435b6904b05228a63e3ed3a9fed78747b843c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Az ügyfélkapcsolódási és hálózati problémák Azure-szolgáltatásokhoz: gyakran ismételt kérdések (GYIK)

Ez a cikk tartalmazza a kapcsolat és a hálózati hibákkal kapcsolatos gyakran ismételt kérdések [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Is tud kezelni a [felhőalapú szolgáltatások Virtuálisgép-méretet lap](cloud-services-sizes-specs.md) mérete információt.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>I multi-VIP felhőszolgáltatásban olyan IP-címet nem sikerült lefoglalni
Először is győződjön meg arról, hogy a virtuálisgép-példányt, amely az IP-Címek lefoglalása próbál be van-e kapcsolva. Ezután ellenőrizze, hogy az átmeneti és üzemi telepítések foglalt IP-cím használja. **Ne** módosíthatja a beállításokat, amíg a telepítés frissítését végzi.

## <a name="how-do-i-remote-desktop-when-i-have-an-nsg"></a>Hogyan készíthetek távoli asztal Ha egy NSG-t?
Szabályok hozzáadása az NSG-t, amelyek lehetővé teszik a forgalom a portokon **3389-es** és **20000**.  A távoli asztal-portot használja **3389-es**.  Felhőalapú szolgáltatás példányainak kerül, így nem közvetlenül szabályozhatja, hogy melyik példányt való csatlakozáshoz.  A *RemoteForwarder* és *RemoteAccess* ügynökök kezelése RDP-forgalmát, és az ügyfél küldése az RDP cookie, és adjon meg egy egyedi példány való csatlakozáshoz.  A *RemoteForwarder* és *RemoteAccess* ügynökök szükséges, hogy a port **20000** nyitható meg, amely blokkolhatja, ha egy NSG.

## <a name="can-i-ping-a-cloud-service"></a>Megpingelheti a cloud Service szolgáltatásra?

Nem, nem a normál "ping" használatával / ICMP protokollt. Az ICMP protokoll nem engedélyezett az Azure load balancer keresztül.

Kapcsolat tesztelése, azt javasoljuk, hogy egy port ping végezzen. Ping.exe az ICMP, míg más eszközök, például a PSPing Nmap vagy telnet engedélyezi, hogy egy adott TCP-portot kapcsolat teszteléséhez.

További információkért lásd: [port ping-üzenetek használata helyett ICMP Azure Virtuálisgép-kapcsolat tesztelése](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-indicate-some-sort-of-malicious-attack-to-the-cloud-service"></a>Hogyan akadályozható meg fogadását, amelyek jelzik a felhőszolgáltatás a rosszindulatú támadás valamilyen ismeretlen IP-címekről találatok ezer?
Azure bevezet egy többrétegű hálózati biztonság, a platform szolgáltatásaiból elosztott-szolgáltatásmegtagadásos (DDoS-) támadások elleni védelméhez. Az Azure DDoS védelmi rendszer Azure folyamatos figyelési folyamat része, amely folyamatosan növelése a behatolás-tesztelés keresztül. A DDoS védelmi rendszer célja, hogy nem csak a kívülről, hanem más Azure bérlő támadások ellen. További részletekért lásd: [Microsoft Azure hálózati biztonság](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Egy indítási feladat szelektív letiltása az egyes IP-címek is létrehozhat. További információkért lásd: [blokkolja egy adott IP-cím](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>A cloud service-példány a távoli asztali meg, az üzenet jelenik meg, "a felhasználói fiók lejárt."
"Ez a felhasználói fiók lejárt" hibaüzenet jelenhet meg, ha a lejárati dátum az RDP-beállítások között megadott megkerülése. Módosíthatja a lejárati dátumot a portálról az alábbiak szerint:
1. Jelentkezzen be az Azure felügyeleti konzol (https://manage.windowsazure.com), keresse meg a felhőalapú szolgáltatás, és válassza ki a **konfigurálása** fülre.
2. Válassza ki **távoli**.
3. Módosítsa a "jár le" dátuma, és mentse a konfigurációt.

Most kell tudni az RDP a számítógépen.

## <a name="why-is-loadbalancer-not-balancing-traffic-equally"></a>Miért van terheléselosztó nem terheléselosztási forgalom egyaránt?
Hogyan belső load balancer works kapcsolatos információkért lásd: [Azure terheléselosztó új terjesztési mód](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

A használt algoritmus egy 5 rekordos (forrás IP-címe, a forrásport, cél IP-címe, cél port protokolltípus) kivonatoló forgalom hozzárendelése elérhető kiszolgálón. Csak egy átviteli munkamenet belül tölcsérútvonalak biztosít. A datacenter IP-címet (DIP) példányt az elosztott terhelésű végpont mögött ugyanabban a TCP vagy UDP-munkamenetben csomagokat a rendszer kéri. Amikor az ügyfél bezárja és újra megnyitja a kapcsolatot, vagy új munkamenet indítása a azonos forrás IP-címről, a forrásport módosításai, és egy másik DIP végpont gomba a forgalmat.

## <a name="how-can-i-redirect-the-incoming-traffic-to-my-default-url-of-cloud-service-to-a-custom-url"></a>Hogyan lehet a bejövő forgalom átirányítása a saját alapértelmezett URL-címet a felhőalapú szolgáltatás, egy egyéni URL? 

Az URL-cím újraíró modul az IIS alapértelmezett URL-címet a felhőalapú szolgáltatáshoz érkező forgalom átirányítására használható (például \*. cloudapp.net) néhány egyéni DNS nevét vagy URL. Mivel az URL-újraíró modult a webes szerepkörök engedélyezve alapértelmezés szerint, valamint a szabályok úgy vannak konfigurálva, az alkalmazás Web.config fájlban, mindig lenne a virtuális gép újraindulását/reimages függetlenül érhető el. További információkért lásd:

- [Az URL-cím létrehozása átdolgozás szabályainak újraíró modul](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Mutató alapértelmezett hivatkozás eltávolítása](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-the-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Hogyan lehet I blokk vagy letiltását a felhőalapú szolgáltatás alapértelmezett URL-címét a bejövő forgalom? 

A bejövő forgalom megakadályozhatja az alapértelmezett URL-címe vagy neve a felhőszolgáltatás (például \*. cloudapp.net) úgy, hogy az állomásfejléc egy egyéni DNS-nevével (például a webszolgáltatáshoz. MyCloudService.com) webhely kötésének konfigurációja a felhő szolgáltatásdefiníciós fájlban (*.csdef) az alábbiak szerint: 
 

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
 
A host fejléc kötést csdef fájlon keresztül, mert a szolgáltatás csak akkor érhető el az egyéni név "www.MyCloudService.com", mivel a bejövő kérelem a "*. cloudapp.net" tartomány mindig sikertelen lesz. A vizsgálathoz használt viselkedését, hogy a kívánt említett, ha azonban egy egyéni SLB mintavételt vagy belső terheléselosztót a szolgáltatás blokkolja-e a szolgáltatás URL-címe vagy neve alapértelmezett gátolja. 

## <a name="how-to-make-sure-the-public-facing-ip-address-of-a-cloud-service-aka-vip-never-changes-so-that-it-could-be-customarily-whitelisted-by-few-specific-clients"></a>Győződjön meg arról, hogy a nyilvános felé néző IP-cím (más néven, VIP) felhőalapú szolgáltatások soha nem változik, hogy annak oka az lehet a szokásos szerepel az engedélyezési listán néhány adott ügyfelek hogyan?

Engedélyezett IP-címét a Felhőszolgáltatások javasolt, hogy rendelkezik-e a vele társított egyéb Azure által biztosított virtuális IP-cím lesz felszabadítása az előfizetésből, ha törli a központi telepítés foglalt IP-cím. Vegye figyelembe, hogy a sikeres VIP csereművelet kell egyes fenntartott IP-címek üzemi és átmeneti üzembe helyezési ponti hiányában mely swap művelet sikertelen lesz. Ezek a cikkek foglaljon le egy IP-címet, és rendelje hozzá azt a Cloud serviceshez kövesse:  
 
- [Foglaljon le egy meglévő felhőszolgáltatáshoz IP-címe](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [A foglalt IP-cím egy felhőszolgáltatásra történő társíthatók a szolgáltatás konfigurációs fájl használatával](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file) 

Mindaddig, amíg a szerepkörök egynél több példány van, és a Felhőszolgáltatás közötti társítás RIP nem okozhat leállási. Másik lehetőségként is engedélyezett az az Azure Datacenter IP-címtartományt. Minden Azure IP-címtartományok található [Itt](https://www.microsoft.com/en-us/download/details.aspx?id=41653). 

Ez a fájl azokat az IP-címtartományokat (beleértve Compute, SQL és Storage tartományokat) tartalmazza, amelyeket a Microsoft Azure adatközpontokban használnak. Hetente közzétesznek egy frissített fájlt, amely bemutatja a jelenleg üzembe helyezett tartományokat és minden, az IP tartományokat érintő közelgő változást. A fájlban megjelenő új tartományokat legalább egy hétig nem használják még az adatközpontok. Töltse le az új XML-fájlt minden héten, és végezze el a szükséges változtatásokat oldalán, hogy helyesen azonosítsa az Azure-ban futó szolgáltatásokat. Az Express Route felhasználóinak feltűnhet, hogy régen ez a fájl frissítette az Azure címterületek BGP-közzétételét minden hónap első hetében. 

## <a name="how-can-i-use-azure-resource-manager-vnets-with-cloud-services"></a>Hogyan használható Azure Resource Manager Vnetek a Felhőszolgáltatásokkal? 

Felhőszolgáltatások nem helyezhető egy Azure Resource Manager Vnetek, de egy Azure Resource Manager virtuális hálózatokat és a hagyományos Vneteket keresztül csatlakozhatnak társviszony-létesítés. További információkért lásd: [virtuális hálózati társviszony-létesítés](../virtual-network/virtual-network-peering-overview.md).