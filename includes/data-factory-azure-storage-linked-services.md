---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: ee368b58195d61a1c6792a3a3655122af7104d58
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012150"
---
### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
Az **Azure Storage társított szolgáltatása** lehetővé teszi, hogy egy Azure Storage-fiókot egy Azure-beli adatgyárhoz társítson a **fiók kulcsa**segítségével, amely az Azure Storage-hoz való globális hozzáféréssel rendelkező adatelőállítót biztosít. Az alábbi táblázat az Azure Storage társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A Type tulajdonságot a következőre kell beállítani: **AzureStorage** |Igen |
| connectionString |Itt adhatja meg az Azure Storage-hoz a connectionString tulajdonsághoz való kapcsolódáshoz szükséges adatokat. |Igen |

Az Azure Storage-beli fiók kulcsának megtekintéséhez/másolásához tekintse meg a következő szakaszt: [Hozzáférési kulcsok](../articles/storage/common/storage-account-manage.md#access-keys).

**Példa:**  

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

### <a name="azure-storage-sas-linked-service"></a>Azure Storage sas társított szolgáltatás
A közös hozzáférésű aláírás (SAS) delegált hozzáférést biztosít a Storage-fiók erőforrásaihoz. Lehetővé teszi, hogy az ügyfél korlátozott engedélyeket biztosítson a Storage-fiókban lévő objektumoknak egy adott időtartamra és egy meghatározott engedélyekkel, anélkül, hogy meg kellene osztania a fiók hozzáférési kulcsait. Az SAS egy URI, amely magában foglalja a lekérdezési paramétereit, a tárolási erőforrásokhoz való hitelesített hozzáféréshez szükséges összes információt. A tároló erőforrásainak SAS-vel való eléréséhez az ügyfélnek csak az SAS-t kell átadnia a megfelelő konstruktorba vagy metódusba. Az SAS-vel kapcsolatos további információkért lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférési aláírások (SAS) használatával](../articles/storage/common/storage-sas-overview.md).

> [!IMPORTANT]
> A Azure Data Factory mostantól csak a **Service sas** -t támogatja, de nem a fiók sas-t. Vegye figyelembe, hogy a SAS URL-generable Azure Portal vagy Storage Explorer egy fiók SAS, amely nem támogatott.

> [!TIP]
> Az alábbi PowerShell-parancsok futtatásával létrehozhat egy Service SAS-t a Storage-fiókjához (cserélje le a tulajdonosokat, és adja meg a szükséges engedélyt):`$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Az Azure Storage SAS társított szolgáltatása lehetővé teszi egy Azure Storage-fiók összekapcsolását egy Azure-beli adatgyárhoz egy közös hozzáférési aláírás (SAS) használatával. Az adat-előállítót a tárolóban lévő összes/meghatározott erőforrás (blob/tároló) számára korlátozott/időhöz kötött hozzáféréssel biztosítja. Az alábbi táblázat az Azure Storage SAS társított szolgáltatásához tartozó JSON-elemek leírását tartalmazza. 

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A Type tulajdonságot a következőre kell beállítani: **AzureStorageSas** |Igen |
| sasUri |Az Azure Storage-erőforrásokhoz, például a blobhoz, a tárolóhoz vagy a táblához válassza a közös hozzáférési aláírás URI-JÁT.  |Igen |

**Példa:**

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

**Sas URI**létrehozásakor a következőket kell figyelembe venni:  

* Állítsa be a megfelelő olvasási/írási **engedélyeket** az objektumokon attól függően, hogy a társított szolgáltatás (olvasás, írás, olvasás/írás) hogyan legyen használatban az adatelőállítóban.
* Állítsa be **lejárati idő** megfelelően. Győződjön meg arról, hogy az Azure Storage-objektumokhoz való hozzáférés nem jár le a folyamat aktív időszakán belül.
* Az URI-t a megfelelő tárolóban, blobban vagy táblázatban kell létrehozni a szükséges érték alapján. Az Azure-Blobok SAS URI-ja lehetővé teszi, hogy az Data Factory szolgáltatás hozzáférjen az adott blobhoz. Az Azure Blob-tároló SAS URI-ja lehetővé teszi, hogy az Data Factory szolgáltatás megismételje az adott tárolóban lévő blobokat. Ha később több vagy kevesebb objektumot kell megadnia, vagy frissítenie kell az SAS URI-t, ne felejtse el frissíteni a társított szolgáltatást az új URI-val.   

