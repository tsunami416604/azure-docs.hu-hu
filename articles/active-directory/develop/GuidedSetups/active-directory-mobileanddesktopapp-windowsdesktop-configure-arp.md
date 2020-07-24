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
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87162719"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Adja hozzá az alkalmazás regisztrációs adatait az alkalmazáshoz
Ebben a lépésben hozzá kell adnia az alkalmazás azonosítóját a projekthez.

1.  Nyissa meg `App.xaml.cs` és cserélje le a-t tartalmazó sort `ClientId` :

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Mi a következő lépés

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
