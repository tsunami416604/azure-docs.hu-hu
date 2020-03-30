---
title: Azure Security Center for IoT útmutató az Azure Sentinel (előzetes verzió) konfigurációjához| Microsoft dokumentumok
description: Bemutatja, hogyan konfigurálhatja az Azure Sentinelt úgy, hogy adatokat fogadjon az Azure Security Center for IoT-megoldásból.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: 082b33332051fee9da2aebe63b0c41edb300afaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303545"
---
> [!IMPORTANT]
> Az Azure Security Center for IoT adatösszekötő az Azure Sentinel jelenleg nyilvános előzetes verzióban.
> Ez a szolgáltatás szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>Az Azure Security Center for IoT adatainak összekapcsolása az Azure Sentinelhez (előzetes verzió) 

Ebből az útmutatóból megtudhatja, hogyan csatlakoztathatja az Azure Security Center for IoT-adatokat az Azure Sentinelhez.  

> [!div class="checklist"]
> * Előfeltételek 
> * Kapcsolati beállítások
> * Log Analytics riasztási nézete 

Az Azure Security Center for IoT riasztásait közvetlenül az Azure Sentinelbe továbbíthatja.

## <a name="prerequisites"></a>Előfeltételek

- A munkaterület **olvasási** és **írási** engedélyekkel kell rendelkeznie.
- **Az Azure Security Center for IoT-t** **engedélyezni** kell a megfelelő IoT Hub(ok)on.
- Olvasási és **read** **írási** engedéllyel is rendelkeznie kell a csatlakoztatni kívánt **Azure IoT Hubon.**
- **Olvasási** és **írási** engedéllyel is rendelkeznie kell az **Azure IoT Hub erőforráscsoportban.**

> [!NOTE]
> Általános Azure-erőforrás-riasztások küldéséhez az Azure Security Center standardszintű licencelése az előfizetésen fut. Az Azure Security Center for IoT-hoz szükséges ingyenes szintű licenceléssel csak az Azure Security Center for IoT-val kapcsolatos riasztások lesznek továbbítva az Azure Sentinelnek. 

## <a name="connect-to-azure-security-center-for-iot"></a>Csatlakozás az Azure Security Center for IoT-hez

1. Az Azure Sentinelben válassza **az Adatösszekötők** lehetőséget, majd kattintson az **Azure Security Center for IoT csempére.**
1. A jobb oldali ablaktábla alján kattintson az **Összekötő lap megnyitása gombra.** 
1. Kattintson **a Csatlakozás**elemre minden olyan IoT Hub-előfizetés mellett, amelynek riasztásait és eszközfigyelmeztetéseit az Azure Sentinelbe szeretné streamelni. 
    - Ha az Azure Security Center for IoT nincs engedélyezve az adott központban, megjelenik egy figyelmeztetési üzenet engedélyezése. Kattintson az **Enable** linkre a szolgáltatás elindításához és engedélyezéséhez. 
1. Eldöntheti, hogy szeretné-e, hogy az Azure Security Center for IoT riasztásai automatikusan generálják az incidenseket az Azure Sentinelben. Az **Incidensek létrehozása csoportban**válassza az **Engedélyezés** lehetőséget, ha engedélyezni szeretné, hogy a szabály automatikusan hozzon létre incidenseket a létrehozott riasztásokból.  Ez a szabály az **Analytics** > **Aktív** szabályai alatt módosítható vagy szerkeszthető.

> [!NOTE]
>A kapcsolat módosítása után 10 másodpercig vagy tovább is eltarthat, amíg frissíti a hublistát. 

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

Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja az Azure Security Center for IoT-t az Azure Sentinelhez. A fenyegetésészlelésről és a biztonsági adatokhoz való hozzáférésről az alábbi cikkekben olvashat bővebben:

- Ismerje meg, hogyan használhatja az Azure Sentinel t, hogy [betekintést az adatok, és a potenciális fenyegetések.](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)

- Ismerje meg, hogyan [érheti el az IoT biztonsági adatait](how-to-security-data-access.md)