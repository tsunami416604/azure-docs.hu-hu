---
title: Hitelesítő adatok tárolása az Azure Key Vaultban
description: Ismerje meg, hogyan tárolhatja az Azure Data Factory által futásidőben automatikusan beolvasni képes Azure-kulcstartóban használt adattárak hitelesítő adatait.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: jingwang
ms.openlocfilehash: b5a181625488a57de4b878d13c01a8c90bf8785a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414665"
---
# <a name="store-credential-in-azure-key-vault"></a>Hitelesítő adatok tárolása az Azure Key Vaultban

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Az adattárak és a számítási feladatok hitelesítő adatait az [Azure Key Vaultban](../key-vault/key-vault-overview.md)tárolhatja. Az Azure Data Factory lekéri a hitelesítő adatokat, amikor az adattárolót/számítást használó tevékenységet hajt végre.

Jelenleg az egyéni tevékenységek kivételével minden tevékenységtípus támogatja ezt a funkciót. Az összekötő konfigurációkifejezetten ellenőrizze a "csatolt szolgáltatás tulajdonságai" szakasz [minden összekötő témakörben](copy-activity-overview.md#supported-data-stores-and-formats) a részletekért.

## <a name="prerequisites"></a>Előfeltételek

Ez a funkció az adat-előállító felügyelt identitásra támaszkodik. Ismerje meg, hogyan működik a [Felügyelt identitás data factory](data-factory-service-identity.md) és győződjön meg arról, hogy az adatgyár rendelkezik egy társított.

## <a name="steps"></a>Lépések

Az Azure Key Vaultban tárolt hitelesítő adatokra való hivatkozáshoz a következőket kell tenni:

1. **Az adat-előállító felügyelt identitás lekéréséhez** másolja a "Felügyelt identitásobjektum-azonosító" értékét a gyárral együtt. Ha ADF szerzői felhasználói felületet használ, a felügyelt identitásobjektum-azonosító megjelenik az Azure Key Vault hozadékablakában; az Azure Portalról is lekérheti, olvassa el az [adat-előállító felügyelt identitás lekérése című hivatkozást.](data-factory-service-identity.md#retrieve-managed-identity)
2. **Adja meg a felügyelt identitás-hozzáférést az Azure Key Vaulthoz.** A key vaultban –> Access-> Access Policy -> Access Policy, keressen erre a felügyelt identitásra, hogy **engedélyt kapjon** titkos engedélyek legördülő legördülő menüben. Ez lehetővé teszi, hogy ez a kijelölt gyár hozzáférést titkos kulcstartóban.
3. **Hozzon létre egy összekapcsolt szolgáltatást, amely az Azure Key Vault.** Tekintse meg az [Azure Key Vault csatolt szolgáltatás.](#azure-key-vault-linked-service)
4. **Hozzon létre adattárhoz csatolt szolgáltatást, amelyen belül hivatkozik a megfelelő titkos kulcs a key vaultban tárolt.** Tekintse meg a [key vaultban tárolt hivatkozási titkos kulcsot.](#reference-secret-stored-in-key-vault)

## <a name="azure-key-vault-linked-service"></a>Azure Key Vaulthoz társított szolgáltatás

Az Azure Key Vaulthoz csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot a következőre kell állítani: **AzureKeyVault**. | Igen |
| baseUrl | Adja meg az Azure Key Vault URL-címét. | Igen |

**A szerzői felhasználói felület használata:**

Válassza **a Kapcsolatok** -> kapcsolt szolgáltatások**új****lehetőséget.** ->  Az Új csatolt szolgáltatásban keresse meg és válassza az "Azure Key Vault" lehetőséget:

![Keresés az Azure Key Vaultban](media/store-credentials-in-key-vault/search-akv.png)

Válassza ki a kiosztott Azure Key Vault, ahol a hitelesítő adatok tárolása. A **Kapcsolat tesztelésével** ellenőrizheti, hogy az AKV-kapcsolat érvényes-e. 

![Az Azure Key Vault konfigurálása](media/store-credentials-in-key-vault/configure-akv.png)

**JSON példa:**

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

## <a name="reference-secret-stored-in-key-vault"></a>Hivatkozás a Key Vaultban tárolt titkos kulcsra

A következő tulajdonságok akkor támogatottak, ha egy mezőt konfigurál egy kulcstartó titkos kulcsára hivatkozó csatolt szolgáltatásban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A mező típustulajdonságának a következő re van állítva: **AzureKeyVaultSecret**. | Igen |
| titkosnév | A titkos kulcs neve az Azure Key Vaultban. | Igen |
| titkosverzió | A titkos azure-kulcstárolóban a titkos verzió.<br/>Ha nincs megadva, mindig a titkos titok legújabb verzióját használja.<br/>Ha meg van adva, akkor ragaszkodik az adott verzióhoz.| Nem |
| áruház | Egy Azure Key Vault-alapú szolgáltatás, amely a hitelesítő adatok tárolására használt. | Igen |

**A szerzői felhasználói felület használata:**

Válassza ki az **Azure Key Vault** titkos mezők létrehozása közben a kapcsolatot az adattár/számítási kapcsolat létrehozása közben. Válassza ki a kiépített Azure Key Vault csatolt szolgáltatást, és adja meg a **titkos nevet.** Opcionálisan titkos verziót is megadhat. 

>[!TIP]
>A kapcsolati karakterláncot használó összekötők esetében az SQL Server, a Blob storage stb., választhat, hogy csak a titkos mezőt tárolja, például a jelszót az AKV-ban, vagy a teljes kapcsolati karakterláncot a KV-ben tárolja. Mindkét lehetőség megtalálható a felhasználói felületen.

![Az Azure Key Vault titkos kulcsának konfigurálása](media/store-credentials-in-key-vault/configure-akv-secret.png)

**JSON példa: (lásd a "jelszó" részt)**

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
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.
