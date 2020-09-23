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
ms.openlocfilehash: 3c05596e16edc5243b8a97002a5cc5990c69ec43
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947186"
---
## <a name="prerequisites"></a>Előfeltételek
Az első lépések előtt ügyeljen a következőre:

- Aktív előfizetéssel rendelkező Azure-fiók létrehozása. Részletekért tekintse meg a [fiók ingyenes létrehozását](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ismertető témakört. 
- A [Python](https://www.python.org/downloads/) telepítése
- Hozzon létre egy Azure kommunikációs szolgáltatások erőforrást. További információ: [Azure kommunikációs erőforrás létrehozása](../../create-communication-resource.md). Ehhez a rövid útmutatóhoz fel kell jegyeznie az erőforrás- **végpontot**
- [Felhasználói hozzáférési jogkivonat](../../access-tokens.md). Ügyeljen arra, hogy a hatókört a "csevegés" értékre állítsa, és jegyezze fel a jogkivonat karakterláncát, valamint a userId karakterláncot.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Nyissa meg a terminált vagy a parancssorablakot, hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Szövegszerkesztő használatával hozzon létre egy **Start-chat.py** nevű fájlt a projekt gyökérkönyvtárában, és adja hozzá a program struktúráját, beleértve az alapszintű kivételek kezelését. Ehhez a rövid útmutatóhoz tartozó forráskódot a következő részekben adja hozzá ehhez a fájlhoz.

```python
import os
# Add required client library components from quickstart here

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-client-library"></a>Az ügyféloldali kódtár telepítése

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek kezelik a Pythonhoz készült Azure kommunikációs szolgáltatások csevegési függvénytárának főbb funkcióit.

| Név                                  | Leírás                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Ez az osztály szükséges a csevegési funkciókhoz. Létrehozza azt az előfizetési adatokkal, és felhasználhatja a szálak létrehozásához, lekéréséhez és törléséhez. |
| ChatThreadClient | Ez az osztály szükséges a csevegési szál működéséhez. A példányokat a ChatClient keresztül szerezheti be, és használhatja az üzenetek küldésére/fogadására/frissítésére/törlésére, a felhasználók hozzáadására/eltávolítására/lekérésére, valamint a beírási értesítések küldésére |

## <a name="create-a-chat-client"></a>Csevegési ügyfél létrehozása

Csevegési ügyfél létrehozásához használja a kommunikációs szolgáltatás végpontját, és a `Access Token` rendszer az előfeltételként szükséges lépések részeként generálta. További információ a [felhasználói hozzáférési tokenekről](../../access-tokens.md).

```console
pip install azure-communication-administration
```

```python
from azure.communication.chat import ChatClient, CommunicationUserCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationUserCredential(<Access Token>))
```

## <a name="start-a-chat-thread"></a>Csevegési szál elindítása

`create_chat_thread`Csevegési szál létrehozásához használja a metódust.

- A használatával `topic` adjon meg egy téma témakört; A témakör a funkció használatával frissíthető a csevegési szál létrehozása után `update_thread` .
- A a `members` `ChatThreadMember` csevegési szálba felvenni kívánt elemek listázásához használja `ChatThreadMember` a `CommunicationUser` típust `user` , amelyet a [felhasználó létrehozása](../../access-tokens.md#create-a-user) után kapott.

A válasz az `chat_thread_client` újonnan létrehozott csevegési szálon végez műveleteket, például tagok hozzáadásával a csevegési szálhoz, üzenet küldése, üzenet törlése stb. Egy olyan `thread_id` tulajdonságot tartalmaz, amely a csevegési szál egyedi azonosítója.

```python
from datetime import datetime
from azure.communication.chat import ChatThreadMember

topic="test topic"
thread_members=[ChatThreadMember(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]
chat_thread_client = chat_client.create_chat_thread(topic, thread_members)
```

## <a name="get-a-chat-thread-client"></a>Csevegési szál ügyfelének beolvasása
A get_chat_thread_client metódus egy szál-ügyfelet ad vissza egy már létező szálhoz. Használható a létrehozott szálon végzett műveletek végrehajtásához: Tagok hozzáadása, üzenet küldése stb. thread_id a meglévő csevegési szál egyedi azonosítója.

```python
thread_id = 'id'
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>Üzenet küldése csevegési szálnak

Az `send_message` imént létrehozott, szálazonosító által azonosított csevegési szálnak küldött üzenet küldése a metódus használatával.

- `content`A csevegési üzenet tartalmának megadásához használja a következőt:
- `priority`Az üzenet prioritási szintjének (például "normál" vagy "magas") megadására használható. Ez a tulajdonság használható felhasználói felületi jelzővel az alkalmazásban, hogy az üzenetet vagy az egyéni üzleti logikát hajtsa végre.
- A használatával `senderDisplayName` adja meg a feladó megjelenítendő nevét;

A válasz `SendChatMessageResult` tartalmaz egy azonosítót, amely az üzenet egyedi azonosítója.

```python
from azure.communication.chat import ChatMessagePriority

content='hello world'
priority=ChatMessagePriority.NORMAL
sender_display_name='sender name'

send_message_result = chat_thread_client.send_message(content, priority=priority, sender_display_name=sender_display_name)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Csevegési üzenetek fogadása csevegési szálból

A csevegési üzeneteket lekérheti a `list_messages` metódus megadott időközönkénti lekérdezésével.

```python
chat_messages = chat_thread_client.list_messages()
```
`list_messages` az üzenet legújabb verzióját adja vissza, beleértve a és a használatával az üzenettel történt módosításokat és törléseket `update_message` is `delete_message` . A törölt üzenetek esetében `ChatMessage.deleted_on` egy DateTime értéket ad vissza, amely azt jelzi, hogy az üzenet törölve lett. A szerkesztett üzenetek esetében `ChatMessage.edited_on` egy DateTime értéket ad vissza, amely azt jelzi, hogy mikor lett szerkesztve az üzenet. Az üzenetek létrehozásának eredeti időpontja elérhető a használatával `ChatMessage.created_on` , amely az üzenetek rendezésére használható.

`list_messages` a által azonosítható különböző típusú üzeneteket ad vissza `ChatMessage.type` . Ezek a típusok a következők:

- `Text`: Egy szál tagja által küldött normál csevegési üzenet.

- `ThreadActivity/TopicUpdate`: Az a Rendszerüzenet, amely azt jelzi, hogy a témakör frissítve lett.

- `ThreadActivity/AddMember`: Az a Rendszerüzenet, amely azt jelzi, hogy egy vagy több tag hozzá lett adva a csevegési szálhoz.

- `ThreadActivity/DeleteMember`: Az a Rendszerüzenet, amely azt jelzi, hogy a tag el lett távolítva a csevegési szálból.

További részletek: [üzenetek típusai](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Felhasználó hozzáadása a csevegési szálhoz tagként

A csevegési szál létrehozása után hozzáadhat és eltávolíthat felhasználókat. A felhasználók hozzáadásával hozzáférést biztosíthat számukra, hogy üzeneteket küldjön a csevegési szálba, és más tagokat adjon hozzá vagy távolítson el. A metódus meghívása előtt `add_members` Győződjön meg arról, hogy új hozzáférési jogkivonatot és identitást szerzett az adott felhasználó számára. A felhasználónak szüksége lesz erre a hozzáférési jogkivonatra ahhoz, hogy inicializálja a csevegési ügyfelet.

`add_members`A metódus használatával a szálazonosító által azonosított szálat adhat hozzá a szálhoz.

- A használatával `members` listázhatja a csevegési szálba felvenni kívánt tagokat;
- `user`, kötelező, a `CommunicationUser` által létrehozott `CommunicationIdentityClient` [felhasználó létrehozása](../../access-tokens.md#create-a-user)
- `display_name`, nem kötelező, a szál tagja megjelenítendő neve.
- `share_history_time`, nem kötelező, az az idő, amelyből a csevegési előzmények meg vannak osztva a taggal. Ha meg szeretné osztani a beszélgetési szál kezdete óta megjelenő előzményeket, állítsa ezt a tulajdonságot bármilyen dátumra vagy kevesebbre, mint a szál létrehozási ideje. Ha meg szeretné osztani az előző előzményeket a tag hozzáadásakor, állítsa az aktuális dátumra. A részleges előzmények megosztásához állítsa azt egy közbenső dátumra.

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadMember
from datetime import datetime
member = ChatThreadMember(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())
thread_members = [member]
chat_thread_client.add_members(thread_members)
```

## <a name="remove-user-from-a-chat-thread"></a>Felhasználó eltávolítása csevegési szálból

Egy tag hozzáadásához hasonlóan egy szálból is eltávolíthat tagokat. Az eltávolításhoz követnie kell a hozzáadott tagok azonosítóit.

`remove_member`A szálazonosító által azonosított szálból távolítsa el a szál tagot a metódus használatával.
- `user` a CommunicationUser el kell távolítani a szálból.

```python
chat_thread_client.remove_member(user)
```

## <a name="run-the-code"></a>A kód futtatása

Futtassa az alkalmazást az alkalmazás könyvtárából a `python` paranccsal.

```console
python start-chat.py
```
