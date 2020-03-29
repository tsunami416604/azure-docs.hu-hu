---
title: Az Azure Service Fabric teljesítményfigyelése
description: Ismerje meg az Azure Service Fabric-fürtök figyeléséhez és diagnosztikájához szolgáló teljesítményszámlálókat.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 30b9b8393007033a7c2e6798cd57d9cf0128820d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464700"
---
# <a name="performance-metrics"></a>Teljesítmény-mérőszámok

Metrikákat kell gyűjteni a fürt és a benne futó alkalmazások teljesítményének megértéséhez. A Service Fabric-fürtök esetében azt javasoljuk, hogy gyűjtse össze a következő teljesítményszámlálókat.

## <a name="nodes"></a>Csomópontok

A fürtben lévő gépek esetében érdemes lehet a következő teljesítményszámlálók at gyűjteni az egyes gépek terhelésének jobb megértéséhez, és megfelelő fürtskálázási döntéseket hozni.

| Számláló kategória | Számláló neve |
| --- | --- |
| Logikai lemez | Logikai lemezszabad terület |
| PhysicalDisk(lemezenként) | Lemezolvasási várólista átlagos hossza |
| PhysicalDisk(lemezenként) | Lemezírási várólista átlagos hossza |
| PhysicalDisk(lemezenként) | Átlagos lemezmásodperc/olvasás |
| PhysicalDisk(lemezenként) | Átlagos lemez másodperc/írás |
| PhysicalDisk(lemezenként) | Lemezolvasás/mp |
| PhysicalDisk(lemezenként) | Lemez olvasási bájtjai/mp |
| PhysicalDisk(lemezenként) | Lemezírás/mp |
| PhysicalDisk(lemezenként) | Lemezírási bájtok/mp |
| Memory (Memória) | Elérhető MBytes |
| Lapozófájl | % használat |
| Processzor(összesen) | A processzor kihasználtsága (%) |
| Folyamat (szolgáltatásonként) | A processzor kihasználtsága (%) |
| Folyamat (szolgáltatásonként) | Azonosító folyamata |
| Folyamat (szolgáltatásonként) | Privát bájtok |
| Folyamat (szolgáltatásonként) | Szálak száma |
| Folyamat (szolgáltatásonként) | Felhasznált virtuális memória jelenlegi mérete (bájt) |
| Folyamat (szolgáltatásonként) | Munkakészlet |
| Folyamat (szolgáltatásonként) | Munkakészlet - Privát |
| Hálózati adapter (összes példány) | Újrabeírható bájtok |
| Hálózati adapter (összes példány) | Küldött bájtok |
| Hálózati adapter (összes példány) | Összes bájt |
| Hálózati adapter (összes példány) | Kimeneti várólista hossza |
| Hálózati adapter (összes példány) | Kimenő kimenő elvetett csomagok |
| Hálózati adapter (összes példány) | Fogadott csomagok elvetve |
| Hálózati adapter (összes példány) | Kimenő csomagok hibái |
| Hálózati adapter (összes példány) | Fogadott csomagok hibái |

## <a name="net-applications-and-services"></a>.NET alkalmazások és szolgáltatások

A következő számlálók összegyűjtése, ha .NET szolgáltatásokat telepít a fürtre. 

| Számláló kategória | Számláló neve |
| --- | --- |
| .NET CLR memória (szolgáltatásonként) | Folyamatazonosító |
| .NET CLR memória (szolgáltatásonként) | # Összes elkötött bájt |
| .NET CLR memória (szolgáltatásonként) | # Összes lefoglalt bájt |
| .NET CLR memória (szolgáltatásonként) | # Bájt minden halomban |
| .NET CLR memória (szolgáltatásonként) | Nagy objektumhalom mérete |
| .NET CLR memória (szolgáltatásonként) | # GC fogantyúk |
| .NET CLR memória (szolgáltatásonként) | # Gen 0 Gyűjtemények |
| .NET CLR memória (szolgáltatásonként) | # Gen 1 Gyűjtemények |
| .NET CLR memória (szolgáltatásonként) | # Gen 2 Gyűjtemények |
| .NET CLR memória (szolgáltatásonként) | %-os idő gc-ben |

### <a name="service-fabrics-custom-performance-counters"></a>A Service Fabric egyéni teljesítményszámlálói

A Service Fabric jelentős mennyiségű egyéni teljesítményszámlálót hoz létre. Ha telepítve van az SDK, a Teljesítményfigyelő alkalmazásban (Start > Performance Monitor) megtekintheti a Windows-gépen található átfogó listát. 

A fürtre üzembe helyezett alkalmazásokban, ha Reliable Actors-t használ, `Service Fabric Actor Method` adjon hozzá számlálókat és kategóriákat `Service Fabric Actor` (lásd: Service Fabric Reliable Actors [Diagnostics).](service-fabric-reliable-actors-diagnostics.md)

Ha megbízható szolgáltatásokat vagy szolgáltatásátmosoló-szolgáltatást `Service Fabric Service` használ, hasonlóképpen vannak, és `Service Fabric Service Method` számlálókategóriákat kell gyűjtenünk, [lásd: figyelés szolgáltatás-átirányító](service-fabric-reliable-serviceremoting-diagnostics.md) és [megbízható szolgáltatások teljesítményszámlálói.](service-fabric-reliable-services-diagnostics.md#performance-counters) 

Ha megbízható gyűjtemények használata, azt `Avg. Transaction ms/Commit` javasoljuk, hogy adja hozzá a a `Service Fabric Transactional Replicator` tranzakciónkénti átlagos véglegesítési késés gyűjtése.


## <a name="next-steps"></a>További lépések

* További információ [az eseménygenerálásról platformszinten a](service-fabric-diagnostics-event-generation-infra.md) Service Fabric ben
* Teljesítménymutatók gyűjtése a [Log Analytics-ügynökön](service-fabric-diagnostics-oms-agent.md) keresztül
