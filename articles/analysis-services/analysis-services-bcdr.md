---
title: "Az Azure Analysis Services magas rendelkezésre állású |} Microsoft Docs"
description: "Modulhoz Azure Analysis Services magas rendelkezésre állású."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 554c5e6e3e3cfa2742ef27a3c1510176184b6bd0
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="analysis-services-high-availability"></a>Analysis Services magas rendelkezésre állás
Ez a cikk ismerteti, biztosítva ezzel az Azure Analysis Services-kiszolgálók magas rendelkezésre állású. 


## <a name="assuring-high-availability-during-a-service-disruption"></a>A szolgáltatás szüneteltetése során magas rendelkezésre állású modulhoz
Ritka, amíg az Azure-adatközpont kimaradás is rendelkezhetnek. Ha kimaradás lép, a business szüneteltetése, előfordulhat, hogy az utolsó néhány percet, vagy előfordulhat, hogy az óra utolsó okoz. Magas rendelkezésre állású kiszolgáló redundanciával leggyakrabban érhető el. Az Azure Analysis Services redundancia érhet el, további, másodlagos kiszolgálók létrehozása egy vagy több régióban. Ahhoz, hogy biztosítsa az adatok és metaadatok az adott kiszolgálókon, redundáns kiszolgálók létrehozása a szinkronizálás régióban a kiszolgálóval, amely offline állapotba került, akkor is:

* Központilag telepíteni modellek redundáns más régiókban. Ennél a módszernél adatainak feldolgozása az elsődleges kiszolgáló és a redundáns kiszolgálók a párhuzamos, biztosítva ezzel az összes kiszolgáló a szinkronizálás.

* Adatbázisok biztonsági mentése az elsődleges kiszolgáló és a visszaállítási redundáns kiszolgálókon. Például automatizálhatja az Azure storage ütemezett biztonsági mentések, és állítsa vissza a más régiókban található egyéb redundáns kiszolgálók. 

Mindkét esetben ha az elsődleges kiszolgáló nem tervezett kimaradás, módosítania kell a jelentéskészítési ügyfelek csatlakozni a kiszolgálóhoz különböző területi adatközpontban kapcsolati karakterláncokat. Ez a változás érdemes figyelembe venni a végső esetben végezze el, és csak akkor, ha egy katasztrofális regionális adatközpont-meghibásodás után következik be. Valószínűbb egy adatközpont-meghibásodás után az elsődleges kiszolgálót futtató szeretné újra hálózatra tud kapcsolódni sikerült kapcsolatok használatát az összes ügyfél frissítése előtt. 



## <a name="related-information"></a>Kapcsolódó információk
[Biztonsági mentés és helyreállítás](analysis-services-backup.md)   
[Az Azure Analysis Services kezelése](analysis-services-manage.md) 

