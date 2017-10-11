### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
A **Azure Storage társított szolgáltatásnak** lehetővé teszi egy Azure storage-fiók összekapcsolása egy Azure data factory használatával a **fiókkulcs**, amely az adat-előállítóban globális hozzáférést biztosít az Azure Storage. A következő táblázat az Azure tárolás társított szolgáltatásának vonatkozó JSON elemeket leírását.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A type tulajdonságot kell beállítani: **AzureStorage** |Igen |
| connectionString |Adja meg a connectionString tulajdonság az Azure storage való kapcsolódáshoz szükséges adatokat. |Igen |

Egy Azure Storage megtekintése és másolása a fiókkulcs lépéseit a következő cikkben talál: [nézet, a másolás és a hívóbetűk újragenerálása tárolási](../articles/storage/common/storage-create-storage-account.md#manage-your-storage-account).

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

### <a name="azure-storage-sas-linked-service"></a>Az Azure Storage Sas társított szolgáltatás
A közös hozzáférésű jogosultságkód (SAS) delegált hozzáférést biztosít azokhoz a tárfiókban lévő erőforrások. Lehetővé teszi az ügyfél csak korlátozott engedélyekkel a tárfiókban lévő objektumok egy adott időszakban, és engedélyeket, megadott számú anélkül, hogy a fiók hozzáférési kulcsait megosztásához megadását. A SAS URI, amely a lekérdezési paraméterek magában foglalja a hitelesített hozzáférést a tároló egyik erőforrásához szükséges összes adatot. Az SA-kat a tárolási erőforrások eléréséhez az ügyfélnek csak kell átadni a SAS a megfelelő konstruktort vagy metódust. Részletes információ a SAS: [megosztott hozzáférési aláírásokkal: az SAS-modell ismertetése](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> Az Azure Data Factory most csak támogatja **szolgáltatás SAS** , de nem fiók SAS. Lásd: [típusok a megosztott hozzáférési aláírásokkal](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) kétféle típusú és létrehozására vonatkozó további információért. Megjegyzés: az Azure-portálon generable SAS URL-címet, vagy a Tártallózó egy fiók SAS, ami nem támogatott.
> 

A társított Azure Storage SAS-szolgáltatás lehetővé teszi egy Azure Storage-fiók összekapcsolása egy Azure data factory egy közös hozzáférésű Jogosultságkód (SAS) használatával. Az adat-előállítóban minden/specifikus erőforrások (blobtárolóban /) a tárolóban lévő korlátozott/időhöz kötött hozzáférést biztosít. A következő táblázat a JSON-elemek szerepelnek Azure Storage SAS kapcsolódó szolgáltatásra vonatkozó leírást. 

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A type tulajdonságot kell beállítani: **AzureStorageSas** |Igen |
| sasUri |Adja meg a megosztott hozzáférési aláírást URI az Azure Storage-erőforrások, például a blob, -tároló vagy tábla.  |Igen |

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

Amikor hoz létre egy **SAS URI**, figyelembe véve a következők:  

* Állítsa be a megfelelő olvasási/írási **engedélyek** alapján a társított szolgáltatás (olvasási, írási, olvasás/írás) az adat-előállítóban felhasznált objektumokon.
* Állítsa be **lejárati idejének** megfelelően. Győződjön meg arról, hogy az Azure Storage-objektumokhoz való hozzáférést nem jár le az adatcsatorna aktív időszakára.
* URI létre kell hozni a megfelelő tárolót vagy blobot vagy a tábla szintjén a igények alapján. A SAS Uri-t az Azure blob lehetővé teszi, hogy a Data Factory szolgáltatásnak, hogy a blob eléréséhez. A SAS Uri-t az Azure blob-tároló lehetővé teszi, hogy az adott tárolóban lévő blobok iterációt a Data Factory szolgáltatásnak. Ha szeretne hozzáférést biztosítson nagyobb vagy kisebb értékre objektumok később, vagy frissítse a SAS URI-t, ne felejtse el a társított szolgáltatás frissítése új URI-azonosítójú.   

