---
title: Figyelés a partnerek az Azure Service Fabric |} A Microsoft Docs
description: Útmutató a partneri megoldások monitorozása az Azure Service Fabric figyelése
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: c2f953c98e41291951f07556bd0cd441d2793d1d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59796244"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Az Azure Service Fabric-figyelés partnerek

Ez a cikk azt mutatja be, hogyan egy figyelheti a Service Fabric alkalmazások, a fürtök és a partneri megoldások és infrastruktúra. Ezért az egyes alábbi partnerek létrehozása a Service fabric integrálható ajánlatoktól.

## <a name="dynatrace"></a>Dynatrace-szel

Az integráció a dynatrace-szel kínál számos, a box szolgáltatásait a Service Fabric-fürtök figyelése kívül. A dynatrace-szel OneAgent a VMSS-példányok telepítése lehetővé teszi, teljesítményszámlálók és a egy a Service Fabric üzembe helyezési topológiája az alkalmazás szintre. Dynatrace akkor is remek választás a helyszíni figyelése. Tekintse meg a felsorolt lehetőségek valamelyikét a [közlemény](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) és [utasításokat](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) engedélyezéséhez a dynatrace-szel a fürtön. 

## <a name="datadog"></a>Datadog

Datadoggal kiterjesztése az vmss-hez Windows- és Linux-példányok. Használatával a Datadoggal összegyűjtése a Windows-eseménynaplók, és ezáltal a Windows a Service Fabric platformot eseményeinek gyűjtése. Tekintse meg a diagnosztikai adatokat küldeni a Datadoggal utasításainak [Itt](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric).

## <a name="appdynamics"></a>AppDynamics

A Service Fabric-integráció, az appdynamics segítségével az alkalmazás szintjén van. Környezeti változók frissítése, és alkalmazás Dynamics csomagolás használ, az AppDynamics Alkalmazáshasználati elküldheti. Ezeket [utasításokat](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) a .NET Service Fabric-alkalmazások integrálása az AppDynamics for.

## <a name="new-relic"></a>New Relic

A New Relic, egy másik alkalmazásteljesítmény-felügyeleti eszköz, amely jól integrálható a Service Fabric-alkalmazásokat. Az új Relic NuGet-csomagok telepítéséhez, és meghatározott környezeti változók hozzáadása a jegyzékfájlok, hogy az alkalmazás telemetriát küldjön New relic-bővítménnyel. Tekintse meg ezeket [utasításokat](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) .NET Service Fabric-alkalmazásait a New relic-bővítménnyel telemetria engedélyezése.

## <a name="elk"></a>ELK 

Az ELK stack gyűjteménye, nyílt forráskódú technológiák: Az Elasticsearch, Logstash és Kibana. Ezek együttes használatával gyűjthet, tárolhatja és elemezheti a Service Fabric monitorozási és diagnosztikai adatokat. Rendelkezünk egy oktatóanyag, az ehhez a Service Fabric natív Java-alkalmazások [Itt](service-fabric-tutorial-java-elk.md). 

## <a name="humio"></a>Humio

Humio, amely képes gyűjteni a naplók az alkalmazások és a Service Fabric a felhőben vagy egy helyszíni valós idejű eseményeket a log adatgyűjtési szolgáltatás. Élő observability Humio legmodernebb elemzési és vizualizációs képességei tekinthetők meg és insights összegyűjti a diagnosztika lehetőséget biztosít. Humio költséghatékony díjcsomagok rendelkezik, és be van építve, megőrzése méretezési van könnyítve gyors sebessége. Közvetlenül integrálható a Service Fabric platform eseményeket és alkalmazás telemetriát. Tudjon meg többet a Humio és a Service Fabric integrálása [Itt](https://github.com/humio/service-fabric-humio).

## <a name="next-steps"></a>További lépések

* Get- [monitorozási és diagnosztikai áttekintése](service-fabric-diagnostics-overview.md) a Service Fabricben
* Ismerje meg, hogyan [diagnosztizálhatja a gyakori forgatókönyvek](service-fabric-diagnostics-common-scenarios.md) az első féltől származó eszközökkel
