---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 5680bd1c3fadab8edcde1634c708ca8683c0e99c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76021140"
---
## <a name="create-base-resources"></a>Alaperőforrások létrehozása

Az üzenet-útválasztás konfigurálása előtt létre kell hoznia egy IoT hubot, egy tárfiókot és egy Service Bus-várólistát. Ezek az erőforrások az oktatóanyag 1.

Ugyanazt az erőforráscsoportot és helyet használja minden erőforráshoz. Ezután a végén az erőforráscsoport törlésével eltávolíthatja az összes erőforrást egy lépésben.

Az alábbiakban összefoglaljuk a következő szakaszokban elvégzendő lépéseket: 

1. Hozzon létre egy [erőforráscsoportot](../articles/azure-resource-manager/management/overview.md).

2. Hozzon létre egy IoT Hubot az S1 szinten. Adjon hozzá egy fogyasztói csoportot az IoT Hubhoz. A fogyasztói csoportot az Azure Stream Analytics használja az adatok lekérésekor.

   > [!NOTE]
   > Az oktatóanyag végrehajtásához egy fizetős szint iot hubot kell használnia. Az ingyenes szint csak lehetővé teszi egy végpont beállítását, és ez az oktatóanyag több végpontot igényel.
   > 

3. Hozzon létre egy standard szintű V1-tárfiókot Standard_LRS-replikációval.

4. Hozzon létre egy Service Bus-névteret és üzenetsort.

5. Hozzon létre egy eszközidentitást ahhoz a szimulált eszközhöz, amely üzeneteket küld a központnak. Mentse a kulcsot a tesztelési fázishoz. (Ha erőforrás-kezelő sablont hoz létre, az a sablon üzembe helyezése után történik.)