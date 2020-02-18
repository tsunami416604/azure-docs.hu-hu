---
title: Adattitkosítás Azure Database for MySQL hibaelhárításhoz
description: Ismerje meg, hogyan lehet elhárítani a Azure Database for MySQL adattitkosítását
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 4b517a463ec949d804798787ad4b35b53145a4a8
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371552"
---
# <a name="troubleshooting-data-encryption-with-customer-managed-keys-in-azure-database-for-mysql"></a>Az adattitkosítás hibaelhárítása az ügyfél által felügyelt kulcsokkal Azure Database for MySQL
Ez a cikk azt ismerteti, hogyan azonosíthatók és oldhatók meg azok a gyakori hibák/hibák, amelyek az ügyfél által felügyelt kulcs használatával adattitkosítással konfigurált Azure Database for MySQLon történnek.

## <a name="introduction"></a>Introduction (Bevezetés)
Ha az adattitkosítás úgy van konfigurálva, hogy az ügyfél által felügyelt kulcs használatára Azure Key Vault, a kulcshoz való folyamatos hozzáférésre van szükség ahhoz, hogy a kiszolgáló elérhető maradjon. Ha a kiszolgáló nem fér hozzá az ügyfél által felügyelt kulcshoz Azure Key Vault, a kiszolgáló megtagadja az összes kapcsolatot a megfelelő hibaüzenettel, és az állapota nem ***érhető*** el a Azure Portal.

Ha nem érhető el Azure Database for MySQL-kiszolgáló már nem szükséges, azonnal törölheti a felmerülő költségek leállítását. A kiszolgálón lévő összes többi művelet nem engedélyezett, amíg az Azure Key vaulthoz való hozzáférést vissza nem állították, és a kiszolgáló elérhetővé válik. Ha az "igen" (az ügyfél által felügyelt) adattitkosítási beállítást "No" (szolgáltatás által felügyelt) értékre módosítja, akkor a kiszolgáló nem érhető el, és az ügyfél által felügyelt kiszolgáló titkosítása is megoldható. A kiszolgáló újbóli érvényesítéséhez manuálisan kell újraérvényesíteni a kulcsot. Erre azért van szükség, hogy megvédje az adatok jogosulatlan hozzáférését, amíg az ügyfél által felügyelt kulcshoz tartozó engedélyeket visszavonták.

## <a name="common-errors-causing-server-to-become-inaccessible"></a>A kiszolgáló elérhetetlenné válását okozó gyakori hibák

A legtöbb olyan problémát, amely akkor fordul elő, amikor a Azure Key Vault adattitkosítást használ a következő hibás konfigurációk egyike okozza:

A kulcstartó nem érhető el, vagy nem létezik.

* A Key vaultot véletlenül törölték.
* Egy időszakos hálózati hiba miatt a kulcstartó nem érhető el.

Nincs engedélye a kulcstartó elérésére, vagy a kulcs nem létezik.

* A kulcs véletlenül törölve lett, le lett tiltva, vagy lejárt a kulcs.
* A Azure Database for MySQL példány által felügyelt identitást véletlenül törölték.
* A kulcsok Azure Database for MySQL kiszolgáló által felügyelt identitásához megadott engedélyek nem elégségesek (nem tartalmazzák a Get, a wrap és a kicsomagolást).
* Az Azure Database for MySQL Server példány által felügyelt identitásra vonatkozó engedélyeket visszavonták.

## <a name="identify-and-resolve-common-errors"></a>Gyakori hibák azonosítása és megoldása
### <a name="errors-on-the-key-vault"></a>Hibák a Key vaultban

#### <a name="disabled-key-vault"></a>A Key Vault letiltva
* AzureKeyVaultKeyDisabledMessage
* **Magyarázat** : a művelet nem hajtható végre a kiszolgálón, mert a Azure Key Vault kulcs le van tiltva.

#### <a name="missing-key-vault-permissions"></a>Hiányzó Key Vault-engedélyek
* AzureKeyVaultMissingPermissionsMessage
* A kiszolgáló nem rendelkezik a szükséges Get, wrap és unwrap engedélyekkel a Azure Key Vault engedélyekhez. Adja meg a hiányzó engedélyeket az egyszerű szolgáltatásnév számára az AZONOSÍTÓval.

### <a name="mitigation"></a>Kezelés
* Győződjön meg arról, hogy az ügyfél által felügyelt kulcs megtalálható a Key Vaultban:
* Azonosítsa a kulcstartót, majd nyissa meg a Azure Portal található kulcstartót.
* Győződjön meg arról, hogy a kulcs URI azonosítóval azonosított kulcs megtalálható.


## <a name="next-steps"></a>Következő lépések
[Állítsa be az adattitkosítást a MySQL-hez készült Azure Database-hez tartozó ügyfél által felügyelt kulccsal a Azure Portal használatával](howto-data-encryption-portal.md).