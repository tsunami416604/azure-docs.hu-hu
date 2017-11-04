Ha rendelkezik egy közös hozzáférésű jogosultságkód (SAS) URL-címet, amely a tárfiókban lévő erőforrásokhoz való hozzáférést biztosít, egy kapcsolati karakterláncot az SA-kat is használhatja. A SAS kérés hitelesítéséhez szükséges információkat tartalmazza, mert Aláírást tartalmazó kapcsolati karakterlánccal biztosít a protokollt, a szolgáltatási végpont és az erőforrás eléréséhez szükséges hitelesítő adatokat.

Hozzon létre egy kapcsolati karakterláncot, amely megosztott hozzáférési aláírást tartalmaz, adja meg a karakterlánc a következő formátumban:

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

Minden egyes végpont nem kötelező, bár a kapcsolati karakterláncnak tartalmaznia kell legalább egy.

> [!NOTE]
> Ajánlott eljárásként ajánlott a HTTPS használata a SAS-kód.
>
> SAS-kód megadása esetén a kapcsolati karakterláncot egy konfigurációs fájlban, esetleg az URL-címben különleges karakterek kódolása.
>
>

### <a name="service-sas-example"></a>Szolgáltatás SAS – példa
Íme egy példa egy kapcsolati karakterláncot, amely tartalmazza a szolgáltatásalapú SAS Blob Storage:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

És a speciális karakterek kódolás ugyanazt a kapcsolati karakterláncot, például:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>Fiók SAS – példa
Íme egy példa egy kapcsolati karakterláncot, amely tartalmazza az SAS-Blob és a fájl tárolási fiók. Vegye figyelembe, hogy mindkét szolgáltatás végpontjai vannak megadva:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

És az URL-kódolást ugyanazt a kapcsolati karakterláncot, például:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```

