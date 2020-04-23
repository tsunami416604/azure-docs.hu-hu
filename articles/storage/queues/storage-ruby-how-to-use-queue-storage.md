---
title: A üzenetsor-tároló használata a Ruby-Azure Storage-ból
description: Megtudhatja, hogyan hozhat létre és törölhet várólistákat az Azure Queue szolgáltatás használatával, valamint hogyan szúrhat be, kérhet le és törölhet üzeneteket. A Rubyban írt minták.
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
Ez az útmutató bemutatja, hogyan hajthat végre gyakori forgatókönyveket a Microsoft Azure Queue Storage szolgáltatás használatával. A minták a Ruby Azure API használatával íródnak.
A tárgyalt forgatókönyvek közé tartozik például a várólista-üzenetek **beszúrása**, **bepillantása**, **beolvasása**és **törlése** , valamint a **várólisták létrehozása és törlése**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Ruby-alkalmazás létrehozása
Ruby-alkalmazás létrehozása. Útmutatásért lásd: [Ruby-alkalmazás létrehozása linuxon app Service](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby).

## <a name="configure-your-application-to-access-storage"></a>Az alkalmazás konfigurálása a tároló elérésére
Az Azure Storage használatához le kell töltenie és használnia kell a Ruby Azure-csomagot, amely a tárolási REST-szolgáltatásokkal kommunikáló kényelmi könyvtárakat tartalmaz.

### <a name="use-rubygems-to-obtain-the-package"></a>A csomag beszerzése a RubyGems használatával
1. Használjon egy parancssori felületet, mint például a **PowerShell** (Windows), a **Terminal** (Mac), vagy a **Bash** (Unix).
2. A gem és a függőségek telepítéséhez írja be a "gem install Azure" parancsot a parancsablakban.

### <a name="import-the-package"></a>A csomag importálása
Használja kedvenc szövegszerkesztőjét, adja hozzá a következőt a Ruby-fájl elejéhez, ahol a Storage-t szeretné használni:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Azure Storage-beli kapcsolatok beállítása
Az Azure-modul beolvassa az **Azure\_Storage\_-fiók** és az **\_Azure Storage\_ACCESS_KEY** környezeti változóit az Azure Storage-fiókhoz való kapcsolódáshoz szükséges információkhoz. Ha ezek a környezeti változók nincsenek beállítva, meg kell adnia a fiók adatait az **Azure:: QueueService** és az alábbi kóddal:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Ezeket az értékeket a következőképp kérheti le egy klasszikus vagy Resource Manager-tárfiókból az Azure Portalon:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Navigáljon a használni kívánt Storage-fiókhoz.
3. A jobb oldali Beállítások panelen kattintson az **Hozzáférési kulcsok** lehetőségre.
4. A megjelenő Hozzáférési kulcsok panelen láthatja az 1. és a 2. hozzáférési kulcsot. Ezek bármelyikét használhatja. 
5. Kattintson a másolás gombra, hogy a kulcsot a vágólapra másolja. 

## <a name="how-to-create-a-queue"></a>Útmutató: üzenetsor létrehozása
A következő kód létrehoz egy **Azure:: QueueService** objektumot, amely lehetővé teszi a várólistákkal való munkavégzést.

```ruby
azure_queue_service = Azure::QueueService.new
```

A **create_queue ()** metódus használatával hozzon létre egy várólistát a megadott névvel.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Útmutató: üzenet beszúrása egy várólistába
Egy üzenet üzenetsorbe való beszúrásához használja a **create_message ()** metódust egy új üzenet létrehozásához és a várólistához való hozzáadásához.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Útmutató: betekintés a következő üzenetbe
A várólista elején lévő üzenetbe való betekintés nélkül is betekintést nyerhet a várólistába a **betekintési\_üzenetek ()** metódus meghívásával. Alapértelmezés szerint a **betekintés\_üzenetei ()** egyetlen üzenetben jelennek meg. Megadhatja azt is, hogy hány üzenetet szeretne megtekinteni.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Útmutató: a következő üzenet elküldése
A várólistákból két lépésben törölhet üzenetet.

1. Ha a híváslista **(\_)** üzenetet hívja, alapértelmezés szerint a következő üzenet jelenik meg egy várólistában. Megadhatja azt is, hogy hány üzenetet szeretne kapni. A **listaelemek\_() üzenetből** visszaadott üzenetek láthatatlanná válnak a várólistáról érkező más kódoknál. A láthatósági időtúllépést másodpercben adja meg paraméterként.
2. Az üzenet várólistából való eltávolításának befejezéséhez a **delete_message ()** hívását is meg kell hívni.

Az üzenetek eltávolításának kétlépéses folyamata biztosítja, hogy ha a kód a hardver vagy a szoftver meghibásodása miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya ugyanazt az üzenetet kapja, és próbálkozzon újra. A kód a **törlési\_üzenetet ()** közvetlenül az üzenet feldolgozását követően hívja meg.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Útmutató: várólistán lévő üzenet tartalmának módosítása
Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Az alábbi kód a **update_message ()** metódust használja egy üzenet frissítéséhez. A metódus egy olyan rekordot ad vissza, amely tartalmazza a várólista-üzenet pop-fogadását, valamint egy UTC-dátum időértékét, amely azt jelzi, hogy az üzenet megjelenjen-e a várólistán.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Útmutató: a dequeuing üzenetek további beállításai
Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból.

1. Az üzenetek egy kötegét is lekérheti.
2. Megadhat egy hosszabb vagy rövidebb láthatósági időkorlátot, így a kód több vagy kevesebb időt vehet igénybe az egyes üzenetek teljes feldolgozásához.

A következő kódrészlet a **List\_messages ()** metódus használatával 15 üzenetet kap egy hívásban. Ezután kinyomtatja és törli az egyes üzeneteket. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Útmutató: a várólista hosszának beolvasása
A várólistában lévő üzenetek számának becslését is elérheti. A **várólista\_-\_metaadatok beolvasása ()** metódus arra kéri a várólista-szolgáltatást, hogy visszaállítsa az üzenetsor hozzávetőleges üzeneteit és a metaadatokat.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Útmutató: üzenetsor törlése
Ha törölni szeretne egy várólistát és a benne található összes üzenetet, hívja meg a **delete\_üzenetsor ()** metódust a várólista-objektumon.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte a várólista-tárolás alapjait, az alábbi hivatkozásokat követve megismerheti az összetettebb tárolási feladatokat.

* Tekintse meg az [Azure Storage csapat blogját](https://blogs.msdn.com/b/windowsazurestorage/)
* Látogasson el az [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) adattárára a githubon

Az ebben a cikkben tárgyalt Azure Queue szolgáltatás és a [Service Bus-várólisták használatának módjával](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/) kapcsolatos Azure Service Bus várólisták összehasonlítását lásd: [Azure queues and Service Bus Queues – összehasonlítás és kontrasztos](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
