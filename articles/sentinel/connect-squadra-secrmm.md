---
title: A Squadra Technologies secRMM-adatainak összekapcsolása az Azure Sentinelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan kapcsolhatja össze a Squadra Technologies secRMM-adatait az Azure Sentinelhez.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: d904e51321870fb4b61a237c23e425034b76dc0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588110"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>A Squadra Technologies secRMM-adatainak összekapcsolása az Azure Sentinelhez 

> [!IMPORTANT]
> A Squadra Technologies security cserélhető media manager (secRMM) adatösszekötő az Azure Sentinel jelenleg nyilvános előzetes verzióban.
> Ez a szolgáltatás szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)


A Squadra Technologies secRMM-összekötő lehetővé teszi, hogy egyszerűen csatlakoztassa a Squadra Technologies secRMM biztonsági megoldás naplók az Azure Sentinel. Lehetővé teszi az irányítópultok megtekintését, egyéni riasztások létrehozását és a vizsgálat javítását. Ez a csatlakozó betekintést nyújt az USB cserélhető tárolási eseményeibe. A Squadra Technologies secRMM és az Azure Sentinel integrációja a REST API-t használja.


> [!NOTE]
> Az adatok at annak a munkaterületnek a földrajzi helyén tároljuk, amelyen az Azure Sentinelt futtatja.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>A Squadra Technologies secRMM konfigurálása és csatlakoztatása 

A Squadra Technologies secRMM közvetlenül az Azure Sentinelbe integrálhatja és exportálhatja a naplókat.
1. Az Azure Sentinel portálon kattintson az Adatösszekötők elemre, és válassza a Squadra Technologies secRMM lehetőséget, majd nyissa meg az összekötő lapot.

2. Kövesse a [Squadra Technologies bevezetési útmutatójában](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) az Azure Sentinelhez felvázolt lépéseket, hogy a Squadra secRMM-adatokat bekértheti az Azure Sentinelben.   


## <a name="find-your-data"></a>Az adatok megkeresése

Sikeres kapcsolat létrehozása után az adatok megjelennek a Log Analytics csoportban CustomLogs secRMM_CL.
Ha a Megfelelő sémát szeretné használni a Log Analytics szolgáltatásban a Squadra Technologies secRMM-hez, keressen secRMM_CL.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése
A naplók megjelenése a Log Analytics szolgáltatásban 20 percet is igénybe vehet. 


## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a Squadra Technologies secRMM-et az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)
- Az adatok figyeléséhez [használjon munkafüzeteket.](tutorial-monitor-your-data.md)

