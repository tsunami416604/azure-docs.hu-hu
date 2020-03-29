---
author: ilayrn
ms.service: data-explorer
ms.topic: include
ms.date: 01/20/2020
ms.author: ilayr
ms.openlocfilehash: d9e23e74cd980ecf44cd04ad2b6f89dc6a4cf868
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76548207"
---
Az alábbi táblázat az Azure Data Explorer-fürtök maximális korlátját ismerteti.

| Erőforrás | Korlát |
| --- | --- |
| Klaszterek régiónként előfizetésenként | 20 |
| Példányok fürtenként | 1000 | 
| Adatbázisok száma egy fürtben | 10,000 |
| A fürthöz csatolt adatbázis-konfigurációk száma | 70 |

Az alábbi táblázat ismerteti az Azure Data Explorer-fürtökön végrehajtott felügyeleti műveletek korlátait.

| Hatókör | Művelet | Korlát |
| --- | --- | --- |
| Fürt | olvasás (például fürt beolvasása) | 500 /5 perc |
| Fürt | írás (például adatbázis létrehozása) | 1000 óránként |

