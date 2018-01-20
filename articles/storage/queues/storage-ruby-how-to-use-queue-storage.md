---
title: "A Ruby a Queue storage használata |} Microsoft Docs"
description: "Útmutató az Azure Queue szolgáltatás segítségével hozza létre, és törli az üzenetsorok, és helyezze, get, és törli az üzenetet. Ruby írt minták."
services: storage
documentationcenter: ruby
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 59c2d81b-db9c-46ee-ade2-2f0caae6b1e6
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 4a9d431e7bd1f204b9ba21b90d9fd6a0894d5d2d
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-queue-storage-from-ruby"></a>How to use Queue storage from Ruby (A Queue Storage használata Rubyval)
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre a szolgáltatást a Microsoft Azure Queue Storage szolgáltatást használó általános forgatókönyvhöz. A minták íródtak, a Ruby Azure API használatával.
Az ismertetett forgatókönyvek **beszúrása**, **megtekintésekor**, **első**, és **törlése** üzenetek várólistára, valamint **létrehozása és törlése várólisták**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Ruby-alkalmazás létrehozása
Ruby-alkalmazás létrehozása. Útmutatásért lásd: [Ruby sínek webalkalmazás egy Azure virtuális gépen](../../virtual-machines/linux/classic/ruby-rails-web-app.md).

## <a name="configure-your-application-to-access-storage"></a>Állítsa be az alkalmazását tároló elérése érdekében
Az Azure storage használatához szüksége töltse le és használja a Ruby azure csomagot, amely tartalmaz egy kényelmi szalagtár szerepel, amely a többi tárolási szolgáltatásokkal kommunikálni.

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems használja a csomag beszerzése
1. Használjon például egy parancssori felületet **PowerShell** (Windows), **Terminálszolgáltatások** (Mac), vagy **Bash** (Unix).
2. Írja be a "gem telepítése azure" gem és függőségeinek telepítéséhez a parancsablakban.

### <a name="import-the-package"></a>A csomag importálása
Kedvenc szövegszerkesztőjével használja, a Ruby, ahol tárolására használni kívánt fájl elejéhez adja hozzá a következőket:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Az Azure Storage-kapcsolat beállítása
Az azure-moduljának a környezeti változókat olvassák **AZURE\_tárolási\_fiók** és **AZURE\_tárolási\_ACCESS_KEY** az Azure storage-fiókhoz való kapcsolódáshoz szükséges információkat. Ha ezek a környezeti változók nem, meg kell adnia a fiók használata előtt **Azure::QueueService** az alábbi kódra:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Ezek az értékek beolvasása klasszikus vagy erőforrás-kezelő storage-fiókot az Azure-portálon:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Nyissa meg a használni kívánt tárfiókot.
3. Kattintson a beállítások panelen kattintson a jobb **hívóbetűk**.
4. A elérési kulcsok paneljén megjelenő láthatja, a hozzáférési kulcs 1 és 2 elérési kulcsot. Ezek bármelyikét használhatja. 
5. A Másolás ikonra a kulcs másolása a vágólapra. 

## <a name="how-to-create-a-queue"></a>Útmutató: A várólista létrehozása
Az alábbi kód létrehoz egy **Azure::QueueService** objektum, amely lehetővé teszi a várólisták használata.

```ruby
azure_queue_service = Azure::QueueService.new
```

Használja a **create_queue()** várólista létrehozása a megadott nevű metódus.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Útmutató: A várólista üzenet beszúrása
Üzenet beszúrása egy üzenetsort, használja a **create_message()** módszert, hozzon létre egy új üzenetet, és vegye fel a várólistára.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Útmutató: A következő üzenet megtekintése
Is bepillanthat, hogy egy sor elején található üzenetbe anélkül, hogy eltávolítaná az üzenetsorból meghívásával a **betekintés\_messages()** metódust. Alapértelmezés szerint **betekintés\_messages()** lekéri egyetlen üzenetben. Betekintés kívánt hány üzeneteket is megadható.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Útmutató: A következő üzenet created
Eltávolíthatja a üzenetet az üzenetsorból két lépésben.

1. A hívás esetén **lista\_messages()**, a hibaüzenet a következő várólista alapértelmezés szerint. Azt szeretné, hogy hány üzenet is megadható. Az üzenetek által visszaadott **lista\_messages()** ebből a várólistából üzeneteket olvasó többi kód láthatatlanná válik. Akkor adjon át a láthatósági időkorlátot másodpercben paraméterként.
2. Szeretné távolítani az üzenetet az üzenetsorból, meg kell is hívni **delete_message()**.

A kétlépéses folyamat eltávolításával előállított üzenet biztosítja, hogy a kód nem tudja feldolgozni egy üzenetet, hardver- vagy szoftverhiba miatt, a kód egy másik példánya is megkaphassa ugyanazt az üzenetet, és próbálkozzon újra. A kód hívások **törlése\_message()** jobb gombbal az üzenet feldolgozása után.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Útmutató: Az aszinkron üzenet tartalmának módosítása
Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Az alábbi által használt kódot a **update_message()** üzenet frissítési módjának. A metódus egy rekord, amely tartalmazza az üzenetsorban lévő üzenetet és UTC dátum időt jelző érték, ha az üzenet a várólistában látható lesz a pop fogadását fog visszaadni.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Útmutató: További beállítások üzenetmozgatót üzenetek
Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból.

1. A kötegelt üzenet kérheti le.
2. Beállíthat egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak hosszabb vagy rövidebb idő teljesen feldolgozni az egyes üzeneteket.

Az alábbi példakód a **lista\_messages()** módszer segítségével 15 üzenetek egy hívásban. Majd kinyomtatja, és törli az egyes üzeneteket. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Útmutató: Az üzenetsor hosszának lekérése
A várólistában lévő üzenetek számának becslése kérheti le. A **beolvasása\_várólista\_metadata()** metódus kéri a queue szolgáltatás adhatja vissza a hozzávetőleges üzenetek száma és az üzenetsorral kapcsolatos metaadatokat.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Útmutató: A várólista törlése
Egy üzenetsor és a benne tárolt összes üzenet törléséhez hívja meg a **törlése\_queue()** a várólista-objektum metódust.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a queue storage alapjait, az alábbi hivatkozásokból tájékozódhat az összetettebb tárolási feladatok elvégzéséről.

* Látogasson el a [Azure Storage csapat blogja](http://blogs.msdn.com/b/windowsazurestorage/)
* Látogasson el a [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) GitHub tárházából

A cikkben szereplő Azure Queue szolgáltatás és az Azure Service Bus-üzenetsorok tárgyalt összehasonlításához a [Service Bus-üzenetsorok használata](/develop/ruby/how-to-guides/service-bus-queues/) cikk című [Azure várólisták és a Service Bus-üzenetsorok - az képest és ellentétben](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)