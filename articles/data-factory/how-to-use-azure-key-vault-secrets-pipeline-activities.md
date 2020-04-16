---
title: Azure Key Vault-beli titkos kulcsok használata a folyamattevékenységekben
description: Megtudhatja, hogyan lehet lekérni a tárolt hitelesítő adatokat az Azure key vaultból, és azokat az adatüzemi folyamatok futtatása során használhatja.
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: f2531ebfd8b1eafc04fa6eda660b0eec3d1147f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417077"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Azure Key Vault-beli titkos kulcsok használata a folyamattevékenységekben

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A hitelesítő adatokat vagy titkos értékeket egy Azure Key Vaultban tárolhatja, és a folyamat végrehajtása során használhatja őket a tevékenységeknek való továbbadáshoz.

## <a name="prerequisites"></a>Előfeltételek

Ez a funkció az adat-előállító felügyelt identitásra támaszkodik.  Ismerje meg, hogyan működik [a Data Factory felügyelt identitásából,](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) és győződjön meg arról, hogy az adatgyárhoz tartozik egy.

## <a name="steps"></a>Lépések

1. Nyissa meg az adat-előállító tulajdonságait, és másolja a felügyelt identitásalkalmazás-azonosító értékét.

    ![Felügyelt identitás értéke](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Nyissa meg a key vault hozzáférési szabályzatokat, és adja hozzá a felügyelt identitás engedélyeket a beszerez és a titkos kulcsok listázása.

    ![Key Vault-hozzáférési házirendek](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Key Vault-hozzáférési házirendek](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Kattintson **a Hozzáadás**gombra, majd a **Mentés gombra.**

3. Nyissa meg a kulcstartó titkos kulcsát, és másolja a titkos azonosítót.

    ![Titkos azonosító](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Jegyezze fel a titkos URI-t, amelyet az adat-üzemi folyamat futtatása során szeretne beszerezni.

4. A Data Factory folyamat, adjon hozzá egy új webes tevékenységet, és konfigurálja azt az alábbiak szerint.  

    |Tulajdonság  |Érték  |
    |---------|---------|
    |Biztonságos kimenet     |True (Igaz)         |
    |URL-cím     |[A titkos URI-érték]?api-version=7.0         |
    |Módszer     |GET         |
    |Hitelesítés     |MSI         |
    |Erőforrás        |https://vault.azure.net       |

    ![Webes tevékenység](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > Hozzá kell adnia **az ?api-version=7.0-t** a titkos URI-hoz.  

    > [!CAUTION]
    > Állítsa a Biztonságos kimenet beállítást true értékre, hogy a titkos érték egyszerű szövegbe kerüljön.  Minden további tevékenységek, amelyek ezt az értéket használó kell rendelkeznie a biztonságos bemeneti beállítás értéke igaz.

5. Az érték másik tevékenységben való használatához használja a következő kódkifejezést ** @activity("Web1").).output.value**.

    ![Kódkifejezés](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>További lépések

Ha meg szeretné tudni, hogyan tárolhatja az Azure Key Vault ot az adattárak és a számítási feladatok hitelesítő adatainak tárolására, olvassa el [az Áruház hitelesítő adatai az Azure Key Vaultban](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
