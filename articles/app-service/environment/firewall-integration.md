---
title: Az Azure App Service Environment-környezet kimenő forgalom meghatározott sémákra kelljen
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
ms.date: 09/24/2018
ms.author: ccompy
ms.openlocfilehash: ce0123528b3fb2454d8b83d59b5916363ae0e944
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51251576"
---
# <a name="locking-down-an-app-service-environment"></a>App Service-környezet sémákra

Az App Service Environment (ASE) rendelkezik egy több külső függőséggel, azt hozzáférésre van szüksége ahhoz, hogy megfelelően működni. Az ASE az Azure Virtual Network (VNet) ügyfél helyén. Ügyfelek engedélyeznie kell az ASE függőségi forgalmat, amely olyan ügyfelek számára, zárolását, így minden kimenő forgalom a virtuális hálózatból szeretné probléma van.

Nincsenek több bejövő függőséggel, amely az ASE rendelkezik. A bejövő kezelési forgalom nem lehet elküldeni egy tűzfal eszközön keresztül. Ezt a forgalmat a forrás-címek ismertek és teszik közzé a [App Service Environment-környezet kezelési címeit](https://docs.microsoft.com/azure/app-service/environment/management-addresses) dokumentumot. Ezen információk alapján a bejövő forgalmának biztonságossá tétele hálózati biztonsági csoportokra vonatkozó szabályokat hozhat létre.

A kimenő ASE-függőségek szinte teljesen teljes tartományneveket, amely nem rendelkezik statikus címeket mögöttük van definiálva. A statikus címek hiánya, az azt jelenti, hogy a hálózati biztonsági csoportok (NSG) való zárolását, így a kimenő forgalmat az ASE nem használható. A címeket, hogy egy nem szabályok alapján a jelenlegi felbontás beállítása és NSG-k létrehozásához használja, amely elég gyakran módosítása. 

A megoldás a kimenő címek védelme érdekében adott tűzfal eszköz, amely a kimenő forgalmat a tartománynevek alapján szabályozhatja a rejlik. Az Azure tűzfal korlátozhatja a kimenő HTTP és HTTPS-forgalom alapján a célkiszolgáló teljes Tartománynevét.  

## <a name="configuring-azure-firewall-with-your-ase"></a>Az ASE Azure tűzfal konfigurálása 

Az ASE az Azure-tűzfalon a kimenő forgalom zárolni a lépések a következők:

1. Hozzon létre egy Azure-tűzfal a virtuális hálózathoz, ha az ASE-t, vagy lesz. [Az Azure tűzfal eszközplatformokon](https://docs.microsoft.com/azure/firewall/)
2. Az Azure tűzfal felhasználói felületén válassza ki az App Service teljes Tartományneve Környezetcímke
3. Hozzon létre egy útválasztási táblázatot, a kezelési címek [App Service Environment-környezet kezelési címeit]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) a következő ugrási típusú internettel. Az útvonal táblabejegyzéseket aszimmetrikus útválasztási problémák elkerülése érdekében van szükség.
4. Adja hozzá a következő ugrási típusú internettel IP cím lekérdezésfüggőségek az alábbiak IP-cím függőségeinek útvonalakat.
5. Adjon hozzá egy útvonalat a 0.0.0.0/0 útvonaltáblája a következő Ugrás az Azure-tűzfal folyamatban van.
6. Hozzon létre Szolgáltatásvégpontok Azure SQL és az Azure Storage, az ASE-alhálózattal.
7. Rendelje hozzá az útvonaltáblát az ASE-alhálózattal létrehozott.

## <a name="application-traffic"></a>Alkalmazás-forgalom 

A fenti lépések lehetővé teszi az ASE problémák nélkül. Továbbra is szeretné konfigurálni az alkalmazás igényeinek megfelelően az alábbiakkal. Az ASE Azure tűzfal konfigurált alkalmazások két problémák vannak.  

- Alkalmazások függőségének teljes tartománynevek az Azure-tűzfal és az útvonaltábla hozzá kell adni
- Útvonalak létre kell hozni a forgalmat az aszimmetrikus útválasztás problémák elkerülése érdekében származnak-címek

Ha az alkalmazások függőségei, azokat hozzá kell adni az Azure-tűzfalhoz. Lehetővé teszi a HTTP/HTTPS-forgalmat, és a hálózati szabályok minden más alkalmazás szabályok létrehozásához. 

Ha ismeri a címtartományt, amely az alkalmazás kérelem forgalmának származnak, adhat hozzá, hogy az útvonaltáblához rendelt az ASE-alhálózattal. Ha a címtartomány nagy vagy nincs megadva, majd használhatja például az Application Gateway egy hálózati berendezések, hogy az útvonaltábla hozzáadása egy címet. Az Application Gateway konfigurálása az ILB ASE részleteket [az ILB ASE integrálása egy Application Gateway](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)


## <a name="dependencies"></a>Függőségek

Az Azure App Service több külső függőséggel rendelkezik. Akkor is kell kategóriák szerinti bontásban több fő területtel:

- A szolgáltatásvégpont kompatibilis szolgáltatásokat kell beállítani a Szolgáltatásvégpontok ha zárolását, így a kimenő hálózati adatforgalmat szeretne.
- IP-cím végpontok nem foglalkozik a tartomány nevét. Ez lehet egy probléma, tűzfal-eszközökhöz, amelyek hatással vannak az összes HTTPS-forgalmat tartománynevek. Az IP-cím végpontokat kell adni az útvonaltábla, amely az ASE-alhálózatra van beállítva.
- A tűzfal eszköz FQDN HTTP/HTTPS-végpontokat is kell elhelyezni.
- Helyettesítő karaktert tartalmazó HTTP/HTTPS-végpontok függőségekkel rendelkező az ASE-t egy minősítők száma alapján változhat. 
- Linux függőségek észrevétel csak olyan, az ASE-be helyez üzembe a Linux-alkalmazások. Ha Linux-alkalmazások, az ASE nem telepíti, majd ezek a címek nem kell hozzáadni a tűzfalhoz. 


#### <a name="service-endpoint-capable-dependencies"></a>Képes a szolgáltatásvégpont-függőségek 

| Végpont |
|----------|
| Azure SQL |
| Azure Storage |
| Az Azure KeyVault |


#### <a name="ip-address-dependencies"></a>IP-cím függőségek 

| Végpont |
|----------|
| 40.77.24.27:443 |
| 13.82.184.151:443 |
| 13.68.109.212:443 |
| 13.90.249.229:443 |
| 13.91.102.27:443 |
| 104.45.230.69:443 |
| 168.62.226.198:12000 |


#### <a name="fqdn-httphttps-dependencies"></a>Teljes tartománynév HTTP/HTTPS-függőségek 

| Végpont |
|----------|
|graph.windows.net:443 |
|login.live.com:443 |
|login.Windows.com:443 |
|login.Windows.NET:443 |
|login.microsoftonline.com:443 |
|Client.wns.Windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|OCSP.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|CRL.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.Thawte.com:443 |
|crl3.digicert.com:80 |
|OCSP.digicert.com:80 |
|csc3 – 2009-2.crl.verisign.com:80 |
|CRL.VeriSign.com:80 |
|OCSP.VeriSign.com:80 |
|azperfcounters1.BLOB.Core.Windows .net: 443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|Global.Metrics.nsatc.NET:80   |
|az-prod.metrics.nsatc.net:443 |
|antares.Metrics.nsatc.NET:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|maupdateaccount.BLOB.Core.Windows.NET:443 |
|clientconfig.Passport.NET:443 |
|Packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|Management.Core.Windows.NET:443 |
|Management.Core.Windows.NET:80 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge .net: 443 |
|ellenőrzés-v2.sls.microsoft.com:443 |
|flighting.CP.WD.microsoft.com:443 |
|DMD.metaservices.microsoft.com:80 |
|Admin.Core.Windows.NET:443 |
|azureprofileruploads.BLOB.Core.Windows.NET:443 |
|azureprofileruploads2.BLOB.Core.Windows .net: 443 |
|azureprofileruploads3.BLOB.Core.Windows .net: 443 |
|azureprofileruploads4.BLOB.Core.Windows .net: 443 |
|azureprofileruploads5.BLOB.Core.Windows .net: 443 |

#### <a name="wildcard-httphttps-dependencies"></a>Helyettesítő karaktert tartalmazó HTTP/HTTPS-függőségek 

| Végpont |
|----------|
|GR – éles -\*. cloudapp.net:443 |
| \*. management.azure.com:443 |
| \*. update.microsoft.com:443 |
| \*. windowsupdate.microsoft.com:443 |
|grmdsprod\*mini\*. servicebus.windows.net:443 |
|grmdsprod\*lini\*. servicebus.windows.net:443 |
|grsecprod\*mini\*. servicebus.windows.net:443 |
|grsecprod\*lini\*. servicebus.windows.net:443 |
|graudprod\*mini\*. servicebus.windows.net:443 |
|graudprod\*lini\*. servicebus.windows.net:443 |

#### <a name="linux-dependencies"></a>Linux-függőségek 

| Végpont |
|----------|
|wawsinfraprodbay063.BLOB.Core.Windows .net: 443 |
|beállításjegyzék-1.docker.io:443 |
|auth.docker.IO:443 |
|Production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|US.Archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|Packages.treasuredata.com:80|
|Security.ubuntu.com:80 |

