---
title: Az Azure Analysis Services magas rendelkezésre állású |} A Microsoft Docs
description: Azure Analysis Services magas rendelkezésre állást biztosítva.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 657800c6f96560c68e690ccbd1dfb166c5034812
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430136"
---
# <a name="analysis-services-high-availability"></a>Analysis Services magas rendelkezésre állás
Ez a cikk ismerteti az Azure Analysis Services-kiszolgálók magas rendelkezésre állást biztosítva. 


## <a name="assuring-high-availability-during-a-service-disruption"></a>Magas rendelkezésre állást biztosítva a szolgáltatáskimaradás közben
Bár ritka, valamely Azure adatközpontnak a kimaradás is rendelkezhet. Szolgáltatáskimaradás esetén előfordulhat, hogy az elmúlt pár percet, vagy előfordulhat, hogy az elmúlt óra üzletmenet okoz. Magas rendelkezésre állású kiszolgáló redundanciával leggyakrabban érhető el. Az Azure Analysis Services további, másodlagos kiszolgálók egy vagy több régióban való létrehozásával érheti el a redundancia. Ahhoz, hogy biztosítsa az adatok és metaadatok ezeken a kiszolgálókon, redundáns kiszolgálók létrehozása szinkronizálva olyan régióban a kiszolgálóval, amely offline állapotba került, is:

* Modellek üzembe helyezése más régióban található redundáns kiszolgálókon. Ennél a módszernél az elsődleges kiszolgáló és a redundáns kiszolgálókon lévő párhuzamos feldolgozási adatok, biztosítva ezzel az összes kiszolgálón szinkronizálva.

* [Biztonsági mentés](analysis-services-backup.md) adatbázisok az elsődleges kiszolgáló és a visszaállítás található redundáns kiszolgálókon. Például automatizálható az Azure storage-éjszakai biztonsági mentése, és más redundáns kiszolgálókon más régiókban való visszaállításához. 

Mindkét esetben ha az elsődleges kiszolgáló szolgáltatáskimaradás következik be, módosítania kell a kapcsolati karakterláncokat a hibajelentő ügyfeleknek egy másik regionális adatközpontban lévő a kiszolgálóhoz való csatlakozáshoz. Ez a változás végső megoldásként kell tekinteni, és csak akkor, ha egy katasztrofális regionális adatközpontjának leállása esetén történik. Több valószínű egy adatközpont-leállás esetén az elsődleges kiszolgálót üzemeltető szeretné ismét online elérhető lesz sikerült kapcsolatok az összes ügyfél frissítése előtt. 

Ne kelljen módosítani a hibajelentő ügyfeleknek kapcsolati karakterláncok, hozzon létre egy kiszolgálót [alias](analysis-services-server-alias.md) az elsődleges kiszolgáló. Ha az elsődleges kiszolgáló leáll, módosíthatja az alias egy másik régióban található redundáns kiszolgálóra mutasson. Automatizálhatja alias a kiszolgálónév kódolási egy végpont állapotának ellenőrzése az elsődleges kiszolgálón. Az állapot-ellenőrzés nem sikerül, ha egyazon végpont irányíthatók, egy másik régióban található redundáns kiszolgálóhoz. 

## <a name="related-information"></a>Kapcsolódó információk
[Biztonsági mentés és visszaállítás](analysis-services-backup.md)   
[Azure Analysis Services kezelése](analysis-services-manage.md)   
[Alias kiszolgálók nevei](analysis-services-server-alias.md) 

