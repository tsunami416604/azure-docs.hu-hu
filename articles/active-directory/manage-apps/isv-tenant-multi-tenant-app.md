---
title: Azure-bérlő létrehozása több-bérlős alkalmazáshoz
description: Útmutató független szoftvergyártók számára a Azure Active Directory integrálásához
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 637adba89445e6974e83486f0641576225ccd268
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "70812614"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Azure-bérlő létrehozása több-bérlős alkalmazáshoz  

A több-bérlős alkalmazáshoz való hozzáférés biztosításához létre kell hoznia egy Azure Active Directory bérlőt az alkalmazás regisztrálásához és az ügyfél identitásának összevonásához. Lásd: [a megfelelő összevonási protokoll kiválasztása a több-bérlős alkalmazáshoz](isv-choose-multi-tenant-federation.md). Ez a bérlő lehetővé teszi az alkalmazás és az összevonás tesztelését olyan környezetben, amely hasonló az ügyfelek Azure AD-környezetekhez.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Több-bérlős alkalmazások üzemeltetésének költségei

Azure Active Directory több kiadásban is elérhető. [Lásd a funkciók részletes összehasonlítását](https://azure.microsoft.com/pricing/details/active-directory/).

Az Azure-előfizetést és az Azure Active Directoryt ingyenesen létrehozhatja, és alapszintű funkciókat is használhat.

## <a name="create-your-tenant"></a>A bérlő létrehozása

1. Hozza létre a bérlőt. Lásd: [fejlesztői környezet beállítása](../develop/quickstart-create-new-tenant.md).

2. Engedélyezheti és tesztelheti az egyszeri bejelentkezéses hozzáférést az alkalmazáshoz,

   a. **OIDC vagy esküt használó alkalmazások**esetén [regisztrálja alkalmazását](../develop/quickstart-register-app.md) több-bérlős alkalmazásként. Válassza ki a fiókokat a támogatott fióktípus bármely szervezeti címtárban és személyes Microsoft-fiók lehetőségében

   b. **Az SAML-és a WS-fed-alapú alkalmazások esetében**az [SAML-alapú egyszeri bejelentkezéses](configure-single-sign-on-non-gallery-applications.md) alkalmazásokat az Azure ad általános SAML-sablonjának használatával konfigurálhatja.

Ha szükséges, [egyetlen bérlős alkalmazást is át lehet alakítani több-bérlőre](../develop/howto-convert-app-to-be-multi-tenant.md) .

## <a name="next-steps"></a>Következő lépések

[Egyszeri bejelentkezés integrálása az alkalmazásba](isv-sso-content.md)
