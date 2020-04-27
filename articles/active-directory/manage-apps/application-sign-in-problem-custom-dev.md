---
title: Problémák egyéni fejlesztésű alkalmazásba való bejelentkezéskor | Microsoft Docs
description: Gyakori hibák, amelyek miatt előfordulhat, hogy nem tud bejelentkezni az Azure AD-vel fejlesztett alkalmazásba
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "65825236"
---
# <a name="problems-signing-in-to-a-custom-developed-application"></a>Problémák egy egyéni fejlesztésű alkalmazásba való bejelentkezéskor

Számos hiba miatt előfordulhat, hogy nem tud bejelentkezni egy alkalmazásba. A probléma legjelentősebb oka az, hogy helytelenül vannak konfigurálva az alkalmazások.

## <a name="errors-related-to--misconfigured-apps"></a>Helytelenül konfigurált alkalmazásokkal kapcsolatos hibák

* Ellenőrizze, hogy a portálon lévő konfigurációk megfelelnek-e az alkalmazásnak. Az ügyfél/alkalmazás-azonosító, a válasz URL-címek, az ügyfél titkos kulcsainak és a kulcsok, valamint az alkalmazás-azonosító URI összehasonlítása.

* Hasonlítsa össze azt az erőforrást, amelyhez hozzáférést kér a kódban a **szükséges erőforrások** lapon beállított engedélyekkel, és győződjön meg arról, hogy csak a konfigurált erőforrásokat kéri.

* Tekintse meg a hasonló hibákat és problémákat az [Azure ad StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory) .

## <a name="next-steps"></a>További lépések

[Az Azure AD fejlesztői útmutatója](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Alkalmazások belefoglalása és integrálása az Azure AD-be](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Az Azure AD v 2.0-nak átszervezett alkalmazások beleegyezése és engedélyezése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD-StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
