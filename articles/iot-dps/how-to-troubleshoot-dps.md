---
title: A kapcsolatbontások diagnosztizálása és hibaelhárítása az Azure IoT Hub DPS-szel
description: Ismerje meg a gyakori hibák diagnosztizálását és elhárítását az Azure IoT Hub-eszközkiépítési szolgáltatás (DPS) eszközkapcsolatával kapcsolatban
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: xujing
ms.openlocfilehash: 3cbab09c6b50abb590cfe9f2720713a8fa547aa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646472"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Hibaelhárítás az Azure IoT Hub eszközkiépítési szolgáltatásával

Az IoT-eszközök kapcsolódási problémáit nehéz lehet elhárítani, mivel számos lehetséges hibapont létezik, például tanúsítványhibák, regisztrációs hibák stb. Ez a cikk útmutatást nyújt az eszközkapcsolati problémák észleléséhez és elhárításához az [Azure Monitoron](https://docs.microsoft.com/azure/azure-monitor/overview)keresztül.

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Az Azure Monitor használata metrikák megtekintéséhez és riasztások beállításához

Az alábbi eljárás bemutatja, hogyan tekintheti meg és állíthatja be a riasztást az IoT Hub-eszközkiépítési szolgáltatás metrikája. 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

2. Tallózással keresse meg az IoT Hub-eszközkiépítési szolgáltatást.

3. Válassza a **Metrika** lehetőséget.

4. Válassza ki a kívánt metrikát. 
   <br />Jelenleg három mérőszám létezik a DPS-hez:

    | Metrikus neve | Leírás |
    |-------|------------|
    | Igazolási kísérletek | Az eszközkiépítési szolgáltatással hitelesítést megkísérelt eszközök száma|
    | Regisztrációs kísérletek | Azon eszközök száma, amelyek sikeres hitelesítés után megpróbáltak regisztrálni az IoT Hubba|
    | Hozzárendelt eszköz | Az IoT Hubhoz sikeresen hozzárendelt eszközök száma|

5. Válassza ki a kívánt összesítési módszert a metrika vizuális nézetének létrehozásához. 

6. Metrika riasztásának beállításához válassza az **Új riasztási szabályok lehetőséget** a metrikapanel jobb felső részén, hasonlóképpen a **Riasztás** panelre, és kiválaszthatja az **Új riasztási szabályok lehetőséget.**

7. Válassza **a Feltétel hozzáadása**lehetőséget, majd a következő utasításokat követve válassza ki a kívánt metrikát és küszöbértéket.

További információ: [Mik a klasszikus riasztások a Microsoft Azure-ban?](../azure-monitor/platform/alerts-overview.md)

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>Hibák megtekintése és megoldása a Naplóantikus használatával

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

2. Tallózással az IoT-központ.

3. Válassza **a Diagnosztikai beállítások lehetőséget**.

4. Válassza **a Diagnosztika bekapcsolása**lehetőséget.

5. Engedélyezze a kívánt naplók gyűjtését.

    | Napló neve | Leírás |
    |-------|------------|
    | DeviceOperations (Eszközüzemeltetés) | Eszközcsatlakozási eseményekhez kapcsolódó naplók |
    | Szolgáltatási műveletek | A szolgáltatás SDK használatával kapcsolatos eseménynaplók (pl. regisztrációs csoportok létrehozása vagy frissítése)|

6. Kapcsolja be a **Küldés a Log Analytics szolgáltatásba** [(lásd: díjszabás](https://azure.microsoft.com/pricing/details/log-analytics/)). 

7. Nyissa meg a **Naplók** lapot az Azure Portalon az Eszközkiépítési szolgáltatás erőforrás alatt.

8. A legutóbbi események megtekintéséhez kattintson a **Futtatás** gombra.

9. Ha vannak eredmények, `OperationName`keresse `ResultType` `ResultSignature`meg `ResultDescription` a , , és (hibaüzenetet) a hiba részletesebb ismertetéséhez.


## <a name="common-error-codes"></a>Gyakori hibakódok
Ebben a táblázatban megismerheti és megoldhatja a gyakori hibákat.

| Hibakód| Leírás | HTTP-állapotkód |
|-------|------------|------------|
| 400 | A kérelem törzse érvénytelen; például nem elemezhető, vagy az objektum nem érvényesíthető.| 400 Rossz formátum |
| 401 | Az engedélyezési jogkivonat nem érvényesíthető; például lejárt, vagy nem vonatkozik a kérelem URI-jára. Ez a hibakód a TPM-tanúsítványfolyamat részeként is visszakerül az eszközökre. | 401 Jogosulatlan|
| 404 | Az Eszközkiépítési szolgáltatás példánya vagy egy erőforrás (pl. egy regisztráció) nem létezik. |404 Nem található |
| 412 | A kérelemben lévő ETag nem egyezik meg a meglévő erőforrás ETag jének az RFC7232 szerint. | 412 Az előfeltétel nem sikerült |
| 429 | A műveleteket a szolgáltatás szabályozza. Az adott szolgáltatáskorlátokat az [IoT Hub-eszközkiépítési szolgáltatás korlátai.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits) | 429 Túl sok kérés |
| 500 | Belső hiba történt. | 500 Belső kiszolgálóhiba|
