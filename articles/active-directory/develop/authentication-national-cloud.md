---
title: Hitelesítés a Azure Active Directory használatával az országos felhőkben
description: Ismerje meg az alkalmazások regisztrációs és hitelesítési végpontját az országos felhők számára.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: negoe,CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: abaf8c910710000930f4e0ab6200d0ebd75b8d46
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835193"
---
# <a name="national-clouds"></a>Nemzeti felhők

Az országos felhők fizikailag elkülönített Azure-példányok. Az Azure ezen régiói úgy vannak kialakítva, hogy meggyőződjenek arról, hogy az adattárolási, a szuverenitási és a megfelelőségi követelmények a földrajzi határokon belül teljesülnek.

A globális felhővel (Azure Active Directory (Azure AD) együtt a következő nemzeti felhőkben is üzembe helyezhetők:  

- Azure Government
- Azure Germany
- Azure China 21Vianet

Az országos felhők egyediek és egy különálló környezetek az Azure Global-től. Fontos, hogy tisztában legyenek a legfontosabb különbségekkel, miközben az alkalmazás fejlesztése ezekben a környezetekben. A különbségek közé tartozik az alkalmazások regisztrálása, a jogkivonatok beszerzése és a végpontok konfigurálása.

## <a name="app-registration-endpoints"></a>Alkalmazás-regisztrációs végpontok

A nemzeti felhők mindegyike külön Azure Portal. Ha egy nemzeti felhőben szeretné integrálni az alkalmazásokat a Microsoft Identity platformmal, akkor külön regisztrálnia kell az alkalmazást a környezetre jellemző összes Azure Portalban.

A következő táblázat felsorolja az egyes nemzeti felhőhöz tartozó alkalmazások regisztrálásához használt Azure AD-végpontok alap URL-címeit.

| Nemzeti felhő | Azure AD-portál végpont |
|----------------|--------------------------|
| Azure AD az USA kormányzati szerveinek | `https://portal.azure.us` |
| Azure AD Germany | `https://portal.microsoftazure.de` |
| 21Vianet által üzemeltetett Azure AD China | `https://portal.azure.cn` |
| Azure AD (globális szolgáltatás) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Azure AD-hitelesítési végpontok

Minden nemzeti felhő külön hitelesíti a felhasználókat az egyes környezetekben, és külön hitelesítési végpontokkal rendelkezik.

A következő táblázat felsorolja az egyes nemzeti felhők jogkivonatának beszerzéséhez használt Azure AD-végpontok alap URL-címeit.

| Nemzeti felhő | Azure AD-hitelesítési végpont |
|----------------|-------------------------|
| Azure AD az USA kormányzati szerveinek | `https://login.microsoftonline.us` |
| Azure AD Germany| `https://login.microsoftonline.de` |
| 21Vianet által üzemeltetett Azure AD China | `https://login.chinacloudapi.cn` |
| Azure AD (globális szolgáltatás)| `https://login.microsoftonline.com` |

Az Azure AD-engedélyezési vagy jogkivonat-végpontokra irányuló kéréseket a megfelelő régióspecifikus alap URL-cím használatával lehet megalkotni. Az Azure Germany esetében például:

  - Az engedélyezési Common `https://login.microsoftonline.de/common/oauth2/authorize`Endpoint értéke.
  - A token Common `https://login.microsoftonline.de/common/oauth2/token`Endpoint értéke.

Egybérlős alkalmazások esetében az előző URL-címek "Common" értékét cserélje le a bérlői AZONOSÍTÓra vagy a névre. Például: `https://login.microsoftonline.de/contoso.com`.

> [!NOTE]
> Az [Azure ad v 2.0 engedélyezési]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) és jogkivonat-végpontok csak a globális szolgáltatáshoz érhetők el. Nem támogatottak a nemzeti Felhőbeli üzemelő példányok esetében.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Ha meg szeretné tudni, hogyan hívhatja meg a Microsoft Graph API-kat egy nemzeti felhőalapú környezetben, lépjen a [Microsoft Graph a nemzeti Felhőbeli üzembe helyezések](https://developer.microsoft.com/graph/docs/concepts/deployments)területen.

> [!IMPORTANT]
> Előfordulhat, hogy a globális szolgáltatás adott régióiban lévő bizonyos szolgáltatások és szolgáltatások nem érhetők el az összes nemzeti felhőben. Ha szeretné megtudni, hogy mely szolgáltatások érhetők el, lépjen az [elérhető termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Ha meg szeretné tudni, hogyan hozhat létre egy alkalmazást a Microsoft Identity platform használatával, kövesse a [Microsoft Authentication Library (MSAL) oktatóanyagot](msal-national-cloud.md). Pontosabban, az alkalmazás bejelentkezik egy felhasználóval, és hozzáférési jogkivonatot kap a Microsoft Graph API meghívásához.

## <a name="next-steps"></a>További lépések

További információk az alábbiakról:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)
- [Az Azure AD-hitelesítés alapjai](authentication-scenarios.md)
