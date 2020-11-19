---
title: Gyors útmutató – csapatokhoz való csatlakozás
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ab13e02c9154dbfc1bd28872f1bd6f993019a604
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2020
ms.locfileid: "94915406"
---
## <a name="prerequisites"></a>Előfeltételek

- Egy működő [kommunikációs szolgáltatás, amely meghívja az alkalmazást](../getting-started-with-calling.md).
- [Csapatok üzembe helyezése](/deployoffice/teams-install).

## <a name="enable-teams-interoperability"></a>Csoportok együttműködésének engedélyezése

A csapatok együttműködési funkciója jelenleg privát előzetes verzióban érhető el. Ha engedélyezni szeretné ezt a funkciót a kommunikációs szolgáltatások erőforrásához, küldjön e-mailt a következő címre [acsfeedback@microsoft.com](mailto:acsfeedback@microsoft.com) :

1. A kommunikációs szolgáltatások erőforrását tartalmazó Azure-előfizetés előfizetés-azonosítója.
2. Csapatának bérlői azonosítója. Ennek a legegyszerűbb módja a [csapatra mutató hivatkozás beszerzése és megosztása](https://support.microsoft.com/office/create-a-link-or-a-code-for-joining-a-team-11b0de3b-9288-4cb4-bc49-795e7028296f#:~:text=Create%20a%20link%20If%20you%E2%80%99re%20a%20team%20owner%2C,link%20into%20any%20browser%20to%20join%20the%20team).

A funkció használatához mindkét entitás tulajdonos szervezetének tagjának kell lennie.

## <a name="add-the-teams-ui-controls"></a>A csapatok felhasználói felületi vezérlőinek hozzáadása

Adjon hozzá egy új szövegmezőt és egy gombot a HTML-fájlon belül. A rendszer a Teams Meeting-környezet megadására fogja használni a szövegmezőt, és a gomb a megadott értekezlethez való csatlakozáshoz használható:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
    />
    <input 
      id="teams-id-input"
      type="text"
      placeholder="Teams meeting context"
      style="margin-bottom:1em; width: 300px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
         <button id="meeting-button" type="button" disabled="false">
        Join Teams Meeting
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>A csapatok felhasználói felületének vezérlésének engedélyezése

Most már összekapcsolhatja a **JOIN Teams Meeting** gombot ahhoz a kódhoz, amely csatlakozik a megadott csapatokhoz:

```javascript
meetingButton.addEventListener("click", () => {
    
    // set display name in the meeting
    callAgent.updateDisplayName('YOUR_NAME');
    
    // join with meeting link
    call = callAgent.join({meetingLink: 'MEETING_LINK'}, {});

     // join with meeting coordinates
     call = callAgent.join({
        threadId: 'CHAT_THREAD_ID',
        organizerId: 'ORGANIZER_ID',
        tenantId: 'TENANT_ID',
        messageId: 'MESSAGE_ID'
    }, {})
    
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
    meetingButton.disabled = true;
});
```

## <a name="get-the-meeting-context"></a>Az értekezlet kontextusának beolvasása

A csapatok kontextusa a Graph API-k használatával kérhető le. Ez részletesen szerepel a [Graph dokumentációjában](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta).

A Meeting **Meeting-értekezlet** URL-címéből is beolvashatja a szükséges értekezletek adatait.

## <a name="run-the-code"></a>A kód futtatása

A webpack felhasználói használhatják az `webpack-dev-server` alkalmazást az alkalmazás létrehozásához és futtatásához. Futtassa az alábbi parancsot az alkalmazás gazdagépének a helyi webkiszolgálón való megadásához:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Nyissa meg a böngészőt, és navigáljon a gombra http://localhost:8080/ . A következőnek kell megjelennie:

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Képernyőkép a befejezett JavaScript-alkalmazásról.":::

Szúrja be a csapatok kontextusát a szövegmezőbe, majd nyomja meg a *Csatlakozás* a csapatok között lehetőséget a Teams Meeting szolgáltatáshoz való csatlakozáshoz a kommunikációs szolgáltatások alkalmazásából.