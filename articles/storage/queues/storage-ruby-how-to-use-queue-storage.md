---
title: A Queue storage, a Ruby használatával |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure Queue szolgáltatás létrehozása és törlése, üzenetsorok, és helyezze, beolvasása, és törli az üzenetet. Ruby nyelven írt mintát.
services: storage
author: tamram
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.component: queues
ms.openlocfilehash: 7074ae21c1816b5266cbcf69d0126751ded19439
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522604"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>How to use Queue storage from Ruby (A Queue Storage használata Rubyval)
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre a Microsoft Azure Queue Storage szolgáltatást használó általános forgatókönyvhöz. A minták írták, a Ruby Azure API-val.
Az ismertetett forgatókönyvek között megtalálható **beszúrása**, **Bepillantás**, **első**, és **törlése** várólista-üzenetek, valamint  **létrehozása és törlése a várólisták**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Ruby alkalmazás létrehozása
Ruby alkalmazás létrehozása Útmutatásért lásd: [Ruby alkalmazás létrehozása Linuxon futó App Service-ben](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby).

## <a name="configure-your-application-to-access-storage"></a>Tárolás-alkalmazás konfigurálása
Az Azure storage használatához szüksége letöltését és a Ruby azure csomagot, amely tartalmazza a kényelem kódtárak, amely kommunikálni a tárolási REST szolgáltatások használatát.

### <a name="use-rubygems-to-obtain-the-package"></a>A csomag beszerzése a RubyGems használatával
1. Használjon egy parancssori felületet, mint például a **PowerShell** (Windows), a **Terminal** (Mac), vagy a **Bash** (Unix).
2. Írja be a "gem-telepítés azure" a parancssori ablakban, a gemet és a függőségek telepítéséhez.

### <a name="import-the-package"></a>A csomag importálása
Használja kedvenc szövegszerkesztőjét, ahol szeretne használni a storage, a Ruby fájl elejéhez adja hozzá a következőket:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Az Azure Storage-kapcsolat beállítása
Az azure-modul a környezeti változókat olvassa **AZURE\_tárolási\_fiók** és **AZURE\_tárolási\_ACCESS_KEY** információ az Azure storage-fiókjába való kapcsolódáshoz szükséges. Ha nincsenek beállítva ezek a környezeti változók, meg kell adnia a fiók adatait, mielőtt **Azure::QueueService** a következő kóddal:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Ezeket az értékeket a következőképp kérheti le egy klasszikus vagy Resource Manager-tárfiókból az Azure Portalon:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Keresse meg a használni kívánt tárfiókot.
3. A jobb oldali Beállítások panelen kattintson az **Hozzáférési kulcsok** lehetőségre.
4. A megjelenő Hozzáférési kulcsok panelen láthatja az 1. és a 2. hozzáférési kulcsot. Ezek bármelyikét használhatja. 
5. Kattintson a másolás gombra, hogy a kulcsot a vágólapra másolja. 

## <a name="how-to-create-a-queue"></a>Útmutató: A várólista létrehozása
Az alábbi kód létrehoz egy **Azure::QueueService** objektum, amely lehetővé teszi, hogy az üzenetsorok.

```ruby
azure_queue_service = Azure::QueueService.new
```

Használja a **create_queue()** metódussal hoz létre egy üzenetsort a megadott néven.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Útmutató: Üzenet beszúrása egy üzenetsorba
Üzenet beszúrása egy üzenetsorba, használja a **create_message()** metódust hozzon létre egy új üzenetet, és adja hozzá az üzenetsorhoz.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Útmutató: A következő üzenet betekintés
Anélkül, hogy eltávolítaná az üzenetsorból meghívásával is bepillanthat egy üzenetsorban található üzenet a **betekintési\_messages()** metódust. Alapértelmezés szerint **betekintési\_messages()** lekéri egy adott üzenet. Belepillantás kívánt üzenetek számának is megadhatja.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Útmutató: A következő üzenet eltávolítása a sorból
Eltávolíthatja az üzenetet egy üzenetsorból két lépésben.

1. Meghívásakor **lista\_messages()**, a következő üzenetet kap egy üzenetsorban lévő alapértelmezés szerint. Azt is megadhatja, hogy hány üzenetet szeretne kapni. A kapott üzeneteket **lista\_messages()** válik az adott üzenetsorban üzeneteket olvasó többi kód számára. A láthatósági időkorlátot másodpercben paraméterként adja át.
2. Befejeződik, az üzenet eltávolítása az üzenetsorból, meg kell is meghívhat **delete_message()**.

Az üzenet eltávolítása a két lépésből álló folyamat biztosítja, hogy a kód nem tudja feldolgozni egy üzenetet, mert hardveres vagy szoftveres hiba, amikor a kód egy másik példánya is megkaphassa ugyanazt az üzenetet, és próbálkozzon újra. A kód meghívja **törlése\_message()** jobb gombbal az üzenet feldolgozása után.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Útmutató: Az üzenetsorban található üzenet tartalmának módosítása
Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Az alábbi kódot a **update_message()** metódus üzenet frissítéséhez. A metódus adja vissza egy rekord, amely tartalmazza az üzenetsorban található üzenet és a egy UTC dátum idő értéke jelöli, az üzenet az üzenetsorban látható lesz a pop fogadását.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Útmutató: További beállítások üzenetmozgatót üzenetek
Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból.

1. A kötegelt üzenet kérheti le.
2. Beállíthat egy hosszabb vagy rövidebb láthatatlansági időkorlátot hosszabb vagy rövidebb idő teljesen feldolgozni az egyes üzenet, amely lehetővé teszi, hogy a kód.

Az alábbi példakód a **lista\_messages()** metódus lekéréséhez 15 üzeneteket egyetlen hívásával. Ezután jelenít meg, és törli az egyes üzeneteket. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Útmutató: Az üzenetsor hosszának lekérése
A várólistában lévő üzenetek számának becslése kérheti le. A **első\_várólista\_metadata()** módszert kéri a queue szolgáltatásból az üzenetek hozzávetőleges száma és a várólista metaadatait adja vissza.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Útmutató: Üzenetsor törlése
Egy üzenetsor és a benne tárolt összes üzenet törléséhez hívja a **törlése\_queue()** metódust a várólista-objektum.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a queue storage alapjait, az alábbi hivatkozásokból tájékozódhat az összetettebb tárolási feladatok.

* Látogasson el a [Azure Storage csapat blogja](http://blogs.msdn.com/b/windowsazurestorage/)
* Látogasson el a [Rubyhoz készült Azure SDK-t](https://github.com/WindowsAzure/azure-sdk-for-ruby) tárházban a Githubon

Az ebben a cikkben tárgyalt Azure Queue szolgáltatás és az Azure Service Bus-üzenetsorok tárgyalt összehasonlítását a [használata a Service Bus-üzenetsorok](/develop/ruby/how-to-guides/service-bus-queues/) cikkre, lásd: [Azure-üzenetsorok és Service Bus-üzenetsorok - képest és Összehasonlítása](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)