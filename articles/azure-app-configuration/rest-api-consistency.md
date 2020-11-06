---
title: Azure app Configuration REST API – konzisztencia
description: A valós idejű konzisztencia biztosítására szolgáló hivatkozási lapok az Azure-alkalmazás konfigurációjának használatával REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 4f11e6edcd4bc128f815db7e93b00b72bf990ea8
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424347"
---
# <a name="real-time-consistency"></a>Valós idejű konzisztencia

Egyes elosztott rendszerek jellegéből adódóan a kérelmek közötti valós idejű konzisztencia nem bizonyulhat implicit módon. A megoldás lehetővé teszi a protokollok támogatását több **szinkronizációs jogkivonat** formájában. A szinkronizálási tokenek nem kötelezőek.

## <a name="initial-request"></a>Kezdeti kérelem

A különböző ügyfél-példányok és kérelmek közötti valós idejű konzisztencia garantálása érdekében az opcionális `Sync-Token` kérés/válasz fejléceket kell használnia.

Szintaxis:

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|Paraméter|Leírás|
|--|--|
| `<id>` | Jogkivonat-azonosító (átlátszatlan) |
| `<value>` | Jogkivonat értéke (átlátszatlan). Base64 kódolású karakterlánc engedélyezése |
| `<sn>` | Jogkivonat-sorszám (verzió). Magasabb az azonos jogkivonat újabb verziója. Lehetővé teszi a jobb párhuzamosságot és az ügyfél-gyorsítótárazást. Az ügyfél dönthet úgy, hogy csak a token utolsó verzióját használja, mivel a jogkivonat-verziók tartalmazzák a szolgáltatást. A kérésekhez nem szükséges. |

## <a name="response"></a>Reagálás

A szolgáltatás `Sync-Token` minden egyes választ tartalmaz egy fejlécet.

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>További kérelmek

Minden további kérelem **valós idejű** konzisztens választ biztosít a megadott értékhez képest `Sync-Token` .

```http
Sync-Token: <id>=<value>
```

Ha a `Sync-Token` fejléc ki van hagyva a kérelemből, akkor lehetséges, hogy a szolgáltatás a gyorsítótárazott adatokkal válaszol a rövid idő alatt (akár néhány másodpercig), mielőtt a rendszer belsőleg rendezi. Ez a viselkedés inkonzisztens olvasást okozhat, ha a módosítások közvetlenül az olvasás előtt történtek.

## <a name="multiple-sync-tokens"></a>Több szinkronizálási token

A kiszolgáló több szinkronizálási jogkivonattal is reagálhat egyetlen kérelemre. A következő kérelem **valós idejű** konzisztenciájának megőrzése érdekében az ügyfélnek az összes fogadott szinkronizálási jogkivonattal kell válaszolnia. /RFC esetében több fejléc értékének vesszővel elválasztva kell lennie.

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
