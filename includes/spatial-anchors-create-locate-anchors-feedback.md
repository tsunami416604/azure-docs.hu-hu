---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76694232"
---
## <a name="provide-feedback-to-the-user"></a>Visszajelzés küldése a felhasználónak

Kódot írhat a munkamenet frissített eseményének kezeléséhez. Ez az esemény minden alkalommal aktiválódik, amikor a munkamenet javítja a környezet megértését. Ezzel lehetővé teszi, hogy:

- Az `UserFeedback` osztály segítségével visszajelzést adhat a felhasználónak az eszköz áthelyezésekor, és a munkamenet frissíti a környezet megértését. Ehhez
- Határozza meg, hogy melyik ponton van elég követett térbeli adat térbeli horgonyok létrehozásához. Ezt a vagy `ReadyForCreateProgress` `RecommendedForCreateProgress`a segítségével határozhatja meg. Ha `ReadyForCreateProgress` 1 felett van, elegendő adatunk van egy felhőbeli térbeli horgony mentéséhez, bár azt javasoljuk, hogy várjon, amíg `RecommendedForCreateProgress` 1 fölé emelkedik.
