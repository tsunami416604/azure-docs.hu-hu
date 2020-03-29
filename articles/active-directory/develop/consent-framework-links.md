---
title: Az alkalmazás hozzájárulásának működése | Microsoft dokumentumok
description: További információ arról, hogyan működik az Azure AD-hozzájárulási keretrendszer, amely ből megtudhatja, hogyan használhatja azt az Azure AD-n való alkalmazások fejlesztése során
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.openlocfilehash: 15206f394f63e9373a4a0016588bc1e2e2c65d44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697710"
---
# <a name="how-application-consent-works"></a>Az alkalmazás hozzájárulásának működése

Ez a cikk segít többet megtudni, hogyan működik az Azure AD-hozzájárulási keretrendszer, így hatékonyabban fejlesztheti az alkalmazásokat.

## <a name="recommended-documents"></a>Ajánlott dokumentumok

- Általános ismereteket kaphat [arról, hogy a hozzájárulás hogyan teszi lehetővé az erőforrás-tulajdonos számára az alkalmazás erőforrásokhoz való hozzáférésének szabályozását.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#consent)
- Részletes áttekintést kaphat [arról, hogy az Azure AD-hozzájárulási keretrendszer hogyan valósítja meg a beleegyezést.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- További részletes, megtudhatja, [hogyan egy több-bérlős alkalmazás használhatja a jóváhagyási keretet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) a "felhasználó" és a "rendszergazda" hozzájárulás megvalósításához, támogatva a fejlettebb többrétegű alkalmazásminták.
- További mélységért ismerje meg, [hogyan támogatja a jóváhagyást az OAuth 2.0 protokollréteg az engedélyezési kód engedélyezési folyamat során.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)

## <a name="next-steps"></a>További lépések
[AzureAD-veremtúlcsordulás](https://stackoverflow.com/questions/tagged/azure-active-directory)
