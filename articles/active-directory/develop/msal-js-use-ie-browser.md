---
title: Problémák az Internet Explorer (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Használja a Microsoft Authentication Library for JavaScript (MSAL.js) programot az Internet Explorer böngészővel.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c4f3c4153e1404a5576427be7ef218f5a669387e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695857"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Ismert problémák az Internet Explorer és a Microsoft Edge böngészőkben (MSAL.js)

A Microsoft Authentication Library for JavaScript (MSAL.js) a [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) protokollhoz jön létre, hogy az internetexplorerben is futtatható legyen. Van azonban néhány dolog, amit tudni kell.

## <a name="run-an-app-in-internet-explorer"></a>Alkalmazás futtatása az Internet Explorerben
Ha az MSAL.js-t olyan alkalmazásokban kívánja használni, amelyek futtathatók az Internet Explorer programban, az MSAL.js parancsfájlra való hivatkozás előtt hozzá kell adnia egy hivatkozást egy ígéret isznakkitöltésére.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Ennek az az oka, hogy az Internet Explorer nem támogatja a JavaScript ígér natív.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Az Internet Explorer böngészőben futó alkalmazások hibakeresése

### <a name="running-in-production"></a>Futás a termelésben
Az alkalmazás éles környezetben történő üzembe helyezése (például az Azure Web apps) általában jól működik, feltéve, hogy a végfelhasználó elfogadta a felugró ablakokat. Teszteltük az Internet Explorer 11-ben.

### <a name="running-locally"></a>Helyi futtatás
Ha helyileg szeretné futtatni és hibakeresést követni az Internet Explorer programban futó alkalmazással, tisztában kell lennie *http://localhost:1234*a következő szempontokkal (tegyük fel, hogy az alkalmazást a következőképpen szeretné futtatni:

- Az Internet Explorer "védett mód" nevű biztonsági mechanizmussal rendelkezik, amely megakadályozza az MSAL.js megfelelő működését. A tünetek között, miután bejelentkezett, az oldal http://localhost:1234/nullátirányítható a- ra.

- Az alkalmazás helyi futtatásához és hibakereséséhez le kell tiltania ezt a "védett módot". Ehhez:

    1. Kattintson az Internet Explorer **Eszközök** (a fogaskerék ikon) ikonjára.
    1. Válassza **az Internetbeállítások,** majd a **Biztonság** lapot.
    1. Kattintson az **Internet** zónára, és törölje a jelet **a Védett mód engedélyezése jelölőnégyzetből (az Internet Explorer újraindítását igényli)**. Az Internet Explorer figyelmeztet, hogy a számítógép már nem védett. Kattintson az **OK** gombra.
    1. Indítsa újra az Internet Explorert.
    1. Futtassa és hibakeresés az alkalmazást.

Ha végzett, állítsa vissza az Internet Explorer biztonsági beállításait.  Válassza az**Internetbeállítások** ->  **beállításai** -> **biztonság** -> **lehetőséget Az összes zóna visszaállítása az alapértelmezett szintre**.

## <a name="next-steps"></a>További lépések
További információ [az MSAL.js internetexplorerben való használatasorán ismert problémákról.](msal-js-use-ie-browser.md)