---
title: Az Azure Security Center az IoT-megoldás előzetes konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az Azure Security Center használata az IoT teljes körű IoT-megoldás.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 64a04861b14e48eaa14a369546dd9d54bf59f019
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58861921"
---
# <a name="quickstart-configure-your-iot-solution"></a>Gyors útmutató: Az IoT-megoldás konfigurálása

> [!IMPORTANT]
> Az Azure Security Center az IoT jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk bemutatja, hogyan kezdeti konfigurálása az ASC használva az IoT biztonsági megoldásokat magyarázattal szolgál. 

## <a name="azure-security-center-asc-for-iot"></a>Az Azure Security Center (ASC) az IoT

ASC az IoT Azure-alapú IoT-megoldások átfogó teljes körű biztonsági biztosít.

Az IoT ASC figyelheti a teljes IoT-megoldás egy irányítópult, felszínre hozza a minden IoT-eszközök, IoT-platformokhoz és háttérbeli erőforrásokhoz az Azure-ban.

Engedélyezve van az IoT hub, IOT ASC automatikusan azonosítja más Azure-szolgáltatásokhoz is csatlakozik az IoT hub és az IoT-megoldás kapcsolatos.

Automatikus kapcsolatok észlelése, mellett válasszon and választhatja mely más Azure-erőforrások címke az IoT-megoldás részeként.
A választott beállításokat adhat hozzá a teljes előfizetések, erőforráscsoportok vagy egyetlen erőforrás.

Után az összes erőforrás kapcsolat definiálása, ASC az IoT használ az Azure Security Center használatával biztosítanak a biztonsági javaslatokra és riasztásokra ezeket az erőforrásokat.

## <a name="add-azure-resources-to-your-iot-solution"></a>Az IoT-megoldás Azure-erőforrások hozzáadása

Az IoT-megoldás új erőforráscsoport hozzáadásához tegye a következőket: 

1. Nyissa meg a **az IoT Hub** az Azure Portalon. 
2. Válassza ki, és nyissa meg a **erőforrások** alatt **biztonsági** a bal oldali menüből. 
3. Válassza ki **erőforrások hozzáadása**.
4. Válassza ki az IoT-megoldás tartozó erőforrásokat.
5. Kattintson a **Hozzáadás** parancsra. 

Gratulálunk! Az IoT-megoldás történő hozzáadása egy új erőforrást.

Az ASC IOT most figyelők Ön újonnan hozzáadott erőforrásokat, és felület releváns biztonsági javaslatok és riasztások az IoT-megoldás részeként.

## <a name="next-steps"></a>További lépések

Folytassa a következő cikkben megtudhatja, hogyan hozhat létre biztonsági modulok...

> [!div class="nextstepaction"]
> [Biztonsági modulok létrehozása](quickstart-create-security-twin.md)