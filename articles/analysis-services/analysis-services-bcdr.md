---
title: Az Azure Analysis Services magas rendelkezésre állású |} Microsoft Docs
description: Modulhoz Azure Analysis Services magas rendelkezésre állású.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: db8f8b9b5af1583662418f774b2eb141bea53ffa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596736"
---
# <a name="analysis-services-high-availability"></a>Analysis Services magas rendelkezésre állás
Ez a cikk ismerteti, biztosítva ezzel az Azure Analysis Services-kiszolgálók magas rendelkezésre állású. 


## <a name="assuring-high-availability-during-a-service-disruption"></a>A szolgáltatás szüneteltetése során magas rendelkezésre állású modulhoz
Ritka, amíg az Azure-adatközpont kimaradás is rendelkezhetnek. Ha kimaradás lép, a business szüneteltetése, előfordulhat, hogy az utolsó néhány percet, vagy előfordulhat, hogy az óra utolsó okoz. Magas rendelkezésre állású kiszolgáló redundanciával leggyakrabban érhető el. Az Azure Analysis Services redundancia érhet el, további, másodlagos kiszolgálók létrehozása egy vagy több régióban. Ahhoz, hogy biztosítsa az adatok és metaadatok az adott kiszolgálókon, redundáns kiszolgálók létrehozása a szinkronizálás régióban a kiszolgálóval, amely offline állapotba került, akkor is:

* Központilag telepíteni modellek redundáns más régiókban. Ennél a módszernél adatainak feldolgozása az elsődleges kiszolgáló és a redundáns kiszolgálók a párhuzamos, biztosítva ezzel az összes kiszolgáló a szinkronizálás.

* [Biztonsági mentés](analysis-services-backup.md) az elsődleges kiszolgáló és a redundáns kiszolgálókon visszaállítási-adatbázisok. Például automatizálhatja az Azure storage ütemezett biztonsági mentések, és állítsa vissza a más régiókban található egyéb redundáns kiszolgálók. 

Mindkét esetben ha az elsődleges kiszolgáló nem tervezett kimaradás, módosítania kell a jelentéskészítési ügyfelek csatlakozni a kiszolgálóhoz különböző területi adatközpontban kapcsolati karakterláncokat. Ez a változás érdemes figyelembe venni a végső esetben végezze el, és csak akkor, ha egy katasztrofális regionális adatközpont-meghibásodás után következik be. Valószínűbb egy adatközpont-meghibásodás után az elsődleges kiszolgálót futtató szeretné újra hálózatra tud kapcsolódni sikerült kapcsolatok használatát az összes ügyfél frissítése előtt. 

Ne kelljen kimutatási ügyfeleken kapcsolati karakterláncok módosítása, létrehozhat egy kiszolgáló [alias](analysis-services-server-alias.md) az elsődleges kiszolgáló. Ha az elsődleges kiszolgáló leáll, módosíthatja az aliast, hogy egy másik régióban redundáns kiszolgálóra mutasson. Automatizálhatja alias a kiszolgáló nevét az elsődleges kiszolgálón egy végpont az állapot-ellenőrzéssel kódolása. Ha az állapot-ellenőrzése sikertelen, az azonos végpont egy másik régióban redundáns kiszolgálóra lehet konfigurálni. 

## <a name="related-information"></a>Kapcsolódó információk
[Biztonsági mentés és helyreállítás](analysis-services-backup.md)   
[Az Azure Analysis Services kezelése](analysis-services-manage.md)   
[Másodlagos kiszolgálók nevei](analysis-services-server-alias.md) 

