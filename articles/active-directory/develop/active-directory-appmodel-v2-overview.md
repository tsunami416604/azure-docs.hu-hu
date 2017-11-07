---
title: "Az Azure Active Directory v2.0-végponttól |} Microsoft Docs"
description: "Megismerkedhet az alkalmazások a Microsoft Account, mind az Azure Active Directory-bejelentkezés létrehozása."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: a6a7c6bdf3deaee3a3949fe409a7fab6b7664695
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="sign-in-microsoft-account--azure-ad-users-in-a-single-app"></a>Bejelentkezés Microsoft-Account & egyetlen alkalmazást az Azure AD-felhasználók
A múltban két külön rendszer integrálása az alkalmazás fejlesztőjének személyes Microsoft-fiókot is támogatja, és a munkahelyi fiókok az Azure Active Directory számára szükséges.  A **az Azure AD v2.0-végponttól** bevezet egy új hitelesítési API-verzió, amely lehetővé teszi, hogy jelentkezzen be mindkét típusú fiókok használatával egy egyszerű integráció.  A v2.0-végpontra használó alkalmazások is felhasználhat REST API-kat a [Microsoft Graph](https://graph.microsoft.io) bármely típusú fiókot használja.

## <a name="getting-started"></a>Első lépések
Válassza ki előnyben részesített platformját az alábbi listából a nyílt forráskódú szalagtárak & keretrendszerek használata az alkalmazás elkészítésére.  Másik lehetőségként segítségével az OAuth 2.0-s & OpenID Connect-protokoll dokumentációját & küldése közvetlenül egy hitelesítési tár használata nélkül protokoll üzeneteket fogadni.

<br />

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="whats-new"></a>Újdonságok
Itt az információk hasznosak bizonyulnak a Mi & Mi nem lehetséges a v2.0-végponttal ismertetése.

* További tudnivalók a [típusú alkalmazásokat hozhat létre a v2.0-végponttal](active-directory-v2-flows.md).
* Megérteni a [korlátozások, korlátozások és megkötések](active-directory-v2-limitations.md) a v2.0-végponttal.
* Ez az áttekintő videó a v2.0-végpontra a kivétel:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="reference"></a>Referencia
Ezek a hivatkozások akkor lehet hasznos, felderítését lehetővé tevő a platform részletesen:

* [v2.0 protokoll referenciái](active-directory-v2-protocols.md)
* [v2.0 jogkivonat referenciái](active-directory-v2-tokens.md)
* [v2.0 kódtár – dokumentáció](active-directory-v2-libraries.md)
* [Hatókörök és a v2.0-végpontra jóváhagyása](active-directory-v2-scopes.md)
* [A Microsoft Graph](https://graph.microsoft.io)

> [!NOTE]
> Ha csak szeretné jelentkezzen be munkahelyi és iskolai fiókok az Azure Active Directoryból, kell kezdődnie, a [az Azure AD – fejlesztői útmutató](active-directory-developers-guide.md).  A v2.0-végpontra a explicit módon be kell jelentkeznie személyes Microsoft-fiókok fejlesztőknek szól.


[!INCLUDE  [Help and Support Options](../../../includes/active-directory-develop-help-support-include.md)]
