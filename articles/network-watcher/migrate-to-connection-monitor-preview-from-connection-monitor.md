---
title: Migrálás a kapcsolódási figyelőbe (előzetes verzió) a kapcsolódási figyelőből
titleSuffix: Azure Network Watcher
description: Megtudhatja, hogyan telepítheti át a kapcsolódási figyelőre (előzetes verzió) a kapcsolódási Figyelőről.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: ddf6e326df876229d32ef15983f76879836f1fca
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701596"
---
# <a name="migrate-to-connection-monitor-preview-from-connection-monitor"></a>Migrálás a kapcsolódási figyelőbe (előzetes verzió) a kapcsolódási figyelőből

Egy kattintással és nulla állásidővel áttelepítheti a meglévő kapcsolódási figyelőket az új és továbbfejlesztett Csatlakozáskezelő szolgáltatásba (előzetes verzió). Ha többet szeretne megtudni az előnyökről, érdemes lehet beolvasni a [kapcsolódási figyelőt (előzetes verzió)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)

## <a name="key-points-to-note"></a>Jegyezze fel a legfontosabb pontokat

* Az ügynökök és a tűzfalbeállítások ugyanúgy működnek, mint a (nem szükséges érintés) 
* A meglévő kapcsolódási figyelők le lesznek képezve a kapcsolódási figyelőre (előzetes verzió) – > test Group – > tesztelési formátumra. A felhasználók a *Szerkesztés* gombra kattintva megtekinthetik és módosíthatják az új kapcsolati figyelő tulajdonságait, és letölthetik a kapcsolati figyelő módosításait, és elküldhetik azt Azure Resource Manager használatával. 
* Az Network Watcher bővítménnyel rendelkező Azure-beli virtuális gépek adatokat küldenek a munkaterületre és a mérőszámokra. A kapcsolatok figyelője az új metrikák (ChecksFailedPercent (előzetes verzió) és RoundTripTimeMs (előzetes verzió)) révén elérhetővé teszi az adatokat a régi mérőszámok leállítása helyett (ProbesFailedPercent és AverageRoundtripMs) 
* Adatok monitorozása
    * Riasztások – az új metrikák áttelepítése a Migrálás részeként történik
    * Irányítópultok és integrációk – manuálisan kell szerkesztenie a metrikákat. 
    
## <a name="prerequisites"></a>Előfeltételek

Ha egyéni munkaterületet használ, győződjön meg arról, hogy Network Watcher engedélyezve van a Log Analytics munkaterület előfizetésében és régiójában. 

## <a name="steps-to-migrate-from-connection-monitor-to-connection-monitor-preview"></a>A kapcsolódási Figyelőről a kapcsolódási figyelőre való Migrálás lépései (előzetes verzió)

1. Kattintson a "kapcsolódási figyelő" lehetőségre, és navigáljon a "kapcsolódási figyelők átmigrálása" elemre, és telepítse át a korábbi és az újabb megoldás közötti kapcsolatok

    ![A kapcsolódási figyelőket a Csatlakozáskezelő előzetes verziójára mutató képernyőkép](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Válassza az előfizetés és a kapcsolatok figyelők lehetőséget, majd kattintson a "kijelöltek áttelepítve" elemre. Egyetlen kattintással áttelepítheti a meglévő kapcsolódási figyelőket a Csatlakozáskezelő szolgáltatásba (előzetes verzió) 
1. Testreszabhatja a Csatlakozáskezelő tulajdonságait, módosíthatja az alapértelmezett munkaterületet, letöltheti a sablont, és ellenőrizheti az áttelepítés állapotát. 
1. Az áttelepítés megkezdése után a következő változások történnek: 
    1. Azure Resource Manager az erőforrás módosításait az újabb kapcsolódási figyelőre
        1. A Csatlakozáskezelő neve, régiója és előfizetése változatlan marad. Ezért nincs hatással az erőforrás-AZONOSÍTÓra.
        1. Ha testre van szabva, az alapértelmezett Log Analytics munkaterület a Csatlakozáskezelő régiójában és előfizetésében jön létre. Ezen a munkaterületen tárolódnak a figyelési adattárolók. A teszt eredményének adatait a rendszer a mérőszámokban is tárolja.
        1. Minden teszt a * defaultTestGroup * nevű tesztelési csoportba kerül át.
        1.  A rendszer létrehozza és használja a forrás-és cél végpontokat a létrehozott tesztelési csoportban. Az alapértelmezett nevek a *defaultSourceEndpoint* és a *defaultDestinationEndpoint*
        1. A célport és a Szondázási intervallum átkerül a *defaultTestConfiguration*nevű teszt-konfigurációba. A port értékei alapján a protokoll beállítása megtörténik. A sikeres küszöbértékek és egyéb opcionális tulajdonságok üresen maradnak.
    1. A metrikai riasztások áttelepültek a kapcsolódási figyelő (előzetes verzió) metrikai riasztásokra. A metrikák eltérnek <link to metric section in the doc> , ezért a változás
    1. Az áttelepített kapcsolati figyelők nem jelennek meg a korábbi kapcsolat-figyelő megoldásban, csak a kapcsolati figyelőben használhatók (előzetes verzió)
    1. Minden külső integrációt, például az Power BI-irányítópultokat, a Grafana, a SIEM-rendszerekkel való integrációt közvetlenül a felhasználónak kell áttelepítenie. Ez az egyetlen manuális lépés, amelyet a felhasználónak végre kell hajtania a beállítás áttelepítéséhez.

## <a name="next-steps"></a>További lépések

* Ismerje meg [, hogyan telepíthet át Network Performance monitorról a kapcsolódási figyelőre (előzetes verzió)](migrate-to-connection-monitor-preview-from-network-performance-monitor.md)
* Ismerje meg [, hogyan hozhatja létre a kapcsolódási figyelőt (előzetes verzió) a Azure Portal használatával](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
