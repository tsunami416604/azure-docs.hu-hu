---
title: Hitelesítés az Azure Active Directory országos felhőkörnyezetekben
description: További információ az országos felhők regisztrációjának és hitelesítésének végpontok alkalmazást.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: negoe,CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd37366697a9c1f5019d2864e6d81a4dcd02e3a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235491"
---
# <a name="national-clouds"></a>Országos felhők

Országos felhők az Azure fizikailag izolált példánya. Győződjön meg arról, hogy adatok fizikai tárolási helye, adatszuverenitási és megfelelőségi követelmények összes régió megfelel a földrajzi határokon belüli Azure ezekben a régiókban lettek kialakítva.

A globális felhő, beleértve az Azure Active Directory (Azure AD) a következő országos felhők telepítve van:  

- Azure Government
- Azure Germany
- Azure China 21Vianet

Országos felhők egyediek és a egy másik környezetbe globális Azure-ból. Fontos tudni, különbségeket ilyen környezetben az alkalmazás fejlesztése során. Különbségek a következők: alkalmazások regisztrálása, -jogkivonatok beszerzésének és végpontok konfigurálása.

## <a name="app-registration-endpoints"></a>Alkalmazás regisztrációs végpontok

Egy külön Azure-portálon az országos felhők mindegyike van. Alkalmazások integrálása az országos felhőben a Microsoft identitásplatformja, módosítania kell minden egyes Azure-portálon, a környezet jellemző külön-külön az alkalmazás regisztrálásához.

A következő táblázat felsorolja az Azure AD-végpontokhoz kell regisztrálni egy alkalmazást, minden egyes országos felhőben használt alap URL-címek.

| Országos felhőben | Az Azure AD-portál végpontja |
|----------------|--------------------------|
| Azure ad-ben az USA kormányzatának | `https://portal.azure.us` |
| Az Azure AD-Németország | `https://portal.microsoftazure.de` |
| Az Azure AD Kínában a 21Vianet által üzemeltetett | `https://portal.azure.cn` |
| Az Azure AD (globális szolgáltatás) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Az Azure AD hitelesítési végpontjai

Az országos felhők minden környezetben külön-külön felhasználóhitelesítést, és külön hitelesítés végpontokat.

Az alábbi táblázat az alap URL-címek a nemzeti felhőnként jogkivonatok beszerzésére használható az Azure AD-végpontok.

| Országos felhőben | Az Azure AD hitelesítési végpontja |
|----------------|-------------------------|
| Azure ad-ben az USA kormányzatának | `https://login.microsoftonline.us` |
| Az Azure AD-Németország| `https://login.microsoftonline.de` |
| Az Azure AD Kínában a 21Vianet által üzemeltetett | `https://login.chinacloudapi.cn` |
| Az Azure AD (globális szolgáltatás)| `https://login.microsoftonline.com` |

Az Azure AD engedélyezési vagy a token végpontok kérelmeket a megfelelő régióspecifikus alap URL-cím használatával is alkotnak. Ha például az Azure Germany:

  - Engedélyezési közös végpont `https://login.microsoftonline.de/common/oauth2/authorize`.
  - Token közös végpont `https://login.microsoftonline.de/common/oauth2/token`.

Egybérlős alkalmazások cserélje le a "általános" az előző URL-címeket a bérlői azonosító vagy név. Például: `https://login.microsoftonline.de/contoso.com`.

> [!NOTE]
> A [az Azure AD v2.0-engedélyezési]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) és jogkivonat-végpont csak a globális szolgáltatás érhető el. Ezek a használt országos felhőbeli telepítések esetén nem támogatott.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Ismerje meg, hogyan hívhat meg a Microsoft Graph API-k az országos felhőkörnyezet, lépjen a [országos felhőben üzemelő Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/deployments).

> [!IMPORTANT]
> Egyes szolgáltatások és funkciók, amelyek az adott régióban, a globális szolgáltatás nem érhető el az országos felhők mindegyikét. Ismerje meg, milyen szolgáltatások érhetők el, keresse fel [elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Ismerje meg, hogyan hozhat létre egy alkalmazást a Microsoft identity platform használatával, kövesse a [Microsoft-hitelesítési tár (MSAL) oktatóanyag](msal-national-cloud.md). Pontosabban az alkalmazás bejelentkeztetni egy felhasználót, és a Microsoft Graph API meghívása a hozzáférési jogkivonatot kapjon.

## <a name="next-steps"></a>További lépések

További információk:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)
- [Az Azure AD-hitelesítés alapjai](authentication-scenarios.md)
