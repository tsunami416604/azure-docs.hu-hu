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
ms.date: 09/25/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c598664ef721103740716ad6215ddaea53bc635f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183074"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Az e-mail-ellenőrzés letiltása az ügyfél-regisztráció során Azure Active Directory B2C

Alapértelmezés szerint a Azure Active Directory B2C (Azure AD B2C) ellenőrzi az ügyfél e-mail-címét a helyi fiókok számára (fiókok azon felhasználók számára, akik e-mail-címmel vagy felhasználónévvel jelentkeznek be). Azure AD B2C az érvényes e-mail-címeket biztosítja, hogy az ügyfeleknek meg kell győződniük a regisztrációs folyamat során. Emellett megakadályozza, hogy a rosszindulatú szereplők automatizált folyamatokat használjanak az alkalmazásokban lévő csalárd fiókok létrehozásához.

Egyes alkalmazás-fejlesztők szívesebben szeretnék kihagyni az e-mailek ellenőrzését a regisztrálási folyamat során, hanem az ügyfelek az e-mail-címüket később is ellenőrzik. Ennek támogatásához Azure AD B2C konfigurálható az e-mail ellenőrzésének letiltására. Ez létrehoz egy simább regisztrációs folyamatot, és lehetővé teszi a fejlesztők számára a rugalmasságot, hogy megkülönböztessék azokat az ügyfeleket, akik nem rendelkeznek az e-mail-címükkel.

Az e-mail-ellenőrzés letiltásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. A felső menüben a **könyvtár + előfizetés** szűrő használatával válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. Válassza a **felhasználói folyamatok**lehetőséget.
1. Válassza ki azt a felhasználói folyamatot, amelynek le szeretné tiltani az e-mail-ellenőrzését. Például *B2C_1_signinsignup*.
1. Válassza **ki a lapelrendezések elemet**.
1. Válassza a **helyi fiók regisztrálása lapot**.
1. A **felhasználói attribútumok**területen válassza az **e-mail-cím**elemet.
1. A **szükséges ellenőrzés** legördülő menüben válassza a **nem**lehetőséget.
1. Kattintson a **Mentés** gombra. Az e-mailek ellenőrzése mostantól le van tiltva ennél a felhasználói folyamatnál.

> [!WARNING]
> Ha letiltja az e-mailek ellenőrzését a regisztrációs folyamatban, a levélszemét is vezethet. Ha letiltja az alapértelmezett Azure AD B2C által biztosított e-mail-ellenőrzést, javasoljuk, hogy hozzon létre egy helyettesítő ellenőrzési rendszerét.
