---
title: Az e-mailek ellenőrzésének letiltása az ügyfélre való feliratkozás során
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan tilthatja le az e-mailek ellenőrzését az Azure Active Directory B2C-ben való ügyfél-regisztráció során.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 369b4e13baa344a71a51b358ef810d1a66b4b6ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126726"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Az e-mailek ellenőrzésének letiltása az ügyfél regisztrációja során az Azure Active Directory B2C-ben

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

Az e-mailek ellenőrzésének letiltásához kövesse az alábbi lépéseket:

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com)
1. A **felső** menü Directory + előfizetésszűrőjével válassza ki az Azure AD B2C-bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. Válassza a **Felhasználói folyamatok lehetőséget.**
1. Válassza ki azt a felhasználói folyamatot, amelynek e-mail-ellenőrzését le szeretné tiltani. Például *B2C_1_signinsignup*.
1. Válassza a **Lapelrendezések lehetőséget.**
1. Válassza **a Helyi fiók regisztrálása lapot**.
1. A **Felhasználói attribútumok csoportban**válassza **az E-mail cím lehetőséget.**
1. A **MEGERŐSÍTÉS GOMBRA** a Nem lehetőséget válassza a **Nem (Nem)** lehetőséget.
1. Kattintson a **Mentés** gombra. Az e-mailek ellenőrzése le van tiltva ehhez a felhasználói folyamathoz.

## <a name="next-steps"></a>További lépések

- A [felhasználói felület testreszabása az Azure Active Directory B2C szolgáltatásban](customize-ui-overview.md)

