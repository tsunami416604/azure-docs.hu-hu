---
title: Migrálás a kapcsolódási figyelőbe (előzetes verzió) Network Performance Monitor
titleSuffix: Azure Network Watcher
description: Megtudhatja, hogyan telepítheti át a Network Performance Monitorról a kapcsolódási figyelőre (előzetes verzió).
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
ms.openlocfilehash: 69dbb1dd4017c5acf9c195f5104741caee38c2b7
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701591"
---
# <a name="migrate-to-connection-monitor-preview-from-network-performance-monitor"></a>Migrálás a kapcsolódási figyelőbe (előzetes verzió) Network Performance Monitor

A tesztek áttelepíthetők Network Performance Monitorról az új és továbbfejlesztett kapcsolódási figyelőre (előzetes verzió) egyetlen kattintással és nulla állásidővel. Ha többet szeretne megtudni az előnyökről, érdemes lehet beolvasni a [kapcsolódási figyelőt (előzetes verzió)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)

>[!NOTE]
> Csak a Service connectivity Figyelőről származó tesztek telepíthetők át a kapcsolati figyelőbe (előzetes verzió).
>

## <a name="key-points-to-note"></a>Jegyezze fel a legfontosabb pontokat

* A helyszíni ügynökök és a tűzfalbeállítások is működni fognak. Nincs szükség módosításra. Log Analytics az Azure-Virtual Machines telepített ügynököket Network Watcher bővítménnyel kell helyettesíteni
* A meglévő tesztek le lesznek képezve a kapcsolódási figyelőre (előzetes verzió) – > test Group – > tesztelési formátumra. A felhasználók a *Szerkesztés* gombra kattintva megtekinthetik és módosíthatják az új kapcsolati figyelő tulajdonságait, és letölthetik a kapcsolati figyelő módosításait, és elküldhetik azt Azure Resource Manager használatával.
* Az ügynökök mindkét Log Analytics munkaterületre és metrikára küldenek adatokat.
* Adatok monitorozása
    * Log Analyticsban tárolt adatmennyiség – az összes adatáttelepítési folyamat továbbra is abban a munkaterületen marad, amelyben a NPM a NetworkMonitoring táblában van konfigurálva. Az áttelepítés után az adatelérési pont NetworkMonitoring táblázatba és ConnectionMonitor_CL táblába kerül ugyanabban a munkaterületen. Ha a tesztek le vannak tiltva a NPM-ből, az adatok csak ConnectionMonitor_CL táblában lesznek tárolva
    * Napló alapú riasztások, irányítópultok és integrációk – az új tábla ConnectionMonitor_CL alapján manuálisan kell szerkesztenie a lekérdezéseket. Ezzel a hivatkozással újból létrehozhatja a mérőszámokban lévő riasztásokat is. A naplókon alapuló riasztások áttelepítése a NetworkMonitoring-táblán a metrikák alapú riasztásokra automatikusan, mivel a Migrálás része hamarosan elérhető lesz
    
## <a name="prerequisites"></a>Előfeltételek

*   Győződjön meg arról, Network Watcher engedélyezve van a Log Analytics munkaterület előfizetésében és régiójában
*   Az Azure-beli virtuális gépeket, amelyeken a log Analytics-ügynökök telepítve vannak, Network Watcher bővítménnyel kell engedélyezni

## <a name="steps-to-migrate-tests-from-network-performance-monitor-to-connection-monitor-preview"></a>A tesztek Network Performance Monitorról a kapcsolódási figyelőbe való áttelepíteni kívánt lépések (előzetes verzió)

1.  Kattintson a "kapcsolódási figyelő" elemre, és navigáljon a tesztek áttelepíteni a NPM-ből a kapcsolódási figyelőbe (előzetes verzió).

    ![A NPM és a Csatlakozáskezelő előzetes verziójára mutató áttelepíteni kívánt tesztek](./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png)
    
1.  Válassza ki az előfizetést, a munkaterületet és az áttelepíteni kívánt NPM szolgáltatást. Jelenleg csak teszteket telepíthet át a szolgáltatás-csatlakozási Figyelőről.  
1.  Kattintson az "Importálás" gombra a tesztek áttelepíteni
1.  Az áttelepítés megkezdése után a következő változások történnek: 
    1. Létrejön egy új Csatlakozáskezelő-erőforrás.
        1. Régiónként egy figyelőt hoznak létre. Helyszíni ügynökökkel végzett tesztek esetén az új kapcsolattípus neve <workspaceName> _"helyszíni" formátumú. Az Azure-ügynökökkel végzett tesztek esetében az új figyelő neve<Azure_region_name formátumú <workspaceName> _>
        1. A figyelési adattárolási szolgáltatás jelenleg ugyanazon a Log Analytics munkaterületen található, amelyben a NPM engedélyezve van, egy új, Connectionmonitor_CL tábla nevű táblában. 
        1. A teszt neve a csoport nevének tesztelésére szolgál. A teszt leírása nem lesz áttelepítve.
        1. A rendszer létrehozza és használja a forrás-és cél végpontokat a létrehozott tesztelési csoportban. A helyszíni ügynökök esetében a végpontok neve <workspaceName> _"Endpoint" (végpont_ <FQDN of on-premises machine> ) formátumú. Ha az Azure-ban az áttelepítési tesztek nem futnak ügynökök, akkor engedélyeznie kell az ügynököket, és újra kell telepíteni az áttelepítést.
        1. A célport és a szondázás időközét a rendszer a "TC"_ <testname> és a "TC"__ "AppThresholds" teszt-konfigurációra helyezi át <testname> . A port értékei alapján a protokoll beállítása megtörténik. A sikeres küszöbértékek és egyéb opcionális tulajdonságok üresen maradnak.
    1. A NPM nincs letiltva. Ezért az áttelepített tesztek továbbra is küldenek az NetworkMonitoring táblába és ConnectionMonitor_CL táblázatba. Ez a lépés biztosítja, hogy a meglévő napló alapú riasztások és integrációk ne legyenek hatással. A napló alapú riasztások áttelepítése a NetworkMonitoring táblázatba automatikusan metrikai alapú riasztásokra, a Migrálás részeként pedig hamarosan elérhető lesz.
    1. Az újonnan létrehozott Csatlakozáskezelő látható lesz a Csatlakozáskezelőben (előzetes verzió)
1.  Az áttelepítés után manuálisan le kell tiltania a teszteket a NPM-ben. Amíg nem teszi meg, továbbra is ugyanazt a díjat kell fizetnie. A NPM letiltása során győződjön meg arról, hogy ConnectionMonitor_CL táblán hozza létre újra a riasztásokat, vagy használjon metrikákat. Győződjön meg arról is, hogy minden külső integrációt, például az irányítópultokat Power BI, Grafana, SIEM-rendszerekkel való integrációját át kell telepíteni ConnectionMonitor_CL táblázatba


## <a name="next-steps"></a>További lépések

* Megtudhatja, [Hogyan migrálhat a kapcsolódási figyelőről a kapcsolódási figyelőre (előzetes verzió)](migrate-to-connection-monitor-preview-from-connection-monitor.md)
* Ismerje meg [, hogyan hozhatja létre a kapcsolódási figyelőt (előzetes verzió) a Azure Portal használatával](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
