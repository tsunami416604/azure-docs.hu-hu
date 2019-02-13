---
title: Váratlan beleegyezést kérő üzenetet, amikor egy alkalmazás bejelentkezik |} A Microsoft Docs
description: Amikor a felhasználó kap jóváhagyásukat kéri az alkalmazás integrálva van az Azure ad-vel, amelyek váratlan hibaelhárítása
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 178a5e4e2a6ed520a6c58ce07a30684b2d1cbc09
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56217292"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Váratlan beleegyezést kérő üzenetet alkalmazáshoz való bejelentkezéskor

Számos olyan alkalmazás, amelyekbe beépül az Azure Active Directory különböző erőforrások engedélyekkel való futtatásához szükséges. Ha ezeket az erőforrásokat is integrálhatók az Azure Active Directoryval, hozzáférési engedélyeket kért az Azure ad-ben hozzájárulási keretrendszer használatával. 

Ennek eredményeképpen a jóváhagyás kérése alatt látható az első alkalommal való használatakor egy alkalmazást, amely gyakran egy egyszeri művelet. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Forgatókönyvek, amelyben a felhasználók látják hozzájárulás utasításokat

További utasításokat a különböző forgatókönyvekben várhatók:

* Az alkalmazás által igényelt engedélykészletet is megváltoztak.

* A felhasználó, aki eredetileg egyezett bele az alkalmazás nem rendszergazda, és most már egy másik (nem rendszergazda jogosultságú) felhasználó használja az alkalmazás első alkalommal.

* A felhasználó, aki eredetileg egyezett bele az alkalmazás-rendszergazda volt, de nem adta beleegyezését az egész munkahely számára olyan nevében.

* Az alkalmazás által használt [növekményes és a dinamikus hozzájárulási](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) hozzájárulást követően először további engedélyek kéréséhez. Ez gyakran használják, amikor egy alkalmazás további választható funkciók szoftverplatformok alapvető működéséhez szükséges engedélyeket igényelnek.

* Jóváhagyás után megkapják kezdetben visszavonták.

* A fejlesztői konfigurálva van az alkalmazás számára szükséges a jóváhagyásukat kéri, minden alkalommal, amikor használatos (Megjegyzés: Ez nem ajánlott).

## <a name="next-steps"></a>További lépések

-   [Alkalmazások, engedélyek és jóváhagyás az Azure Active Directoryban (1.0-s verziójú végpont)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Hatókörök, engedélyek és jóváhagyás az Azure Active Directoryban (v2.0-végpontra)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


