---
title: Az Azure Service Fabric teljesítmény figyelése |} Microsoft Docs
description: További információk a figyelési és az Azure Service Fabric-fürtök diagnostics teljesítményszámlálók.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: 9e740dd3acce842f888e5994fe8f46222477adc1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="performance-metrics"></a>Teljesítmény-mérőszámok

Metrikák be kell tudni, hogy a fürt, valamint azt a futó alkalmazások teljesítményére. A Service Fabric-fürtök esetén ajánlott, az alábbi teljesítményszámlálók gyűjtése.

## <a name="nodes"></a>Csomópontok

A gépek a fürt érdemes lehet jobban megismerni a minden gép terhelését, és ellenőrizze a megfelelő döntések skálázás fürt számára az alábbi teljesítményszámlálók gyűjtése.

| Teljesítményszámláló-kategóriája | Számláló neve |
| --- | --- |
| Fizikai lemez (lemezenként) | Átlagos Olvasási Lemezvárólista hossza |
| Fizikai lemez (lemezenként) | Átlagos Írási Lemezvárólista hossza |
| Fizikai lemez (lemezenként) | Átlagos Lemez mp/Olvasás |
| Fizikai lemez (lemezenként) | Átlagos Lemez mp/írás |
| Fizikai lemez (lemezenként) | Lemezolvasások/mp |
| Fizikai lemez (lemezenként) | Lemezolvasási sebesség (bájt/s) |
| Fizikai lemez (lemezenként) | Lemezírás/mp |
| Fizikai lemez (lemezenként) | Lemezírási sebesség (bájt/s) |
| Memory (Memória) | Rendelkezésre álló memória (MB) |
| PagingFile | Kihasználtsága |
| Processor(total) | Processzor kihasználtsága |
| Folyamat száma (szolgáltatás) | Processzor kihasználtsága |
| Folyamat száma (szolgáltatás) | A folyamatot |
| Folyamat száma (szolgáltatás) | Saját [nem megosztható] memória (bájt) |
| Folyamat száma (szolgáltatás) | Szálak száma |
| Folyamat száma (szolgáltatás) | Virtuális memória |
| Folyamat száma (szolgáltatás) | Munkakészlet |
| Folyamat száma (szolgáltatás) | Munkakészlet - saját |
| Hálózati Interface(all-instances) | Kimeneti várólista hossza |
| Hálózati Interface(all-instances) | Elvetett kimenő csomagok |
| Hálózati Interface(all-instances) | Az elvetett fogadott csomagok |
| Hálózati Interface(all-instances) | Hibás kimenő csomagok |
| Hálózati Interface(all-instances) | A fogadott csomagok hibák |

## <a name="net-applications-and-services"></a>.NET-alkalmazások és szolgáltatások

Be az alábbi számlálók értékét, ha a .NET-szolgáltatásokat telepít a fürthöz. 

| Teljesítményszámláló-kategóriája | Számláló neve |
| --- | --- |
| .NET CLR memória (szolgáltatás) | Folyamat azonosítója |
| .NET CLR memória (szolgáltatás) | # Összesen előjegyzett memória kihasználtsága |
| .NET CLR memória (szolgáltatás) | # Összesen fenntartott memória |
| .NET CLR memória (szolgáltatás) | (Bájt) összes halommemória |
| .NET CLR memória (szolgáltatás) | # Generációs gyűjtemények 0 |
| .NET CLR memória (szolgáltatás) | # Generációs gyűjtemények 1 |
| .NET CLR memória (szolgáltatás) | # Generációs gyűjtemények 2 |
| .NET CLR memória (szolgáltatás) | % Töltött idő |

### <a name="service-fabrics-custom-performance-counters"></a>A Service Fabric egyéni teljesítményszámlálói

A Service Fabric egyéni teljesítményszámlálóit jelentős mennyiségű állít elő. Ha az SDK-val telepített, látható az átfogó listáját a Windows-számítógépre az alkalmazás teljesítményének figyelése (Start > Teljesítményfigyelő). 

Az alkalmazások üzembe helyezi a fürthöz, ha Reliable Actors használ, adja hozzá a countes `Service Fabric Actor` és `Service Fabric Actor Method` kategóriák (lásd: [Service Fabric megbízható szereplője diagnosztika](service-fabric-reliable-actors-diagnostics.md)).

Ha Reliable Services használatához hasonló módon kell `Service Fabric Service` és `Service Fabric Service Method` kell gyűjtése teljesítményszámláló-kategóriák a teljesítményszámlálók. 

Megbízható gyűjtemények használatakor, azt javasoljuk, hogy a `Avg. Transaction ms/Commit` a a `Service Fabric Transactional Replicator` / tranzakció metrika az átlagos véglegesítési késés gyűjtéséhez.


## <a name="next-steps"></a>További lépések

* További információ [platform szintjén az eseménygenerálás](service-fabric-diagnostics-event-generation-infra.md) a Service Fabric
* Teljesítményadatok gyűjtéséhez keresztül [OMS-ügynököt](service-fabric-diagnostics-oms-agent.md)
