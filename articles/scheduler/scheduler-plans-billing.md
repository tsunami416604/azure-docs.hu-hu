---
title: Csomagok és számlázás az Azure Schedulerben
description: Csomagok és számlázás az Azure Schedulerben
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 13a2be8c-dc14-46cc-ab7d-5075bfd4d724
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 03f335634b7ce1fe4aa6251d6ec21922ed9b84c8
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37887487"
---
# <a name="plans-and-billing-in-azure-scheduler"></a>Csomagok és számlázás az Azure Schedulerben
## <a name="job-collection-plans"></a>Feladat gyűjtési terveket
Feladatgyűjtemények az Azure Schedulerben a számlázható entitás. Feladatgyűjtemények feladatok számos tartalmaznak, és a következő három csomagok – Standard, P10 prémium és P20 prémium szintűre –, amelyek leírása alább.

| **Feladatgyűjtemény** | **Feladatok száma gyűjteményenként feladatok maximális száma** | **Ismétlődési maximum** | **Feladatgyűjtemények maximális száma előfizetésenként** | **Korlátok** |
|:--- |:--- |:--- |:--- |:--- |
| **Standard** |50 feladatok feladat gyűjteményenként |Percenként egyszer. Percenként egyszer többször nem hajtható végre feladatok |Egy előfizető legfeljebb 100 standard feladatgyűjtemény |A Scheduler teljes funkciókészlete a hozzáférést |
| **P10 prémium** |50 feladatok feladat gyűjteményenként |Percenként egyszer. Percenként egyszer többször nem hajtható végre feladatok |Egy előfizető legfeljebb 10 000 P10 prémium feladatgyűjtemény. <a href="mailto:wapteams@microsoft.com">Lépjen kapcsolatba velünk</a> további. |A Scheduler teljes funkciókészlete a hozzáférést |
| **P20 prémium** |1000 feladatok feladat gyűjteményenként |Percenként egyszer. Percenként egyszer többször nem hajtható végre feladatok |Egy előfizető legfeljebb 10 000 P20 prémium feladatgyűjtemény. <a href="mailto:wapteams@microsoft.com">Lépjen kapcsolatba velünk</a> további. |A Scheduler teljes funkciókészlete a hozzáférést |

## <a name="upgrades-and-downgrades-of-job-collection-plans"></a>A feladat gyűjtési terveket előfizetések
Előfordulhat, hogy bármelyik irányba változtathatja a feladatgyűjtemény bármikor között a Standard, P10 prémium és P20 prémium szintű csomag.

## <a name="billing-and-azure-plans"></a>Számlázással és az Azure-csomagok
Ha több mint 100, standard feladatgyűjtemény (10 standard szintű elszámolási egység) rendelkezik, majd, jobb üzletet, szeretné, hogy minden feladatgyűjtemények a prémium szintű csomag.

Ha egy standard feladatgyűjtemény és a egy prémium feladatgyűjtemény,-e számlázott egy standard szintű elszámolási egység *és* egy prémium szintű elszámolási egység. A Scheduler szolgáltatás számlázása a standard vagy prémium szintű; beállított egy aktív feladatgyűjtemény száma alapján a további a következő két szakasz ennek a magyarázatát.

## <a name="standard-billable-units"></a>Standard szintű elszámolási egységek
A standard számlázható egység akár 10 standard feladatgyűjtemény is tartalmazhat. Egy standard feladatgyűjtemény feladat gyűjteményenként legfeljebb 50 feladatok is rendelkezik, mivel egy standard szintű elszámolási egység segítségével az előfizetés legfeljebb 500 feladatokat – akár szinte 22 millió feladatfuttatás havonta.

1 és 10 standard feladatgyűjtemény között van, ha esetén egy standard szintű elszámolási egység után kell díjat. Ha 11. és 20 standard feladatgyűjtemény között van, akkor számítunk két standard szintű elszámolási egység. 21. és 30 standard feladatgyűjtemény között van, ha standard szintű elszámolási egység három számítunk, és így tovább.

## <a name="p10-premium-billable-units"></a>P10 Prémium szintű elszámolási egységek
Egy P10 premium számlázható egység legfeljebb 10 000 P10 prémium feladatgyűjtemény is tartalmazhat. Egy prémium szintű elszámolási egység egy P10 prémium feladatgyűjtemény feladat gyűjteményenként legfeljebb 50 feladatok is rendelkezik, mivel segítségével az előfizetés legfeljebb 500 000 feladatokat – szinte 22-es milliárd feladatfuttatás havonta legfeljebb.

1 és 10 000 prémium feladatgyűjtemény között van, akkor lesz kell fizetnie egy P10 prémium szintű elszámolási egység. Ha 10,001 és 20 000 prémium feladatgyűjtemény között van, díját fogjuk számlázni 2 P10 prémium szintű elszámolási egység, és így tovább.

Így P10 prémium feladatgyűjtemény a standard feladatgyűjtemény ugyanazokkal a funkciókkal rendelkezik, de abban az esetben, ha az alkalmazás számos feladatgyűjtemények igényel, adjon meg egy árváltozás.

## <a name="p20-premium-billable-units"></a>P20 Prémium szintű elszámolási egységek
Prémium szintű P20 számlázható egység legfeljebb 5 000 P20 prémium feladatgyűjtemény is tartalmazhat. Mivel a prémium szintű P20-as feladatgyűjtemények feladat gyűjteményenként legfeljebb 1000 feladatok is rendelkezik, egy prémium szintű elszámolási egység segítségével az előfizetés legfeljebb 5,000,000 feladatokat – akár szinte 220 milliárd feladatfuttatás havonta.

P20 prémium feladatgyűjtemény, P10 prémium feladatgyűjtemény ugyanazokat a képességeket biztosítanak, de feladat gyűjteményenként egy nagyobb számú feladatok és a egy nagyobb feladatok teljes száma általános P10 prémium, lehetővé téve, hogy nagyobb méretezhetőséget is jelent, mint is támogatja.

## <a name="billing-and-active-status"></a>Számlázási és aktív állapot
Feladatgyűjtemények mindig aktívak, kivéve, ha a teljes előfizetés számlázási problémák miatt bizonyos ideiglenes letiltott állapotba került. Győződjön meg arról, hogy nem kell fizetniük a feladatgyűjtemény csak úgy, hogy a feladatgyűjtemény törlése.

Bár előfordulhat, hogy letiltja a feladatokhoz minden esetben egyetlen művelettel feladatgyűjtemények, nem változtatja meg a feladatgyűjtemény számlázási állapota – a feladatgyűjtemény fog *továbbra is* számolunk. Hasonlóképpen üres feladatgyűjtemények aktív számít, és számítjuk fel.

## <a name="pricing"></a>Díjszabás
A díjszabás részleteiért lásd: [Scheduler díjszabása](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="see-also"></a>Lásd még:
 [A Scheduler ismertetése](scheduler-intro.md)

 [Az Azure Scheduler alapfogalmai, terminológiája és entitáshierarchiája](scheduler-concepts-terms.md)

 [Ismerkedés a Scheduler szolgáltatás Azure Portalon való használatával](scheduler-get-started-portal.md)

 [Az Azure Scheduler REST API-jának leírása](https://msdn.microsoft.com/library/mt629143)

 [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)

 [Azure Scheduler – magas fokú rendelkezésre állás és megbízhatóság](scheduler-high-availability-reliability.md)

 [Azure Scheduler – korlátozások, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)

 [Kimenő hitelesítés az Azure Schedulerben](scheduler-outbound-authentication.md)

