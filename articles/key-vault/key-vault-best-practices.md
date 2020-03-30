---
title: Gyakorlati tanácsok a Key Vault használatához – Azure Key Vault | Microsoft dokumentumok
description: Ez a dokumentum ismerteti a Key Vault használatának néhány bevált gyakorlati tanácsát
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: cb4bec8170466f0fc667b592d44b0858c41ccd84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270991"
---
# <a name="best-practices-to-use-key-vault"></a>Gyakorlati tanácsok a Key Vault használatához

## <a name="control-access-to-your-vault"></a>Hozzáférés szabályozása a trezorhoz

Az Azure Key Vault egy felhőalapú szolgáltatás, amely védi a titkosítási kulcsokat és a titkos kulcsokat, például a tanúsítványokat, a kapcsolati karakterláncokat és a jelszavakat. Mivel ezek az adatok bizalmas és üzleti szempontból kritikus fontosságúak, csak az engedélyezett alkalmazások és felhasználók engedélyezésével kell biztonságos hozzáférést biztosítania a kulcstartókhoz. Ez a [cikk](key-vault-secure-your-key-vault.md) áttekintést nyújt a Key Vault hozzáférési modell. Ismerteti a hitelesítést és az engedélyezést, és ismerteti, hogyan biztosíthatja a hozzáférést a kulcstartókhoz.

A trezorhoz való hozzáférés szabályozása során tett javaslatok a következők:
1. Az előfizetéshez, az erőforráscsoporthoz és a kulcstárolókhoz (RBAC) való hozzáférés zárolása
2. Access-házirendek létrehozása minden tárolóhoz
3. A hozzáférés biztosításához a legkisebb jogosultsági hozzáférés egyszerű használata
4. Tűzfal- és [vnet-szolgáltatásvégpontok bekapcsolása](key-vault-overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>Külön kulcstartó használata

Javasoljuk, hogy egy boltozat alkalmazásonként környezetenként (fejlesztés, elő-termelés és éles). Ez segít, hogy ne ossza meg a titkokat a környezetek között, és csökkenti a fenyegetést megsértése esetén.

## <a name="backup"></a>Backup

Győződjön meg róla, hogy rendszeres biztonsági másolatot készít a [tárolófrissítés](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) /törlés/létrehozása objektumok egy vault.

## <a name="turn-on-logging"></a>Naplózás bekapcsolása

[Kapcsolja be a trezor naplózását.](key-vault-logging.md) Riasztásokat is állíthat be.

## <a name="turn-on-recovery-options"></a>Helyreállítási beállítások bekapcsolása

1. Kapcsolja be a [helyreállítható törlést](key-vault-ovw-soft-delete.md).
2. Kapcsolja be a tisztítási védelmet, ha a titkos / trezor erő törlésével szemben szeretne védekezni, még a lágy törlés bekapcsolása után is.
