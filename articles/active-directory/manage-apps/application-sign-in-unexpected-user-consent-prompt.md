---
title: Váratlan beleegyezés kérése egy alkalmazásba való bejelentkezéskor | Microsoft Docs
description: Az Azure AD-vel integrált, nem várt alkalmazásokra vonatkozó jóváhagyást kérő felhasználó értesítése
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa74424dddd7577eb942f72f038f5bd56854abac
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94648191"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Váratlan beleegyezés kérése egy alkalmazásba való bejelentkezéskor

Számos Azure Active Directory integrációt igénylő alkalmazásnak a futtatásához különböző erőforrásokhoz van szükségük. Ha ezek az erőforrások integrálva vannak a Azure Active Directoryba, a hozzáféréshez szükséges engedélyek az Azure AD-beli engedélyezési keretrendszer használatával is megtalálhatók. 

Ennek eredményeként a rendszer az alkalmazás első használatakor egy hozzájárulási kérést jelenít meg, amely gyakran egyszeri művelet. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Olyan forgatókönyvek, amelyekben a felhasználók a hozzájárulási kéréseket látják

További kérések várhatók különböző helyzetekben:

* Az alkalmazás által igényelt engedélyek halmaza megváltozott.

* A felhasználó, aki eredetileg beleegyezett az alkalmazásba, nem rendszergazda, és most már egy másik (nem rendszergazda) felhasználó használja az alkalmazást az első alkalommal.

* A felhasználó, aki eredetileg beleegyezett az alkalmazásba, rendszergazdai jogosultságot kapott, de a teljes szervezet nevében nem járult hozzá.

* Az alkalmazás [növekményes és dinamikus beleegyezik](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) , hogy a beleegyező engedély megadása után további engedélyeket kérjen. Ezt gyakran akkor érdemes használni, ha egy alkalmazás opcionális funkciói további engedélyek megadását igénylik az alapkonfiguráció működéséhez szükségeseken túl.

* A beleegyezést a kezdeti engedélyezés után visszavonták.

* A fejlesztő úgy konfigurálta az alkalmazást, hogy minden használatkor megkövetelje a beleegyezés kérését (Megjegyzés: ez nem ajánlott eljárás).

## <a name="next-steps"></a>Következő lépések

-   [Alkalmazások, engedélyek és beleegyezett Azure Active Directory (v 1.0 végpont)](../develop/quickstart-register-app.md)

-   [Hatókörök, engedélyek és beleegyezett a Azure Active Directory (v 2.0-végpont)](../develop/v2-permissions-and-consent.md)