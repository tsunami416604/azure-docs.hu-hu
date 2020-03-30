---
title: Azure Service Fabric figyelési partnerek
description: Ismerje meg, hogyan figyelheti az Azure Service Fabric-alkalmazásokat, fürtöket és infrastruktúrát partnerfigyelési megoldásokkal.
author: srrengar
ms.topic: article
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: fd24d65ebdf4e458870819286024d1ea2e13d83e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645718"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Azure Service Fabric figyelési partnerek

Ez a cikk bemutatja, hogyan lehet figyelni a Service Fabric-alkalmazások, fürtök és infrastruktúra egy maroknyi partnermegoldások. Az alábbi partnerekkel együttműködve integrált ajánlatokat hoztunk létre a Service Fabric számára.

## <a name="dynatrace"></a>Dynatrace

A Dynatrace-del való integrációszámos, a szolgáltatásháló-fürtök figyeléséhez biztosít funkciókat. A Dynatrace OneAgent telepítése a VMSS-példányok on a VMSS-példányok teljesítményszámlálók és a Service Fabric központi telepítésének topológiáját az alkalmazás szintjére. A Dynatrace kiváló választás helyszíni felügyelethez is. Tekintse meg a [közleményben](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) felsorolt funkciókat és [utasításokat,](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) amelyek lehetővé teszik a Dynatrace szolgáltatást a fürtön. 

## <a name="datadog"></a>Datadog (Adatkutya)

Datadog van egy kiterjesztése VMSS windowsos és Linux-példányok. A Datadog használatával windowsos eseménynaplókat gyűjthet, és ezáltal gyűjthet Service Fabric platformeseményeket a Windows rendszeren. Nézze meg az utasításokat, hogyan kell elküldeni a diagnosztikai adatokat Datadog [itt](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric).

## <a name="appdynamics"></a>AppDynamics

A Service Fabric-integráció az AppDynamics alkalmazásszintjén van. A környezeti változók frissítésével és az App Dynamics NuGets használatával alkalmazástelemetriai adatokat küldhet az AppDynamics-nek. A .NET Service Fabric-alkalmazások AppDynamics alkalmazásokkal való integrálásáról ezek az [utasítások](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) találhatók.

## <a name="new-relic"></a>New Relic

Az Új relic egy másik alkalmazásteljesítmény-kezelő eszköz, amely jól integrálható a Service Fabric-alkalmazásokkal. Telepítheti az új relic NuGet csomagokat, és hozzáadhat konkrét környezeti változókat a jegyzékfájlokban, hogy az alkalmazás telemetriáját az új relic nek küldhesse. Tekintse meg ezeket [az utasításokat,](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) hogy engedélyezze az új relic telemetriai adatokat a .NET Service Fabric-alkalmazások.

## <a name="elk"></a>Elk 

Az ELK verem nyílt forráskódú technológiák gyűjteménye: Elasticsearch, Logstash és Kibana. Ezekkel a technológiákkal kombinálva gyűjtheti, tárolhatja és elemezheti a Service Fabric figyelési és diagnosztikai adatait. Van egy tutorial, hogyan kell ezt csinálni a Service Fabric natív Java alkalmazások [itt](service-fabric-tutorial-java-elk.md). 

## <a name="humio"></a>Humio között

A Humio egy naplógyűjtési szolgáltatás, amely valós időben naplózhatja az alkalmazásokat és eseményeket a Service Fabric-ből a felhőben vagy a helyszínen. Az élő megfigyelhetőség mellett a Humio a legkorszerűbb elemzési és vizualizációs lehetőségeket is kínál a diagnosztikából származó információk megtekintéséhez és gyűjtéséhez. A Humio költséghatékony árképzési tervekkel rendelkezik, és úgy készült, hogy méretezze, miközben megőrzi a gyors sebességet. Közvetlenül integrálja a Service Fabric platformesemények és az alkalmazás telemetriai. A Humio és a Service Fabric integrációjáról [itt](https://github.com/humio/service-fabric-humio)olvashat bővebben.

## <a name="next-steps"></a>További lépések

* A [figyelés és a diagnosztika áttekintése](service-fabric-diagnostics-overview.md) a Service Fabricben
* Ismerje meg, hogyan diagnosztizálhatja a [gyakori forgatókönyveket](service-fabric-diagnostics-common-scenarios.md) a saját féleszközeivel
