---
title: "Az Azure AD v2 Windows asztali első lépések - Config |} Microsoft Docs"
description: "A Windows asztali .NET (XAML) alkalmazások hogyan szereznie egy hozzáférési jogkivonatot, és a hívja az API-k védi az Azure Active Directory v2 végpont."
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 922fd0e24334eb45995b66dfaaa49d3d4f835e9a
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Az alkalmazás regisztrációs adatok hozzáadása az alkalmazáshoz
Ebben a lépésben kell hozzáadnia az alkalmazásazonosítót a projekthez.

1.  Nyissa meg `App.xaml.cs` , és cserélje le a sort tartalmazó a `ClientId` együtt:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Mi az a következő

[!INCLUDE  [Test and Validate](..\..\..\..\includes\active-directory-develop-guidedsetup-windesktop-test.md)]
