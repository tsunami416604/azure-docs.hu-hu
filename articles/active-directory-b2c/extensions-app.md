---
title: Bővítmények alkalmazás az Azure Active Directory B2C-ben | Microsoft dokumentumok
description: A b2c-extensions-app visszaállítása.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/06/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 547b625996a65999c32c1b73699e3b408be01de3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188596"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: Bővítmények alkalmazás

Az Azure AD B2C könyvtár létrehozásakor `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` egy nevű alkalmazás automatikusan létrejön az új könyvtárban. Ez az alkalmazás, a továbbiakban a **b2c-extensions-app**, látható *App regisztrációk*. Az Azure AD B2C szolgáltatás a felhasználók és az egyéni attribútumok tárolására használja. Ha az alkalmazás törlődik, az Azure AD B2C nem fog megfelelően működni, és az éles környezetben hatással lesz.

> [!IMPORTANT]
> Ne törölje a b2c-extensions-app, kivéve, ha azt tervezi, hogy azonnal törölje a bérlő. Ha az alkalmazás 30 napnál tovább törlődik, a felhasználói adatok véglegesen elvesznek.

## <a name="verifying-that-the-extensions-app-is-present"></a>Annak ellenőrzése, hogy a bővítményalkalmazás jelen van-e

Annak ellenőrzése, hogy a b2c-extensions-app jelen van-e:

1. Az Azure AD B2C-bérlőn belül kattintson a bal oldali navigációs menü **Minden szolgáltatás** elemre.
1. Keresse meg és nyissa meg **az alkalmazásregisztrációkat.**
1. **B2c-extensions-app-alkalmazással** kezdődő alkalmazás keresendő

## <a name="recover-the-extensions-app"></a>A bővítmények alkalmazás helyreállítása

Ha véletlenül törölte a b2c-extensions-app, akkor 30 nap, hogy visszaszerezze azt. Az alkalmazást a Graph API segítségével állíthatja vissza:

1. Tallózás a tallózáshoz. [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/)
1. Jelentkezzen be a webhelyre az Azure AD B2C könyvtár globális rendszergazdájaként, amelyhez vissza szeretné állítani a törölt alkalmazást. Ennek a globális rendszergazdának a következőhöz `username@{yourTenant}.onmicrosoft.com`hasonló e-mail címmel kell rendelkeznie: .
1. Http GET kiadása az `https://graph.windows.net/myorganization/deletedApplications` API-version=1.6-os URL-címmel szemben. Ez a művelet felsorolja az összes olyan alkalmazást, amelyet az elmúlt 30 napban töröltek.
1. Keresse meg az alkalmazást abban a listában, ahol a név kezdődik `objectid` "b2c-extension-app", és másolja a tulajdonság értékét.
1. Http-bejegyzés kiadása az `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`URL-cím hez képest. Cserélje `{OBJECTID}` le az URL-cím egy részét az előző `objectid` lépésből származóra.

Most már látnia kell [a visszaállított alkalmazást](#verifying-that-the-extensions-app-is-present) az Azure Portalon.

> [!NOTE]
> Egy alkalmazás csak akkor állítható vissza, ha az elmúlt 30 napban törölték. Ha több mint 30 nap telt el, az adatok véglegesen elvesznek. További segítségért nyújtson be támogatási jegyet.
