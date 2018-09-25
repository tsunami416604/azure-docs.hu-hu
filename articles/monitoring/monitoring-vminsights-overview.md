---
title: Mi az Azure Monitor-beli virtuális gépek? | Microsoft Docs
description: A virtuális gépek az Azure Monitor az egyik szolgáltatása, amely egyesíti az állapotának és teljesítményének figyelése az Azure virtuális gép operációs rendszerének, valamint alkalmazás-összetevők és az egyéb erőforrások függőségeinek automatikus felderítése, és feltérképezi a kommunikációt az Azure Monitor közöttük. Ez a cikk áttekintést nyújt.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: 26fcc3eb78af53360cca57382b4c06b017f36c0e
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063268"
---
# <a name="what-is-azure-monitor-for-vms"></a>Mi az Azure Monitor-beli virtuális gépek?

Az Azure Monitor-beli virtuális gépek az Azure-beli virtuális gépek (VM) ipari méretekben elemzésével, a teljesítmény és a Windows és Linux rendszerű virtuális gépekhez, beleértve azok eltérő folyamatokat és egyéb erőforrások és a külső folyamatok összekapcsolt függőségek állapotát figyeli. A megoldás tartalmaz a teljesítmény figyelése támogatása, és a virtuális gépek alkalmazásfüggőségek üzemeltetett a helyszínen vagy egy másik felhőszolgáltatóval.  Három legfontosabb funkcióit, hogy az ilyen részletes elemzések benne:

* Windows és Linux operációs rendszert futtató Azure virtuális gépek logikai összetevőit alapján előre konfigurált állapotára vonatkozó feltételek és a riasztások alapján a kiértékelt feltétel teljesülése esetén.  
* Alapvető teljesítmény-mérőszámok a processzor, memória, lemez és a Vendég virtuális gép operációs rendszerének a hálózati adapter gyűjtött és előre definiált felkapott teljesítményét a diagramokban.
* Függőségi térkép, amely a virtuális Gépeket a felderített összekapcsolt összetevők több erőforráscsoportok és előfizetések.  

Ezek a funkciók vannak szervezve három szempontok:

* Állapot
* Teljesítmény
* Térkép

>[!NOTE]
>Az állapotfigyelő szolgáltatás jelenleg csak Azure-beli virtuális gépek kínáljuk.
>

A Log Analytics-integráció kínál hatékony összesítését, szűrés és az adatok tendenciája végrehajtása idővel. A számítási feladatok átfogó figyelést nem érhető el önmagában az Azure Monitor, a Service Map vagy a Log Analytics.  

Megtekintheti az adatok közvetlenül a környezetében egyetlen virtuális Gépet a virtuális gépről, vagy az Azure Monitor szolgáltatással biztosít a virtuális gépeket az egyes szolgáltatásokhoz, a következő perspektívát alapján összesített nézete:

* Egy erőforráscsoportba kapcsolódó virtuális gépek állapota –
* Térkép és a teljesítmény - virtuális gépek konfigurálva, hogy egy adott Log Analytics-munkaterület

![Virtuális gép insights perspektíva portálról](./media/monitoring-vminsights-overview/vminsights-azmon-directvm-01.png)

Fejlesztési és üzemeltetési hatékonyan kiszámítható teljesítményt és a fontos alkalmazások rendelkezésre állásának biztosításához azonosításával, az operációs rendszer kritikus események és a teljesítmény szűk hálózati problémákat, és ismerje meg, ha egy probléma kapcsolatos egyéb függőségek.  

## <a name="data-usage"></a>Adathasználat 

A virtuális gépek esetében a virtuális gépek által gyűjtött adatok előkészítése az Azure Monitor, amint azt betöltött és az Azure Monitor tárolja.  A virtuális gépek az Azure Monitor történik az adatok betöltött és őrzi, a metrika idősorozat-figyelt, a riasztási szabályok létrehozott értesítéseket küldeni, díjszabás szerint az Azure monitoron közzétett állapotára vonatkozó feltételek száma [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/monitor/)

A napló méretét karakterlánc hosszának számlálói alapján változik, és növelheti a logikai lemezek és hálózati adapterek számát.  Ha már rendelkezik egy munkaterületet, és ezeket a számlálókat gyűjti, nem kell a alkalmazni ismétlődő díjakat.  A Service Map már használ, az egyetlen változás megjelenik-e a kapcsolat további adatokat küld a Azure Monitor.

## <a name="next-steps"></a>További lépések
Felülvizsgálat [előkészítése az Azure Monitor-beli virtuális gépek](monitoring-vminsights-onboard.md) követelmények és módszerek ahhoz, hogy a virtuális gépek figyelése.
