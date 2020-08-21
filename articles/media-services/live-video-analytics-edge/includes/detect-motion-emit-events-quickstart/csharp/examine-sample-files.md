---
ms.openlocfilehash: 1e3ba4b39baa045f35c232fa97c14bc78d8de5ca
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690982"
---
1. A Visual Studio Code-ban lépjen az *src/Edge*elemre. Ekkor megjelenik a *. env* fájl és néhány központi telepítési sablon fájl.

    A központi telepítési sablon a peremhálózati eszköz központi telepítési jegyzékére hivatkozik, ahol egyes tulajdonságok esetében változókat kell használni. A *. env* fájl tartalmazza a változók értékeit.
1. Lépjen a *src/Cloud-to-Device-Console-app* mappába. Itt láthatja a fájl *appsettings.jsét* és néhány további fájlt:

    * ***C2D-Console-app. csproj*** – a Project fájl a Visual Studio Code-hoz.
    * ***operations.json*** – a program futtatásához használni kívánt műveletek listája.
    * ***Program.cs*** – a minta programkódja. Ez a kód:
    
      * Betölti az alkalmazás beállításait.
      * Az élő videó Analytics IoT Edge modulban elérhető közvetlen metódusokat hívja meg. A modul segítségével elemezheti az élő videó streameket a [közvetlen metódusok](../../../direct-methods.md)meghívásával.
      * Felfüggeszti a program kimenetét a **terminál** ablakban, és megvizsgálhatja a modul által a **kimeneti** ablakban generált eseményeket.
      * Közvetlen metódusokat hív meg az erőforrások törléséhez.   
