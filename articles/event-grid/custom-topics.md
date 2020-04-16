---
title: Egyéni témakörök az Azure Event Gridben
description: Az Azure Event Grid egyéni témaköreit ismerteti.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 166a48d74d32c8b0a3a59310e693ea96ada29116
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394357"
---
# <a name="custom-topics-in-azure-event-grid"></a>Egyéni témakörök az Azure Event Gridben
Az eseményrács témaköre egy végpontot biztosít, ahol a forrás eseményeket küld. A közzétevő létrehozza az eseményrács témakörét, és eldönti, hogy egy eseményforrásnak szüksége van-e egy vagy több témakörre. A témakör kapcsolódó események gyűjteményéhez használatos. Bizonyos típusú eseményekre való reagáláshoz az előfizetők eldöntik, hogy mely témákra iratkozzanak fel.

**Az egyéni témakörök** alkalmazás- és harmadik féltől származó témakörök. Amikor létrehoz vagy hozzáférést kap egy egyéni témakörhöz, akkor az megjelenik a saját előfizetésében. 

Az alkalmazás tervezésekor rugalmasan dönti el, hogy hány témakört szeretne létrehozni. Nagy megoldások esetén hozzon létre **egyéni témakört** **a kapcsolódó események minden kategóriájához.** Tekintsünk például egy olyan alkalmazást, amelyik a felhasználói fiókok módosításához és a megrendelések feldolgozásához kapcsolódóan küld eseményeket. Nem valószínű, hogy valamely eseménykezelőnek mind a két esemény kategória kellene. Hozzon létre két külön témakört, és az eseménykezelők hadd iratkozzanak fel arra, amelyik érdekli őket. Kis megoldások esetén előfordulhat, hogy az összes eseményt egyetlen témakörbe szeretné küldeni. Az esemény-előfizetők szűrhetik a kívánt eseménytípusokat.

## <a name="event-schema"></a>Eseményséma
Az eseményséma részletes áttekintését az [Azure Event Grid eseménysémájában](event-schema.md)találja. Egyéni témakörök esetén az esemény **data** közzétevője határozza meg az adatobjektumot. A legfelső szintű adatoknak ugyanazoknak a mezőknek kell rendelkezniük, mint a szabványos erőforrás-definiált eseményeknek.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

Az alábbi szakaszok az Azure Portal, a CLI, a PowerShell és az Azure Resource Manager (ARM) sablonjainak használatával egyéni témakörök létrehozására mutató témakörökre mutató hivatkozásokat tartalmaznak. 


## <a name="azure-portal-tutorials"></a>Az Azure Portal oktatóanyagai
|Cím  |Leírás  |
|---------|---------|
| [Rövid útmutató: egyéni események létrehozása és irányítása az Azure Portalon](custom-event-quickstart-portal.md) | Bemutatja, hogyan lehet a portál használatával egyéni eseményeket küldeni. |
| [Rövid útmutató: egyéni események irányítása az Azure Queue storage-ba](custom-event-to-queue-storage.md) | Ez a témakör azt ismerteti, hogy miként küldhet egyéni eseményeket várólista-tárolóba. |
| [Hogyan: tegye az egyéni téma](post-to-custom-topic.md) | Bemutatja, hogyan lehet eseményt közzétenni egy egyéni témakörben. |


## <a name="azure-cli-tutorials"></a>Az Azure CLI oktatóanyagai
|Cím  |Leírás  |
|---------|---------|
| [Rövid útmutató: egyéni események létrehozása és irányítása az Azure CLI-vel](custom-event-quickstart.md) | Bemutatja, hogyan használhatja az Azure CLI-t egyéni események küldésére. |
| [Azure CLI: egyéni témakör létrehozása az Event Grid ben](./scripts/event-grid-cli-create-custom-topic.md)|Mintaparancsfájl, amely egyéni témakört hoz létre. A parancsfájl beolvassa a végpontot és egy kulcsot.|
| [Azure CLI: előfizetni eseményekre egy egyéni témakörhöz](./scripts/event-grid-cli-subscribe-custom-topic.md)|Mintaparancsfájl, amely előfizetést hoz létre egy egyéni témakörhöz. Eseményeket küld egy WebHook.It sends events to a WebHook.|

## <a name="azure-powershell-tutorials"></a>Azure PowerShell-oktatóanyagok
|Cím  |Leírás  |
|---------|---------|
| [Rövid útmutató: egyéni események létrehozása és irányítása az Azure PowerShell használatával](custom-event-quickstart-powershell.md) | Bemutatja, hogyan használhatja az Azure PowerShellt egyéni események küldésére. |
| [PowerShell: event grid egyéni témakör létrehozása](./scripts/event-grid-powershell-create-custom-topic.md)|Mintaparancsfájl, amely egyéni témakört hoz létre. A parancsfájl beolvassa a végpontot és egy kulcsot.|
| [PowerShell: feliratkozás egyéni témakör eseményeire](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Mintaparancsfájl, amely előfizetést hoz létre egy egyéni témakörhöz. Eseményeket küld egy WebHook.It sends events to a WebHook.|

## <a name="arm-template-tutorials"></a>ARM sablon oktatóanyagok
|Cím  |Leírás  |
|---------|---------|
| [Resource Manager-sablon: egyéni témakör és WebHook-végpont](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Erőforrás-kezelő sablon, amely egyéni témakört és előfizetést hoz létre az adott egyéni témakörhöz. Eseményeket küld egy WebHook.It sends events to a WebHook. |
| [Erőforrás-kezelő sablon: egyéni témakör és Eseményközpontok végpontja](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Erőforrás-kezelő sablon, amely előfizetést hoz létre egy egyéni témakörhöz. Eseményeket küld egy Azure Event Hubs. |

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket: 

- [Rendszerrel kapcsolatos témakörök](system-topics.md)
- [Tartományok](event-domains.md)