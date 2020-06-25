---
title: Alkalmazások figyelése programkód módosítása nélkül – automatikus rendszerállapot-Azure Monitor Application Insights | Microsoft Docs
description: A Azure Monitor Application Insights-kód alapú alkalmazások teljesítményének felügyeletéhez szükséges automatikus kiépítés áttekintése
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 87342dcd316b0364522baa01e632b704665c998e
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85363759"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>Mi az Auto-Instrumentation vagy a kód-Azure Monitor Application Insights?

Az automatikus és a kód nélküli csatolás lehetővé teszi, hogy a kód módosítása nélkül engedélyezze az alkalmazások figyelését Application Insights.  

Application Insights integrálva van különböző erőforrás-szolgáltatókkal, és különböző környezetekben működik. Lényegében mindössze annyit kell tennie, hogy engedélyezi és – bizonyos esetekben – konfigurálja az ügynököt, amely összegyűjti a telemetria. A Application Insights erőforrásban a metrikák, az adatok és a függőségek nem jelennek meg, ami lehetővé teszi a lehetséges problémák forrásának megvásárlását, mielőtt azok bejelentkeznek, és elemezze a kiváltó okot a végpontok közötti tranzakciós nézettel.

## <a name="supported-environments-languages-and-resource-providers"></a>Támogatott környezetek, nyelvek és erőforrás-szolgáltatók

Ahogy egyre több integrációt adunk hozzá, az automatikus rendszerállapot-kialakítási képesség mátrixa összetettvé válik. Az alábbi táblázat azt mutatja be, hogy milyen aktuális állapotban van a különböző erőforrás-szolgáltatók, nyelvek és környezetek támogatása.

|Környezet/erőforrás-szolgáltató | .NET            | .NET Core       | Java            | Node.js         |
|------------------------------|-----------------|-----------------|-----------------|-----------------|
|Azure App Service Windows rendszeren  | GA, OnBD *       | GA, opt-in      | Privát előzetes verzió | Privát előzetes verzió |
|Azure App Service Linuxon    | N/A             | Nem támogatott   | Nyilvános előzetes verzió  | Nyilvános előzetes verzió  |
|Azure App Service AK-on      | N/A             | A tervezésben       | A tervezésben       | A tervezésben       |
|Azure Functions – alapszintű       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       |
|Azure Functions – függőségek| Nem támogatott   | Nem támogatott   | Nyilvános előzetes verzió  | Nem támogatott   |
|Azure Kubernetes Service      | N/A             | A tervezésben       | Ügynökön keresztül   | A tervezésben       |
|Azure-beli virtuális gépek Windows             | Nyilvános előzetes verzió  | Nem támogatott   | Nem támogatott   | Nem támogatott   |
|Helyszíni virtuális gépek Windows       | GA, opt-in      | Nem támogatott   | Ügynökön keresztül   | Nem támogatott   |
|Önálló ügynök – bármely env.   | Nem támogatott   | Nem támogatott   | Nyilvános előzetes verzió  | Nem támogatott   |

* A OnBD alapértelmezés szerint a következő: a Application Insights automatikusan engedélyezve lesz, amint üzembe helyezi az alkalmazást a támogatott környezetekben. 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

Az [alkalmazások figyelése Azure app Service](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps?tabs=net) a .NET-alkalmazáshoz érhető el, és alapértelmezés szerint engedélyezve van, a .net Core egyetlen kattintással engedélyezhető, a Java és a Node.js pedig privát előzetes verzióban.

### <a name="linux"></a>Linux 

A Java-és Node.js-alkalmazásoknak a App Serviceban való figyelése nyilvános előzetes verzióban érhető el, és Azure Portal-ben is engedélyezhető, minden régióban elérhető.

## <a name="azure-functions"></a>Azure Functions

A Azure Functions alapszintű figyelése alapértelmezés szerint engedélyezve van a naplók, a teljesítmény, a hibák és a HTTP-kérések gyűjtésére. A Java-alkalmazások esetében engedélyezheti a szélesebb körű figyelést az elosztott nyomkövetéssel, és lekérheti a végpontok közötti tranzakció részleteit. A Javához készült funkció nyilvános előzetes verzióban érhető el, és [Azure Portalban is engedélyezhető](https://docs.microsoft.com/azure/azure-monitor/app/monitor-functions).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Az Azure Kubernetes szolgáltatás kód nélküli üzembe helyezése jelenleg a [különálló ügynökön](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)keresztül érhető el Java-alkalmazásokhoz. 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Azure Windows-alapú virtuális gépek és virtuálisgép-méretezési csoport

Az [Azure-beli virtuális gépek és a virtuálisgép-méretezési csoport automatikus kiosztása .net-](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps) alkalmazásokhoz érhető el 

## <a name="on-premises-servers"></a>Helyszíni kiszolgálók
Egyszerűen engedélyezheti a helyszíni Windows- [kiszolgálók .NET-alkalmazásokhoz](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) és [Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)-alkalmazásokhoz való figyelését.

## <a name="other-environments"></a>Egyéb környezetek
A sokoldalú Java önálló ügynök bármilyen környezetben működik, nincs szükség a kód megalkotására. Az [útmutatót követve](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) engedélyezze Application Insightst, és olvassa el a Java-ügynök csodálatos képességeit. Az ügynök nyilvános előzetes verzióban érhető el, és minden régióban elérhető. 

## <a name="next-steps"></a>További lépések

* [Application Insights áttekintése](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)
* [Alkalmazás-hozzárendelés](./../../azure-monitor/app/app-map.md)
* [Végpontok közötti teljesítmény figyelése](./../../azure-monitor/learn/tutorial-performance.md)
