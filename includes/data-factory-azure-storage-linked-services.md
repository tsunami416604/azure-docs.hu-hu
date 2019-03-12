---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: f7c189c59b5098ef22491a914a618afda2b5f51e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554701"
---
### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
A **Azure Storage társított szolgáltatás** lehetővé teszi, hogy az Azure storage-fiók összekapcsolása az Azure data factory használatával az **fiókkulcs**, amely az adat-előállító globális hozzáférést biztosít az Azure Storage. Az alábbi táblázatban az adott Azure Storage társított szolgáltatás JSON-elemek leírását.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A type tulajdonságot kell beállítani: **AzureStorage** |Igen |
| kapcsolati Sztringje |Adja meg a connectionString tulajdonság az Azure storage való kapcsolódáshoz szükséges adatokat. |Igen |

Lépések megtekintése és másolása a fiókkulcs egy Azure Storage a következő szakaszban talál: [Hozzáférési kulcsok](../articles/storage/common/storage-account-manage.md#access-keys).

**Példa**  

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="azure-storage-sas-linked-service"></a>A Sas Azure Storage társított szolgáltatás
Közös hozzáférésű jogosultságkód (SAS) a tárfiókban található erőforrások delegált hozzáférést biztosít. Lehetővé teszi az ügyfeleknek a tárfiókban lévő objektumokra vonatkozó adott ideig idő és a egy meghatározott engedélyek, a hozzáférési kulcsainak megosztása nélkül. A SAS URI, amely a lekérdezési paraméterek magában foglalja a tárolási erőforrásokhoz való hitelesített hozzáférés szükséges összes információt az. A SAS-tároló-erőforrások eléréséhez, az ügyfélnek csak kell megadni a megfelelő konstruktor vagy metódus az SAS. SAS kapcsolatos részletes információkért lásd: [közös hozzáférési aláírások: A SAS-modell ismertetése](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> Az Azure Data Factory jelenleg csak támogatja **szolgáltatásalapú SAS** , de nem a fiók SAS. Lásd: [típusok a közös hozzáférésű Jogosultságkódokat](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) két és az összeállítás módját. Jegyezze fel az Azure Portalról generable SAS URL-cím vagy a Storage Explorer egy fiók SAS, ami nem támogatott.

> [!TIP]
> Alább a tárfiók (cserélje le a helyőrzőket és a szükséges engedély megadása) szolgáltatás SAS létrehozása a PowerShell-parancsokat hajthat végre: `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Az Azure Storage SAS társított szolgáltatás egy Azure Storage-fiók összekapcsolása az Azure data factory egy közös hozzáférésű Jogosultságkód (SAS) használatával teszi lehetővé. Az adat-előállító all/adott erőforrásokhoz (a blob/tároló) a storage-ban korlátozott/időhöz kötött hozzáférést biztosít. Az alábbi táblázatban az adott Azure Storage SAS társított szolgáltatás JSON-elemek leírását. 

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A type tulajdonságot kell beállítani: **AzureStorageSas** |Igen |
| sasUri |Adja meg a megosztott hozzáférési Jogosultságkód URI az Azure Storage-erőforrások, például blob, tárolót vagy tábla.  |Igen |

**Példa**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<Specify SAS URI of the Azure Storage resource>"
        }
    }
}
```

Létrehozásakor egy **SAS URI-t**, figyelembe véve a következők:  

* Állítsa be a megfelelő olvasási/írási **engedélyek** objektumok hogyan a társított szolgáltatás (olvasási, írási, olvasási/írási) használatban van-e az adat-előállító alapján.
* Állítsa be **lejárati idő** megfelelően. Győződjön meg arról, hogy a hozzáférést az Azure Storage-objektumokat nem jár le a folyamat aktív időszakon belül.
* URI-t a megfelelő tárolót vagy blobot vagy a táblázat szintjén az igényei alapján kell létrehoznia. A SAS URI-t az Azure-blobba lehetővé teszi, hogy a Data Factory szolgáltatás eléréséhez, hogy a blob. Egy SAS URI-t egy Azure blob-tárolóba lehetővé teszi, hogy a Data Factory szolgáltatás iterálódnak a tárolóban lévő blobokat. Ha szeretne hozzáférést biztosítani más vagy kevesebb objektumot később, vagy frissítse a SAS URI-t, ne felejtse el frissítse a társított szolgáltatás az új URI-t.   

