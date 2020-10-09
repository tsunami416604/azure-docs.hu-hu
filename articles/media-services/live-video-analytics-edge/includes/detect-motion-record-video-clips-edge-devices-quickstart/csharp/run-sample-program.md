---
ms.openlocfilehash: f5e180cb85e65cf832ffe0a3746e25790644e1ba
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91829021"
---
1. A Visual Studio Code-ban nyissa meg a **bővítmények** lapot (vagy nyomja le a CTRL + SHIFT + X billentyűkombinációt), és keressen rá az Azure IoT hubra.
1. Kattintson a jobb gombbal, és válassza a **bővítmény beállításai**lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Bővítmény beállításai&quot;:::
1. Keresse meg és engedélyezze a &quot;részletes üzenet megjelenítése" lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Bővítmény beállításai&quot;:::
1. Keresse meg és engedélyezze a &quot;részletes üzenet megjelenítése"
        }
      ]
    }
  }
  ```
    
  * Egy hívás, `GraphInstanceActivate` amely elindítja a Graph-példányt és a videó áramlását.
  * Egy második hívás, `GraphInstanceList` amely azt mutatja, hogy a Graph-példány fut állapotban van.
1. A **terminál** ablakban lévő kimenet a következő időpontban szünetel: `Press Enter to continue` . Ne jelölje be az ENTER billentyűt. Görgetéssel tekintse meg a meghívott közvetlen metódusok JSON-válaszának hasznos adatait.
1. Váltson a **kimeneti** ablakra a Visual Studio Code-ban. Láthatja, hogy az élő videó Analytics IoT Edge-modulon az IoT hub-ra küld üzenetet. A rövid útmutató következő szakasza ezeket az üzeneteket tárgyalja.
1. A Media Graph továbbra is fut, és kinyomtatja az eredményeket. Az RTSP-szimulátor megtartja a forrás videóját. A Media Graph leállításához térjen vissza a **terminál** ablakába, és válassza az ENTER billentyűt. 

    A következő hívási sorozat megtisztítja az erőforrásokat:

    * A Graph- `GraphInstanceDeactivate` példány inaktiválására irányuló hívás.
    * A `GraphInstanceDelete` példány törlésére irányuló hívás.
    * A `GraphTopologyDelete` topológia törlésére irányuló hívás.
    * A végső hívás `GraphTopologyList` azt mutatja, hogy a lista már üres.
