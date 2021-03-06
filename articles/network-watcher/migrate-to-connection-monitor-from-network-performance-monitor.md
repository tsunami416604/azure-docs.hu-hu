---
title: Migrálás a Network Performance Monitorről a kapcsolódási figyelőbe
titleSuffix: Azure Network Watcher
description: Megtudhatja, hogyan telepítheti át a Network Performance Monitorról a kapcsolódási figyelőre.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: 688786fd60d7945340e0a027cf6ee28e6dc2bb32
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492471"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Migrálás a Network Performance Monitorről a kapcsolódási figyelőbe

Network Performance Monitor (NPM) teszteit áttelepítheti az új, továbbfejlesztett kapcsolódási figyelőre egyetlen kattintással és nulla állásidővel. További információ az előnyökről: a [kapcsolódási figyelő](./connection-monitor-overview.md).


>[!NOTE]
> A kapcsolódási figyelő csak a szolgáltatás-kapcsolati figyelőből származó teszteket lehet áttelepíteni.
>

## <a name="key-points-to-note"></a>Jegyezze fel a legfontosabb pontokat

Az áttelepítés a következő eredményeket segíti elő:

* A helyszíni ügynökök és a tűzfalbeállítások ugyanúgy működnek, mint a. Nincs szükség módosításra. Log Analytics Azure-beli virtuális gépekre telepített ügynököket a Network Watcher bővítménnyel kell helyettesíteni.
* A meglévő tesztek le vannak képezve a Csatlakozáskezelő > a tesztelési csoport > tesztelési formátumára. A **Szerkesztés** lehetőség kiválasztásával megtekintheti és módosíthatja az új kapcsolat figyelője tulajdonságait, letöltheti a sablonokat, és elküldheti a sablont Azure Resource Manager használatával.
* Az ügynökök az Log Analytics munkaterületre és a metrikára is küldenek adatokat.
* Adatfigyelés:
   * **Log Analyticsban tárolt adatértékek**: az áttelepítés előtt az adatterület abban a munkaterületen marad, amelyben a NPM a NetworkMonitoring táblában van konfigurálva. Az áttelepítés után az adatelérési pont a NetworkMonitoring táblába kerül, és a ConnectionMonitor_CL tábla ugyanabban a munkaterületen található. Miután a tesztek le vannak tiltva a NPM-ben, az adattárolást csak a ConnectionMonitor_CL táblában tárolja a rendszer.
   * **Napló alapú riasztások, irányítópultok és integrációk**: az új ConnectionMonitor_CL táblázat alapján manuálisan kell szerkesztenie a lekérdezéseket. A riasztások a mérőszámokban való újbóli létrehozásával kapcsolatban lásd: [hálózati kapcsolat figyelése a kapcsolat figyelője szolgáltatással](./connection-monitor-overview.md#metrics-in-azure-monitor).
    
## <a name="prerequisites"></a>Előfeltételek

* Győződjön meg arról, hogy a Network Watcher engedélyezve van az előfizetésben és a Log Analytics munkaterület régiójában.
* A telepített Log Analytics-ügynökökkel rendelkező Azure-beli virtuális gépeket a Network Watcher bővítménnyel kell engedélyezni.

## <a name="migrate-the-tests"></a>A tesztek áttelepíteni

A tesztek Network Performance Monitorról a következőre történő áttelepíthetők:

1. A Network Watcher területen válassza a **Csatlakozáskezelő** lehetőséget, majd válassza a **tesztek átmigrálása a NPM** lapról lehetőséget. 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Tesztek migrálása Network Performance Monitorról a kapcsolódási figyelőbe" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. A legördülő listában válassza ki az előfizetést és a munkaterületet, majd válassza ki az áttelepíteni kívánt NPM szolgáltatást. Jelenleg csak a Service connectivity figyelőből lehet áttelepíteni a teszteket.  
1. Az **Importálás** gombra kattintva áttelepítheti a teszteket.

Az áttelepítés megkezdése után a következő módosítások lépnek érvénybe: 
* Létrejön egy új Csatlakozáskezelő-erőforrás.
   * Régiónként egy figyelőt hoznak létre. Helyszíni ügynökökkel végzett tesztek esetén az új figyelő neve a következő lesz: `<workspaceName>_"on-premises"` . Az Azure-ügynökökkel végzett tesztek esetében az új figyelő neve a következő lesz: `<workspaceName>_<Azure_region_name>` .
   * A figyelési adattárolási szolgáltatás jelenleg ugyanazon a Log Analytics munkaterületen található, amelyben a NPM engedélyezve van, egy új táblázatban Connectionmonitor_CL néven. 
   * A teszt neve a test Group neve alapján kerül továbbításra. A teszt leírása nincs áttelepítve.
   * A forrás és a cél végpontok létrehozása és használata az új tesztelési csoportban történik. A helyszíni ügynökök esetében a végpontok formátuma a következő: `<workspaceName>_"endpoint"_<FQDN of on-premises machine>` . Az Azure esetében, ha az áttelepítési tesztek olyan ügynököket tartalmaznak, amelyek nem futnak, engedélyeznie kell az ügynököket, és újra kell telepíteni az áttelepítést.
   * A célport és a Szondázási intervallum átkerül egy *\<testname> TC_* nevű és *TC_ \<testname> _AppThresholdsi* teszt-konfigurációba. A protokoll beállítása a portok értékei alapján történik. A sikeres küszöbértékek és egyéb opcionális tulajdonságok üresen maradnak.
* A NPM nincs letiltva, ezért az áttelepített tesztek továbbra is küldhetnek adatátvitelt a NetworkMonitoring és ConnectionMonitor_CL táblákba. Ez a megközelítés biztosítja, hogy a meglévő napló alapú riasztások és integrációk ne legyenek hatással.
* Az újonnan létrehozott Csatlakozáskezelő látható a Csatlakozáskezelőben.

Az áttelepítés után ügyeljen a következőre:
* Manuálisan tiltsa le a teszteket a NPM. Amíg így tesz, továbbra is díjat kell fizetnie. 
* A NPM letiltása közben hozza létre újra a riasztásokat a ConnectionMonitor_CL táblában, vagy használjon metrikákat. 
* Telepítse át a külső integrációkat a ConnectionMonitor_CL táblába. A külső integrációk példái a Power BI-és Grafana található irányítópultok, valamint a biztonsági információkkal és az Event Management-(SIEM-) rendszerekkel való integrációk.


## <a name="next-steps"></a>További lépések

A kapcsolódási Figyelőről további információt a következő témakörben talál:
* [Áttelepítés a kapcsolódási Figyelőről a kapcsolódási figyelőre](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Csatlakozáskezelő létrehozása a Azure Portal használatával](./connection-monitor-create-using-portal.md)