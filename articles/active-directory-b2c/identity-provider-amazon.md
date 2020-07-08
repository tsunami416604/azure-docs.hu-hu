---
title: Regisztráció és bejelentkezés beállítása Amazon-fiókkal
titleSuffix: Azure AD B2C
description: A regisztráció és bejelentkezés az Amazon-fiókkal rendelkező ügyfelek számára Azure Active Directory B2C használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d1a771cb13fcfa76449500ad71c67dcf7c446fa4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388442"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Egy Amazon-fiókkal történő regisztráció és bejelentkezés beállítása Azure Active Directory B2C

## <a name="create-an-app-in-the-amazon-developer-console"></a>Alkalmazás létrehozása az Amazon fejlesztői konzolján

Ha az Amazon-fiókot összevont identitás-szolgáltatóként szeretné használni Azure Active Directory B2C (Azure AD B2C), létre kell hoznia egy alkalmazást az [Amazon fejlesztői szolgáltatásaiban és technológiájában](https://developer.amazon.com). Ha még nem rendelkezik Amazon-fiókkal, regisztrálhat a következő címen: [https://www.amazon.com/](https://www.amazon.com/) .

> [!NOTE]  
> Használja a következő URL-címeket az alábbi **8. lépésben** , a helyére pedig `your-tenant-name` a bérlő nevét. A bérlő nevének megadásakor használja az összes kisbetűt, még akkor is, ha a bérlő nagybetűvel van definiálva Azure AD B2Cban.
> - Az **engedélyezett eredetek**mezőben adja meg a következőt:`https://your-tenant-name.b2clogin.com` 
> - Az **engedélyezett visszatérési URL-címek**esetében adja meg a`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`

[!INCLUDE [identity-provider-amazon-idp-register.md](../../includes/identity-provider-amazon-idp-register.md)]

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Amazon-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd az **Amazon**elemet.
1. Adjon meg egy **nevet**. Például: *Amazon*.
1. Az **ügyfél-azonosító**mezőben adja meg a korábban létrehozott Amazon-alkalmazás ügyfél-azonosítóját.
1. Az **ügyfél titka**mezőben adja meg a rögzített ügyfél-titkot.
1. Kattintson a **Mentés** gombra.
