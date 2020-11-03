---
title: Ajánlott eljárások a Key Vault-Azure Key Vault használatához | Microsoft Docs
description: Ismerje meg a Azure Key Vault ajánlott eljárásait, beleértve a hozzáférés szabályozását, a különböző kulcstartók használatát, a biztonsági mentést, a naplózást és a helyreállítási lehetőségeket.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 4a522e58677e2c71cb52ad7e04b288622ad62d91
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287328"
---
# <a name="best-practices-to-use-key-vault"></a>Ajánlott eljárások a Key Vault használatához

## <a name="control-access-to-your-vault"></a>A tárolóhoz való hozzáférés szabályozása

A Azure Key Vault egy felhőalapú szolgáltatás, amely védelmet biztosít a titkosítási kulcsok és a titkok, például a tanúsítványok, a kapcsolatok karakterláncai és a jelszavak számára. Mivel ezek az adatok érzékenyek és üzleti szempontból kritikus fontosságúak, a kulcstartóhoz való hozzáférést csak a jogosult alkalmazások és felhasználók engedélyezésével kell biztosítani. Ez a [cikk](secure-your-key-vault.md) a Key Vault hozzáférési modell áttekintését tartalmazza. Ismerteti a hitelesítést és az engedélyezést, valamint ismerteti a kulcstartók hozzáférésének biztonságossá tételét.

A tárolóhoz való hozzáférés szabályozása során a következő javaslatok érhetők el:
1. Az előfizetés, az erőforráscsoport és a kulcstartók (RBAC) hozzáférésének zárolása
2. Hozzáférési szabályzatok létrehozása minden tárolóhoz
3. A minimális jogosultság-hozzáférési tag használata hozzáférés biztosításához
4. A tűzfal és a [VNET szolgáltatás-végpontok](overview-vnet-service-endpoints.md) bekapcsolása

## <a name="use-separate-key-vault"></a>Külön Key Vault használata

Javasoljuk, hogy a tárolót egy alkalmazás/környezet (fejlesztés, üzem előtti és éles üzem) alapján használja. Ez segít abban, hogy ne ossza meg a titkokat a környezetek között, és a fenyegetést is csökkenti a szabálysértés esetén.

## <a name="backup"></a>Backup

Győződjön meg arról, hogy a tárolóban rendszeresen készít biztonsági másolatot a tárolóban lévő objektumok frissítéséről/törléséről/létrehozásáról.

### <a name="azure-powershell-backup-commands"></a>Azure PowerShell biztonsági mentési parancsok

* [Biztonsági mentési tanúsítvány](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate?view=azurermps-6.13.0)
* [Biztonsági mentési kulcs](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey?view=azurermps-6.13.0)
* [Biztonsági másolat titka](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret?view=azurermps-6.13.0)

### <a name="azure-cli-backup-commands"></a>Azure CLI biztonsági mentési parancsok

* [Biztonsági mentési tanúsítvány](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-backup)
* [Biztonsági mentési kulcs](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-backup)
* [Biztonsági másolat titka](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-backup)


## <a name="turn-on-logging"></a>Naplózás bekapcsolása

A [naplózás bekapcsolása](logging.md) a tárolóban. Riasztásokat is beállíthat.

## <a name="turn-on-recovery-options"></a>Helyreállítási beállítások bekapcsolása

1. A helyreállítható [Törlés](soft-delete-overview.md)bekapcsolása.
2. Ha azt szeretné, hogy a rendszer a törlés után is bekapcsolja a titkos kulcs vagy tár kényszerített törlését, kapcsolja be a védelem kiürítését.