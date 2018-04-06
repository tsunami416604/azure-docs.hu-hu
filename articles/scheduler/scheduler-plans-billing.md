---
title: Csomagok és a számlázás az Azure Schedulerrel
description: Csomagok és a számlázás az Azure Schedulerrel
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
ms.openlocfilehash: b25e97b0f0d0b6f63134a774856eb7ec8f77b679
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="plans-and-billing-in-azure-scheduler"></a>Csomagok és a számlázás az Azure Schedulerrel
## <a name="job-collection-plans"></a>Feladat gyűjtési terveket
Feladatgyűjtemények az Azure Scheduler számlázható entitás. Feladatgyűjtemények feladatok száma tartalmazhat, és a három csomagokban – Standard, P10 Premium és P20 prémium –, amelyek folyamata az alábbiakban olvasható származnak.

| **Feladat gyűjtési terv** | **Feladat gyűjteményenként feladatok maximális száma** | **Maximális ismétlődésére** | **Maximális Feladatgyűjteményei előfizetésenként** | **Korlátok** |
|:--- |:--- |:--- |:--- |:--- |
| **Standard** |50 feladatok feladat gyűjteményenként |Percenkénti. Percenként egyszer többször nem hajtható végre feladatok |Egy előfizetés legfeljebb 100 szabványos feladatgyűjteményei engedélyezett |A Feladatütemező szolgáltatás teljes készletét a hozzáférést |
| **P10 Premium** |50 feladatok feladat gyűjteményenként |Percenkénti. Percenként egyszer többször nem hajtható végre feladatok |Egy előfizetés legfeljebb 10 000 P10 prémium feladatgyűjteményei engedélyezett. <a href="mailto:wapteams@microsoft.com">Kapcsolatfelvétel</a> több. |A Feladatütemező szolgáltatás teljes készletét a hozzáférést |
| **P20 prémium** |1000 feladatok feladat gyűjteményenként |Percenkénti. Percenként egyszer többször nem hajtható végre feladatok |Egy előfizetés legfeljebb 10 000 P20 prémium feladatgyűjteményei engedélyezett. <a href="mailto:wapteams@microsoft.com">Kapcsolatfelvétel</a> több. |A Feladatütemező szolgáltatás teljes készletét a hozzáférést |

## <a name="upgrades-and-downgrades-of-job-collection-plans"></a>Frissítések és a feladat gyűjtési terveket Downgrades
Előfordulhat, hogy frissítse vagy visszaminősítését egy feladat gyűjtési terv bármikor a Standard, P10 Premium és P20 prémium csomagok között.

## <a name="billing-and-azure-plans"></a>Számlázási és az Azure-csomagok
Ha több mint 100 szabványos feladatgyűjteményei (szabványos 10 számlázási egységek), majd egy jobb üzletet, hogy minden feladat gyűjtemény a prémium tervének.

Ha egy szabványos feladatgyűjtemény és egy prémium szintű feladatgyűjtemény,-e számlázott egy standard számlázási egység *és* egy prémium szintű számlázási egységet. A Feladatütemező szolgáltatás váltók standard vagy prémium; beállított aktív feladat gyűjtemények száma alapján Ennek a magyarázatát a következő két szakaszokban további.

## <a name="standard-billable-units"></a>Standard számlázható egység
Egy szabványos számlázható egység legfeljebb 10 szabványos feladatgyűjteményei tartalmazhatnak. Mivel a szabványos feladatgyűjtemény feladat gyűjteményenként legfeljebb 50 feladatok is rendelkeznek, egy standard számlázási egységet lehetővé teszi, hogy legfeljebb 500 feladatok – szinte 22 millió feladat végrehajtások havonta legfeljebb egy előfizetést.

Ha szabványos feladatgyűjteményei 1 és 10 között, akkor 1 standard számlázási egység fogjuk számlázni. Ha szabványos feladatgyűjteményei 11 és 20 közötti, hogy 2 standard számlázási egység fogjuk számlázni. Ha 21 és 30 szabványos feladatgyűjteményei között, 3 standard számlázási egység fogjuk számlázni, és így tovább.

## <a name="p10-premium-billable-units"></a>P10 Prémium számlázható egységek
Egy P10 prémium számlázható egység legfeljebb 10 000 P10 prémium feladatgyűjteményei tartalmazhatnak. Mivel egy P10 prémium feladatgyűjtemény feladat gyűjteményenként legfeljebb 50 feladatok is rendelkeznek, egy prémium szintű számlázási egységet lehetővé teszi, hogy legfeljebb 500 000 feladatok – szinte 22 milliárd feladat végrehajtások havonta legfeljebb egy előfizetést.

Ha prémium szintű feladat gyűjtemények 1 és 10 000 között, akkor 1 P10 prémium számlázási egység fogjuk számlázni. Ha 10,001 és 20 000 prémium feladatgyűjteményei között, 2 P10 prémium számlázási egység fogjuk számlázni, és így tovább.

Így P10 prémium feladatgyűjteményei ugyanazt a funkciót látja, mint a szokásos feladatgyűjteményeket, de ár szünet adja meg, abban az esetben, ha az alkalmazás által igényelt feladatgyűjteményei számos.

## <a name="p20-premium-billable-units"></a>P20 Prémium számlázható egységek
P20 prémium számlázható egység legfeljebb 5000 P20 prémium feladatgyűjteményei tartalmazhatnak. Mivel egy P20 prémium feladatgyűjtemény feladat gyűjteményenként legfeljebb 1000 feladatot is rendelkeznek, egy prémium szintű számlázási egység lehetővé teszi, hogy legfeljebb 5,000,000 feladatok – szinte 220 milliárd feladat végrehajtások havonta legfeljebb egy előfizetést.

P20 prémium feladatgyűjteményei P10 prémium feladatgyűjteményeket, ugyanazokat a képességeket biztosít, de támogatja a feladat gyűjteményenként nagyobb számú feladatok és a nagyobb feladatok teljes száma általános mint P10 támogatás lehetővé teszi további méretezhetőséget.

## <a name="billing-and-active-status"></a>Számlázási és aktív állapota
Feladatgyűjtemények mindig aktívak, kivéve, ha a teljes előfizetés számlázási problémák miatt egyes ideiglenes letiltott állapotba állapotba került. Csak annak érdekében, hogy a feladatgyűjtemény nem lesz számlázva, vagy állítsa be a *szabad* terv vagy a feladatgyűjtemény törlése.

Bár előfordulhat, hogy letiltja a feladatokhoz minden esetben a feladatgyűjtemény egyetlen művelettel, az a feladatgyűjtemény számlázási állapota nem változtatható – a feladatgyűjtemény fog *továbbra is* kell fizetni. Hasonlóképpen üres feladatgyűjteményei aktív számít, és lesz terhelve.

## <a name="pricing"></a>Díjszabás
A díjszabás részleteit, lásd: [Feladatütemező árképzési](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="see-also"></a>Lásd még:
 [A Scheduler ismertetése](scheduler-intro.md)

 [Az Azure Scheduler alapfogalmai, terminológiája és entitáshierarchiája](scheduler-concepts-terms.md)

 [Ismerkedés a Scheduler szolgáltatás Azure Portalon való használatával](scheduler-get-started-portal.md)

 [Az Azure Scheduler REST API-jának leírása](https://msdn.microsoft.com/library/mt629143)

 [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)

 [Azure Scheduler – magas fokú rendelkezésre állás és megbízhatóság](scheduler-high-availability-reliability.md)

 [Azure Scheduler – korlátozások, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)

 [Kimenő hitelesítés az Azure Schedulerben](scheduler-outbound-authentication.md)

