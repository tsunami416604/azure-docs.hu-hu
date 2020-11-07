---
author: russell-cooks
ms.service: media-services
ms.topic: include
ms.date: 10/05/2020
ms.author: russellcooks
ms.openlocfilehash: 359c5f93516ea6f0561865bd86e4f51dedb4c3a5
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358238"
---
1. A Visual Studio Code-ban keresse meg az src/Edge lehetőséget. A létrehozott. env fájlt a telepítési sablonok néhány fájlja tartalmazza.

    A központi telepítési sablon a peremhálózati eszköz központi telepítési jegyzékére hivatkozik néhány helyőrző értékkel. A. env fájl ezen változók értékeit tartalmazhatja.
1. Ezután keresse meg a src/Cloud-to-Device-Console-app mappát. Itt láthatja a appsettings.jsa létrehozott fájlon, néhány további fájllal együtt:

    * C2D-Console-app. csproj: Ez a Project fájl a Visual Studio Code-hoz.
    * operations.jsbekapcsolva: Ez a fájl felsorolja azokat a különböző műveleteket, amelyeket futtatni kíván a program.
    * Program.cs: a minta programkódja:

        * Betölti az alkalmazás beállításait.
        * Meghívja az élő videó-elemzést IoT Edge modul közvetlen metódusait a topológia létrehozásához, a gráf létrehozásához és a gráf aktiválásához.
        * Szünetelteti a diagram kimenetének vizsgálatát a **terminál** ablakban, valamint az IoT hub számára a **kimeneti** ablakban elküldett eseményeket.
        * Inaktiválja a Graph-példányt, törli a Graph-példányt, és törli a Graph-topológiát.
