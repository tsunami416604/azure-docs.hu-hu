---
title: "Az Azure Service Fabric teljesítmény figyelése |} Microsoft Docs"
description: "További információk a figyelési és az Azure Service Fabric-fürtök diagnostics teljesítményszámlálók."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: b19a2db85b2e1cc4c5f79f6b0dee97965f40ef88
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2017
---
# <a name="performance-metrics"></a>Teljesítménymértékeket

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
| Fizikai lemez (lemezenként) | Lemez sebessége olvasott bájt/mp |
| Fizikai lemez (lemezenként) | Lemezírás/mp |
| Fizikai lemez (lemezenként) | Lemez írási bájtok/s |
| Memory (Memória) | Rendelkezésre álló memória (MB) |
| PagingFile | Kihasználtsága |
| Processor(total) | Processzor kihasználtsága |
| Folyamat száma (szolgáltatás) | Processzor kihasználtsága |
| Folyamat száma (szolgáltatás) | A folyamatot |
| Folyamat száma (szolgáltatás) | A saját memória |
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
| .NET CLR memória (szolgáltatás) | Folyamatazonosító |
| .NET CLR memória (szolgáltatás) | # Összesen előjegyzett memória kihasználtsága |
| .NET CLR memória (szolgáltatás) | # Összesen fenntartott memória |
| .NET CLR memória (szolgáltatás) | (Bájt) összes halommemória |
| .NET CLR memória (szolgáltatás) | # 0. generációs gyűjtemények |
| .NET CLR memória (szolgáltatás) | # 1. generációból gyűjtemények |
| .NET CLR memória (szolgáltatás) | # Generációból 2 gyűjtemények |
| .NET CLR memória (szolgáltatás) | % Töltött idő |

### <a name="service-fabrics-custom-performance-counters"></a>A Service Fabric egyéni teljesítményszámlálói

A Service Fabric egyéni teljesítményszámlálóit jelentős mennyiségű állít elő. Ha az SDK-val telepített, látható az átfogó listáját a Windows-számítógépre az alkalmazás teljesítményének figyelése (Start > Teljesítményfigyelő). 

Az alkalmazások üzembe helyezi a fürthöz, ha Reliable Actors használ, adja hozzá a countes `Service Fabric Actor` és `Service Fabric Actor Method` kategóriák (lásd: [Service Fabric megbízható szereplője diagnosztika](service-fabric-reliable-actors-diagnostics.md)).

Ha Reliable Services használatához hasonló módon kell `Service Fabric Service` és `Service Fabric Service Method` kell gyűjtése teljesítményszámláló-kategóriák a teljesítményszámlálók. 

Megbízható gyűjtemények használatakor, azt javasoljuk, hogy a `Avg. Transaction ms/Commit` a a `Service Fabric Transactional Replicator` / tranzakció metrika az átlagos véglegesítési késés gyűjtéséhez.


## <a name="next-steps"></a>Következő lépések

* További információ [platform szintjén az eseménygenerálás](service-fabric-diagnostics-event-generation-infra.md) a Service Fabric
* Teljesítményadatok gyűjtéséhez keresztül [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md)
