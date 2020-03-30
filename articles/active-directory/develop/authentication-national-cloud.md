---
title: Az Azure AD hitelesítése & nemzeti felhőknek | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg az alkalmazásregisztrációt és a hitelesítési végpontokat a nemzeti felhőkhöz.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: negoe
ms.reviewer: negoe,celested
ms.custom: aaddev
ms.openlocfilehash: 20a053369149dc29d6485c49bb091a75bb9fb591
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262996"
---
# <a name="national-clouds"></a>Nemzeti felhők

A nemzeti felhők fizikailag elszigetelt Azure-példányok. Az Azure ezen régiói úgy vannak kialakítva, hogy az adatok tárolási, szuverenitási és megfelelőségi követelményeiföldrajzi határokon belül teljesüljenek.

A globális felhővel együtt az Azure Active Directory (Azure AD) a következő nemzeti felhőkben van telepítve:  

- Azure Government
- Azure Germany
- Azure China 21Vianet

A nemzeti felhők egyediek, és külön környezetben vannak az Azure globális környezetétől. Fontos, hogy tisztában legyen ek a legfontosabb különbségek, miközben az alkalmazás fejlesztése az ilyen környezetekben. A különbségek közé tartozik az alkalmazások regisztrálása, jogkivonatok beszerzése és a végpontok konfigurálása.

## <a name="app-registration-endpoints"></a>Alkalmazásregisztrációs végpontok

A nemzeti felhők mindegyikéhez külön Azure-portál tartozik. Az alkalmazások integrálásához a Microsoft identitásplatform egy nemzeti felhőben, akkor külön regisztrálnia kell az alkalmazást külön-külön minden Azure-portálon, amely a környezetre jellemző.

Az alábbi táblázat felsorolja az Azure AD-végpontok alap URL-címeket az egyes nemzeti felhőkhöz használt alkalmazás regisztrálásához.

| Nemzeti felhő | Az Azure AD portal végpontja |
|----------------|--------------------------|
| Azure AD az Egyesült Államok kormánya számára | `https://portal.azure.us` |
| Azure AD Németország | `https://portal.microsoftazure.de` |
| Az Azure AD China által üzemeltetett 21Vianet | `https://portal.azure.cn` |
| Azure AD (globális szolgáltatás) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Azure AD hitelesítési végpontok

Az összes nemzeti felhő minden környezetben külön-külön hitelesíti a felhasználókat, és külön hitelesítési végponttal rendelkezik.

Az alábbi táblázat felsorolja az azure-beli AD-végpontok alap URL-címeit, amelyek az egyes nemzeti felhők jogkivonatok beszerzéséhez szolgálnak.

| Nemzeti felhő | Azure AD hitelesítési végpont |
|----------------|-------------------------|
| Azure AD az Egyesült Államok kormánya számára | `https://login.microsoftonline.us` |
| Azure AD Németország| `https://login.microsoftonline.de` |
| Az Azure AD China által üzemeltetett 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (globális szolgáltatás)| `https://login.microsoftonline.com` |

Az Azure AD-hitelesítésvagy token-végpontok az Azure AD-engedélyezési vagy token-végpontok a megfelelő régió-specifikus alap URL-cím használatával. Például az Azure Germany esetében:

  - Az engedélyezési közös `https://login.microsoftonline.de/common/oauth2/authorize`végpont a.
  - A token közös `https://login.microsoftonline.de/common/oauth2/token`végpontja.

Egybérlős alkalmazások esetén cserélje le a "közös" az előző URL-címek a bérlőazonosító vagy név. Például: `https://login.microsoftonline.de/contoso.com`.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Ha meg szeretné tudni, hogyan hívhatja meg a Microsoft Graph API-kat nemzeti felhőkörnyezetben, látogasson el [a Microsoft Graph nemzeti felhőalapú környezetben című verzióba.](https://developer.microsoft.com/graph/docs/concepts/deployments)

> [!IMPORTANT]
> Előfordulhat, hogy a globális szolgáltatás bizonyos régióiban található bizonyos szolgáltatások és szolgáltatások nem érhetők el az összes nemzeti felhőben. Ha meg szeretné tudni, hogy milyen szolgáltatások érhetők el, látogasson el a [Termékek régiónként elérhető](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Ha meg szeretné tudni, hogyan hozhat létre alkalmazást a Microsoft identity platform használatával, kövesse a [Microsoft Hitelesítési könyvtár (MSAL) oktatóanyagát.](msal-national-cloud.md) Pontosabban, ez az alkalmazás bejelentkezik egy felhasználó, és kap egy hozzáférési jogkivonatot a Microsoft Graph API-t.

## <a name="next-steps"></a>További lépések

További információk:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)
- [Az Azure AD hitelesítésének alapjai](authentication-scenarios.md)
