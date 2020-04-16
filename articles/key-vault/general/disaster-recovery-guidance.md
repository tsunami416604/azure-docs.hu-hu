---
title: Mi a teendő az Azure Key Vault – Azure Key Vault – Azure Key Vault – szolgáltatást érintő azure-szolgáltatáskimaradás esetén | Microsoft dokumentumok
description: Ismerje meg, mi a teendő az Azure Key Vaultot érintő Azure-szolgáltatáskimaradás esetén.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 96929dcbe3d51589b0c3c0df89671dadb20e37cc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422948"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Az Azure Key Vault elérhetősége és redundanciája

Az Azure Key Vault többrétegű redundanciát tartalmaz annak érdekében, hogy a kulcsok és a titkos kulcsok továbbra is elérhetők maradjanak az alkalmazás számára, még akkor is, ha a szolgáltatás egyes összetevői sikertelenek.

A kulcstartó tartalma replikálása a régión belül és egy másodlagos régióban legalább 150 mérföldre van, de ugyanazon a földrajzi helyen. Ez fenntartja a kulcsok és titkok nagy fokú tartósságát. Az [Azure párosított régiók](../../best-practices-availability-paired-regions.md) dokumentum az adott régiópárok részleteit.

Ha a key vault szolgáltatás egyes összetevői nem sikerül, a régión belüli alternatív összetevők lépnek be a kérés kiszolgálásához, hogy megbizonyosodjon arról, hogy nincs a funkcionalitás romlása. Ennek elindításához nem kell semmilyen műveletet végrehajtania. Ez automatikusan történik, és átlátható lesz az Ön számára.

Abban a ritka esetben, ha egy teljes Azure-régió nem érhető el, az Azure Key Vault az adott régióban a kérelmeket, hogy az adott régióban automatikusan átirányítja *(feladatátvétel)* egy másodlagos régióba. Amikor az elsődleges régió ismét elérhető, a kérelmek vissza *(feladat-vissza*) az elsődleges régióba. Ismét nem kell semmilyen műveletet végrehajtania, mert ez automatikusan megtörténik.

Ezzel a magas rendelkezésre állású kialakítással az Azure Key Vault nem igényel állásidőt a karbantartási tevékenységekhez.

Van néhány kikötések, hogy tudatában legyünk:

* Egy régió feladatátvétele esetén eltarthat néhány percig, amíg a szolgáltatás feladatátvételt. Az ez idő alatt végrehajtott kérelmek sikertelenek lehetnek, amíg a feladatátvétel befejeződik.
* A feladatátvétel befejezése után a kulcstartó írásvédett módban van. Az ebben a módban támogatott kérelmek a következők:
  * Kulcstartók listázása
  * A kulcstartók tulajdonságainak beszereznie
  * Titkok listázása
  * Titkok beszerezése
  * Billentyűk listázása
  * Kulcsok bekése (tulajdonságai)
  * Titkosítás
  * Visszafejtés
  * Betakar
  * Kicsomagolás
  * Ellenőrzés
  * Előjel
  * Backup
* A feladatátvétel sikertelenvé válik, az összes kérelemtípus (beleértve az olvasási *és* írási kérelmeket is) elérhető.

