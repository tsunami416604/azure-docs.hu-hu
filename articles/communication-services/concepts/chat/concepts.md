---
title: Csevegési fogalmak az Azure kommunikációs szolgáltatásokban
titleSuffix: An Azure Communication Services concept document
description: Ismerje meg a kommunikációs szolgáltatások csevegési fogalmait.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: f33d7efd1c136619767c3eadd93740442ae7239a
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762042"
---
# <a name="chat-concepts"></a>Csevegéssel kapcsolatos alapfogalmak

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Az Azure kommunikációs szolgáltatások csevegési ügyfélszoftverei valós idejű SMS-beszélgetések hozzáadására használhatók az alkalmazásokhoz. Ez az oldal a legfontosabb csevegési fogalmakat és képességeket foglalja össze.

Tekintse meg a [kommunikációs szolgáltatások csevegési függvénytárának áttekintését](./sdk-features.md) , hogy többet tudjon meg az adott ügyféloldali függvénytár-nyelvekről és-képességekről.

## <a name="chat-overview"></a>Csevegés – áttekintés 

Csevegési beszélgetések a csevegési szálakon belül történnek. Egy csevegési szál Számos üzenetet és számos felhasználót tartalmazhat. Minden üzenet egyetlen szálhoz tartozik, és egy felhasználó egy vagy több szál része lehet. 

A csevegési szál minden felhasználójának neve tag. Egy csevegési szálban akár 250 taggal is rendelkezhet. Csak a szál tagjai küldhetnek és fogadhatnak üzeneteket, vagy hozzáadhatnak vagy eltávolíthatnak tagokat egy csevegési szálban. Az üzenet maximális megengedett mérete körülbelül 28KB. A csevegési szálban lévő összes üzenetet lekérheti a `List/Get Messages` művelettel. A kommunikációs szolgáltatások addig tárolja a csevegési előzményeket, amíg nem hajt végre törlési műveletet a csevegési szálon vagy az üzeneten, vagy amíg egyetlen tag sem marad a csevegési szálban, ahol az árva, és a törlésre van feldolgozva.   

A több mint 20 taggal rendelkező csevegési szálak esetében a beolvasás és a begépelési mutatók funkció le van tiltva. 

## <a name="chat-architecture"></a>Csevegési architektúra

Két fő részből áll a csevegő architektúra: 1) megbízható szolgáltatás és 2) ügyfélalkalmazás.

:::image type="content" source="../../media/chat-architecture.png" alt-text="A kommunikációs szolgáltatások csevegési architektúráját bemutató ábra.":::

 - **Megbízható szolgáltatás:** A csevegési munkamenetek megfelelő kezeléséhez olyan szolgáltatásra van szükség, amely segítséget nyújt a kommunikációs szolgáltatásokhoz való kapcsolódáshoz az erőforrás-kapcsolati karakterlánc használatával. Ez a szolgáltatás felelős a csevegési szálak létrehozásához, a szálak tagságának kezeléséhez, valamint hozzáférési jogkivonatok biztosításához a felhasználók számára. A hozzáférési jogkivonatokkal kapcsolatos további információk a [hozzáférési tokenek](../../quickstarts/access-tokens.md) rövid útmutatójában találhatók.

 - **Ügyfélalkalmazás:**  Az ügyfélalkalmazás csatlakozik a megbízható szolgáltatáshoz, és fogadja a közvetlenül a kommunikációs szolgáltatásokhoz való csatlakozáshoz használt hozzáférési jogkivonatokat. A kapcsolatfelvétel után az ügyfélalkalmazás küldhet és fogadhat üzeneteket.
    
## <a name="message-types"></a>Üzenetek típusai

A kommunikációs szolgáltatások csevegés megosztja a felhasználó által létrehozott üzeneteket, valamint a szál- **tevékenységek**nevű rendszer által generált üzeneteket. A hozzászóláslánc-tevékenységek akkor jönnek létre, amikor egy csevegési szál frissül. `List Messages` `Get Messages` Egy csevegési szál hívásakor az eredmény a felhasználó által generált szöveges üzeneteket, valamint a rendszerüzeneteket is tartalmazza időrendben. Ez segít megállapítani, hogy mikor lett hozzáadva vagy eltávolítva egy tag, vagy mikor frissítették a csevegési szál témakört. A támogatott üzenetek típusai a következők:  

 - `Text`: A csevegési beszélgetés részeként a felhasználó által összeállított és küldött tényleges üzenet. 
 - `ThreadActivity/AddMember`: Az a Rendszerüzenet, amely azt jelzi, hogy egy vagy több tag hozzá lett adva a csevegési szálhoz. Például:

```xml

<addmember>
    <eventtime>1598478187549</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</id>
        <friendlyName>User 1</friendlyName>
    </detailedtargetinfo>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</addmember>

```  

- `ThreadActivity/DeleteMember`: Az a Rendszerüzenet, amely azt jelzi, hogy a tag el lett távolítva a csevegési szálból. Például:

```xml

<deletemember>
    <eventtime>1598478187642</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</deletemember>

```

- `ThreadActivity/TopicUpdate`: Az a Rendszerüzenet, amely azt jelzi, hogy a témakör frissítve lett. Például:

```xml

<topicupdate>
    <eventtime>1598477591811</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <value>New topic</value>
</topicupdate>

```

## <a name="real-time-signaling"></a>Valós idejű jelzés 

A csevegés JavaScript-ügyféloldali könyvtára valós idejű jelzéseket tartalmaz. Ez lehetővé teszi az ügyfeleknek, hogy valós idejű frissítéseket és beérkező üzeneteket hallgassanak egy csevegési szálra anélkül, hogy le kellene kérdezni az API-kat. Az elérhető események a következők:

 - `ChatMessageReceived` – új üzenet küldése egy olyan csevegési szálnak, amelynek tagja a felhasználó. Ezt az eseményt a rendszer nem továbbítja automatikusan generált rendszerüzenetek számára, amelyeket az előző témakörben ismertetünk.  
 - `ChatMessageEdited` – Ha egy üzenet olyan csevegési szálban van szerkesztve, amelynek tagja a felhasználó. 
 - `ChatMessageDeleted` – Ha töröl egy üzenetet egy csevegési szálban, amelynek tagja a felhasználó. 
 - `TypingIndicatorReceived` – Ha egy másik tag egy olyan csevegési szálba ír be egy üzenetet, amelyben a felhasználó tagja. 
 - `ReadReceiptReceived` -Ha egy másik tag elolvasta azt az üzenetet, amelyet a felhasználó elküldött egy csevegési szálban. 

## <a name="chat-events"></a>Csevegési események 

A valós idejű jelzések lehetővé teszik, hogy a felhasználók valós időben beszélgessenek. A szolgáltatások a Azure Event Grid segítségével előfizethetnek a csevegéssel kapcsolatos eseményekre. További részletek: Event- [kezelés fogalma](../event-handling.md).

## <a name="using-cognitive-services-with-chat-client-library-to-enable-intelligent-features"></a>Cognitive Services használata a csevegő ügyféloldali kódtár használatával az intelligens funkciók engedélyezéséhez

Az [Azure kognitív API](https://docs.microsoft.com/azure/cognitive-services/) -k és a csevegési ügyféloldali kódtár segítségével intelligens funkciókat adhat az alkalmazásaihoz. Megteheti például a következőt:

- Lehetővé teheti a felhasználók számára, hogy különböző nyelveken beszélgessenek egymással. 
- Egy támogatási ügynök rangsorolhatja a jegyeket úgy, hogy kideríti az ügyféltől beérkező problémák negatív véleményét.
- Elemezze a beérkező üzeneteket a kulcs észleléséhez és az entitások felismeréséhez, majd az üzenet tartalma alapján kérje meg az alkalmazás felhasználójának a megfelelő adatokat.

Ezt úgy érheti el, ha a megbízható szolgáltatás egy csevegési szál tagjaként működik. Tegyük fel, hogy szeretné engedélyezni a nyelvi fordítást. Ennek a szolgáltatásnak a feladata a többi tag által cserélt üzenetek figyelése [1], a kognitív API-k meghívása a tartalom fordítására a kívánt nyelvre [2, 3], valamint a lefordított eredmény üzenetként való elküldése a csevegési szálban [4]. 

Így az üzenetek előzményei az eredeti és a lefordított üzeneteket is tartalmazzák. Az ügyfélalkalmazás felveheti az eredeti vagy lefordított üzenet megjelenítéséhez szükséges logikát. [Ebből](https://docs.microsoft.com/azure/cognitive-services/translator/quickstart-translator) a rövid útmutatóból megtudhatja, hogyan használhatja a kognitív API-kat a szövegek különböző nyelvekre való fordításához. 

:::image type="content" source="../media/chat/cognitive-services.png" alt-text="A kommunikációs szolgáltatások csevegési architektúráját bemutató ábra.":::

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés a csevegéssel](../../quickstarts/chat/get-started.md)

A következő dokumentumok érdekesek lehetnek:

- Ismerkedjen meg a [csevegési ügyfél könyvtárával](sdk-features.md)
