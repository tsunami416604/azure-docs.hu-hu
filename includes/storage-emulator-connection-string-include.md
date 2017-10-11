A storage emulator megosztott kulcsos hitelesítést támogatja egy rögzített fiókhoz és egy jól ismert hitelesítési kulcs. A fiók és a kulcs a storage emulator való használatra engedélyezett csak megosztott kulcsos hitelesítő adatait. Ezek a következők:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> A hitelesítési kulcs a storage emulator által támogatott készült csak egy ügyfél-hitelesítési kód funkció tesztelése. Bármilyen biztonsági célt nem szolgál. A termelési tárfiók és a kulcs a storage emulator nem használható. Ne használjon a fejlesztői fiók termelési adatokkal.
> 
> A storage emulator csak a HTTP Protokollon keresztül kapcsolatot támogat. HTTPS azonban az ajánlott protokoll egy éles Azure-tárfiók-erőforrások eléréséhez.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Csatlakozás a parancsikonnal emulátor-fiókhoz
Csatlakozás a storage emulator az alkalmazás a legegyszerűbb módja a konfigurálhat egy kapcsolati karakterláncot az alkalmazás konfigurációs fájljában, amely hivatkozik a helyi `UseDevelopmentStorage=true`. A storage emulator a kapcsolati karakterlánc példa egy *app.config* fájlt: 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Csatlakozás a jól ismert fióknevet és a kulcs segítségével emulátor-fiókhoz
Hozzon létre egy kapcsolati karakterláncot, amely hivatkozik a emulátor fióknevet és kulcsot, meg kell adnia a végpontok minden, a szolgáltatások szeretné használni a kapcsolódási karakterláncban emulátorától. Erre akkor szükség, úgy, hogy a kapcsolati karakterlánc használatával hivatkozik a emulátor végpontok, amelyek eltérnek a storage-fiók esetében. Például a kapcsolati karakterlánc értékét fog kinézni:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Ez az érték megegyezik a fenti, helyi `UseDevelopmentStorage=true`.

#### <a name="specify-an-http-proxy"></a>Adjon meg egy HTTP-proxy
A szolgáltatás a storage emulatorban tesztelést használandó HTTP-proxy is megadható. Ez lehet hasznos, ha HTTP-kérések és válaszok betartásával, akkor a tárolási szolgáltatások műveleteket hibakeresése közben. A proxy megadásához adja hozzá a `DevelopmentStorageProxyUri` a kapcsolati karakterlánc módosításait lehetőséget, és állítsa be az értékét a proxy URI. Például itt található egy kapcsolati karakterláncot, amely a storage emulator mutat, és egy HTTP-proxy konfigurálása:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

