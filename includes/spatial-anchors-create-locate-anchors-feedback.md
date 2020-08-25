---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "76694232"
---
## <a name="provide-feedback-to-the-user"></a>Visszajelzés küldése a felhasználó számára

A munkamenet frissített eseményének kezelésére kódot írhat. Ez az esemény minden alkalommal aktiválódik, amikor a munkamenet javítja a környezetének megértését. Ezzel lehetővé teszi a következőket:

- A `UserFeedback` osztály használatával visszajelzést adhat a felhasználónak, amikor az eszköz áthelyezi, és a munkamenet frissíti a környezetének ismeretét. Ehhez
- Határozza meg, hogy a térbeli horgonyok létrehozásához milyen ponton van elég nyomon követett térbeli adatmennyiség. Ezt a következővel állapíthatja meg: `ReadyForCreateProgress` vagy `RecommendedForCreateProgress` . `ReadyForCreateProgress`Az 1. után a Felhőbeli térbeli horgonyok mentéséhez elegendő mennyiségű adattal rendelkezünk, de azt javasoljuk, hogy várjon, amíg erre `RecommendedForCreateProgress` az 1.
