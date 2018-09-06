---
title: Absztrakt tárolóbiztonság Microsoft Azure-ban
description: A tároló biztonság, a Microsoft Azure (tanulmány) absztrakt.
author: TomShinder
ms.author: TomSh
ms.date: 09/05/2018
ms.topic: article
ms.service: security
ms.openlocfilehash: 2ce3aec3b7a588076584ebdf400f8cafcdeb02fe
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "43842939"
---
# <a name="container-security-in-microsoft-azure"></a>Tárolóbiztonság Microsoft Azure-ban
## <a name="abstract"></a>Absztrakt

Tárolótechnológia szerkezeti változást okoz a felhő-számítástechnika világában. Tárolók koncepcióját egy alkalmazás több példányának futtatása az operációs rendszer, és ezáltal hatékonyabban az erőforrások használata egyetlen példányán. Tárolók ad a szervezetek konzisztencia és rugalmasság. Mivel az alkalmazás lehet egy asztali fejlesztett, tesztelni a virtuális gépen, és majd az éles környezetben, a felhőben üzembe helyezett használatukkal folyamatos üzembe helyezés. Tárolók biztosítják a rugalmasságot, a műveletek egyszerűsítése, a skálázhatóság és az erőforrás-optimalizálás miatt csökkenti a költségeket.

Mivel tárolótechnológia viszonylag új, számos informatikai szakemberek biztonsági aggályokat láthatóságot és használati hiánya éles környezetben rendelkezik. Fejlesztői csapatok gyakran deduplikálta a bevált biztonsági gyakorlatokat. Ez a tanulmány segítségével biztonsági üzemeltetési csapatok és módszerek kiválasztása a fejlesztők számára, hogy biztonságos tárolóalapú fejlesztést és a Microsoft Azure platformon való központi telepítéséhez.

Ez a tanulmány azt ismerteti, tárolók, a tárolók üzembe helyezése és a felügyeleti és a natív adatplatform-szolgáltatásaival. Azt is ismerteti modul biztonsági problémák merülnek fel a tárolók használatát az Azure platformon. Az ábrák és példák koncentrál Docker tároló modell és a tárolóvezénylők főcsomópontjait Kubernetes. A biztonsági javaslatok a legtöbb is alkalmazható egyéb tároló-modellek a Microsoft-partnerektől az Azure platformon.

[A tanulmány letöltése](https://azure.microsoft.com/mediahandler/files/resourcefiles/container-security-in-microsoft-azure/Open%20Container%20Security%20in%20Microsoft%20Azure.pdf)