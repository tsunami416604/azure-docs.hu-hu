---
title: Hitelesítés az Azure ad-ben a nemzeti felhőkben
description: További információ az országos felhők regisztrációjának és hitelesítésének végpontok alkalmazást.
services: active-directory
documentationcenter: ''
author: negoe
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/20/2018
ms.author: negoe
ms.reviewer: negoe,andret,saeeda,CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a487c233e7bb71f05c6f2181d9c822ca508a583c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56192948"
---
# <a name="national-clouds"></a>Országos felhőkörnyezetek

Országos felhők (más néven a független felhőkben) az Azure fizikailag izolált példánya. Győződjön meg arról, hogy adatok fizikai tárolási helye, adatszuverenitási és megfelelőségi követelmények összes régió megfelel a földrajzi határokon belüli Azure ezekben a régiókban lettek kialakítva.

Azure Active Directory globális felhő, beleértve a következő országos felhők üzemel:  

- Amerikai Egyesült Államok kormánya által használt Azure
- Azure Germany
- Azure China 21Vianet

Országos felhők, mint a globális Azure egyedi és eltérő környezetben. Ezért fontos figyelembe venni néhány fontos eltérés az ezekben a környezetekben, például alkalmazásokat regisztrálni, -jogkivonatok beszerzésének és végpontok konfigurálása az alkalmazás fejlesztése során.

## <a name="app-registration-endpoints"></a>Alkalmazás regisztrációs végpontok

Egy külön Azure-portálon az országos felhők mindegyike van. Alkalmazások integrálása az országos felhőben a Microsoft Identity Platform, szükségesek az alkalmazás regisztrálásához külön-külön az egyes környezetben az Azure Portalon.

A következő táblázat felsorolja az alap URL-címeket kell regisztrálni egy alkalmazást, minden egyes országos felhőben használt Azure Active Directory (Azure AD-) végpontjait.

| Országos felhőben | Az Azure AD-portál végpontja
| --- | --- |
| Azure ad-ben az USA kormányzatának |https://portal.azure.us
|Az Azure AD-Németország |https://portal.microsoftazure.de
|Az Azure AD Kínában a 21Vianet által üzemeltetett |https://portal.azure.cn
|Az Azure AD (globális szolgáltatás)|https://portal.azure.com 

## <a name="azure-ad-authentication-endpoints"></a>Az Azure AD hitelesítési végpontjai

Az országos felhők minden környezetben külön-külön felhasználóhitelesítést, és külön hitelesítés végpontokat.

A következő táblázat felsorolja az alap URL-címek nemzeti felhőnként jogkivonatok beszerzésére használható Azure Active Directory (Azure AD-) végpontjait.

| Országos felhőben | Az Azure AD-hitelesítési végpont
| --- | --- |
| Azure ad-ben az USA kormányzatának |`https://login.microsoftonline.us`
|Az Azure AD-Németország| `https://login.microsoftonline.de`
|Az Azure AD Kínában a 21Vianet által üzemeltetett | `https://login.chinacloudapi.cn`
|Az Azure AD (globális szolgáltatás)|`https://login.microsoftonline.com`

- Az Azure AD engedélyezési vagy a token-végpontokra irányuló kéréseket a megfelelő régióspecifikus alap URL-cím használatával kell létrehozni. Ha például az Azure Germany:

  - Engedélyezési közös végpont `https://login.microsoftonline.de/common/oauth2/authorize`.
  - Token közös végpont `https://login.microsoftonline.de/common/oauth2/token`.

- Egybérlős alkalmazások, cserélje le az előző URL-címeket a bérlő Azonosítóját vagy nevét, a közös például `https://login.microsoftonline.de/contoso.com`.

>[!NOTE]
> A [az Azure AD v2.0-engedélyezési]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) és jogkivonat-végpont csak a globális szolgáltatás érhetők el. Még nem támogatott az országos felhőben üzemelő példányok esetében ez.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Megtudhatja, hogyan hívhat meg a Microsoft Graph API-k az országos felhőkörnyezet Ugrás [országos felhőben a Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/deployments).



>[!IMPORTANT]
Egyes szolgáltatások és funkciók, amelyek az adott régióban, a globális szolgáltatás nem érhető el az országos felhők mindegyikét. Ismerje meg, milyen szolgáltatások érhetők el, lépjen a [elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [Azure Government](https://docs.microsoft.com/azure/azure-government/).
- Tudjon meg többet [Azure China 21Vianet](https://docs.microsoft.com/azure/china/).
- Tudjon meg többet [Azure Germany](https://docs.microsoft.com/azure/germany/).
- További információ a [az Azure AD hitelesítési alapok](authentication-scenarios.md).
