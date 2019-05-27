---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: a7a86c6a2661a8a1f30491391fc76f4dc5d71f54
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66163558"
---
IoT Hub létrehozása az Azure Portallal:

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).

1. Válassza az **Erőforrás létrehozása** > **Eszközök internetes hálózata** > **IoT Hub** elemet.

    ![Az IoT Hub telepítésének kiválasztása](media/iot-hub-tutorials-create-free-hub/selectiothub.png)

1. Az ingyenes szintű IoT Hub létrehozásához használja az alábbi táblákban szereplő értékeket:

    | Beállítás | Érték |
    | ------- | ----- |
    | Előfizetés | Válassza ki saját Azure-előfizetését a legördülő menüből. |
    | Erőforráscsoport | Hozzon létre újat. Ez az oktatóanyag a **tutorials-iot-hub-rg** nevet használja. |
    | Régió | Ez az oktatóanyag az **USA nyugati régióját** használja. Válassza ki az Önhöz legközelebbi régiót. |
    | Name (Név) | A következő képernyőkép a **tutorials-iot-hub** nevet használja. Saját hub létrehozásakor egyedi nevet kell választania. |

    ![Hub beállításai – 1](media/iot-hub-tutorials-create-free-hub/hubdefinition-1.png)

    | Beállítás | Érték |
    | ------- | ----- |
    | Tarifa- és méretcsomag | F1 – Ingyenes. Egy előfizetésben csak egyetlen ingyenes szintű hubja lehet. |
    | IoT Hub-egységek | 1. |

    ![Hub beállításai – 2](media/iot-hub-tutorials-create-free-hub/hubdefinition-2.png)

1. Kattintson a **Create** (Létrehozás) gombra. A hub létrehozása több percet is igénybe vehet.

    ![Hub beállításai – 3](media/iot-hub-tutorials-create-free-hub/hubdefinition-3.png)

1. Jegyezze fel az IoT Hub választott nevét. Ezt az értéket az oktatóanyag későbbi részében még használni fogja.