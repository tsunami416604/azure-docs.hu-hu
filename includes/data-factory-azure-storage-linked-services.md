---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 37917e0ed663675677f1d0452b5796120ca2694e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75466730"
---
### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
Az **Azure Storage-hoz kapcsolódó szolgáltatás** lehetővé teszi, hogy egy Azure-tárfiókot egy Azure-adat-előállító a **fiókkulcs**használatával, amely biztosítja az adatgyár globális hozzáférést biztosít az Azure Storage-hoz. Az alábbi táblázat az Azure Storage-hoz csatolt szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type |A típustulajdonságot a következőre kell állítani: **AzureStorage** |Igen |
| connectionString (kapcsolati karakterlánc) |Adja meg a connectionString tulajdonság Azure storage-hoz való csatlakozásához szükséges információkat. |Igen |

A tárfiók hozzáférési kulcsainak beolvasásáról a [Tárfiók hozzáférési kulcsainak kezelése](../articles/storage/common/storage-account-keys-manage.md)című témakörben talál további információt.

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

### <a name="azure-storage-sas-linked-service"></a>Azure Storage Sas csatolt szolgáltatás
A közös hozzáférésű jogosultságkód (SAS) delegált hozzáférést biztosít a tárfiókon lévő erőforrásokhoz. Lehetővé teszi, hogy korlátozott engedélyeket adjon az ügyfélnek a tárfiókban lévő objektumokhoz egy meghatározott ideig és egy megadott engedélykészlettel, anélkül, hogy meg kellene osztania a fiók hozzáférési kulcsait. A SAS egy URI, amely magában foglalja a lekérdezési paraméterek minden szükséges információt a tárolási erőforrás hitelesített hozzáférést. A SAS-szal való tárolási erőforrások eléréséhez az ügyfélnek csak a Megfelelő konstruktornak vagy metódusnak kell átadnia a SAS-t. A SAS-ről további információt az [Azure Storage-erőforrásokhoz megosztott hozzáférésű aláírások (SAS) használatával való korlátozott hozzáférés megadása](../articles/storage/common/storage-sas-overview.md)című témakörben talál.

> [!IMPORTANT]
> Az Azure Data Factory mostantól csak a **Service SAS-t** támogatja, de a fiók SAS-t nem. Vegye figyelembe, hogy az Azure Portalról vagy a Storage Explorerből származó SAS-URL-cím egy fiók SAS, amely nem támogatott.

> [!TIP]
> A PowerShell-parancsok alatt futtathatja a tárfiók hozlétre egy Service SAS-t (cserélje le a helyőrzőket, és adja meg a szükséges engedélyt):`$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Az Azure Storage SAS-alapú szolgáltatás lehetővé teszi, hogy egy Azure Storage-fiók összekapcsolásával egy Azure-adat-előállító egy megosztott hozzáférésű aláírás (SAS) használatával. Az adat-előállító korlátozott/időhöz kötött hozzáférést biztosít a tárolóban lévő összes/adott erőforrásokhoz (blob/tároló). Az alábbi táblázat az Azure Storage SAS-alapú szolgáltatásra jellemző JSON-elemek leírását tartalmazza. 

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type |A típustulajdonságot a következőre kell állítani: **AzureStorageSas** |Igen |
| sasUri között |Adja meg a megosztott hozzáférésű aláírás URI-t az Azure Storage-erőforrásokhoz, például blobhoz, tárolóhoz vagy táblához.  |Igen |

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

**SAS URI**létrehozásakor a következőket veszi figyelembe:  

* Állítsa be a megfelelő olvasási/írási **engedélyeket** az objektumokhoz a csatolt szolgáltatás (olvasás, írás, írás/írás) adat-előállítóban való használatával.
* Megfelelően állítsa be **a lejárati időt.** Győződjön meg arról, hogy az Azure Storage-objektumokhoz való hozzáférés nem jár le a folyamat aktív időszakán belül.
* Uri kell létrehozni a megfelelő tároló/blob vagy tábla szinten az igény alapján. A SAS Uri egy Azure blob lehetővé teszi, hogy a Data Factory szolgáltatás az adott blob eléréséhez. A SAS Uri egy Azure blob tároló lehetővé teszi, hogy a Data Factory szolgáltatás iterálni a tárolóban lévő blobok on keresztül. Ha később több/kevesebb objektumot kell biztosítania, vagy frissítenie kell a SAS URI-t, ne felejtse el frissíteni a csatolt szolgáltatást az új URI-val.   

