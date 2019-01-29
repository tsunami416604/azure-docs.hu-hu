---
title: Az Azure Service Fabric teljesítményének figyelése |} A Microsoft Docs
description: Tudnivalók a teljesítményszámlálók a monitorozást és diagnosztikát az Azure Service Fabric-fürtöket.
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
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 114d71227e3cf3d0f235fc63135534b1030d4adc
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55188339"
---
# <a name="performance-metrics"></a>Teljesítmény-mérőszámok

Metrikák be kell tudni, hogy a fürt, valamint a benne lévő futó alkalmazások teljesítményét. A Service Fabric-fürtök esetén javasoljuk, hogy az alábbi teljesítményszámlálók gyűjtése.

## <a name="nodes"></a>Csomópontok

A gépek a fürtben fontolja meg a következő teljesítményszámlálók adatait szeretné jobban megismerni a terhelés az összes olyan számítógépen, és győződjön meg arról, döntéseket hozhat a megfelelő fürtméretezés gyűjtése.

| Teljesítményszámláló-kategóriája | Számláló neve |
| --- | --- |
| Logikai lemez | Logikai lemez – szabad terület |
| Fizikai lemez (lemezenként) | Átl. Olvasási Lemezvárólista hossza |
| Fizikai lemez (lemezenként) | Átl. Írási Lemezvárólista hossza |
| Fizikai lemez (lemezenként) | Átl. Lemez mp/Olvasás |
| Fizikai lemez (lemezenként) | Átl. Lemez mp/írás |
| Fizikai lemez (lemezenként) | Lemezolvasások/mp |
| Fizikai lemez (lemezenként) | Lemezolvasási sebesség (bájt/s) |
| Fizikai lemez (lemezenként) | Lemezírások/mp |
| Fizikai lemez (lemezenként) | Lemezírási sebesség (bájt/s) |
| Memory (Memória) | Rendelkezésre álló memória |
| PagingFile | % Usage |
| Processor(total) | Processzoridő |
| Folyamat (szolgáltatásonként) | Processzoridő |
| Folyamat (szolgáltatásonként) | Folyamat azonosítója |
| Folyamat (szolgáltatásonként) | Saját [nem megosztható] memória (bájt) |
| Folyamat (szolgáltatásonként) | Szálak száma |
| Folyamat (szolgáltatásonként) | Virtuális memória |
| Folyamat (szolgáltatásonként) | Pracovní Sada |
| Folyamat (szolgáltatásonként) | Munkakészlet – magán |
| Hálózati Interface(all-instances) | Bájt recd |
| Hálózati Interface(all-instances) | Küldött bájtok |
| Hálózati Interface(all-instances) | Összes bájt |
| Hálózati Interface(all-instances) | Kimeneti várólista hossza |
| Hálózati Interface(all-instances) | Elvetett kimenő csomagok |
| Hálózati Interface(all-instances) | Az elvetett fogadott csomagok |
| Hálózati Interface(all-instances) | Hibás kimenő csomagok |
| Hálózati Interface(all-instances) | Beérkezett csomagok hibák |

## <a name="net-applications-and-services"></a>.NET-alkalmazások és szolgáltatások

Az alábbi számlálókat gyűjtése, ha a fürtön helyezi üzembe a .NET-szolgáltatásokat. 

| Teljesítményszámláló-kategóriája | Számláló neve |
| --- | --- |
| .NET CLR memória (szolgáltatásonként) | Folyamat azonosítója |
| .NET CLR memória (szolgáltatásonként) | # Összesen előjegyzett memória kihasználtsága |
| .NET CLR memória (szolgáltatásonként) | # Összes lefoglalt bájtok |
| .NET CLR memória (szolgáltatásonként) | Bájtok száma az összes halommemória |
| .NET CLR memória (szolgáltatásonként) | Nagy objektumok halommemóriájának mérete |
| .NET CLR memória (szolgáltatásonként) | # Globális Katalógus kezeli |
| .NET CLR memória (szolgáltatásonként) | # Generációs gyűjtemények 0 |
| .NET CLR memória (szolgáltatásonként) | # Generációs gyűjtemények 1 |
| .NET CLR memória (szolgáltatásonként) | # Generációs gyűjtemények 2 |
| .NET CLR memória (szolgáltatásonként) | Szemétgyűjtéssel töltött idő %-át |

### <a name="service-fabrics-custom-performance-counters"></a>A Service Fabric – teljesítményszámlálók egyéni

A Service Fabric hoz létre egyéni teljesítményszámlálók jelentős mennyiségű. Ha az SDK-t, látható az átfogó listának a Windows-gépen az alkalmazás teljesítményének figyelése (Start > Teljesítményfigyelő). 

Az alkalmazások a telepíti, akkor a fürthöz, ha használja a Reliable Actors, adjon hozzá számlálókat `Service Fabric Actor` és `Service Fabric Actor Method` kategóriák (lásd: [Service Fabric Reliable Actors diagnosztikai](service-fabric-reliable-actors-diagnostics.md)).

A Reliable Services vagy a szolgáltatás távelérésének lehetővé tétele, hogy hasonló módon van-e `Service Fabric Service` és `Service Fabric Service Method` kategóriák, hogy meg kell gyűjteni számlálókat, lásd: számláló [figyelés szolgáltatás távelérésének lehetővé tétele a](service-fabric-reliable-serviceremoting-diagnostics.md) és [megbízható teljesítményszámlálók Services](service-fabric-reliable-services-diagnostics.md#performance-counters). 

A Reliable Collections használata, ha ajánlott felvenni a `Avg. Transaction ms/Commit` származó a `Service Fabric Transactional Replicator` a véglegesítés átlagos késés tranzakció metrikánként gyűjtéséhez.


## <a name="next-steps"></a>További lépések

* Tudjon meg többet [események létrehozása a platform szintjén](service-fabric-diagnostics-event-generation-infra.md) a Service Fabricben
* Teljesítményadatok gyűjtéséhez keresztül [Log Analytics-ügynököket](service-fabric-diagnostics-oms-agent.md)
