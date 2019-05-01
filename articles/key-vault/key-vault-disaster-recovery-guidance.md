---
title: Mi a teendő, ha meghibásodik egy Azure-szolgáltatás megszakadása, ami hatással van az Azure Key Vault az Azure Key Vault - |} A Microsoft Docs
description: Ismerje meg, mi a teendő az Azure szolgáltatáskimaradás, amely hatással van az Azure Key Vault működésében.
services: key-vault
author: barclayn
manager: barbkess
editor: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 9346f3f9bd9395ac863af87d05724a76ae83fb2f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702331"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Az Azure Key Vault rendelkezésre állás és redundancia

Az Azure Key Vault többrétegű redundanciát biztosít, győződjön meg arról, hogy a kulcsok és titkok továbbra is elérhetők az alkalmazás akkor is, ha a szolgáltatás egyes összetevők nem tudnak funkciókat.

A key vault tartalmát a rendszer replikálja a régión belül, és a egy másodlagos régióba legalább 150 mérföld nyelvet a lenti listában, de azonos földrajzi helyen belül. Ez a nagyfokú tartósság, a kulcsok és titkok tart fenn. Tekintse meg a [Azure párosított régióiról](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) dokumentum adott régiópárok részleteiért.

A key vault szolgáltatásban az egyes összetevők nem tudnak, ha a régión belül más összetevők lépést győződjön meg arról, hogy nincs-e funkció nincs teljesítménycsökkenése kérelem kiszolgálására. Nem kell semmit sem ez aktiválásához. Ez automatikusan megtörténik, és érzékelhető lesz.

A ritka esemény, hogy egy teljes Azure-régió nem érhető el, a rendszer automatikusan irányítja a kérelmeket, hogy az Azure Key vault az adott régióban (*átadta a feladatait*) egy másodlagos régióba. Ha az elsődleges régió újra elérhetővé válik, a kérelmek útválasztása vissza (*részt a feladatátvételben*) az elsődleges régióba. Újra nem kell tennie semmit, mert ez automatikusan megtörténik.

Van néhány figyelmeztetések érdemes figyelembe vennie:

* Egy régióban feladatátvételkor igénybe vehet néhány percet, amíg a szolgáltatás a feladatátvételt. Ebben az időszakban kérelmek mindaddig, amíg befejeződik a feladatátvétel sikertelen lehet.
* A feladatátvétel befejezését követően a key vault írásvédett módban van. Ebben a módban támogatott kéréseket a következők:
  * A kulcstartók listája
  * Kulcstartók tulajdonságainak beolvasása
  * Titkos kulcsainak listázása
  * Titkos kódok lekéréséhez
  * Kulcsok listázása
  * (A Tulajdonságok) kulcsok beolvasása
  * Titkosítás
  * Visszafejtés
  * Sortörés
  * Kicsomagolása
  * Ellenőrzés
  * Aláírás
  * Backup
* Feladatátvétel feladatátvételben, miután az összes kérelem típusok (beleértve az olvasási *és* írási kérelmek) érhetők el.

