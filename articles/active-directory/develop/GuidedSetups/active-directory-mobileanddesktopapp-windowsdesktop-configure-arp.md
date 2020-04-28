---
title: Azure AD v2 Windows asztali Első lépések – konfiguráció
description: Egy Windowsos asztali .NET-(XAML-) alkalmazás hozzáférési jogkivonatának beszerzése és egy Azure Active Directory v2-végpont által védett API meghívása.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885769"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Adja hozzá az alkalmazás regisztrációs adatait az alkalmazáshoz
Ebben a lépésben hozzá kell adnia az alkalmazás azonosítóját a projekthez.

1.  Nyissa meg `App.xaml.cs` és cserélje le a `ClientId` -t tartalmazó sort:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Mi a következő lépés

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
