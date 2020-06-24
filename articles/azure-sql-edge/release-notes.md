---
title: Az Azure SQL Edge kibocsátási megjegyzései
description: A kibocsátási megjegyzések részletesen ismertetik az Azure SQL Edge-lemezképek újdonságait vagy változásait
keywords: kibocsátási megjegyzések – SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 06/05/2020
ms.openlocfilehash: 7238e2d7cbcb26a7b45ae9d7db52a0826abe0754
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84897981"
---
# <a name="azure-sql-edge-release-notes"></a>Az Azure SQL Edge kibocsátási megjegyzései 

Ez a cikk ismerteti az újdonságokat és az Azure SQL Edge minden új buildjét.

## <a name="ctp-20"></a>CTP 2,0 
### <a name="sql-engine-build-number---15020001401"></a>SQL Engine Build száma – 15.0.2000.1401
### <a name="whats-new"></a>Újdonságok
1.  A terméknév frissítve az "Azure SQL Edge" névre
2.  Date_bucket függvény

    i.  A dátum, idő és DateTime típus támogatása
3.  ELŐREJELZÉS a ONNX
    
    i.  ONNX szükséges FUTTATÓKÖRNYEZETi paraméter 
    
4.  TSQL streaming-támogatás (korlátozott előzetes verzió) 
 
### <a name="known-issues"></a>Ismert problémák

1. <b>Probléma:</b> Lehetséges hibák a dacpac alkalmazás indításakor az időzítési probléma miatt.

    <b>Áthidaló megoldás:</b> SQL Server vagy tároló újraindítása újra megpróbálja alkalmazni a dacpac, és ki kell javítania a problémát
### <a name="request-support"></a>Támogatás kérése
1. A támogatást a [támogatási oldalon](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)kérheti le.

4. Győződjön meg arról, hogy a következő mezők vannak kiválasztva: 
    * Probléma típusa – technikai 
    * Szolgáltatás – IoT Edge
    * Probléma típusa – a probléma egy IoT Edge modulhoz kapcsolódik
    * Probléma altípusa – Azure SQL Edge

   ![Példa támogatási jegyre](media/get-support/support-ticket.png)

## <a name="ctp-15"></a>CTP 1,5
### <a name="sql-engine-build-number---15020001331"></a>SQL Engine Build száma – 15.0.2000.1331
### <a name="whats-new"></a>Újdonságok
1. Date_bucket függvény
    
    i. DateTimeOffset-típus támogatása
2. ELŐREJELZÉS ONNX-modellekkel

    i. nvarchar-támogatás
 
## <a name="ctp-14"></a>CTP 1,4
### <a name="sql-engine-build-number---15020001247"></a>SQL Engine Build száma – 15.0.2000.1247
### <a name="whats-new"></a>Újdonságok
1.  ELŐREJELZÉS ONNX-modellekkel
 
    i.  Varchar-támogatás
    
    ii. Áttelepítés a ONNX Runtime 1,0-es verziójára 
2.  Szolgáltatások támogatása – a következő funkciók engedélyezettek:

    i.   CDC-támogatás

    ii.  Előzmények táblázat tömörítéssel

    iii. Nagyobb méretezési tényező a naplóban olvasás előtt

    iv.  Batch Mode ES szűrő pushdown

    v.   Előre beolvasott optimalizálások
 
## <a name="ctp-13"></a>CTP 1,3
### <a name="sql-engine-build-number---15020001147"></a>SQL Engine Build száma – 15.0.2000.1147
### <a name="whats-new"></a>Újdonságok
1. Azure IOT-portál üzembe helyezése 

    i.   Az AMD64-és ARM-lemezképek üzembe helyezésének támogatása

    ii.  A folyamatos átviteli feladatok létrehozásának támogatása

    iii. Dacpac üzembe helyezése
2. ELŐREJELZÉS ONNX-modellekkel

    i. Numerikus típusú támogatás
3. Szolgáltatások támogatása – a következő funkciók engedélyezettek:

    i.  Pushdown összesítése az oszlopok tárolására

    ii. Merry-go-round vizsgálatok
4. A helyigény és a memóriahasználat csökkentése
