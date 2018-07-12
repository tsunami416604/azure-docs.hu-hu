Ha rendelkezik egy közös hozzáférésű jogosultságkód (SAS) URL-címet, amely a tárfiókban lévő erőforrásokhoz való hozzáférést, használhatja a SAS egy kapcsolati karakterláncot. Az SAS a kérés hitelesítéséhez szükséges információkat tartalmazza, mert egy kapcsolati karakterláncot az SAS használatával biztosítja a protokoll, a szolgáltatási végpont és az erőforrás eléréséhez szükséges hitelesítő adatok.

Hozzon létre egy kapcsolati karakterláncot, amely tartalmazza a közös hozzáférésű jogosultságkódot, adja meg a karakterlánc a következő formátumban:

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

Egyes Szolgáltatásvégpontok megadása nem kötelező, bár a kapcsolati karakterláncot tartalmaznia kell legalább egy.

> [!NOTE]
> Ajánlott eljárásként ajánlott a HTTPS használata az SAS használatával.
>
> SAS megadása esetén egy kapcsolati karakterláncot egy konfigurációs fájlban, szükség lehet az URL-CÍMBEN szereplő különleges karakterek kódolása.
>
>

### <a name="service-sas-example"></a>Szolgáltatás SAS-példa
Íme egy példa, amely tartalmazza az SAS a Blob Storage szolgáltatás kapcsolati karakterlánc:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

És Íme egy példa a különleges karakterek kódolása ugyanabban a kapcsolati karakterlánc:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>Fiók SAS – példa
Íme egy példa egy kapcsolati karakterlánc, amely tartalmazza a fájl és a Blob storage-fiók SAS. Vegye figyelembe, hogy mindkét szolgáltatás végpontjai vannak megadva:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

És Íme egy példa az URL-Címének kódolása azonos kapcsolati karakterlánc:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```

