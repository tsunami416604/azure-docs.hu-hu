---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694232"
---
## <a name="provide-feedback-to-the-user"></a>Visszajelzés küldése a felhasználó számára

A munkamenet frissített eseményének kezelésére kódot írhat. Ez az esemény minden alkalommal aktiválódik, amikor a munkamenet javítja a környezetének megértését. Ezzel lehetővé teszi a következőket:

- A `UserFeedback` osztály használatával visszajelzést adhat a felhasználónak, amikor az eszköz áthelyezi, és a munkamenet frissíti a környezetének ismeretét. Ehhez
- Határozza meg, hogy a térbeli horgonyok létrehozásához milyen ponton van elég nyomon követett térbeli adatmennyiség. Ezt `ReadyForCreateProgress` vagy `RecommendedForCreateProgress`alapján állapíthatja meg. Ha `ReadyForCreateProgress` meghaladja az 1-et, elegendő mennyiségű adattal rendelkezünk a Felhőbeli térbeli horgonyok mentéséhez, de azt javasoljuk, hogy várjon, amíg a `RecommendedForCreateProgress` meghaladja az 1-et.
