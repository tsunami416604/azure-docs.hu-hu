---
title: Azure Active Directory-bérlő létrehozása | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Azure AD-bérlőt, amelyet az alkalmazások regisztrálásához és létrehozásához használhat.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: 3e4cd8859f376fa4fbb9069b145d893483c3fc14
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55101260"
---
# <a name="quickstart-set-up-a-dev-environment"></a>Gyors útmutató: Fejlesztési környezet beállítása

A Microsoft identitásplatformja lehetővé teszi a fejlesztők számára az egyéni Microsoft 365-környezetek és -identitások széles választékát célzó alkalmazások létrehozását. Első lépésként a Microsoft identity platform használatával kell hozzáférést egy környezethez, más néven az Azure AD-bérlővel, amely regisztrálása és alkalmazások kezelése, a Microsoft 365-adatokhoz való hozzáférése, és egyéni feltételes hozzáférési és a bérlői korlátozások telepítése.

A bérlő az egy szervezet reprezentációját. Az Azure AD egy dedikált példánya, amelyet a szervezetek vagy alkalmazásfejlesztők kapnak, amikor kapcsolatot hoznak létre a Microsofttal, például regisztrálnak az Azure, a Microsoft Intune vagy a Microsoft 365 szolgáltatásra.

Mindegyik Azure AD-bérlő önálló, elkülönül a többi Azure AD-bérlőtől, és a munkahelyi, iskolai és ügyfélidentitások (ha Azure AD B2C-bérlőről van szó) saját leképezésével, valamint saját alkalmazásregisztrációkkal rendelkeznek. A bérlőn belül található alkalmazásregisztrációkkal megadható, hogy a hitelesítés csak a saját bérlőben vagy az összes bérlőben található fiókokból legyen-e lehetséges.

## <a name="determining-environment-type"></a>A környezet típusának meghatározása

Kétféle környezetet hozhat létre. Annak eldöntése, hogy melyikre van szükség, kizárólag az alkalmazás által hitelesített felhasználók típusán alapul.

* Munkahelyi és iskolai (Azure AD-fiókok) vagy Microsoft-fiókok (például outlook.com és live.com)
* Közösségi és helyi fiókok (Azure AD B2C)

A rövid útmutató két forgatókönyvre oszlik a létrehozni kívánt alkalmazás típusától függően. Ha további segítségre van szüksége egy identitástípushoz kapcsolódóan, tekintse meg a [Microsoft identitásplatformját ismertető](about-microsoft-identity-platform.md) témakört.

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Munkahelyi és iskolai fiókok, vagy személyes Microsoft-fiókok

### <a name="use-an-existing-tenant"></a>Meglévő bérlő használata

Sok fejlesztő az Azure AD-bérlőkhöz kapcsolódó szolgáltatások vagy előfizetések (például Microsoft 365- vagy Azure-előfizetések) révén már rendelkezik bérlőkkel.

1. A bérlő ellenőrzéséhez jelentkezzen be az [Azure Portalra](https://portal.azure.com) az alkalmazás felügyeletéhez használni kívánt fiókkal.
1. Tekintse meg a jobb felső sarokban látható információkat. Ha rendelkezik bérlővel, automatikusan bejelentkezik, és közvetlenül a fióknév alatt láthatja a bérlő nevét.
   * Ha a fiókja neve fölé helyezi a kurzort az Azure Portal jobb felső részén, megjelenik a neve, e-mail-címe, címtár-/bérlőazonosítója (egy GUID), valamint a tartománya.
   * Ha a fiók több bérlővel van társítva, a fiók nevének kiválasztásával megnyithat egy menüt, ahol válthat a bérlők között. Minden bérlő saját bérlőazonosítóval rendelkezik.

> [!TIP]
> Ha meg kell keresnie a bérlőazonosítót, az alábbiakat teheti:
* Helyezze a kurzort a fiók neve fölé a címtár-/bérlőazonosító lekéréséhez, vagy
* válassza az Azure Portalon az **Azure Active Directory > Tulajdonságok > Címtár-azonosító** elemet.

Ha nincs társítva meglévő bérlő a fiókkal, egy GUID-t lát a fiók neve alatt, és nem hajthat végre műveleteket, például nem regisztrálhat alkalmazásokat, amíg végre nem hajtja a következő részben található lépéseket.

### <a name="create-a-new-azure-ad-tenant"></a>Új Azure AD-bérlő létrehozása

Ha még nem rendelkezik Azure AD-bérlővel, vagy ha újat szeretne létrehozni, kövesse a [címtár létrehozása élmény](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) utasításait. Az új bérlő létrehozásához az alábbi adatokat kell megadnia:

- **Szervezet neve**
- **Kezdeti tartomány** – a *.onmicrosoft.com része lesz. A tartományt később testreszabhatja. 
- **Ország vagy régió**

## <a name="social-and-local-accounts"></a>Közösségi és helyi fiókok

A közösségi és helyi fiók bejelentkeztetésére szolgáló alkalmazások létrehozásának megkezdéséhez egy Azure AD B2C-bérlőt kell létrehoznia. A létrehozást az [Azure AD B2C-bérlők létrehozását ismertető](../../active-directory-b2c/tutorial-create-tenant.md) témakör utasításait követve kezdheti meg.

## <a name="next-steps"></a>További lépések

* Tekintse át a kódolással foglalkozó rövid útmutatót, és kezdje meg a felhasználók hitelesítését. 
* Részletesebb kódmintákat a dokumentáció **Oktatóanyagok** részében tekinthet meg.
* A felhőben szeretné üzembe helyezni az alkalmazását? Tekintse meg a [tárolóknak az Azure-ban történő üzembe helyezésével foglalkozó](https://docs.microsoft.com/azure/index#pivot=products&panel=containers) témakört. 
