---
title: Hozzon létre & törölheti Azure AD B2C fogyasztói felhasználói fiókjait a Azure Portal
description: Ebből a témakörből megtudhatja, hogyan hozhat létre és törölhet felhasználói felhasználókat a Azure AD B2C könyvtárban a Azure Portal használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c8fad8b5bd2dccba36c0c6888712fa806f3cbfc8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187219"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>A Azure Portal használatával hozhat létre és törölhet felhasználói felhasználókat a Azure AD B2C

Lehetnek olyan helyzetek, amikor manuálisan szeretné létrehozni a fogyasztói fiókokat a Azure Active Directory B2C (Azure AD B2C) címtárban. Habár a Azure AD B2C könyvtárban lévő fogyasztói fiókok leggyakrabban akkor jönnek létre, amikor a felhasználók az egyik alkalmazás használatára jelentkeznek, programozott módon és a Azure Portal használatával is létrehozhatók. Ez a cikk a felhasználók létrehozásának és törlésének Azure Portal módszerét tárgyalja.

Felhasználók hozzáadásához vagy törléséhez a fióknak hozzá kell rendelnie a *felhasználói rendszergazda* vagy a *globális rendszergazdai* szerepkört.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="types-of-user-accounts"></a>Felhasználói fiókok típusai

A [Azure ad B2C felhasználói fiókjainak áttekintése](user-overview.md)című cikkben leírtak szerint három típusú felhasználói fiók hozható létre egy Azure ad B2C könyvtárban:

* Munka
* Vendég
* Fogyasztói

Ez a cikk a Azure Portal **felhasználói fiókjainak** használatáról szól. A munkahelyi és a vendég fiókok létrehozásával és törlésével kapcsolatos információkért lásd: [felhasználók hozzáadása vagy törlése Azure Active Directory használatával](../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="create-a-consumer-user"></a>Fogyasztói felhasználó létrehozása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. A **Kezelés** alatt válassza a **Felhasználókat**.
1. Válassza az **új felhasználó**lehetőséget.
1. Válassza a **létrehozás Azure ad B2C a felhasználó**lehetőséget.
1. Válasszon **bejelentkezési módszert** , és adjon meg egy **e-mail-** címet vagy egy **felhasználónevet** az új felhasználó számára. Az itt kiválasztott bejelentkezési módszernek meg kell egyeznie az Azure AD B2C bérlő *helyi fiókjának* identitás-szolgáltatójának beállításával (lásd: > **identitás-szolgáltatók** **kezelése** a Azure ad B2C bérlőben).
1. Adja meg a felhasználó **nevét** . Ez általában a felhasználó teljes neve (adott és vezetékneve).
1. Választható Ha szeretné, hogy a felhasználó ne jelentkezzen be, tiltsa le a **bejelentkezést** . A bejelentkezést később is engedélyezheti, ha szerkeszti a felhasználó **profilját** a Azure Portal.
1. Válassza az **Automatikus létrehozás jelszava** vagy a **jelszó létrehozása**lehetőséget.
1. Adja **meg a** felhasználó vezetéknevét és **vezetéknevét**.
1. Kattintson a **Létrehozás** gombra.

Ha nem jelölte be a **Bejelentkezés blokkolását**, a felhasználó bejelentkezhet a megadott bejelentkezési módszerrel (e-mail vagy Felhasználónév).

## <a name="delete-a-consumer-user"></a>Fogyasztói felhasználó törlése

1. A Azure AD B2C könyvtárban válassza a **felhasználók**lehetőséget, majd válassza ki a törölni kívánt felhasználót.
1. Válassza a **Törlés**lehetőséget, majd az **Igen** gombot a törlés megerősítéséhez.

A felhasználók a törlést követő 30 napon belüli visszaállításával, illetve a felhasználók végleges törlésével kapcsolatos részletekért lásd: [a közelmúltban törölt felhasználó visszaállítása vagy eltávolítása a Azure Active Directory használatával](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="next-steps"></a>További lépések

Az automatikus felhasználói felügyeleti forgatókönyvek esetében például a felhasználók áttelepítése egy másik identitás-szolgáltatótól a Azure AD B2C könyvtárba [: Azure ad B2C: felhasználói áttelepítés](user-migration.md).
