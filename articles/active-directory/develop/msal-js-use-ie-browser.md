---
title: Az Internet Explorer (a Microsoft Authentication Library for JavaScript rendszerhez) |} Az Azure
description: Ismerje meg a Microsoft-hitelesítési tár (MSAL.js) JavaScript használatával az Internet Explorer böngészőben.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cf8c84120f4c90d3943cfc31ffbf9aafcec0ba3
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873915"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Az Internet Explorer és Microsoft Edge böngésző MSAL.js az ismert problémák

Jön létre a Microsoft Authentication Library for JavaScript rendszerhez (MSAL.js) [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) , hogy az Internet Explorer is fussanak. Vannak, azonban néhány tudnivaló a.

## <a name="run-an-app-in-internet-explorer"></a>Az Internet Explorer alkalmazás futtatása
Ha szeretne MSAL.js használata, amely képes futni az Internet Explorer-alkalmazásokban, szüksége lesz egy hivatkozást egy promise kód hozzáadása előtt a MSAL.js parancsfájl hivatkozó.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Ennek az oka az Internet Explorer nem támogatja natív módon JavaScript ígéreteinket.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Az Internet Explorer-ben futó alkalmazás hibakeresése

### <a name="running-in-production"></a>Az éles környezetben futó
Az üzembe helyezés az alkalmazás éles környezetbe (például az Azure Web apps) általában jól működik, a felhasználó elfogadta ablakok megadott. Tesztelt az Internet Explorer 11.

### <a name="running-locally"></a>Helyi futtatása
Ha szeretné futtatni, és helyben fut az Internet Explorer alkalmazás hibakeresése, érdemes figyelembe vennie az alábbiakat kell (azt feltételezik, hogy szeretné-e futtatni az alkalmazását *http://localhost:1234*):

- Az Internet Explorer nevű, "védett módba", amely megakadályozza, hogy a MSAL.js megfelelően működik-e biztonsági mechanizmussal rendelkezik. Többek között a tünetek, miután bejelentkezett, a lap átirányíthatóak http://localhost:1234/null.

- Futtassa, és az alkalmazás helyi hibakeresési, kell a "védett mód" letiltása. Ehhez:

    1. Kattintson az Internet Explorer **eszközök** (a fogaskerék ikonra).
    1. Válassza ki **Internetbeállítások** , majd a **biztonsági** fülre.
    1. Kattintson a **Internet** zónát, és törölje a jelet **védett mód engedélyezése (az Internet Explorer újraindítása szükséges)**. Az Internet Explorer figyelmezteti, hogy a számítógép többé nem védett. Kattintson az **OK** gombra.
    1. Restart Internet Explorer.
    1. Futtassa, és az alkalmazás hibakeresését.

Amikor elkészült, az Internet Explorer biztonsági beállítások visszaállítása.  Válassza ki **beállítások** -> **Internetbeállítások** -> **biztonsági** -> **minden zóna visszaáll az alapértelmezett szint**.

## <a name="next-steps"></a>További lépések
Tudjon meg többet [ismert problémái az Internet Explorer MSAL.js használatakor](msal-js-use-ie-browser.md).