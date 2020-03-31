---
title: Az Azure Active Directory fejlesztőknek (1.0-s v1.0) – áttekintés
description: Ez a cikk áttekintést nyújt a Microsoft munkahelyi és iskolai fiókokban való bejelentkezésről az Azure Active Directory 1.0-s verzióbeli végpontjának és platformjának használatával.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ade350c91ebd2f3a68b52011e598f739a14c220f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154491"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Az Azure Active Directory fejlesztőknek (1.0-s v1.0) – áttekintés

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Az Azure Active Directory (Azure AD) egy olyan felhőalapú identitásszolgáltatás, amely lehetővé teszi a fejlesztők számára az olyan alkalmazások létrehozását, amelyek biztosítják a munkahelyi vagy iskolai Microsoft-fiókkal rendelkező felhasználók biztonságos bejelentkeztetését. Az Azure AD egyaránt támogatja egybérlős, üzletági (LOB) alkalmazások, illetve a több-bérlős alkalmazások fejlesztését. Az alapszintű bejelentkezés mellett az Azure AD lehetővé teszi az alkalmazások számára, hogy Microsoft API-kat (pl. [Microsoft Graph](https://docs.microsoft.com/graph/overview)) és az Azure AD platformon alapuló egyéni API-kat egyaránt hívhassanak. Ebben a dokumentációban megtekintheti, hogyan történik az Azure AD-támogatás hozzáadása az alkalmazásához iparági szabvány szerinti protokollok (pl. OAuth2.0 és OpenID Connect) használatával.

> [!NOTE]
> A lap tartalmának nagy része a v1.0-s végpontra és platformra összpontosít, amely csak a Microsoft munkahelyi vagy iskolai fiókokat támogatja. Ha fogyasztói vagy személyes Microsoft-fiókokat szeretne bejelentkezni, tekintse meg a [2.0-s verzióvégpontján és platformján](../develop/v2-overview.md)található információkat. A 2.0-s verziós végpont egységes fejlesztői élményt nyújt az összes Microsoft-identitásba bejelentkezni kívánó alkalmazások számára.

| | |
| --- | --- |
|[Hitelesítési alapok](v1-authentication-scenarios.md) | Bevezetés az Azure AD-vel történő hitelesítés használatába. |
|[Alkalmazástípusok](app-types.md) | Áttekintés az Azure AD által támogatott hitelesítési forgatókönyvekről. |
| | |

## <a name="get-started"></a>Bevezetés

A v1.0-s rövid útmutatók és oktatóanyagok végigvezeti az azure AD hitelesítési könyvtár (ADAL) SDK használatával egy alkalmazás létrehozása a preferált platformon. Az első lépésekhez tekintse meg a **v1.0-s rövid útmutatókat** és **a v1.0-s oktatóanyagokat** a [Microsoft identity platformon (Azure Active Directory a fejlesztőknek).](index.yml)

## <a name="how-to-guides"></a>Útmutatók

Tekintse meg a **v1.0 útmutató részletes** információkat és forgatókönyveket a leggyakoribb feladatok az Azure AD-ben.

## <a name="reference-topics"></a>Referencia-témakörök

A következő cikkekben az API-król, a protokollüzenetekről és az Azure AD által használt kifejezésekről találhat részletes információkat.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Hitelesítési tárak (ADAL)](active-directory-authentication-libraries.md)   | Áttekintés az Azure AD által támogatott kódtárakról és SDK-król. |
| [Kódminták](sample-v1-code.md)                                  | Az Azure AD összes kódmintáját tartalmazó lista. |
| [Szószedet](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)                                      | A jelen dokumentációban használt fogalmak terminológiája és meghatározásai. |
|  |  |

## <a name="videos"></a>Videók

Tekintse meg [az Azure Active Directory fejlesztői platform videóit](videos.md) az új Microsoft-identitásplatformra való migráláshoz.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
