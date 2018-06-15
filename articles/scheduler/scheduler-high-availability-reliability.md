---
title: A Feladatütemező magas rendelkezésre állású és a megbízhatóság
description: A Feladatütemező magas rendelkezésre állású és a megbízhatóság
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 5ec78e60-a9b9-405a-91a8-f010f3872d50
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2016
ms.author: deli
ms.openlocfilehash: 7e7fe49de7814b6058468d630f8638720e5864f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866092"
---
# <a name="scheduler-high-availability-and-reliability"></a>A Feladatütemező magas rendelkezésre állású és a megbízhatóság
## <a name="azure-scheduler-high-availability"></a>Azure Schedulerrel magas rendelkezésre állású
Alapszintű Azure platformon service Azure Scheduler magas rendelkezésre állású és georedundáns szolgáltatás központi telepítése és a földrajzi-területi feladat replikációs.

### <a name="geo-redundant-service-deployment"></a>Georedundáns szolgáltatás központi telepítése
Azure Schedulerrel szinte minden földrajzi régióban, amely az Azure-ban még ma a felhasználói felületen keresztül érhető el. A lista, amely Azure Scheduler elérhető régiók [az itt felsorolt](https://azure.microsoft.com/regions/#services). Egy adott adatközpont üzemeltetett régióban megjelenítése nem érhető el, ha Azure Scheduler feladatátvételi képességének van, úgy, hogy a szolgáltatás nem érhető el egy másik adatközpontból.

### <a name="geo-regional-job-replication"></a>Földrajzi-területi feladat replikáció
Nem csak az előtér-kérelmek, de a saját feladat elérhető egyben georeplikált az Azure Scheduler. Ha kimaradás van egy régió tartozik, az Azure Scheduler átadja a feladatokat, és biztosítja, hogy a feladat futnak-e egy másik adatközpontból párosított földrajzi régióban.

Például ha létrehozott egy feladatot a déli középső Régiójában, Azure Scheduler automatikusan replikálja az északi középső Régiójában feladatot. Ha hiba történik a déli középső Régiójában, a Azure Scheduler biztosítja, hogy a feladat fut északi középső Régiójában. 

![][1]

Ennek eredményeképpen Azure Scheduler biztosítja, hogy az adatok egy Azure hiba esetén azonos nevű szélesebb körű földrajzi régióban belül marad. Ennek eredményeképpen kell nem másolatot készít a feladat csak adja hozzá a magas rendelkezésre állás – Azure Scheduler automatikusan a feladatok a magas rendelkezésre állású képességeket biztosít.

## <a name="azure-scheduler-reliability"></a>Azure Schedulerrel megbízhatóság
Azure Schedulerrel biztosítja, hogy a saját magas rendelkezésre állású, és egy másik módszert a felhasználó által létrehozott feladatok vesz igénybe. Például a feladat alkalmazhatja a HTTP-végponttal, amely nem érhető el. Azure Schedulerrel mégis megpróbálja végrehajtása a feladat, mivel a alternatív beállítások foglalkoznak. Azure Schedulerrel hajtja végre ezt két módon:

### <a name="configurable-retry-policy-via-retrypolicy"></a>"RetryPolicy" via konfigurálható újra házirend
Azure Schedulerrel újrapróbálkozási házirendje konfigurálását teszi lehetővé. Alapértelmezés szerint a feladat sikertelen lesz, ha Feladatütemező megkísérli a feladat újra négy alkalommal, 30 másodperces időközönként. Újból konfigurálhatja az újrapróbálkozási házirendet szigorúbb (például tízszeresének, 30 másodperces időközönként) vagy lazább (például kétszer, naponta történik.)

Amikor ez segíthet az például létrehozhat egy feladatot, amely hetente egyszer fut, és elindítja a HTTP-végponttal. A HTTP-végpont a feladat futtatásakor néhány órán keresztül nem működik, ha lehetséges, hogy nem szeretne várjon a feladat újra futtatni, mivel még a alapértelmezett újrapróbálkozási házirend egy további hét. Ilyen esetekben előfordulhat, hogy újrakonfigurálása 30 másodpercenként helyett a szokásos újrapróbálkozási házirendet, majd ismételje meg minden három óra (például).

Annak megismerése, hogyan konfigurálhatja az újrapróbálkozási házirendje, tekintse meg [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoint-configurability-via-erroraction"></a>Másik végpont Beállíthatóság "errorAction" keresztül
Ha a cél-végponthoz, a Azure Scheduler feladat marad nem érhető el, Azure Scheduler visszaáll a másodlagos hibakezelő végpont az újrapróbálkozási házirendje végrehajtása után. Ha egy másik hibakezelés végpontot úgy van beállítva, az Azure Scheduler meghívja. Egy másik végpont a saját feladatok magas rendelkezésre állásuk hiba áll.

Tegyük fel, az alábbi ábrán Azure Scheduler követi az újrapróbálkozási házirendje egy Győri webszolgáltatás érni. Után az ismételt próbálkozás sikertelen lesz, ha nincs alternatív ellenőrzi. Majd előre kerül, és elindítja a kérelmet benyújtó a helyettesítő az azonos újrapróbálkozási házirendet.

![][2]

Vegye figyelembe, hogy az azonos újrapróbálkozási házirendet az eredeti művelet és a másik hibaműveletet is vonatkozik. Az is előfordulhat, hogy a másik hibaműveletet művelettípus térhet el a fő műveletétől művelet típusa. Például a fő műveletétől. Előfordulhat, hogy egy HTTP-végpont meghívása kell, amíg a hibaműveletnek lehet egy tároló várólista, a service bus-üzenetsorba vagy a service bus témakör művelet, amelyet hiba-naplózás.

Egy másik végpont konfigurálásáról további tudnivalókért tekintse meg [errorAction](scheduler-concepts-terms.md#action-and-erroraction).

## <a name="see-also"></a>Lásd még:
 [A Scheduler ismertetése](scheduler-intro.md)

 [Az Azure Scheduler alapfogalmai, terminológiája és entitáshierarchiája](scheduler-concepts-terms.md)

 [Ismerkedés a Scheduler szolgáltatás Azure Portalon való használatával](scheduler-get-started-portal.md)

 [Csomagok és számlázás az Azure Schedulerben](scheduler-plans-billing.md)

 [Komplex és speciális, ismétlődő ütemezések létrehozása az Azure Scheduler használatával](scheduler-advanced-complexity.md)

 [Az Azure Scheduler REST API-jának leírása](https://msdn.microsoft.com/library/mt629143)

 [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)

 [Azure Scheduler – korlátozások, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)

 [Kimenő hitelesítés az Azure Schedulerben](scheduler-outbound-authentication.md)

[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png

[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png
