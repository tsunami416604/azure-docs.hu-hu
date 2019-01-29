---
title: Információk az 1.0-s verzió |} Az Azure
description: Ez a cikk áttekintést nyújt a Microsoft aláírási munkahelyi és iskolai fiókok az Azure Active Directory 1.0-s verziójú végpontján és a platform használatával.
services: active-directory
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 4b3e7e777abc524e512acb2f9abc9c53ba29146d
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55102464"
---
# <a name="about-v10"></a>Információk az 1.0-s verzió

Az Azure Active Directory (Azure AD) egy olyan felhőalapú identitásszolgáltatás, amely lehetővé teszi a fejlesztők számára az olyan alkalmazások létrehozását, amelyek biztosítják a munkahelyi vagy iskolai Microsoft-fiókkal rendelkező felhasználók biztonságos bejelentkeztetését. Az Azure AD egyaránt támogatja egybérlős, üzletági (LOB) alkalmazások, illetve a több-bérlős alkalmazások fejlesztését. Az alapszintű bejelentkezés mellett az Azure AD lehetővé teszi az alkalmazások számára, hogy Microsoft API-kat (pl. [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/overview)) és az Azure AD platformon alapuló egyéni API-kat egyaránt hívhassanak. Ebben a dokumentációban megtekintheti, hogyan történik az Azure AD-támogatás hozzáadása az alkalmazásához iparági szabvány szerinti protokollok (pl. OAuth2.0 és OpenID Connect) használatával.

> [!NOTE]
> Ezen az oldalon tartalmának nagy része foglalkozik az 1.0-s verziójú végpont és a platform, amely támogatja a csak a Microsoft munkahelyi vagy iskolai fiókokat. Ha a bejelentkezéshez használandó végfelhasználói és személyes Microsoft-fiókok, olvassa el a [v2.0-végpontra és a platform](v2-overview.md). A v2.0-végpont alkalmazásokat, jelentkezzen be az összes Microsoft-identitások szeretné egy egységesített fejlesztői felületet kínál.

| | |
| --- | --- |
|[Hitelesítési alapok](authentication-scenarios.md) | Bevezetés az Azure AD-vel történő hitelesítés használatába. |
|[Alkalmazástípusok](app-types.md) | Áttekintés az Azure AD által támogatott hitelesítési forgatókönyvekről. |
| | |

## <a name="get-started"></a>Bevezetés

Az 1.0-s verziójú útmutatóink és oktatóanyagaink segítségével végigvezetik egy alkalmazást a kívánt platformon, az Azure AD Authentication Library (ADAL) SDK használatával. Tekintse meg a **1.0-s verziójú rövid útmutatók** és **1.0-s verziójú oktatóanyagok** a [Microsoft identitásplatformja (az Azure vonatkozó Active Directory fejlesztők számára)](index.yml) a kezdéshez.

## <a name="how-to-guides"></a>Útmutatók

Tekintse meg a **útmutató végigvezeti a v1.0** részletes adatai, valamint a legáltalánosabb feladatokat az Azure AD-forgatókönyvek esetében.

## <a name="reference-topics"></a>Referencia-témakörök

A következő cikkekben az API-król, a protokollüzenetekről és az Azure AD által használt kifejezésekről találhat részletes információkat.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Hitelesítési tárak (ADAL)](active-directory-authentication-libraries.md)   | Áttekintés az Azure AD által támogatott kódtárakról és SDK-król. |
| [Kódminták](sample-v1-code.md)                                  | Az Azure AD összes kódmintáját tartalmazó lista. |
| [Szószedet](developer-glossary.md)                                      | A jelen dokumentációban használt fogalmak terminológiája és meghatározásai. |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
