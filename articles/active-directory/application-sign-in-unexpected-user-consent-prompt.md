---
title: Váratlan jóváhagyási kérése az alkalmazás történő bejelentkezéskor |} Microsoft Docs
description: Amikor egy felhasználó láthatja a jóváhagyási kérése integrálva van, akkor nem várt az Azure AD alkalmazás hibaelhárítása
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 096f099c7fc44078cc6c6329b7022613ef09a0d2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36331060"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Váratlan beleegyezést kérő üzenete alkalmazáshoz való bejelentkezéskor

Számos alkalmazás, amelyekbe beépül az Azure Active Directory futtatásához különböző erőforrásokhoz engedély szükséges. Ha ezeket az erőforrásokat is integrálva vannak az Azure Active Directoryval, azok eléréséhez engedélyek, van szükség az Azure AD hozzájárulási keretrendszer használatával. 

Az eredmény hozzájárulás kérése alatt jelenik meg, az első alkalommal való használatakor egy alkalmazás, amely gyakran műveletet egyszer kell elvégezni. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Forgatókönyvek, amelyben a felhasználók látni hozzájárulás kérdések

További útmutatást a különböző forgatókönyvekben várhatók:

* Az alkalmazás által igényelt engedélykészletüket módosult.

* A felhasználó, aki eredetileg átadni kívánt hozzájárult e az alkalmazás nem rendszergazda, és most már egy másik (nem rendszergazda) felhasználó használ az alkalmazás első alkalommal.

* A felhasználó, aki eredetileg átadni kívánt hozzájárult e az alkalmazás egy rendszergazda, de azok nem volt hozzájárulás olyan a teljes szervezet nevében.

* Az alkalmazás által használt [növekményes és dinamikus hozzájárulási](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) után hozzájárulási kezdetben nyert további engedélyek kéréséhez. Ez gyakran használják, amikor az alkalmazás további választható funkciók mellett alapterv működéséhez szükséges engedélyek szükségesek.

* Hozzájárulás kezdetben átadása után visszavonták.

* A fejlesztőnek az alkalmazást egy beleegyezést kérő üzenete megkövetelése minden alkalommal, amikor a rendszer konfigurált (Megjegyzés: Ez nem ajánlott).

## <a name="next-steps"></a>További lépések

-   [Alkalmazások, engedélyek és az Azure Active Directoryban (1.0-s verziójú végpont) hozzájárulás](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Hatókörök, engedélyek és az Azure Active Directoryban (v2.0-végponttól) hozzájárulás](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


