---
title: A tár másik régióba való áthelyezése Azure Key Vault | Microsoft Docs
description: Útmutató a kulcstartó más régióba való áthelyezéséhez.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another region.
ms.openlocfilehash: e65a723ac9daafdc09896a50e197034104408df2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254147"
---
# <a name="moving-an-azure-key-vault-across-regions"></a>Azure Key Vault áthelyezése régiók között

## <a name="overview"></a>Áttekintés

A Key Vault nem támogatja az erőforrás-áthelyezési műveletet, amely lehetővé teszi a kulcstartó áthelyezését egy másik régióba. Ez a cikk a megkerülő megoldásokat ismerteti, ha a kulcstartót egy másik régióba kell áthelyeznie. Az egyes lehetőségek korlátozásokkal rendelkeznek, és fontos megérteni a megkerülő megoldások következményeit, mielőtt éles környezetben kísérlik meg őket.

Ha egy kulcstartót egy másik régióba kell áthelyeznie, a megoldás egy új kulcstartó létrehozása a kívánt régióban, és manuális másolás a meglévő kulcstartóból az új kulcstartóba. Ezt a műveletet az alábbi módokon végezheti el.

## <a name="design-considerations"></a>Kialakítási szempontok

* A Key Vault nevek globálisan egyediek. A tár neve nem használható fel újra.

* Az új kulcstartóban újra kell konfigurálnia a hozzáférési házirendeket és a hálózati konfigurációs beállításokat.

* Az új kulcstartóban újra kell konfigurálnia a Soft-delete és a kiürítési védelmet.

* A biztonsági mentés és visszaállítás művelet nem őrzi meg a beállítások újrakonfigurálásához szükséges beállításokat.

## <a name="option-1---use-the-key-vault-backup-and-restore-commands"></a>1. lehetőség – a Key Vault biztonsági mentési és visszaállítási parancsainak használata

A biztonsági mentési parancs használatával biztonsági mentést készíthet a tárolóban lévő egyes titkos kulcsokról, kulcsokról és tanúsítványokról. A titkos kódok titkosított blobként lesznek letöltve. Ezután visszaállíthatja a blobot az új kulcstartóba. A parancsokat az alábbi hivatkozásra kattintva dokumentáljuk.

[Azure Key Vault parancsok](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault)

### <a name="limitations"></a>Korlátozások

* Egy kulcstartóban nem lehet biztonsági másolatot készíteni egy adott földrajzi helyen, és visszaállíthatja egy másik földrajzi helyre. További információ az Azure földrajzi területekről. [Hivatkozás](https://azure.microsoft.com/global-infrastructure/geographies/)

* A Backup parancs az egyes titkos kódok összes verzióját biztonsági másolatot készít. Ha nagy számú korábbi verzióval rendelkezik (több mint 10), akkor a kérés túllépi az engedélyezett maximális méretet, és a művelet sikertelen lehet.

## <a name="option-2---manually-download-and-upload-secrets"></a>2. lehetőség – titkok manuális letöltése és feltöltése

Bizonyos titkos típusok manuálisan is letölthetők. Például letöltheti a tanúsítványokat. pfx-fájlként. Ez a beállítás kiküszöböli a földrajzi korlátozásokat bizonyos titkos típusok, például tanúsítványok esetén. Bármely régióban bármely kulcstartóba feltöltheti a. pfx-fájlokat. A titkos kód nem jelszóval védett formátumban lesz letöltve. Ha elhagyják az áthelyezést, az Ön feladata a titkok védelme Key Vault.
