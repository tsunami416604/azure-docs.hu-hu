---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: nikuklic
ms.openlocfilehash: 009bd57fdb82b8463352da8dc63c9aeebceab09b
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779274"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Egy központilag telepített kommunikációs szolgáltatások erőforrása. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).
- A kommunikációs szolgáltatások erőforrásában beszerzett telefonszám. [telefonszám beszerzése](../../telephony-sms/get-phone-number.md).
- A a `User Access Token` hívási ügyfél engedélyezéséhez. További információ a [beszerzéséről `User Access Token` ](../../access-tokens.md)
- Az [alkalmazáshoz való telefonálás hozzáadásával kapcsolatos első lépések](../getting-started-with-calling.md) elvégzése

### <a name="prerequisite-check"></a>Előfeltételek ellenőrzése

- A kommunikációs szolgáltatások erőforrásához tartozó telefonszámok megtekintéséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), keresse meg a kommunikációs szolgáltatások erőforrását, és nyissa meg a **telefonszámok** lapot a bal oldali navigációs ablaktáblán.
- Az alkalmazást felépítheti és futtathatja az Azure kommunikációs szolgáltatásokkal, és meghívja az ügyféloldali kódtárat a JavaScripthez:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js
```

## <a name="setting-up"></a>Beállítás

### <a name="add-pstn-functionality-to-your-app"></a>PSTN-funkciók hozzáadása az alkalmazáshoz

Kiterjesztheti elrendezését telefonos tárcsázási vezérlőkkel.

Helyezze a kódot a `<body />` **index.html**. szakaszának végére a `<script />` címkék előtt:

```html
<input 
  id="callee-phone-input"
  type="text"
  placeholder="Phone number you would like to dial"
  style="margin-bottom:1em; width: 230px;"
/>
<div>
  <button id="call-phone-button" type="button">
    Start Phone Call
  </button>
  &nbsp;
  <button id="hang-up-phone-button" type="button" disabled="true">
    Hang Up Phone Call
  </button>
</div>
```

Kiterjesztheti az alkalmazás logikáját a telefonos funkciókkal.

Adja hozzá ezt a kódot a **client.jshoz **:

```javascript
const calleePhoneInput = document.getElementById("callee-phone-input");
const callPhoneButton = document.getElementById("call-phone-button");
const hangUpPhoneButton = document.getElementById("hang-up-phone-button");
```

## <a name="start-a-call-to-phone"></a>Telefonhívás indítása telefonra

Itt adhatja meg a kommunikációs szolgáltatások erőforrásában beszerzett telefonszámot, amelyet a rendszer a hívás elindításához fog használni:
> [!WARNING]
> Vegye figyelembe, hogy a telefonszámokat E. 164 nemzetközi szabvány formátumban kell megadni. (például: + 12223334444)

Eseménykezelő hozzáadásával kezdeményezheti a hívását, amikor a `callPhoneButton` gombra kattint:


```javascript
callPhoneButton.addEventListener("click", () => {
  // start a call to phone
  const phoneToCall = calleePhoneInput.value;
  call = callAgent.call(
    [{phoneNumber: phoneToCall}], { alternateCallerId: {phoneNumber: '+18336528005'}
  });

  // toggle button states
  hangUpPhoneButton.disabled = false;
  callPhoneButton.disabled = true;
});
```

## <a name="end-a-call-to-phone"></a>Telefon hívásának befejezése

Esemény-figyelő hozzáadása az aktuális hívás befejezéséhez, amikor a `hangUpPhoneButton` gombra kattint:

```javascript
hangUpPhoneButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({
    forEveryone: true
  });

  // toggle button states
  hangUpPhoneButton.disabled = true;
  callPhoneButton.disabled = false;
});
```

A `forEveryone` tulajdonság véget ér a hívás minden résztvevőnél.

## <a name="run-the-code"></a>A kód futtatása

Használja az `webpack-dev-server` alkalmazást az alkalmazás létrehozásához és futtatásához. Futtassa a következő parancsot az alkalmazás gazdagépének a helyi webkiszolgálón való becsomagolásához:


```console
npx webpack-dev-server --entry ./client.js --output bundle.js
```

Nyissa meg a böngészőt, és navigáljon a gombra `http://localhost:8080/` . A következőnek kell megjelennie:


![Képernyőkép a befejezett JavaScript-alkalmazásról.](../media/javascript/pstn-calling-javascript-app.png)

Ha egy valós telefonszámot szeretne meghívni, adjon meg egy telefonszámot a hozzáadott szövegmezőben, és kattintson a **telefonos hívás indítása** gombra.

> [!WARNING]
> Vegye figyelembe, hogy a telefonszámokat E. 164 nemzetközi szabvány formátumban kell megadni. (például: + 12223334444)
