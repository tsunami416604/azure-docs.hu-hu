## <a name="configure-your-application-to-access-azure-storage"></a>Állítsa be az alkalmazását Azure Storage eléréséhez
A tárolási szolgáltatások eléréséhez az alkalmazás hitelesítéséhez két módja van:

* Megosztott kulcs: Megosztott kulcs használata csak tesztelési célokra
* Közös hozzáférésű Jogosultságkód (SAS): SAS használja az éles környezetben

### <a name="shared-key"></a>Megosztott kulcsos
Megosztott kulcsos hitelesítést, az azt jelenti, hogy az alkalmazás fogja használni a fiók nevét és a fiókkulcsot tárolási szolgáltatások eléréséhez. Gyors ismertető a tár használata céljából fogjuk használni az első lépések megosztott kulcsos hitelesítést.

> [!WARNING] 
> **Csak tesztelési célra használja a megosztott kulcsos hitelesítést!** A fiók nevét és a fiókkulcsot, teljes olvasási/írási hozzáférést a társított tárolási fiók, amely terjeszti tovább a rendszer minden személy, amely letölti az alkalmazást. Ez az **nem** jó gyakorlat az, akkor kockázatát, hogy a kulcsot ne legyenek az ügyfelek nem megbízható.
> 
> 

Ha megosztott kulcsos hitelesítést használ, létrehozhat egy [kapcsolati karakterlánc](../articles/storage/common/storage-configure-connection-string.md). A kapcsolati karakterlánc áll:  

* A **megadni: DefaultEndpointsProtocol** -dönthet úgy, HTTP vagy HTTPS PROTOKOLLT. HTTPS-kapcsolaton keresztül azonban erősen ajánlott.
* A **fióknév** -a tárfiók neve
* A **Fiókkulcs** - a következőn a [Azure Portal](https://portal.azure.com), lépjen a tárfiókhoz, és kattintson a **kulcsok** ikonra kattintva előkeresheti ezt az információt.
* (Választható) **EndpointSuffix** -régiók másik végpont utótagok, például az Azure China vagy Azure irányítás tárolószolgáltatásokhoz szolgál.

Íme egy példa megosztott kulcsos hitelesítést használó kapcsolódási karakterlánc:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Közös hozzáférésű jogosultságkódok (SAS)
A mobilalkalmazás hitelesítéséhez az Azure Storage szolgáltatás egy ügyfél kérelmet az ajánlott módszer esetén egy közös hozzáférésű Jogosultságkód (SAS) használatával. SAS lehetővé teszi a hozzáférést egy ügyfél egy erőforráshoz egy adott időn, meghatározott engedélyekkel vannak beállítva.
A tárolási fiók tulajdonosának szüksége lesz a mobil ügyfelek felhasználásához SAS-kód létrehozása. Az SA-kat létrehozni, valószínűleg érdemes írni egy külön szolgáltatás, amely a SAS-t, az ügyfelek számára terjeszthető hoz létre. Tesztelési célokra is használhatja a [Microsoft Azure Tártallózó](http://storageexplorer.com) vagy a [Azure Portal](https://portal.azure.com) SAS-kód létrehozásához. A biztonsági Társítások létrehozásakor megadhatja a időtartam alatt, amelyben a SAS érvénytelen, és az engedélyeket, az ügyfél számára biztosít az SA-kat.

A következő példa bemutatja, hogyan használja a Microsoft Azure Tártallózó SAS-kód létrehozásához.

1. Ha még nem tette, [telepítse a Microsoft Azure Tártallózó](http://storageexplorer.com)
2. Csatlakozzon az előfizetéséhez.
3. Kattintson a tárfiók, majd kattintson a bal alsó "Műveletek" lapon. Kattintson a "Get közös hozzáférésű Jogosultságkód" létrehozására az a SAS "kapcsolati karakterlánc".
4. Íme egy példa egy SAS-kapcsolati karakterláncot, hogy biztosít olvasási és írási engedéllyel a szolgáltatás, a tároló és az objektum szintjén a tárfiók a blob szolgáltatás.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Ahogy látja, amikor a SAS használatával, akkor nem teszi ki a fiókkulcs az alkalmazásban. További biztonsági Társítások és gyakorlati tanácsok a SAS használatával kiveszi [megosztott hozzáférési aláírásokkal: az SAS-modell ismertetése](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md).

