---
title: 'Gyors útmutató: a megoldás konfigurálása'
description: Ebből a rövid útmutatóból megtudhatja, hogyan konfigurálhatja a teljes körű IoT-megoldást a IoT Azure Security Center használatával.
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
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: e6ab713715cacc799d2b980c2bce2a2a15b76887
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505454"
---
# <a name="quickstart-configure-your-iot-solution"></a>Rövid útmutató: a IoT-megoldás konfigurálása

Ez a cikk azt ismerteti, hogyan hajthatja végre a IoT biztonsági megoldás kezdeti konfigurációját a IoT Azure Security Center használatával.

## <a name="azure-security-center-for-iot"></a>Azure Security Center for IoT

A IoT Azure Security Center teljes körű biztonságot nyújt az Azure-alapú IoT-megoldásokhoz.

A IoT Azure Security Center segítségével egyetlen irányítópulton figyelheti a teljes IoT-megoldást, felhasználva az összes IoT-eszközt, IoT platformot és háttér-erőforrást az Azure-ban.

Ha engedélyezte a IoT Hub, a IoT Azure Security Center automatikusan azonosít más Azure-szolgáltatásokat is, amelyek a IoT Hubhoz és a IoT-megoldáshoz kapcsolódnak.

Az automatikus kapcsolat észlelése mellett kiválaszthatja és kiválaszthatja, hogy mely egyéb Azure-erőforráscsoportok legyenek felcímkézve a IoT-megoldás részeként.

A kiválasztott lehetőségek lehetővé teszik teljes előfizetések, erőforráscsoportok vagy önálló erőforrások hozzáadását.

Az összes erőforrás-kapcsolat meghatározása után a IoT Azure Security Center a Azure Security Center, hogy biztonsági javaslatokat és riasztásokat nyújtson ezekhez az erőforrásokhoz.

## <a name="add-azure-resources-to-your-iot-solution"></a>Azure-erőforrások hozzáadása a IoT-megoldáshoz

Ha új erőforrást szeretne hozzáadni a IoT-megoldáshoz, tegye a következőket:

1. Nyissa meg a **IoT Hubt** Azure Portal.
1. Válassza ki és nyissa meg a **Beállítások** lehetőséget a bal oldali menüben a **Biztonság** szakaszban, majd válassza a **figyelt erőforrások**lehetőséget.
1. Válassza a **Szerkesztés** lehetőséget, és válassza ki a IoT-megoldáshoz tartozó megfigyelt erőforrásokat.
1. Kattintson a **Hozzáadás** parancsra.

Gratulálunk! Új erőforráscsoportot adott hozzá a IoT-megoldáshoz.

A IoT-hez készült Azure Security Center mostantól figyeli az újonnan hozzáadott erőforráscsoportokat, és a IoT-megoldás részeként felfedi a megfelelő biztonsági javaslatokat és riasztásokat.

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan hozhat létre biztonsági modulokat...

> [!div class="nextstepaction"]
> [Biztonsági modulok létrehozása](quickstart-create-security-twin.md)
