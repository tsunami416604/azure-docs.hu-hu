---
title: Az Azure AD v2 Windows asztali első lépések – Config |} A Microsoft Docs
description: A Windows asztali .NET (XAML) alkalmazások hogyan hozzáférési jogkivonatot kapjon, és a egy API-t védi az Azure Active Directory v2 végpontot.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fce464c2351de7d3ef26882d0ab56f11743ea3f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702956"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Az alkalmazás regisztrációs adatok hozzáadása az alkalmazáshoz
Ebben a lépésben kell hozzáadnia az alkalmazásazonosítót a projekthez.

1.  Nyissa meg `App.xaml.cs` , és cserélje le a sort tartalmazó a `ClientId` együtt:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Mi a következő

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
