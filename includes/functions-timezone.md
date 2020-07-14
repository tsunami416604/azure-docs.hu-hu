---
ms.openlocfilehash: b4fbeb6baa4516ca4cf3ca6194195fae2c688b07
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165658"
---
A CRON-kifejezésekkel használt alapértelmezett időzóna az egyezményes világidő (UTC) szerint van megadva. Ha egy másik időzóna alapján szeretné megkeresni a CRON-kifejezést, hozzon létre egy alkalmazást az nevű Function-alkalmazáshoz `WEBSITE_TIME_ZONE` . 

A beállítás értéke attól függ, hogy az operációs rendszer és az alkalmazás melyik csomagja fut.

|Operációs rendszer |Felkészülés |Érték |
|-|-|-|
| **Windows** |Mind | Állítsa az értéket a kívánt időzóna nevére a [Microsoft időzóna-index] ( https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10) . |
| **Linux** |Prémium<br/>Dedikált |Állítsa az értéket a kívánt időzóna nevére a [TZ-adatbázisban](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)látható módon. |

> [!NOTE]
> `WEBSITE_TIME_ZONE`jelenleg nem támogatott a Linux-fogyasztási tervben.

Például a *keleti téli idő* (Windows) vagy az *America/New_York* (Linux) UTC-05:00. A következő NCRONTAB-kifejezéssel, amely az UTC-időzónához 10:00 tartozik:

```
"0 0 15 * * *"
``` 

Vagy hozzon létre egy alkalmazás-beállítást a nevű Function alkalmazáshoz `WEBSITE_TIME_ZONE` , állítsa az értéket `Eastern Standard Time` (Windows) vagy `America/New_York` (Linux) értékre, majd használja a következő NCRONTAB kifejezést: 

```
"0 0 10 * * *"
``` 

A használatakor `WEBSITE_TIME_ZONE` az idő az adott időzónában, például a nyári időszámítás időpontjára módosul. 
