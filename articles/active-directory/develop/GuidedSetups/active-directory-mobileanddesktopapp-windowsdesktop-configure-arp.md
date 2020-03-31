---
title: Azure AD v2 Windows asztali első lépések – Konfigurációs | Microsoft dokumentumok
description: Hogyan kaphat egy Windows Asztali .NET (XAML) alkalmazás egy hozzáférési jogkivonatot, és hogyan hívhat ja meg az Azure Active Directory v2-végpontja által védett API-t.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2020
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 4c757b52e7378bffe4a4954022b131b772a337cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897684"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Az alkalmazás regisztrációs adatainak hozzáadása az alkalmazáshoz
Ebben a lépésben hozzá kell adnia az alkalmazásazonosítót a projekthez.

1.  Nyissa `App.xaml.cs` meg és cserélje `ClientId` ki a következő sort:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Mi a következő lépés

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
