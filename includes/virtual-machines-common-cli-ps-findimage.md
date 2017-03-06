

## <a name="azure-cli-20"></a>Azure CLI 2.0

Miután [telepítette az Azure CLI 2.0 szoftvert](https://docs.microsoft.com/cli/azure/install-az-cli2), az `az vm image list` paranccsal megjelenítheti a népszerű VM-rendszerképek gyorsítótárazott listáját. Például az `az vm image list -o table` parancs következő példája az alábbit jeleníti meg:

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               14.04.4-LTS         Canonical:UbuntuServer:14.04.4-LTS:latest                       UbuntuLTS            latest
CentOS         OpenLogic               7.2                 OpenLogic:CentOS:7.2:latest                                     CentOS               latest
openSUSE       SUSE                    13.2                SUSE:openSUSE:13.2:latest                                       openSUSE             latest
RHEL           RedHat                  7.2                 RedHat:RHEL:7.2:latest                                          RHEL                 latest
SLES           SUSE                    12-SP1              SUSE:SLES:12-SP1:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

### <a name="finding-all-current-images"></a>Összes aktuális rendszerkép keresése

Az összes rendszerkép aktuális listájának lekérdezéséhez használja az `az vm image list` parancsot az `--all` beállítással. Az Azure CLI 1.0 parancsoktól eltérően az `az vm image list --all` parancs alapértelmezés szerint a **westus** összes rendszerképét visszaadja (kivéve, ha meghatároz egy adott `--location` argumentumot), így az `--all` parancs futtatása eltarthat egy ideig. Ha a vizsgálatot interaktív módon szeretné végezni, használja az `az vm image list --all > allImages.json` parancsot, amely az Azure-ban jelenleg elérhető összes rendszerkép listáját visszaadja és elmenti egy fájlként helyi használatra. 

Kiválaszthat egyet a rendelkezésre álló beállítások közül, amelyekkel korlátozhatja a keresést egy adott helyre, ajánlatra, közzétevőre vagy termékváltozatra. Ha nem ad meg helyet, a rendszer a **westus** értékeit adja vissza.

### <a name="find-specific-images"></a>Adott rendszerképek keresése

Használja az `az vm image list` parancsot [JMESPATH lekérdezésszűrővel](https://docs.microsoft.com/cli/azure/query-az-cli2) adott információk kereséséhez. A következő példa a **Debian** rendszeren elérhető **termékváltozatokat** jeleníti meg (ne feledje, hogy a rendszer az `--all` kapcsoló nélkül csak a gyakori rendszerképek helyi gyorsítótárában keres):

```azurecli
az vm image list --query '[?contains(offer,`Debian`)]' -o table --all
```

A kimenet a következőképpen fog kinézni: 
```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
  Sku  Publisher    Offer    Urn                       Version    UrnAlias
-----  -----------  -------  ------------------------  ---------  ----------
    8  credativ     Debian   credativ:Debian:8:latest  latest     Debian

<list shortened for the example>
```

Ha tudja, hol kerül sor az üzembe helyezésre, használhatja az általános rendszerképkeresés eredményeit az `az vm image list-skus`, `az vm image list-offers` és `az vm image list-publishers` parancsokkal, hogy pontosan a kívánt elemet és annak üzembe helyezési helyeit kereshesse ki. Ha például az előző példából tudja, hogy a `credativ` rendelkezik egy Debian-ajánlattal, a `--location` paranccsal és egyéb beállításokkal megkeresheti a kívánt elemet. A következő példa egy Debian 8 rendszerképet keres a **westeurope** helyen:

```azurecli 
az vm image show -l westeurope -f debian -p credativ --skus 8 --version 8.0.201701180
```

A kimenet a következő:

```json
{
  "dataDiskImages": [],
  "id": "/Subscriptions/<guid>/Providers/Microsoft.Compute/Locations/westeurope/Publishers/credativ/ArtifactTypes/VMImage/Offers/debian/Skus/8/Versions/8.0.201701180",
  "location": "westeurope",
  "name": "8.0.201701180",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

## <a name="azure-cli-10"></a>Azure CLI 1.0 

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan navigálhat és választhat ki virtuálisgép-rendszerképeket egy, az Azure Resource Manager-alapú üzemi modellt támogató Azure CLI 1.0- vagy Azure PowerShell-példány segítségével. Előfeltételként váltson Resource Manager módra. Az Azure CLI-ben ebbe az üzemmódba a következő beírásával léphet: `azure config mode arm`. 
> 

Egy rendszerkép megkeresésének leggyorsabb módja az `azure vm image list` parancs meghívása, majd a helyszín, a közzétevő (kis- és nagybetűket nem megkülönböztető) nevének és egy ajánlatnak a megadása, ha ismeri az ajánlatot. A következő lista csak egy rövid példa (a legtöbb lista kifejezetten hosszú lehet) arra, ha tudja, hogy a „Canonical” az „UbuntuServer” ajánlat közzétevője.

```azurecli
azure vm image list westus canonical ubuntuserver
info:    Executing command vm image list
warn:    The parameter --sku if specified will be ignored
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         Sku                OS     Version          Location  Urn
data:    ---------  ------------  -----------------  -----  ---------------  --------  --------------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607220  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607220
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607230  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607230
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607240  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607240
```

A rendszer az **Urn** oszlopban található űrlapot adja át az `azure vm quick-create` parancsnak.

Gyakran azonban ilyenkor még nincs információja róla, hogy mi érhető el és mi nem. Ebben az esetben a parancssorban az `azure vm image list-publishers` parancs és egy adatközponthely meghatározásával navigálhat a rendszerképek között. A következő példa az USA nyugati régiójában („West US”) található összes rendszerkép-közzétevőt felsorolja (a helyszín argumentumot a szabványos helyek kisbetűs és szóköz nélküli írásmódjával adhatja át).

```azurecli
azure vm image list-publishers
info:    Executing command vm image list-publishers
Location: westus
+ Getting virtual machine and/or extension image publishers (Location: "westus")
data:    Publisher                                       Location
data:    ----------------------------------------------  --------
data:    a10networks                                     westus  
data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
data:    alertlogic                                      westus  
data:    AlertLogic.Extension                            westus  
```

Ezek a listák kifejezetten hosszúak lehetnek, ezért a fenti példa csupán egy részlet. Tegyük fel, észrevette, hogy a Canonical valóban egy rendszerkép-közzétevő az USA nyugati régiójában („West US”). Az ajánlataikat az `azure vm image list-offers` meghívásával, majd a parancssorban a helyszín és a közzétevő megadásával keresheti meg, az alábbi példához hasonlóan:

```azurecli
azure vm image list-offers
info:    Executing command vm image list-offers
Location: westus
Publisher: canonical
+ Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
data:    Publisher  Offer                      Location
data:    ---------  -------------------------  --------
data:    canonical  Ubuntu15.04Snappy          westus
data:    canonical  Ubuntu15.04SnappyDocker    westus
data:    canonical  UbunturollingSnappy        westus
data:    canonical  UbuntuServer               westus
data:    canonical  Ubuntu_Snappy_Core         westus
data:    canonical  Ubuntu_Snappy_Core_Docker  westus
info:    vm image list-offers command OK
```

Megtudtuk, hogy az USA nyugati régiójában a Canonical teszi közzé az **UbuntuServer**-ajánlatot az Azure-ban. De milyen termékváltozatokról van szó? Ezek megismeréséhez hívja meg az `azure vm image list-skus` parancsot, és adja meg a megismert helyet, közzétevőt és ajánlatot.

```azurecli
azure vm image list-skus
info:    Executing command vm image list-skus
Location: westus
Publisher: canonical
Offer: ubuntuserver
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         sku                Location
data:    ---------  ------------  -----------------  --------
data:    canonical  ubuntuserver  12.04.2-LTS        westus
data:    canonical  ubuntuserver  12.04.3-LTS        westus
data:    canonical  ubuntuserver  12.04.4-LTS        westus
data:    canonical  ubuntuserver  12.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  12.04.5-LTS        westus
data:    canonical  ubuntuserver  12.10              westus
data:    canonical  ubuntuserver  14.04-beta         westus
data:    canonical  ubuntuserver  14.04.0-LTS        westus
data:    canonical  ubuntuserver  14.04.1-LTS        westus
data:    canonical  ubuntuserver  14.04.2-LTS        westus
data:    canonical  ubuntuserver  14.04.3-LTS        westus
data:    canonical  ubuntuserver  14.04.4-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.4-LTS        westus
data:    canonical  ubuntuserver  14.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.5-LTS        westus
data:    canonical  ubuntuserver  14.10              westus
data:    canonical  ubuntuserver  14.10-beta         westus
data:    canonical  ubuntuserver  14.10-DAILY        westus
data:    canonical  ubuntuserver  15.04              westus
data:    canonical  ubuntuserver  15.04-beta         westus
data:    canonical  ubuntuserver  15.04-DAILY        westus
data:    canonical  ubuntuserver  15.10              westus
data:    canonical  ubuntuserver  15.10-alpha        westus
data:    canonical  ubuntuserver  15.10-beta         westus
data:    canonical  ubuntuserver  15.10-DAILY        westus
data:    canonical  ubuntuserver  16.04-alpha        westus
data:    canonical  ubuntuserver  16.04-beta         westus
data:    canonical  ubuntuserver  16.04.0-DAILY-LTS  westus
data:    canonical  ubuntuserver  16.04.0-LTS        westus
data:    canonical  ubuntuserver  16.10-DAILY        westus
info:    vm image list-skus command OK
```

Ezekkel az információkkal most már megtalálhatja a keresett rendszerképet, ha felül az eredeti hívás szerepel.

```azurecli
azure vm image list westus canonical ubuntuserver 16.04.0-LTS
info:    Executing command vm image list
+ Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "16.04.0-LTS" Location:"westus")
data:    Publisher  Offer         Sku          OS     Version          Location  Urn
data:    ---------  ------------  -----------  -----  ---------------  --------  --------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
info:    vm image list command OK
```

Most már kiválaszthatja azt az adott rendszerképet, amelyet használni szeretne. Ha gyorsan létre szeretne hozni egy virtuális gépet az imént megismert URN-adatokkal, vagy egy ezeket az adatokat tartalmazó sablont szeretne használni, tekintse át [a Mac, Linux és Windows eszközökhöz készült Azure CLI Azure Resource Managerrel történő használatával](../articles/xplat-cli-azure-resource-manager.md) foglalkozó témakört.

## <a name="powershell"></a>PowerShell
> [!NOTE]
> Telepítse és konfigurálja az [Azure PowerShell legújabb verzióját](/powershell/azureps-cmdlets-docs). Ha az 1.0-s verziónál régebbi Azure PowerShell-modult használ, akkor is használhatja az alábbi parancsokat, de előbb futtatnia kell a `Switch-AzureMode AzureResourceManager` parancsmagot. 
> 
> 

Amikor új virtuális gépet hoz létre az Azure Resource Managerrel, néhány esetben meg kell határoznia egy rendszerképet a következő tulajdonságok kombinációja alapján:

* Közzétevő
* Ajánlat
* SKU

Ezek az értékek szükségesek például a `Set-AzureRMVMSourceImage` PowerShell-parancsmaghoz vagy egy erőforráscsoport sablonfájljához, amelyben meg kell adnia létrehozni kívánt virtuális gép típusát.

Ha meg kell adnia ezeket az értékeket, ezt a rendszerképek között navigálva teheti meg:

1. Listázza a rendszerkép-közzétevőket.
2. Listázza egy adott közzétevő ajánlatait.
3. Listázza egy adott ajánlathoz tartozó termékváltozatokat.

Először listázza a közzétevőket a következő parancsokkal:

```powershell
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Adja meg a kiválasztott közzétevő nevét, és futtassa a következő parancsokat:

```powershell
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Adja meg a kiválasztott ajánlat nevét, és futtassa a következő parancsokat:

```powershell
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

A `Get-AzureRMVMImageSku` parancs kimenetéből megtudhatja az összes információt, amelyre szüksége van az új virtuális gép rendszerképének meghatározásához.

Az alábbi egy teljes példa:

```powershell
PS C:\> $locName="West US"
PS C:\> Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

PublisherName
-------------
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

A „MicrosoftWindowsServer” közzétevő esetében:

```powershell
PS C:\> $pubName="MicrosoftWindowsServer"
PS C:\> Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer

Offer
-----
WindowsServer
```

A „WindowsServer” ajánlat esetében:

```powershell
PS C:\> $offerName="WindowsServer"
PS C:\> Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

Skus
----
2008-R2-SP1
2012-Datacenter
2012-R2-Datacenter
2016-Nano-Server-Technical-Previe
2016-Technical-Preview-with-Conta
Windows-Server-Technical-Preview
```

Ha a listából kimásolja a kiválasztott termékváltozat nevét, akkor rendelkezésére áll a `Set-AzureRMVMSourceImage` PowerShell-parancsmaghoz vagy egy erőforráscsoport sablonjához szükséges összes információ.

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/