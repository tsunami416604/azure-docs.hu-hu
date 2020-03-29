---
title: Problémák az egyéni fejlesztésű alkalmazásokba való bejelentkezéskor | Microsoft dokumentumok
description: Gyakori hibák, amelyek miatt nem tud bejelentkezni az Azure AD-vel kifejlesztett alkalmazásba
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
ms.openlocfilehash: b8ad2499aea8bf4e41ca00d6c78d76e112f0493e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825236"
---
# <a name="problems-signing-in-to-a-custom-developed-application"></a>Problémák az egyéni fejlesztésű alkalmazásokba való bejelentkezéssel

Számos olyan hiba van, amely miatt nem tud bejelentkezni egy alkalmazásba. A legnagyobb ok, amiért az emberek találkoznak ezzel a problémával, a helytelenül konfigurált alkalmazások.

## <a name="errors-related-to--misconfigured-apps"></a>A helytelenül konfigurált alkalmazásokkal kapcsolatos hibák

* Ellenőrizze, hogy a portál mindkét konfigurációja megegyezik-e az alkalmazásban lévőkonfigurációval. Pontosabban hasonlítsa össze az ügyfél-/alkalmazásazonosítót, a válasz URL-eket, az ügyféltitkokat/kulcsokat és az alkalmazásazonosító URI-t.

* Hasonlítsa össze azt az erőforrást, amelyhez kódban hozzáférést kér, a **Szükséges erőforrások** lapon beállított engedélyekkel, és győződjön meg arról, hogy csak a konfigurált erőforrásokat kéri.

* Lásd: [Az Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory) a hasonló hibák at.

## <a name="next-steps"></a>További lépések

[Az Azure AD fejlesztői útmutatója](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Hozzájárulás és alkalmazások integrálása az Azure AD-be](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Hozzájárulás és engedélyezés az Azure AD v2.0-s konvergens alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
