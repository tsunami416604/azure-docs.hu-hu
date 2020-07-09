---
title: Azure Key Vault biztonsági mentés | Microsoft Docs
description: Ez a dokumentum segít a Azure Key Vault tárolt titkos kulcs, kulcs vagy tanúsítvány biztonsági mentésében.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: sudbalas
ms.openlocfilehash: 8a152e2771f0b207e81f42c6ecae2e4d14605051
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147811"
---
# <a name="azure-key-vault-backup"></a>Azure Key Vault biztonsági mentés

Ebből a dokumentumból megtudhatja, hogyan végezheti el a kulcstartóban tárolt egyéni titkok, kulcsok és tanúsítványok biztonsági mentését. Ez a biztonsági másolat arra szolgál, hogy az összes titkát offline másolattal lássa el abban a valószínűtlen esetben, ha elveszti a kulcstartóhoz való hozzáférést.

## <a name="overview"></a>Áttekintés

A Key Vault automatikusan számos funkciót biztosít a rendelkezésre állás fenntartásához és az adatvesztés elkerüléséhez. Ezt a biztonsági mentést csak akkor kell megkísérelni, ha kritikus üzleti indoklással rendelkezik a titkos kulcsok biztonsági mentésének fenntartásához. Előfordulhat, hogy a kulcstartóban lévő titkos kulcsok biztonsági mentése további működési kihívásokat jelent, például több napló, engedély és biztonsági mentés fenntartása, ha a titkok lejárnak vagy forognak.

Key Vault megtartja a rendelkezésre állást a katasztrófa-forgatókönyvekben, és a felhasználók beavatkozása nélkül automatikusan átadja a kérelmeket a párosított régiónak. További információkért tekintse meg a következő hivatkozást. [Azure Key Vault vész-helyreállítás](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)

Key Vault megvédi a titkos kódok véletlen és rosszindulatú törlését a helyreállítható törlési és törlési védelemmel. Ha a titkos kódok véletlen vagy rosszindulatú törlésével szembeni védelmet szeretne védeni, konfigurálja a Key vaulton a helyreállítható törlési és kiürítő védelmi funkciókat. További információkért tekintse meg a következő dokumentumot. [Azure Key Vault helyreállítás](https://docs.microsoft.com/azure/key-vault/general/overview-soft-delete)

## <a name="limitations"></a>Korlátozások

A Azure Key Vault jelenleg nem támogatja egy teljes kulcstartó biztonsági mentését egyetlen művelettel. A Microsoft vagy a Azure Key Vault csapat nem támogatja az ebben a dokumentumban felsorolt parancsok használatát a Key Vault automatikus biztonsági mentésének elvégzéséhez.

Ha az alábbi dokumentumban látható parancsokat szeretné használni az egyéni automatizálás létrehozásához, hibákhoz vezethet.

* A titkos kulcsok több verzióval történő biztonsági mentése időtúllépési hibát okozhat.
* A Backup egy időponthoz tartozó pillanatképet hoz létre. A titkos kulcsok biztonsági mentése során megújíthatja a titkos kulcsokat.
* A Key Vault szolgáltatási korlátait a másodpercenkénti kérelmeknél nagyobb mértékben korlátozza a kulcstartó szabályozása, ami miatt a biztonsági mentés sikertelen lesz.

## <a name="design-considerations"></a>Kialakítási szempontok

Ha a Key vaultban (Secret, Key vagy Certificate) tárolt objektumról készít biztonsági másolatot, a biztonsági mentési művelet titkosított blobként tölti le az objektumot. Ez a blob nem fejthető vissza az Azure-on kívül. Ahhoz, hogy a blob használható legyen, vissza kell állítania a blobot az Azure-előfizetésben és az Azure-földrajzban található kulcstartóba.
[Azure földrajzi területek](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="prerequisites"></a>Előfeltételek

* Közreműködő vagy magasabb szintű engedélyek egy Azure-előfizetéshez
* A biztonsági mentéshez használni kívánt titkokat tartalmazó elsődleges kulcstartó
* Másodlagos kulcstartó, ahol a titkos kulcsok vissza lesznek állítva.

## <a name="back-up-and-restore-with-azure-portal"></a>Biztonsági mentés és visszaállítás Azure Portal

### <a name="back-up"></a>Biztonsági mentés

1. Lépjen az Azure Portalra.
2. Válassza ki a kulcstartót.
3. Navigáljon arra az objektumra (titkos kulcs, kulcs vagy tanúsítvány), amelyről biztonsági másolatot szeretne készíteni.

    ![Kép](../media/backup-1.png)

4. Válassza ki az objektumot.
5. Válassza a "biztonsági másolat letöltése" lehetőséget

    ![Kép](../media/backup-2.png)
    
6. Kattintson a letöltés gombra.

    ![Kép](../media/backup-3.png)
    
7. Tárolja a titkosított blobot biztonságos helyen.

### <a name="restore"></a>Visszaállítás

1. Lépjen az Azure Portalra.
2. Válassza ki a kulcstartót.
3. Navigáljon a visszaállítani kívánt objektum (titok, kulcs vagy tanúsítvány) típusára.
4. A biztonsági másolat visszaállítása lehetőség kiválasztása

    ![Kép](../media/backup-4.png)
    
5. Keresse meg a helyet, ahová a titkosított blobot tárolta.
6. Válassza az OK gombot.

## <a name="back-up-and-restore-with-the-azure-cli"></a>Biztonsági mentés és visszaállítás az Azure CLI-vel

```azurecli
## Login To Azure
az login

## Set your Subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a Provider
az provider register -n Microsoft.KeyVault

## Backup a Certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

```

## <a name="next-steps"></a>További lépések

A Azure Key Vault naplózásának és figyelésének bekapcsolása. [Azure Key Vault naplózás](https://docs.microsoft.com/azure/key-vault/general/logging)
