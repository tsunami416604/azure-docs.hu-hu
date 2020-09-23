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
ms.openlocfilehash: f32b81184b9a96760e43d0cd64c27459952c5953
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947190"
---
## <a name="prerequisites"></a>Előfeltételek
Az első lépések előtt ügyeljen a következőre:
- Aktív előfizetéssel rendelkező Azure-fiók létrehozása. Részletekért tekintse meg a [fiók ingyenes létrehozását](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ismertető témakört. 
- A [Visual Studio](https://visualstudio.microsoft.com/downloads/) telepítése 
- Hozzon létre egy Azure kommunikációs szolgáltatások erőforrást. További információ: [Azure kommunikációs erőforrás létrehozása](../../create-communication-resource.md). Ehhez a rövid útmutatóhoz fel kell jegyeznie az erőforrás- **végpontot** .
- [Felhasználói hozzáférési jogkivonat](../../access-tokens.md). Ügyeljen arra, hogy a hatókört a "csevegés" értékre állítsa, és jegyezze fel a jogkivonat karakterláncát, valamint a userId karakterláncot.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `ChatQuickstart` . Ez a parancs egy egyszerű ""Helló világ!"alkalmazás" C#-projektet hoz létre egyetlen forrásfájlban: **program.cs**.

```console
dotnet new console -o ChatQuickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába, és használja az `dotnet build` parancsot az alkalmazás fordításához.

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>A csomag telepítése

Az Azure kommunikációs csevegési ügyféloldali kódtár telepítése a .NET-hez

```PowerShell
dotnet add package Azure.Communication.Chat
``` 

## <a name="object-model"></a>Objektummodell

A következő osztályok a C#-hoz készült Azure Communication Services csevegő függvénytárának főbb funkcióit kezelik.

| Név                                  | Leírás                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Ez az osztály szükséges a csevegési funkciókhoz. Létrehozza azt az előfizetési adatokkal, és felhasználhatja a szálak létrehozásához, lekéréséhez és törléséhez. |
| ChatThreadClient | Ez az osztály szükséges a csevegési szál működéséhez. A példányokat a ChatClient keresztül szerezheti be, és használhatja az üzenetek küldésére/fogadására/frissítésére/törlésére, a felhasználók hozzáadására/eltávolítására/lekérésére, valamint a beírási értesítések küldésére |

## <a name="create-a-chat-client"></a>Csevegési ügyfél létrehozása

Csevegési ügyfél létrehozásához használja a kommunikációs szolgáltatások végpontját és az előfeltételként szükséges lépések részeként létrehozott hozzáférési jogkivonatot. Az `CommunicationIdentityClient` ügyfél-függvénytár osztályát kell használnia `Administration` egy felhasználó létrehozásához, és ki kell állítania a tokent a csevegési ügyfélnek való továbbításhoz. További információ a [felhasználói hozzáférési tokenekről](../../access-tokens.md).

```csharp
using Azure.Communication.Identity;
using Azure.Communication.Chat;

// Your unique Azure Communication service endpoint
Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(<Access_Token>);
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential);
```

## <a name="start-a-chat-thread"></a>Csevegési szál elindítása

`createChatThread`Csevegési szál létrehozásához használja a metódust.
- A használatával `topic` témakört adhat ehhez a csevegéshez; A témakör a funkció használatával frissíthető a csevegési szál létrehozása után `UpdateThread` .
- `members`A tulajdonság használatával adja át a `ChatThreadMember` csevegési szálhoz hozzáadandó objektumok listáját. Az `ChatThreadMember` objektum egy objektummal van inicializálva `CommunicationUser` . Egy objektum lekéréséhez `CommunicationUser` meg kell adnia egy hozzáférési azonosítót, amelyet a következő utasítások alapján hozott létre [egy felhasználó létrehozásához](../../access-tokens.md#create-a-user) :

A válasz a `chatThreadClient` létrehozott csevegési szálon végzett műveletek végrehajtásához használható: Tagok hozzáadása a csevegési szálhoz, üzenet küldése, üzenet törlése stb. Tartalmazza azt az `Id` attribútumot, amely a csevegési szál egyedi azonosítója. 

```csharp
var chatThreadMember = new ChatThreadMember(new CommunicationUser("<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync(topic: "Chat Thread topic C# sdk", members: new[] { chatThreadMember });
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>Csevegési szál ügyfelének beolvasása
A `GetChatThreadClient` metódus egy szál-ügyfelet ad vissza egy már létező szálhoz. Használható a létrehozott szálon végzett műveletek végrehajtásához: Tagok hozzáadása, üzenet küldése stb. a szálazonosító a meglévő csevegési szál egyedi azonosítója.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = await chatClient.GetChatThreadClient(threadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Üzenet küldése csevegési szálnak

A `SendMessage` metódus használatával küldhet üzenetet a szálazonosító által azonosított szálnak.

- `content`A csevegési üzenet tartalmának megadásához használja a következőt:.
- Ezzel `priority` a beállítással megadhatja az üzenet prioritási szintjét, például a "NORMAL" vagy a "magas" értéket, ha nincs megadva, a "NORMAL" értéket fogja használni.
- Ezzel a `senderDisplayName` beállítással megadhatja a küldő megjelenítendő nevét, ha nincs megadva, a rendszer üres nevet használ.

`SendChatMessageResult` az üzenet elküldésekor kapott válasz egy azonosítót tartalmaz, amely az üzenet egyedi azonosítója.

```csharp
var content = "hello world";
var priority = ChatMessagePriority.Normal;
var senderDisplayName = "sender name";

SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(content, priority, senderDisplayName);
string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Csevegési üzenetek fogadása csevegési szálból

A csevegési üzeneteket lekérheti úgy, hogy a `GetMessages` csevegési szál ügyfélprogramjában megadott időközönként lekérdezi a metódust.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Sender.Id}:{message.Content}");
}
```

`GetMessages` egy opcionális `DateTimeOffset` paramétert használ. Ha az eltolás meg van adva, a rendszer a kapott, frissített vagy törölt üzeneteket fogja kapni. Vegye figyelembe, hogy a rendszer az eltolási idő előtt fogadta az üzeneteket, de azt a program a visszaadott érték után szerkesztette vagy eltávolította.

`GetMessages` az üzenet legújabb verzióját adja vissza, beleértve a és a használatával az üzenettel történt módosításokat és törléseket `UpdateMessage` is `DeleteMessage` . A törölt üzenetek esetében `chatMessage.DeletedOn` egy DateTime értéket ad vissza, amely azt jelzi, hogy az üzenet törölve lett. A szerkesztett üzenetek esetében `chatMessage.EditedOn` egy DateTime értéket ad vissza, amely azt jelzi, hogy mikor lett szerkesztve az üzenet. Az üzenet létrehozásának eredeti időpontja a használatával érhető el `chatMessage.CreatedOn` , és az üzenetek rendezésére is használható.

`GetMessages` a által azonosítható különböző típusú üzeneteket ad vissza `chatMessage.Type` . Ezek a típusok a következők:

- `Text`: Egy szál tagja által küldött normál csevegési üzenet.

- `ThreadActivity/TopicUpdate`: Az a Rendszerüzenet, amely azt jelzi, hogy a témakör frissítve lett.

- `ThreadActivity/AddMember`: Az a Rendszerüzenet, amely azt jelzi, hogy egy vagy több tag hozzá lett adva a csevegési szálhoz.

- `ThreadActivity/DeleteMember`: Az a Rendszerüzenet, amely azt jelzi, hogy a tag el lett távolítva a csevegési szálból.

További részletek: [üzenetek típusai](../../../concepts/chat/concepts.md#message-types).

## <a name="update-a-message"></a>Üzenet frissítése

A következő meghívásával frissítheti a már elküldött üzeneteket `UpdateMessage` `ChatThreadClient` .

```csharp
string id = "id-of-message-to-edit";
string content = "updated content";
await chatThreadClient.UpdateMessageAsync(id, content);
```

## <a name="deleting-a-message"></a>Üzenet törlése

Az üzenetet úgy törölheti, hogy meghívja a következőt: `DeleteMessage` `ChatThreadClient` .

```csharp
string id = "id-of-message-to-delete";
await chatThreadClient.DeleteMessageAsync(id);
```

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Felhasználó hozzáadása a csevegési szálhoz tagként

Miután létrehozta a szálat, hozzáadhat és eltávolíthat felhasználókat. A felhasználók hozzáadásával hozzáférést biztosíthat számukra, hogy üzeneteket küldhessen a szálnak, illetve más tagokat vegyen fel/távolítson el. A hívás előtt `AddMembers` Győződjön meg arról, hogy új hozzáférési jogkivonatot és identitást szerzett az adott felhasználó számára. A felhasználónak szüksége lesz erre a hozzáférési jogkivonatra ahhoz, hogy inicializálja a csevegési ügyfelet.

`AddMembers`A metódus használatával a szálazonosító által azonosított szálat adhat hozzá a szálhoz.

 - A használatával `members` listázhatja a csevegési szálba felvenni kívánt tagokat;
 - `User`, kötelező, az új felhasználóhoz kapott identitás.
 - `DisplayName`, nem kötelező, a szál tagja megjelenítendő neve.
 - `ShareHistoryTime`, nem kötelező, a csevegési előzmények megosztásának időpontja a taggal. Ha meg szeretné osztani a előzményeket a csevegési szál kezdete óta, állítsa azt a DateTime. MinValue értékre. Ha a tag hozzáadása előtt nem szeretne előzményeket megosztani, állítsa be az aktuális időpontra. A részleges előzmények megosztásához állítsa azt egy időpontra a szál létrehozása és az aktuális idő között.

```csharp
ChatThreadMember member = new ChatThreadMember(communicationUser);
member.DisplayName = "display name member 1";
member.ShareHistoryTime = DateTime.MinValue; // share all history
await chatThreadClient.AddMembersAsync(members: new[] {member});
```
## <a name="remove-user-from-a-chat-thread"></a>Felhasználó eltávolítása csevegési szálból

A felhasználók egy szálhoz való hozzáadásához hasonlóan a csevegési szálból is eltávolíthat felhasználókat. Ehhez nyomon kell követnie a felvett tagok identitását (CommunicationUser).

```csharp
await chatThreadClient.RemoveMemberAsync(communicationUser);
```

## <a name="run-the-code"></a>A kód futtatása

Futtassa az alkalmazást az alkalmazás könyvtárából a `dotnet run` paranccsal.

```console
dotnet run
```
