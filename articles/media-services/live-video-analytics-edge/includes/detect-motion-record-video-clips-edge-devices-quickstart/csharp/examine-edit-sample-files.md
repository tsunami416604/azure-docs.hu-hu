---
ms.openlocfilehash: 426c735dfd0d015cdc1a734edde9d336fb88cfbc
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486781"
---
A rövid útmutató előfeltételeinek részeként letöltötte a mintakód egy mappába. A mintakód vizsgálatához és szerkesztéséhez kövesse az alábbi lépéseket.

1. A Visual Studio Code-ban lépjen az *src/Edge* elemre. Ekkor megjelenik a *. env* fájl és néhány központi telepítési sablon fájl.

    A központi telepítési sablon a peremhálózati eszköz központi telepítési jegyzékére hivatkozik, ahol egyes tulajdonságok esetében változókat kell használni. A *. env* fájl tartalmazza a változók értékeit.
1. Lépjen a *src/Cloud-to-Device-Console-app* mappába. Itt láthatja a fájl *appsettings.jsét* és néhány további fájlt:
    * ***C2D-Console-app. csproj** _ – a Project fájl a Visual Studio Code-hoz.
    _ ***operations.jsa** _-ben – a program futtatásához használni kívánt műveletek listája.
    _ ***Program.cs** _ – a minta programkódja. Ez a kód:

        _ Betölti az alkalmazás beállításait.
        * Közvetlen metódusokat hív meg, amelyeket az élő videó Analytics IoT Edge modulban tesz elérhetővé. A modul segítségével elemezheti az élő videó streameket a [közvetlen metódusok](../../../direct-methods.md)meghívásával. 
        * Szünetelteti, így megvizsgálhatja a program kimenetét a **terminál** ablakban, és megvizsgálhatja a modul által a **kimeneti** ablakban generált eseményeket.
        * Közvetlen metódusokat hív meg az erőforrások törléséhez.

1. Szerkessze a *operations.js* fájlt:
    * Módosítsa a Graph-topológiára mutató hivatkozást:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/2.0/topology.json"`
    * A (z) alatt `GraphInstanceSet` szerkessze a gráf topológiájának nevét, hogy az megfeleljen az előző hivatkozásban szereplő értéknek:
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`
    * Szerkessze az RTSP URL-címét, hogy a videofájl mutasson:

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * `GraphTopologyDelete`A alatt szerkessze a nevet:

        `"name": "EVRToFilesOnMotionDetection"`
