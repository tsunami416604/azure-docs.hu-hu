---
title: "Webalkalmazási tűzfal (WAF) App Service Environment-környezet konfigurálása"
description: "Ismerje meg az App Service-környezet elé webalkalmazási tűzfal konfigurálásáról."
services: app-service\web
documentationcenter: 
author: naziml
manager: erikre
editor: jimbe
ms.assetid: a2101291-83ba-4169-98a2-2c0ed9a65e8d
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: naziml
ms.openlocfilehash: 3c218a6fe3857c216bc185c5d3630025f332147b
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2017
---
# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>Webalkalmazási tűzfal (WAF) App Service Environment-környezet konfigurálása
## <a name="overview"></a>Áttekintés
Webalkalmazási tűzfalak, például a [Barracuda WAF az Azure-](https://www.barracuda.com/programs/azure) , amely megtalálható a [Azure piactér](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/) segítségével biztonságos a webalkalmazások, ellenőrizze a bejövő webes forgalomnak SQL alkalommal többhelyes parancsfájlok, kártevő szoftverek feltöltések letiltása & DDoS alkalmazás és más támadásoknak. Azt is ellenőrzi a válaszokat a háttérben futó webes-kiszolgálókról az adatok adatvesztés-megelőzési (DLP). Az elkülönítési és az App Service Environment-környezetek által biztosított további skálázás együtt, a környezetet biztosít az ideális üzleti kritikus webes alkalmazások kell állnia, kártékony kérelmek és nagy mennyiségű forgalom.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>Beállítás
A dokumentum konfiguráljuk az App Service-környezet terhelésű több példánya Barracuda WAF mögött, hogy a WAF csak forgalmát képes elérni az App Service Environment-környezet, és azt nem érhető el a Szegélyhálózaton. Azt is, hogy Azure Traffic Manager elé a Barracuda WAF példányok Azure-adatközpont és régiók terheléselosztása érdekében. Egy magas szintű diagramját, a telepítő az alábbi képen jelenne meg:

![Architektúra][Architecture] 

> [!NOTE]
> Bevezetésével [ILB támogatja az App Service Environment-környezet](app-service-environment-with-internal-load-balancer.md), konfigurálhatja a ASE a Szegélyhálózaton a nem érhető el, és csak a magánhálózaton elérhetővé tenni. 
> 
> 

## <a name="configuring-your-app-service-environment"></a>Az App Service-környezet konfigurálása
Az App Service Environment-környezet konfigurálásához tekintse meg [dokumentációban](app-service-web-how-to-create-an-app-service-environment.md) a témáról. Miután egy App Service-környezet létrehozása, Web Apps, API-alkalmazás, létrehozhat és [Mobile Apps](../../app-service-mobile/app-service-mobile-value-prop.md) ebben a környezetben, amely az összes védi a konfigurálását a következő szakaszban végezzük WAF mögött.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>A Barracuda WAF Felhőszolgáltatás konfigurálása
Barracuda rendelkezik egy [részletes cikk](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) üzembe helyezni a WAF az Azure virtuális gépen. De azt szeretné, hogy redundancia, és a hibaérzékeny pontok kialakulását vezet be, mert szeretné-e legalább két WAF példány virtuális gépek üzembe helyezés az ugyanazon a felhőalapú szolgáltatás, ha az alábbi utasításokat.

### <a name="adding-endpoints-to-cloud-service"></a>A felhőalapú szolgáltatás végpontok hozzáadása
Ha szükség van a 2 vagy több WAF VM példánya a felhőalapú szolgáltatás, a [Azure-portálon](https://portal.azure.com/) HTTP és HTTPS-végpont a következő ábrán látható módon az alkalmazás által használt hozzáadása:

![Konfigurálja a végpontot][ConfigureEndpoint]

Az alkalmazások más végpontok használata, ha mindenképpen adja hozzá ezen a listán. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>A felügyeleti portálon keresztül WAF Barracuda konfigurálása
Barracuda WAF TCP Port 8000 használja a konfiguráció a felügyeleti portálon keresztül. Ha a WAF virtuális gépek több példánya van, akkor ismételje meg a Itt minden egyes Virtuálisgép-példány. 

> [!NOTE]
> Ha elkészült, WAF konfigurációjával, távolítsa el a TCP/8000 végpont a WAF virtuális gépeinek a WAF biztonsága.
> 
> 

Adja hozzá a felügyeleti végpont, a Barracuda WAF konfigurálása a következő ábrán látható módon.

![Felügyeleti végpont hozzáadása][AddManagementEndpoint]

A böngésző segítségével keresse meg a felügyeleti végpont a felhőalapú szolgáltatás. Ha a Felhőszolgáltatás neve test.cloudapp.net, ehhez a végponthoz http://test.cloudapp.net:8000 tallózással elérésére. A bejelentkezési oldal, például az alábbi képen, amely bejelentkezhet a WAF virtuális gép telepítési fázis megadott hitelesítő adatokkal kell megjelennie.

![Management bejelentkezési oldal][ManagementLoginPage]

Miután jelentkezik be, meg kell jelennie egy irányítópultot, ahogy az az alábbi ábrán, amely megadja a WAF védelmi alapvető statisztikája.

![Kezelési irányítópult][ManagementDashboard]

Kattintson a **szolgáltatások** lap lehetővé teszi az általa védett szolgáltatásokhoz WAF konfigurálását. A Barracuda WAF konfigurálásával kapcsolatos további részletekért lásd: [dokumentum](https://techlib.barracuda.com/waf/getstarted1). A következő példában az Azure Web Apps HTTP és HTTPS-forgalmat szolgáltató nincs beállítva.

![Felügyeleti szolgáltatások hozzáadása][ManagementAddServices]

> [!NOTE]
> Attól függően, hogy az alkalmazások hogyan vannak konfigurálva, és milyen funkciók használnak az App Service-környezet kell továbbítsa a forgalmat a TCP portokat eltérő 80-as és 443-as, például ha egy webalkalmazás IP SSL-beállítása. Az App Service Environment-környezetek használt hálózati portok listáját lásd: [vezérlő bejövő forgalom dokumentáció](app-service-app-service-environment-control-inbound-traffic.md) hálózati portok szakasz.
> 
> 

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Microsoft Azure Traffic Managerben (nem kötelező)
Az alkalmazás érhető el több régióba, akkor kell betölteni egyenleg azok mögötti [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md). Ehhez adja hozzá a végpont a [Azure-portálon](https://portal.azure.com) használatával a felhőalapú szolgáltatás nevét a WAF a Traffic Manager-profil a következő ábrán látható módon. 

![Traffic Manager-végpontot][TrafficManagerEndpoint]

Ha az alkalmazás hitelesítést igényel, ellenőrizze, hogy néhány, nincs szükség a hitelesítés a Traffic Manager Pingelje meg az alkalmazás rendelkezésre állásának az erőforrás. Beállíthatja az URL-címet a **konfigurációs** lapját a [Azure-portálon](https://portal.azure.com) a következő ábrán látható módon:

![A Traffic Manager konfigurálása][ConfigureTrafficManager]

A Traffic Manager ping-üzenetek továbbítása a WAF az alkalmazást, állítsa be a Barracuda WAF a webhely fordítások továbbítsa a forgalmat, hogy az alkalmazást a következő példában látható módon kell:

![Webhely fordítások][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>Az App Service Environment-környezet hálózati biztonsági csoportokkal (NSG) adatforgalom biztonságossá tétele
Kövesse a [vezérlő bejövő forgalom dokumentáció](app-service-app-service-environment-control-inbound-traffic.md) forgalmának korlátozása az App Service-környezet a WAF a csak a VIP-cím a felhőalapú szolgáltatás használatával kapcsolatos részletekért. Íme egy minta Powershell-parancsot a 80-as TCP-port a feladat végrehajtásához.

    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

A SourceAddressPrefix cserélje le a virtuális IP-cím (VIP) a WAF felhőalapú szolgáltatás.

> [!NOTE]
> A felhőszolgáltatás VIP megváltozik, törölje és hozza létre a felhőalapú szolgáltatás. Ügyeljen arra, hogy a hálózati csoport az IP-címének frissítése, a telepítést követően. 
> 
> 

<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png
