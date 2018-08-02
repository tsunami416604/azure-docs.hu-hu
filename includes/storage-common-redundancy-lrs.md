---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 58d81bd4c1ce4b3af91a335039f62df08b340576
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399991"
---
Helyileg redundáns tárolás (LRS) úgy tervezték, hogy legalább 99,999999999 %-os (11 9-es) tartós objektumok egy adott évben tárolóskálázási egységben belül az adatok replikálásával. Tárolóskálázási egységben helyezkedik el egy adatközpontban, a régiót, amelyben a tárfiókot létrehozták. LRS tárfiók írási kérelem sikeresen visszaadja, csak azt követően az adatok összes replika lett írva. E replikák mindegyike található tartalék tartományok elválasztásához, és a frissítési tartományok belül egy tárolóskálázási egységben.

Tárolóskálázási egységben tárolási csomópontok rackszekrények gyűjteménye. A tartalék tartomány (FD) az felel meg a hiba fizikai egységet, és az ugyanazon fizikai állványra szerelt tartozó tekinthető csomópontok egy csoportját. A frissítési tartomány (UD) olyan csomópontok, amelyek együtt vannak frissítve egy szolgáltatás frissítése (Bevezetés) folyamata során. A replikák vannak elosztva frissítési és tartalék tartományok belül egy tárolóskálázási egységben. Ez az architektúra biztosítja, hogy az adatok akkor áll rendelkezésre, ha hardverhiba egyetlen állványon hatással van, vagy amikor a csomópont frissítése a kibocsátás közben.

LRS legalacsonyabb költségek replikálási beállítását és az egyéb lehetőségek képest legalább tartóssági kínál. (Például fire vagy -elárasztás) adatközpont-szintű katasztrófa történik, ha az összes replika lehet elveszett vagy helyreállíthatatlan. A kockázat csökkentése érdekében a Microsoft azt javasolja, a zónaredundáns tárolás (ZRS) vagy georedundáns tárolást (GRS) használatával.

* Ha az alkalmazás, amely egyszerűen rekonstruálható, adatvesztés esetén adatokat tárolja, az lrs esetén is választhat.
* Az adatreplikálás adatok cégirányítási követelmények miatt országon belül csak bizonyos alkalmazások korlátozódnak. Bizonyos esetekben a párosított régiók között, amelyek az adatok replikálási GRS fiókok lehet egy másik országban. A párosított régiók további információkért lásd: [Azure-régiók](https://azure.microsoft.com/regions/).
