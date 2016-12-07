## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása
A következő lépésként állítsa be a fejlesztési környezetet a Visual Studióban, hogy kipróbálhassa az útmutatóban megadott példákat.

### <a name="create-a-windows-console-application-project"></a>Windows-konzolalkalmazás projekt létrehozása
Az alábbiak alapján hozzon létre egy új Windows-konzolalkalmazást a Visual Studióban:

![Windows-konzolalkalmazás létrehozása](./media/storage-development-environment-include/storage-development-environment-include-1.png)

Az oktatóanyagban szereplő példák hozzáadhatók a **Main()** metódusához a konzolalkalmazás `program.cs` részében.

Vegye figyelembe, hogy az Azure Storage ügyféloldali kódtárat bármilyen típusú .NET-alkalmazásból használhatja, ideértve az Azure-felhőszolgáltatásokat, az Azure-webalkalmazásokat, az asztali alkalmazásokat, illetve a mobilalkalmazásokat. Ebben az útmutatóban az egyszerűség kedvéért egy konzolalkalmazást használunk.

### <a name="use-nuget-to-install-the-required-packages"></a>A szükséges csomagok telepítése a NuGettel
Két csomagot kell telepítenie a projektben az oktatóanyag teljesítéséhez:

* [A Microsoft Azure Storage ügyféloldali kódtára a .NET-hez](https://www.nuget.org/packages/WindowsAzure.Storage/): Ez a csomag programozott hozzáférést biztosít a tárfiókja adatforrásaihoz.
* [A Microsoft Azure Configuration Manager könyvtár a .NET-hez](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): Ez a csomag egy osztályt biztosít a konfigurációs fájlból származó kapcsolati karakterlánc elemzéséhez, függetlenül attól, hogy az alkalmazás hol fut.

A NuGettel mindkét csomagot beszerezheti. Kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a projektjére a **Megoldáskezelőben**, és válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget.
2. Keressen rá az interneten a „WindowsAzure.Storage” kifejezésre, és kattintson az **Install** (Telepítés) gombra a Storage ügyféloldali kódtár és függőségeinek telepítéséhez.
3. Keresse rá az interneten a „ConfigurationManager” kifejezésre, és kattintson az **Install** (Telepítés) gombra az Azure Configuration Manager telepítéséhez.

> [!NOTE]
> A Storage ügyféloldali kódtár csomagja a [.NET-keretrendszerhez készült Azure SDK-ban](https://azure.microsoft.com/downloads/) is megtalálható. Azt javasoljuk azonban, hogy a Storage ügyféloldali kódtárat a NuGetből is telepítse, hogy az ügyféloldali kódtárnak biztosan mindig a legújabb verziójával rendelkezzen.
> 
> A .NET-keretrendszerhez készült Storage ügyféloldali kódtár ODataLib-függőségeit nem a WCF-adatszolgáltatások, hanem a NuGeten keresztül elérhető (5.0.2-es vagy újabb verziójú) ODataLib-csomagok oldják fel. Az ODataLib-kódtárak letölthetők közvetlenül, vagy a kódprojektje hivatkozhat rájuk a NuGeten keresztül. A Storage ügyféloldali kódtár által használt konkrét ODataLib-csomagok az [OData](http://nuget.org/packages/Microsoft.Data.OData/5.0.2), az [Edm](http://nuget.org/packages/Microsoft.Data.Edm/5.0.2) és a [Spatial](http://nuget.org/packages/System.Spatial/5.0.2) csomagok. Ezeket a kódtárakat az Azure Table Storage osztályai használják, de szükséges függőségek a Storage ügyféloldali kódtárral való programozáshoz.
> 
> 

### <a name="determine-your-target-environment"></a>A célkörnyezet meghatározása
Az útmutatóban lévő példákat kétféle környezetben futtathatja:

* A kódot futtathatja a felhőben, egy Azure Storage-fiókban. 
* A kódot futtathatja az Azure Storage Emulatorban is. A Storage Emulator egy helyi környezet, amely egy Azure Storage-fiókot emulál a felhőben. Az emulátor ingyenes lehetőséget biztosít a kódja tesztelésére és hibakeresésére, amíg az alkalmazása fejlesztés alatt áll. Az emulátor egy jól ismert fiókot és kulcsot használ. A további részleteket lásd: [Use the Azure Storage Emulator for Development and Testing](../articles/storage/storage-use-emulator.md) (Fejlesztés és tesztelés az Azure Storage Emulatorral)

Ha egy felhőbeli tárfiókot céloz meg, akkor másolja ki a tárfiók elsődleges hívóbetűjét az Azure Portalról. További információért lásd: [View and copy storage access keys](../articles/storage/storage-create-storage-account.md#view-and-copy-storage-access-keys) (A tárelérési kulcsok megtekintése és másolása).

> [!NOTE]
> A Storage Emulator megcélzásával elkerülheti az Azure Storage-hoz kapcsolódó költségeket. Ha azonban mégis egy Azure Storage-fiókot céloz meg a felhőben, az oktatóanyag végrehajtásával járó költségek elhanyagolhatóak.
> 
> 

### <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati karakterlánc konfigurálása
A .NET-hez készült Azure Storage ügyféloldali kódtár támogatja a tárolási szolgáltatások eléréséhez használt végpontok és hitelesítő adatok tárolási kapcsolati karakterlánccal történő konfigurálását. A tárolási kapcsolati karakterlánc egy konfigurációs fájlban tartható fenn a legjobban. 

A kapcsolati karakterláncokkel kapcsolatos további információkért lásd: [Az Azure Storage kapcsolati karakterláncának konfigurálása](../articles/storage/storage-configure-connection-string.md).

> [!NOTE]
> A tárfiók kulcsa hasonlít a tárfiók rendszergazdai jelszavához. Mindig ügyeljen a tárfiók kulcsának védelmére. Ne adja ki másoknak, ne kódolja fixen és ne mentse egy mások számára elérhető egyszerű szöveges fájlban. Ha azt gyanítja, hogy a kulcs biztonsága sérült, az Azure Portal segítségével generálja újra.
> 
> 

A kapcsolati karakterlánc konfigurálásához nyissa meg az `app.config` fájlt a Visual Studio Megoldáskezelőjében. Adja hozzá az alábbi `<appSettings>` elem tartalmát. Az `account-name` kifejezést cserélje a tárfiókja nevére, az `account-key` kifejezést pedig a hívóbetűre:

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
      <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
      </appSettings>
</configuration>
```

A konfiguráció beállítása például hasonló lesz a következőhöz:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=nYV0gln6fT7mvY+rxu2iWAEyzPKITGkhM88J8HUoyofvK7C6fHcZc2kRZp6cKgYRUM74lHI84L50Iau1+9hPjB==" />
```

A Storage Emulator célzásához használhat egy hivatkozást, amely leképezi a jól ismert fióknevet és kulcsot. Ebben az esetben a kapcsolati karakterlánc beállítása a következő lesz:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```



<!--HONumber=Nov16_HO3-->


