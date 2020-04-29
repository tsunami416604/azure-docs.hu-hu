---
title: Azure Active Directory fejlesztők számára (v 1.0) – áttekintés
description: Ez a cikk áttekintést nyújt a Microsoft munkahelyi és iskolai fiókjainak a Azure Active Directory v 1.0 végpont és platform használatával történő aláírásáról.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80154491"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Azure Active Directory fejlesztők számára (v 1.0) – áttekintés

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Az Azure Active Directory (Azure AD) egy olyan felhőalapú identitásszolgáltatás, amely lehetővé teszi a fejlesztők számára az olyan alkalmazások létrehozását, amelyek biztosítják a munkahelyi vagy iskolai Microsoft-fiókkal rendelkező felhasználók biztonságos bejelentkeztetését. Az Azure AD egyaránt támogatja egybérlős, üzletági (LOB) alkalmazások, illetve a több-bérlős alkalmazások fejlesztését. Az alapszintű bejelentkezés mellett az Azure AD lehetővé teszi az alkalmazások számára, hogy Microsoft API-kat (pl. [Microsoft Graph](https://docs.microsoft.com/graph/overview)) és az Azure AD platformon alapuló egyéni API-kat egyaránt hívhassanak. Ebben a dokumentációban megtekintheti, hogyan történik az Azure AD-támogatás hozzáadása az alkalmazásához iparági szabvány szerinti protokollok (pl. OAuth2.0 és OpenID Connect) használatával.

> [!NOTE]
> Az oldal tartalmának nagy része a 1.0-s végpontra és platformra koncentrál, amely kizárólag a Microsoft munkahelyi vagy iskolai fiókjait támogatja. Ha felhasználói vagy személyes Microsoft-fiókokat szeretne bejelentkezni, tekintse meg a [v 2.0-végpont és-platform](../develop/v2-overview.md)információit. A v 2.0 végpontja olyan alkalmazások egységes fejlesztői felületét kínálja, amelyek az összes Microsoft-identitást szeretnék bejelentkezni.

| | |
| --- | --- |
|[Hitelesítési alapok](v1-authentication-scenarios.md) | Bevezetés az Azure AD-vel történő hitelesítés használatába. |
|[Alkalmazástípusok](app-types.md) | Áttekintés az Azure AD által támogatott hitelesítési forgatókönyvekről. |
| | |

## <a name="get-started"></a>Bevezetés

A 1.0-s verzióban elérhető gyors útmutatók és oktatóanyagok végigvezetik az alkalmazások az előnyben részesített platformon való létrehozásán az Azure AD Authentication Library (ADAL) SDK használatával. Első lépésként tekintse meg a [Microsoft Identity platform (Azure Active Directory for Developers)](index.yml) **1.0** -s és 1.0-s **verziójának oktatóanyagait** .

## <a name="how-to-guides"></a>Útmutatók

Az Azure AD leggyakoribb feladatainak részletes információit és bemutatóit lásd a **v 1.0 útmutatók** című részben.

## <a name="reference-topics"></a>Referencia-témakörök

A következő cikkekben az API-król, a protokollüzenetekről és az Azure AD által használt kifejezésekről találhat részletes információkat.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Hitelesítési tárak (ADAL)](active-directory-authentication-libraries.md)   | Áttekintés az Azure AD által támogatott kódtárakról és SDK-król. |
| [Kódminták](sample-v1-code.md)                                  | Az Azure AD összes kódmintáját tartalmazó lista. |
| [Szószedet](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)                                      | A jelen dokumentációban használt fogalmak terminológiája és meghatározásai. |
|  |  |

## <a name="videos"></a>Videók

Az új Microsoft Identity platformra történő Migrálás érdekében tekintse meg [Azure Active Directory fejlesztői platform videóit](videos.md) .

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
