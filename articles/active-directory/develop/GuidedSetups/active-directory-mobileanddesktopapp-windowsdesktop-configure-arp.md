---
title: Azure AD v2 Windows asztali Első lépések – konfiguráció | Microsoft Docs
description: Egy Windowsos asztali .NET-(XAML-) alkalmazás hozzáférési jogkivonatának beszerzése és egy Azure Active Directory v2-végpont által védett API meghívása.
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
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76897684"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Adja hozzá az alkalmazás regisztrációs adatait az alkalmazáshoz
Ebben a lépésben hozzá kell adnia az alkalmazás azonosítóját a projekthez.

1.  Nyissa meg `App.xaml.cs` és cserélje le a `ClientId`t tartalmazó sort a:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Mi a következő lépés

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
