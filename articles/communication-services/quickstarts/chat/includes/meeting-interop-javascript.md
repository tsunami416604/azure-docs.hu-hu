---
title: Gyors útmutató – csapatokhoz való csatlakozás
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 0c41771af81989ff965098a762338216db54fd27
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97578122"
---
## <a name="join-the-meeting-chat"></a>Csatlakozás az értekezlet csevegéséhez 

A csapatok együttműködési képességének engedélyezése után a kommunikációs szolgáltatások felhasználója a hívó ügyféloldali kódtár használatával csatlakozhat vendégként a csapathoz. A híváshoz való csatlakozáskor a rendszer résztvevőként hozzáadja őket az értekezlet csevegéséhez, ahol üzeneteket küldhet és fogadhat a hívás más felhasználóival. A felhasználó nem férhet hozzá a híváshoz való csatlakozás előtt küldött csevegési üzenetekhez. 

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>A kommunikációs szolgáltatások felhasználóinak csevegési szálának beolvasása a csapatoknak

Először hozza létre a `ChatThreadClient` for the Meeting csevegési szálat. Elemezze az értekezlet hivatkozását, vagy használja a Graph API-kat az értekezlet-AZONOSÍTÓval a szál AZONOSÍTÓjának lekéréséhez. 

- A csapatok Értekezletének hivatkozása a következőképpen néz ki: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here` . A szál AZONOSÍTÓjának helye a `meeting_chat_thread_id` hivatkozás. 
- Ha rendelkezik az értekezlet AZONOSÍTÓval, a szál AZONOSÍTÓjának lekéréséhez használhatja a [Graph API](https://docs.microsoft.com/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta) . A [Get API](https://docs.microsoft.com/graph/api/onlinemeeting-get?view=graph-rest-beta&tabs=http%22%20%5C) -válasz egy objektummal fog rendelkezni `chatInfo` , amely tartalmazza a `threadID` . 

Ha már rendelkezik a csevegési szál azonosítójával, a csevegési szál ügyfelét a JavaScript csevegő ügyféloldali kódtár használatával érheti el: 

```javascript
let chatThreadClient = await chatClient.getChatThreadClient(threadId); 

console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`); 
```
  
A használható a `chatThreadClient` csevegési szál tagjainak listázására, a csevegési előzmények beszerzésére és az üzenetek küldésére.  

## <a name="send-and-receive-messages"></a>Üzenetek küldése és fogadása  

A használatával `SendMessage` küldhet üzenetet az értekezlet csevegésének. A bejövő üzenetek fogadásához, például a nem támogatott eseményekre való előfizetéshez nem `chatMessageReceived` használható valós idejű jelzés, mert ez a forgatókönyv nem engedélyezett. A legfrissebb üzenetek beszerzéséhez lekérdezheti az `ListMessages` API-t. Az együttműködési forgatókönyv esetében az `ListMessages` API immár három új típusú üzenet visszaadását támogatja:
- `RichText/HTML`
- `ThreadActivity/MemberJoined`
- `ThreadActivity/MemberLeft` </br>

További információ az üzenetek típusairól: [itt](../../../concepts/chat/concepts.md). 

**Megjegyzés** – jelenleg csak üzenetek küldése és fogadása támogatott a Teams együttműködési forgatókönyvek esetében. Más funkciók, mint például a kijelzők és a kommunikációs szolgáltatások felhasználói a Teams Meeting más felhasználóinak hozzáadásával vagy eltávolításával még nem támogatottak.  

 
