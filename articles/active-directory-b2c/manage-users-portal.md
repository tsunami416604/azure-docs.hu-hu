---
title: Hozzon létre & törölje az Azure AD B2C felhasználói fiókokat az Azure Portalon
description: Ismerje meg, hogyan használhatja az Azure Portalon fogyasztói felhasználók at az Azure AD B2C címtárban.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187219"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Az Azure Portal használata fogyasztói felhasználók létrehozása és törlése az Azure AD B2C-ben

Előfordulhatnak olyan forgatókönyvek, amelyekben manuálisan szeretné létrehozni a fogyasztói fiókokat az Azure Active Directory B2C (Azure AD B2C) címtárban. Bár az Azure AD B2C-címtárban lévő fogyasztói fiókok leggyakrabban akkor jönnek létre, amikor a felhasználók regisztrálnak az alkalmazások valamelyikének használatára, létrehozhatja őket programozott módon és az Azure Portal használatával. Ez a cikk a felhasználók létrehozásának és törlésének Azure Portal metódusára összpontosít.

Felhasználók hozzáadásához vagy törléséhez a fiókhoz a *Felhasználó rendszergazdát* vagy a *Globális rendszergazdai* szerepkört kell rendelni.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="types-of-user-accounts"></a>A felhasználói fiókok típusai

Az [Azure AD B2C felhasználói fiókok áttekintése című](user-overview.md)témakörben leírtak szerint háromféle felhasználói fiók hozható létre egy Azure AD B2C-címtárban:

* Feladatok
* Vendég
* Fogyasztói

Ez a cikk az Azure Portalon a **fogyasztói fiókok** használatára összpontosít. A munkahelyi és vendégfiókok létrehozásáról és törléséről az Azure Active Directory használatával felhasználók hozzáadása és törlése című [témakörben talál.](../active-directory/fundamentals/add-users-azure-active-directory.md)

## <a name="create-a-consumer-user"></a>Fogyasztói felhasználó létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. A **Kezelés** alatt válassza a **Felhasználókat**.
1. Válassza az **Új felhasználó**lehetőséget.
1. Válassza **az Azure AD B2C-felhasználó létrehozása lehetőséget.**
1. Válasszon egy **bejelentkezési metódust,** és adjon meg **egy e-mail** címet vagy egy **felhasználónevet** az új felhasználószámára. Az itt kiválasztott bejelentkezési módszernek meg kell egyeznie az Azure AD B2C-bérlő *helyi fiók* identitásszolgáltatójához megadott beállítással (lásd:**Identitásszolgáltatók** **kezelése** > az Azure AD B2C-bérlőben).
1. Adja meg a felhasználó **nevét.** Ez általában a felhasználó teljes neve (adott és vezetéknév).
1. (Nem kötelező) **Letilthatja a bejelentkezést,** ha el szeretné halasztani a felhasználó bejelentkezésének lehetőségét. A bejelentkezéskésőbb a felhasználói **profil** az Azure Portalon szerkesztve engedélyezheti a bejelentkezést.
1. Válassza **a Jelszó automatikus generálása** vagy a Jelszó létrehozása **lehetőséget.**
1. Adja meg a felhasználó **utónevét** és **vezetéknevét**.
1. Kattintson a **Létrehozás** gombra.

Ha nem jelölte be a **Bejelentkezés tiltása**lehetőséget, a felhasználó most már bejelentkezhet a megadott bejelentkezési módszerrel (e-mail vagy felhasználónév).

## <a name="delete-a-consumer-user"></a>Fogyasztói felhasználó törlése

1. Az Azure AD B2C címtárban válassza a **Felhasználók**lehetőséget, majd válassza ki a törölni kívánt felhasználót.
1. A törlés megerősítéséhez válassza a **Törlés**lehetőséget, majd az **Igen** lehetőséget.

A felhasználó nak a törlést követő első 30 napon belüli visszaállításáról, illetve a felhasználó végleges törléséről az [Azure Active Directory használatával nemrég törölt felhasználó visszaállítása vagy eltávolítása](../active-directory/fundamentals/active-directory-users-restore.md)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

Az automatikus felhasználói felügyeleti forgatókönyvek, például a felhasználók áttelepítése egy másik identitásszolgáltató az Azure AD B2C könyvtárba, lásd: [Azure AD B2C: Felhasználói áttelepítés.](user-migration.md)
