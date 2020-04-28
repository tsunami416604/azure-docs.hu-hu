---
title: E-mail-ellenőrzés letiltása az ügyfél regisztrálása közben
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan tilthatja le az e-mailek ellenőrzését a Azure Active Directory B2C az ügyfelek regisztrációja során.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79126726"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Az e-mail-ellenőrzés letiltása az ügyfél-regisztráció során Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

Az e-mail-ellenőrzés letiltásához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com)
1. A felső menüben a **könyvtár + előfizetés** szűrő használatával válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. Válassza a **felhasználói folyamatok**lehetőséget.
1. Válassza ki azt a felhasználói folyamatot, amelynek le szeretné tiltani az e-mail-ellenőrzését. Például *B2C_1_signinsignup*.
1. Válassza **ki a lapelrendezések elemet**.
1. Válassza a **helyi fiók regisztrálása lapot**.
1. A **felhasználói attribútumok**területen válassza az **e-mail-cím**elemet.
1. A **szükséges ellenőrzés** legördülő menüben válassza a **nem**lehetőséget.
1. Kattintson a **Mentés** gombra. Az e-mailek ellenőrzése mostantól le van tiltva ennél a felhasználói folyamatnál.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [szabhatja testre a felhasználói felületet Azure Active Directory B2C](customize-ui-overview.md)

