---
title: Kimenő forgalom lezárása
description: Ismerje meg, hogyan integrálható az Azure Firewall szolgáltatással az App Service-környezetből származó kimenő forgalom biztonságossá tétele érdekében.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 01/24/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 84fcb9076bbc1e75d46d9a6682c96035576ae09e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475442"
---
# <a name="locking-down-an-app-service-environment"></a>App Service-környezet zárolása

Az App Service-környezet (ASE) számos külső függőséget, hogy a megfelelő működéshez hozzáférést igényel. Az ASE az ügyfél Azure virtuális hálózat (VNet) él. Az ügyfeleknek engedélyezniük kell az ASE függőségi forgalmat, ami problémát jelent az ügyfelek számára, amelyek szeretnék lezárni az összes kimenő a virtuális hálózatról.

Az ASE kezelésére használt bejövő végpontok száma. A bejövő felügyeleti forgalom nem küldhető tűzfaleszközön keresztül. A forgalom forráscímei ismertek, és közzé vannak téve az [App Service-környezet felügyeleti címeket](https://docs.microsoft.com/azure/app-service/environment/management-addresses) tartalmazó dokumentumban. Van is egy Service Tag nevű AppServiceManagement, amely használható a hálózati biztonsági csoportok (NSG-k) a bejövő forgalom biztonságossá tétele.

Az ASE kimenő függőségek szinte teljesen definiálva vannak a teljes tartománynevekkel, amelyek mögött nincsenek statikus címek. A statikus címek hiánya azt jelenti, hogy a hálózati biztonsági csoportok nem használhatók az ASE kimenő forgalmának zárolására. A címek elég gyakran változnak ahhoz, hogy ne lehessen szabályokat beállítani az aktuális felbontás alapján, és ezt fel használni az NSG-k létrehozásához. 

A kimenő címek biztonságossá tétele olyan tűzfaleszköz használatának megoldása, amely a tartománynevek en alapuló kimenő forgalmat szabályozhatja. Az Azure Firewall korlátozhatja a kimenő HTTP- és HTTPS-forgalmat a cél teljes tartományszáma alapján.  

## <a name="system-architecture"></a>Rendszer-architektúra

Az ASE üzembe helyezése tűzfaleszközön áthaladó kimenő forgalommal az ASE alhálózaton lévő útvonalak módosításához szükséges. Az útvonalak IP szinten működnek. Ha nem vigyáz az útvonalak definiálására, kényszerítheti a TCP-válaszforgalmat egy másik címforrásra. Ha a válaszcíme eltér a címforgalomtól, a problémát aszimmetrikus útválasztásnak nevezzük, és megszakítja a TCP-t.

Az ASE-be érkező bejövő forgalomnak meg kell határoznia az útvonalakat, ugyanúgy válaszolhat, mint a hogy a forgalom beérkezett. Az útvonalakat meg kell határozni a bejövő felügyeleti kérelmekhez és a bejövő alkalmazáskérelmekhez.

Az ASE-be irányuló és onnan érkező forgalomnak be kell tartania az alábbi egyezményeket:

* Az Azure SQL, Storage és Event Hub forradtforgalmat tűzfaleszköz használata nem támogatja. Ezt a forgalmat közvetlenül ezeknek a szolgáltatásoknak kell elküldeni. Ennek módja a három szolgáltatás szolgáltatásvégpontjainak konfigurálása. 
* Útvonaltábla-szabályokat kell definiálni, amelyek a bejövő felügyeleti forgalmat visszaküldik onnan, ahonnan érkezett.
* Útvonaltábla-szabályokat kell definiálni, amelyek a bejövő alkalmazásforgalmat visszaküldik onnan, ahonnan érkezett. 
* Az ASE-ből kilépő összes többi forgalom elküldhető a tűzfaleszközre egy útvonaltábla-szabállyal.

![ASE az Azure Tűzfal kapcsolati folyamatával][5]

## <a name="locking-down-inbound-management-traffic"></a>Bejövő felügyeleti forgalom zárolása

Ha az ASE alhálózatmég nem rendelkezik nsg-hez rendelt, hozzon létre egyet. Az NSG-n belül állítsa be az első szabályt, amely engedélyezi az AppServiceManagement nevű szolgáltatáscímkéből származó forgalmat a 454-es, 455-ös portokon. Az AppServiceManagement címkéből való hozzáférést engedélyező szabály az egyetlen dolog, amely az ASE kezeléséhez szükséges a nyilvános IP-címekhez. A szolgáltatási címke mögött található címek csak az Azure App Service felügyeletére szolgálnak. Az ezeken a kapcsolatokon keresztül áramló felügyeleti forgalom titkosított és hitelesítési tanúsítványokkal védett. A csatorna tipikus forgalma olyan dolgokat tartalmaz, mint például az ügyfél által kezdeményezett parancsok és az állapotminta. 

Ases, amelyek a portálon keresztül egy új alhálózat készül egy NSG, amely tartalmazza az engedélyezési szabály az AppServiceManagement címke.  

Az ASE-nek engedélyeznie kell az 16001-es port terheléselosztó címkéjéből érkező bejövő kérelmeket is. Az 16001-es port terheléselosztójának kérései életben tartják a terheléselosztó és az ASE előtér-végeközötti ellenőrzéseket. Ha az 16001-es port le van tiltva, az ASE nem megfelelő állapotba kerül.

## <a name="configuring-azure-firewall-with-your-ase"></a>Az Azure tűzfal konfigurálása az ASE-vel 

A meglévő ASE-ből az Azure Firewall tűzfallal való zárolásának lépései a következők:

1. Engedélyezze a szolgáltatásvégpontokat az SQL, storage és Event Hub az ASE alhálózaton. A szolgáltatásvégpontok engedélyezéséhez lépjen be a hálózati portálra, > alhálózatokat, és válassza a Microsoft.EventHub, a Microsoft.SQL és a Microsoft.Storage lehetőséget a Szolgáltatás végpontjai legördülő menüből. Ha az Azure SQL szolgáltatásvégpontjai engedélyezve vannak, az alkalmazások által konfigurált Azure SQL-függőségeket is konfigurálni kell a szolgáltatásvégpontokkal. 

   ![szolgáltatásvégpontok kiválasztása][2]
  
1. Hozzon létre egy AzureFirewallSubnet nevű alhálózatot a virtuális hálózatban, ahol az ASE létezik. Kövesse az Azure [Firewall dokumentációjában](https://docs.microsoft.com/azure/firewall/) található utasításokat az Azure Firewall létrehozásához.

1. Az Azure Firewall felhasználói felületén > szabályok > alkalmazásszabály gyűjteményében válassza az Alkalmazásszabály-gyűjtemény hozzáadása lehetőséget. Adjon meg egy nevet, prioritást és állítsa be az Engedélyezés lehetőséget. A Teljes tartománynév-címkék szakaszban adjon meg egy nevet, állítsa a forráscímeket *-ra, és válassza ki az App Service-környezet teljes tartománynév-címkét és a Windows Update szolgáltatást. 
   
   ![Alkalmazásszabály hozzáadása][1]
   
1. Az Azure Firewall felhasználói felületén > szabályok > hálózati szabály gyűjtemény, válassza a Hálózati szabály gyűjtemény hozzáadása. Adjon meg egy nevet, prioritást és állítsa be az Engedélyezés lehetőséget. Az IP-címek csoport Szabályok szakaszában adjon meg egy nevet, válasszon egy **ptocol-t bármelyik**ből , állítsa a * forrás és cél címek beállítását, és állítsa a portokat 123-ra. Ez a szabály lehetővé teszi, hogy a rendszer az Óraszinkronizálást az NTP használatával hajtsa végre. Hozzon létre egy másik szabályt ugyanúgy az 12000-es portra, hogy segítsen a rendszerproblémák osztályozásában. 

   ![NTP hálózati szabály hozzáadása][3]
   
1. Az Azure Firewall felhasználói felületén > szabályok > hálózati szabály gyűjtemény, válassza a Hálózati szabály gyűjtemény hozzáadása. Adjon meg egy nevet, prioritást és állítsa be az Engedélyezés lehetőséget. A Szabályok szakaszban a Szolgáltatás címkék, adjon meg egy nevet, válassza ki a protokoll **bármely**, állítsa * a Forrás címek, válassza ki az AzureMonitor szolgáltatáscímkéjét, és állítsa be a portok 80, 443. Ez a szabály lehetővé teszi, hogy a rendszer az Azure Monitor állapot- és metrikaadatokkal való ellátásához.

   ![NTP-szolgáltatáscímke hálózati szabályának hozzáadása][6]
   
1. Hozzon létre egy útvonaltáblát az [App Service-környezet felügyeleti címeiből]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) származó felügyeleti címekkel az internet következő ugrásával. Az útvonaltábla-bejegyzések szükségesek az aszimmetrikus útválasztási problémák elkerüléséhez. Adja hozzá az IP-címfüggőségek útvonalait az ip-címfüggőségekben az internet következő ugrásával. Adjon hozzá egy virtuális berendezés-útvonalat a 0.0.0.0/0-s útvonaltáblához, és a következő ugrás az Azure Tűzfal privát IP-címe. 

   ![Útvonaltábla létrehozása][4]
   
1. Rendelje hozzá a létrehozott útvonaltáblát az ASE alhálózatához.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Az ASE telepítése tűzfal mögött

Az ASE tűzfal mögötti üzembe helyezésének lépései megegyeznek a meglévő ASE konfigurálásával egy Azure tűzfallal, kivéve, hogy létre kell hoznia az ASE alhálózatot, és kövesse az előző lépéseket. Az ASE egy már meglévő alhálózatban történő létrehozásához egy Erőforrás-kezelő sablont kell használnia az [ASE létrehozása erőforrás-kezelő sablonnal](https://docs.microsoft.com/azure/app-service/environment/create-from-template)című dokumentumban leírtak szerint.

## <a name="application-traffic"></a>Alkalmazás forgalma 

A fenti lépések lehetővé teszik az ASE működését gond nélkül. Továbbra is konfigurálnia kell a dolgokat az alkalmazás igényeinek megfelelően. Az Azure Tűzfallal konfigurált ASE-ben lévő alkalmazások két probléma merülnek fel.  

- Alkalmazás-függőségek hozzá kell adni az Azure tűzfal vagy az útvonaltábla. 
- Az aszimmetrikus útválasztási problémák elkerülése érdekében útvonalakat kell létrehozni az alkalmazásforgalomhoz.

Ha az alkalmazások függőségekkel rendelkeznek, hozzá kell adni őket az Azure tűzfalhoz. Alkalmazásszabályok létrehozása a HTTP/HTTPS-forgalom és a hálózati szabályok engedélyezéséhez minden máshoz. 

Ha ismeri a címtartományt, amelyből az alkalmazáskérelem-forgalom származik, hozzáadhatja azt az ASE-alhálózathoz rendelt útvonaltáblához. Ha a címtartomány nagy vagy meghatározatlan, akkor egy hálózati készülék, például az Application Gateway segítségével adhat meg egy címet, amelyet hozzáadhat az útvonaltáblához. Az Alkalmazásátjáró ILB ASE-vel történő konfigurálásáról az [ILB ASE integrálása alkalmazásátjáróval](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway) című témakört

Az Application Gateway ezen használata csak egy példa a rendszer konfigurálására. Ha ezt az elérési utat követte, akkor hozzá kell adnia egy útvonalat az ASE alhálózati útvonaltáblához, hogy az Application Gateway-nek küldött válaszforgalom közvetlenül menjen oda. 

## <a name="logging"></a>Naplózás 

Az Azure Firewall naplókat küldhet az Azure Storage, az Event Hub vagy az Azure Monitor naplóiba. Az alkalmazás bármely támogatott célhoz való integrálásához nyissa meg az Azure Firewall portált > diagnosztikai naplókat, és engedélyezze a naplókat a kívánt célhoz. Ha integrálja az Azure Monitor naplók, majd láthatja az Azure Firewall-nek küldött forgalom naplózását. A visszautasított forgalom megtekintéséhez nyissa meg a Log Analytics munkaterületi portálját > Naplók portálon, és írjon be egy lekérdezést, például 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
Az Azure-tűzfal integrálása az Azure Monitor-naplók hasznos, ha először egy alkalmazás működik, ha nem ismeri az összes alkalmazás-függőségek. Az Azure Monitor naplóiról az [Azure Monitor adatainak elemzése az Azure Monitorban](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)című területen olvashat bővebben.
 
## <a name="dependencies"></a>Függőségek

A következő információk csak akkor szükségesek, ha az Azure Firewall-kiszolgálótól eltérő tűzfalberendezést kíván konfigurálni. 

- A szolgáltatásvégpontra képes szolgáltatásokat szolgáltatásvégpontokkal kell konfigurálni.
- Az IP-címfüggőségek nem HTTP/S-forgalomra (TCP- és UDP-forgalomra) szolgálnak
- Az FQDN HTTP/HTTPS végpontok a tűzfaleszközbe helyezhetők.
- A helyettesítő HTTP/HTTPS-végpontok olyan függőségek, amelyek az ASE-től függően változhatnak a minősítők száma alapján. 
- A Linux-függőségek csak akkor jelentenek problémát, ha Linux-alkalmazásokat telepít az ASE-be. Ha nem telepít Linux-alkalmazásokat az ASE-be, akkor ezeket a címeket nem kell hozzáadni a tűzfalhoz. 

#### <a name="service-endpoint-capable-dependencies"></a>Szolgáltatásvégpont-képes függőségek 

| Végpont |
|----------|
| Azure SQL |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>IP-címfüggőségek

| Végpont | Részletek |
|----------| ----- |
| \*:123 | NTP óra ellenőrzés. A forgalmat a 123-as port több végpontján ellenőrzik |
| \*:12000 | Ez a port néhány rendszerfigyelésre szolgál. Ha blokkolva van, akkor néhány kérdést nehezebb lesz osztályozás, de az ASE továbbra is működik |
| 40.77.24.27:80 | Az ASE-problémák nyomon követéséhez és riasztásához szükséges |
| 40.77.24.27:443 | Az ASE-problémák nyomon követéséhez és riasztásához szükséges |
| 13.90.249.229:80 | Az ASE-problémák nyomon követéséhez és riasztásához szükséges |
| 13.90.249.229:443 | Az ASE-problémák nyomon követéséhez és riasztásához szükséges |
| 104.45.230.69:80 | Az ASE-problémák nyomon követéséhez és riasztásához szükséges |
| 104.45.230.69:443 | Az ASE-problémák nyomon követéséhez és riasztásához szükséges |
| 13.82.184.151:80 | Az ASE-problémák nyomon követéséhez és riasztásához szükséges |
| 13.82.184.151:443 | Az ASE-problémák nyomon követéséhez és riasztásához szükséges |

Az Azure tűzfal automatikusan kap mindent az alábbi konfigurálva a teljes tartománynév-címkéket. 

#### <a name="fqdn-httphttps-dependencies"></a>Teljes qdn HTTP/HTTPS-függőségek 

| Végpont |
|----------|
|graph.microsoft.com:443 |
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
|global.metrics.nsatc.net:443 |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
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
|prod.warmpath.msftcloudes.com:443 |
|prod.warmpath.msftcloudes.com:80 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 | 
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|settings-win.data.microsoft.com:443 |
|maupdateaccount2.blob.core.windows.net:443 |
|maupdateaccount3.blob.core.windows.net:443 |
|dc.services.visualstudio.com:443 |
|gmstorageprodsn1.blob.core.windows.net:443 |
|gmstorageprodsn1.file.core.windows.net:443 |
|gmstorageprodsn1.queue.core.windows.net:443 |
|gmstorageprodsn1.table.core.windows.net:443 |
|rteventservice.trafficmanager.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Helyettesítő http/HTTPS-függőségek 

| Végpont |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*windowsupdate.microsoft.com:443 |
| \*identity.azure.net:443 |

#### <a name="linux-dependencies"></a>Linux-függőségek 

| Végpont |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |
| \*cdn.mscr.io:443 |
|mcr.microsoft.com:443 |
|packages.fluentbit.io:80 |
|packages.fluentbit.io:443 |
|apt-mo.trafficmanager.net:80 |
|apt-mo.trafficmanager.net:443 |
|azure.archive.ubuntu.com:80 |
|azure.archive.ubuntu.com:443 |
|changelogs.ubuntu.com:80 |
|13.74.252.37:11371 |
|13.75.127.55:11371 |
|13.76.190.189:11371 |
|13.80.10.205:11371 |
|13.91.48.226:11371 |
|40.76.35.62:11371 |
|104.215.95.108:11371 |

## <a name="us-gov-dependencies"></a>Us Gov függőségek

Az EGYESÜLT ÁLLAMOKbeli gov-régiók as-ek esetében kövesse az [Azure Firewall konfigurálása a](https://docs.microsoft.com/azure/app-service/environment/firewall-integration#configuring-azure-firewall-with-your-ase) jelen dokumentum ASE szakaszában található utasításokat az Azure tűzfal ase-vel történő konfigurálásához.

Ha az Azure Tűzfaltól eltérő eszközt szeretne használni az USA-ban 

* A szolgáltatásvégpontra képes szolgáltatásokat szolgáltatásvégpontokkal kell konfigurálni.
* Az FQDN HTTP/HTTPS végpontok a tűzfaleszközbe helyezhetők.
* A helyettesítő HTTP/HTTPS-végpontok olyan függőségek, amelyek az ASE-től függően változhatnak a minősítők száma alapján.

A Linux nem érhető el az EGYESÜLT Államok kormányának régióiban, és így nem szerepel választható konfigurációként.

#### <a name="service-endpoint-capable-dependencies"></a>Szolgáltatásvégpont-képes függőségek ####

| Végpont |
|----------|
| Azure SQL |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>IP-címfüggőségek

| Végpont | Részletek |
|----------| ----- |
| \*:123 | NTP óra ellenőrzés. A forgalmat a 123-as port több végpontján ellenőrzik |
| \*:12000 | Ez a port néhány rendszerfigyelésre szolgál. Ha blokkolva van, akkor néhány kérdést nehezebb lesz osztályozás, de az ASE továbbra is működik |
| 40.77.24.27:80 | Az ASE-problémák nyomon követéséhez és riasztásához szükséges |
| 40.77.24.27:443 | Az ASE-problémák nyomon követéséhez és riasztásához szükséges |
| 13.90.249.229:80 | Az ASE-problémák nyomon követéséhez és riasztásához szükséges |
| 13.90.249.229:443 | Az ASE-problémák nyomon követéséhez és riasztásához szükséges |
| 104.45.230.69:80 | Az ASE-problémák nyomon követéséhez és riasztásához szükséges |
| 104.45.230.69:443 | Az ASE-problémák nyomon követéséhez és riasztásához szükséges |
| 13.82.184.151:80 | Az ASE-problémák nyomon követéséhez és riasztásához szükséges |
| 13.82.184.151:443 | Az ASE-problémák nyomon követéséhez és riasztásához szükséges |

#### <a name="dependencies"></a>Függőségek ####

| Végpont |
|----------|
| \*ctldl.windowsupdate.com:80 |
| \*management.usgovcloudapi.net:80 |
| \*update.microsoft.com:80 |
|admin.core.usgovcloudapi.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azprofileruploads1.blob.core.windows.net:80 |
|azprofileruploads10.blob.core.windows.net:80 |
|azprofileruploads2.blob.core.windows.net:80 |
|azprofileruploads3.blob.core.windows.net:80 |
|azprofileruploads4.blob.core.windows.net:80 |
|azprofileruploads5.blob.core.windows.net:80 |
|azprofileruploads6.blob.core.windows.net:80 |
|azprofileruploads7.blob.core.windows.net:80 |
|azprofileruploads8.blob.core.windows.net:80 |
|azprofileruploads9.blob.core.windows.net:80 |
|azureprofilerfrontdoor.cloudapp.net:80 |
|azurewatsonanalysis.usgovcloudapp.net:80 |
|cacerts.digicert.com:80 |
|client.wns.windows.com:80 |
|crl.microsoft.com:80 |
|crl.verisign.com:80 |
|crl3.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|ctldl.windowsupdate.com:80 |
|definitionupdates.microsoft.com:80 |
|download.windowsupdate.com:80 |
|fairfax.warmpath.usgovcloudapi.net:80 |
|flighting.cp.wd.microsoft.com:80 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:80 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:80 |
|global.metrics.nsatc.net:80 |
|go.microsoft.com:80 |
|gr-gcws-prod-bd3.usgovcloudapp.net:80 |
|gr-gcws-prod-bn1.usgovcloudapp.net:80 |
|gr-gcws-prod-dd3.usgovcloudapp.net:80 |
|gr-gcws-prod-dm2.usgovcloudapp.net:80 |
|gr-gcws-prod-phx20.usgovcloudapp.net:80 |
|gr-gcws-prod-sn5.usgovcloudapp.net:80 |
|login.live.com:80 |
|login.microsoftonline.us:80 |
|management.core.usgovcloudapi.net:80 |
|management.usgovcloudapi.net:80 |
|maupdateaccountff.blob.core.usgovcloudapi.net:80 |
|mscrl.microsoft.com
|az ocsp.digicert.0 |
|ocsp.msocsp.co|
|az ocsp.verisign.0 |
|rteventse.trafficmanager.net:80 |
|settings-n.data.microsoft.com:80 |
|shavamafestcdnprod1.azureedge.net:80 |
|shavanifestcdnprod1.azureedge.net:80 |
|v10ortex-win.data.microsoft.com:80 |
|wp.microsoft.com:80 |
|dcpalt.microsoft.com:80 |
|www.microsoft.com:80 |
|www.msftconnecttest.com:80 |
|www.thawte.com:80 |
|\*ctldl.windowsupdate.com:443 |
|\*management.usgovcloudapi.net:443 |
|\*.update.microsoft.com:443 |
|admin.core.usgovcloudapi.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads5.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 |
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|azurewatsonanalysis.usgovcloudapp.net:443 |
|cacerts.digicert.com:443 |
|client.wns.windows.com:443 |
|crl.microsoft.com:443 |
|crl.verisign.com:443 |
|crl3.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:443 |
|ctldl.windowsupdate.com:443 |
|definitionupdates.microsoft.com:443 |
|download.windowsupdate.com:443 |
|fairfax.warmpath.usgovcloudapi.net:443 |
|flighting.cp.wd.microsoft.com:443 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:443 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:443 |
|global.metrics.nsatc.net:443 |
|go.microsoft.com:443 |
|gr-gcws-prod-bd3.usgovcloudapp.net:443 |
|gr-gcws-prod-bn1.usgovcloudapp.net:443 |
|gr-gcws-prod-dd3.usgovcloudapp.net:443 |
|gr-gcws-prod-dm2.usgovcloudapp.net:443 |
|gr-gcws-prod-phx20.usgovcloudapp.net:443 |
|gr-gcws-prod-sn5.usgovcloudapp.net:443 |
|login.live.com:443 |
|login.microsoftonline.us:443 |
|management.core.usgovcloudapi.net:443 |
|management.usgovcloudapi.net:443 |
|maupdateaccountff.blob.core.usgovcloudapi.net:443 |
|mscrl.microsoft.com:443 |
|ocsp.digicert.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.verisign.com:443 |
|rteventservice.trafficmanager.net:443 |
|settings-win.data.microsoft.com:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|v10.vortex-win.data.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|www.microsoft.com:443 |
|www.msftconnecttest.com:443 |
|www.thawte.com:443 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
[6]: ./media/firewall-integration/firewall-ntprule-monitor.png
