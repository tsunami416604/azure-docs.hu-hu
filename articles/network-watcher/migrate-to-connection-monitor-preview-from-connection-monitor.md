---
title: Migrálás a kapcsolódási figyelőbe (előzetes verzió) a kapcsolódási figyelőből
titleSuffix: Azure Network Watcher
description: Megtudhatja, hogyan telepítheti át a kapcsolódási figyelőre (előzetes verzió) a kapcsolódási Figyelőről.
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
ms.openlocfilehash: 05b42024529b8d9de3590488ecafbdf83283e007
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441816"
---
# <a name="migrate-to-connection-monitor-preview-from-connection-monitor"></a>Migrálás a kapcsolódási figyelőbe (előzetes verzió) a kapcsolódási figyelőből

A meglévő kapcsolódási figyelőket áttelepítheti új, továbbfejlesztett (előzetes verzió), és csak néhány kattintással és nulla állásidővel. További információ az előnyökről: a [kapcsolatkezelő (előzetes verzió)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview).

## <a name="key-points-to-note"></a>Jegyezze fel a legfontosabb pontokat

Az áttelepítés a következő eredményeket segíti elő:

* Az ügynökök és a tűzfalbeállítások ugyanúgy működnek, mint a. Nincs szükség módosításra. 
* A meglévő kapcsolódási figyelők a kapcsolódási figyelőre (előzetes verzió) > a tesztelési csoport > tesztelési formátumára vannak leképezve. A **Szerkesztés**lehetőség kiválasztásával megtekintheti és módosíthatja az új kapcsolat figyelője tulajdonságait, letölthet egy sablont a kapcsolati figyelő módosításához, és beküldheti azt Azure Resource Manager használatával. 
* Az Network Watcher bővítménnyel rendelkező Azure-beli virtuális gépek adatokat küldenek a munkaterületnek és a mérőszámoknak. A Csatlakozáskezelő az új metrikák (ChecksFailedPercent [előzetes verzió] és RoundTripTimeMs [előzetes verzió]) révén elérhetővé teszi az adatokat a régi metrikák (ProbesFailedPercent és AverageRoundtripMs) helyett. 
* Adatfigyelés:
   * **Riasztások**: a rendszer automatikusan áttelepíti az új metrikákat.
   * **Irányítópultok és integrációk**: a beállított mérőszámok manuális szerkesztését igényli. 
    
## <a name="prerequisites"></a>Előfeltételek

Ha egyéni munkaterületet használ, győződjön meg arról, hogy a Network Watcher engedélyezve van az előfizetésben és a Log Analytics munkaterület régiójában. 

## <a name="migrate-the-connection-monitors"></a>A hálózati figyelők migrálása

1. Ha a régebbi kapcsolódási figyelőket át szeretné telepíteni az újabb verzióra, válassza a **Csatlakozáskezelő**lehetőséget, majd válassza a **kapcsolódási figyelők áttelepíteni**lehetőséget.

    ![A kapcsolódási figyelők áttelepítését bemutató képernyőfelvétel a kapcsolódási figyelőhöz (előzetes verzió).](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Válassza ki az előfizetését és az áttelepíteni kívánt kapcsolódási figyelőket, majd válassza a **kijelölt áttelepít**lehetőséget. 

Csak néhány kattintással áttelepítette a meglévő kapcsolódási figyelőket a Csatlakozáskezelő (előzetes verzió) szolgáltatásba. 

Mostantól testreszabhatja a Csatlakozáskezelő (előzetes verzió) tulajdonságait, módosíthatja az alapértelmezett munkaterületet, letöltheti a sablonokat, és ellenőrizheti az áttelepítési állapotot. 

Az áttelepítés megkezdése után a következő módosítások lépnek érvénybe: 
* A Azure Resource Manager erőforrás az újabb kapcsolódási figyelőre módosul.
    * A Csatlakozáskezelő neve, régiója és előfizetése változatlan marad. Az erőforrás-azonosító nem érvényes.
    * Ha a Csatlakozáskezelő nincs testreszabva, a rendszer az előfizetésben és a Csatlakozáskezelő régiójában hozza létre az alapértelmezett Log Analytics munkaterületet. Ezt a munkaterületet a figyelési adattárolási pont tárolja. A teszt eredményének adatait a metrikák is tárolják.
    * Minden teszt át lett telepítve egy *defaultTestGroup*nevű tesztelési csoportba.
    * A forrás és a cél végpontok létrehozása és használata az új tesztelési csoportban történik. Az alapértelmezett nevek: *defaultSourceEndpoint* és *defaultDestinationEndpoint*.
    * A rendszer áthelyezi a célport és a szondázás intervallumát egy *defaultTestConfiguration*nevű teszt-konfigurációba. A protokoll beállítása a portok értékei alapján történik. A sikeres küszöbértékek és egyéb opcionális tulajdonságok üresen maradnak.
* A metrikák riasztásait a rendszer áttelepíti a kapcsolódási figyelő (előzetes verzió) mérőszámokra. A metrikák eltérnek, ezért a változás. További információ: [hálózati kapcsolat figyelése a kapcsolat figyelője (előzetes verzió)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#metrics-in-azure-monitor).
* Az áttelepített kapcsolatok figyelője már nem jelenik meg a régebbi verziójú kapcsolatkezelő megoldásként. Most már csak a Csatlakozáskezelő (előzetes verzió) szolgáltatásban érhetők el.
* A külső integrációkat, például az Power BI-és Grafana-irányítópultokat, valamint a biztonsági információkkal és az eseménykezelő (SIEM) rendszerekkel való integrációt manuálisan kell áttelepíteni. Ez az egyetlen kézi lépés, amelyet a telepítő áttelepítéséhez kell végrehajtania.

## <a name="next-steps"></a>Következő lépések

A Csatlakozáskezelő (előzetes verzió) szolgáltatásról további információt a következő témakörben talál:
* [Áttelepítés Network Performance Monitorról a kapcsolódási figyelőbe (előzetes verzió)](migrate-to-connection-monitor-preview-from-network-performance-monitor.md)
* [Csatlakozáskezelő (előzetes verzió) létrehozása a Azure Portal használatával](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
