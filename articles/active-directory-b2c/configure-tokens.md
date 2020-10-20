---
title: Jogkivonatok konfigurálása – Azure Active Directory B2C | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a jogkivonatok élettartamát és kompatibilitási beállításait a Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 10f86d194c5c70f2de0ab6502893e228800b1bdf
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92215467"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Jogkivonatok konfigurálása a Azure Active Directory B2Cban

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a [jogkivonatok élettartamát és kompatibilitását](tokens-overview.md) Azure Active Directory B2C (Azure ad B2C).

## <a name="prerequisites"></a>Előfeltételek

[Hozzon létre egy felhasználói folyamatot](tutorial-create-user-flows.md), amellyel a felhasználók regisztrálhatnak és bejelentkezhetnek az alkalmazásba.

## <a name="configure-jwt-token-lifetime"></a>JWT-jogkivonat élettartamának konfigurálása

A jogkivonat élettartama bármely felhasználói folyamaton beállítható.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza a **felhasználói folyamatok (szabályzatok)** lehetőséget.
5. Nyissa meg a korábban létrehozott felhasználói folyamatot.
6. Válassza ki a **Tulajdonságok** elemet.
7. A **jogkivonat élettartama**alatt állítsa be a következő tulajdonságokat az alkalmazás igényeinek megfelelően:

    ![A jogkivonat élettartama tulajdonságának beállításai a Azure Portal](./media/configure-tokens/token-lifetime.png)

8. Kattintson a **Mentés** gombra.

> [!NOTE]
> Az engedélyezési programkódot használó egylapos alkalmazások esetében a PKCE mindig 24 órás frissítési jogkivonat élettartama szükséges. [További információ a frissítési tokenek biztonsági vonatkozásairól a böngészőben](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="configure-jwt-token-compatibility"></a>JWT-jogkivonat kompatibilitásának konfigurálása

1. Válassza a **felhasználói folyamatok (szabályzatok)** lehetőséget.
2. Nyissa meg a korábban létrehozott felhasználói folyamatot.
3. Válassza ki a **Tulajdonságok** elemet.
4. A **jogkivonat-kompatibilitási beállítások**területen állítsa be a következő tulajdonságokat az alkalmazás igényeinek megfelelően:

    ![Jogkivonat-kompatibilitási tulajdonságok beállításai a Azure Portal](./media/configure-tokens/token-compatibility.png)

5. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

További információ a [hozzáférési tokenek igényléséről](access-tokens.md).



