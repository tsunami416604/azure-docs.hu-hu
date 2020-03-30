---
title: Jogkivonatok konfigurálása – Azure Active Directory B2C | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhatja a token élettartamát és kompatibilitási beállításait az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 160898f8535d3dad4811af016ebca779b7ef43a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189617"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Tokenek konfigurálása az Azure Active Directory B2C-ben

Ebben a cikkben megtudhatja, hogyan konfigurálhatja a [jogkivonat élettartamát és kompatibilitását](tokens-overview.md) az Azure Active Directory B2C (Azure AD B2C) szolgáltatásban.

## <a name="prerequisites"></a>Előfeltételek

[Hozzon létre egy felhasználói folyamatot,](tutorial-create-user-flows.md) amely lehetővé teszi a felhasználók számára, hogy regisztráljanak és jelentkezzenek be az alkalmazásba.

## <a name="configure-token-lifetime"></a>Token élettartamának konfigurálása

A token élettartama bármely felhasználói folyamat.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, és válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
3. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
4. Válassza a **Felhasználói folyamatok (házirendek)** lehetőséget.
5. Nyissa meg a korábban létrehozott felhasználói folyamatot.
6. Válassza **a Tulajdonságok lehetőséget.**
7. A **Token élettartama csoportban**módosítsa a következő tulajdonságokat az alkalmazás igényeinek megfelelően:

    ![Token élettartamú tulajdonságbeállításai az Azure Portalon](./media/configure-tokens/token-lifetime.png)

8. Kattintson a **Mentés** gombra.

## <a name="configure-token-compatibility"></a>Tokenkompatibilitás konfigurálása

1. Válassza a **Felhasználói folyamatok (házirendek)** lehetőséget.
2. Nyissa meg a korábban létrehozott felhasználói folyamatot.
3. Válassza **a Tulajdonságok lehetőséget.**
4. A **Token kompatibilitási beállításai csoportban**módosítsa az alábbi tulajdonságokat az alkalmazás igényeinek megfelelően:

    ![Tokenkompatibilitási tulajdonságbeállításai az Azure Portalon](./media/configure-tokens/token-compatibility.png)

5. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

További információ a [hozzáférési jogkivonatok használatáról.](access-tokens.md)



