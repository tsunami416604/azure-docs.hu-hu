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
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc9b5cbc4904c6b2238e56742381feb4d2811437
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56195821"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Az alkalmazás regisztrációs adatok hozzáadása az alkalmazáshoz
Ebben a lépésben kell hozzáadnia az alkalmazásazonosítót a projekthez.

1.  Nyissa meg `App.xaml.cs` , és cserélje le a sort tartalmazó a `ClientId` együtt:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Mi a következő

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
