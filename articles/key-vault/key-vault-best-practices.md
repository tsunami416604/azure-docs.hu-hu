---
title: Ajánlott eljárások a Key Vault-Azure Key Vault használatához | Microsoft Docs
description: Ez a dokumentum a Key Vault használatának ajánlott eljárásait ismerteti.
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184827"
---
# <a name="best-practices-to-use-key-vault"></a>Ajánlott eljárások a Key Vault használatához

## <a name="control-access-to-your-vault"></a>A tárolóhoz való hozzáférés szabályozása

A Azure Key Vault egy felhőalapú szolgáltatás, amely védelmet biztosít a titkosítási kulcsok és a titkok, például a tanúsítványok, a kapcsolatok karakterláncai és a jelszavak számára. Mivel ezek az adatok érzékenyek és üzleti szempontból kritikus fontosságúak, a kulcstartóhoz való hozzáférést csak a jogosult alkalmazások és felhasználók engedélyezésével kell biztosítani. Ez a [cikk](key-vault-secure-your-key-vault.md) a Key Vault hozzáférési modell áttekintését tartalmazza. Ismerteti a hitelesítést és az engedélyezést, valamint ismerteti a kulcstartók hozzáférésének biztonságossá tételét.

A tárolóhoz való hozzáférés szabályozása során a következő javaslatok érhetők el:
1. Az előfizetés, az erőforráscsoport és a kulcstartók (RBAC) hozzáférésének zárolása
2. Hozzáférési szabályzatok létrehozása minden tárolóhoz
3. A minimális jogosultság-hozzáférési tag használata hozzáférés biztosításához
4. A tűzfal és a [VNET szolgáltatás-végpontok](key-vault-overview-vnet-service-endpoints.md) bekapcsolása

## <a name="use-separate-key-vault"></a>Külön Key Vault használata

Javasoljuk, hogy a tárolót egy alkalmazás/környezet (fejlesztés, üzem előtti és éles üzem) alapján használja. Ez segít abban, hogy ne ossza meg a titkokat a környezetek között, és a fenyegetést is csökkenti a szabálysértés esetén.

## <a name="backup"></a>Biztonsági mentés

Győződjön meg arról, hogy a tárolóban rendszeresen készít biztonsági [másolatot a](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) tárolóban lévő objektumok frissítéséről/törléséről/létrehozásáról.

## <a name="turn-on-logging"></a>Naplózás bekapcsolása

A [naplózás bekapcsolása](key-vault-logging.md) a tárolóban. Riasztásokat is beállíthat.

## <a name="turn-on-recovery-options"></a>Helyreállítási beállítások bekapcsolása

1. A helyreállítható [Törlés](key-vault-ovw-soft-delete.md)bekapcsolása.
2. Ha be szeretné állítani a védelem kiürítését, akkor is be kell kapcsolni a védelmet, ha a törlés után is be van kapcsolva.
