---
title: Hogyan kell engedélyeket egy egyénileg fejlesztett alkalmazásba |} A Microsoft Docs
description: Hogyan kell engedélyeket az egyénileg fejlesztett alkalmazásba, az Azure AD portálon vagy egy URL-cím paraméter használatával
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 75beeb35b740bb126fff905f4cfa5a0b455e025e
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365243"
---
# <a name="how-to-grant-permissions-to-a-custom-developed-application"></a>Hogyan kell engedélyeket egy egyénileg fejlesztett alkalmazásba

Ha előrelátó módon az alkalmazás a hozzájárulási biztosítani, vagy nem hozzájárult, akkor hiba történik egy alkalmazáshoz futnak, próbálkozzon az alábbi lépéseket.

## <a name="how-to-perform-admin-consent-for-your-application"></a>Az alkalmazás rendszergazdai jóváhagyás végrehajtása

Ez befolyásolja a hozzájárulás az alkalmazáshoz a szervezet összes felhasználója számára.

1. Keresse meg a **Alkalmazásregisztrációk** panel egy **globális rendszergazdai**, majd válassza ki az alkalmazást.

2. Válassza ki **szükséges engedélyek**, és végül nyomja le az **engedélyek megadása** gombra a panel tetején.

Másik megoldásként kérést hozhatnak létre *login.microsoftonline.com* való az alkalmazás-konfigurációkat és a hozzáfűző *& Rákérdezés rendszergazdai =\_hozzájárulás*. Rendszergazdai hitelesítő adataival bejelentkezni, miután az alkalmazás kapott hozzájárulás az összes felhasználó számára.

## <a name="how-to-force-user-consent-for-your-application"></a>Kényszerítése a felhasználói hozzájárulás az alkalmazáshoz

* Hitelesítési kérések az alakzatot hozzáfűzése *& Rákérdezés hozzájárulási =* amelyhez a végfelhasználók számára, hogy engedélyt adjanak az minden alkalommal, amikor a hitelesítéshez szükséges.

## <a name="next-steps"></a>További lépések

[Jóváhagyás és az Azure ad-alkalmazások integrálását ismertető](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

[Jóváhagyás és az Azure ad v2.0 csatornákban összevont alkalmazások](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure ad stackoverflow-n](http://stackoverflow.com/questions/tagged/azure-active-directory)
