---
title: Az Azure Analysis Services magas rendelkezésre állása | Microsoft dokumentumok
description: Ez a cikk azt ismerteti, hogy az Azure Analysis Services hogyan biztosít magas rendelkezésre állást a szolgáltatás kibontása során.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2e750dce804ea93f3d3068ffd36bc7a73a50906a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73573354"
---
# <a name="analysis-services-high-availability"></a>Analysis Services magas rendelkezésre állás

Ez a cikk az Azure Analysis Services-kiszolgálók magas rendelkezésre állásának biztosítását ismerteti. 

## <a name="assuring-high-availability-during-a-service-disruption"></a>Magas rendelkezésre állás biztosítása a szolgáltatás kimaradása esetén

Bár ritka, az Azure-adatközpontok kimaradást is okozhatnak. Kimaradás esetén az üzleti zavarok néhány percig tarthatnak, vagy órákig tarthatnak. A magas rendelkezésre állást leggyakrabban a kiszolgáló redundanciája éri el. Az Azure Analysis Services használatával redundanciát érhet el, ha egy vagy több régióban további másodlagos kiszolgálókat hoz létre. Amikor redundáns kiszolgálókat hoz létre, hogy az ezeken a kiszolgálókon lévő adatok és metaadatok szinkronban vannak a kiszolgálóval egy kapcsolat nélküli módú régióban, a következőket teheti:

* Modellek telepítése más régiók redundáns kiszolgálóira. Ez a módszer az elsődleges kiszolgálón és a redundáns kiszolgálókon egyaránt párhuzamosan kell feldolgozni az adatokat, biztosítva, hogy az összes kiszolgáló szinkronban legyen.

* [Biztonsági másolat](analysis-services-backup.md) az elsődleges kiszolgálóról, és visszaállítás redundáns kiszolgálókon. Például automatizálhatja az azure-tárhelyre való éjszakai biztonsági mentéseket, és visszaállíthatja a többi redundáns kiszolgálót más régiókban. 

Mindkét esetben, ha az elsődleges kiszolgáló szolgáltatás kimaradást tapasztal, módosítania kell a kapcsolati karakterláncokat a jelentési ügyfelekben, hogy egy másik regionális adatközpontban csatlakozzanak a kiszolgálóhoz. Ezt a módosítást végső megoldásnak kell tekinteni, és csak akkor, ha katasztrofális regionális adatközpont-kimaradás következik be. Valószínűbb, hogy az elsődleges kiszolgálót üzemeltető adatközpont-kimaradás újra online állapotba kerülne, mielőtt az összes ügyfél kapcsolatát frissíthetné. 

Annak elkerülése érdekében, hogy a jelentéskészítő ügyfelek kapcsolati karakterláncait meg kelljen változtatnia, létrehozhat egy [kiszolgálóaliast](analysis-services-server-alias.md) az elsődleges kiszolgálóhoz. Ha az elsődleges kiszolgáló leáll, módosíthatja az aliast úgy, hogy egy másik régióban lévő redundáns kiszolgálóra mutasson. Az aliast az elsődleges kiszolgálón lévő végpontállapot-ellenőrzés kódolásával automatizálhatja a kiszolgáló nevéhez. Ha az állapot-ellenőrzés sikertelen, ugyanaz a végpont egy másik régióban lévő redundáns kiszolgálóra irányítható. 

## <a name="related-information"></a>Kapcsolódó információk

[Biztonsági mentés és visszaállítás](analysis-services-backup.md)   
[Az Azure Analysis Services kezelése](analysis-services-manage.md)   
[Aliaskiszolgáló-nevek](analysis-services-server-alias.md) 

