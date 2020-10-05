---
title: fájlbefoglalás
description: fájlbefoglalás
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: d0754ea2d7e8f8f59ec475be8e27fcffd058c11f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91376871"
---
## <a name="prerequisites"></a>Előfeltételek
Az első lépések előtt ügyeljen a következőre:

- Aktív előfizetéssel rendelkező Azure-fiók létrehozása. Részletekért tekintse meg a [fiók ingyenes létrehozását](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ismertető témakört. 
- Telepítse [Node.js](https://nodejs.org/en/download/) aktív LTS-és karbantartási LTS-verzióit (8.11.1 és 10.14.1 ajánlott).
- Hozzon létre egy Azure kommunikációs szolgáltatások erőforrást. További információ: [Azure kommunikációs erőforrás létrehozása](../../create-communication-resource.md). Ehhez a rövid útmutatóhoz fel kell jegyeznie az erőforrás- **végpontot** .
- [Felhasználói hozzáférési jogkivonat](../../access-tokens.md). Ügyeljen arra, hogy a hatókört a "csevegés" értékre állítsa, és jegyezze fel a jogkivonat karakterláncát, valamint a userId karakterláncot.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-web-application"></a>Új Webalkalmazás létrehozása

Először nyissa meg a terminált vagy a parancssorablakot, hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

```console
mkdir chat-quickstart && cd chat-quickstart
```
   
A futtatásával `npm init -y` **package.js** hozhat létre az alapértelmezett beállításokkal rendelkező fájlon.

```console
npm init -y
```

Szövegszerkesztő használatával hozzon létre egy **start-chat.js** nevű fájlt a projekt gyökérkönyvtárában. Ehhez a rövid útmutatóhoz tartozó forráskódot a következő részekben adja hozzá ehhez a fájlhoz.

### <a name="install-the-packages"></a>A csomagok telepítése

A `npm install` paranccsal telepítse az alábbi kommunikációs szolgáltatások ügyféloldali kódtárait a javascripthez.

```console
npm install @azure/communication-common --save

npm install @azure/communication-administration --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

A (z `--save` ) lehetőség a könyvtárat listázza a **package.js** fájlon belüli függőségként.

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

Ez a rövid útmutató a webpack használatával csomagolja ki az alkalmazási eszközöket. Futtassa a következő parancsot a webpack, a webpack-CLI és a webpack-dev-Server NPM csomagok telepítéséhez, és sorolja fel azokat fejlesztési függőségeiként a **package.jsban**:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Hozzon létre egy **index.html** fájlt a projekt gyökérkönyvtárában. Ezt a fájlt sablonként használjuk a csevegési funkció hozzáadásához az Azure kommunikációs csevegési ügyféloldali kódtár használatával a JavaScripthez.

A kód itt látható:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Chat Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Chat Quickstart</h1>
    <script src="./bundle.js"></script>
  </body>
</html>
```
Hozzon létre egy fájlt **client.js** nevű projekt gyökérkönyvtárában, hogy tartalmazza az alkalmazás logikáját ehhez a rövid útmutatóhoz. 

### <a name="create-a-chat-client"></a>Csevegési ügyfél létrehozása

Ha csevegési ügyfelet szeretne létrehozni a webalkalmazásban, akkor a kommunikációs szolgáltatás végpontját és az előfeltételként szükséges lépések részeként létrehozott hozzáférési tokent fogja használni. A felhasználói hozzáférési tokenek lehetővé teszik olyan ügyfélalkalmazások összeállítását, amelyek közvetlenül az Azure kommunikációs szolgáltatásokban vannak hitelesítve. Miután létrehozta ezeket a jogkivonatokat a kiszolgálón, továbbíthatja őket egy ügyfél-eszköznek. Az osztályt kell használnia `AzureCommunicationUserCredential` `Common client library` , hogy átadja a tokent a csevegési ügyfelének.

Hozzon létre egy **client.js** fájlt a projekt gyökérkönyvtárában. Ezt a fájlt használjuk a csevegési funkció hozzáadására az Azure kommunikációs csevegési ügyféloldali kódtár használatával a JavaScripthez.

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationUserCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationUserCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
Cserélje le a **végpontot** a létrehozás előtt az [Azure kommunikációs erőforrás létrehozása](../../create-communication-resource.md) dokumentáció alapján.
Cserélje le a **USER_ACCESS_TOKENt** a [felhasználói hozzáférési jogkivonat](../../access-tokens.md) dokumentációja alapján kiadott jogkivonatra.
A kód hozzáadása **client.js** fájlhoz


### <a name="run-the-code"></a>A kód futtatása
Használja az `webpack-dev-server` alkalmazást az alkalmazás létrehozásához és futtatásához. Futtassa a következő parancsot az alkalmazás gazdagépének a helyi webkiszolgálón való megadásához:
```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
Nyissa meg a böngészőt, és navigáljon a gombra http://localhost:8080/ .
A böngészőben a fejlesztői eszközök konzolján a következőket kell látnia:

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>Objektummodell 
A következő osztályok és felületek az Azure kommunikációs szolgáltatások csevegési függvénytárának főbb funkcióit kezelik a JavaScripthez.

| Név                                   | Leírás                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Ez az osztály szükséges a csevegési funkciókhoz. Létrehozza azt az előfizetési adatokkal, és felhasználhatja a szálak létrehozásához, lekéréséhez és törléséhez. |
| ChatThreadClient | Ez az osztály szükséges a csevegési szál működéséhez. A példányokat a ChatClient keresztül szerezheti be, és használhatja az üzenetek küldésére/fogadására/frissítésére/törlésére, a felhasználók hozzáadására/eltávolítására/lekérésére, valamint a beírási értesítések küldésére és a beolvasási visszaigazolásokra |


## <a name="start-a-chat-thread"></a>Csevegési szál elindítása

`createThread`Csevegési szál létrehozásához használja a metódust.

`createThreadRequest` a szál kérelmének leírására szolgál:

- A használatával `topic` témakört adhat ehhez a csevegéshez; A témakör a funkció használatával frissíthető a csevegési szál létrehozása után `UpdateThread` . 
- A használatával `members` listázhatja a csevegési szálba felvenni kívánt tagokat;

Ha megoldotta `createChatThread` a metódust, `threadId` a függvény az újonnan létrehozott csevegési szálon műveleteket hajt végre, például tagok hozzáadását a csevegési szálhoz, üzenetek küldését, üzenet törlését stb.

```Javascript
async function createChatThread() {
   let createThreadRequest = {
       topic: 'Preparation for London conference',
       members: [{
                   user: { communicationUserId: '<USER_ID_FOR_JACK>' },
                   displayName: 'Jack'
               }, {
                   user: { communicationUserId: '<USER_ID_FOR_GEETA>' },
                   displayName: 'Geeta'
               }]
   };
   let chatThreadClient= await chatClient.createChatThread(createThreadRequest);
   let threadId = chatThreadClient.threadId;
   return threadId;
}

createChatThread().then(async threadId => {
   console.log(`Thread created:${threadId}`);
   // PLACEHOLDERS
   // <CREATE CHAT THREAD CLIENT>
   // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
   // <SEND MESSAGE TO A CHAT THREAD>
   // <LIST MESSAGES IN A CHAT THREAD>
   // <ADD NEW MEMBER TO THREAD>
   // <LIST MEMBERS IN A THREAD>
   // <REMOVE MEMBER FROM THREAD>
});
```

Cserélje le **USER_ID_FOR_JACK** és **USER_ID_FOR_GEETA** az előző lépésből beszerzett felhasználói azonosítókkal (felhasználók létrehozása és [felhasználói hozzáférési tokenek](../../access-tokens.md)kiadása)

Amikor frissíti a böngésző lapját, a következőnek kell megjelennie a konzolon:
```console
Thread created: <threadId>
```

## <a name="get-a-chat-thread-client"></a>Csevegési szál ügyfelének beolvasása

A `getChatThreadClient` metódus egy olyan `chatThreadClient` szálat ad vissza, amely már létezik. Használható a létrehozott szálon végzett műveletek végrehajtásához: Tagok hozzáadása, üzenet küldése stb. a szálazonosító a meglévő csevegési szál egyedi azonosítója.

```JavaScript

let chatThreadClient = await chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`);

```
Vegye fel ezt a kódot a `<CREATE CHAT THREAD CLIENT>` Megjegyzés helyére a **client.js**, frissítse a böngésző fület, és ellenőrizze a konzolt, és tekintse meg a következőt:
```console
Chat Thread client for threadId: <threadId>
```

## <a name="send-a-message-to-a-chat-thread"></a>Üzenet küldése csevegési szálnak

A `sendMessage` metódus használatával küldhet csevegési üzenetet az imént létrehozott, szálazonosító által azonosított szálra.

`sendMessageRequest` a csevegő üzenet kérésének kötelező mezőinek leírása:

- `content`A csevegési üzenet tartalmának megadásához használja a következőt:

`sendMessageOptions` a csevegési üzenet kérésének választható mezőinek ismertetése:

- Ezzel `priority` a beállítással adható meg a csevegés prioritási szintje, például a "NORMAL" vagy a "High"; ezzel a tulajdonsággal az alkalmazásban lévő felhasználó felhasználói felületi mutatója lehet, hogy az üzenetre vagy egyéni üzleti logikára lenne szükség.   
- A használatával `senderDisplayName` adja meg a feladó megjelenítendő nevét;

A válasz `sendChatMessageResult` tartalmaz egy azonosítót, amely az üzenet egyedi azonosítója.

```JavaScript

let sendMessageRequest =
{
    content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
    priority: 'Normal',
    senderDisplayName : 'Jack'
};
let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
let messageId = sendChatMessageResult.id;
console.log(`Message sent!, message id:${messageId}`);

```
Vegye fel ezt a kódot a `<SEND MESSAGE TO A CHAT THREAD>` Megjegyzés helyére **client.js**, frissítse a böngésző fület, és tekintse meg a konzolt.
```console
Message sent!, message id:<number>
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Csevegési üzenetek fogadása csevegési szálból

A valós idejű jelzésekkel előfizethet az új bejövő üzenetek figyelésére, és ennek megfelelően frissítheti az aktuális üzeneteket a memóriában. Az Azure kommunikációs szolgáltatások olyan [események listáját támogatják, amelyek előfizethetnek a szolgáltatásra](../../../concepts/chat/concepts.md#real-time-signaling).

```JavaScript
// open notifications channel
await chatClient.startRealtimeNotifications();
// subscribe to new notification
chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    // your code here
});

```
Adja hozzá ezt a kódot a `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` Megjegyzés helyén **client.js**.
Frissítse a böngésző fület, és a konzolon egy üzenetet kell látnia `Notification chatMessageReceived` .

Azt is megteheti, hogy a csevegési üzeneteket lekérdezi a `listMessages` metódus megadott időközönkénti lekérdezésével. 

```JavaScript

let pagedAsyncIterableIterator = await chatThreadClient.listMessages();
let nextMessage = await pagedAsyncIterableIterator.next();
 while (!nextMessage.done) {
     let chatMessage = nextMessage.value;
     console.log(`Message :${chatMessage.content}`);
     // your code here
     nextMessage = await pagedAsyncIterableIterator.next();
 }

```
Adja hozzá ezt a kódot a `<LIST MESSAGES IN A CHAT THREAD>` Megjegyzés helyén **client.js**.
Frissítse a fület a konzolon, és keresse meg az ebben a csevegési szálban küldött üzenetek listáját.


`listMessages` az üzenet legújabb verzióját adja vissza, beleértve a és a használatával az üzenettel történt módosításokat és törléseket `updateMessage` is `deleteMessage` .
A törölt üzenetek esetében `chatMessage.deletedOn` egy DateTime értéket ad vissza, amely azt jelzi, hogy az üzenet törölve lett. A szerkesztett üzenetek esetében `chatMessage.editedOn` egy DateTime értéket ad vissza, amely azt jelzi, hogy mikor lett szerkesztve az üzenet. Az üzenetek létrehozásának eredeti időpontja elérhető a használatával `chatMessage.createdOn` , amely az üzenetek rendezésére használható.

`listMessages` a által azonosítható különböző típusú üzeneteket ad vissza `chatMessage.type` . Ezek a típusok a következők:

- `Text`: Egy szál tagja által küldött normál csevegési üzenet.

- `ThreadActivity/TopicUpdate`: Az a Rendszerüzenet, amely azt jelzi, hogy a témakör frissítve lett.

- `ThreadActivity/AddMember`: Az a Rendszerüzenet, amely azt jelzi, hogy egy vagy több tag hozzá lett adva a csevegési szálhoz.

- `ThreadActivity/RemoveMember`: Az a Rendszerüzenet, amely azt jelzi, hogy a tag el lett távolítva a csevegési szálból.

További részletek: [üzenetek típusai](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Felhasználó hozzáadása a csevegési szálhoz tagként

A csevegési szál létrehozása után hozzáadhat és eltávolíthat felhasználókat. A felhasználók hozzáadásával hozzáférést biztosíthat számukra, hogy üzeneteket küldjön a csevegési szálba, és más tagokat adjon hozzá vagy távolítson el. A metódus meghívása előtt `addMembers` Győződjön meg arról, hogy új hozzáférési jogkivonatot és identitást szerzett az adott felhasználó számára. A felhasználónak szüksége lesz erre a hozzáférési jogkivonatra ahhoz, hogy inicializálja a csevegési ügyfelet.

`addMembersRequest` leírja a kérelmeket tartalmazó objektumot, amelyben `members` felsorolja a csevegési szálhoz hozzáadandó tagokat.
- `user`, kötelező, az a kommunikációs felhasználó, akit fel kell venni a csevegési szálba.
- `displayName`, nem kötelező, a szál tagja megjelenítendő neve.
- `shareHistoryTime`, nem kötelező, az az idő, amelyből a csevegési előzmények meg vannak osztva a taggal. Ha meg szeretné osztani a beszélgetési szál kezdete óta megjelenő előzményeket, állítsa ezt a tulajdonságot bármilyen dátumra vagy kevesebbre, mint a szál létrehozási ideje. Ha meg szeretné osztani az előző előzményeket a tag hozzáadásakor, állítsa az aktuális dátumra. A részleges előzmények megosztásához állítsa azt a választott dátumra.

```JavaScript

let addMembersRequest =
{
    members: [
        {
            user: { communicationUserId: '<NEW_MEMBER_USER_ID>' },
            displayName: 'Jane'
        }
    ]
};

await chatThreadClient.addMembers(addMembersRequest);

```
**NEW_MEMBER_USER_ID** cseréje [új felhasználói azonosítóval](../../access-tokens.md) adja hozzá ezt a kódot a Megjegyzés helyett `<ADD NEW MEMBER TO THREAD>` **client.js**

## <a name="list-users-in-a-chat-thread"></a>Csevegési szál felhasználóinak listázása
```JavaScript
async function listThreadMembers() {
   let pagedAsyncIterableIterator = await chatThreadClient.listMembers();
   let next = await pagedAsyncIterableIterator.next();
   while (!next.done) {
      let user = next.value;
      console.log(`User :${user.displayName}`);
      next = await pagedAsyncIterableIterator.next();
   }
}
await listThreadMembers();
```
Vegye fel ezt a kódot a `<LIST MEMBERS IN A THREAD>` Megjegyzés helyére **client.js**, frissítse a böngésző fület, és ellenőrizze a konzolt, és tekintse meg a szál felhasználóiról szóló információkat.

## <a name="remove-user-from-a-chat-thread"></a>Felhasználó eltávolítása csevegési szálból

Egy tag hozzáadásához hasonlóan a csevegési szálból is eltávolíthat tagokat. Az eltávolításhoz követnie kell a hozzáadott tagok azonosítóit.

Használja a `removeMember` metódust, ahol `member` a kommunikációs felhasználó el lesz távolítva a szálból.

```JavaScript

await chatThreadClient.removeMember({ communicationUserId: <MEMBER_ID> });
await listThreadMembers();
```
Cserélje le a **MEMBER_IDt** az előző lépésben használt felhasználói azonosítóra (<NEW_MEMBER_USER_ID>).
Adja hozzá ezt a kódot a `<REMOVE MEMBER FROM THREAD>` Megjegyzés helyén **client.js**,
