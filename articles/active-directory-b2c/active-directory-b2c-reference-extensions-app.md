---
title: Az Azure Active Directory B2C bővítmények app |} Microsoft Docs
description: A b2c-bővítmények-alkalmazás visszaállítása.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 9/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ad908e7408c2d14a843af49bc091fea725bfba1d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712283"
---
# <a name="azure-ad-b2c-extensions-app"></a>Az Azure AD B2C: Bővítmények alkalmazás

Az Azure AD B2C-címtárban jön létre, amikor egy alkalmazás nevű `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` automatikusan létrejön az új könyvtárán belül. Az alkalmazáshoz, a továbbiakban a **b2c-bővítmények-alkalmazás**, látható *App regisztrációk*. Segítségével az Azure AD B2C szolgáltatás felhasználók és az egyéni attribútumok adatainak tárolására. Ha az alkalmazást törlik, az Azure AD B2C nem működik megfelelően, és az éles környezetben milyen hatással lesz.

> [!IMPORTANT]
> Ne törölje a b2c-bővítmények-alkalmazás, kivéve, ha azt tervezi, hogy a bérlő azonnal törli. Ha az alkalmazás több mint 30 napig törölt marad, felhasználói adatok végleg elvesznek.

## <a name="verifying-that-the-extensions-app-is-present"></a>Ellenőrzi, hogy jelen-e a bővítmények alkalmazás

Annak ellenőrzéséhez, hogy a b2c-bővítmények-alkalmazás jelen:

1. Az Azure AD B2C-bérlő belül kattintson a **minden szolgáltatás** a bal oldali navigációs menü.
1. Keresse meg, és nyissa meg a **App regisztrációk**.
1. Keressen olyan alkalmazás, amelynek kezdődik **b2c-bővítmények-alkalmazás**

## <a name="recover-the-extensions-app"></a>A bővítmények app helyreállítása

Ha véletlenül törli a b2c-bővítmények-alkalmazás, végezze el a helyreállítást 30 napja van. Visszaállíthatja az alkalmazást, a Graph API-val:

1. Keresse meg a [ https://graphexplorer.azurewebsites.net/ ](https://graphexplorer.azurewebsites.net/).
1. Jelentkezzen be a webhely, amelyet szeretne visszaállítani a törölt alkalmazást az Azure AD B2C könyvtár globális rendszergazdaként. A globális rendszergazdának rendelkeznie kell egy e-mail címet a következőhöz hasonló: `username@{yourTenant}.onmicrosoft.com`.
1. Az URL egy HTTP GET ki `https://graph.windows.net/myorganization/deletedApplications` az api-version 1.6-os =. Ez a művelet felsorolja összes alkalmazást, amely az elmúlt 30 napban törölve lett.
1. Alkalmazás található a listában, ahol a karaktersorozattal kezdődő "b2c-bővítmény-alkalmazás", és másolja a `objectid` tulajdonság értéke.
1. Az URL HTTP POST ki `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`. Cserélje le a `{OBJECTID}` URL-CÍMÉT a részét a `objectid` az előző lépésben. 

Meg kell tudni [lásd: a visszaállított app](#verifying-that-the-extensions-app-is-present) az Azure portálon.

> [!NOTE]
> Az alkalmazás csak akkor állítható vissza, ha törölve lett az utolsó 30 napban. Ha több mint 30 napig lett, adat végleg elvesznek. Ha további segítségre van szüksége a fájl egy támogatási jegy.