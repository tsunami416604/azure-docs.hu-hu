---
title: Az Azure Security Center for IoT csatlakoztatása az Azure Sentinelhez | Microsoft dokumentumok
description: Megtudhatja, hogyan csatlakoztathatja az Azure Security Center for IoT-adatokat az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 3af51110a4c4604444573f62be65077c786db606
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588637"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>Az Azure Security Center for IoT és az Azure Sentinel adatainak összekapcsolása 


> [!IMPORTANT]
> Az Azure Security Center for IoT-adatösszekötő jelenleg nyilvános előzetes verzióban. Ez a szolgáltatás szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Az Azure Security Center for IoT-összekötő segítségével streamelheti az Azure Security Centert az AzureT-eseményekhez az Azure Sentinelbe. 

## <a name="prerequisites"></a>Előfeltételek

- **Olvasási** és **írási** engedélyek a munkaterülethez, amelyre az Azure Sentinel telepítve van
- **Az IoT-hez tartozó Azure Security Centert** **engedélyezni** kell a megfelelő IoT Hub(ok)on
- **Olvasási** és **írási** engedélyek a csatlakoztatni kívánt **Azure IoT Hubon**
- **Olvasási** és **írási** engedélyek az **Azure IoT Hub erőforráscsoporthoz**

> [!NOTE]
> Bár az Azure Security Center **standardszintű** licencét engedélyeznie kell az előfizetésében az IoT-erőforrás-riasztások Azure Sentinelbe való streameléséhez, csak az Azure Security Center **ingyenes** szintű licencét kell engedélyeznie az előfizetésén az Azure Security Center for IoT-riasztások megtekintéséhez az Azure Sentinelben. 

## <a name="connect-to-azure-security-center-for-iot"></a>Csatlakozás az Azure Security Center for IoT-hez

1. Az Azure Sentinelben válassza **az Adatösszekötők** lehetőséget, majd kattintson az **Azure Security Center for IoT csempére.**
1. A jobb alsó ablaktáblán kattintson az **Összekötő lap megnyitása gombra.** 
1. Kattintson **a Csatlakozás**elemre minden olyan IoT Hub-előfizetés mellett, amelynek riasztásait és eszközfigyelmeztetéseit az Azure Sentinelbe szeretné streamelni. 
    - Ha az Azure Security Center for IoT nincs engedélyezve az adott központban, megjelenik egy figyelmeztetési üzenet **engedélyezése.** A szolgáltatás elindításához kattintson az **Engedélyezés** hivatkozásra. 
1. Eldöntheti, hogy szeretné-e, hogy az Azure Security Center for IoT riasztásai automatikusan generálják az incidenseket az Azure Sentinelben. Az **Incidensek létrehozása**csoportban válassza az **Engedélyezés** lehetőséget, ha engedélyezni szeretné az alapértelmezett analitikus szabályt, hogy automatikusan hozzon létre incidenseket a csatlakoztatott biztonsági szolgáltatásban létrehozott riasztásokból. Ez a szabály az **Analytics** > **Aktív** szabályai alatt módosítható vagy szerkeszthető.

> [!NOTE]
> A kapcsolat módosítása után eltarthat egy ideig, amíg a hublista frissül. 

## <a name="log-analytics-alert-display"></a>A Log Analytics riasztási megjelenítése

A megfelelő séma használata a Log Analytics az Azure Security Center for IoT riasztások megjelenítéséhez:

1. Nyissa meg a Logs SecurityInsights SecurityAlert **(Logs** > **SecurityInsights** > **SecurityAlert**) mezőt, vagy keressen rá a **SecurityAlert**kifejezésre. 
2. Szűrje le, hogy csak az Azure Security Center az IoT által generált riasztások a következő kql szűrő használatával:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Szolgáltatási megjegyzések

Az IoT Hub csatlakoztatása után a központi adatok körülbelül 15 perccel később érhetők el az Azure Sentinelben.


## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja az Azure Security Center for IoT-adatokat az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)
- Az adatok figyeléséhez [használjon munkafüzeteket.](tutorial-monitor-your-data.md)
