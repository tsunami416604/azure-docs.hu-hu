---
title: Az Azure AD v2 Windows asztali első lépések – Config |} A Microsoft Docs
description: A Windows asztali .NET (XAML) alkalmazások hogyan hozzáférési jogkivonatot kapjon, és a egy API-t védi az Azure Active Directory v2 végpontot.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 47d62b5ea49205a5e639d620d78466e7e4a66bca
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52581408"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Az alkalmazás regisztrációs adatok hozzáadása az alkalmazáshoz
Ebben a lépésben kell hozzáadnia az alkalmazásazonosítót a projekthez.

1.  Nyissa meg `App.xaml.cs` , és cserélje le a sort tartalmazó a `ClientId` együtt:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Mi a következő

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
