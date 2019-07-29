---
title: A Azure Security Center konfigurálása a IoT-megoldáshoz | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a teljes körű IoT-megoldást a IoT Azure Security Center használatával.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 15c8aabbcb19d009bb202d111dc7f80da4cebff1
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597028"
---
# <a name="quickstart-configure-your-iot-solution"></a>Gyors útmutató: A IoT-megoldás konfigurálása

Ez a cikk azt ismerteti, hogyan hajthatja végre a IoT biztonsági megoldás kezdeti konfigurációját a IoT Azure Security Center használatával. 

## <a name="azure-security-center-for-iot"></a>Azure Security Center for IoT

A IoT Azure Security Center teljes körű biztonságot nyújt az Azure-alapú IoT-megoldásokhoz.

A IoT Azure Security Center segítségével egyetlen irányítópulton figyelheti a teljes IoT-megoldást, felhasználva az összes IoT-eszközt, IoT platformot és háttér-erőforrást az Azure-ban.

Ha engedélyezte a IoT Hub, a IoT Azure Security Center automatikusan azonosítja a többi Azure-szolgáltatást is, amely az IoT hubhoz csatlakozik, és kapcsolódik a IoT-megoldáshoz.

Az automatikus kapcsolat észlelése mellett kiválaszthatja és kiválaszthatja, hogy mely egyéb Azure-erőforráscsoportok legyenek felcímkézve a IoT-megoldás részeként.

A kiválasztott lehetőségek lehetővé teszik teljes előfizetések, erőforráscsoportok vagy önálló erőforrások hozzáadását.

Az összes erőforrás-kapcsolat meghatározása után a IoT Azure Security Center a Azure Security Center, hogy biztonsági javaslatokat és riasztásokat nyújtson ezekhez az erőforrásokhoz.

## <a name="add-azure-resources-to-your-iot-solution"></a>Azure-erőforrások hozzáadása a IoT-megoldáshoz

Ha új erőforrást szeretne hozzáadni a IoT-megoldáshoz, tegye a következőket: 

1. Nyissa  meg a IoT Hubt Azure Portal. 
2. Válassza ki és nyissa meg a bal oldali menüben a **Biztonság** területen található **erőforrások** elemet. 
3. Válassza a **Szerkesztés** lehetőséget, és válassza ki a IoT-megoldáshoz tartozó erőforrásokat.
5. Kattintson a **Hozzáadás**lehetőségre. 

Gratulálunk! Új erőforráscsoportot adott hozzá a IoT-megoldáshoz.

A IoT-hez készült Azure Security Center mostantól figyeli az újonnan hozzáadott erőforráscsoportokat, és a IoT-megoldás részeként felfedi a megfelelő biztonsági javaslatokat és riasztásokat.

## <a name="next-steps"></a>További lépések

A következő cikkből megtudhatja, hogyan hozhat létre biztonsági modulokat...

> [!div class="nextstepaction"]
> [Biztonsági modulok létrehozása](quickstart-create-security-twin.md)