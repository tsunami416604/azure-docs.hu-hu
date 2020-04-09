---
title: Azure AD v2 Windows asztali első lépések – Config
description: Hogyan kaphat egy Windows Asztali .NET (XAML) alkalmazás egy hozzáférési jogkivonatot, és hogyan hívhat ja meg az Azure Active Directory v2-végpontja által védett API-t.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: d82f9beecb1b558fca094c31f8c6718c990debd1
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885769"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Az alkalmazás regisztrációs adatainak hozzáadása az alkalmazáshoz
Ebben a lépésben hozzá kell adnia az alkalmazásazonosítót a projekthez.

1.  Nyissa `App.xaml.cs` meg és cserélje `ClientId` ki a következő sort:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Mi a következő lépés

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
