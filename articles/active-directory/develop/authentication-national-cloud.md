---
title: Hitelesítés az Azure ad-ben a nemzeti felhőkben
description: További információ az országos felhők regisztrációjának és hitelesítésének végpontok alkalmazást.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: negoe
ms.reviewer: negoe,andret,saeeda
ms.custom: aaddev
ms.openlocfilehash: 866a86178d66b7b4af069d684e4eb56c12db47ca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982001"
---
# <a name="national-clouds"></a>Országos felhők

Országos felhők (más néven a független felhőkben) az Azure fizikailag izolált példánya. Győződjön meg arról, hogy adatok fizikai tárolási helye, adatszuverenitási és megfelelőségi követelmények összes régió megfelel a földrajzi határokon belüli Azure ezekben a régiókban lettek kialakítva.

Azure Active Directory globális felhő, beleértve a következő országos felhők üzemel:  

- Amerikai Egyesült Államok kormánya által használt Azure
- Azure Germany
- Azure China 21Vianet

## <a name="app-registration-endpoints"></a>Alkalmazás regisztrációs végpontok

A következő táblázat felsorolja az alap URL-címeket kell regisztrálni egy alkalmazást, minden egyes országos felhőben használt Azure Active Directory (Azure AD-) végpontjait.

| Országos felhőben | Az Azure AD-portál végpontja
| --- | --- |
| Azure ad-ben az USA kormányzatának |https://portal.azure.us
|Az Azure AD-Németország |https://portal.microsoftazure.de
|Az Azure AD Kínában a 21Vianet által üzemeltetett |https://portal.azure.cn
|Az Azure AD (globális szolgáltatás)|https://portal.azure.com

## <a name="azure-ad-authentication-endpoints"></a>Az Azure AD hitelesítési végpontjai

Az alábbi táblázat az alap URL-címek az Azure Active Directory (Azure AD) végpontokhoz jogkivonatok beszerzéséhez használja a Microsoft Graph meghívásához nemzeti felhőnként.

| Országos felhőben | Az Azure AD-hitelesítési végpont
| --- | --- |
| Azure ad-ben az USA kormányzatának |`https://login.microsoftonline.us`
|Az Azure AD-Németország| `https://login.microsoftonline.de`
|Az Azure AD Kínában a 21Vianet által üzemeltetett | `https://login.chinacloudapi.cn`
|Az Azure AD (globális szolgáltatás)|`https://login.microsoftonline.com`

Az Azure AD engedélyezési vagy a token-végpontokra irányuló kéréseket a megfelelő régióspecifikus alap URL-cím használatával kell létrehozni. Ha például Németország: esetén

- Az általános engedélyezési végpont `https://login.microsoftonline.de/common/oauth2/authorize`
- Token közös végpont `https://login.microsoftonline.de/common/oauth2/token` 

Egybérlős alkalmazások cserélje le a fenti URL-címeket a bérlő azonosítóját vagy nevét, a közös például `https://login.microsoftonline.de/contoso.com`

>[!NOTE]
> A [az Azure AD v2.0-engedélyezési]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) és jogkivonat-végpont csak a globális szolgáltatás érhetők el. Még nem támogatott az országos felhőben üzemelő példányok esetében ez.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- Tudjon meg többet [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- Tudjon meg többet [Azure Germany](https://docs.microsoft.com/azure/germany/)
- További információ a [az Azure AD-hitelesítés alapjai](authentication-scenarios.md)
- Tudjon meg többet [országos felhőben a Microsoft Graph-telepítés](https://developer.microsoft.com/graph/docs/concepts/deployments)