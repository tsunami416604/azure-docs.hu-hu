---
title: Hozzon létre egy Azure-bérlőt egy több-bérlős alkalmazáshoz
description: Útmutató független szoftverszállítóknak az Azure Active Directoryval való integrációhoz
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70812614"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Hozzon létre egy Azure-bérlőt egy több-bérlős alkalmazáshoz  

A több-bérlős alkalmazáshoz való hozzáférés biztosításához létre kell hoznia egy Azure Active Directory-bérlőt az alkalmazás regisztrálásához és az ügyfél identitásainak összevonásához. Lásd: [A megfelelő összevonási protokoll kiválasztása a több-bérlős alkalmazáshoz.](isv-choose-multi-tenant-federation.md) Ez a bérlő lehetővé teszi, hogy tesztelje az alkalmazást és az összevonási környezetben, amely hasonló az ügyfelek Azure AD-környezetekben.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Több-bérlős alkalmazás üzemeltetésének költségei

Az Azure Active Directory több kiadásban is elérhető. [Tekintse meg a részletes funkcióösszehasonlítást](https://azure.microsoft.com/pricing/details/active-directory/).

Az Azure-előfizetést és az Azure active directoryt ingyenesen létrehozhatja, és alapvető funkciókat használhat.

## <a name="create-your-tenant"></a>A bérlő létrehozása

1. Hozza létre a bérlőt. Lásd: [Fejlesztői környezet beállítása.](../develop/quickstart-create-new-tenant.md)

2. Egyszeri bejelentkezés engedélyezése és tesztelése az alkalmazáshoz,

   a. **OIDC vagy Oath alkalmazások**esetén [regisztrálja az alkalmazást](../develop/quickstart-register-app.md) több-bérlős alkalmazásként. Válassza ki a Fiókok elemet bármely szervezeti címtárban és személyes Microsoft-fiókban a Támogatott fióktípusokban

   b. **SAML- és WS-Fed-alapú alkalmazások esetén** [konfigurálhatja az SAML-alapú egyszeri bejelentkezési](configure-single-sign-on-non-gallery-applications.md) alkalmazásokat egy általános SAML-sablon használatával az Azure AD-ben.

Szükség esetén [egybérlős alkalmazást is konvertálhat több-bérlőssé.](../develop/howto-convert-app-to-be-multi-tenant.md)

## <a name="next-steps"></a>Következő lépések

[Integrálja az SSO-t az alkalmazásba](isv-sso-content.md)
