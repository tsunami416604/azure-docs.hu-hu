---
title: Bejelentkezés egy egyénileg fejlesztett alkalmazásba problémák |} A Microsoft Docs
description: Közös rrors okozza, hogy nem tudnak jelentkezni egy alkalmazásba, kidolgoztunk az Azure ad-vel
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
ms.reviewer: asteen
ms.openlocfilehash: 8ae8fa823b919ec4a67832e7c42088c994bd2d97
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356617"
---
# <a name="problems-signing-in-to-an-custom-developed-application"></a>Egy egyénileg fejlesztett alkalmazásba történő bejelentkezésnél

Nincsenek okozza, hogy nem tud bejelentkezni az alkalmazás több hibákról. A legnagyobb OK személyek esetlegesen fellépő probléma van az alkalmazásokat beállításai helytelenek.

## <a name="errors-related-to--misconfigured-apps"></a>Helytelenül konfigurált alkalmazások kapcsolatos hibák

* Ellenőrizze, hogy mindkét a portálon a konfiguráció megfelel az alkalmazásában rendelkezik-e. Pontosabban hasonlítsa össze az ügyfél/alkalmazás azonosítója, a válasz URL-címek, az ügyfél titkos kódok és kulcsok és a Alkalmazásazonosító URI-t.

* Hasonlítsa össze a hozzáférést a kódban a konfigurált engedélyekkel a kért erőforrás a **szükséges erőforrások** fülre, és ellenőrizze, hogy Ön csak erőforrás-kérelmek konfigurálta.

* Lásd: [Azure AD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory) hasonló hibákat és problémákat.

## <a name="next-steps"></a>További lépések

[Az Azure AD fejlesztői útmutató](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Jóváhagyás és az Azure AD-alkalmazások integrálását ismertető](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications>)<br>

[Jóváhagyás és az Azure AD v2.0 csatornákban összevont alkalmazások](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Az Azure AD-StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory>)
