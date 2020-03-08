---
title: Azure Database for MySQL adattitkosításának hibáinak megoldása
description: Ismerje meg, hogyan lehet elhárítani az adattitkosítást Azure Database for MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 516f0b2080fc894ec00f222c712ffdea4ee74356
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851101"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-mysql"></a>Azure Database for MySQL adattitkosításának hibáinak megoldása

Ez a cikk azt ismerteti, hogyan azonosíthatók és oldhatók meg a Azure Database for MySQLben előforduló gyakori problémák, ha az ügyfél által felügyelt kulcs használatával van konfigurálva adattitkosítással.

## <a name="introduction"></a>Bevezetés

Ha úgy konfigurálja az adattitkosítást, hogy Azure Key Vault ügyfél által felügyelt kulcsot használjon, a kiszolgálók folyamatos hozzáférést igényelnek a kulcshoz. Ha a kiszolgáló nem fér hozzá az ügyfél által felügyelt kulcshoz Azure Key Vault-ben, az megtagadja az összes kapcsolatot, visszaadja a megfelelő hibaüzenetet, és úgy módosítja az állapotát, hogy az nem ***érhető*** el a Azure Portal.

Ha már nincs szüksége egy nem elérhető Azure Database for MySQL-kiszolgálóra, törölheti a költségeket. A kiszolgálón semmilyen más művelet nem engedélyezett, amíg a kulcstartóhoz való hozzáférés vissza nem áll, és a kiszolgáló elérhető. A `Yes`(ügyfél által felügyelt) adattitkosítási lehetőség nem módosítható a nem elérhető kiszolgálókon `No` (szolgáltatás által felügyelt) értékre, ha az ügyfél által felügyelt kulccsal van titkosítva. A kulcs ismételt érvényesítéséhez manuálisan kell újraérvényesíteni a kiszolgálót, mielőtt újra elérhetővé válik. Ez a művelet szükséges az adatok jogosulatlan hozzáférés elleni védelme érdekében, az ügyfél által felügyelt kulcs engedélyeinek visszavonása mellett.

## <a name="common-errors-that-cause-the-server-to-become-inaccessible"></a>Gyakori hibák, amelyek miatt a kiszolgáló elérhetetlenné válik

A következő helytelen konfiguráció a Azure Key Vault kulcsokat használó adattitkosítással kapcsolatos legtöbb problémát okoz:

- A Key Vault nem érhető el, vagy nem létezik:
  - A Key vaultot véletlenül törölték.
  - Egy időszakos hálózati hiba miatt a kulcstartó nem érhető el.

- Nincs engedélye a kulcstartó elérésére, vagy a kulcs nem létezik:
  - A kulcs lejárt, vagy véletlenül törölték vagy letiltották.
  - A Azure Database for MySQL példány felügyelt identitása véletlenül törölve lett.
  - A Azure Database for MySQL példány felügyelt identitása nem rendelkezik elegendő jogosultsággal. Az engedélyek például nem tartalmazzák a Get, a wrap és a kicsomagolás funkciót.
  - A Azure Database for MySQL példány felügyelt identitására vonatkozó engedélyeket visszavonták vagy törölték.

## <a name="identify-and-resolve-common-errors"></a>Gyakori hibák azonosítása és megoldása

### <a name="errors-on-the-key-vault"></a>Hibák a Key vaultban

#### <a name="disabled-key-vault"></a>A Key Vault letiltva

- `AzureKeyVaultKeyDisabledMessage`
- **Magyarázat**: a művelet nem fejeződött be a kiszolgálón, mert a Azure Key Vault kulcs le van tiltva.

#### <a name="missing-key-vault-permissions"></a>Hiányzó Key Vault-engedélyek

- `AzureKeyVaultMissingPermissionsMessage`
- **Magyarázat**: a kiszolgáló nem rendelkezik a szükséges Get, wrap és unwrap engedélyekkel a Azure Key Vaulthoz. Adja meg a hiányzó engedélyeket az egyszerű szolgáltatásnév számára az AZONOSÍTÓval.

### <a name="mitigation"></a>Kezelés

- Győződjön meg arról, hogy az ügyfél által felügyelt kulcs megtalálható a kulcstartóban.
- Azonosítsa a kulcstartót, majd nyissa meg a Azure Portal található kulcstartót.
- Győződjön meg arról, hogy a kulcs URI-ja egy jelen lévő kulcsot azonosít.

## <a name="next-steps"></a>További lépések

[Az Azure Portal használatával állíthatja be az adattitkosítást az ügyfél által felügyelt kulccsal Azure Database for MySQL](howto-data-encryption-portal.md)
