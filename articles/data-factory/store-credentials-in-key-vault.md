---
title: Hitelesítő adatok tárolása az Azure Key Vault |} Microsoft Docs
description: Útmutató adatok tárolóinak egy, az Azure Data Factory automatikusan, futásidőben kérheti le az Azure key vault használt hitelesítő adatok tárolására.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: jingwang
ms.openlocfilehash: f12c3a2a1193adc08c408292a4e0eca2e2458085
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620666"
---
# <a name="store-credential-in-azure-key-vault"></a>Hitelesítő adatok tárolása az Azure Key Vault

Az adattároló, és kiszámítja a hitelesítő adatok tárolhatók egy [Azure Key Vault](../key-vault/key-vault-whatis.md). Az Azure Data Factory lekéri a hitelesítő adatokat, használja az adatok tárolási/számítási tevékenység végrehajtása közben.

Jelenleg az egyéni tevékenység kivételével az összes tevékenységtípus támogatja ezt a szolgáltatást. -Összekötő konfigurációjában, ellenőrizze a "társított szolgáltatás tulajdonságai" szakaszban [minden összekötő témakör](copy-activity-overview.md#supported-data-stores-and-formats) részleteiről.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [adat-előállító version1 dokumentációja](v1/data-factory-introduction.md).

## <a name="prerequisites"></a>Előfeltételek

Ez a funkció a data factory szolgáltatásidentitás támaszkodik. Ismerje meg, hogyan működik a [Data factory szolgáltatásidentitás](data-factory-service-identity.md) , hogy a data factory van társítva egy.

>[!TIP]
>Az Azure Key Vault, amikor létrehoz egy titkos kulcsot **(pl. kapcsolati karakterlánc-jelszóváltozás-szolgáltatás egyszerű kulcs/stb.) kéri, hogy az ADF kapcsolt szolgáltatás titkos tulajdonság teljes értékének put**. Például az Azure Storage társított szolgáltatás put `DefaultEndpointsProtocol=http;AccountName=myAccount;AccountKey=myKey;` AKV titkos kulcsot, akkor a hivatkozás "connectionString" mezőben ADF; Dynamics kapcsolódó szolgáltatás, mint a put `myPassword` AKV titkos kulcsként majd hivatkozik az ADF "jelszó" mezőt. Tekintse meg a minden összekötő/számítási cikk a támogatott tulajdonság részletek.

## <a name="steps"></a>Lépések

Az Azure Key Vault tárolt hitelesítő adatok hivatkozik, kell:

1. **Beolvasása a data factory szolgáltatásidentitás** a "SERVICE IDENTITÁS alkalmazás Azonosítójának értéke" jön létre, valamint a gyári másolásával. Szerzői felhasználói felület ADF használja, a szolgáltatás identitásának azonosítója megjelenik-e az Azure Key Vault társított szolgáltatás létrehozása ablak; is megtalálja az Azure-portálról, tekintse meg [beolvasása a data factory szolgáltatásidentitás](data-factory-service-identity.md#retrieve-service-identity).
2. **A szolgáltatás identitásának hozzáférési jogot az Azure Key Vault.** Key vaultban lévő -> házirendek -> hozzáférési Hozzáadás -> Új keresés ehhez a szolgáltatásalkalmazáshoz identitás megadását azonosító **beolvasása** titkos engedélyek legördülő engedéllyel. Lehetővé teszi a kijelölt gyári titkos kulcstároló eléréséhez.
3. **Az Azure Key Vault mutató társított szolgáltatás létrehozása.** Tekintse meg [a társított szolgáltatásnak Azure Key Vault](#azure-key-vault-linked-service).
4. **Adatok kapcsolódó tároló szolgáltatás, mely hivatkozás található a megfelelő mappájában tárolt titkos kulcsot tároló létrehozása.** Tekintse meg [key vaultban tárolt hivatkozás titkos](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Az Azure Key Vault társított szolgáltatás

Csatolt Azure Key Vault szolgáltatás támogatott a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **AzureKeyVault**. | Igen |
| BaseUrl | Adja meg az Azure Key Vault URL-CÍMÉT. | Igen |

**Használatával szerzői felhasználói felületén:**

Kattintson a **kapcsolatok** -> **összekapcsolt szolgáltatások** -> **+ új** -> "Az Azure Key Vault" keresése:

![Keresési AKV](media/store-credentials-in-key-vault/search-akv.png)

Válassza ki a kiépített Azure Key Vault a hitelesítő adatok tárolására. Mindent **kapcsolat tesztelése** győződjön meg arról, hogy az AKV való kapcsolat érvénytelen. 

![AKV konfigurálása](media/store-credentials-in-key-vault/configure-akv.png)

**JSON-NÁ. példa:**

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

## <a name="reference-secret-stored-in-key-vault"></a>A key vaultban tárolt hivatkozás titkos kulcs

A következő tulajdonságok kulcstároló titkos kulcs hivatkozó társított szolgáltatás mező konfigurálásakor támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A mező a type tulajdonságot kell beállítani: **AzureKeyVaultSecret**. | Igen |
| secretName | Az azure key vault a titkos kulcs neve. | Igen |
| secretVersion | A titkos kulcsot az azure key vault verzióját.<br/>Ha nincs megadva, a legújabb verzióját a titkos kulcsot mindig használja.<br/>Ha meg van adva, majd csatlakoztat a megadott verzió lesz.| Nem |
| tároló | Egy Azure Key Vault kapcsolódó szolgáltatás, amely a hitelesítő adatok tárolása hivatkozik. | Igen |

**Használatával szerzői felhasználói felületén:**

Válassza ki **Azure Key Vault** titkos mezők a adatokat tároló/számítási létesített kapcsolat létrehozása során. Válassza ki a kiépített Azure Key Vault társított szolgáltatás, és adja meg a **titkos neve**. A titkos kulcs verzióját is megadhat. 

![Konfigurálja az AKV titkos kulcs](media/store-credentials-in-key-vault/configure-akv-secret.png)

**JSON-példa: (lásd a "password")**

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
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).
