---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b4b050befe160d69fbc44b7f0a2ebbbbad3d705a
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69015961"
---
A helyileg redundáns tárolás (LRS) legalább 99,999999999%-os (11 kilenc) tartósságot biztosít az objektumok számára egy adott évben. Az LRS biztosítja az objektum tartósságát, ha az adatait egy tárolási méretezési egységbe replikálja. Az adatközpont, amely abban a régióban található, ahol létrehozta a Storage-fiókot, a a tárolási méretezési egységet tárolja. A LRS Storage-fiók írási kérelme csak az adatoknak az összes replikába való beírását követően sikeresnek számít. Minden replika külön tartalék tartományokban található, és a tárolási méretezési egységen belül frissíti a tartományokat.

A tárolási méretezési egység tárolási csomópontok gyűjteménye. A tartalék tartomány (FD) csomópontok olyan csoportja, amely a meghibásodás fizikai egységét jelöli. Úgy gondolja, hogy egy tartalék tartomány az ugyanahhoz a fizikai állványhoz tartozó csomópontként van. A frissítési tartomány (UD) olyan csomópontok csoportja, amelyek egy szolgáltatás verziófrissítésének (Bevezetés) folyamata során frissülnek. A replikák a frissítési és a tartalék között oszlanak meg egyetlen tárolási méretezési egységen belül. Ez az architektúra biztosítja, hogy az adatai elérhetők legyenek, ha a hardverhiba hatással van egyetlen állványra, vagy amikor a csomópontok frissülnek a szolgáltatás frissítésekor.

A LRS a legalacsonyabb árú replikálási lehetőség, amely a lehető legkevesebb tartósságot kínálja a többi lehetőséghez képest. Ha adatközpont-szintű katasztrófa (például tűz vagy árvíz) történik, az összes replika elveszhet vagy helyreállíthatatlan lehet. A kockázat enyhítése érdekében a Microsoft a ZRS-t, a Geo-redundáns tárolást (GRS) vagy a Geo-zóna redundáns tárolást (GZRS) javasolja.

* Ha az alkalmazás adatvesztés esetén könnyen újraépíthető adatait tárolja, a LRS is dönthet.
* Egyes alkalmazások az adatirányítási követelmények miatt csak az országon/régión belül replikálják az adatreplikálást. Bizonyos esetekben azok a párosított régiók, amelyeken keresztül az GRS-fiókok replikálása történik, egy másik országban vagy régióban is lehetnek. A párosított régiókkal kapcsolatos további információkért lásd: [Azure-régiók](https://azure.microsoft.com/regions/).
