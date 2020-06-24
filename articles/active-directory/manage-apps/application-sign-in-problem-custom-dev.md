---
title: Problémák egyéni fejlesztésű alkalmazásba való bejelentkezéskor | Microsoft Docs
description: Gyakori hibák, amelyek miatt előfordulhat, hogy nem tud bejelentkezni az Azure AD-vel fejlesztett alkalmazásba
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
ms.openlocfilehash: cdfc2a39c6bd3b68df7feb978d2548ad67631235
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2020
ms.locfileid: "84759130"
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

[Engedélyek és hozzájárulás a Microsoft-identitásplatform végpontján](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD-StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
