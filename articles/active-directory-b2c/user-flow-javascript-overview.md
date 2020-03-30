---
title: JavaScript- és lapelrendezési verziók
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan engedélyezheti a JavaScriptet, és hogyan használhatja a lapelrendezési verziókat az Azure Active Directory B2C-ben.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185836"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>JavaScript- és lapelrendezési verziók az Azure Active Directory B2C szolgáltatásában

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Az Azure AD B2C html, CSS és JavaScript-kódot tartalmazó csomagolt tartalomkészletet biztosít a felhasználói felület elemeihez a felhasználói folyamatokban és az egyéni házirendekben.

A JavaScript engedélyezése az alkalmazásokhoz:

* Engedélyezze a felhasználói folyamaton az Azure Portal használatával
* [Oldalelrendezés kijelölése](page-layout.md)
* [A b2clogin.com](b2clogin.md) használata a kérésekben

Ha engedélyezni kívánja a [JavaScript](javascript-samples.md) ügyféloldali kódját, a JavaScript-alapú elemeknek nem módosíthatóknak kell lenniük. Ha nem módosíthatók, a módosítások nem várt viselkedést okozhatnak a felhasználói oldalakon. A problémák elkerülése érdekében érvényesítse a lapelrendezés használatát, és adjon meg egy lapelrendezési verziót annak biztosítására, hogy a JavaScript-alapú tartalomdefiníciók nem módosíthatók legyenek. Még ha nem is kívánja engedélyezni a JavaScriptet, megadhatja az oldalak oldalelrendezési verzióját.

## <a name="enable-javascript"></a>JavaScript engedélyezése

A felhasználói folyamat **tulajdonságai**párbeszédpanelen engedélyezheti a JavaScriptet. A JavaScript engedélyezése a lapelrendezés használatát is kikényszeríti. Ezután beállíthatja a felhasználói folyamat lapelrendezési verzióját a következő szakaszban leírtak szerint.

![A felhasználói folyamat tulajdonságai lap kiemelt JavaScript-beállítással](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>Lapelrendezési verzió kiválasztása

Függetlenül attól, hogy engedélyezi-e a JavaScriptet a felhasználói folyamat tulajdonságaiban, megadhat egy lapelrendezési verziót a felhasználói folyamatoldalakhoz. Nyissa meg a felhasználói folyamatot, és válassza **a Lapelrendezések**lehetőséget. Az **ELRENDEZÉS NEVE csoportban**jelöljön ki egy felhasználói folyamatlapot, és válassza a **Lapelrendezés verziója lehetőséget.**

A lapelrendezés különböző verzióiról a [Lapelrendezés verziómódosítási naplójában](page-layout.md)talál további információt.

![Lapelrendezés-beállítások a portálon a lapelrendezés verziólegördülő legördülő verziójával](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>További lépések

Az [Azure Active Directory B2C-ben használható JavaScript-minták JavaScript-használatára](javascript-samples.md)vonatkozó példákat találhat.
