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
ms.openlocfilehash: 367a0b1d17f8d5ebe4f46835ace963b00e75354e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68229253"
---
IoT Hub létrehozása az Azure Portallal:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Válassza > **az** >  **Erőforrás-internet**létrehozása**IoT Hub**lehetőséget.

    ![Az IoT Hub telepítésének kiválasztása](media/iot-hub-tutorials-create-free-hub/selectiothub.png)

1. Az ingyenes szintű IoT Hub létrehozásához használja az alábbi táblákban szereplő értékeket:

    | Beállítás | Érték |
    | ------- | ----- |
    | Előfizetés | Válassza ki saját Azure-előfizetését a legördülő menüből. |
    | Erőforráscsoport | Hozzon létre újat. Ez az oktatóanyag a **tutorials-iot-hub-rg** nevet használja. |
    | Régió | Ez az oktatóanyag az **USA nyugati régióját** használja. Válassza ki az Önhöz legközelebbi régiót. |
    | Név | A következő képernyőkép a **tutorials-iot-hub** nevet használja. Saját hub létrehozásakor egyedi nevet kell választania. |

    ![Hub beállításai – 1](media/iot-hub-tutorials-create-free-hub/hubdefinition-1.png)

    | Beállítás | Érték |
    | ------- | ----- |
    | Tarifacsomag és méret | F1 – Ingyenes. Egy előfizetésben csak egyetlen ingyenes szintű hubja lehet. |
    | IoT Hub-egységek | 1 |

    ![Hub beállításai – 2](media/iot-hub-tutorials-create-free-hub/hubdefinition-2.png)

1. Kattintson **a Létrehozás gombra.** A hub létrehozása több percet is igénybe vehet.

    ![Hub beállításai – 3](media/iot-hub-tutorials-create-free-hub/hubdefinition-3.png)

1. Jegyezze fel az IoT Hub választott nevét. Ezt az értéket az oktatóanyag későbbi részében még használni fogja.
