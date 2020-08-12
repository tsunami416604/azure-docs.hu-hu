---
title: Az alkalmazások jóváhagyásának működése
description: 'További információ az Azure AD-beli engedélyezési keretrendszer működéséről: hogyan használható az Azure AD-alkalmazások fejlesztéséhez'
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.openlocfilehash: 15270a998aff174c04acf2969d984eb022852635
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88117362"
---
# <a name="how-application-consent-works"></a>Az alkalmazások jóváhagyásának működése

Ebből a cikkből megtudhatja, hogyan működik az Azure AD-keretrendszer, így hatékonyabban fejlesztheti alkalmazásait.

## <a name="recommended-documents"></a>Ajánlott dokumentumok

- Általános ismeretek arról, hogy [a beleegyezés hogyan teszi lehetővé az erőforrás-tulajdonosok számára az alkalmazások erőforrásokhoz való hozzáférésének szabályozását](./developer-glossary.md#consent).
- Részletes áttekintést kaphat [arról, hogy az Azure ad-beli engedélyezési keretrendszer hogyan valósítja meg a hozzájárulásukat](./quickstart-register-app.md).
- További részletekért tekintse meg, [Hogyan használhatják a több-bérlős alkalmazások a](./howto-convert-app-to-be-multi-tenant.md) "felhasználó" és a "rendszergazda" jogosultságot a "felhasználói" és "rendszergazdai" engedély megvalósításához, és támogatják a fejlettebb többrétegű alkalmazás-mintákat.
- További részletekért tekintse meg, [Hogyan támogatott a hozzájárulás a OAuth 2,0 protokoll-rétegben az engedélyezési kód engedélyezése folyamat során.](../azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)

## <a name="next-steps"></a>További lépések
[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)