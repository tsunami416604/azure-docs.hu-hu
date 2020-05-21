---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/2/2019
ms.author: crtreasu
ms.openlocfilehash: e3ef7e7b8f527cde1fcfbb13141fd3a520921267
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673424"
---
A következő lépés az alkalmazás konfigurálása a fiók azonosítójának és a fiók kulcsának használatára. [A térbeli horgonyok erőforrásának beállításakor](#create-a-spatial-anchors-resource)egy szövegszerkesztőbe másolta őket.

A **projekt** ablaktáblán navigáljon a következőhöz: `Assets\AzureSpatialAnchors.SDK\Resources` . Válassza a(z) `SpatialAnchorConfig` lehetőséget. Ezután a **felügyelő** ablaktáblán adja meg a `Account Key` értéket a `Spatial Anchors Account Key` és a `Account ID` értékként a következőhöz: `Spatial Anchors Account Id` .

Ezután nyissa meg a következőt: `SpatialAnchorManager.cs` . Keresse meg `CreateSessionAsync()` és adja hozzá a következő sort a fiók tartományba való behelyettesítésével a korábbi verziók közül: `session.Configuration.AccountId = "MyAccountDomain";` . Ezt a sort közvetlenül a Megjegyzés előtt is hozzáadhatja `// Configure authentication` .
