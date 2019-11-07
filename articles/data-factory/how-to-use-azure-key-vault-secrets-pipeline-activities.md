---
title: Azure Key Vault titkok használata a folyamat tevékenységeiben
description: Ismerje meg, hogyan hívhat le tárolt hitelesítő adatokat az Azure Key vaultból, és hogyan használhatja őket a adat-előállító folyamatának futtatása közben.
services: data-factory
author: ChrisLound
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: 83d0981a0d277eab1aae8654343ab34661b3e88b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672895"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Azure Key Vault titkok használata a folyamat tevékenységeiben

A hitelesítő adatokat vagy a titkos értékeket egy Azure Key Vault tárolhatja, és a folyamat végrehajtása során felhasználhatja a tevékenységeket.

## <a name="prerequisites"></a>Előfeltételek

Ez a funkció az adatok gyári felügyelt identitására támaszkodik.  Ismerje meg, hogyan működik az [Data Factory felügyelt identitása](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) , és győződjön meg arról, hogy az adatok előállítója társítva van.

## <a name="steps"></a>Lépések

1. Nyissa meg az adatok előállítójának tulajdonságait, és másolja a felügyelt Identity Application ID értéket.

    ![Felügyelt identitás értéke](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Nyissa meg a Key Vault hozzáférési házirendjét, és adja hozzá a felügyelt identitás engedélyeit a titkos kulcsok lekéréséhez és listázásához.

    ![Key Vault hozzáférési szabályzatok](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Key Vault hozzáférési szabályzatok](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Kattintson a **Hozzáadás**, majd a **Mentés**gombra.

3. Navigáljon a Key Vault titkára, és másolja a titkos azonosítót.

    ![Titkos azonosító](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Jegyezze fel a titkos URI-t, amelyet az adatfeldolgozó-folyamat futtatásakor szeretne kapni.

4. A Data Factory folyamatában adjon hozzá egy új webes tevékenységet, és konfigurálja az alábbiak szerint.  

    |Tulajdonság  |Érték  |
    |---------|---------|
    |Biztonságos kimenet     |True (Igaz)         |
    |URL-cím     |[A titkos URI-érték]? API-Version = 7.0         |
    |Módszer     |GET         |
    |Authentication     |MSI         |
    |Erőforrás        |https://vault.azure.net       |

    ![Webes tevékenység](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > Hozzá kell adnia a **? API-Version = 7.0** értéket a titkos URI végéhez.  

    > [!CAUTION]
    > Állítsa a Secure output (biztonságos kimenet) beállítást igaz értékre, hogy megakadályozza a titkos érték egyszerű szövegként való naplózását.  Az értéket használó további tevékenységeknek a biztonságos bemenet beállítását True értékre kell állítani.

5. Ha egy másik tevékenységben szeretné használni az értéket, használja a következő kódú kifejezést **@activity("web"). output. Value)** .

    ![Kód kifejezése](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>További lépések

Az adattárak és a számítási adatok hitelesítő adatainak tárolásához Azure Key Vault használatáról további információt a [hitelesítő adatok tárolása a Azure Key Vault-ben](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) című témakörben talál.
