---
title: Application Insights és Log Analytics által használt IP-címek | Microsoft Docs
description: A Application Insights által igényelt kiszolgálói tűzfal-kivételek
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 12/19/2019
ms.openlocfilehash: ef8f5d1e011990e1d98a492ab8bfd871b7f429aa
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275918"
---
# <a name="ip-addresses-used-by-application-insights-and-log-analytics"></a>Application Insights és Log Analytics által használt IP-címek
Az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) szolgáltatás számos IP-címet használ. Előfordulhat, hogy ismernie kell ezeket a címeket, ha a figyelt alkalmazás tűzfal mögött található.

> [!NOTE]
> Habár ezek a címek statikusak, lehetséges, hogy időről időre módosítaniuk kell őket. Az összes Application Insights forgalom a kimenő forgalmat a rendelkezésre állás monitorozása és a beérkező tűzfalszabályok használatát igénylő webhookok kivételével jelenti.
> 
> 

> [!TIP]
> Fizessen elő erre az oldalra RSS-hírcsatornáként https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/azure-monitor/app/ip-addresses.md.atom hozzáadásával kedvenc RSS/ATOM-olvasójának, hogy értesítést kapjon a legújabb változásokról.
> 
> 

## <a name="outgoing-ports"></a>Kimenő portok
Meg kell nyitnia néhány kimenő portot a kiszolgálója tűzfalán, hogy a Application Insights SDK és/vagy Állapotmonitor számára lehetővé váljon az adatküldés a portálra:

| Cél | URL-cím | IP | Portok |
| --- | --- | --- | --- |
| Telemetria |dc.services.visualstudio.com<br/>dc.applicationinsights.microsoft.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221<br/>52.167.221.184<br/>52.169.64.244<br/>40.85.218.175<br/>104.211.92.54<br/>52.175.198.74<br/>51.140.6.23<br/>40.71.12.231<br/>13.69.65.22<br/>13.78.108.165<br/>13.70.72.233<br/>20.44.8.7<br/>13.86.218.248<br/>40.79.138.41<br/>52.231.18.241<br/>13.75.38.7<br/>102.133.155.50<br/>52.162.110.67<br/>191.233.204.248<br/>13.69.66.140<br/>13.77.52.29<br/>51.107.59.180<br/>40.71.12.235<br/>20.44.8.10<br/>40.71.13.169 | 443 |
| Élő metrikastream (USA keleti régiója) |use.rt.prod.applicationinsights.trafficmanager.net |23.96.28.38<br/>13.92.40.198<br/>40.112.49.101<br/>40.117.80.207 |443 |
| Élő metrikastream (USA déli középső régiója) |ussc.rt.prod.applicationinsights.trafficmanager.net |157.55.177.6<br/>104.44.140.84<br/>104.215.81.124<br/>23.100.122.113 |443 |
| Élő metrikastream (Észak-Európa) |eun.rt.prod.applicationinsights.trafficmanager.net |40.115.103.168<br/>40.115.104.31<br/>40.87.140.215<br/>40.87.138.220 |443 |
| Élő metrikastream (Nyugat-Európa) |euw.rt.prod.applicationinsights.trafficmanager.net |13.80.134.255<br/>40.68.61.229<br/>23.101.69.223<br/>52.232.106.242 |443 |
| Élő metrikastream (Kelet-Ázsia) |ase.rt.prod.applicationinsights.trafficmanager.net |23.100.90.7<br/>23.101.13.65<br/>23.101.0.142<br/>23.101.9.4 |443 |
| Élő metrikastream (Délkelet-Ázsia) |asse.rt.prod.applicationinsights.trafficmanager.net |207.46.224.101<br/>207.46.236.191<br/>137.116.151.139<br/>13.76.87.86 |443 |

## <a name="status-monitor"></a>Állapotmonitor
Állapotmonitor konfiguráció – csak a módosítások végrehajtásakor szükséges.

| Cél | URL-cím | IP | Portok |
| --- | --- | --- | --- |
| Konfiguráció |`management.core.windows.net` | |`443` |
| Konfiguráció |`management.azure.com` | |`443` |
| Konfiguráció |`login.windows.net` | |`443` |
| Konfiguráció |`login.microsoftonline.com` | |`443` |
| Konfiguráció |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| Konfiguráció |`auth.gfx.ms` | |`443` |
| Konfiguráció |`login.live.com` | |`443` |
| Telepítés | `globalcdn.nuget.org`, `packages.nuget.org`,`api.nuget.org/v3/index.json` `nuget.org`, `api.nuget.org`, `dc.services.vsallin.net` | |`443` |

## <a name="availability-tests"></a>Rendelkezésre állási tesztek
Azon címek listája, amelyekről a [rendelkezésre állási webes tesztek](../../azure-monitor/app/monitor-web-app-availability.md) futnak. Ha webteszteket szeretne futtatni az alkalmazáson, de a webkiszolgálója meghatározott ügyfelek kiszolgálására korlátozódik, akkor engedélyeznie kell a rendelkezésre állási tesztelési kiszolgálókról érkező bejövő forgalmat.

Ha Azure hálózati biztonsági csoportokat használ, egyszerűen adjon hozzá egy **bejövő portszabály-szabályt** , amely lehetővé teszi a Application Insights rendelkezésre állási tesztek adatforgalmát, ha a **szolgáltatás címkét** a **forrás-és** **ApplicationInsightsAvailability** adja meg a **forrásoldali szolgáltatás címkéje**.

>[!div class="mx-imgBorder"]
>![a beállítások területen válassza a bejövő biztonsági szabályok elemet, majd a lap tetején kattintson a Hozzáadás elemre ](./media/ip-addresses/add-inbound-security-rule.png)

>[!div class="mx-imgBorder"]
>![a bejövő biztonsági szabály hozzáadása lap](./media/ip-addresses/add-inbound-security-rule2.png)

Nyissa meg a 80 (http) és a 443 (https) portot a bejövő forgalomhoz a következő címekről (az IP-címek helye szerint vannak csoportosítva):

```
Australia East
20.40.124.176/28
20.40.124.240/28
20.40.125.80/28

Brazil South
191.233.26.176/28
191.233.26.128/28
191.233.26.64/28

France Central (Formerly France South)
20.40.129.96/28
20.40.129.112/28
20.40.129.128/28
20.40.129.144/28

France Central
20.40.129.32/28
20.40.129.48/28
20.40.129.64/28
20.40.129.80/28

East Asia
52.229.216.48/28
52.229.216.64/28
52.229.216.80/28

North Europe
52.158.28.64/28
52.158.28.80/28
52.158.28.96/28
52.158.28.112/28

Japan East
52.140.232.160/28
52.140.232.176/28
52.140.232.192/28

West Europe
51.144.56.96/28
51.144.56.112/28
51.144.56.128/28
51.144.56.144/28
51.144.56.160/28
51.144.56.176/28

UK South
51.105.9.128/28
51.105.9.144/28
51.105.9.160/28

UK West
20.40.104.96/28
20.40.104.112/28
20.40.104.128/28
20.40.104.144/28

Southeast Asia
52.139.250.96/28
52.139.250.112/28
52.139.250.128/28
52.139.250.144/28

West US
40.91.82.48/28
40.91.82.64/28
40.91.82.80/28
40.91.82.96/28
40.91.82.112/28
40.91.82.128/28

Central US
13.86.97.224/28
13.86.97.240/28
13.86.98.48/28
13.86.98.0/28
13.86.98.16/28
13.86.98.64/28

North Central US
23.100.224.16/28
23.100.224.32/28
23.100.224.48/28
23.100.224.64/28
23.100.224.80/28
23.100.224.96/28
23.100.224.112/28
23.100.225.0/28

South Central US
20.45.5.160/28
20.45.5.176/28
20.45.5.192/28
20.45.5.208/28
20.45.5.224/28
20.45.5.240/28

East US
20.42.35.32/28
20.42.35.64/28
20.42.35.80/28
20.42.35.96/28
20.42.35.112/28
20.42.35.128/28

```  

## <a name="application-insights-api"></a>Application Insights API
| Cél | URI | IP | Portok |
| --- | --- | --- | --- |
| API |api.applicationinsights.io<br/>api1.applicationinsights.io<br/>api2.applicationinsights.io<br/>api3.applicationinsights.io<br/>api4.applicationinsights.io<br/>api5.applicationinsights.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |
| API-dokumentumok |dev.applicationinsights.io<br/>dev.applicationinsights.microsoft.com<br/>dev.aisvc.visualstudio.com<br/>www.applicationinsights.io<br/>www.applicationinsights.microsoft.com<br/>www.aisvc.visualstudio.com |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |
| Azure pipeline-jegyzetek bővítmény |aigs1.aisvc.visualstudio.com |dinamikus|443 |

## <a name="log-analytics-api"></a>Log Analytics API

| Cél | URI | IP | Portok |
| --- | --- | --- | --- |
| API |api.loganalytics.io<br/>*.api.loganalytics.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |
| API-dokumentumok |dev.loganalytics.io<br/>docs.loganalytics.io<br/>www.loganalytics.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |

## <a name="application-insights-analytics"></a>Application Insights Analitika

| Cél | URI | IP | Portok |
| --- | --- | --- | --- |
| Elemzési portál | analytics.applicationinsights.io | dinamikus | 80,443 |
| Tartalomkézbesítési hálózat (CDN) | applicationanalytics.azureedge.net | dinamikus | 80,443 |
| Media CDN | applicationanalyticsmedia.azureedge.net | dinamikus | 80,443 |

Megjegyzés: a *. applicationinsights.io tartomány Application Insights csapat tulajdonában van.

## <a name="log-analytics-portal"></a>Log Analytics portál

| Cél | URI | IP | Portok |
| --- | --- | --- | --- |
| Portál | portal.loganalytics.io | dinamikus | 80,443 |
| Tartalomkézbesítési hálózat (CDN) | applicationanalytics.azureedge.net | dinamikus | 80,443 |

Megjegyzés: a *. loganalytics.io tartomány tulajdonosa a Log Analytics csapata.

## <a name="application-insights-azure-portal-extension"></a>Application Insights Azure Portal bővítmény

| Cél | URI | IP | Portok |
| --- | --- | --- | --- |
| Application Insights bővítmény | stamp2.app.insightsportal.visualstudio.com | dinamikus | 80,443 |
| Application Insights-bővítmény CDN | insightsportal-prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | dinamikus | 80,443 |

## <a name="application-insights-sdks"></a>SDK-k Application Insights

| Cél | URI | IP | Portok |
| --- | --- | --- | --- |
| Application Insights JS SDK CDN | az416426.vo.msecnd.net | dinamikus | 80,443 |
| Application Insights Java SDK | aijavasdk.blob.core.windows.net | dinamikus | 80,443 |

## <a name="alert-webhooks"></a>Riasztási webhookok

| Cél | IP | Portok
| --- | --- | --- |
| Riasztások kezelése | 23.96.11.4 | 443 |

## <a name="profiler"></a>Profilkészítő

| Cél | URI | IP | Portok |
| --- | --- | --- | --- |
| Ügynök | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| Portál | gateway.azureserviceprofiler.net | dinamikus | 443
| Tárterület | *.core.windows.net | dinamikus | 443

## <a name="snapshot-debugger"></a>Pillanatkép-hibakereső

> [!NOTE]
> A Profiler és a Snapshot Debugger ugyanazokat az IP-címeket használják.

| Cél | URI | IP | Portok |
| --- | --- | --- | --- |
| Ügynök | ppe.azureserviceprofiler.net<br/>*.ppe.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| Portál | ppe.gateway.azureserviceprofiler.net | dinamikus | 443
| Tárterület | *.core.windows.net | dinamikus | 443
