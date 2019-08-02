---
title: Absztrakt – tárolók biztonsága Microsoft Azure
description: Absztrakt a tárolók biztonsága Microsoft Azure tanulmányban.
author: TomShinder
ms.author: TomSh
ms.date: 09/05/2018
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.openlocfilehash: b70744f403c483448a844d3f3decf6ce26f48f06
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727784"
---
# <a name="container-security-in-microsoft-azure"></a>Tárolók biztonsága Microsoft Azure
## <a name="abstract"></a>Absztrakt

A tároló technológia strukturális változást okoz a felhőalapú számítástechnikai világban. A tárolók lehetővé teszik, hogy egy alkalmazás több példányát futtassák egy operációs rendszer egyetlen példányán, így hatékonyabban használják az erőforrásokat. A tárolók a szervezetek következetességét és rugalmasságát biztosítják. Lehetővé teszik a folyamatos üzembe helyezést, mivel az alkalmazás az asztalon, egy virtuális gépen tesztelve, majd a felhőben éles környezetben üzembe helyezhető. A tárolók az erőforrás-optimalizálás miatt rugalmasságot, gördülékeny működést, méretezhetőséget és alacsonyabb költségeket biztosítanak.

Mivel a Container Technology viszonylag új, az informatikai szakemberek számára számos biztonsági probléma merült fel az éles környezetben való láthatóság és használat hiánya miatt. A fejlesztői csapatok gyakran nem ismerik az ajánlott biztonsági eljárásokat. Ez a tanulmány segíthet a biztonsági operatív csapatoknak és fejlesztőknek a Microsoft Azure platformon a tárolók fejlesztéséhez és üzembe helyezéséhez szükséges megközelítések kiválasztásában.

Ez a tanulmány a tárolókat, a tárolók üzembe helyezését és felügyeletét, valamint a natív platform-szolgáltatásokat ismerteti. Emellett az Azure platformon tárolók használatával kapcsolatos futásidejű biztonsági problémákat is ismerteti. Az ábrák és példák alapján ez a dokumentum a Docker-re összpontosít, mint a tároló-és a Kubernetes tároló Orchestrator. A biztonsági javaslatok többsége az Azure platformon található Microsoft-partnerektől származó egyéb tároló modellekre is érvényes.

[A tanulmány letöltése](https://azure.microsoft.com/mediahandler/files/resourcefiles/container-security-in-microsoft-azure/Open%20Container%20Security%20in%20Microsoft%20Azure.pdf)