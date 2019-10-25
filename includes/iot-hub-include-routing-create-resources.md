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
ms.openlocfilehash: c97c8231265cf87f52333a56d21d6fb13180c554
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808834"
---
## <a name="create-base-resources"></a>Alaperőforrások létrehozása

Az üzenet-útválasztás konfigurálása előtt létre kell hoznia egy IoT hubot, egy Storage-fiókot és egy Service Bus várólistát. Ezek az erőforrások az oktatóanyag 1. részében elérhető négy cikk egyikének használatával hozhatók létre: a Azure Portal, egy Azure Resource Manager sablon, az Azure CLI vagy a Azure PowerShell.

Ugyanazt az erőforráscsoportot és helyet használja minden erőforráshoz. Ezután törölheti az összes erőforrást egy lépésben az erőforráscsoport törlésével.

Az alábbiakban a következő részekben végrehajtandó lépések összegzése látható: 

1. Hozzon létre egy [erőforráscsoportot](../articles/azure-resource-manager/resource-group-overview.md).

2. Hozzon létre egy IoT Hubot az S1 szinten. Adjon hozzá egy fogyasztói csoportot az IoT Hubhoz. A fogyasztói csoportot az Azure Stream Analytics használja az adatok lekérésekor.

   > [!NOTE]
   > Az oktatóanyag elvégzéséhez egy fizetős szinten IOT hub-t kell használnia. Az ingyenes szint csak egy végpont beállítását teszi lehetővé, és ez az oktatóanyag több végpontot igényel.
   > 

3. Hozzon létre egy standard szintű V1-tárfiókot Standard_LRS-replikációval.

4. Hozzon létre egy Service Bus-névteret és üzenetsort.

5. Hozzon létre egy eszközidentitást ahhoz a szimulált eszközhöz, amely üzeneteket küld a központnak. Mentse a kulcsot a tesztelési fázishoz. (Resource Manager-sablon létrehozásakor ez a sablon telepítése után történik.)