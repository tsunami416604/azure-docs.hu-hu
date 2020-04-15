---
title: 'Gyorsútmutató: A megoldás konfigurálása'
description: Ebben a rövid útmutatóban megtudhatja, hogyan konfigurálhatja a végpontok között IoT-megoldást az Azure Security Center for IoT használatával.
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
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 04505527428a6bb312c6fb7a12c116947fbd7cf6
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310858"
---
# <a name="quickstart-configure-your-iot-solution"></a>Rövid útmutató: Az IoT-megoldás konfigurálása

Ez a cikk ismerteti, hogyan hajthatja végre az IoT biztonsági megoldás kezdeti konfigurációját az Azure Security Center for IoT használatával.

## <a name="azure-security-center-for-iot"></a>Azure Security Center for IoT

Az Azure Security Center for IoT átfogó, teljes körű biztonságot nyújt az Azure-alapú IoT-megoldásokhoz.

Az Azure Security Center for IoT segítségével egyetlen irányítópulton figyelheti a teljes IoT-megoldást, és az Azure-ban az összes IoT-eszközét, IoT-platformját és háttér-erőforrását is felügyelheti.

Miután engedélyezve van az IoT Hubon, az Azure Security Center for IoT automatikusan azonosítja a többi Azure-szolgáltatást, amelyek szintén kapcsolódnak az IoT Hubhoz, és az IoT-megoldáshoz kapcsolódnak.

Az automatikus kapcsolatészlelés mellett kiválaszthatja, hogy az IoT-megoldás részeként mely más Azure-erőforráscsoportokat szeretné címkézni.

A beállítások lehetővé teszik teljes előfizetések, erőforráscsoportok vagy egyetlen erőforrások hozzáadását.

Az összes erőforrás-kapcsolat meghatározása után az Azure Security Center for IoT az Azure Security Center segítségével nyújt biztonsági javaslatokat és riasztásokat ezekhez az erőforrásokhoz.

## <a name="add-azure-resources-to-your-iot-solution"></a>Azure-erőforrások hozzáadása az IoT-megoldáshoz

Ha új erőforrást szeretne hozzáadni az IoT-megoldáshoz, tegye a következőket:

1. Nyissa meg az **IoT Hubot** az Azure Portalon.
1. Válassza ki és nyissa meg az **Erőforrások elemet** a bal oldali menü **Biztonság** lapján.
1. Válassza **a Szerkesztés** lehetőséget, és válassza ki az IoT-megoldáshoz tartozó erőforráscsoportokat.
1. Kattintson a **Hozzáadás** parancsra.

Gratulálunk! Új erőforráscsoportot adott hozzá az IoT-megoldáshoz.

Az Azure Security Center for IoT mostantól figyeli az Újonnan hozzáadott erőforráscsoportokat, és az IoT-megoldás részeként felületezi a vonatkozó biztonsági javaslatokat és riasztásokat.

## <a name="next-steps"></a>További lépések

Tovább a következő cikkhez, hogy megtudja, hogyan hozhat létre biztonsági modulokat...

> [!div class="nextstepaction"]
> [Biztonsági modulok létrehozása](quickstart-create-security-twin.md)
