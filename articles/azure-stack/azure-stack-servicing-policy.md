---
title: "Házirend karbantartási Azure verem |} Microsoft Docs"
description: "További tudnivalók az Azure verem karbantartási házirend, és hogyan kell fenntartani az integrált rendszer támogatott állapotban."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 498d0cdc3eac25b8efc7339e48381a4d167c0c7b
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-stack-servicing-policy"></a>Az Azure verem karbantartása házirend

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Ez a cikk ismerteti a karbantartási házirend integrált Azure verem rendszerekhez, és hogy a rendszer egy támogatott állapotban kell tennie. 

## <a name="update-package-types"></a>A csomagok frissítése

Két különböző frissítési csomagok integrált rendszerekhez; A Microsoft, és a számítógépgyártó (OEM) hardver gyártójától, például az illesztőprogramok és a belső vezérlőprogram vonatkozó frissítéseket. Ezek a frissítések külön Azure verem frissítési csomagként érkeznek, és önállóan felügyelt.

- **A Microsoft**. A Microsoft a Microsoft a szoftverfrissítési csomagokat a végpont karbantartási életciklusa felelős. A csomagok tartalmazhatnak, a Windows Server legújabb biztonsági frissítéseket, nem biztonsági frissítések és Azure verem szolgáltatás-frissítéseket. Közvetlenül a Microsoft letöltheti ezek frissítési csomagokat.
- **OEM szállító által biztosított hardverfrissítések**. Az Azure verem hardverszállító partnerétől felelősek a végpont karbantartási életciklusa (beleértve az útmutató) a hardverrel kapcsolatos belső vezérlőprogram és illesztőprogram-csomagok frissítése. Emellett Azure verem hardverszállító partnerétől tulajdonosai, és minden szoftver- és a hardver életciklus gazdagépen útmutatást karbantartása. Az OEM forgalmazó futtatja ezeket a saját letöltési helyen-csomagok frissítése.

## <a name="update-package-release-cadence"></a>Frissítési csomag kiadás ütemben történik

Microsoft havi ütemben történik a szoftverfrissítési csomagokat a kibocsátási vár. Azonban akkor lehetséges, hogy több, vagy nincs frissítés kiadásokban hónap. OEM hardverszállítók a frissítéseket igény szerint alapon.

A Microsoft update csomagot a következő elnevezési konvenció segítségével könnyen azonosíthatja a kiadási dátum:

*MajorProductVersion.MinorProductVersion.YYMMDD.BuildNumber*

Például egy Microsoft szoftverfrissítés 2017. június 15-én kiadott kellene "1.0.170615.1" verzió.

## <a name="keep-your-system-under-support"></a>A rendszer a támogatás

A rendszer a támogatás igénybevételéhez mindig az adott időintervallumon belül frissített Azure verem. A házirend a Microsoft szoftverfrissítések halasztás három hónappal. Ha a rendszer három hónapnál elavult, akkor nem megfelelő most számít. A rendszer frissítenie kell a legkisebb támogatott verzió a fiókrendszergazdától támogatást. 

A Microsoft a szoftverfrissítési csomagokat nem összesítő, és a korábbi csomag előfeltételként szükséges. Ha úgy dönt, hogy egy vagy több frissítés késleltetésének, fontolja meg a teljes futásidejű, ha le szeretné kérdezni a legújabb verzióra.

A következő táblázatban példa frissítési csomag kiadások, az Előfeltételek és a támogatott legalacsonyabb verziójú, amely a rendszer el kell érnie támogatási fenntartásához. A tábla az eredeti kiadásának integrált Azure verem rendszerek (build 1708), szeptember 2017 első frissítési csomag kiadásban (1709) alapul. 

| Legfrissebb csomagot (*példa*) | Előfeltétel | Legkisebb támogatott verzió |
| -- | -- | -- |
| 1709 | Build 1708 | N/A |
| 1710 | 1709 | N/A |
| 1711 | 1710 | N/A |
| 1712 | 1711 | 1709 |
| 1801 | 1712 | 1710 |
| 1802 | 1801 | 1711 |
| 1803 | 1802 | 1712 |
| 1804 | 1803 | 1801 |
| | | 

## <a name="next-steps"></a>Következő lépések

- [Azure-készletben frissítések kezelése](azure-stack-updates.md)


