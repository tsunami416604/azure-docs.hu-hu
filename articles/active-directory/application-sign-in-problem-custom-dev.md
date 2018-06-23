---
title: Probléma jelentkezik be egy egyéni által fejlesztett alkalmazás |} Microsoft Docs
description: Az Azure ad-val kialakítása, amelyek okozza, hogy nem fog tudni bejelentkezni egy alkalmazás általános rrors
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
ms.openlocfilehash: 3cbc9f981ab528efe2d739022c674cef48dfeb51
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36330886"
---
# <a name="problems-signing-in-to-an-custom-developed-application"></a>Egy egyéni által fejlesztett alkalmazás bejelentkezés problémák

Nincsenek számos hibát, amely az okozza, hogy nem lehet bejelentkezni az alkalmazásba. A legnagyobb OK személyek esetlegesen fellépő probléma van az alkalmazások konfigurációja.

## <a name="errors-related-to--misconfigured-apps"></a>Helytelenül konfigurált alkalmazások kapcsolatos hibák

* Ellenőrizze a portálon mindkét a konfiguráció megfelel az alkalmazás rendelkezik. Pontosabban hasonlítsa össze az ügyfél/alkalmazás azonosítója, a válasz URL-címek, a titkos kulcsok/Ügyfélkulcsokat és a App ID URI.

* Hasonlítsa össze a hozzáférést a kódokat, amelyek a beállított engedélyek szükségesek a kért erőforrás a **szükséges erőforrások** fülre, és ellenőrizze, hogy Ön csak erőforrás-kérelmek konfigurálását.

* Lásd: [Azure AD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory) hasonló hibákat és problémákat.

## <a name="next-steps"></a>További lépések

[Az Azure AD-fejlesztői útmutató](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Hozzájárulás és az alkalmazások az Azure AD integrálása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications>)<br>

[Hozzájárulás és az Azure AD v2.0 Permissioning összevont alkalmazások](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Az Azure AD-StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory>)
