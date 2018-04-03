---
title: Házirend karbantartási Azure verem |} Microsoft Docs
description: További tudnivalók az Azure verem karbantartási házirend, és hogyan kell fenntartani az integrált rendszer támogatott állapotban.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mabrigg
ms.openlocfilehash: 2c10dcf185c62f3672be80ad2e3d049eae82fe6b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-servicing-policy"></a>Az Azure verem karbantartása házirend
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
Továbbra is támogatja, az Azure Alkalmazásveremben üzembe kell tartani, aktuális. Halasztás frissítések házirendje, hogy Azure verem továbbra is támogatott, csak a legutóbb kiadott frissített verzió vagy futtassa a két előző fő frissítési verziók egyikét.  Gyorsjavítások nem tekinthetők fő frissítési verziók.  Ha az Azure-verem felhő mögött *kettőnél több frissítések*, nem megfelelő minősül, és a frissíteniük kell legalább a legrégebbi támogatott verzió a fiókrendszergazdától támogatást. 

Például ha a rendelkezésre álló legfrissebb frissített verzióra 1805, és az előző két frissítési csomagokat volt 1804 és 1803, 1803 és a 1804 maradnak támogatása. 1802 azonban nem támogatott. A házirend igaz, ha az egy vagy két nincs felszabadítás. Például ha a jelenlegi kiadásban 1805, és nem 1804 kiadás történt, az előző két frissítési csomagokat 1803 és 1802 marad támogatására.

A Microsoft a szoftverfrissítési csomagokat nem összesítő és a korábbi csomag előfeltételként igényel. Ha úgy dönt, hogy egy vagy több frissítés késleltetésének, fontolja meg a teljes futásidejű, ha le szeretné kérdezni a legújabb verzióra. 

A következő táblázatban példa frissítési csomag kiadások, az Előfeltételek és a támogatott legalacsonyabb verziójú, amely a rendszer el kell érnie támogatási fenntartásához. A tábla az eredeti kiadásának integrált Azure verem rendszerek (build 1708), szeptember 2017 első frissítési csomag kiadásban (1709) alapul. 

| Legfrissebb csomagot (*példa*) | Előfeltétel | Legkisebb támogatott verzió |
| -- | -- | -- |
| 1710 | 1709 | – |
| 1711 | 1710 | 1709 |
| 1712 | 1711 | 1710 |
| 1802 | 1712 | 1711 |
| 1803 | 1802 | 1712 |
| 1804 | 1803 | 1802 |
| 1805 | 1804 | 1803 |
| | | 
Az előző táblázatban szereplő nincs nincs 1801-es verzió.

## <a name="next-steps"></a>További lépések

- [Azure-készletben frissítések kezelése](azure-stack-updates.md)


