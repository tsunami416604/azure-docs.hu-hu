---
title: Bejelentkezés egy egyénileg fejlesztett alkalmazásba problémák |} A Microsoft Docs
description: Közös rrors okozza, hogy nem tudnak jelentkezni egy alkalmazásba, kidolgoztunk az Azure ad-vel
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
ms.openlocfilehash: f4365c87f0028fe3a9c4ba35f40599359eb1455f
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259125"
---
# <a name="problems-signing-in-to-an-custom-developed-application"></a>Egy egyénileg fejlesztett alkalmazásba történő bejelentkezésnél

Nincsenek okozza, hogy nem tud bejelentkezni az alkalmazás több hibákról. A legnagyobb OK személyek esetlegesen fellépő probléma van az alkalmazásokat beállításai helytelenek.

## <a name="errors-related-to--misconfigured-apps"></a>Helytelenül konfigurált alkalmazások kapcsolatos hibák

* Ellenőrizze, hogy mindkét a portálon a konfiguráció megfelel az alkalmazásában rendelkezik-e. Pontosabban hasonlítsa össze az ügyfél/alkalmazás azonosítója, a válasz URL-címek, az ügyfél titkos kódok és kulcsok és a Alkalmazásazonosító URI-t.

* Hasonlítsa össze a hozzáférést a kódban a konfigurált engedélyekkel a kért erőforrás a **szükséges erőforrások** fülre, és ellenőrizze, hogy Ön csak erőforrás-kérelmek konfigurálta.

* Lásd: [Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory) hasonló hibákat és problémákat.

## <a name="next-steps"></a>További lépések

[Az Azure AD fejlesztői útmutató](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Jóváhagyás és az Azure AD-alkalmazások integrálását ismertető](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Jóváhagyás és az Azure AD v2.0 csatornákban összevont alkalmazások](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Az Azure AD-StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
