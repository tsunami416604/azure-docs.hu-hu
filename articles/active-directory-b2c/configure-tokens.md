---
title: Jogkivonatok konfigurálása – Azure Active Directory B2C | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a jogkivonatok élettartamát és kompatibilitási beállításait a Azure Active Directory B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 83f8051fa31b6431d4a8515e2c0912cc1872a402
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064388"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Jogkivonatok konfigurálása a Azure Active Directory B2Cban

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a [jogkivonatok élettartamát és kompatibilitását](active-directory-b2c-reference-tokens.md) Azure Active Directory B2C (Azure ad B2C).

## <a name="prerequisites"></a>Előfeltételek

[Hozzon létre egy felhasználói folyamatot](tutorial-create-user-flows.md) , amely lehetővé teszi a felhasználók számára az alkalmazásba való regisztrációt és bejelentkezést.

## <a name="configure-token-lifetime"></a>Jogkivonat élettartamának konfigurálása

A jogkivonat élettartama bármely felhasználói folyamaton beállítható.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza a **felhasználói folyamatok (szabályzatok)** lehetőséget.
5. Nyissa meg a korábban létrehozott felhasználói folyamatot.
6. Válassza ki **tulajdonságok**.
7. A **jogkivonat élettartama**alatt állítsa be a következő tulajdonságokat az alkalmazás igényeinek megfelelően:

    ![A jogkivonat élettartama tulajdonságának beállításai a Azure Portal](./media/configure-tokens/token-lifetime.png)

8. Kattintson a **Save** (Mentés) gombra.

## <a name="configure-token-compatibility"></a>Jogkivonat-kompatibilitás konfigurálása

1. Válassza a **felhasználói folyamatok (szabályzatok)** lehetőséget.
2. Nyissa meg a korábban létrehozott felhasználói folyamatot.
3. Válassza ki **tulajdonságok**.
4. A **jogkivonat-kompatibilitási beállítások**területen állítsa be a következő tulajdonságokat az alkalmazás igényeinek megfelelően:

    ![Jogkivonat-kompatibilitási tulajdonságok beállításai a Azure Portal](./media/configure-tokens/token-compatibility.png)

5. Kattintson a **Save** (Mentés) gombra.

## <a name="next-steps"></a>További lépések

További információ a [hozzáférési tokenek használatáról](active-directory-b2c-access-tokens.md).



