---
title: Egy több-bérlős alkalmazás egy Azure-bérlő létrehozása
description: Független szoftverszállítók, Azure Active Directoryval való integrálását ismertető útmutató
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
ms.openlocfilehash: 69cc625500af60a753ad8e7db0363954088f3307
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659579"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Egy több-bérlős alkalmazás egy Azure-bérlő létrehozása  

A több-bérlős alkalmazás hozzáférést biztosít az Azure Active Directory-bérlő, regisztrálnia kell az alkalmazást, és az összevonás az ügyfél-azonosítók engedélyezése kell létrehoznia. Lásd: [kiválasztása a megfelelő összevonási protokollt a több-bérlős alkalmazás](isv-choose-multi-tenant-federation.md). Ehhez a bérlőhöz lehetővé teszi, hogy tesztelje az alkalmazás és az összevonási olyan környezetben, az ügyfelek az Azure AD-környezetek hasonló.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Több-bérlős alkalmazás üzemeltetési költségeit

A három termékváltozatban, ingyenes, alapszintű és prémium szintű Azure Active Directory érhető el. [Tekintse meg a részletes funkció-összehasonlítást](https://azure.microsoft.com/pricing/details/active-directory/).

Hozzon létre az Azure-előfizetés és az Azure active directory ingyenes és alapszintű funkcióit használja.

## <a name="create-your-tenant"></a>A bérlő létrehozása

1. A bérlő létrehozásához. Lásd: [a fejlesztési környezet beállítása](../develop/quickstart-create-new-tenant.md).

2. Engedélyezze és tesztelje az egyszeri bejelentkezést az alkalmazáshoz való hozzáférést,

   a. **OIDC vagy Oath alkalmazások**, [regisztrálhatja alkalmazását](../develop/quickstart-register-app.md) több-bérlős alkalmazás. Válassza ki a fiókok minden olyan szervezeti directory és személyes Microsoft fiókok beállítás támogatott fiók típusa

   b. **SAML - és a WS-Fed-alapú alkalmazások**, akkor [konfigurálása SAML-alapú egyszeri bejelentkezés](configure-single-sign-on-non-gallery-applications.md) alkalmazások az Azure AD általános SAML-sablon használatával.

Emellett [átalakítani egy egybérlős alkalmazást több-bérlős](../develop/howto-convert-app-to-be-multi-tenant.md) szükség esetén.

## <a name="next-steps"></a>További lépések

[Az alkalmazás egyszeri bejelentkezési integrálása](isv-sso-content.md)
