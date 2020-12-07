---
title: Magas rendelkezésre állás és vészhelyreállítás
titleSuffix: Azure Digital Twins
description: Ismerteti az Azure és az Azure digitális Twins szolgáltatásait, amelyek segítségével a vész-helyreállítási képességekkel rendelkező, magasan elérhető Azure IoT-megoldások hozhatók létre.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ac75a5b0b59a06855b7ee88d971c269ca915e429
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763164"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Azure digitális Twins – magas rendelkezésre állás és vész-helyreállítás

A rugalmas IoT megoldások szempontjából kulcsfontosságú terület az üzletmenet folytonossága és a vész-helyreállítás. A **magas rendelkezésre állású (ha)** és a vész- **helyreállítási (Dr)** rendszer tervezése lehetővé teszi a megoldáshoz szükséges hasznos üzemidői célok meghatározását és elérését.

Ez a cikk az Azure Digital Twins szolgáltatás által kínált HA és DR funkciókat ismerteti.

Az Azure Digital Twins a következő funkció-lehetőségeket támogatja:
* *Régión belüli ha* – beépített redundancia a szolgáltatás rendelkezésre állásának biztosításához
* *Régión* kívüli Dr – feladatátvétel egy geo-Párosítású Azure-régióba az adatközpont váratlan meghibásodása esetén

Az általános Azure-útmutatóhoz a HA/DR megtervezése című témakör [*ajánlott eljárásokat*](#best-practices) ismertető szakasza.

## <a name="intra-region-ha"></a>Régión belüli HA
 
Az Azure digitális Twins a szolgáltatáson belüli redundancia megvalósításán keresztül biztosít a régión belüli területet. Ezt a [szolgáltatás SLA](https://azure.microsoft.com/support/legal/sla/digital-twins) -ja mutatja a rendelkezésre álláshoz. **Egy Azure digitális Twins-megoldás fejlesztőinek nincs további teendője, hogy kihasználhassa ezeket a HA funkciókat.** Bár az Azure Digital Twins ésszerűen magas szintű üzemidőt biztosít, az átmeneti hibák várhatóak, ugyanúgy, mint bármely elosztott számítástechnikai platform esetében. A megfelelő újrapróbálkozási házirendeket be kell építeni a Felhőbeli alkalmazásokkal kommunikáló összetevőkbe az átmeneti hibák kezeléséhez.

## <a name="cross-region-dr"></a>Több régió – DR

Előfordulhat, hogy az adatközpont bizonyos ritkán fordul elő, ha áramkimaradások vagy a régió más eseményei miatt kibővített kimaradások állnak fenn. Ezek az események ritkán fordulnak elő, és az ilyen hibák miatt a fent ismertetett régión kívüli, HA-képesség nem segít. Az Azure digitális Twins ezt a Microsoft által kezdeményezett feladatátvételsel kezeli.

A Microsoft **által kezdeményezett feladatátvételt** ritka helyzetekben a Microsoft gyakorolja az érintett régióból származó összes Azure digitális Twins-példány feladatátvételére a megfelelő földrajzi helyzetű régióba. Ez a folyamat egy alapértelmezett beállítás (a felhasználók kizárása nélkül), és nem igényel beavatkozást a felhasználótól. A Microsoft fenntartja a jogot arra, hogy meghatározza, hogy a rendszer mikor gyakorolja ezt a lehetőséget. Ez a mechanizmus nem tartalmaz felhasználói beleegyező engedélyt a felhasználó példányának feladatátvétele előtt.

>[!NOTE]
> Bizonyos Azure-szolgáltatások egy, az **ügyfél által kezdeményezett feladatátvétel** nevű további lehetőséget is biztosítanak, amely lehetővé teszi az ügyfeleknek, hogy csak a példányuk számára kezdeményezzenek feladatátvételt, például egy Dr-részletezés futtatására. Az Azure Digital Twins jelenleg **nem támogatja** ezt a mechanizmust. 

## <a name="best-practices"></a>Ajánlott eljárások

A HA/DR-vel kapcsolatos ajánlott eljárásokért tekintse meg az alábbi Azure-útmutatást a témakörben: 
* Az [*Azure üzletmenet-folytonossági műszaki útmutatója*](/azure/architecture/framework/resiliency/overview) egy általános keretrendszert ismertet, amely segít az üzletmenet folytonosságának és a vész-helyreállításnak. 
* Az [*Azure-alkalmazások vész-helyreállítási és magas rendelkezésre állása*](/azure/architecture/framework/resiliency/backup-and-recovery) az Azure-alkalmazások magas rendelkezésre állást (ha) és a vész-helyreállítást (Dr) biztosító stratégiákra vonatkozó architektúrával kapcsolatos útmutatást nyújt.

## <a name="next-steps"></a>További lépések 

További információ az Azure Digital Twins-megoldások használatáról:
 
* [*Mi az az Azure Digital Twins?*](overview.md)
* [*Gyors útmutató: példa a forgatókönyvre*](quickstart-adt-explorer.md)