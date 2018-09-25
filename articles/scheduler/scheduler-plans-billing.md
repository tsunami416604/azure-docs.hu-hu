---
title: Csomagok és számlázás – Azure Scheduler szolgáltatásával
description: Csomagok és számlázás az Azure Scheduler ismertetése
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 13a2be8c-dc14-46cc-ab7d-5075bfd4d724
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 74f13628b62d53a84b4d19255a30a6bc4a7367ec
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974289"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Csomagok és számlázás az Azure Scheduler

> [!IMPORTANT]
> [Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) kivezetjük, az Azure Scheduler lecseréli. Feladatok ütemezése [helyette próbálkozzon az Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

## <a name="job-collection-plans"></a>Feladat gyűjtési terveket

Az Azure Scheduler feladatgyűjtemények feladatok egy adott számot tartalmaz. A feladatgyűjtemény a számlázható entitás, és Standard, P10 prémium és P20 prémium szintű csomagokat, amelyek leírását itt érhető el: 

| Feladatgyűjtemény | Maximális feladatok száma gyűjteményenként | Ismétlődési maximum | Feladatgyűjtemények maximális száma előfizetésenként | Korlátok | 
|:--- |:--- |:--- |:--- |:--- |
| **Standard** | 50 feladatok száma gyűjteményenként | Egy tranzakció percenként. Nem futtatható, mint egy perc alatt egy gyakrabban feladatok. | Azure-előfizetések Standard akár 100 feladatgyűjtemény rendelkezhet. | A Scheduler teljes funkciókészlete a hozzáférést | 
| **P10 prémium** | 50 feladatok száma gyűjteményenként | Egy tranzakció percenként. Nem futtatható, mint egy perc alatt egy gyakrabban feladatok. | Minden Azure-előfizetés legfeljebb 10 000 P10 prémium feladatgyűjtemény rendelkezhet. A további gyűjteményekhez <a href="mailto:wapteams@microsoft.com">lépjen kapcsolatba velünk</a>. | A Scheduler teljes funkciókészlete a hozzáférést |
| **P20 prémium** | 1000 feladatok száma gyűjteményenként | Egy tranzakció percenként. Nem futtatható, mint egy perc alatt egy gyakrabban feladatok. | Minden Azure-előfizetés legfeljebb 5000 P20 prémium feladatgyűjtemény rendelkezhet. A további gyűjteményekhez <a href="mailto:wapteams@microsoft.com">lépjen kapcsolatba velünk</a>. | A Scheduler teljes funkciókészlete a hozzáférést |
|||||| 

## <a name="pricing"></a>Díjszabás

A díjszabás részleteiért lásd: [Scheduler díjszabása](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="upgrade-or-downgrade-plans"></a>- És Visszaléptetés a tervek

Bármikor akkor lehetséges- és Visszaléptetés a feladatgyűjtemény között a Standard, P10 prémium és P20 prémium szintű csomag.

## <a name="active-status-and-billing"></a>Aktív állapot és számlázás

Feladatgyűjtemények mindig aktívak, kivéve, ha a teljes Azure-előfizetés számlázási problémák miatt ideiglenesen letiltott állapotba kerül. Bár minden feladatok egy egyetlen műveletben keresztül bármikor leállíthatja, így a feladatgyűjtemény le van ez a művelet nem változik a feladatgyűjtemény számlázási állapotát, és *továbbra is* számlázzuk. Üres feladatgyűjtemények aktív tekinti, és számítunk fel díjat.

Ahhoz, hogy a feladatgyűjtemény nem számlázzuk, törölnie kell a feladatgyűjteményt.

## <a name="standard-billable-units"></a>Standard szintű elszámolási egységek

A standard számlázható egység akár 10 Standard feladatgyűjtemény is rendelkezhet. Egy Standard feladatgyűjtemény legfeljebb 50 feladatok száma gyűjteményenként is rendelkezik, mivel egy standard szintű elszámolási egység lehetővé teszi, hogy az Azure-előfizetés rendelkezik, 500 feladatok, vagy akár szinte 22-es *millió* feladat-végrehajtások havonta. Ez a lista azt ismerteti, hogyan számlázzuk Standard feladatgyűjtemény különböző számú alapján:

* 1 és 10 Standard feladatgyűjtemény között van, ha egy standard szintű elszámolási egység díjköteles. 

* Ha 11. és 20 Standard feladatgyűjtemény között, két standard szintű elszámolási egység díjköteles. 

* Ha 21. és 30 Standard feladatgyűjtemény között van, akkor három standard szintű elszámolási egység díjköteles, és így tovább.

## <a name="p10-premium-billable-units"></a>P10 prémium szintű elszámolási egységek

Egy P10 premium számlázható egység legfeljebb 10 000 P10 prémium feladatgyűjtemény is rendelkezhet. Mivel a P10 prémium feladatgyűjtemény legfeljebb 50 feladatok száma gyűjteményenként is rendelkezik, egy P10 prémium szintű elszámolási egység lehetővé teszi, hogy az Azure-előfizetés rendelkezik, 500 000 feladatok, vagy akár szinte 22-es *milliárd* feladat-végrehajtások havonta. 

P10 prémium feladatgyűjtemény ugyanazokat a lehetőségeket, mint a Standard feladatgyűjtemény biztosítanak, de az alkalmazásokhoz, melyek sok feladatgyűjtemények igényelnek, és lehetővé teszi további díj szünet kínálnak. Ez a lista azt ismerteti, hogyan számlázás P10 prémium feladatgyűjtemény különböző számú alapján:

* P10 prémium feladatgyűjtemények száma 1 és 10 000 között van, ha egy P10 prémium szintű elszámolási egység díjköteles. 

* Ha 10,001 és 20 000 P10 prémium feladatgyűjtemény között van, 2 P10 prémium szintű elszámolási egység díjköteles, és így tovább.

## <a name="p20-premium-billable-units"></a>P20 prémium szintű elszámolási egységek

Prémium szintű P20 számlázható egység legfeljebb 5 000 P20 prémium feladatgyűjtemény is rendelkezhet. A P20 prémium feladatgyűjtemény feladat gyűjteményenként legfeljebb 1000 feladatok is rendelkezik, mivel egy P20 prémium szintű elszámolási egység lehetővé teszi, hogy az Azure-előfizetés rendelkezik 5,000,000 feladatok, vagy akár szinte 220 *milliárd* feladat-végrehajtások havonta.

P20 prémium feladatgyűjtemény, P10 prémium feladatgyűjtemény ugyanazokat a képességeket biztosítanak, de nagyobb számú feladatok száma gyűjteményenként és a egy nagyobb feladatok teljes száma általános P10 prémium, további méretezhetőséget is támogatja.

## <a name="plan-comparison"></a>Csomagok összehasonlítása

* Ha több mint 100, Standard feladatgyűjtemény (10 standard szintű elszámolási egység), a prémium szintű csomagban lévő összes feladatgyűjtemények kellene beolvasása jobb üzletet.

* Ha egy Standard feladatgyűjtemény és a egy prémium feladatgyűjtemény van, akkor egy standard szintű elszámolási egység díjköteles *és* egy prémium szintű elszámolási egység.

  A Scheduler szolgáltatás számlázása a normál vagy prémium szintű active feladatgyűjtemények száma alapján.

## <a name="see-also"></a>Lásd még

* [Mi az Azure Scheduler?](scheduler-intro.md)
* [Az Azure Scheduler alapfogalmai, terminológiája és entitáshierarchiája](scheduler-concepts-terms.md)
* [Azure Scheduler – korlátozások, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)