---
ms.openlocfilehash: 46e8609be2ec57600e666ef9dab33eae2900e1ce
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88690991"
---
1. A Visual Studio Code-ban lépjen az *src/Edge*elemre. Ekkor megjelenik a *. env* fájl és néhány központi telepítési sablon fájl.

    A központi telepítési sablon a peremhálózati eszköz központi telepítési jegyzékére hivatkozik, ahol egyes tulajdonságok esetében változókat kell használni. A *. env* fájl tartalmazza a változók értékeit.
1. Lépjen a *src/Cloud-to-Device-Console-app* mappába. Itt láthatja a fájl *appsettings.jsét* és néhány további fájlt:

    * ***operations.json*** – a program futtatásához használni kívánt műveletek listája.
    * **Main.py** – a minta programkódja. Ez a kód:
    
      * Betölti az alkalmazás beállításait.
      * Az élő videó Analytics IoT Edge modulban elérhető közvetlen metódusokat hívja meg. A modul segítségével elemezheti az élő videó streameket a [közvetlen metódusok](../../../direct-methods.md)meghívásával.
      * Felfüggeszti a program kimenetét a **terminál** ablakban, és megvizsgálhatja a modul által a **kimeneti** ablakban generált eseményeket.
      * Közvetlen metódusokat hív meg az erőforrások törléséhez.   

