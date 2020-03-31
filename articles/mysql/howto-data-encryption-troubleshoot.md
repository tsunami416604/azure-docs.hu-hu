---
title: Adattitkosítás – Azure Database for MySQL
description: Megtudhatja, hogyan háríthatja el az adattitkosítást az Azure Database for MySQL szolgáltatásban
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 42956d115590fd322d2851fd546c505a76a851fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297040"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-mysql"></a>Adattitkosítás – hiba az Azure Database for MySQL szolgáltatásban

Ez a cikk ismerteti, hogyan azonosíthatja és oldhatja meg az okat, amelyek a MySQL Azure-adatbázisában fordulhatnak elő, ha ügyfél által felügyelt kulccsal konfigurálva vannak az adattitkosítással.

## <a name="introduction"></a>Bevezetés

Ha úgy konfigurálja az adattitkosítást, hogy ügyfél által felügyelt kulcsot használjon az Azure Key Vaultban, a kiszolgálók nak folyamatos hozzáférésre van szükségük a kulcshoz. Ha a kiszolgáló elveszíti a hozzáférést az ügyfél által felügyelt kulcshoz az Azure Key Vaultban, megtagadja az összes kapcsolatot, visszaadja a megfelelő hibaüzenetet, és az állapotát ***nem érhető el*** az Azure Portalon.

Ha már nincs szüksége egy nem elérhető Azure-adatbázis a MySQL-kiszolgáló, törölheti azt, hogy ne merüljenek fel a költségek. A kiszolgálón nincs más művelet, amíg a kulcstartóhoz való hozzáférés helyre nem áll, és a kiszolgáló elérhetővé nem válik. Az adattitkosítási lehetőség nem módosítható `Yes`(ügyfél által kezelt) `No` és (szolgáltatás által felügyelt) egy elérhetetlen kiszolgálón, ha az ügyfél által felügyelt kulccsal van titkosítva. A kiszolgáló újbóli elérhetővé tétele előtt manuálisan kell újraérvényesítenie a kulcsot. Ez a művelet azért szükséges, hogy megvédje az adatokat a jogosulatlan hozzáféréstől, miközben az ügyfél által felügyelt kulcsra vonatkozó engedélyeket visszavonják.

## <a name="common-errors-that-cause-the-server-to-become-inaccessible"></a>Gyakori hibák, amelyek a kiszolgáló elérhetetlenné válását okozzák

A következő helytelen konfigurációk okozzák a legtöbb problémát az Azure Key Vault-kulcsokat használó adattitkosítással kapcsolatban:

- A kulcstartó nem érhető el, vagy nem létezik:
  - A kulcstartó véletlenül törölve lett.
  - Egy időszakos hálózati hiba miatt a key vault nem érhető el.

- Nincs engedélye a kulcstartó eléréséhez, vagy a kulcs nem létezik:
  - A kulcs lejárt, vagy véletlenül törölték vagy letiltották.
  - A MySQL-példányhoz készült Azure Database felügyelt identitása véletlenül törlődött.
  - Az Azure Database for MySQL-példány felügyelt identitása nem rendelkezik megfelelő kulcsengedélyekkel. Az engedélyek például nem tartalmazzák a Beget, a Körbefuttatást és a Kicsomagolást.
  - Az Azure Database for MySQL-példány felügyelt identitásengedélyeit visszavonták vagy törölték.

## <a name="identify-and-resolve-common-errors"></a>Gyakori hibák azonosítása és megoldása

### <a name="errors-on-the-key-vault"></a>Hibák a key vaultban

#### <a name="disabled-key-vault"></a>Letiltott kulcstartó

- `AzureKeyVaultKeyDisabledMessage`
- **Magyarázat:** A művelet nem hajtható végre a kiszolgálón, mert az Azure Key Vault kulcs le van tiltva.

#### <a name="missing-key-vault-permissions"></a>Hiányzó kulcstartó engedélyek

- `AzureKeyVaultMissingPermissionsMessage`
- **Magyarázat:** A kiszolgáló nem rendelkezik a szükséges Get, Wrap és Unwrap engedélyekkel az Azure Key Vaulthoz. Adja meg a hiányzó engedélyeket az azonosítóval rendelkező egyszerű szolgáltatásnak.

### <a name="mitigation"></a>Kezelés

- Győződjön meg arról, hogy az ügyfél által felügyelt kulcs a key vaultban található.
- Azonosítsa a key vault, majd nyissa meg a key vault az Azure Portalon.
- Győződjön meg arról, hogy a kulcs URI azonosítja a kulcs, amely jelen van.

## <a name="next-steps"></a>További lépések

[Az Azure Portal használatával adattitkosítást állíthat be egy ügyfél által felügyelt kulccsal a MySQL Azure Database-ben](howto-data-encryption-portal.md)
