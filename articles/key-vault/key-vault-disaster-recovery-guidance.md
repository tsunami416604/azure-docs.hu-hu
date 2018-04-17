---
title: Mi a teendő, ha az Azure szolgáltatás, amely befolyásolja az Azure Key Vault megszűnésének |} Microsoft Docs
description: Ismerje meg, mi a teendő az Azure-szolgáltatások becsukódjon, amely befolyásolja az Azure Key Vault.
services: key-vault
documentationcenter: ''
author: adamglick
manager: mbaldwin
editor: ''
ms.assetid: 19a9af63-3032-447b-9d1a-b0125f384edb
ms.service: key-vault
ms.workload: key-vault
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: aglick
ms.openlocfilehash: 11c2fe5d4b84f99c3b0e303d1abeea73442f57aa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Az Azure Key Vault rendelkezésre állás és redundancia
Az Azure Key Vault a redundancia csökkentése érdekében győződjön meg arról, hogy a kulcsok és titkos használhatóak maradnak arra az alkalmazás akkor is, ha a szolgáltatás egyes összetevők sikertelen rétege funkciókat.

A kulcstartót tartalmát replikálódnak a régión belül, és egy másodlagos régióban legalább 150 miles számítógépnél, de ugyanazon a földrajzi belül. Ezzel a megoldással magas tartósság a kulcsok és titkos kulcsok. Tekintse meg a [Azure párosítva régiók](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) dokumentumban talál részletes információt adott régióban párokat.

A kulcstároló szolgáltatáson belül az egyes összetevők sikertelen lesz, ha más összetevők abban a régióban lépéseket teljesíteni a kérést, győződjön meg arról, hogy van-e a funkcióinak csökkenése nélkül működhet. Nem kell elindítani a teendője. Ez automatikusan történik, és lesz érzékelhető.

Az esemény ritkán fordul elő, hogy a teljes Azure-régió nem érhető el, a rendszer a kérést, az Azure Key Vault az adott régióban végrehajtott automatikusan átirányítja (*átadja a feladatokat*) másodlagos régióba. Az elsődleges régióban újból elérhető lesz, ha rendszer kérést átirányítja vissza (*vissza nem sikerült*) az elsődleges régióban. Ebben az esetben nem kell tennie semmit, mert ez automatikusan megtörténik.

Van néhány figyelmeztetések-érdemes figyelembe:

* Régió feladatátvétel esetén a szolgáltatás számára, hogy a feladatátvétel néhány percig is eltarthat. Ebben az időszakban irányuló kérelmek sikertelenek lehetnek mindaddig, amíg befejeződik a feladatátvétel.
* A feladatátvétel befejezése után a kulcstartót csak olvasható módban van. Ebben a módban támogatott kéréseket a következők:
  * Kulcstároló naplófájljainak listája
  * Kulcstároló tulajdonságainak beolvasása
  * Titkos kulcsainak listázása
  * Titkos kulcsok beszerzése
  * Listázása
  * (Tulajdonságainak) kulcs beszerzése
  * Titkosítás
  * Visszafejtés
  * Naplócsonkulási
  * Kicsomagolásához
  * Ellenőrzés
  * Aláírás
  * Biztonsági mentés
* Miután a feladatátvétel nem sikerült vissza, az összes kérelemtípusok (beleértve az olvasás *és* írási kérelmeket szolgál) érhetők el.

