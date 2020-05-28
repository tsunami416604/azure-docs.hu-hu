---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/2/2019
ms.author: crtreasu
ms.openlocfilehash: 7e7825e8247e78cbc0c0e9e22bdbd9326939e0a8
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83998120"
---
A következő lépés az alkalmazás konfigurálása a fiók azonosítójának és a fiók kulcsának használatára. [A térbeli horgonyok erőforrásának beállításakor](#create-a-spatial-anchors-resource)egy szövegszerkesztőbe másolta őket.

A **projekt** ablaktáblán navigáljon a következőhöz: `Assets\AzureSpatialAnchors.SDK\Resources` . Válassza a(z) `SpatialAnchorConfig` lehetőséget. Ezután a **felügyelő** ablaktáblán adja meg a `Account Key` értéket a `Spatial Anchors Account Key` és a `Account ID` értékként a következőhöz: `Spatial Anchors Account Id` .

Ezután nyissa meg a következőt: `SpatialAnchorManager.cs` . Keresse meg `CreateSessionAsync()` és adja hozzá a következő sort a fiók tartományba való behelyettesítésével a korábbi verziók közül: `session.Configuration.AccountDomain = "MyAccountDomain";` . Ezt a sort közvetlenül a Megjegyzés előtt is hozzáadhatja `// Configure authentication` .
