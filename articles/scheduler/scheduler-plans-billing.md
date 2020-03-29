---
title: Csomagok és számlázás
description: Az Azure Scheduler csomagjaira és számlázására vonatkozó információk
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: e821036ff4ddb5a9786bc4f4537bb81539ab2c87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898473"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Csomagok és számlázás az Azure Scheduler számára

> [!IMPORTANT]
> [Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) felváltja az Azure Scheduler programot, [amelyet megszüntetnek.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Ha továbbra is szeretne dolgozni az Ütemezőben beállított feladatokkal, a lehető leghamarabb [telepítse át az Azure Logic Apps-alkalmazásokba.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> Az Ütemező már nem érhető el az Azure Portalon, de a [REST API](/rest/api/scheduler) és az [Azure Scheduler PowerShell-parancsmagok](scheduler-powershell-reference.md) jelenleg elérhetők maradnak, így kezelheti a feladatokat és a feladatgyűjteményeket.

## <a name="job-collection-plans"></a>Feladatgyűjtési tervek

Az Azure Scheduler egy feladatgyűjtemény egy adott számú feladatot tartalmaz. A feladatgyűjtemény a számlázható entitás, és a Standard, P10 Premium és P20 Premium csomagokban jelenik meg, amelyek et itt ismertetett: 

| Feladatgyűjtési terv | Maximális feladatok gyűjteményenként | Maximális ismétlődés | Feladatgyűjtemények maximális kimerítése előfizetésenként | Korlátok | 
|:--- |:--- |:--- |:--- |:--- |
| **Standard** | 50 feladat gyűjteményenként | Percenként egy. A feladatok nem futtathatók percenként egynél gyakrabban. | Minden Azure-előfizetés legfeljebb 100 standard feladatgyűjteményt rendelkezhet. | Hozzáférés az Ütemező teljes szolgáltatáskészletéhez | 
| **P10 Prémium** | 50 feladat gyűjteményenként | Percenként egy. A feladatok nem futtathatók percenként egynél gyakrabban. | Minden Azure-előfizetés legfeljebb 10 000 P10 prémium szintű feladatgyűjteményt kaphat. További gyűjtemények, <a href="mailto:wapteams@microsoft.com">Lépjen kapcsolatba velünk</a>. | Hozzáférés az Ütemező teljes szolgáltatáskészletéhez |
| **P20 Prémium** | 1000 feladat gyűjteményenként | Percenként egy. A feladatok nem futtathatók percenként egynél gyakrabban. | Minden Azure-előfizetés legfeljebb 5000 P20 Prémium szintű feladatgyűjteményt kaphat. További gyűjtemények, <a href="mailto:wapteams@microsoft.com">Lépjen kapcsolatba velünk</a>. | Hozzáférés az Ütemező teljes szolgáltatáskészletéhez |
|||||| 

## <a name="pricing"></a>Díjszabás

Az árképzésrészleteiről az [Ütemező díjszabása](https://azure.microsoft.com/pricing/details/scheduler/)témakörben talál.

## <a name="upgrade-or-downgrade-plans"></a>Csomagok frissítése vagy visszaminősítése

Bármikor frissítheti vagy visszaminősítheti a feladatgyűjtési tervet a Standard, a P10 Premium és a P20 Premium csomagokban.

## <a name="active-status-and-billing"></a>Aktív állapot és számlázás

A feladatgyűjtemények mindig aktívak, kivéve, ha a teljes Azure-előfizetés számlázási problémák miatt ideiglenesen letiltott állapotba kerül. És bár egyetlen műveleten keresztül letilthatja a feladatgyűjtemény összes feladatát, ez a művelet nem módosítja a feladatgyűjtemény számlázási állapotát, így a feladatgyűjtemény *továbbra* is számlázásra kerül. Az üres feladatgyűjtemények aktívnak számítanak, és számlázásra kerülnek.

Annak érdekében, hogy a feladatgyűjtemény nincs számlázva, törölnie kell a feladatgyűjteményt.

## <a name="standard-billable-units"></a>Szabványos számlázható egységek

Egy szabványos számlázható egység legfeljebb 10 standard feladatgyűjteményt rendelkezhet. Mivel egy standard feladatgyűjtemény gyűjteményenként legfeljebb 50 feladattal rendelkezhet, egy standard számlázási egység lehetővé teszi, hogy az Azure-előfizetés akár 500 feladattal, vagy havonta közel 22 *millió* feladatvégrehajtással rendelkezhet. Ez a lista bemutatja, hogyan kell fizetnie a standard feladatgyűjtemények különböző számai alapján:

* Ha 1 és 10 standard feladatgyűjtemény között van, akkor egy szabványos számlázási egységért kell fizetnie. 

* Ha 11 és 20 standard feladatgyűjtemény között van, két szabványos számlázási egységért kell fizetnie. 

* Ha 21 és 30 standard feladatgyűjtemény között van, három szabványos számlázási egységért kell fizetnie, és így tovább.

## <a name="p10-premium-billable-units"></a>P10 prémium számlázható egységek

A P10 prémium számlázható egység legfeljebb 10 000 P10 prémium feladatgyűjteményt használhat. Mivel egy P10 Prémium szintű feladatgyűjtemény gyűjteményenként legfeljebb 50 feladattal rendelkezhet, egy P10 prémium szintű számlázási egység lehetővé teszi, hogy az Azure-előfizetés akár 500 000 feladattal, vagy havonta közel 22 *milliárd* feladatvégrehajtással rendelkezhet. 

A P10 prémium szintű feladatgyűjtemények ugyanazokat a képességeket biztosítják, mint a standard feladatgyűjtemények, de ártörést kínálnak a sok feladatgyűjteményt igénylő és nagyobb méretezhetőséget igénylő alkalmazások számára. Ez a lista bemutatja, hogyan kell fizetnie a P10 Prémium feladatgyűjtemények különböző számai alapján:

* Ha 1 és 10 000 P10 prémium szintű feladatgyűjtemény között van, akkor egy P10 prémium szintű számlázási egységért kell fizetnie. 

* Ha 10 001 és 20 000 P10 prémium szintű feladatgyűjtemény között van, 2 P10 prémium számlázási egységet számlázunk, és így tovább.

## <a name="p20-premium-billable-units"></a>P20 prémium számlázható egységek

A P20 prémium számlázható egység legfeljebb 5000 P20 prémium feladatgyűjteményt használhat. Mivel egy P20 Prémium szintű feladatgyűjtemény feladatgyűjteményenként akár 1000 feladattal is rendelkezhet, egy P20 prémium szintű számlázási egység lehetővé teszi, hogy az Azure-előfizetés akár 5 000 000 feladattal, vagy havonta közel 220 *milliárd* feladatvégrehajtással rendelkezhet.

A P20 Prémium feladatgyűjtemények ugyanazokat a képességeket biztosítják, mint a P10 Premium feladatgyűjtemények, de gyűjteményenként több feladatot és több teljes számú feladatot is támogatnak, mint a P10 Premium, így nagyobb méretezhetőséget biztosítanak.

## <a name="plan-comparison"></a>Terv összehasonlítása

* Ha több mint 100 standard feladatgyűjteménysel rendelkezik (10 standard számlázási egység), akkor jobb ajánlatot kaphat, ha az összes feladatgyűjteményt prémium szintű csomagban kapja meg.

* Ha egy standard feladatgyűjteményés egy prémium szintű feladatgyűjtemény, akkor egy standard számlázási egység *és* egy prémium szintű számlázási egység számlázása.

  Az Ütemező szolgáltatás számlái a normál vagy prémium aktív feladatgyűjtemények száma alapján.

## <a name="next-steps"></a>További lépések

* [Az Azure Scheduler alapfogalmai, entitáshierarchiája és terminológiája](scheduler-concepts-terms.md)
* [Azure Scheduler – korlátozások, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)