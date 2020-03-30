---
title: A Ruby várólistás tárhelyének használata - Azure Storage
description: Ismerje meg, hogyan használhatja az Azure Queue szolgáltatást várólisták létrehozására és törlésére, valamint üzenetek beszúrására, leésére és törlésére. Ruby-val írt minták.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: c7211bc805f4ed1d026faedbfdc9d53d3c1dfd93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68721294"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>How to use Queue storage from Ruby (A Queue Storage használata Rubyval)
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthatja végre a gyakori forgatókönyveket a Microsoft Azure Queue Storage szolgáltatás használatával. A minták a Ruby Azure API használatával vannak írásban.
A tárgyalt forgatókönyvek közé tartozik a várólista-üzenetek **beszúrása,** **bepillantása,** **bepillantása**és **törlése,** valamint **várólisták létrehozása és törlése.**

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Ruby alkalmazás létrehozása
Ruby alkalmazás létrehozása. További információt a [Ruby App alkalmazás létrehozása az App Service linuxos szolgáltatásában című témakörben talál.](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby)

## <a name="configure-your-application-to-access-storage"></a>Az alkalmazás konfigurálása az Access Storage szolgáltatásba
Az Azure storage használatához le kell töltenie és használnia kell a Ruby azure csomagot, amely a REST-szolgáltatásokkal kommunikáló kényelmi kódtárakkészletét tartalmazza.

### <a name="use-rubygems-to-obtain-the-package"></a>A csomag beszerzése a RubyGems használatával
1. Használjon egy parancssori felületet, mint például a **PowerShell** (Windows), a **Terminal** (Mac), vagy a **Bash** (Unix).
2. Írja be a "gem install azure" a parancs ablakban telepíteni a gem és a függőségek.

### <a name="import-the-package"></a>A csomag importálása
Használja kedvenc szövegszerkesztőjét, és adja hozzá a következőket a Ruby-fájl tetejéhez, ahol tárhelyet kíván használni:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Azure storage-kapcsolat beállítása
Az azure modul az **Azure\_STORAGE-FIÓK\_** és az AZURE **\_STORAGE\_ACCESS_KEY** környezeti változókat olvassa be az Azure storage-fiókjához való csatlakozáshoz szükséges információkért. Ha ezek a környezeti változók nincsenek beállítva, az **Azure::QueueService** használata előtt meg kell adnia a fiókadatokat a következő kóddal:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Ezeket az értékeket a következőképp kérheti le egy klasszikus vagy Resource Manager-tárfiókból az Azure Portalon:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Keresse meg a használni kívánt tárfiókot.
3. A jobb oldali Beállítások panelen kattintson az **Hozzáférési kulcsok** lehetőségre.
4. A megjelenő Hozzáférési kulcsok panelen láthatja az 1. és a 2. hozzáférési kulcsot. Ezek bármelyikét használhatja. 
5. Kattintson a másolás gombra, hogy a kulcsot a vágólapra másolja. 

## <a name="how-to-create-a-queue"></a>Útmutató: Várólista létrehozása
A következő kód létrehoz egy **Azure::QueueService** objektumot, amely lehetővé teszi a várólisták kal való munkát.

```ruby
azure_queue_service = Azure::QueueService.new
```

A **create_queue()** metódussal hozzon létre egy várólistát a megadott névvel.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Útmutató: Üzenet beszúrása várólistába
Ha üzenetet szeretne beszúrni egy várólistába, a **create_message()** módszerrel hozzon létre egy új üzenetet, és adja hozzá a várólistához.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Hogyan: Betekintés a következő üzenetbe
A **betekintő\_üzenetek()** metódus hívásával a várólista elejére is betekinthet a várólista elejére. Alapértelmezés szerint a **betekintő\_üzenetek()** egyetlen üzenetbe tekintbe. Azt is megadhatja, hogy hány üzenetet szeretne betekinteni.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Útmutató: A következő üzenet várólistájának törlése
Az üzeneteket két lépésben távolíthatja el a várólistából.

1. A **listaüzenetek()\_** hívásakor alapértelmezés szerint a következő üzenet jelenik meg a várólistában. Azt is megadhatja, hogy hány üzenetet szeretne kapni. A **listaüzenetek()\_** üzenetekből visszaadott üzenetek láthatatlanná válnak a várólistából érkező üzeneteket olvasó bármely más kód számára. A láthatósági időoutot paraméterként másodpercben adja meg.
2. Az üzenet várólistából való eltávolításának befejezéséhez meg kell hívnia **a delete_message()**.

Az üzenet eltávolításának kétlépéses folyamata biztosítja, hogy ha a kód hardver- vagy szoftverhiba miatt nem tudja feldolgozni az üzenetet, a kód egy másik példánya megkapja ugyanazt az üzenetet, és újra próbálkozhat. A kód az üzenet **feldolgozása\_** után azonnal törli az üzenetet() .

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Útmutató: A várólistán lévő üzenetek tartalmának módosítása
Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Az alábbi kód a **update_message()** metódust használja az üzenet frissítéséhez. A metódus egy olyan törzset ad vissza, amely tartalmazza a várólistaüzenet popfogadását és egy UTC dátumidő-értéket, amely azt jelzi, hogy az üzenet mikor lesz látható a várólistán.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Útmutató: Az üzenetek dekonkulálásának további lehetőségei
Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból.

1. Kaphat egy csomó üzenetet.
2. Hosszabb vagy rövidebb láthatatlansági időhosszabbítást is beállíthat, így a kódnak többé-kevésbé időre van szüksége az egyes üzenetek teljes feldolgozásához.

A következő kódpélda a **lista\_üzenetek()** metódust használja 15 üzenet egy hívásban való lehívásához. Ezután kinyomtatja és törli az egyes üzeneteket. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Útmutató: A várólista hosszának beszerezni
A várólistában lévő üzenetek számának becslését kaphatja. A **\_várólista\_metadata()** metódusa arra kéri a várólista-szolgáltatást, hogy adja vissza a várólista hozzávetőleges üzenetszámát és metaadatait.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Útmutató: Várólista törlése
Egy várólista és a benne lévő összes üzenet törléséhez hívja meg a **várólista-objektum\_törlési várólista()** metódusát.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Következő lépések
Most, hogy megtanulta a várólista-tárolás alapjait, kövesse ezeket a hivatkozásokat az összetettebb tárolási feladatok megismeréséhez.

* Látogasson el az [Azure Storage Team blogjára](https://blogs.msdn.com/b/windowsazurestorage/)
* Látogasson el az [Azure SDK a Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) tárház a GitHubon

A cikkben tárgyalt Azure Queue Service és az Azure Service Bus-várólisták a [Service Bus-várólisták használata](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/) című cikkben tárgyalt Azure-várólisták összehasonlítását az [Azure-várólisták és a Szolgáltatásbusz-várólisták](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md) – Összehasonlítás és kontrasztos
