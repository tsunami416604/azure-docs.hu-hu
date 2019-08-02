---
title: A IoT előfeltételei Azure Security Center | Microsoft Docs
description: A IoT szolgáltatás előfeltételeinek megkezdéséhez szükséges Azure Security Center részletei.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/28/2019
ms.author: mlottner
ms.openlocfilehash: cc2dc3a190e3ad06bdc048f2a5770eae2a6990ec
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596831"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>A IoT előfeltételei Azure Security Center

Ez a cikk ismerteti a IoT szolgáltatáshoz tartozó Azure Security Center (ASC) különböző építőelemeit, a kezdéshez szükséges tudnivalókat, valamint ismerteti az alapvető fogalmakat, amelyek segítenek a szolgáltatás megismerésében. 

## <a name="minimum-requirements"></a>Minimális követelmények

- Standard szintű IoT Hub
    - RBAC szerepkör -tulajdonosi szintű jogosultságok 
- [Log Analytics munkaterület](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure Security Center (ajánlott)
    - A Azure Security Center használata javaslat, nem követelmény. Azure Security Center nélkül nem tudja megtekinteni a többi Azure-erőforrást IoT Hubon belül. 
 
## <a name="working-with-asc-for-iot-service"></a>Az ASC használata a IoT szolgáltatáshoz

Az IoT-észlelések és jelentéskészítési ASC az Azure IoT Hub és Azure Security Center használatával érhető el. Ha engedélyezni szeretné az ASC-t az Azure-IoT Hub IoT, a tulajdonosi szintű jogosultságokkal rendelkező fiókra van szükség. Miután engedélyezte az ASC szolgáltatást a IoT Hub IoT, a IoT-megállapítások ASC az Azure IoT Hub **biztonsági** funkciójaként jelenik meg, Azure Security Center pedig **IoT** . 

## <a name="supported-service-regions"></a>Támogatott szolgáltatási régiók 

A IoT esetében az ASC jelenleg a következő Azure-régiókban támogatott az IoT-hubok esetében:
  - USA középső régiója  
  - East US 
  - USA 2. keleti régiója
  - USA nyugati középső régiója
  - USA nyugati régiója
  - USA 2. nyugati régiója
  - USA déli középső régiója
  - USA északi középső régiója
  - Közép-Kanada
  - Kelet-Kanada 
  - Észak-Európa    
  - Dél-Brazília
  - Közép-Franciaország  
  - Az Egyesült Királyság nyugati régiója 
  - Az Egyesült Királyság déli régiója
  - Nyugat-Európa 
  - Észak-Európa 
  - Nyugat-Japán  
  - Kelet-Japán  
  - Délkelet-Ausztrália
  - Kelet-Ausztrália
  - Kelet-Ázsia   
  - Délkelet-Ázsia
  - Korea középső régiója
  - Korea déli régiója 
  - Közép-India
  - Dél-India
  
## <a name="wheres-my-iot-hub"></a>Hol van a IoT Hub?

A szolgáltatás elérhetőségének megkezdése előtt ellenőrizze a IoT Hub helyét. 

1. Nyissa meg a IoT Hub. 
2. Kattintson az **Áttekintés** elemre. 
3. Ellenőrizze, hogy a felsorolt hely megfelel-e a [támogatott szolgáltatási régiók](#supported-service-regions)egyikének. 


## <a name="supported-platforms-for-agents"></a>Az ügynökök által támogatott platformok 

A IoT-ügynökök ASC az eszközök és platformok egyre bővülő listáját támogatja. Tekintse meg a [támogatott platformok listáját](how-to-deploy-agent.md) , hogy ellenőrizze a meglévő vagy tervezett eszköz könyvtárát.  

## <a name="next-steps"></a>További lépések
- Az Azure IoT biztonsági [áttekintése](overview.md)
- Útmutató [a szolgáltatás engedélyezéséhez](quickstart-onboard-iot-hub.md)
- Olvassa el a [IoT kapcsolatos gyakori kérdéseket Azure Security Center](resources-frequently-asked-questions.md)
- A IoT- [riasztások Azure Security Center megismerése](concept-security-alerts.md)
