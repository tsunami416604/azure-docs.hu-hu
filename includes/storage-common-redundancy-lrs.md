---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 32032f729283cb3f2a786412b563fdee88ba4c8a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238567"
---
Helyileg redundáns tárolás (LRS) biztosít legalább 99,999999999 %-os (11 nines) tárolt objektumok számára egy adott évben. LRS ezen objektum tartósságot biztosít az adatok replikálásával tárolóskálázási egységben. Egy adatközpontban, ahol létrehozta a tárfiókot, a régióban a storage skálázási egység üzemelteti. LRS tárfiók írási kérelem sikeresen visszaadja, csak azt követően az adatok írása az összes replika. Minden egyes replikának külön tartalék tartományokban és frissítési tartományokba, tárolóskálázási egységben belül található.

Tárolóskálázási egységben tárolási csomópontok rackszekrények gyűjteménye. Egy tartalék tartományt (FD) hiba fizikai egység képviselő csomópontok egy csoportját. Úgy egy tartalék tartományt, mint az azonos fizikai állványra szerelt tartozó csomópontokat. A frissítési tartomány (UD) olyan csomópontok, amelyek együtt vannak frissítve egy szolgáltatás frissítése (Bevezetés) folyamata során. A replikák vannak elosztva frissítési és tartalék tartományok belül egy tárolóskálázási egységben. Ez az architektúra biztosítja az adatok érhető el, ha hardverhiba egyetlen állványon hatással van, vagy amikor a csomópont frissítése szolgáltatás frissítése során.

LRS a legalacsonyabb költségű replikációs beállítást, és az egyéb lehetőségek képest legalább tartósságot biztosít. (Például fire vagy -elárasztás) adatközpont-szintű katasztrófa történik, ha az összes replika lehet elveszett vagy helyreállíthatatlan. A kockázat csökkentése érdekében a Microsoft azt javasolja, a zónaredundáns tárolás (ZRS) vagy georedundáns tárolást (GRS) használatával.

* Ha az alkalmazás, amely egyszerűen rekonstruálható, adatvesztés esetén adatokat tárolja, az lrs esetén is választhat.
* Az adatreplikálás adatok cégirányítási követelmények miatt országon belül csak bizonyos alkalmazások korlátozódnak. Bizonyos esetekben a párosított régiók között, amelyek a rendszer replikálja az adatokat GRS-fiók esetén lehet egy másik országban. A párosított régiók további információkért lásd: [Azure-régiók](https://azure.microsoft.com/regions/).
