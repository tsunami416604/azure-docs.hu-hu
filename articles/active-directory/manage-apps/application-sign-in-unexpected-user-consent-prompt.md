---
title: Váratlan hozzájárulási kérdés, amikor bejelentkezik egy alkalmazásba | Microsoft dokumentumok
description: Hibaelhárítás, ha egy felhasználó olyan azure AD-vel integrált alkalmazás hozzájárulási parancsát látja, amelyet nem várt
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83d043ecef152f977437e21e2caec40d1c40ce0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65781154"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Váratlan hozzájárulási kérdés, amikor bejelentkezik egy alkalmazásba

Az Azure Active Directoryval integrálható alkalmazások futtatásához különböző erőforrásokhoz szükséges engedélyeket igényel. Ha ezek az erőforrások is integrálva vannak az Azure Active Directoryval, az Azure AD-hozzájárulási keretrendszer használatával az azok eléréséhez szükséges engedélyeket kérik. 

Ez azt eredményezi, hogy egy alkalmazás első használatkor megjelenik egy jóváhagyási parancs, amely gyakran egyszeri művelet. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Olyan esetek, amelyekben a felhasználók hozzájárulási utasításokat látnak

További kérések rejthetők különböző esetekben:

* Az alkalmazás által igényelt engedélyek készlete megváltozott.

* Az alkalmazáshoz eredetileg hozzájáruló felhasználó nem volt rendszergazda, és most egy másik (nem rendszergazdai) felhasználó használja először az alkalmazást.

* Az alkalmazáshoz eredetileg hozzájáruló felhasználó rendszergazda volt, de a teljes szervezet nevében nem.

* Az alkalmazás [növekményes és dinamikus beleegyezést](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) használ további engedélyek kéréséhez a hozzájárulás kezdeti megadása után. Ez gyakran akkor használatos, ha egy alkalmazás nem kötelező szolgáltatásai további engedélyeket igényelnek az alapkonfiguráció működéséhez szükséges engedélyeken túl.

* A hozzájárulást az eredeti megadása után visszavonták.

* A fejlesztő úgy konfigurálta az alkalmazást, hogy minden használatkor kérje meg a hozzájárulási kérdést (megjegyzés: ez nem ajánlott eljárás).

## <a name="next-steps"></a>További lépések

-   [Alkalmazások, engedélyek és hozzájárulás az Azure Active Directoryban (1.0-s végpont)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Hatókörök, engedélyek és hozzájárulás az Azure Active Directoryban (2.0-s vagy os végpont)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


