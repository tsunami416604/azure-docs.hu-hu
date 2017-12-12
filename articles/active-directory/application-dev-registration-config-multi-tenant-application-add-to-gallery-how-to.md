---
title: "Az Azure AD application gallery egy több-bérlős alkalmazás hozzáadása |} Microsoft Docs"
description: "Azt ismerteti, hogyan listázhatja a Azure AD Application Gallery a saját fejlesztésű több-bérlős alkalmazásához"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 969273d1bc0750685a0b2010670915f4183ac8ed
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>Az Azure AD application gallery egy több-bérlős alkalmazás hozzáadása

## <a name="what-is-the-azure-ad-application-gallery"></a>Mi az az Azure AD Application Gallery?

Az Azure AD Application Gallery kiváló módja a az alkalmazás Azure Active Directory-ügyfelek számára a bővíthetők a hatás és a felhasználók elérését a piactéren az alkalmazás összes millióit elé. A következő lépések azt ismertetik, hogyan listázhatja a Azure AD Application Gallery az alkalmazást.

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Ha az alkalmazás támogatja az SAML-alapú vagy OpenIDConnect
Ha egy több-bérlős alkalmazást szeretné az Azure AD Application Gallery listájában, először ellenőrizze, az alkalmazás támogatja a következő egyszeri bejelentkezési technológiák egyikét:

1. **OpenID Connect** -közvetlen integráció használja OpenID Connect hitelesítési és az Azure AD hozzájárulás API konfigurálása az Azure AD-val. Ha integrációs csak indításakor, és az alkalmazás nem támogatja az SAML-alapú, majd azt az ajánlott mód.
2. **SAML** – az alkalmazás már rendelkezik az ügyfélgépek konfigurálására harmadik fél Identitásszolgáltatók az SAML protokoll használatával.

Ha az alkalmazás támogatja ezeket egyszeri bejelentkezési módok egyikét, és szeretné több-bérlős alkalmazás szerepeltetése az Azure AD Application Gallery, követheti a dokumentum az alábbi lépéseket. Ha gyorsan el szeretné küldeni az e-mailek  **waadpartners@microsoft.com** .

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Ha az alkalmazás nem támogatja az SAML-alapú vagy OpenIDConnect
Akkor is, ha az alkalmazás nem támogatja e két beállítás közül, azt továbbra is integrálható, a gyűjtemény, a jelszót az egyszeri bejelentkezés technológiával. Milyen eltérések felfedezése, ezt a beállítást, ha az e-mailt küldhet  **waadpartners@microsoft.com** .

## <a name="next-steps"></a>Következő lépések
[Hogyan alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)
