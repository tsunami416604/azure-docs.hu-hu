---
title: Azure Compute – Linux diagnosztikai bővítmény
description: Hogyan konfigurálhatja az Azure Linux diagnosztikai bővítményt (LAD) metrikák és az Azure-ban futó linuxos virtuális gépekről való naplózási események gyűjtésére.
services: virtual-machines-linux
author: axayjo
manager: gwallace
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: akjosh
ms.openlocfilehash: 7a7c1af1193ba391550438229a22c4a8c116e6be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289175"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Metrikák és naplók figyelése a Linux diagnosztikai bővítmény használatával

Ez a dokumentum a Linux diagnosztikai bővítmény 3.0-s és újabb verzióját ismerteti.

> [!IMPORTANT]
> A 2.3-as és újabb verziókról [a dokumentumban](../linux/classic/diagnostic-extension-v2.md)talál további információt.

## <a name="introduction"></a>Bevezetés

A Linux diagnosztikai bővítmény segít a felhasználónak a Microsoft Azure-on futó Linux virtuális gép állapotának figyelésében. A következő képességekkel rendelkezik:

* Rendszer teljesítménymetrikákat gyűjt a virtuális gép, és tárolja őket egy adott táblában egy kijelölt tárfiókban.
* Lekéri a naplóeseményeket a syslogból, és tárolja őket egy adott táblában a kijelölt tárfiókban.
* Lehetővé teszi a felhasználók számára az összegyűjtött és feltöltött adatmutatók testreszabását.
* Lehetővé teszi a felhasználók számára, hogy testre szabják a syslog létesítmények és a súlyossági szint az összegyűjtött és feltöltött események.
* Lehetővé teszi a felhasználók számára, hogy megadott naplófájlokat töltsenek fel egy kijelölt tárolótáblába.
* Támogatja a metrikák és a naplóesemények tetszőleges EventHub-végpontok és JSON-formázott blobok a kijelölt tárfiókban.

Ez a bővítmény mindkét Azure üzembe helyezési modellel működik.

## <a name="installing-the-extension-in-your-vm"></a>A bővítmény telepítése a virtuális gépre

Ezt a bővítményt az Azure PowerShell-parancsmagok, az Azure CLI-parancsfájlok, az ARM-sablonok vagy az Azure Portal használatával engedélyezheti. További információt a [Bővítmények szolgáltatásai című témakörben talál.](features-linux.md)

Ezek a telepítési utasítások és a [letölthető mintakonfiguráció](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) a LAD 3.0-t a következőkre konfigurálja:

* ugyanazokat a mutatókat kell rögzíteni és tárolni, mint amelyeket a LAD 2.3 biztosít;
* a LAD 3.0 új fájlrendszer-mérőszámainak rögzítése;
* a LAD 2.3 által engedélyezett alapértelmezett syslog gyűjtemény rögzítése;
* lehetővé teszi az Azure Portal on charting és riasztás a virtuális gép metrikák.

A letölthető konfiguráció csak egy példa; saját igényeinek megfelelően módosíthatja.

### <a name="prerequisites"></a>Előfeltételek

* **Az Azure Linux Agent 2.2.0-s vagy újabb verzióját.** A legtöbb Azure VM Linux-katalógus képek közé tartozik a 2.2.7-es vagy újabb verzió. Futtassa `/usr/sbin/waagent -version` a virtuális gépre telepített verzió megerősítéséhez. Ha a virtuális gép a vendégügynök egy régebbi verzióját futtatja, kövesse [az alábbi utasításokat](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) a frissítéshez.
* **Az Azure CLI**. [Állítsa be az Azure CLI-környezetet](https://docs.microsoft.com/cli/azure/install-azure-cli) a számítógépen.
* A wget parancs, ha még nem rendelkezik ez: Fuss `sudo apt-get install wget`.
* Egy meglévő Azure-előfizetés és egy meglévő tárfiók az adatok tárolására.
* A támogatott Linux-disztribúciók listája be van kapcsolvahttps://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic#supported-linux-distributions

### <a name="sample-installation"></a>Minta telepítése

Futtatás előtt töltse ki a változók megfelelő értékeit az első szakaszban:

```azurecli
# Set your Azure VM diagnostic variables correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finallly tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

Az ezekben a példákban letöltött mintakonfiguráció szabványos adatokat gyűjt, és elküldi azokat a táblatárolóba. A mintakonfiguráció és annak tartalma URL-címe változhat. A legtöbb esetben le kell töltenie a portál beállítások JSON fájl és testre, hogy az Ön igényeinek, majd minden sablonok vagy automatizálási építeni használja a saját verzióját a konfigurációs fájl helyett letölti az URL-t minden alkalommal.

#### <a name="powershell-sample"></a>PowerShell-minta

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have your own customized public settings, you can inline those rather than using the template above: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally install the extension with the settings built above
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="updating-the-extension-settings"></a>A bővítménybeállításainak frissítése

Miután módosította a védett vagy nyilvános beállításokat, telepítse őket a virtuális gépre ugyanazzal a paranccsal. Ha valami megváltozott a beállításokban, a frissített beállításokat a bővítmény elküldi. A LAD újratölti a konfigurációt, és újraindítja magát.

### <a name="migration-from-previous-versions-of-the-extension"></a>Áttelepítés a bővítmény korábbi verzióiról

A legújabb verzió a kiterjesztés **3.0**. **2018. július 31-én vagy azt követően a régi verziók (2.x) elavultak, és nem tehetők közzé.**

> [!IMPORTANT]
> Ez a bővítmény a bővítmény konfigurációjának törési módosításait vezeti be. Az egyik ilyen változtatás a kiterjesztés biztonságának javítása érdekében történt; ennek eredményeképpen a 2.x-szel való visszamenőleges kompatibilitás nem tartható fenn. Emellett a bővítmény bővítményközzétevője is eltér a 2.x verziók közzétevőjétól.
>
> A bővítmény 2.x-ről a bővítmény új verziójára való átgréshez el kell távolítania a régi bővítményt (a régi közzétevő neve alatt), majd telepítenie kell a bővítmény 3-as verzióját.

Ajánlások:

* Telepítse a bővítményt úgy, hogy az automatikus alverziófrissítés engedélyezve van.
  * Klasszikus üzembe helyezési modell virtuális gépeken adja meg a "3.*" verziót, ha a bővítményt az Azure XPLAT CLI-n vagy a Powershellen keresztül telepíti.
  * Az Azure Resource Manager üzembe helyezési modell virtuális gépek, tartalmazza a ""autoUpgradeMinorVersion": true" a virtuális gép telepítési sablonban.
* Használjon új/másik tárfiókot a LAD 3.0-hoz. Számos kis inkompatibilitás van a LAD 2.3 és a LAD 3.0 között, amelyek problémássá teszik a fiók megosztását:
  * A LAD 3.0 más nevű táblában tárolja a syslog eseményeket.
  * A metrikák `builtin` counterSpecifier karakterláncai különböznek a LAD 3.0-ban.

## <a name="protected-settings"></a>Védett beállítások

Ez a konfigurációs információkészlet olyan bizalmas információkat tartalmaz, amelyeket védeni kell a nyilvános nézettől, például a tárolási hitelesítő adatoktól. Ezeket a beállításokat a bővítmény titkosított formában továbbítja és tárolja.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Név | Érték
---- | -----
storageAccountName | Annak a tárfióknak a neve, amelyben az adatokat a bővítmény írja.
tárAccountEndPoint | (nem kötelező) A végpont azonosítja a felhő, amelyben a tárfiók létezik. Ha ez a beállítás hiányzik, a LAD `https://core.windows.net`alapértelmezés szerint az Azure nyilvános felhője, . Ha az Azure Germany, az Azure Government vagy az Azure China tárfiókot szeretne használni, ennek megfelelően állítsa be ezt az értéket.
storageAccountSasToken | Fiók [SAS-jogkivonat](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) a Blob`ss='bt'`és table szolgáltatásokhoz`srt='co'`( ), tárolókra és objektumokra (`sp='acluw'`), amely hozzáadási, létrehozási, lista-, frissítési és írási engedélyeket ( biztosít. *Ne* tartalmazza a vezető kérdőjelet (?).
mdsdHttpProxy | (nem kötelező) HTTP-proxy adatok szükségesek ahhoz, hogy a bővítmény csatlakozzon a megadott tárfiókhoz és végponthoz.
mosogatókConfig | (nem kötelező) A metrikák és események által kézbesíthető alternatív úti célok részletei. A bővítmény által támogatott adatgyűjtők konkrét részleteit a következő szakaszok ismertetik.

Ha egy Erőforrás-kezelő sablonon belül SAS-jogkivonatot szeretne beszerezni, használja a **listAccountSas** függvényt. Például sablon, lásd: [Lista függvény példa](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Könnyedén létrehozhatja a szükséges SAS-jogkivonatot az Azure Portalon keresztül.

1. Válassza ki azt az általános célú tárfiókot, amelybe a bővítményt írni szeretné.
1. Válassza a "Megosztott hozzáférésű aláírás" lehetőséget a bal oldali menü Beállítások részében
1. A korábban leírt szakaszok meghozása
1. Kattintson a "SAS létrehozása" gombra.

![image](./media/diagnostics-linux/make_sas.png)

Másolja a létrehozott SAS-t a storageAccountSasToken mezőbe; távolítsa el a vezető kérdőjelet ("?").

### <a name="sinksconfig"></a>mosogatókConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

Ez a választható szakasz további célokat határoz meg, amelyekre a bővítmény elküldi az összegyűjtött adatokat. A "fogadó" tömb minden további adatgyűjtőhöz tartalmaz egy objektumot. A "type" attribútum határozza meg az objektum többi attribútumát.

Elem | Érték
------- | -----
név | A bővítmény konfigurációjában máshol található fogadóra hivatkozó karakterlánc.
type | A definiált mosogató típusa. Az ilyen típusú példányok egyéb értékeit határozza meg (ha vannak ilyenek).

A Linux diagnosztikai bővítmény 3.0-s verziója két fogadótípust támogat: az EventHubot és a JsonBlob.Version 3.0 a Linux Diagnostic Extension támogatja a két fogadó típusok: EventHub és JsonBlob.

#### <a name="the-eventhub-sink"></a>Az EventHub-fogadó

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

A "sasURL" bejegyzés tartalmazza a teljes URL-címet, beleértve a SAS-jogkivonatot, az Event Hub, amelyhez adatokat kell közzétenni. A LAD-nak sas-elnevezési házirendet kell megneveznie, amely lehetővé teszi a Küldés jogcímet. Egy példa:

* Eseményközpontok nevű névtere`contosohub`
* Eseményközpont létrehozása a névtérben, amelynek neve`syslogmsgs`
* Megosztott hozzáférési házirend létrehozása a `writer` névvel ellátott Eseményközpontban, amely engedélyezi a Küldés jogcímet

2018. január 1-jén éjfélig létrehozott egy SAS-jót utc éjfélig, a sasURL-érték a következő lehet:

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Az Event Hubs SAS-tokenjeivel kapcsolatos információk létrehozásáról és lekéréséről [ezen a weboldalon](https://docs.microsoft.com/rest/api/eventhub/generate-sas-token#powershell)talál további információt.

#### <a name="the-jsonblob-sink"></a>A JsonBlob-fogadó

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

A JsonBlob-fogadóba irányított adatok az Azure storage-ban lévő blobokban tárolódnak. A LAD minden példánya minden egyes fogadónévhez létrehoz egy blobot. Minden blob mindig tartalmaz egy szintaktikailag érvényes JSON-objektumtömböt. Az új bejegyzések atomilag kerülnek a tömbbe. A blobok egy tárolóban vannak tárolva, amelynek neve megegyezik a fogadó nevével. A blob tárolónevek Azure-tárolási szabályai a JsonBlob-fogadók nevére vonatkoznak: 3 és 63 kisméretű alfanumerikus ASCII karakter vagy kötőjel között.

## <a name="public-settings"></a>Nyilvános beállítások

Ez a struktúra különböző beállításblokkokat tartalmaz, amelyek a bővítmény által gyűjtött adatokat szabályozzák. Minden beállítás nem kötelező. Ha megadja `ladCfg`a értéket, `StorageAccount`a értéket is meg kell adnia.

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Elem | Érték
------- | -----
StorageAccount | Annak a tárfióknak a neve, amelyben az adatokat a bővítmény írja. A [Védett beállításokban](#protected-settings)megadott névvel megegyező nek kell lennie.
mdsdHttpProxy | (nem kötelező) Ugyanaz, mint a [Védett beállításokban.](#protected-settings) A nyilvános értéket felülírja a magánérték, ha be van állítva. Helyezze a titkos kulcsot(például jelszót) tartalmazó proxybeállításokat a [Védett beállítások között.](#protected-settings)

A többi elemet részletesen ismertetjük a következő szakaszokban.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

Ez az opcionális struktúra szabályozza a metrikák és naplók gyűjtését az Azure Metrics szolgáltatásba és más adatgyűjtőkbe való kézbesítéshez. Meg kell `performanceCounters` adnia vagy vagy `syslogEvents` mindkettőt meg kell adnia. Meg kell `metrics` adnia a szerkezetet.

Elem | Érték
------- | -----
eventVolume | (nem kötelező) A tárolótáblában létrehozott partíciók számát szabályozza. A ( `"Large"`a) `"Medium"` `"Small"`vagy a . Ha nincs megadva, az `"Medium"`alapértelmezett érték .
samplerateinszszekundumban | (nem kötelező) A nyers (nem összesített) metrikák gyűjtése közötti alapértelmezett időköz. A legkisebb támogatott mintavételi sebesség 15 másodperc. Ha nincs megadva, az `15`alapértelmezett érték .

#### <a name="metrics"></a>metrics

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Elem | Érték
------- | -----
resourceId | Az Azure Resource Manager erőforrás-azonosítója a virtuális gép vagy a virtuális gép méretezési készlet, amelyhez a virtuális gép tartozik. Ezt a beállítást akkor is meg kell adni, ha a konfigurációban egy JsonBlob-fogadót használ.
scheduledTransferPeriod | Az összesített metrikák kiszámításának gyakorisága és átvitele az Azure Metrics-be, IS 8601-es időintervallumban kifejezve. A legkisebb átviteli időszak 60 másodperc, azaz PT1M. Meg kell adnia legalább egy scheduledTransferPeriod.

A teljesítményszámlálók szakaszban megadott metrikák mintáit 15 másodpercenként vagy a számlálóhoz kifejezetten meghatározott mintavételi sebességgel gyűjti össze. Ha több scheduledTransferPeriod frekvencia jelenik meg (mint a példában), minden aggregáció egymástól függetlenül számítja ki.

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

Ez a választható szakasz szabályozza a metrikák gyűjtését. A nyers mintákat minden [egyes scheduledTransferPeriod](#metrics) hoz létre a következő értékek hez:

* középérték
* minimum
* maximum
* utolsó begyűjtött érték
* az aggregátum kiszámításához használt nyers minták száma

Elem | Érték
------- | -----
Mosogató | (nem kötelező) A fogadók nevének vesszővel tagolt listája, amelyre a LAD összesített metrikaeredményeket küld. Az összes összesített metrikák közzé minden felsorolt fogadó. Lásd [sinksConfig](#sinksconfig). Példa: `"EHsink1, myjsonsink"`.
type | A mérőszám tényleges szolgáltatóját azonosítja.
osztály | A "számlálóval" együtt azonosítja az adott metrikát a szolgáltató névterén belül.
számláló | Az "osztály" mezővel együtt azonosítja az adott metrikát a szolgáltató névterén belül.
counterSpecifier | Az Azure Metrics névtérben azonosítja az adott metrikát.
Feltétel | (nem kötelező) Kiválasztja annak az objektumnak egy adott példányát, amelyre a metrika vonatkozik, vagy kiválasztja az összesítést az objektum összes példányában. További információkért tekintse `builtin` meg a metrikadefiníciók.
samplerate (mintaráta) | IS 8601 időköz, amely beállítja a nyers minták gyűjtésének sebességét. Ha nincs beállítva, a gyűjtési időközt a [sampleRateInSeconds](#ladcfg)értéke állítja be. A legrövidebb támogatott mintavételi sebesség 15 másodperc (PT15S).
egység | A következő karakterláncok egyike kell, hogy legyen: "Count", "Bytes", "Seconds", "Percent", "CountPerSecond", "BytesPerSecond", "Milliszekundum". A metrika egységét határozza meg. Az összegyűjtött adatok fogyasztói elvárják, hogy az összegyűjtött adatértékek megegyeznek ezzel az egységgel. A LAD figyelmen kívül hagyja ezt a mezőt.
displayName | A címke (a társított területi beállítás által megadott nyelven) kell csatolni ezeket az adatokat az Azure Metrics. A LAD figyelmen kívül hagyja ezt a mezőt.

A counterSpecifier egy tetszőleges azonosító. A metrikák fogyasztói, például az Azure Portal diagramkészítési és riasztási funkció, használja counterSpecifier a "kulcs", amely azonosítja a metrika vagy egy példány a metrika. Metrikák esetén `builtin` azt javasoljuk, hogy `/builtin/`a . Ha egy metrika egy adott példányát gyűjti, azt javasoljuk, hogy csatolja a példány azonosítóját a counterSpecifier értékhez. Néhány példa:

* `/builtin/Processor/PercentIdleTime`- Az összes vCPU-n átlagban lévő tétlen idő
* `/builtin/Disk/FreeSpace(/mnt)`- Szabad hely a /mnt fájlrendszer számára
* `/builtin/Disk/FreeSpace`- Szabad hely átlagolt az összes csatlakoztatott fájlrendszerek

Sem a LAD, sem az Azure Portal nem várja el, hogy a counterSpecifier érték bármely mintának megfeleljen. Konzisztensnek kell lennie a counterSpecifier értékek létrehozásában.

Ha megadja, `performanceCounters`a LAD mindig adatokat ír egy táblába az Azure Storage-ban. A JSON-blobok és/vagy az Eseményközpontok ba is megkaphatja ugyanazokat az adatokat, de nem tilthatja le az adatok táblába való tárolását. Az azonos tárfiók név és végpont használatára konfigurált diagnosztikai bővítmény összes példánya hozzáadja a metrikákat és a naplókat ugyanahhoz a táblához. Ha túl sok virtuális gép ír ugyanabba a táblapartícióra, az Azure szabályozhatja az adott partícióra írt írásokat. Az eventVolume beállítás hatására a bejegyzések 1 (Kicsi), 10 (közepes) vagy 100 (nagy) különböző partíciók között oszlanak meg. Általában a "Közepes" elegendő annak biztosításához, hogy a forgalom nem szabályozott. Az Azure Metrics funkció az Azure Portal használja az ebben a táblázatban szereplő adatokat grafikonok létrehozásához vagy riasztások aktiválásához. A táblanév a következő karakterláncok összefűzése:

* `WADMetrics`
* A "scheduledTransferPeriod" a táblázatban tárolt összesített értékekhez
* `P10DV2S`
* A dátum, formájában "YYYYMMDD", amely változik minden 10 nap

Ilyen `WADMetricsPT1HP10DV2S20170410` például a és `WADMetricsPT1MP10DV2S20170609`a.

#### <a name="syslogevents"></a>syslogEsemények

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

Ez a választható szakasz a syslog naplóeseményeinek gyűjtését szabályozza. Ha a szakasz nincs megadva, a syslog események egyáltalán nem lesznek rögzítve.

A syslogEventConfiguration gyűjtemény minden érdeklődésre számot tartó syslog létesítményhez egy bejegyzéssel rendelkezik. Ha a minSeverity egy adott létesítményesetében "NINCS" , vagy ha a létesítmény egyáltalán nem jelenik meg az elemben, akkor az adott létesítményből származó események et nem rögzíti a megfelelő esemény.

Elem | Érték
------- | -----
Mosogató | Az egyes naplóeseményeket közzétenni tartalmazó fogadók nevének vesszővel tagolt listája. A syslogEventConfiguration korlátozásoknak megfelelő összes naplóesemény minden felsorolt fogadóban közzé lesz téve. Példa: "EHforsyslog"
facilityName | A syslog létesítmény neve (például "LOG\_\_USER" vagy "LOG LOCAL0"). Lásd a "létesítmény" részben a [syslog man oldalon](http://man7.org/linux/man-pages/man3/syslog.3.html) a teljes listát.
minSúlyossági szint | A syslog súlyossági szintje\_(például "LOG ERR" vagy "LOG\_INFO"). Lásd a "szint" részben a [syslog man oldalon](http://man7.org/linux/man-pages/man3/syslog.3.html) a teljes listát. A bővítmény rögzíti a létesítménynek küldött eseményeket a megadott szinten vagy az azt meghaladó szinten.

Ha megadja, `syslogEvents`a LAD mindig adatokat ír egy táblába az Azure Storage-ban. A JSON-blobok és/vagy az Eseményközpontok ba is megkaphatja ugyanazokat az adatokat, de nem tilthatja le az adatok táblába való tárolását. A tábla particionálási viselkedése megegyezik a. `performanceCounters` A táblanév a következő karakterláncok összefűzése:

* `LinuxSyslog`
* A dátum, formájában "YYYYMMDD", amely változik minden 10 nap

Ilyen `LinuxSyslog20170410` például a és `LinuxSyslog20170609`a.

### <a name="perfcfg"></a>perfCfg

Ez a választható szakasz tetszőleges OMI-lekérdezések végrehajtását [szabályozza.](https://github.com/Microsoft/omi)

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

Elem | Érték
------- | -----
névtér | (nem kötelező) Az OMI-névtér, amelyen belül a lekérdezést végre kell hajtani. Ha nincs megadva, az alapértelmezett érték a "root/scx", amelyet a [System Center Cross-platform Providers](https://github.com/Microsoft/SCXcore)valósít meg.
lekérdezés | A végrehajtandó OMI-lekérdezés.
tábla | (nem kötelező) Az Azure storage-tábla a kijelölt tárfiókban (lásd: [Védett beállítások).](#protected-settings)
frequency | (nem kötelező) A lekérdezés végrehajtása között eltelt másodpercek száma. Az alapértelmezett érték 300 (5 perc); minimális érték 15 másodperc.
Mosogató | (nem kötelező) A további fogadók nevének vesszővel tagolt listája, amelyhez a nyers mintametrika eredményeket közzé kell tenni. Ezek a nyers minták összesítése nem számítja ki a bővítmény vagy az Azure Metrics.

Meg kell adni a "table" vagy a "sinks" (asztal) vagy a "fogadó", vagy mindkettőt.

### <a name="filelogs"></a>fileLogs

A naplófájlok rögzítését szabályozza. A LAD rögzíti az új szövegsorokat, ahogy azok a fájlba vannak írva, és a táblasorokba és/vagy bármely megadott fogadóba (JsonBlob vagy EventHub) írja őket.

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Elem | Érték
------- | -----
file | A figyelni és rögzítendő naplófájl teljes elérési útja. Az elérési útnévnek egyetlen fájlt kell elneveznie; nem nevezhet nevet könyvtárnak, és nem tartalmazhat helyettesítő karaktereket.
tábla | (nem kötelező) Az Azure storage-tábla, a kijelölt tárfiókban (a védett konfigurációban megadottak szerint), amelybe a fájl "farkából" új sorok vannak írva.
Mosogató | (nem kötelező) A naplósorokat küldő további fogadók nevének vesszővel tagolt listája.

Meg kell adni a "table" vagy a "sinks" (asztal) vagy a "fogadó", vagy mindkettőt.

## <a name="metrics-supported-by-the-builtin-provider"></a>A beépített szolgáltató által támogatott mérőszámok

A beépített metrikaszolgáltató a felhasználók széles köre számára legérdekesebb mérőszámok forrása. Ezek a mutatók öt nagy osztályba sorolnak:

* Processzor
* Memory (Memória)
* Network (Hálózat)
* Fájlrendszer
* Lemez

### <a name="builtin-metrics-for-the-processor-class"></a>beépített mérőszámok a Processzor osztályhoz

A processzor osztály metrikák információt szolgáltat a processzor használata a virtuális gép. Százalékok összesítésekénál, az eredmény az összes processzor átlaga. Egy két vCPU-s virtuális gép, ha az egyik vCPU 100%-os, a másik pedig 100%-os tétlen, a jelentett PercentIdleTime lenne 50. Ha minden vCPU 50%-os foglalt volt ugyanebben az időszakban, a jelentett eredmény is 50 lenne. Egy négy vCPU-s virtuális gép, egy vCPU 100%-os foglalt, a többi tétlen, a jelentett PercentIdleTime lenne 75.

számláló | Jelentés
------- | -------
PercentIdleTime | Az összesítési időszak alatt a processzorok által a kernel tétlen ciklusát végrehajtó idő százalékos aránya
SzázalékProcesszor-idő | Nem tétlen szál végrehajtása az idő százaléka
PercentIOWaittime | Az IO-műveletek befejezésére váró idő százaléka
PercentInterruptTime | A hardver-/szoftvermegszakítások és dpc-k (késleltetett eljáráshívások) végrehajtásával töltött idő százalékos aránya
PercentUserTime | Az összesítési időszak alatt nem tétlen idő után a felhasználóban töltött idő százalékos aránya normál prioritással
SzázalékNiceTime | A nem tétlen idő, a százalékos töltött alacsonyabb (szép) prioritás
PercentPrivilegedTime között | A nem tétlen idő, a töltött százalékos jogosultsággal rendelkező (kernel) módban

Az első négy számláló nak 100%-ot kell tennie. Az utolsó három számláló is 100%; felosztják a PercentProcessorTime, percentIOWaitTime és PercentInterruptTime összegét.

Ha egyetlen metrikát szeretne összesíteni `"condition": "IsAggregate=TRUE"`az összes processzoron, állítsa be a . Egy adott processzor metrika, például egy négyvCPU-s virtuális `"condition": "Name=\\"1\\""`gép második logikai processzorának beszerzéséhez állítsa be a készletet. A logikai processzorszámok `[0..n-1]`a tartományban vannak.

### <a name="builtin-metrics-for-the-memory-class"></a>beépített mérőszámok a Memória osztályhoz

A metrikák memóriaosztálya információt nyújt a memória kihasználtságáról, a lapozásról és a felcserélésről.

számláló | Jelentés
------- | -------
Elérhető memória | A MiB-ben rendelkezésre álló fizikai memória
PercentAvailableMemory memória | A rendelkezésre álló fizikai memória a teljes memória százalékaként
Használt memória | Használatban lévő fizikai memória (MiB)
PercentUsedMemory memória | Használaton belüli fizikai memória a teljes memória százalékában
PagesPerSec | Összes lapozás (olvasás/írás)
PagesreadperSec | Lapok olvasni a háttértárolóból (swap fájl, programfájl, leképezett fájl stb.)
PagesWrittenPerSec | A háttértárolóra írt lapok (lapozófájl, leképezett fájl stb.)
Elérhető Swap | Nem használt csereterület (MiB)
Rendelkezésre álló százalékswap | Nem használt swap-terület a teljes swap százalékában
UsedSwap | Használat közbeni csereterület (MiB)
SzázalékHasználtSwap | Használat közbeni swap-terület a teljes swap százalékában

Ez az osztály a metrikák csak egy példány. A "condition" attribútumnak nincsenek hasznos beállításai, ezért ki kell hagyni.

### <a name="builtin-metrics-for-the-network-class"></a>beépített mérőszámok a Hálózati osztályhoz

A metrikák hálózati osztálya a rendszerindítás óta az egyes hálózati adapterek hálózati tevékenységéről nyújt információt. A LAD nem teszi elérhetővé a sávszélesség-metrikákat, amelyek a gazdamutatókból lehívhatók.

számláló | Jelentés
------- | -------
Továbbított bájtok | A rendszerindítás óta küldött bájtok összesen
Fogadott bájt | A rendszerindítás óta fogadott bájtok összesen
Teljes bájt | A rendszerindítás óta küldött vagy fogadott bájtok összesen
Csomagok átvitele | A rendszerindítás óta küldött összes csomag
Fogadott csomagok | A rendszerindítás óta fogadott összes csomag
TotalRxhibák | Fogadási hibák száma a rendszerindítás óta
TotalTxhibák | Az átviteli hibák száma a rendszerindítás óta
TotalCollisions (Összes ütközések) | A hálózati portok által a rendszerindítás óta jelentett ütközések száma

 Bár ez az osztály példányos, a LAD nem támogatja a hálózati metrikák összesítését az összes hálózati eszközön. Egy adott kapcsolat, például az eth0 metrikáinak beszerzéséhez állítsa be a készletet. `"condition": "InstanceID=\\"eth0\\""`

### <a name="builtin-metrics-for-the-filesystem-class"></a>beépített mérőszámok a Fájlrendszer osztályhoz

A Fájlrendszer metrikák osztálya a fájlrendszer használatáról nyújt tájékoztatást. Az abszolút és százalékos értékek úgy jelennek meg, ahogy azok megjelennek egy átlagos felhasználónak (nem root).

számláló | Jelentés
------- | -------
FreeSpace | Rendelkezésre álló lemezterület bájtban
Használttér | Használt lemezterület bájtban
SzázalékFreeSpace | Szabad terület százalékos aránya
PercentUsedSpace (SzázalékHasznált terület) | Felhasznált terület százalékos aránya
PercentFreeInodes (SzázalékfreeInodes) | A fel nem használt inodák százalékos aránya
SzázalékUsedInodes | Az összes fájlrendszerben összegezve kiosztott (használatban lévő) inodák százalékos aránya
BytesReadPerSecond | Másodpercenként olvasott bájtok
BytesWrittenPerSecond | Másodpercenként írt bájt
BytesPerSecond | Másodpercenként olvasott vagy írt bájtok
Olvasásszekunduma | Olvasási műveletek másodpercenként
ÍrásperSz | Írási műveletek másodpercenként
TransfersPerSecond (ÁtutalásperSz | Olvasási vagy írási műveletek másodpercenként

Az összes fájlrendszer összesített értékei a `"condition": "IsAggregate=True"`beállítással szerezhetők be. Egy adott csatlakoztatott fájlrendszer értékei, például az "/mnt", a beállítással szerezhetők be. `"condition": 'Name="/mnt"'` 

**MEGJEGYZÉS:** Ha az Azure Portalt használja a JSON helyett, a helyes feltételmező űrlapja Name='/mnt'

### <a name="builtin-metrics-for-the-disk-class"></a>beépített metrikák a Lemez osztályhoz

A Lemez osztály metrikák információt biztosít a lemezeszköz-használat. Ezek a statisztikák a teljes meghajtóra vonatkoznak. Ha egy eszközön több fájlrendszer is található, az eszköz számlálói gyakorlatilag összesítve vannak.

számláló | Jelentés
------- | -------
Olvasásszekunduma | Olvasási műveletek másodpercenként
ÍrásperSz | Írási műveletek másodpercenként
TransfersPerSecond (ÁtutalásperSz | Összes művelet másodpercenként
Átlagos Olvasási idő | Átlagos másodperc olvasási műveletenként
Átlagos WriteTime | Átlagos másodperc írási műveletenként
AverageTransferTime | Műveletenként átlagos másodpercek
AverageDiskQueueLength | A várakozási sorba állított lemezműveletek átlagos száma
ReadBytesPerSecond | Másodpercenként beolvasott bájtok száma
WriteBytesPerSecond | Másodpercenként írt bájtok száma
BytesPerSecond | Másodpercenként olvasott vagy írt bájtok száma

Az összes lemez összesített értékei a `"condition": "IsAggregate=True"`beállítással szerezhetők be. Egy adott eszköz (például /dev/sdf1) információinak bekéréséhez állítsa be a . `"condition": "Name=\\"/dev/sdf1\\""`

## <a name="installing-and-configuring-lad-30-via-cli"></a>LAD 3.0 telepítése és konfigurálása CLI-n keresztül

Feltételezve, hogy a védett beállítások a PrivateConfig.json fájlban találhatók, és a nyilvános konfigurációs adatok a PublicConfig.json fájlban vannak, futtassa a következő parancsot:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

A parancs feltételezi, hogy az Azure Resource Management mode (kar) az Azure CLI. A LAD klasszikus telepítési modell (ASM) virtuális gépekhez való`azure config mode asm`konfigurálásához váltson "asm" módba ( ) , és hagyja ki az erőforráscsoport nevét a parancsból. További információt a [platformfüggetlen CLI dokumentációjában](https://docs.microsoft.com/azure/xplat-cli-connect)talál.

## <a name="an-example-lad-30-configuration"></a>Példa lad 3.0 konfiguráció

Az előző definíciók alapján, itt egy minta LAD 3.0 kiterjesztés konfiguráció némi magyarázatot. A minta az esethez, a saját tárfiók nevét, a fiók SAS-jogkivonatot és az EventHubs SAS-jogkivonatokat kell használnia.

### <a name="privateconfigjson"></a>PrivátConfig.json

Ezek a személyes beállítások a következőket konfigurálják:

* tárfiók
* egyező fiók SAS tokenje
* több fogadó (JsonBlob vagy EventHubs SAS-jogkivonatokkal)

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="publicconfigjson"></a>NyilvánosConfig.json

Ezek a nyilvános beállítások a LAD-ot a következőkre késztetik:

* A százalékprocesszor-idő és a használt lemezterület-mérőszámok feltöltése a `WADMetrics*` táblázatba
* Töltsön fel üzeneteket syslog létesítmény "felhasználó" `LinuxSyslog*` és súlyossága "info" az asztalra
* Nyers OMI-lekérdezési eredmények (PercentProcessorTime és PercentIdleTime) feltöltése az elnevezett `LinuxCPU` táblába
* Csatolt sorok feltöltése a fájlban `/var/log/myladtestlog` a `MyLadTestLog` táblába

Az adatokat minden esetben a következő képpen is feltöltjük:

* Azure Blob storage (a tároló neve a JsonBlob-fogadóban meghatározott)
* EventHubs-végpont (az EventHubs-fogadóban megadottak szerint)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

A `resourceId` konfigurációban meg kell egyeznie a virtuális gép vagy a virtuális gép méretezési készlet.

* Az Azure platform metrikák diagramkészítés és riasztási ismeri a virtuális gép, amelyen dolgozik. Azt várja, hogy megtalálja az adatokat a virtuális gép a forrásazonosító a keresés kulcs használatával.
* Ha az Azure automatikus skálázás, az erőforrás-azonosító az automatikus skálázási konfigurációban meg kell egyeznie a LAD által használt resourceId.
* A resourceId be van építve a LAD által írt JsonBlobs nevekbe.

## <a name="view-your-data"></a>Adatok megtekintése

Az Azure Portal használatával teljesítményadatokat tekinthet meg, vagy riasztásokat állíthat be:

![image](./media/diagnostics-linux/graph_metrics.png)

Az `performanceCounters` adatok mindig egy Azure Storage-táblában tárolódnak. Az Azure Storage API-k számos nyelven és platformon érhetők el.

A JsonBlob-fogadóknak küldött adatok a Védett beállításokban megnevezett tárfiókban lévő blobokban [tárolódnak.](#protected-settings) A blobadatokat bármely Azure Blob Storage API-k használatával használhatja fel.

Ezenkívül ezekkel a felhasználói felületi eszközökkel is hozzáférhet az Azure Storage-ban lévő adatokhoz:

* Visual Studio Server Explorer alkalmazást.
* [Microsoft Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer").

Ez a pillanatkép a Microsoft Azure Storage Explorer munkamenet mutatja a generált Azure Storage-táblák és tárolók egy megfelelően konfigurált LAD 3.0-bővítmény egy teszt virtuális gép. A kép nem pontosan egyezik a [LAD 3.0-s mintakonfigurációval.](#an-example-lad-30-configuration)

![image](./media/diagnostics-linux/stg_explorer.png)

Tekintse meg a megfelelő [EventHubs dokumentációt,](../../event-hubs/event-hubs-what-is-event-hubs.md) amelyből megtudhatja, hogyan használhatja fel az EventHubs-végponton közzétett üzeneteket.

## <a name="next-steps"></a>További lépések

* Metrikariasztásokat hozhat létre az [Azure Monitorban](../../monitoring-and-diagnostics/insights-alerts-portal.md) a begyűjtött metrikákhoz.
* [Figyelési diagramokat](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) hozhat létre a mérőszámokhoz.
* Ismerje meg, hogyan [hozhat létre egy virtuálisgép-méretezési csoport](../linux/tutorial-create-vmss.md) a metrikák segítségével az automatikus skálázás szabályozásához.
