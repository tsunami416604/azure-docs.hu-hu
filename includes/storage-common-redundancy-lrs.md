---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8908ded31b96aac50db1fc25e92c2c0a8e960453
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219034"
---
A helyileg redundáns tárolás (LRS) egy adatközponton belül háromszor replikálja az adatait. A LRS legalább 99,999999999%-os (11 kilenc) tartósságot biztosít az objektumok számára egy adott évben. A LRS a legalacsonyabb árú replikálási lehetőség, amely a lehető legkevesebb tartósságot kínálja a többi lehetőséghez képest.

Ha adatközpont-szintű katasztrófa (például tűz vagy árvíz) történik, a LRS használó összes replika elveszhet vagy helyreállíthatatlan lehet. A kockázat enyhítése érdekében a Microsoft a ZRS-t, a Geo-redundáns tárolást (GRS) vagy a Geo-zóna redundáns tárolást (GZRS) javasolja.

A LRS Storage-fiók írási kérelme csak az adatok mindhárom replikába való írását követően sikeresnek számít.

A LRS a következő helyzetekben érdemes használni:

* Ha az alkalmazás adatvesztés esetén könnyen újraépíthető adatait tárolja, a LRS is dönthet.
* Egyes alkalmazások az adatirányítási követelmények miatt csak az országon/régión belül replikálják az adatreplikálást. Bizonyos esetekben azok a párosított régiók, amelyeken keresztül az GRS-fiókok replikálása történik, egy másik országban vagy régióban is lehetnek. A párosított régiókkal kapcsolatos további információkért lásd: [Azure-régiók](https://azure.microsoft.com/regions/).
