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
ms.openlocfilehash: 4c0e2d649f71d7797efbfe2c8e93ea0c844152df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>Webalkalmazási tűzfal (WAF) App Service Environment-környezet konfigurálása
## <a name="overview"></a>Áttekintés
Webalkalmazási tűzfalak, például a [Barracuda WAF az Azure-](https://www.barracuda.com/programs/azure) , amely megtalálható a [Azure piactér](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/) segítségével biztonságos a webalkalmazások, ellenőrizze a bejövő webes forgalomnak SQL alkalommal többhelyes parancsfájlok, kártevő szoftverek feltöltések letiltása & DDoS alkalmazás és más támadásoknak. Azt is ellenőrzi a válaszokat a háttérben futó webes-kiszolgálókról az adatok adatvesztés-megelőzési (DLP). Az elkülönítési és az App Service Environment-környezetek által biztosított további skálázás együtt, a környezetet biztosít az ideális üzleti kritikus webes alkalmazások kell állnia, kártékony kérelmek és nagy mennyiségű forgalom.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>Beállítás
A dokumentum azt konfigurálja az App Service-környezet több terhelés mögött, hogy csak a WAF forgalmát képes elérni az App Service Environment-környezet, és nem lesz elérhető a Szegélyhálózaton kiegyensúlyozott Barracuda WAF példányai. Azt is Azure Traffic Manager elé a Barracuda WAF példányok Azure-adatközpont és régiók terheléselosztása érdekében. A telepítés magas szintű diagramját néz alább láthatók.

![Architektúra][Architecture] 

> Megjegyzés: A bevezetése [ILB támogatja az App Service Environment-környezet](app-service-environment-with-internal-load-balancer.md), konfigurálhatja a ASE a Szegélyhálózaton a nem érhető el, és csak a magánhálózaton elérhetővé tenni. 
> 
> 

## <a name="configuring-your-app-service-environment"></a>Az App Service-környezet konfigurálása
Konfigurálhatja az App Service-környezetek hivatkoznak [dokumentációban](app-service-web-how-to-create-an-app-service-environment.md) a témáról. Miután egy App Service-környezet létrehozása, Web Apps, API-alkalmazásokat hozhat létre és [Mobile Apps](../../app-service-mobile/app-service-mobile-value-prop.md) ebben a környezetben, amely az összes védi a konfigurálását a következő szakaszban végezzük WAF mögött.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>A Barracuda WAF Felhőszolgáltatás konfigurálása
Barracuda rendelkezik egy [részletes cikk](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) üzembe helyezni a WAF az Azure virtuális gépen. De azt szeretné, hogy redundancia, és a hibaérzékeny pontok kialakulását vezet be, mert szeretné-e legalább 2 WAF példány virtuális gép üzembe helyezés az ugyanazon a felhőalapú szolgáltatás, ha az alábbi utasításokat.

### <a name="adding-endpoints-to-cloud-service"></a>A felhőalapú szolgáltatás végpontok hozzáadása
Ha szükség van a 2 vagy több WAF Virtuálisgép-példányok a felhőszolgáltatásban a [Azure-portálon](https://portal.azure.com/) HTTP és HTTPS-végpontok az alábbi ábrán látható módon az alkalmazás által használt hozzáadni.

![Konfigurálja a végpontot][ConfigureEndpoint]

Ha az alkalmazások végpontja, ne felejtse el hozzáadni azokat ebben a listában, megfelelő-e. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>A felügyeleti portálon keresztül WAF Barracuda konfigurálása
Barracuda WAF TCP Port 8000 használja a konfiguráció a felügyeleti portálon keresztül. Mivel a WAF virtuális gépek több példánya van szüksége lesz a ismételje meg a Itt minden egyes Virtuálisgép-példány. 

> Megjegyzés: Ha elkészült, WAF konfigurációjával, távolítsa el a TCP/8000 végpont a WAF virtuális gépeinek a WAF biztonsága.
> 
> 

Adja hozzá a felügyeleti végpont, a Barracuda WAF konfigurálása az alábbi ábrán látható módon.

![Felügyeleti végpont hozzáadása][AddManagementEndpoint]

A böngésző segítségével keresse meg a felügyeleti végpont a felhőalapú szolgáltatás. Ha a Felhőszolgáltatás neve test.cloudapp.net, ehhez a végponthoz http://test.cloudapp.net:8000 tallózással elérésére. A bejelentkezési oldal megjelenik, például alatt is bejelentkezési WAF VM telepítő szakaszban megadott hitelesítő adataival.

![Management bejelentkezési oldal][ManagementLoginPage]

Egyszer meg bejelentkezési meg kell jelennie egy irányítópultot megegyezik a lenti képen, hogy a WAF védelmi alapvető statisztikája.

![Kezelési irányítópult][ManagementDashboard]

Kattintson a szolgáltatások lapon így állíthatja be az általa védett szolgáltatásokhoz WAF. A Barracuda WAF konfigurálásával kapcsolatos további részletekért tanulmányozza [dokumentum](https://techlib.barracuda.com/waf/getstarted1). Az Azure Web Apps az alábbi példában a HTTP és HTTPS-forgalmat kiszolgáló van konfigurálva.

![Felügyeleti szolgáltatások hozzáadása][ManagementAddServices]

> Megjegyzés: Attól függően, hogy az alkalmazások hogyan vannak konfigurálva, és milyen funkciók használnak az App Service-környezet, akkor továbbítja a forgalmat a TCP eltérő 80-as és 443-as port például ha egy webalkalmazás IP SSL-beállítása. Az App Service Environment-környezetek használt hálózati portok listája, olvassa el [vezérlő bejövő forgalom dokumentáció](app-service-app-service-environment-control-inbound-traffic.md) hálózati portok szakasz.
> 
> 

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Microsoft Azure Traffic Managerben (nem kötelező)
Az alkalmazás érhető el több régióba, akkor kell betölteni egyenleg azok mögötti [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md). A végpont adhat hozzá ehhez a [a klasszikus Azure portálon](https://manage.azure.com) használatával a felhőalapú szolgáltatás nevét a WAF a Traffic Manager-profilt, az alábbi ábrán látható módon. 

![Traffic Manager-végpontot][TrafficManagerEndpoint]

Ha az alkalmazás hitelesítést igényel, ellenőrizze, hogy néhány, nincs szükség a hitelesítés a Traffic Manager Pingelje meg az alkalmazás rendelkezésre állásának az erőforrás. Az URL-cím konfigurálása szakaszban konfigurálhatók a [a klasszikus Azure portálon](https://manage.azure.com) alább látható módon.

![A Traffic Manager konfigurálása][ConfigureTrafficManager]

A Traffic Manager ping-üzenetek továbbítása a WAF az alkalmazáshoz, hogy végre kell telepítő webhely fordítások a Barracuda WAF továbbítsa a forgalmat az alkalmazáshoz az alábbi példában látható módon.

![Webhely fordítások][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>Az App Service Environment-környezet hálózati biztonsági csoportokkal (NSG) adatforgalom biztonságossá tétele
Kövesse a [vezérlő bejövő forgalom dokumentáció](app-service-app-service-environment-control-inbound-traffic.md) forgalmának korlátozása az App Service-környezet a WAF a csak a VIP-cím a felhőalapú szolgáltatás használatával kapcsolatos részletekért. Íme egy minta Powershell-parancsot a 80-as TCP-port a feladat végrehajtásához.

    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

A SourceAddressPrefix cserélje le a virtuális IP-cím (VIP) a WAF felhőalapú szolgáltatás.

> Megjegyzés: A felhőszolgáltatás VIP változik, törölje és hozza létre a felhőalapú szolgáltatáshoz. Ügyeljen arra, hogy a hálózati csoport az IP-címének frissítése, a telepítést követően. 
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
