---
title: Az Azure Active Directory v2.0-végpont |} A Microsoft Docs
description: Bevezetés a Microsoft Account és az Azure Active Directory-bejelentkezés alkalmazások létrehozásához.
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
ms.openlocfilehash: 8cf592ddd2c4a68e15856d968b77813604713b78
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592040"
---
# <a name="sign-in-microsoft-account-and-azure-active-directory-users-in-a-single-application"></a>Jelentkezzen be Microsoft-Account és az Azure Active Directory a felhasználók egy adott alkalmazásban
Múltbeli időpont az alkalmazásfejlesztők számára személyes Microsoft-fiókot is támogatja, és a munkahelyi fiókok Azure Active Directoryból kellett két külön rendszerekkel való integrációt segítik. Az Azure Active Directory (Azure AD) v2.0-végpont egy új hitelesítési API-verziót, amely leegyszerűsíti a folyamatot mutatja be. Az Azure AD v2.0-végpont be mindkét típusú fiókok olyan egységes integrációs segítségével teszi lehetővé. Az Azure AD v2.0-végpont használó alkalmazások is használhatnak fel, a REST API-kat a [Microsoft Graph API](https://graph.microsoft.io) mindkét típusú fiók használatával.

## <a name="getting-started"></a>Első lépések
Válassza ki kedvenc platformjának használatával a Microsoft egy olyan alkalmazás létrehozásához az alábbi listáról nyissa meg a forrás-kódtárakat és keretrendszereket. Az OAuth 2.0 és OpenID Connect protokollok segítségével üzenetek küldése és fogadása protokoll közvetlenül a-hitelesítési tár használata nélkül.
<br />

[!INCLUDE [Azure AD v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-azure-ad-v20-endpoint"></a>További információ az Azure AD v2.0-végpont
Ismerje meg mi mindent az Azure AD v2.0-végpont:

* Fedezze fel a [típusú hozhat létre az Azure AD v2.0-végpont az alkalmazások](v2-app-types.md).
* Megismerheti a [korlátozások, korlátozások és megkötések](active-directory-v2-limitations.md) együtt az Azure AD v2.0-végpont.
* Ebben a videóban áttekintést az Azure AD v2.0-végpont:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>További források
Ismerkedés az Azure AD v2.0-végpont platform kapcsolatos részletes információk:

* [Az Azure AD v2.0 protokoll hivatkozhat.](active-directory-v2-protocols.md)
* [Az Azure AD v2.0-jogkivonatok referencia](v2-id-and-access-tokens.md)
* [Hitelesítési tárak az Azure AD v2.0-hivatkozás](reference-v2-libraries.md)
* [Hatókörök és jóváhagyás az Azure AD v2.0-végpont](v2-permissions-and-consent.md)
* [A Microsoft Graph API](https://graph.microsoft.io)

> [!NOTE]
> Ha csak kell bejelentkezni az Azure Active Directory munkahelyi és iskolai fiókok, kezdje a [az Azure AD fejlesztői útmutató](azure-ad-developers-guide.md). Az Azure AD v2.0-végpont a fejlesztőknek szól, akik kifejezetten be kell jelentkeznie személyes Microsoft-fiókok célja.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
