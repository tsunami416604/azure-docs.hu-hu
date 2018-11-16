---
title: Mi az Azure Monitor-beli virtuális gépek (előzetes verzió)? | Microsoft Docs
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
ms.date: 11/07/2018
ms.author: magoedte
ms.openlocfilehash: d37ca7d46f1231a8e1b0b258c10089fe6ba81fba
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51715549"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Mi az Azure Monitor-beli virtuális gépek (előzetes verzió)?

A virtuális gépek az Azure Monitor figyeli az Azure-beli virtuális gépek (VM) és az Azure-beli virtuálisgép-méretezési csoportok ipari méretekben elemzésével, a teljesítmény és a Windows és Linux rendszerű virtuális gépekhez, beleértve a különböző folyamatok és az egyéb összekapcsolt függőségek állapotát erőforrások és a külső folyamatok. A megoldás tartalmaz a teljesítmény figyelése támogatása, és a virtuális gépek alkalmazásfüggőségek üzemeltetett a helyszínen vagy egy másik felhőszolgáltatóval. Három legfontosabb funkcióit, hogy az ilyen részletes elemzések benne:

* Windows és Linux operációs rendszert futtató Azure virtuális gépek logikai összetevőit alapján előre konfigurált állapotára vonatkozó feltételek és a riasztások alapján a kiértékelt feltétel teljesülése esetén.  
* Alapvető teljesítmény-mérőszámok a processzor, memória, lemez és a Vendég virtuális gép operációs rendszerének a hálózati adapter gyűjtött és előre definiált felkapott teljesítményét a diagramokban.
* Függőségi térkép, amely a virtuális Gépeket a felderített összekapcsolt összetevők több erőforráscsoportok és előfizetések.  

Ezek a funkciók vannak szervezve három szempontok:

* Állapot
* Teljesítmény
* Térkép

>[!NOTE]
>Jelenleg az állapotfigyelő szolgáltatás csak kínáljuk az Azure virtuális gépek és virtuálisgép-méretezési csoportok. Teljesítmény és a térkép támogatja az Azure virtuális gépek és a környezet vagy a más felhőszolgáltatók által futtatott virtuális gépek is.
>

A Log Analytics-integráció kínál hatékony összesítését, szűrés és az adatok tendenciája végrehajtása idővel. A számítási feladatok átfogó figyelést nem érhető el önmagában az Azure Monitor, a Service Map vagy a Log Analytics.  

Megtekintheti az adatok közvetlenül a környezetében egyetlen virtuális Gépet a virtuális gépről, vagy az Azure Monitor szolgáltatással biztosít a virtuális gépeket az egyes szolgáltatásokhoz, a következő perspektívát alapján összesített nézete:

* Egy erőforráscsoportba kapcsolódó virtuális gépek állapota –
* Térkép és a teljesítmény - virtuális gépek konfigurálva, hogy egy adott Log Analytics-munkaterület

![Virtuális gép insights perspektíva portálról](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

Fejlesztési és üzemeltetési hatékonyan kiszámítható teljesítményt és a fontos alkalmazások rendelkezésre állásának biztosításához azonosításával, az operációs rendszer kritikus események és a teljesítmény szűk hálózati problémákat, és ismerje meg, ha egy probléma kapcsolatos egyéb függőségek.  

## <a name="data-usage"></a>Adathasználat 

A virtuális gépek esetében a virtuális gépek által gyűjtött adatok előkészítése az Azure Monitor, amint azt betöltött és az Azure Monitor tárolja. A virtuális gépek az Azure Monitor történik az adatok betöltött és őrzi, a metrika idősorozat-figyelt, a riasztási szabályok létrehozott értesítéseket küldeni, díjszabás szerint az Azure monitoron közzétett állapotára vonatkozó feltételek száma [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/monitor/)

A napló méretét karakterlánc hosszának számlálói alapján változik, és növelheti a logikai lemezek és hálózati adapterek számát. Ha már rendelkezik egy munkaterületet, és ezeket a számlálókat gyűjti, nem kell a alkalmazni ismétlődő díjakat. A Service Map már használ, az egyetlen változás megjelenik-e a kapcsolat további adatokat küld a Azure Monitor.

## <a name="next-steps"></a>További lépések
Felülvizsgálat [előkészítése az Azure Monitor-beli virtuális gépek](vminsights-onboard.md) követelmények és módszerek ahhoz, hogy a virtuális gépek figyelése.
