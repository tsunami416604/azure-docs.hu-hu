---
title: Mi a teendő egy olyan Azure-szolgáltatás megszakadása esetén, amely hatással van a Azure Key Vault-Azure Key Vaultra | Microsoft Docs
description: Ismerje meg, mi a teendő olyan Azure-szolgáltatás megszakadásakor, amely hatással van a Azure Key Vaultra.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 96929dcbe3d51589b0c3c0df89671dadb20e37cc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422948"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Azure Key Vault rendelkezésre állás és redundancia

Azure Key Vault több réteget is tartalmaz, így biztosíthatja, hogy a kulcsok és a titkos kódok továbbra is elérhetők legyenek az alkalmazás számára, még akkor is, ha a szolgáltatás egyes összetevői meghibásodnak.

A Key Vault tartalmát a régión belül, a másodlagos régióba legalább 150 mérfölddel arrébb, de ugyanazon a földrajzon belül replikálja a rendszer. Ez fenntartja a kulcsok és a titkok magas tartósságát. Az egyes régiókkal kapcsolatos részletekért tekintse meg az [Azure párosított régiókról](../../best-practices-availability-paired-regions.md) szóló dokumentumot.

Ha a Key Vault szolgáltatásban lévő egyes összetevők meghibásodnak, a régión belüli alternatív összetevők, hogy kiszolgálják a kérést, hogy a rendszer ne csökkentse a funkciók romlását. A művelet elindításához semmilyen műveletet nem kell elvégeznie. Ez automatikusan megtörténik, és transzparens lesz az Ön számára.

Abban a ritka esetben, ha egy teljes Azure-régió nem érhető el, az adott régióban Azure Key Vault a kérelmeket a rendszer automatikusan átirányítja (*feladatátvétel*) egy másodlagos régióba. Ha az elsődleges régió újra elérhetővé válik, a rendszer visszairányítja a kérelmeket az elsődleges régióhoz (a*sikertelen visszaállítást*). Megint nem kell semmit tennie, mert ez automatikusan megtörténik.

A magas rendelkezésre állás kialakításán keresztül a Azure Key Vault nem igényel állásidőt karbantartási tevékenységekhez.

A következőkkel kapcsolatos figyelmeztetéseket kell figyelembe venni:

* Régió feladatátvétele esetén eltarthat néhány percig, amíg a szolgáltatás feladatátvételt végez. Az ebben az időszakban végrehajtott kérelmek sikertelenek lehetnek, amíg a feladatátvétel be nem fejeződik.
* A feladatátvétel befejezése után a kulcstartó írásvédett módban van. Az ebben a módban támogatott kérelmek a következők:
  * Kulcstartók listázása
  * Key vaultok tulajdonságainak beolvasása
  * Titkos kulcsok listázása
  * Titkok beolvasása
  * Kulcsok listázása
  * Kulcsok beolvasása (tulajdonságai)
  * Titkosítás
  * Visszafejtés
  * Wrap
  * Kicsomagolása
  * Ellenőrzés
  * Előjel
  * Backup
* A feladatátvétel meghiúsulása után az összes kérelem típusa (beleértve az olvasási *és* írási kérelmeket is) elérhető.

