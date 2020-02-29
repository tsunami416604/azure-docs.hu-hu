---
title: JavaScript-és lapelrendezés-verziók
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan engedélyezheti a JavaScriptet, és hogyan használhatja a Azure Active Directory B2C a lapelrendezések verzióját.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 23d345ea9f22be5c4dac20e6e8784a8de079bccb
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78185836"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>JavaScript-és lapelrendezés-verziók a Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

A Azure AD B2C a felhasználói felület és az egyéni házirendek felhasználói felületi elemeihez tartalmaz HTML-, CSS-és JavaScript-készletet tartalmazó csomagolt tartalmat.

JavaScript engedélyezése az alkalmazásokhoz:

* A felhasználói folyamat engedélyezése a Azure Portal használatával
* [Lapelrendezés kiválasztása](page-layout.md)
* [B2clogin.com](b2clogin.md) használata a kérésekben

Ha engedélyezni kívánja a [JavaScript](javascript-samples.md) ügyféloldali kódját, a JavaScript-alapú elemek nem változtathatók meg. Ha nem változtathatók meg, a módosítások váratlan viselkedést okozhatnak a felhasználói oldalakon. Ezen problémák megelőzése érdekében kényszerítse ki a lapelrendezés használatát, és adja meg az oldalelrendezés verzióját, hogy a JavaScript-alapú tartalom-definíciók nem változtathatók meg. Ha nem kívánja engedélyezni a JavaScriptet, megadhatja a lapokhoz tartozó lapelrendezés-verziót is.

## <a name="enable-javascript"></a>JavaScript engedélyezése

A felhasználói folyamat **tulajdonságainál**engedélyezheti a JavaScriptet. A JavaScript engedélyezése is kikényszeríti a lapelrendezés használatát. Ezután a következő szakaszban leírtak szerint beállíthatja a felhasználói folyamathoz tartozó lapelrendezés verzióját.

![Felhasználói folyamat tulajdonságai lap a JavaScript engedélyezése beállítás kiemelve](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>Lapelrendezés verziójának kiválasztása

Függetlenül attól, hogy engedélyezi-e a JavaScriptet a felhasználói folyamat tulajdonságaiban, megadhatja a felhasználói folyamat lapjaihoz tartozó lapelrendezés-verziót. Nyissa meg a felhasználói folyamatot, **és válassza a lapelrendezések lehetőséget**. Az **elrendezés neve**területen válassza ki a felhasználói folyamat lapot, és válassza ki a lapelrendezés **verzióját**.

További információ a különböző lapelrendezés-verziókról: az [oldalelrendezés verziójának változási naplója](page-layout.md).

![Oldalelrendezés beállításai a portálon, a lapelrendezés verziószámának legördülő menüje](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>További lépések

Példák a JavaScript-használatra JavaScript- [mintákban a Azure Active Directory B2C való használatra](javascript-samples.md).
