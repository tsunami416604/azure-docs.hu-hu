---
title: Az Azure IoT Hub DPS kapcsolatának diagnosztizálása és hibakeresése
description: Ismerje meg, hogyan diagnosztizálhatja és elháríthatja a gyakori hibákat az Azure IoT Hub DPS-vel való csatlakoztatásával
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: xujing
ms.openlocfilehash: b596a09e2185b38e6161ea4af2aa109e80faf248
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963420"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Hibaelhárítás az Azure IoT Hub Device Provisioning Service

A IoT-eszközök kapcsolódási problémái nehézkesek lehetnek a hibaelhárításhoz, mert számos lehetséges meghibásodási pont, például az igazolási hibák, a regisztrációs hibák stb. Ez a cikk útmutatást nyújt az eszközök csatlakozási problémáinak észleléséhez és hibaelhárításához [Azure monitoron](https://docs.microsoft.com/azure/azure-monitor/overview)keresztül.

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>A Azure Monitor használata a metrikák megjelenítéséhez és a riasztások beállításához

Az alábbi eljárás azt ismerteti, hogyan lehet megtekinteni és beállítani a riasztást IoT Hub Device Provisioning Service metrikán. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg a IoT Hub Device Provisioning Service.

3. Válassza a **Metrika** lehetőséget.

4. Válassza ki a kívánt metrikát. 
   <br />A DPS jelenleg három mérőszámot biztosít:

    | Metrika neve | Leírás |
    |-------|------------|
    | Igazolási kísérletek | Az eszköz kiépítési szolgáltatásával hitelesíteni próbált eszközök száma|
    | Regisztrációs kísérletek | A sikeres hitelesítés után IoT Hub regisztrálni próbált eszközök száma|
    | Eszköz hozzárendelve | IoT Hub sikeresen hozzárendelt eszközök száma|

5. Válassza ki a kívánt összesítési módszert a metrika vizuális nézetének létrehozásához. 

6. Egy metrika riasztásának beállításához válassza a metrika panel jobb felső sarkában található **új riasztási szabályok** lehetőséget, és **válassza az** **új riasztási szabályok**lehetőséget.

7. Válassza a **feltétel hozzáadása**lehetőséget, majd a kérdésekkel válassza ki a kívánt metrikát és küszöbértéket.

További információ: [Mi a klasszikus riasztás a Microsoft Azureban?](../azure-monitor/platform/alerts-overview.md)

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>A log Analytics használata a hibák megtekintéséhez és megoldásához

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg az IoT hubot.

3. Válassza a **diagnosztikai beállítások**lehetőséget.

4. Válassza ki **diagnosztika bekapcsolása**.

5. A kívánt naplók összegyűjtésének engedélyezése.

    | Napló neve | Leírás |
    |-------|------------|
    | DeviceOperations | Eszköz-kapcsolódási eseményekhez kapcsolódó naplók |
    | ServiceOperations | A Service SDK-val kapcsolatos eseménynaplók (például regisztrációs csoportok létrehozása vagy frissítése)|

6. A **küldés log Analytics** bekapcsolása ([lásd a díjszabást](https://azure.microsoft.com/pricing/details/log-analytics/)). 

7. Ugrás a **naplók** lapra a Azure Portal eszköz kiépítési szolgáltatásának erőforrás területén.

8. Kattintson a **Futtatás** gombra a legutóbbi események megtekintéséhez.

9. Ha `OperationName`vannak eredmények, keresse `ResultType` `ResultSignature`meg a, a, a `ResultDescription` és a (hibaüzenetet), hogy részletesebb információkat kapjon a hibáról.


## <a name="common-error-codes"></a>Gyakori hibakódok
Ez a táblázat a gyakori hibák megismerésére és megoldására használható.

| Hibakód| Leírás | HTTP-állapotkód |
|-------|------------|------------|
| 400 | A kérelem törzse nem érvényes; például nem lehet elemezni, vagy az objektumot nem lehet érvényesíteni.| 400 helytelen formátum |
| 401 | Az engedélyezési jogkivonat nem érvényesíthető; például lejárt vagy nem vonatkozik a kérelem URI-ra. Ezt a hibakódot a rendszer a TPM-igazolási folyamat részeként is visszaadja az eszközöknek. | 401 jogosulatlan|
| 404 | Az eszköz kiépítési szolgáltatásának példánya vagy egy erőforrás (például egy beléptetés) nem létezik. |404 – Nem található |
| 412 | A kérelemben szereplő ETag nem felel meg a meglévő erőforrás ETag, a RFC7232. | 412 előfeltétel sikertelen |
| 429 | A szolgáltatás szabályozza a műveleteket. Az egyes szolgáltatási korlátokat lásd: [IoT hub Device Provisioning Service korlátok](https://docs.microsoft.com/azure/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits). | 429 túl sok kérés |
| 500 | Belső hiba történt. | 500 Belső kiszolgálóhiba|
