---
title: A Forcepoint DLP csatlakoztatása az Azure Sentinelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan csatlakozhat a Forcepoint DLP-hez az Azure Sentinelhez.
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
ms.openlocfilehash: 3bdb9d2b23ce05929ba5612e0c6a03fe1aab05de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588246"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>A Forcepoint DLP csatlakoztatása az Azure Sentinelhez

> [!IMPORTANT]
> A Forcepoint adatveszteség-megelőzési (DLP) adatösszekötő az Azure Sentinel jelenleg nyilvános előzetes verzióban. Ez a szolgáltatás szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)



A Forcepoint DLP-összekötő lehetővé teszi a DLP incidensadatok automatikus exportálását az Azure Sentinelbe. Segítségével betekintést nyerhet a felhasználói tevékenységekbe és az adatvesztési incidensekbe. Emellett lehetővé teszi az Azure-számítási feladatokból és más hírcsatornákból származó adatokkal való korrelációkat, és javítja a figyelési képességet az Azure Sentinelen belüli munkafüzetekkel.

> [!NOTE]
> Az adatok at annak a munkaterületnek a földrajzi helyén tároljuk, amelyen az Azure Sentinelt futtatja.

## <a name="configure-and-connect-forcepoint-dlp"></a>Forcepoint DLP konfigurálása és csatlakoztatása

Állítsa be a Forcepoint DLP-t úgy, hogy JSON formátumban továbbítsa az incidensadatokat az Azure-munkaterületre a REST API-n keresztül a [Forcepoint DLP integrációs útmutatóban leírtak szerint.](https://frcpnt.com/dlp-sentinel)


## <a name="find-your-data"></a>Az adatok megkeresése

A Forcepoint DLP-összekötő beállítása után az adatok megjelennek a Log Analytics csoportban a CustomLogs **ForcepointDLPEvents_CL**.


Ha a Megfelelő sémát szeretné használni a Forcepoint DLP Log Analytics szolgáltatásában, keresse meg **a ForcepointDLPEvents_CL.**


## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

A naplók megjelenése a Log Analytics szolgáltatásban 20 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a Forcepoint DLP-t az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:

- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)
- Az adatok figyeléséhez [használjon munkafüzeteket.](tutorial-monitor-your-data.md)