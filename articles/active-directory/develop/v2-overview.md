---
title: A 2.0-s verzió bemutatása | Azure
description: Ez a cikk a 2.0-s verziójú végpontot és platformot ismerteti.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ecb95f0440751a6cdbf81dbf02c62bed6b5e780b
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51286690"
---
# <a name="about-v20"></a>A 2.0-s verzió bemutatása

A 2.0-s verziójú végpont és platform eddig előzetes verzióban voltak elérhetők, és folyamatos fejlesztés alatt álltak. A mai naptól a JavaScript egyoldalas alkalmazásit (SPA) érintő forgatókönyvek funkcionális szempontból elkészültek. Ezennel meghívjuk Önt, hogy készítsen böngészőalapú alkalmazásokat az MSAL.js segítségével, és küldjön nekünk visszajelzéseket, hogy az előzetes verzió állapotát általánosan elérhetőre (GA) frissíthessük.

> [!NOTE]
> Az MSAL Android-, iOS- és .NET-alapú verzióinak bizonyos funkciói továbbra is fejlesztés alatt állnak. Ezeket is használhatja alkalmazások létrehozásához, majd visszajelzéseket küldhet nekünk.

Az Azure Portal fejlesztői élménye jelentősen frissült: mostantól az összes, ADAL vagy MSAL használatával készült alkalmazást tartalmazza, és könnyebben használható.

Korábban, ha a fejlesztők azt akarták, hogy az alkalmazásuk a személyes Microsoft-fiókokat és a munkahelyi Azure Active Directory (Azure AD)-fiókokat is, akkor ehhez két külön rendszert kellett integrálniuk. A 2.0-s verziójú végpont és platform egy olyan hitelesítési API-verziót biztosít, amely leegyszerűsíti ezt a folyamatot. Egyetlen integrációval lehetővé teszi a bejelentkezést mindkét fajta fiók használatával. A 2.0-s végpontot használó alkalmazások [Microsoft Graph API](https://developer.microsoft.com/graph) REST API-jait is használhatják bármelyik típusú fiókkal.

## <a name="getting-started"></a>Első lépések

Válassza ki a kedvenc platformját az alábbi listából, és hozzon létre egy alkalmazást a Microsoft nyílt forráskódtáraival és keretrendszereivel. Az OAuth 2.0 és az OpenID Connect protokollt is használhatja protokollüzenetek közvetlen küldéséhez és fogadásához, hitelesítési kódtár használata nélkül.

[!INCLUDE [v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-v20-endpoint-and-platform"></a>További információk a 2.0-s verziójú végpontról és platformról

Ismerkedjen meg részletesebben az Azure AD 2.0-s verziójú végpont által kínált lehetőségekkel:

* Ismerje meg, [milyen típusú alkalmazásokat hozhat létre az Azure AD 2.0-s verziójú végponttal](v2-app-types.md).
* Ismerje meg az Azure AD 2.0-s verziójú végpont [korlátozásait és megkötéseit](active-directory-v2-limitations.md).
* Tekintse meg ezt a videót az Azure AD 2.0-s verziójú végpont áttekintéséhez:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>További források

Részletes információk a 2.0-s verzióról:

* [A 2.0-s verzió protokolljainak referenciája](active-directory-v2-protocols.md)
* [Hozzáférési jogkivonatok referenciája](access-tokens.md)
* [Azonosító jogkivonatok referenciája](id-tokens.md)
* [A 2.0-s verzió hitelesítési kódtárainak referenciája](reference-v2-libraries.md)
* [Engedélyek és jóváhagyás a 2.0-s verzióban](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)

> [!NOTE]
> Ha csak az Azure Active Directoryban tárolt munkahelyi vagy iskolai fiókokra van szüksége a bejelentkezéshez, kezdje az [Azure AD fejlesztői útmutatójának](v1-overview.md) áttekintésével. A 2.0-s verziójú végpontot azoknak a fejlesztőknek szánjuk, akiknek kifejezetten a személyes Microsoft-fiókokkal való bejelentkezésre van szükségük.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
