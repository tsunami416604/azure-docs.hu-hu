---
title: Jogkivonatok – Azure Active Directory B2C konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogy a jogkivonat élettartamát és kompatibilitási beállítások konfigurálása az Azure Active Directory B2C-t.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 69a6284ea7b8905bb5efdb1f4c9f26027bd6f9d9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689607"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Jogkivonatok konfigurálása az Azure Active Directory B2C-vel

Ebből a cikkből megismerheti, hogyan konfigurálhatja a [élettartama, és a egy jogkivonat-kompatibilitási](active-directory-b2c-reference-tokens.md) Azure Active Directory (Azure AD) B2C-ben.

## <a name="prerequisites"></a>Előfeltételek

[Felhasználói folyamat létrehozása](tutorial-create-user-flows.md) regisztráljon, és jelentkezzen be az alkalmazást használók.

## <a name="configure-token-lifetime"></a>Az élettartam konfigurálása

A jogkivonat élettartamát bármely felhasználói folyamatot is beállíthatja.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő tartalmazó könyvtárba. Válassza ki a **címtár és előfizetés-szűrő** a felső menüben válassza az Azure AD B2C-bérlő tartalmazó könyvtárba.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Válassza ki **felhasználókövetési adatai (szabályzatok)**.
5. Nyissa meg a korábban létrehozott felhasználói folyamatot. 
6. Válassza ki **tulajdonságok**.
7. A **jogkivonat élettartama**, állítsa be az alkalmazás igényeinek a következő tulajdonságokkal:

    ![Az élettartam konfigurálása](./media/configure-tokens/token-lifetime.png)

8. Kattintson a **Save** (Mentés) gombra.

## <a name="configure-token-compatibility"></a>Jogkivonat-kompatibilitási konfigurálása

1. Válassza ki **felhasználókövetési adatai (szabályzatok)**.
2. Nyissa meg a korábban létrehozott felhasználói folyamatot. 
3. Válassza ki **tulajdonságok**.
4. A **jogkivonat-kompatibilitási beállítások**, állítsa be az alkalmazás igényeinek a következő tulajdonságokkal:

    ![Jogkivonat-kompatibilitási konfigurálása](./media/configure-tokens/token-compatibility.png)

5. Kattintson a **Save** (Mentés) gombra.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [hozzáférési kódok használata](active-directory-b2c-access-tokens.md).



