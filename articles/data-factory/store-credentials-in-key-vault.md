---
title: Hitelesítő adatok Store az Azure Key Vault |} A Microsoft Docs
description: Megtudhatja, hogyan tárolhatjuk a hitelesítő adatokat egy Azure key vault, amely az Azure Data Factory automatikusan lekérheti futásidőben használt adattárakban.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: ff070adbda2a36261ca24eb0cc993ca22eada1c7
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55661241"
---
# <a name="store-credential-in-azure-key-vault"></a>Hitelesítő adatok Store az Azure Key Vaultban

Az adattárak és számítási erőforrások, a hitelesítő adatokat tárolhatja egy [Azure Key Vault](../key-vault/key-vault-whatis.md). Az Azure Data Factory kérdezi le a hitelesítő adatokat, az adatok adattárat/számítási használó tevékenység végrehajtása közben.

Egyéni tevékenység kivételével minden tevékenység típus jelenleg ez a funkció támogatja. Az összekötő-konfiguráció, ellenőrizze a "társított szolgáltatások tulajdonságai" szakaszban [minden összekötő témakör](copy-activity-overview.md#supported-data-stores-and-formats) részleteiről.

## <a name="prerequisites"></a>Előfeltételek

Ez a funkció a data factory-szolgáltatásidentitás támaszkodik. Megtudhatja, hogyan használható a [Data factory-szolgáltatásidentitás](data-factory-service-identity.md) , és győződjön meg, hogy az adat-előállítóhoz társított egy.

## <a name="steps"></a>Lépések

Az Azure Key Vaultban tárolt hitelesítő adatokat, hivatkozni kell tennie:

1. **Beolvasni a data factory-szolgáltatásidentitás** másolásával "SZOLGÁLTATÁSIDENTITÁS Alkalmazásazonosítója" az előállító együtt létrehozott értékét. Ha az ADF-létrehozási felhasználói felületen, a felügyeltszolgáltatás-identitás azonosítója megjelenik az Azure Key Vault társított szolgáltatás létrehozása ablak; is lekérheti az Azure Portalról, tekintse meg [beolvasni a data factory-szolgáltatásidentitás](data-factory-service-identity.md#retrieve-service-identity).
2. **A szolgáltatás identitás-hozzáférési jogot az Azure Key Vaultban.** A kulcstartó-hozzáférési házirendek -> > Új -> keresési megadását azonosító a szolgáltatásalkalmazás-identitás hozzáadása **első** engedély titkos kód engedélyei legördülő listában. Lehetővé teszi a kijelölt factory eléréséhez a key vaultban titkos kulcsot.
3. **Az Azure Key Vaultban mutató társított szolgáltatás létrehozása.** Tekintse meg [Azure Key Vault-beli társított szolgáltatást](#azure-key-vault-linked-service).
4. **Adattár társított szolgáltatását, belül mely hivatkozás a megfelelő titkos tárolja a kulcsot tároló létrehozása.** Tekintse meg [referencia titkos kulcsot a key vaultban tárolt](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Az Azure Key Vault-beli társított szolgáltatás

Társított Azure Key Vault szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **AzureKeyVault**. | Igen |
| baseUrl | Adja meg az Azure Key Vault URL-címet. | Igen |

**Használatával a szerzői műveletek a felhasználói felület:**

Kattintson a **kapcsolatok** -> **társított szolgáltatások** -> **+ új** -> "Az Azure Key Vault" keresése:

![Keresés AKV](media/store-credentials-in-key-vault/search-akv.png)

Válassza ki az üzembe helyezett Azure Key Vault, a hitelesítő adatok tárolására. Teheti **kapcsolat tesztelése** , győződjön meg arról, hogy az AKV kapcsolat érvényességét. 

![AKV konfigurálása](media/store-credentials-in-key-vault/configure-akv.png)

**JSON-példa:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>A key vaultban tárolt referencia titkos kulcs

A következő tulajdonságok támogatottak a key vault titkos társított szolgáltatás mező konfigurálásakor:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonság, mező értékre kell állítani: **AzureKeyVaultSecret**. | Igen |
| secretName | Az azure key vault titkos neve. | Igen |
| secretVersion | Az azure key vaultban titkos kód verziója.<br/>Ha nincs megadva, mindig használja a titkos kulcs legújabb verzióját.<br/>Ha meg van adva, majd azt csatlakoztat az adott verzióra.| Nem |
| Store | Hivatkozik egy társított Azure Key Vault szolgáltatás, amellyel a hitelesítő adatok tárolásához. | Igen |

**Használatával a szerzői műveletek a felhasználói felület:**

Válassza ki **Azure Key Vault** titkos mezők az adatok adattárat/számítási kapcsolat létrehozása során. Válassza ki az üzembe helyezett Azure Key Vault társított szolgáltatásban, és adja meg a **název tajného Kódu**. Opcionálisan megadhat egy titkos kód verziója. 

![Konfigurálja az AKV titkos kulcs](media/store-credentials-in-key-vault/configure-akv-secret.png)

**JSON-példa: (lásd a "password" szakaszt)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
