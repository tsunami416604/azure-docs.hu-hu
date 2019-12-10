---
title: JavaScript-és lapelrendezés-verziók
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan engedélyezheti a JavaScriptet, és hogyan használhatja a Azure Active Directory B2C a lapelrendezések verzióját.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 92925dc32d7f26b224811aa852112a275f112b35
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950833"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>JavaScript-és lapelrendezés-verziók a Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

A Azure AD B2C a felhasználói felület és az egyéni házirendek felhasználói felületi elemeihez tartalmaz HTML-, CSS-és JavaScript-készletet tartalmazó csomagolt tartalmat. Az alkalmazásokhoz való JavaScript engedélyezéséhez hozzá kell adnia egy elemet az [Egyéni szabályzathoz](active-directory-b2c-overview-custom.md) , vagy engedélyeznie kell azt a portálon a felhasználói folyamatok számára, ki kell választania egy lapelrendezést, és [b2clogin.com](b2clogin.md) kell használnia a kérésekben.

Ha engedélyezni kívánja a [JavaScript](javascript-samples.md) ügyféloldali kódját, érdemes meggyőződnie arról, hogy a JavaScript-alapú elemek nem változtathatók meg. Ellenkező esetben előfordulhat, hogy a módosítások váratlan viselkedést okoznak a felhasználói lapokon. Ezen problémák elkerülése érdekében kikényszerítheti a lapelrendezés használatát, és megadhatja a lapelrendezés verzióját. Ez biztosítja, hogy a JavaScripten alapuló összes tartalmi definíció nem változtatható. Ha nem kívánja engedélyezni a JavaScriptet, megadhatja a lapokhoz tartozó lapelrendezés-verziót is.

## <a name="user-flows"></a>Felhasználói folyamatok

A felhasználói folyamat **tulajdonságainál**engedélyezheti a JavaScript használatát, amely egyúttal kikényszeríti a lapelrendezés használatát is. Ezután a következő szakaszban leírtak szerint beállíthatja a felhasználói folyamathoz tartozó lapelrendezés verzióját.

![Felhasználói folyamat tulajdonságai lap a JavaScript engedélyezése beállítás kiemelve](media/user-flow-javascript-overview/javascript-settings.png)

### <a name="select-a-page-layout-version"></a>Lapelrendezés verziójának kiválasztása

Függetlenül attól, hogy engedélyezi-e a JavaScriptet a felhasználói folyamat tulajdonságaiban, megadhatja a felhasználói folyamat lapjaihoz tartozó lapelrendezés-verziót. Nyissa meg a felhasználói folyamatot, **és válassza a lapelrendezések lehetőséget**. Az **elrendezés neve**területen válassza ki a felhasználói folyamat lapot, és válassza ki a lapelrendezés **verzióját**.

További információ a különböző lapelrendezés-verziókról: a [verzió módosítása napló](page-layout.md#version-change-log).

![Oldalelrendezés beállításai a portálon, a lapelrendezés verziószámának legördülő menüje](media/user-flow-javascript-overview/page-layout-version.png)

## <a name="custom-policies"></a>Egyéni szabályzatok

Ha egyéni házirendekben szeretné engedélyezni a JavaScriptet, adja hozzá a **ScriptExecution** elemet a **RelyingParty** elemhez az egyéni házirend-fájlban. További információ: [JavaScript-minták Azure Active Directory B2Cban való használatra](javascript-samples.md).

Függetlenül attól, hogy engedélyezi-e a JavaScriptet az egyéni házirendekben, megadhatja a lapokhoz tartozó lapelrendezés-verziót. Az oldalelrendezés megadásával kapcsolatos további információkért tekintse meg [az egyéni házirendek használatával Azure Active Directory B2C az oldal elrendezésének kiválasztása](page-layout.md)című témakört.

## <a name="next-steps"></a>Következő lépések

További információ a különböző oldalelrendezési verziókról: az [Egyéni házirendek használatával a Azure Active Directory B2C az oldal elrendezésének kiválasztása](page-layout.md#version-change-log) **szakasza** .

Példák a JavaScript-használatra JavaScript- [mintákban a Azure Active Directory B2C való használatra](javascript-samples.md).
