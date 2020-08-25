---
title: Mi a teendő, ha az Azure-szolgáltatás megszakadása hatással van a Azure Key Vault-Azure Key Vaultra | Microsoft Docs
description: Ismerje meg, mi a teendő egy Azure-szolgáltatás megszakadásával kapcsolatban Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: sudbalas
ms.openlocfilehash: e4364c3c5bcba1a04837f3f9e7a0576579211c72
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88796595"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Az Azure Key Vault rendelkezésre állása és redundanciája

Azure Key Vault több réteget is tartalmaz, így biztosíthatja, hogy a kulcsok és a titkos kódok továbbra is elérhetők legyenek az alkalmazás számára, még akkor is, ha a szolgáltatás egyes összetevői meghibásodnak.

A Key Vault tartalmát a régión belül, illetve legalább 150 mérfölddel arrébb egy másodlagos régióba replikálja a rendszer, a kulcsok és a titkok magas tartósságának fenntartása érdekében. Az egyes régiókkal kapcsolatos részletekért tekintse meg az [Azure párosított régiókról](../../best-practices-availability-paired-regions.md) szóló dokumentumot.

Ha a Key Vault szolgáltatásban lévő egyes összetevők meghibásodnak, a régión belüli alternatív összetevők, hogy kiszolgálják a kérést, hogy a rendszer ne csökkentse a funkciók romlását. A folyamat elindításához semmilyen műveletet nem kell végrehajtania, automatikusan megtörténik, és transzparens lesz.

Abban a ritka esetben, ha egy teljes Azure-régió nem érhető el, az adott régióban Azure Key Vault a kérelmeket a rendszer automatikusan átirányítja (*feladatátvétel*) egy másodlagos régióba. Ha az elsődleges régió újra elérhetővé válik, a rendszer visszairányítja a kérelmeket az elsődleges régióhoz (a*sikertelen visszaállítást*). Megint nem kell semmit tennie, mert ez automatikusan megtörténik.

A magas rendelkezésre állás kialakításán keresztül a Azure Key Vault nem igényel állásidőt karbantartási tevékenységekhez.

A következőkkel kapcsolatos figyelmeztetéseket kell figyelembe venni:

* Régió feladatátvétele esetén eltarthat néhány percig, amíg a szolgáltatás feladatátvételt végez. A feladatátvételt megelőzően végrehajtott kérelmek sikertelenek lehetnek.
* A feladatátvétel során a kulcstartó írásvédett módban van. Az ebben a módban támogatott kérelmek a következők:
  * Kulcstartók listázása
  * Key vaultok tulajdonságainak beolvasása
  * Tanúsítványok listázása
  * Tanúsítványok beolvasása
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
* A feladatátvétel során a Key Vault tulajdonságai nem módosíthatók. Nem fogja tudni módosítani a hozzáférési házirendet, illetve a tűzfal konfigurációit és beállításait.
* A feladatátvétel meghiúsulása után az összes kérelem típusa (beleértve az olvasási *és* írási kérelmeket is) elérhető.

