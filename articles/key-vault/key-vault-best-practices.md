---
title: Ajánlott eljárások az Azure Key Vault használata a Key Vault - |} A Microsoft Docs
description: Ez a dokumentum azt ismerteti, néhány ajánlott eljárást a Key vaulttal
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-key-vault
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: eb7150d0b1c3a4a312b0c05ba7612960aaf640f6
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65227932"
---
# <a name="best-practices-to-use-key-vault"></a>A Key Vault használatának ajánlott eljárásait

## <a name="control-access-to-your-vault"></a>A tároló hozzáférés-vezérlése

Az Azure Key Vault egy felhőszolgáltatás, amely megvédi a titkosítási kulcsokat és titkokat – például a tanúsítványok, kapcsolati karakterláncok és jelszavak. Mivel ezek az adatok bizalmas és kritikus fontosságú üzleti szeretné megvédeni a kulcstartókhoz azáltal, hogy csak az engedélyezett alkalmazások és felhasználók. Ez [cikk](key-vault-secure-your-key-vault.md) nyújt áttekintést a Key Vault hozzáférés modell. Hitelesítését és engedélyezését ismerteti és bemutatja, hogyan teheti biztonságossá a kulcstartók való hozzáférést.

A tároló hozzáférésének ellenőrzése közben a javaslatok a következők:
1. Az előfizetés, erőforráscsoport és a kulcs tárolók (RBAC) való hozzáférés zárolása
2. Hozzáférési szabályzatok minden tároló létrehozása
3. Használja a legalacsonyabb jogosultsági hozzáférés egyszerű hozzáférést
4. A tűzfal bekapcsolása és [virtuális hálózati Szolgáltatásvégpontok](key-vault-overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>Külön a Key Vault használata

Azt javasoljuk, hogy a környezetben (fejlesztői, éles üzem előtti és éles környezetek) alkalmazásonként egy tárolót. Ez segít a titkos kulcsok ossza meg környezetekben, és csökkenti a fenyegetés megsértése esetén is.

## <a name="backup"></a>Backup

Győződjön meg arról is regular vissza a ups a [tároló](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) az update/delete/hozzon létre egy tárolóban található objektumok.

## <a name="turn-on-logging"></a>Naplózás bekapcsolása

[Kapcsolja be a naplózást](key-vault-logging.md) a tároló számára. Emellett riasztásokat állíthat be.

## <a name="turn-on-recovery-options"></a>Kapcsolja be a helyreállítási lehetőségek

1. Kapcsolja be a [helyreállítható törlési](key-vault-ovw-soft-delete.md).
2. Kapcsolja be a végleges törlés elleni védelem, ha azt szeretné, a titkos kulcs kényszerített törlése ellen őr / tároló akkor is helyreállítható törlési be van kapcsolva.
