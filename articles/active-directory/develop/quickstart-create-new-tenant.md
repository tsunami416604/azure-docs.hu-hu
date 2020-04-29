---
title: Azure Active Directory-bérlő létrehozása
description: Megtudhatja, hogyan hozhat létre Azure AD-bérlőt, amelyet az alkalmazások regisztrálásához és létrehozásához használhat.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 03/12/2020
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: 0e2247e94b20846f19c2ed26c96a5dc53972e770
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80883813"
---
# <a name="quickstart-set-up-a-tenant"></a>Gyors útmutató: bérlő beállítása

A Microsoft identitásplatformja lehetővé teszi a fejlesztők számára az egyéni Microsoft 365-környezetek és -identitások széles választékát célzó alkalmazások létrehozását. A Microsoft Identity platform használatának megkezdéséhez hozzá kell férnie egy olyan környezethez (más néven Azure AD-bérlőhöz), amely képes regisztrálni és kezelni az alkalmazásokat, hozzáférni Microsoft 365 adatokhoz, és üzembe helyezi az egyéni feltételes hozzáférési és bérlői korlátozásokat.

A bérlő egy szervezet képviselete. Az Azure AD egy dedikált példánya, amelyet a szervezetek vagy alkalmazásfejlesztők kapnak, amikor kapcsolatot hoznak létre a Microsofttal, például regisztrálnak az Azure, a Microsoft Intune vagy a Microsoft 365 szolgáltatásra.

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
> * Helyezze a kurzort a fiók neve fölé a címtár-/bérlőazonosító lekéréséhez, vagy
> * válassza az Azure Portalon az **Azure Active Directory > Tulajdonságok > Címtár-azonosító** elemet.

Ha nincs társítva meglévő bérlő a fiókkal, egy GUID-t lát a fiók neve alatt, és nem hajthat végre műveleteket, például nem regisztrálhat alkalmazásokat, amíg végre nem hajtja a következő részben található lépéseket.

### <a name="create-a-new-azure-ad-tenant"></a>Új Azure AD-bérlő létrehozása

Ha még nem rendelkezik Azure AD-Bérlővel, vagy létre szeretne hozni egy újat a fejlesztéshez, tekintse meg a rövid útmutatót [, vagy egyszerűen](../fundamentals/active-directory-access-create-new-tenant.md) kövesse a [címtár-létrehozási élményt](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory). Az új bérlő létrehozásához az alábbi adatokat kell megadnia:

- **Szervezet neve**
- **Kezdeti tartomány** – a *.onmicrosoft.com része lesz. A tartományt később testreszabhatja.
- **Ország vagy régió**

> [!NOTE]
> A bérlő elnevezése esetén alfanumerikus karaktereket használjon. A speciális karakterek használata nem engedélyezett. A név nem lehet hosszabb 256 karakternél.

## <a name="social-and-local-accounts"></a>Közösségi és helyi fiókok

A közösségi és helyi fiók bejelentkeztetésére szolgáló alkalmazások létrehozásának megkezdéséhez egy Azure AD B2C-bérlőt kell létrehoznia. A létrehozást az [Azure AD B2C-bérlők létrehozását ismertető](../../active-directory-b2c/tutorial-create-tenant.md) témakör utasításait követve kezdheti meg.

## <a name="next-steps"></a>További lépések

* [Alkalmazás regisztrálása](quickstart-register-app.md) és integrálása a Microsoft Identity platformmal. 
* Ismerje meg a [hitelesítés alapjait](authentication-scenarios.md).
* Az előfizetések és az Azure AD-bérlő közötti kapcsolat részleteiért tekintse meg [, hogy az Azure-előfizetések hogyan vannak társítva Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md) .
