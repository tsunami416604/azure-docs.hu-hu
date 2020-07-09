---
title: Azure Event Grid kézbesítés és újrapróbálkozás
description: Leírja, hogy Azure Event Grid hogyan kézbesíti az eseményeket, és hogyan kezeli a kézbesítetlen üzeneteket.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e565bbc8592dc2818e3573672e6e3035c3c8983a
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86113836"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid üzenet kézbesítése és újrapróbálkozás

Ez a cikk azt ismerteti, hogyan kezeli a Azure Event Grid az eseményeket, amikor a kézbesítés nem ismerhető fel.

Event Grid tartós kézbesítést biztosít. Minden egyes előfizetéshez legalább egyszer kézbesít minden üzenetet. Az eseményeket azonnal elküldi az egyes előfizetések regisztrált végpontjának. Ha egy végpont nem igazolja egy esemény fogadását, Event Grid újrapróbálkozik az esemény kézbesítésével.

## <a name="batched-event-delivery"></a>Kötegelt esemény kézbesítése

Event Grid alapértelmezett értéke az egyes események küldése az előfizetőknek. Az előfizető egyetlen eseménnyel rendelkező tömböt kap. A nagy átviteli sebességű forgatókönyvek esetében beállíthatja, hogy a rendszer a kötegelt eseményekre Event Grid a HTTP-teljesítmény javítására.

A kötegelt kézbesítésnek két beállítása van:

* **Események másodpercenkénti** maximális száma – a rendszer a kötegben Event Grid által kézbesített események maximális számát adja meg. A rendszer soha nem lépi túl ezt a számot, azonban kevesebb esemény jelenhet meg, ha a közzétételkor nem áll rendelkezésre más esemény. Event Grid nem késlelteti az eseményeket, ha kevesebb esemény áll rendelkezésre. 1 és 5 000 közöttinek kell lennie.
* Az **előnyben részesített köteg mérete (kilobájtban** ) – a Batch méretének felső határa kilobájtban. A maximális eseményekhez hasonlóan a köteg mérete is kisebb lehet, ha a közzétételkor több esemény nem érhető el. Lehetséges, hogy egy köteg nagyobb, mint az előnyben részesített köteg mérete, *Ha* egyetlen esemény nagyobb az előnyben részesített méretnél. Ha például az előnyben részesített méret 4 KB, a 10 KB-os eseményt pedig Event Grid küldi el a rendszer, akkor a 10 KB-os esemény továbbra is a saját kötegében fog megjelenni az eldobása helyett.

A kötegelt kézbesítés a portálon, a CLI-n, a PowerShellen vagy az SDK-n keresztül, az esemény-előfizetések alapján konfigurálva van.

### <a name="azure-portal"></a>Azure Portal: 
![Batch-kézbesítési beállítások](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure CLI
Esemény-előfizetés létrehozásakor használja a következő paramétereket: 

- **Max-Events-per-batch** -a kötegben lévő események maximális száma. 1 és 5000 közötti számnak kell lennie.
- **preferált-batch-size-in-kilobájtban** – előnyben részesített köteg mérete (kilobájtban). 1 és 1024 közötti számnak kell lennie.

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

Az Azure CLI és a Event Grid használatával kapcsolatos további információkért lásd: [tárolási események továbbítása webes végponthoz az Azure CLI-vel](../storage/blobs/storage-blob-event-quickstart.md).

## <a name="retry-schedule-and-duration"></a>Újrapróbálkozási ütemterv és időtartam

Az üzenet kézbesítése után a Event Grid 30 másodpercet vár a válaszra. 30 másodperc elteltével, ha a végpont nem válaszolt, az üzenet várólistára kerül az újrapróbálkozáshoz. Event Grid exponenciális leállítási újrapróbálkozási házirendet használ az események kézbesítéséhez. Event Grid a következő ütemterv szerint újrapróbálkozik a kézbesítéssel az ajánlott eljárás alapján:

- 10 másodperc
- 30 másodperc
- 1 perc
- 5 perc
- 10 perc
- 30 perc
- 1 óra
- Óránként legfeljebb 24 óráig

Ha a végpont 3 percen belül válaszol, Event Grid megpróbálja eltávolítani az eseményt az újrapróbálkozási sorból a legjobb erőfeszítés alapján, de a duplikált elemek továbbra is fogadhatók.

Event Grid egy kis véletlenszerűség az összes újrapróbálkozási lépéshez, és bizonyos újrapróbálkozások kihagyása, ha egy végpont nem kifogástalan állapotú, hosszú ideig leáll, vagy úgy tűnik, hogy túlterhelt.

A determinisztikus működéséhez állítsa az esemény időpontját élő és maximális kézbesítési kísérletekre az [előfizetés-újrapróbálkozási házirendekben](manage-event-delivery.md).

Alapértelmezés szerint a Event Grid minden olyan eseményt lejár, amely 24 órán belül nem érkezik meg. [Az újrapróbálkozási szabályzatot az](manage-event-delivery.md) esemény-előfizetés létrehozásakor is testreszabhatja. Megadja a kézbesítési kísérletek maximális számát (az alapértelmezett érték 30) és az esemény élettartamát (az alapértelmezett érték 1440 perc).

## <a name="delayed-delivery"></a>Késleltetett kézbesítés

A végpontok kézbesítési meghibásodások esetén a Event Grid megkezdi a kézbesítést, és az események ismételt megismétlését az adott végpontra. Ha például egy végponton közzétett első 10 esemény meghiúsul, Event Grid feltételezi, hogy a végpont problémákba ütközik, és az összes további újrapróbálkozást *és új* kézbesítést késlelteti, néhány esetben akár több óráig is eltarthat.

A késleltetett kézbesítés funkcionális célja a nem megfelelő állapotú végpontok, valamint a Event Grid rendszer megóvása. A nem kifogástalan állapotú végpontokra történő kézbesítés nélkül, Event Grid az újrapróbálkozási szabályzat és a mennyiségi képességek könnyedén elérhetik a rendszereket.

## <a name="dead-letter-events"></a>Kézbesítetlen levelek eseményei

Ha Event Grid nem tud eseményt kézbesíteni, akkor a kézbesítetlen eseményt elküldheti egy Storage-fiókba. Ezt a folyamatot kézbesítetlen levélnek nevezzük. Alapértelmezés szerint a Event Grid nem kapcsolja be a kézbesítetlen betűket. Az engedélyezéshez meg kell adnia egy Storage-fiókot, amely az esemény-előfizetés létrehozásakor nem kézbesítési eseményeket tart fenn. A kézbesítések feloldásához le kell kérnie az eseményeket ebből a Storage-fiókból.

Event Grid küld egy eseményt a kézbesítetlen levelek helyére, amikor megpróbálta az összes újrapróbálkozási kísérletet. Ha a Event Grid 400 (hibás kérés) vagy 413 (kérelem entitás túl nagy) választ kap, az azonnal elküldi az eseményt a kézbesítetlen levelek végpontjának. Ezek a hibakódok jelzik, hogy az esemény kézbesítése soha nem fog sikerülni.

Az utolsó kísérlet az esemény kézbesítése és a kézbesítetlen levél helyére való továbbítása között öt perc késéssel jár. Ez a késleltetés a blob Storage-műveletek számának csökkentésére szolgál. Ha a kézbesítetlen levelek helye négy órán keresztül nem érhető el, a rendszer elveti az eseményt.

A kézbesítetlen levelek helyének beállítása előtt egy tárolóval rendelkező Storage-fiókkal kell rendelkeznie. Az esemény-előfizetés létrehozásakor adja meg a tároló végpontját. A végpont formátuma a (z):`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Előfordulhat, hogy értesítést szeretne kapni, ha egy eseményt elküldtek a kézbesítetlen levél helyére. Ha Event Gridt szeretne használni a nem kézbesített eseményekre való válaszadásra, [hozzon létre egy esemény-előfizetést](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) a kézbesítetlen levél blob Storage-hoz. Minden alkalommal, amikor a kézbesítetlen levelek blob-tárolója egy nem kézbesített eseményt kap, Event Grid értesíti a kezelőt. A kezelő olyan műveletekkel válaszol, amelyeket el kíván végezni a nem beérkező események egyeztetéséhez.

A kézbesítetlen levelek helyének beállításával kapcsolatos példát a [kézbesítetlen levelek és az újrapróbálkozási szabályzatok](manage-event-delivery.md)című témakörben talál.

## <a name="message-delivery-status"></a>Üzenet kézbesítési állapota

A Event Grid HTTP-válasz kódokat használ az események fogadásának visszaigazolására. 

### <a name="success-codes"></a>Sikerességi kódok

A Event Grid **csak** a következő http-válaszokat veszi figyelembe sikeres kézbesítésként. Az összes többi állapotkód sikertelen kézbesítésnek minősül, és szükség szerint újra próbálkozik vagy deadlettered. A sikeres állapotkód fogadása után Event Grid úgy véli, hogy a kézbesítés befejeződött.

- 200 OK
- 201 létrehozva
- 202 elfogadva
- 203 nem mérvadó információ
- 204 nincs tartalom

### <a name="failure-codes"></a>Hibakódok

Az összes többi, a fenti készletben nem szereplő kód (200-204) hibáknak minősül, és újra próbálkozik. Némelyikhez az alább vázolt konkrét újrapróbálkozási szabályzatok tartoznak, az összes többi pedig a standard exponenciális visszalépési modellt követi. Fontos szem előtt tartani, hogy a Event Grid architektúrájának nagy mértékben párhuzamos jellege miatt az újrapróbálkozási viselkedés nem determinisztikus. 

| Állapotkód | Újrapróbálkozási viselkedés |
| ------------|----------------|
| 400 hibás kérelem | Újrapróbálkozás 5 perc vagy több után (kézbesítetlen levelek azonnal, ha a kézbesítetlen levelek telepítője) |
| 401 jogosulatlan | Újrapróbálkozás 5 perc vagy több idő után |
| 403 – Tiltott | Újrapróbálkozás 5 perc vagy több idő után |
| 404 Nem található | Újrapróbálkozás 5 perc vagy több idő után |
| 408 Kérés időtúllépése | Próbálkozzon újra 2 perc múlva |
| 413 kérelem entitása túl nagy | Újrapróbálkozás 10 másodperc vagy több után (a kézbesítetlen levelek azonnal, ha a kézbesítetlen levelek telepítője) |
| 503 A szolgáltatás nem érhető el | Újrapróbálkozás 30 másodperc vagy több után |
| Minden más | Újrapróbálkozás 10 másodperc vagy több után |


## <a name="next-steps"></a>Következő lépések

* Az események kézbesítési állapotának megtekintéséhez lásd: [Event Grid üzenet kézbesítésének figyelése](monitor-event-delivery.md).
* Az esemény-kézbesítési beállítások testreszabásával kapcsolatban lásd: [kézbesítetlen levelek és újrapróbálkozási szabályzatok](manage-event-delivery.md).
