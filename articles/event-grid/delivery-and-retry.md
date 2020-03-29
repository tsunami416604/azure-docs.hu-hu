---
title: Az Azure Event Grid kézbesítése és újrapróbálkozása
description: Ez a témakör azt ismerteti, hogy az Azure Event Grid hogyan biztosítja az eseményeket, és hogyan kezeli a kézbesítetlen üzeneteket.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: spelluru
ms.openlocfilehash: dda2fd98c4c0d330059156a5ec00baa97ffaf627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921062"
---
# <a name="event-grid-message-delivery-and-retry"></a>Eseményrácsüzenet kézbesítése és újrapróbálkozása

Ez a cikk azt ismerteti, hogy az Azure Event Grid hogyan kezeli az eseményeket, ha a kézbesítés nem nyugtázott.

Az Event Grid tartós kézbesítést biztosít. Minden egyes üzenetet legalább egyszer kézbesít minden egyes előfizetéshez. Az események azonnal elküldésre kerülnek az egyes előfizetések regisztrált végpontjára. Ha egy végpont nem nyugtázza egy esemény fogadását, az Event Grid újrapróbálkozik az esemény kézbesítésével.

## <a name="batched-event-delivery"></a>Kötegelt esemény kézbesítése

Az Event Grid alapértelmezés szerint minden eseményt külön küld az előfizetőknek. Az előfizető egyetlen eseményt kap egy tömböt. Az Event Grid konfigurálható kötegelt eseményekre a nagyobb átakátviteli forgatókönyvek ben a HTTP-teljesítmény javítása érdekében.

A kötegelt kézbesítés nek két beállítása van:

* **Események maximális száma kötegenként** – Az események maximális száma, amelyet az Event Grid kötegenként kézbesít. Ez a szám soha nem lépi túl, azonban kevesebb esemény érhető el, ha a közzététel időpontjában más esemény nem érhető el. Az Event Grid nem késlelteti az eseményeket, hogy köteget hozzon létre, ha kevesebb esemény érhető el. 1 és 5000 között lehet.
* **Előnyben részesített kötegméret kilobájtban** – A kötegméret célplafonja kilobájtban. A maximális eseményekhez hasonlóan a kötegmérete kisebb is lehet, ha a közzététel időpontjában nem érhető el több esemény. Lehetséges, hogy egy köteg nagyobb, mint az előnyben részesített kötegméretet, *ha* egyetlen esemény nagyobb, mint az előnyben részesített méret. Ha például az előnyben részesített méret 4 KB, és egy 10 KB-os esemény kerül az Event Gridre, a 10 KB-os esemény továbbra is a saját kötegében lesz kézbesítve, nem pedig eldobás.

A csomagban történő kézbesítés eseményalapú előfizetési alapon konfigurálva a portálon, a CLI-n, a PowerShellen vagy az SDK-kon keresztül.

### <a name="azure-portal"></a>Azure-portál: 
![Kötegelt kézbesítési beállítások](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure CLI
Esemény-előfizetés létrehozásakor használja a következő paramétereket: 

- **maximális események kötegenként** – egy kötegben lévő események maximális száma. 1 és 5000 közötti számnak kell lennie.
- **preferált kötegméretű kilobájtban** – Preferált kötegméret kilobájtban. 1 és 1024 közötti számnak kell lennie.

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

Az Azure CLI eseményhálózattal való használatáról további információt a [Tárolási események irányítása a webvégpontba az Azure CLI segítségével](../storage/blobs/storage-blob-event-quickstart.md)című témakörben talál.

## <a name="retry-schedule-and-duration"></a>Ütemezés és időtartam újrapróbálkozása

Az Event Grid 30 másodpercet vár a válaszra az üzenet kézbesítése után. 30 másodperc elteltével, ha a végpont nem válaszolt, az üzenet várólistára kerül az újrapróbálkozásra. Event Grid egy exponenciális visszautasítás-újrapróbálkozási házirendet használ az esemény kézbesítéséhez. Az Event Grid a következő ütemezés szerint próbálja meg a kézbesítést a legjobb erőfeszítés alapján:

- 10 másodperc
- 30 másodperc
- 1 perc
- 5 perc
- 10 perc
- 30 perc
- 1 óra
- Óránként akár 24 órán keresztül

Ha a végpont 3 percen belül válaszol, az Event Grid megkísérli eltávolítani az eseményt az újrapróbálkozási várólistából a legjobb erőfeszítés alapján, de az ismétlődések továbbra is érkezhetnek.

Event Grid egy kis véletlenszerűséget ad hozzá az összes újrapróbálkozási lépéshez, és opportunista módon kihagyhat bizonyos újrapróbálkozásokat, ha egy végpont folyamatosan nem kifogástalan, hosszú ideig levan csökkentve, vagy túlterheltnek tűnik.

Determinisztikus viselkedés, állítsa be az esemény ideje, hogy az élő és a maximális kézbesítési kísérletek az [előfizetés újrapróbálkozási házirendek](manage-event-delivery.md).

Alapértelmezés szerint az Event Grid lejár minden olyan esemény, amely nem kézbesíti 24 órán belül. Az [újrapróbálkozási szabályzatot](manage-event-delivery.md) esemény-előfizetés létrehozásakor testreszabhatja. Adja meg a kézbesítési kísérletek maximális számát (alapértelmezett érték 30) és az esemény élő adását (alapértelmezett érték 1440 perc).

## <a name="delayed-delivery"></a>Késleltetett szállítás

Végpontként kézbesítési hibák, Event Grid megkezdi az események kézbesítésének és újrapróbálkozásának késleltetése a végpontra. Ha például a végponton közzétett első 10 esemény sikertelen, az Event Grid azt feltételezi, hogy a végpont problémákat tapasztal, és egy ideig késlelteti az összes későbbi újrapróbálkozást *és új* kézbesítést – egyes esetekben akár több óráig is.

A késleltetett kézbesítés funkcionális célja a nem megfelelő állapotú végpontok, valamint az Event Grid rendszer védelme. A nem megfelelő állapotú végpontokra történő kézbesítés és a kézbesítés késleltetése nélkül az Event Grid újrapróbálkozási házirendje és kötetképességei könnyen elboríthatják a rendszert.

## <a name="dead-letter-events"></a>Kézbesítési kézbesítési események

Ha az Event Grid nem tud kézbesíteni egy eseményt, elküldheti a nem kézbesített eseményt egy tárfiókba. Ezt a folyamatot kézbesítési levélnek nevezzük. Alapértelmezés szerint az Event Grid nem kapcsolja be a kézbesítési betűket. Az engedélyezéshez meg kell adnia egy tárfiókot a nem kézbesített események tárolásához az esemény-előfizetés létrehozásakor. Eseményeket kell leállítania ebből a tárfiókból a szállítások feloldásához.

Az Event Grid egy eseményt küld a kézbesítési levél helyére, amikor az összes újrapróbálkozási kísérletet megpróbálta. Ha az Event Grid 400 (hibás kérelem) vagy 413 (Request Entity Too Large) válaszkódot kap, azonnal elküldi az eseményt a kézbesítési levél végpontjára. Ezek a válaszkódok azt jelzik, hogy az esemény kézbesítése soha nem fog sikerülni.

Öt perces késleltetés van az esemény kézbesítésére tett utolsó kísérlet és a kézbesítés között a kézbesített levél helyére. Ez a késleltetés célja, hogy csökkentse a Blob tárolási műveletek számát. Ha a kézbesítési levél helye négy órán keresztül nem érhető el, az esemény megszakad.

A kézbesítési levél helyének beállítása előtt rendelkeznie kell egy tárolóval rendelkező tárfiókkal. Adja meg a végpontot ehhez a tárolóhoz az esemény-előfizetés létrehozásakor. A végpont formátuma:`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Előfordulhat, hogy értesítést szeretne kapni, ha egy eseményt a kézbesítési levél helyére küldtek. Ha az Event Grid használatával válaszol a kézbesítetlen eseményekre, [hozzon létre egy esemény-előfizetést](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) a kézbesítetlen levél blob storage. Minden alkalommal, amikor a kézbesítetlen levél blob storage kap egy kézbesítetlen esemény, Event Grid értesíti a kezelőt. A kezelő a nem kézbesített események egyeztetéséhez szükséges műveletekkel válaszol.

A kézbesítési levél helyének beállítására a [Kézbesítési levél és az újrapróbálkozási házirendek](manage-event-delivery.md)című témakörben talál példát.

## <a name="message-delivery-status"></a>Az üzenetek kézbesítésének állapota

Az Event Grid HTTP válaszkódokat használ az események bevételezésének igazolására. 

### <a name="success-codes"></a>Sikeres kódok

Az Event Grid **csak** a következő HTTP-válaszkódokat tekinti sikeres szállításnak. Minden más állapotkód sikertelen kézbesítésnek minősül, és szükség szerint újra próbálkozik vagy halott levelet kap. Sikeres állapotkód beérkezéseután az Event Grid teljesnek tekinti a kézbesítést.

- 200 OK
- 201 Létrehozva
- 202 Elfogadva
- 203 Nem mérvadó információk
- 204 Nincs tartalom

### <a name="failure-codes"></a>Hibakódok

A fenti készletben nem található összes többi kód (200-204) sikertelennek minősül, és újra próbálkozik. Néhány konkrét újrapróbálkozási házirendek kötődnek hozzájuk az alábbiakban ismertetett, az összes többi követi a szabványos exponenciális back-off modell. Fontos szem előtt tartani, hogy az Event Grid architektúrájának erősen párhuzamos jellege miatt az újrapróbálkozási viselkedés nem determinisztikus. 

| Állapotkód | Újrapróbálkozási viselkedés |
| ------------|----------------|
| 400 rossz kérés | Próbálkozzon újra 5 perc vagy több után (deadletter azonnal, ha holtlevél beállítás) |
| 401 Jogosulatlan | Próbálkozzon újra 5 perc vagy több után |
| 403 – Tiltott | Próbálkozzon újra 5 perc vagy több után |
| 404 Nem található | Próbálkozzon újra 5 perc vagy több után |
| 408 Kérés időtúllépése | Próbálkozzon újra 2 perc vagy több után |
| 413 Kérelem entitás túl nagy | Próbálkozzon újra 10 másodperc vagy több után (kézbesítési levél azonnal, ha a kézbesítési levél beállítása) |
| 503 A szolgáltatás nem érhető el | Próbálkozzon újra 30 másodperc vagy több után |
| Az összes többi | Próbálkozzon újra 10 másodperc vagy több után |


## <a name="next-steps"></a>További lépések

* Az eseménykézbesítések állapotának megtekintéséhez olvassa el az [Eseményrács üzenetkézbesítésének figyelése](monitor-event-delivery.md)című témakört.
* Az eseménykézbesítési beállítások testreszabásához olvassa el a [Kézbesítésben elévülve és az újrapróbálkozási házirendekkel című témakört.](manage-event-delivery.md)
