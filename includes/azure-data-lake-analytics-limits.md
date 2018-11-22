---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: e3059b5a76a6a9188f79b683bb997bbcf8185ced
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279689"
---
A Data Lake Analytics leegyszerűsíti az elosztott infrastruktúra és az összetett programkód kezelésének bonyolult feladatát. A szolgáltatás az erőforrások dinamikus kiosztásával akár több exabájt adat elemzését is lehetővé teszi. Amikor a feladat befejeződik, automatikusan kivonja az erőforrásokat a használatból, így csak a ténylegesen felhasznált számítási teljesítményért kell fizetnie. Ha növeli vagy csökkenti a tárolt adatok méretét, vagy a felhasznált számítási teljesítményt, nem kell újraírnia a programot. Az alapértelmezés szerinti korlátozásoknak számos egyszerűen kiváltható az előfizetéshez tartozó támogatási szolgálatával. 

| **Erőforrás** | **Alapértelmezett korlát** | **Megjegyzések** |
| --- | --- | --- |
| Egyidejű feladatok maximális száma |20 | |
| Elemzési egység (AU-k) maximális száma fiókonként |250 | Legfeljebb 250 együtt is használhatók az AU-k 20-feladat között.  Forduljon a Microsoft ügyfélszolgálatához a kvótahatár növelését. |
| A feladatok elküldéséhez parancsfájl maximális mérete | 3 MB | |
| ADLA fiókok régiónként és előfizetésenként maximális száma | 5 | Forduljon a Microsoft ügyfélszolgálatához a kvótahatár növelését. |
