---
title: Az Internet Explorer használata a MSAL. js használatával | Azure
titleSuffix: Microsoft identity platform
description: Használja az Internet Explorer böngészőt a JavaScripthez készült Microsoft Authentication Library (MSAL. js) használatával.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 715e92a10ca0b4cbe38119931bc66d36c3b9259a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916281"
---
# <a name="use-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Az Internet Explorer és a Microsoft Edge böngészők használata a MSAL. js használatával

A javascripthez készült Microsoft Authentication Library (MSAL. js) JavaScript- [ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) jön létre, hogy az Internet Explorerben is futtatható legyen. Van azonban néhány tudnivaló.

## <a name="run-an-app-in-internet-explorer"></a>Alkalmazás futtatása az Internet Explorerben
Ha az Internet Explorerben futtatható alkalmazásokban szeretné használni a MSAL. js fájlt, akkor a MSAL. js parancsfájlra való hivatkozás előtt hozzá kell adnia egy hivatkozást egy ígérethez.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Ennek az az oka, hogy az Internet Explorer nem támogatja natív módon a JavaScript-ígéreteket.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Az Internet Explorerben futó alkalmazások hibakeresése

### <a name="running-in-production"></a>Éles üzemben fut
Az alkalmazás éles környezetben történő üzembe helyezése (például az Azure web Appsben) általában jól működik, ha a végfelhasználó elfogadta a felugró ablakokat. Az Internet Explorer 11 böngészőben teszteltük.

### <a name="running-locally"></a>Helyileg futtatva
Ha az Internet Explorerben futtató alkalmazást helyileg szeretné futtatni és hibakeresést végezni, a következő szempontokat kell figyelembe vennie (tegyük fel, hogy az alkalmazást *http://localhost:1234 ként* szeretné futtatni):

- Az Internet Explorer "védett mód" nevű biztonsági mechanizmussal rendelkezik, amely megakadályozza, hogy a MSAL. js megfelelően működjön. A tünetek között a bejelentkezés után a lap átirányítható http://localhost:1234/null ba.

- Az alkalmazás helyi futtatásához és hibakereséséhez le kell tiltania ezt a "védett módot". Ehhez:

    1. Kattintson az Internet Explorer **eszközök** elemre (a fogaskerék ikonra).
    1. Válassza az **Internetbeállítások lehetőséget** , majd a **Biztonság** lapot.
    1. Kattintson az **Internet** zónára, és törölje a **védett mód engedélyezése (az Internet Explorer újraindítását igényli)** jelölőnégyzet jelölését. Az Internet Explorer figyelmezteti, hogy a számítógép már nem védett. Kattintson az **OK** gombra.
    1. Indítsa újra az Internet Explorert.
    1. Az alkalmazás futtatása és hibakeresése.

Ha elkészült, állítsa vissza az Internet Explorer biztonsági beállításait.  Válassza a **beállítások** -> **internetbeállítások** -> **biztonsági** -> **az összes zóna visszaállítása alapértelmezett szintre**lehetőséget.

## <a name="next-steps"></a>Következő lépések
További információ a [MSAL. js az Internet Explorerben való használatakor felmerülő ismert problémákról](msal-js-use-ie-browser.md).