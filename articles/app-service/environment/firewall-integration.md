---
title: App Service Environment-környezet kimenő forgalom – Azure sémákra
description: Ismerteti, hogyan integrálható az Azure-tűzfal kimenő forgalom védelmére
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 6ae7037ad4cd532b6661a56e6e37a88df3eb54a2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121706"
---
# <a name="locking-down-an-app-service-environment"></a>App Service-környezet sémákra

Az App Service Environment (ASE) rendelkezik egy több külső függőséggel, azt hozzáférésre van szüksége ahhoz, hogy megfelelően működni. Az ASE az Azure Virtual Network (VNet) ügyfél helyén. Ügyfelek engedélyeznie kell az ASE függőségi forgalmat, amely olyan ügyfelek számára, zárolását, így minden kimenő forgalom a virtuális hálózatból szeretné probléma van.

Nincsenek több bejövő függőséggel, amely az ASE rendelkezik. A bejövő kezelési forgalom nem lehet elküldeni egy tűzfal eszközön keresztül. Ezt a forgalmat a forrás-címek ismertek és teszik közzé a [App Service Environment-környezet kezelési címeit](https://docs.microsoft.com/azure/app-service/environment/management-addresses) dokumentumot. Ezen információk alapján a bejövő forgalmának biztonságossá tétele hálózati biztonsági csoportokra vonatkozó szabályokat hozhat létre.

A kimenő ASE-függőségek szinte teljesen teljes tartományneveket, amely nem rendelkezik statikus címeket mögöttük van definiálva. A statikus címek hiánya, az azt jelenti, hogy a hálózati biztonsági csoportok (NSG) való zárolását, így a kimenő forgalmat az ASE nem használható. A címeket, hogy egy nem szabályok alapján a jelenlegi felbontás beállítása és NSG-k létrehozásához használja, amely elég gyakran módosítása. 

A megoldás a kimenő címek védelme érdekében adott tűzfal eszköz, amely a kimenő forgalmat a tartománynevek alapján szabályozhatja a rejlik. Az Azure tűzfal korlátozhatja a kimenő HTTP és HTTPS-forgalom alapján a célkiszolgáló teljes Tartománynevét.  

## <a name="configuring-azure-firewall-with-your-ase"></a>Az ASE Azure tűzfal konfigurálása 

A meglévő ASE az Azure-tűzfalon a kimenő forgalom zárolni a lépések a következők:

1. Engedélyezze a szolgáltatásvégpontokat SQL, a Storage és az Event Hub, az ASE-alhálózattal. Ehhez nyissa meg a hálózati portálon történő > alhálózatok és a select hátralékának, Microsoft.SQL és Microsoft.Storage a szolgáltatás-végpontok legördülő listából. Ha engedélyezve van az Azure SQL-szolgáltatásvégpontokat, minden olyan Azure SQL-függőségek, amelyek az alkalmazások, valamint a szolgáltatásvégpontokkal kell konfigurálni. 

   ![Válassza ki a Szolgáltatásvégpontok][2]
  
1. Hozzon létre egy alhálózatot a virtuális hálózat, ahol az ASE létezik AzureFirewallSubnet nevű. Kövesse az utasításokat a [Azure tűzfal dokumentáció](https://docs.microsoft.com/azure/firewall/) létrehozása az Azure-tűzfal.
1. Az Azure-tűzfal felhasználói felületen > szabályok > alkalmazás szabálygyűjtemény, Add-alkalmazás szabálygyűjtemény válassza. Adjon meg egy nevet, prioritás, és állítsa be a engedélyezése. A teljes Tartománynevet a címkék területen adjon meg egy nevet, a forrás-címek beállítása *, és válassza ki az App Service teljes Tartományneve Környezetcímke és a Windows Update. 
   
   ![Alkalmazás-szabály hozzáadása][1]
   
1. Az Azure-tűzfal felhasználói felületen > szabályok > Hozzáadás hálózati szabálygyűjtemény válassza ki a hálózati szabályok gyűjteményéhez. Adjon meg egy nevet, prioritás, és állítsa be a engedélyezése. Válassza ki a szabályok területen adjon meg egy nevet **bármely**állítsa be * forrás és cél-címekre, és a portok beállítása 123. Ez a szabály lehetővé teszi, hogy a rendszer használatával NTP óra szinkronizálás végrehajtásához. Hozzon létre egy másik szabály osztályozási segítségével portra 12000 ugyanúgy rendszer problémákat.

   ![NTP hálózati szabály hozzáadása][3]

1. Hozzon létre egy útválasztási táblázatot, a kezelési címek [App Service Environment-környezet kezelési címeit]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) a következő ugrási típusú internettel. Az útvonal táblabejegyzéseket aszimmetrikus útválasztási problémák elkerülése érdekében van szükség. Adja hozzá a következő ugrási típusú internettel IP cím lekérdezésfüggőségek az alábbiak IP-cím függőségeinek útvonalakat. Virtuális berendezés útvonal hozzáadása a 0.0.0.0/0 útválasztási táblázatot, a következő Ugrás az Azure-tűzfal magánhálózati IP-cím alatt. 

   ![Útválasztási táblázat létrehozása][4]
   
1. Rendelje hozzá az útvonaltáblát az ASE-alhálózattal létrehozott.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Üzembe helyezi az ASE tűzfal mögött

Az ASE tűzfal mögötti telepítése lépései is ugyanaz, mint a meglévő ASE konfigurálása az Azure-tűzfalak, kivéve a kell létrehozni az ASE-alhálózattal, és kövesse az előző lépésekben. Az ASE létrehozását egy már meglévő alhálózatban Resource Manager-sablont használja, lásd a dokumentum kell [az ASE létrehozása a Resource Manager-sablonnal](https://docs.microsoft.com/azure/app-service/environment/create-from-template).

## <a name="application-traffic"></a>Alkalmazás-forgalom 

A fenti lépések lehetővé teszi az ASE problémák nélkül. Továbbra is szeretné konfigurálni az alkalmazás igényeinek megfelelően az alábbiakkal. Az ASE Azure tűzfal konfigurált alkalmazások két problémák vannak.  

- Az Azure-tűzfal és az útvonaltábla alkalmazásfüggőségek hozzá kell adni. 
- Útvonalak létre kell hozni az alkalmazás forgalmának aszimmetrikus útválasztási problémák elkerülése érdekében

Ha az alkalmazások függőségei, azokat hozzá kell adni az Azure-tűzfalhoz. Lehetővé teszi a HTTP/HTTPS-forgalmat, és a hálózati szabályok minden más alkalmazás szabályok létrehozásához. 

Ha ismeri a címtartományt, amely az alkalmazás kérelem forgalmának származnak, adhat hozzá, hogy az útvonaltáblához rendelt az ASE-alhálózattal. Ha a címtartomány nagy vagy nincs megadva, majd használhatja például az Application Gateway egy hálózati berendezések, hogy az útvonaltábla hozzáadása egy címet. Az Application Gateway konfigurálása az ILB ASE részleteket [az ILB ASE integrálása egy Application Gateway](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)

![Az ASE az Azure-tűzfal csatlakozási folyamat][5]

Ez az Application Gateway használata csak egy példa bemutatja, hogyan konfigurálható a rendszer. Ha ezt az elérési utat adott követi, majd kell útvonal hozzáadása az ASE alhálózat útvonaltáblájához, így a válasz az Application Gateway küldött forgalmat szeretné ott közvetlenül. 

## <a name="logging"></a>Naplózás 

Azure tűzfal naplókat elküldheti az Azure Storage, az Eseményközpontok felé, vagy az Azure Monitor naplózza. Integrálhatja az alkalmazást bármely támogatott cél, nyissa meg a tűzfal az Azure portal > diagnosztikai naplók és a kívánt cél-naplók engedélyezésére. Ha integrálja az Azure Monitor naplóira, majd megtekintheti az Azure-tűzfal küldött összes forgalom naplózása. A forgalom megtagadásához megtekintéséhez nyissa meg a Log Analytics-munkaterület portál > naplókat, és adja meg a lekérdezésben 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
Az Azure-tűzfal integrálása az Azure Monitor naplóira nagyon hasznos beolvasásakor először egy alkalmazás használata, ha Ön nem ismeri az összes alkalmazás függőségeit. További információ az Azure Monitor naplóinak [naplóadatok elemzése az Azure monitorban](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
 
## <a name="dependencies"></a>Függőségek

Az alábbi adatokat csak akkor szükséges, ha a nem Azure-tűzfalat készülékként egy tűzfalat konfigurálni szeretné. 

- A Szolgáltatásvégpontok szolgáltatásvégpont kompatibilis szolgáltatások kell konfigurálni.
- IP-cím függőségei vannak nem HTTP/Https-forgalom (TCP és UDP-forgalom)
- A tűzfal eszköz FQDN HTTP/HTTPS-végpontokat is kell elhelyezni.
- Helyettesítő karaktert tartalmazó HTTP/HTTPS-végpontok függőségekkel rendelkező az ASE-t egy minősítők száma alapján változhat. 
- Linux függőségek észrevétel csak olyan, az ASE-be helyez üzembe a Linux-alkalmazások. Ha Linux-alkalmazások, az ASE nem telepíti, majd ezek a címek nem kell hozzáadni a tűzfalhoz. 

#### <a name="service-endpoint-capable-dependencies"></a>Képes a szolgáltatásvégpont-függőségek 

| Végpont |
|----------|
| Azure SQL |
| Azure Storage |
| Azure-eseményközpont |

#### <a name="ip-address-dependencies"></a>IP-cím függőségek

| Végpont | Részletek |
|----------| ----- |
| \*:123 | NTP az óra ellenőrzése. Forgalom be van jelölve, a porton 123 több végpontot |
| \*:12000 | Ezt a portot használja a rendszer figyeli. Ha ezután bizonyos problémák osztályozási nehezebben lesz, de az ASE-t továbbra is megfelelően működjenek blokkolva van |
| 40.77.24.27:80 | A figyelő és riasztás az ASE problémák szükséges |
| 40.77.24.27:443 | A figyelő és riasztás az ASE problémák szükséges |
| 13.90.249.229:80 | A figyelő és riasztás az ASE problémák szükséges |
| 13.90.249.229:443 | A figyelő és riasztás az ASE problémák szükséges |
| 104.45.230.69:80 | A figyelő és riasztás az ASE problémák szükséges |
| 104.45.230.69:443 | A figyelő és riasztás az ASE problémák szükséges |
| 13.82.184.151:80 | A figyelő és riasztás az ASE problémák szükséges |
| 13.82.184.151:443 | A figyelő és riasztás az ASE problémák szükséges |

Az az Azure-tűzfalak automatikusan mindaz az alábbi konfigurálva a teljes tartománynév címkékkel. 

#### <a name="fqdn-httphttps-dependencies"></a>Teljes tartománynév HTTP/HTTPS-függőségek 

| Végpont |
|----------|
|graph.windows.net:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|cacerts.digicert.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80 |
|Global.Metrics.nsatc.NET:443 |
|az-prod.metrics.nsatc.net:443 |
|antares.Metrics.nsatc.NET:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|management.azure.com:443 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Helyettesítő karaktert tartalmazó HTTP/HTTPS-függőségek 

| Végpont |
|----------|
|GR – éles -\*. cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*. update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |

#### <a name="linux-dependencies"></a>Linux-függőségek 

| Végpont |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|beállításjegyzék-1.docker.io:443 |
|auth.docker.IO:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
