---
title: Azure Security Center IoT útmutató az Azure Sentinel (előzetes verzió) konfigurálásához | Microsoft Docs
description: Ez az útmutató azt ismerteti, hogyan konfigurálható az Azure Sentinel a Azure Security Center IoT-megoldás adatainak fogadására.
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
ms.openlocfilehash: f6e7eddd6ddbcec61c3d8d173891cbc9abaaf08f
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463179"
---
> [!IMPORTANT]
> Az Azure Sentinel IoT-adatösszekötő Azure Security Center jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>Az adatok összekapcsolásának Azure Security Center a IoT-ből az Azure Sentinelbe (előzetes verzió) 

Ebből az útmutatóból megtudhatja, hogyan csatlakoztatható a Azure Security Center IoT-adataihoz az Azure Sentinel szolgáltatáshoz.  

> [!div class="checklist"]
> * Előfeltételek 
> * Kapcsolati beállítások
> * Log Analytics riasztás nézet 

Azure Security Center riasztások összekapcsolását a IoT, és közvetlenül az Azure Sentinelben továbbíthatja őket.

## <a name="prerequisites"></a>Előfeltételek

- A munkaterület **olvasási** és **írási** engedélyekkel kell rendelkeznie.
- A **IoT Azure Security Center** engedélyezni kell a megfelelő IoT hub (ka) **t** .
- **Olvasási** és **írási** engedéllyel kell rendelkeznie az **Azure-IoT hubhoz** , amelyhez csatlakozni szeretne.
- Az **Azure IoT hub erőforráscsoporthoz** **olvasási** és **írási** engedélyekkel is rendelkeznie kell.

> [!NOTE]
> Az általános Azure-erőforrásokra vonatkozó riasztások elküldéséhez az előfizetéshez tartozó Azure Security Center standard szintű licencelésnek kell futnia. A IoT Azure Security Centerhoz szükséges ingyenes szintű licenceléssel csak a IoT kapcsolatos riasztások Azure Security Center a rendszer továbbítja az Azure Sentinelnek. 

## <a name="connect-to-azure-security-center-for-iot"></a>Kapcsolódás Azure Security Center IoT

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd kattintson a **Azure Security Center IoT** csempére.
1. A jobb alsó ablaktáblán kattintson az **összekötő lap megnyitása**lehetőségre. 
1. Kattintson a **Kapcsolódás**lehetőségre minden olyan IoT hub-előfizetés mellett, amelynek a riasztásait és az eszközök riasztásait az Azure Sentinel szolgáltatásba kívánja továbbítani. 
    - Ha a IoT Azure Security Center nincs engedélyezve ezen a központban, egy figyelmeztető üzenet jelenik meg. A szolgáltatás elindításához kattintson az **enable (Engedélyezés** ) hivatkozásra. 
1. Eldöntheti, hogy szeretné-e, hogy a riasztások Azure Security Center IoT az incidensek automatikus előállítását az Azure Sentinelben. Az **incidensek létrehozása**területen válassza az **Engedélyezés** lehetőséget az alapértelmezett analitikus szabály engedélyezéséhez, hogy automatikusan hozzon létre incidenseket a csatlakoztatott biztonsági szolgáltatásban létrehozott riasztásokból. Ez a szabály az **Analytics** > **aktív** szabályok területén módosítható vagy szerkeszthető.

> [!NOTE]
>A kapcsolatok módosítása után akár 10 másodpercig is eltarthat a központ listájának frissítése. 

## <a name="log-analytics-alert-display"></a>Log Analytics riasztás megjelenítése

A Log Analytics megfelelő sémájának használata a IoT-riasztások Azure Security Center megjelenítéséhez:

1. Nyissa meg a **naplókat** > **SecurityInsights** > **SecurityAlert**, vagy keressen rá a **SecurityAlert**kifejezésre. 
2. A szűréssel csak a következő kql szűrővel rendelkező IoT által generált riasztások Azure Security Center látható:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Szolgáltatási megjegyzések

IoT Hub csatlakoztatása után a hub-adatközpont körülbelül 15 perccel később elérhető az Azure Sentinelben.


## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható Azure Security Center a IoT-hez az Azure Sentinel szolgáltatáshoz. A veszélyforrások észlelésével és a biztonsági adathozzáféréssel kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan használhatja az Azure Sentinelt az [adatai és a potenciális fenyegetések](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)megismerésére.

- Ismerje meg, hogyan [férhet hozzá a IoT biztonsági adataihoz](how-to-security-data-access.md)