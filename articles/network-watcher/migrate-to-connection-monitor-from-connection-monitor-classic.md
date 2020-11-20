---
title: Migrálás a kapcsolódási figyelőbe (előzetes verzió) a kapcsolódási figyelőből
titleSuffix: Azure Network Watcher
description: Megtudhatja, hogyan telepítheti át a kapcsolódási figyelőt a Csatlakozáskezelő szolgáltatásból.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: d785ecd48816150cd33d8e2f5fcce2ff36f1603f
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94985790"
---
# <a name="migrate-to-connection-monitor-preview-from-connection-monitor-classic"></a>Migrálás a kapcsolódási figyelőbe (előzetes verzió) a kapcsolódási figyelőből (klasszikus)

A meglévő kapcsolódási figyelőket áttelepítheti új, továbbfejlesztett (előzetes verzió), és csak néhány kattintással és nulla állásidővel. További információ az előnyökről: a [kapcsolódási figyelő](./connection-monitor-overview.md).

> [!IMPORTANT]
> A Csatlakozáskezelő jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-points-to-note"></a>Jegyezze fel a legfontosabb pontokat

Az áttelepítés a következő eredményeket segíti elő:

* Az ügynökök és a tűzfalbeállítások ugyanúgy működnek, mint a. Nincs szükség módosításra. 
* A meglévő kapcsolódási figyelők le vannak képezve a > a test Group > tesztelési formátumára. A **Szerkesztés** lehetőség kiválasztásával megtekintheti és módosíthatja az új kapcsolat figyelője tulajdonságait, letölthet egy sablont a kapcsolati figyelő módosításához, és beküldheti azt Azure Resource Manager használatával. 
* Az Network Watcher bővítménnyel rendelkező Azure-beli virtuális gépek adatokat küldenek a munkaterületnek és a mérőszámoknak. A Csatlakozáskezelő az új metrikák (ChecksFailedPercent [előzetes verzió] és RoundTripTimeMs [előzetes verzió]) révén elérhetővé teszi az adatokat a régi metrikák (ProbesFailedPercent és AverageRoundtripMs) helyett. 
* Adatfigyelés:
   * **Riasztások**: a rendszer automatikusan áttelepíti az új metrikákat.
   * **Irányítópultok és integrációk**: a beállított mérőszámok manuális szerkesztését igényli. 
    
## <a name="prerequisites"></a>Előfeltételek

Ha egyéni munkaterületet használ, győződjön meg arról, hogy a Network Watcher engedélyezve van az előfizetésben és a Log Analytics munkaterület régiójában. 

## <a name="migrate-the-connection-monitors"></a>A hálózati figyelők migrálása

1. Ha a régebbi kapcsolódási figyelőket át szeretné telepíteni az újabb verzióra, válassza a **Csatlakozáskezelő** lehetőséget, majd válassza a **kapcsolódási figyelők áttelepíteni** lehetőséget.

    ![A kapcsolódási figyelők áttelepítését bemutató képernyőfelvétel a kapcsolódási figyelőhöz.](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Válassza ki az előfizetését és az áttelepíteni kívánt kapcsolódási figyelőket, majd válassza a **kijelölt áttelepít** lehetőséget. 

Mindössze néhány kattintással áttelepítette a meglévő kapcsolódási figyelőket a Csatlakozáskezelő szolgáltatásba. 

Mostantól testreszabhatja a Csatlakozáskezelő tulajdonságait, módosíthatja az alapértelmezett munkaterületet, letöltheti a sablonokat, és ellenőrizheti az áttelepítés állapotát. 

Az áttelepítés megkezdése után a következő módosítások lépnek érvénybe: 
* A Azure Resource Manager erőforrás az újabb kapcsolódási figyelőre módosul.
    * A Csatlakozáskezelő neve, régiója és előfizetése változatlan marad. Az erőforrás-azonosító nem érvényes.
    * Ha a Csatlakozáskezelő nincs testreszabva, a rendszer az előfizetésben és a Csatlakozáskezelő régiójában hozza létre az alapértelmezett Log Analytics munkaterületet. Ezt a munkaterületet a figyelési adattárolási pont tárolja. A teszt eredményének adatait a metrikák is tárolják.
    * Minden teszt át lett telepítve egy *defaultTestGroup* nevű tesztelési csoportba.
    * A forrás és a cél végpontok létrehozása és használata az új tesztelési csoportban történik. Az alapértelmezett nevek: *defaultSourceEndpoint* és *defaultDestinationEndpoint*.
    * A rendszer áthelyezi a célport és a szondázás intervallumát egy *defaultTestConfiguration* nevű teszt-konfigurációba. A protokoll beállítása a portok értékei alapján történik. A sikeres küszöbértékek és egyéb opcionális tulajdonságok üresen maradnak.
* A metrikák riasztásait a rendszer áttelepíti a kapcsolódási figyelő metrikáinak riasztására. A metrikák eltérnek, ezért a változás. További információ: [hálózati kapcsolat figyelése a kapcsolat figyelője szolgáltatással](./connection-monitor-overview.md#metrics-in-azure-monitor).
* Az áttelepített kapcsolatok figyelője már nem jelenik meg a régebbi verziójú kapcsolatkezelő megoldásként. Most már csak a figyelőben használhatók.
* A külső integrációkat, például az Power BI-és Grafana-irányítópultokat, valamint a biztonsági információkkal és az eseménykezelő (SIEM) rendszerekkel való integrációt manuálisan kell áttelepíteni. Ez az egyetlen kézi lépés, amelyet a telepítő áttelepítéséhez kell végrehajtania.

## <a name="next-steps"></a>Következő lépések

A kapcsolódási Figyelőről további információt a következő témakörben talál:
* [Áttelepítés Network Performance Monitorról a kapcsolódási figyelőbe](/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor)
* [Csatlakozáskezelő létrehozása a Azure Portal használatával](./connection-monitor-create-using-portal.md)