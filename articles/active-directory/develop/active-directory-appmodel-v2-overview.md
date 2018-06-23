---
title: Az Azure Active Directory v2.0-végponttól |} Microsoft Docs
description: A Microsoft Account, mind az Azure Active Directory-bejelentkezés alkalmazások bemutatása.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: celested
ms.reviewer: hirsin, jmprieur, elisol, dastrock
ms.custom: aaddev
ms.openlocfilehash: 1c91c1ed8358f58ab7a4d9a697ec2d7933c4f137
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316738"
---
# <a name="sign-in-microsoft-account-and-azure-active-directory-users-in-a-single-application"></a>Jelentkezzen be a Microsoft Account és az Azure Active Directory felhasználók egy adott alkalmazásban
A múltban az alkalmazásfejlesztők számára személyes Microsoft-fiókot is támogatja, és a munkahelyi fiókok az Azure Active Directory kellett két külön rendszer integrálása. Az Azure Active Directory (Azure AD) v2.0-végponttól bevezet egy új hitelesítési API-verziót, amely leegyszerűsíti ezt a folyamatot. Az Azure AD v2.0-végponttól lehetővé teszi, hogy bejelentkezés mindkét típusú fiókok egyetlen integrációs használatával. Az Azure AD v2.0-végponttól használó alkalmazások is igénybe vehet, a REST API-kat a [Microsoft Graph API](https://graph.microsoft.io) bármely típusú fiókot használva.

## <a name="getting-started"></a>Első lépések
Válassza ki az alábbi listából alkalmazás létrehozásához használja a Microsoft által előnyben részesített platformját nyissa meg a forrás kódtárai és keretrendszerei. Az OAuth 2.0 és az OpenID Connect protokoll használatával üzeneteket küldjön és fogadjon protokoll közvetlenül egy hitelesítési tár használata nélkül.
<br />

[!INCLUDE [Azure AD v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-azure-ad-v20-endpoint"></a>További tudnivalók az Azure AD v2.0-végponttól
Ismerje meg, mi mindent a Azure AD v2.0-végponttal kapcsolatban:

* Fedezze fel az [úgy, hogy az Azure AD v2.0-végponttal rendelkező alkalmazások](active-directory-v2-flows.md).
* Megérteni a [korlátozások, korlátozások és megkötések](active-directory-v2-limitations.md) a Azure AD v2.0-végponttal.
* A videó az Azure AD v2.0-végponttól áttekintése:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>További források
Fedezze fel az Azure AD v2.0 végpont platform kapcsolatos részletesebb információk:

* [Az Azure AD v2.0 protokoll hivatkozik](active-directory-v2-protocols.md)
* [Az Azure AD v2.0 jogkivonatok referencia](active-directory-v2-tokens.md)
* [Az Azure AD v2.0-hitelesítési kódtárakkal hivatkozik](active-directory-v2-libraries.md)
* [Hatókörök és az Azure AD v2.0-végponttól jóváhagyása](active-directory-v2-scopes.md)
* [A Microsoft Graph API](https://graph.microsoft.io)

> [!NOTE]
> Ha csak kell jelentkezzen be munkahelyi és iskolai fiókok az Azure Active Directoryból, kezdje a [az Azure AD – fejlesztői útmutató](active-directory-developers-guide.md). Az Azure AD v2.0-végpontra a explicit módon be kell jelentkeznie személyes Microsoft-fiókok fejlesztőknek szól.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
