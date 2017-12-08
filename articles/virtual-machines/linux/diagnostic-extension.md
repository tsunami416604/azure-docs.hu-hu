---
title: "Az Azure Compute - Linux diagnosztikai bővítmény |} Microsoft Docs"
description: "Hogyan konfigurálható az Azure Linux diagnosztikai bővítmény (LAD) gyűjtéséhez és az Azure-ban futó Linux virtuális gépek események naplózása."
services: virtual-machines-linux
author: jasonzio
manager: anandram
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/09/2017
ms.author: jasonzio
ms.openlocfilehash: ebb963236a069f272499fce59945d0cf0d3d647f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Linux diagnosztikai kiterjesztésének használatával figyelheti a metrikák és a naplókat

Ez a dokumentum ismerteti a 3.0-s és a Linux-diagnosztikai bővítmény újabb verziója.

> [!IMPORTANT]
> 2.3-as és a régebbi verziójával kapcsolatos információkért lásd: [Ez a dokumentum](./classic/diagnostic-extension-v2.md).

## <a name="introduction"></a>Bevezetés

A Linux diagnosztikai bővítmény segít a felhasználói figyelése a Microsoft Azure-on futó Linux virtuális gép állapotát. A következő képességekkel rendelkezik:

* Rendszer teljesítménymutatók gyűjti össze a virtuális Gépet, és a megadott tábla kijelölt tárfiókban tárolja azokat.
* Alkalmazásnapló-események lekéri a syslog, és a megadott tábla kijelölt tárfiókban tárolja azokat.
* Lehetővé teszi a felhasználók a gyűjtött és a feltöltött adatok metrikák testreszabásához.
* Lehetővé teszi a felhasználók gyűjtött és a feltöltése események súlyossági szintek és syslog létesítményekben testreszabásához.
* Lehetővé teszi a felhasználók egy kijelölt tároló táblához megadott naplófájlok feltöltéséhez.
* Támogatja a metrikákat és naplózási események küldése tetszőleges EventHub-végpontokat és a JSON-formátumú blobot, amely a kijelölt tárfiókot.

A bővítmény mindkét Azure üzembe helyezési modellel működik.

## <a name="installing-the-extension-in-your-vm"></a>A bővítmény telepítése a virtuális gépen

A bővítmény engedélyezéséhez az Azure PowerShell-parancsmagok, az Azure parancssori felület parancsfájlok vagy Azure központi telepítési sablonok használatával. További információkért lásd: [bővítményeit biztosító funkciókat](./extensions-features.md).

Az Azure-portálon engedélyezése és konfigurálása LAD 3.0 nem használható. Ehelyett azt telepíti, és konfigurálja a 2.3 verziója. Az Azure portál diagramok és értesítések működéséhez a bővítmény verzióját is adataival.

A telepítési utasításokat és egy [letölthető mintakonfiguráció](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) LAD 3.0-s konfigurálása:

* lépéssel rögzítheti és tárolhatja a metrikák LAD 2.3; által biztosított volt
* fájl rendszer metrikákat, új, 3.0-s LAD; hasznos készlete rögzítése
* az alapértelmezett syslog gyűjtemény LAD 2.3; által engedélyezett rögzítése
* Engedélyezze az Azure portál élményét diagramkészítési, és a virtuális gép metrikák riasztást küld.

A letölthető konfigurációja, csak egy példa; Módosítsa a saját igényeinek megfelelően.

### <a name="prerequisites"></a>Előfeltételek

* **Az Azure Linux ügynök 2.2.0 verzió vagy újabb**. A legtöbb Azure virtuális gép Linux gyűjtemény lemezképei 2.2.7 verzióját tartalmazzák, vagy később. Futtatás `/usr/sbin/waagent -version` megerősítéséhez, hogy a virtuális Gépen telepített verzióval. Ha a vendégügynök egy régebbi verzióját a virtuális gép fut, hajtsa végre a [ezeket az utasításokat](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/update-agent) frissíti.
* **Azure parancssori felület (CLI)**. [Állítsa be az Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) környezet a számítógépen.
* A wget parancs, ha már nincs: futtatása `sudo apt-get install wget`.
* Meglévő Azure-előfizetése és a meglévő tárfiók belül úgy, hogy az adatok tárolásához.

### <a name="sample-installation"></a>A minta telepítése

Adja meg az első három sorokban a megfelelő paramétereket, majd hajtsa végre ezt a parancsfájlt a legfelső szintű:

```bash
# Set your Azure VM diagnostic parameters correctly below
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
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 9999-12-31T23:59Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finallly tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

A minta konfigurációját, és annak tartalmát, az URL-címe van változhatnak. Töltse le a portálbeállítások JSON-fájlt, és az igényeinek megfelelően. A sablonok vagy automation, hozhat létre egy saját másolat ahelyett, hogy letöltése URL-címet minden alkalommal, amikor használja.

### <a name="updating-the-extension-settings"></a>A bővítmény beállításainak frissítése folyamatban

Miután megváltoztatta a védett vagy nyilvános beállításokat, azok a virtuális gép ugyanaz a parancs futtatásával. Ha a változás a a beállításokat, a bővítmény kapja meg a frissített beállításokkal. LAD betölti a konfigurációt, és újraindul saját magát.

### <a name="migration-from-previous-versions-of-the-extension"></a>A bővítmény korábbi verzióiról való áttelepítés

A bővítmény legújabb verziója **3.0**. **A régi verziókat (2.x) elavultak, ezért lehet, hogy közzé nem tett, vagy azt követően 2018 július 31**.

> [!IMPORTANT]
> A bővítmény jelentős változásokat a bővítmény konfigurációja vezet be. Egy ilyen módosítás, melyekkel biztonságosabbá teheti a bővítmény; Ennek eredményeképpen visszamenőleges 2.x kompatibilitást sikerült nem kell tartani. A bővítmény Publisher ehhez a kiterjesztéshez is eltér a közzétevő a 2.x verziójához.
>
> 2.x át ezt a bővítmény új verzióját, távolítsa el a régi bővítmény (alatt a régi közzétevő neve), majd telepítse a bővítmény 3 verzióját.

Javaslatok:

* Telepítse a bővítmény engedélyezve van az automatikus alverzió frissítését.
  * A klasszikus telepítési modell virtuális gépek adja meg "3.*" verzióval Azure XPLAT parancssori felületen vagy a Powershellen keresztül bővítmény telepítésekor.
  * Az Azure Resource Manager telepítési modellhez tartozó virtuális gépek esetén tartalmazhat ""autoUpgradeMinorVersion": true" az a virtuális gép központi telepítési sablont.
* A LAD 3.0 új/eltérőek a tárolási fiók használata. Van több kis incompatibilities LAD 2.3 és LAD 3.0 között, amelyek megosztása a problémás fiók:
  * LAD 3.0 tárolja a syslog-események egy táblát, amely egy másik nevet.
  * A karakterláncok a counterSpecifier `builtin` metrikák LAD 3.0 különböznek.

## <a name="protected-settings"></a>Védett beállításai

Ez a konfigurációs adatokat bizalmas adatokat, amelyeket védeni kell a nyilvános nézet, például a tároló hitelesítő adatait tartalmazza. Ezek a beállítások továbbítva, és a bővítmény titkosított formában tárolja.

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
storageAccountName | A tárfiók, amelyben adatot ír a kiegészítő mező neve.
storageAccountEndPoint | (választható) A végpont a felhőben, amelyben a tárfiók található azonosítása. Ha ez a beállítás hiányzik, LAD az Azure nyilvános felhőjében alapértelmezett `https://core.windows.net`. A Németországi Azure storage-fiók használatához Azure Government vagy Azure Kína, állítsa ezt az értéket ennek megfelelően.
storageAccountSasToken | Egy [fiók SAS-jogkivonat](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) a Blob és Table szolgáltatásait (`ss='bt'`), tárolók és objektumok alkalmazandó (`srt='co'`), amely hozzáadásához létrehozása, listában frissítése, és írási engedélyekkel (`sp='acluw'`). Tegye *nem* közé tartozik a bevezető kérdőjel (?).
mdsdHttpProxy | (választható) HTTP-proxyadatok csatlakozni a megadott tárfiók és a végpont a bővítmény engedélyezéséhez szükséges.
sinksConfig | (választható) Alternatív célhoz, amelyhez metrikákkal és eseményekkel kézbesítése részleteit. A bővítmény által támogatott minden egyes adatokat a fogadó részleteit a következő szakaszok ismertetnek.

A szükséges SAS-jogkivonatot az Azure portálon keresztül egyszerűen állíthat össze.

1. Válassza ki az általános célú tárfiók, amelybe írni a bővítmény
1. Válassza a "Megosztott hozzáférési aláírást" a bal oldali menü Beállítások része
1. Ellenőrizze a megfelelő szakaszait, mint korábban leírt
1. A "Készítése SAS" gombra.

![Kép](./media/diagnostic-extension/make_sas.png)

A generált SAS másolja az storageAccountSasToken mezőjébe; Távolítsa el a bevezető kérdőjel ("?").

### <a name="sinksconfig"></a>sinksConfig

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

Ez a szakasz választható, amelyhez a bővítményt a összegyűjti az adatokat elküldi a további célok meghatározása. A "fogadó" tömb minden további adatokat fogadó egy objektumot tartalmaz. A "type" attribútum meghatározza, hogy az objektum más attribútumokat.

Elem | Érték
------- | -----
név | Ez a gyűjtő részeiben bővítménykonfiguráció hivatkozik karakterlánc.
type | A múltbeli fogadó típusa. Meghatározza, hogy a többi érték (ha vannak) az ilyen típusú példányok.

A Linux diagnosztikai bővítmény 3.0-s verziója két fogadó-típusokat támogatja: az EventHub és JsonBlob.

#### <a name="the-eventhub-sink"></a>Az EventHub fogadó

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

A "sasURL" bejegyzés tartalmazza a teljes URL-címet, beleértve a SAS-jogkivonat, az Event Hubs, amelyhez adatokat közzé kell tenni. LAD van szükség, egy olyan házirendet, amely lehetővé teszi, hogy a küldési elnevezési SAS jogcímek. Példa:

* Hozzon létre egy Event Hubs-névtér neve`contosohub`
* Létrehoz egy Eseményközpontot, a névtér neve`syslogmsgs`
* Az Event Hubs nevű meg megosztott hozzáférési házirend létrehozása `writer` , amely lehetővé teszi, hogy a küldési jogcím

Ha létrehozott egy SAS jó 2018. január 1. az UTC éjfél sasURL érték lehet:

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

További információ a SAS-jogkivonatokat előállító az Event Hubs: [Ez a weblap](../../event-hubs/event-hubs-authentication-and-security-model-overview.md).

#### <a name="the-jsonblob-sink"></a>A JsonBlob fogadó

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Az Azure-tárfiókba blobok egy JsonBlob fogadó irányítva adatokat tárolja. Minden példánya LAD blob minden fogadó név óránként hoz létre. Minden egyes blob mindig tartalmaz egy szintaktikailag érvényes JSON-tömb objektum. Új bejegyzések i hozzáadódnak a tömb. A tároló neve megegyezik a gyűjtő a blobok tárolja. A blob-tároló neve az Azure storage szabályok vonatkoznak a nevek JsonBlob nyelő: 3 és 63 kisbetűs alfanumerikus ASCII-karaktereket, és kötőjelek között.

## <a name="public-settings"></a>Nyilvános beállításai

Ez a struktúra a bővítmény által gyűjtött információk szabályozó beállítások több blokkot tartalmaz. Minden beállítás nem kötelező. Ha megad `ladCfg`, meg kell adni `StorageAccount`.

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
StorageAccount | A tárfiók, amelyben adatot ír a kiegészítő mező neve. A névvel kell lennie, mint a megadott a [beállítások védett](#protected-settings).
mdsdHttpProxy | (választható) Ugyanaz, mint a a [beállítások védett](#protected-settings). A nyilvános érték felülbírálja a saját értéket, ha beállítása. Helyezze el, amely tartalmazza a titkos kulcs, például a jelszó, a proxybeállításokat a [beállítások védett](#protected-settings).

A fennmaradó összetevőit az alábbi szakaszok részletesen.

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

A választható struktúra vezérlők metrikák és a naplókat a kézbesítési az Azure metrikaszolgáltatás és egyéb adatok összegyűjtése fogadók esetében. Meg kell adnia vagy `performanceCounters` vagy `syslogEvents` vagy mindkettőt. Meg kell adnia a `metrics` struktúra.

Elem | Érték
------- | -----
eventVolume | (választható) A tárolási tábla belül létrehozott partíciók számát szabályozza. Egyikének kell lennie `"Large"`, `"Medium"`, vagy `"Small"`. Ha nincs megadva, az alapértelmezett érték: `"Medium"`.
sampleRateInSeconds | (választható) Az alapértelmezett időköz közötti nyers (unaggregated) mérőszámok gyűjteménye. A legkisebb támogatott mintavételi gyakoriság: 15 másodperc. Ha nincs megadva, az alapértelmezett érték: `15`.

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
resourceId | A virtuális gép vagy virtuálisgép-méretezési az Azure Resource Manager erőforrás-azonosítója, amelyhez tartozik a virtuális gép beállítása. Ezzel a beállítással lehet is megadott, ha bármely JsonBlob fogadó szerepel-e a konfigurációban.
scheduledTransferPeriod | A gyakoriság, amellyel összesített adatok gyűjtése le van számított és Azure metrika, százalékban kifejezve van 8601 időt időközönkénti továbbítja. A legkisebb átviteli időtartam 60 másodperc, ez azt jelenti, hogy PT1M. Meg kell adnia legalább egy scheduledTransferPeriod.

A metrikák performanceCounters szakaszában megadott mintáit összegyűjtött 15 másodpercenként vagy: a minta értékelje az explicit módon definiálva számláló. Ha több scheduledTransferPeriod gyakoriságot jelenik meg (ahogy a példában), minden Összesítés kiszámítása egymástól függetlenül.

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

Ez a szakasz választható metrikák gyűjteményét határozza meg. Nyers minták összesítik az egyes [scheduledTransferPeriod](#metrics) ezeket az értékeket létrehozásához:

* témakörök
* minimális
* Maximális
* utolsó összegyűjtött érték
* a nyers, összesített kiszámítására használt minták száma

Elem | Érték
------- | -----
fogadók esetében | (választható) Egy vesszővel tagolt listája nyelő mely LAD való küld mérték eredményeit összesíti. Minden felsorolt fogadó összes összesített metrikát kerülnek közzétételre. Lásd: [sinksConfig](#sinksconfig). Példa: `"EHsink1, myjsonsink"`.
type | A metrika a tényleges szolgáltató azonosítja.
Osztály | "Számláló", és azonosítja az adott metrika a szolgáltató névtéren belül.
A számláló | "Class", és azonosítja az adott metrika a szolgáltató névtéren belül.
counterSpecifier | Az Azure metrikák névtérben adott metrika azonosítja.
Az állapot | (választható) Kiválasztja az objektum, amelyhez a metrika vonatkozik, vagy az összesítés kiválasztja, hogy az objektum összes példánya között egy adott példányához. További információkért lásd: a [ `builtin` metrikai meghatározásainak](#metrics-supported-by-builtin).
sampleRate | Beállítja a változási gyakoriság, amellyel ez a mérőszám a nyers minták gyűjtik 8601 intervallum van. Ha nincs megadva, az adatgyűjtési időköz értéke értékével [sampleRateInSeconds](#ladcfg). A legrövidebb támogatott mintavételi gyakoriság: 15 másodperc (PT15S).
egység | Ezek a karakterláncok egyike lehet: "Count", "Memória", "S", "Százaléka", "CountPerSecond", "BytesPerSecond", "Ezredmásodperces". Határozza meg a metrika egység. Az összegyűjtött adatok fogyasztóinak várhatóan az összegyűjtött adatok értékeket a egység. LAD figyelmen kívül hagyja ezt a mezőt.
displayName | A címke (a kapcsolódó területi beállításban megadott nyelven) csatolni kell ezeket az adatokat az Azure metrikákat. LAD figyelmen kívül hagyja ezt a mezőt.

A counterSpecifier tetszőleges azonosító, amely. Metrikák fogyasztóinak, például az Azure portál diagramkészítési, és counterSpecifier riasztási szolgáltatás, használja a "key" azonosító a metrika példányának vagy egy mértéket. A `builtin` metrika, ajánlott counterSpecifier értékek kezdődő `/builtin/`. Gyűjti a metrika egy adott példányához, azt javasoljuk a példány azonosítója csatolása counterSpecifier értékét. Néhány példa:

* `/builtin/Processor/PercentIdleTime`-Minden Vcpu között átlagosan üresjárati idő
* `/builtin/Disk/FreeSpace(/mnt)`– Szabad terület a /mnt fájlrendszer
* `/builtin/Disk/FreeSpace`– Az összes csatlakoztatott fájlrendszerek között átlagosan szabad terület

LAD és az Azure-portál nem vár a counterSpecifier értéket megfelel a mintának. Hogyan hozható létre counterSpecifier értékek a kell.

Ha a `performanceCounters`, LAD mindig írja az adatokat az Azure storage-táblázathoz. Lehet írni a JSON-blobok és/vagy az Event Hubs ugyanazokat az adatokat, de nem tiltható le, a tábla adatainak tárolásához. A diagnosztikai bővítmény összes példánya azonos tárfióknév használatára konfigurált, és adja hozzá a metrikák és a naplók végpont ugyanahhoz a táblához. Túl sok virtuális gép tábla partícióra ír, az Azure képes szabályozni a írások ehhez a partícióhoz. A eventVolume beállítás azt eredményezi, 1 (kisméretű), 10 (közepes) keresztül terjedésének vagy 100 (nagy) különböző partíciók bejegyzések. "Közepes" általában elegendő biztosítására a forgalom nem folyamatban van. Az Azure portál Azure metrikák jellemzője diagramok létrehozására vagy riasztásokat kiváltó használja ebben a táblázatban az adatokat. A táblanév: ezek a karakterláncok a kapott:

* `WADMetrics`
* Az összesített értékek a táblában tárolt "scheduledTransferPeriod"
* `P10DV2S`
* Az űrlap "ÉÉÉÉHHNN", amely megváltoztatja minden 10 nap, dátum

Példák `WADMetricsPT1HP10DV2S20170410` és `WADMetricsPT1MP10DV2S20170609`.

#### <a name="syslogevents"></a>syslogEvents

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

Ez a szakasz választható határozza meg a syslog alkalmazásnapló-események gyűjtése. A szakasz elhagyása esetén syslog-események nem minden rögzíti.

A syslogEventConfiguration gyűjtemény szerepel egy bejegyzés minden egyes csomópontjára syslog-szolgáltatást. Ha minSeverity "NONE" egy adott helyen, vagy az, hogy a létesítmény nem jelenik meg az elem minden, a rendszer rögzíti, hogy a létesítmény származó események.

Elem | Érték
------- | -----
fogadók esetében | Egy vesszővel tagolt listája, amelyhez egyedi aktiválási naplót esemény közzé lesz téve nyelő. Minden felsorolt fogadó syslogEventConfiguration korlátozásai megfelelő összes naplózási események kerülnek közzétételre. Példa: "EHforsyslog"
facilityName | A syslog létesítmény nevét (például a "napló\_felhasználói" vagy "napló\_LOCAL0"). Az "létesítmény" című a [syslog man lap](http://man7.org/linux/man-pages/man3/syslog.3.html) teljes listáját.
minSeverity | A syslog súlyossági szint (például a "napló\_hiba" vagy "napló\_INFO"). Az "szint" című a [syslog man lap](http://man7.org/linux/man-pages/man3/syslog.3.html) teljes listáját. A bővítmény a létesítmény elérte vagy meghaladta a megadott szint küldött eseményeket rögzíti.

Ha a `syslogEvents`, LAD mindig írja az adatokat az Azure storage-táblázathoz. Lehet írni a JSON-blobok és/vagy az Event Hubs ugyanazokat az adatokat, de nem tiltható le, a tábla adatainak tárolásához. Ez a tábla particionáló működése megegyeznek a `performanceCounters`. A táblanév: ezek a karakterláncok a kapott:

* `LinuxSyslog`
* Az űrlap "ÉÉÉÉHHNN", amely megváltoztatja minden 10 nap, dátum

Példák `LinuxSyslog20170410` és `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

Ez a szakasz választható szabályozza tetszőleges végrehajtásának [OMI](https://github.com/Microsoft/omi) lekérdezések.

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
Namespace | (választható) Az OMI névtér belül, amely hajtható végre a lekérdezést. Ha nincs megadva, az alapértelmezett érték: "legfelső szintű/scx", által megvalósított a [a System Center platformfüggetlen szolgáltatók](http://scx.codeplex.com/wikipage?title=xplatproviders&referringTitle=Documentation).
lekérdezés | Az OMI lekérdezés végrehajtására.
Tábla | (választható) Az Azure storage tábla, a kijelölt tárfiókban lévő (lásd: [beállítások védett](#protected-settings)).
frequency | (választható) A lekérdezés végrehajtása között eltelt másodpercek száma. Alapértelmezett értéke 300 (5 percig); minimális érték 15 másodpercre.
fogadók esetében | (választható) További nyelő, amelyhez metrika eredmények nyers minta közzé kell tenni a neveket vesszővel tagolt listája. Nincs összesítési e nyers minták számított a bővítmény vagy Azure metrikákat.

"Table" vagy "fogadók esetében", vagy mindkettőt, meg kell adni.

### <a name="filelogs"></a>fileLogs

A rögzítés a naplófájlok szabályozza. LAD új szöveges sort rögzíti, mert a fájlt írás, és írja a táblázat sorainak és/vagy a megadott mosdók (JsonBlob vagy EventHub).

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
Fájl | A teljes elérési útja a naplófájl figyelése és rögzítése. A pathname nevet egyetlen fájl; nem egy könyvtár nevet és nem tartalmazhat helyettesítő karaktereket.
Tábla | (választható) Az Azure storage tábla, a kijelölt tárfiókban (meghatározottak szerint a védett configuration), amelybe a "végéről" a fájl új sorok készültek.
fogadók esetében | (választható) További nyelő küldött napló sorok a neveket vesszővel tagolt listája.

"Table" vagy "fogadók esetében", vagy mindkettőt, meg kell adni.

## <a name="metrics-supported-by-the-builtin-provider"></a>A beépített szolgáltató támogatja a mérőszámok

A beépített metrika szolgáltató a forrása a metrikák a legérdekesebb a felhasználók széles körét. A metrikák öt széleskörű osztályok sorolhatók:

* Processzor
* Memory (Memória)
* Network (Hálózat)
* Fájlrendszer
* Lemez

### <a name="builtin-metrics-for-the-processor-class"></a>a processzor osztály beépített metrikák

A processzor osztály a mérőszámok tájékoztatást ad azokról a virtuális gép processzor kihasználtsága. Százalékos összesítésekor eredménye átlagos összes processzorok között. A két-vCPU virtuális gépen Ha egy vCPU 100 %-os elfoglalt volt, és a másik 100 %-os üresjárati, a jelentésben szereplő PercentIdleTime pedig 50. Ha minden vCPU 50 % azonos időszakára vonatkozó elfoglalt volt, a jelentésben szereplő eredmény is pedig 50. Négy-vCPU virtuális gép, egy vCPU 100 %-os foglalt, és a többi üresjárati, és a jelentett PercentIdleTime 75 lenne.

A számláló | Jelentése
------- | -------
PercentIdleTime | A összesítési időszakban, hogy a processzorok volt végrehajtása a kernel üresjárati hurok idő százalékos aránya
PercentProcessorTime | Nem üresjárati szálat idő százalékos aránya
PercentIOWaitTime | Várakozás az I/O műveletek elvégzéséhez idő százalékos aránya
PercentInterruptTime | Hardver vagy szoftver megszakítások, DPC-k (késleltetett eljáráshívások) végrehajtása idő százalékos aránya
PercentUserTime | Az összesítési időszak alatt nem üresjárati idő a fordított idő százalékos aránya a felhasználó több normál prioritással
PercentNiceTime | Nem üresjárati időt százalékos süllyesztett (jó) prioritással töltött
PercentPrivilegedTime | Nem üresjárati időt százalékos töltött védett (kernel) módban

Az első négy számlálók kell összeg 100 %. Az utolsó három is számlálók összege 100 %; a PercentProcessorTime, PercentIOWaitTime és PercentInterruptTime azok tovább.

Az összes processzor gyűjtődnek egyetlen mérőszám beszerzéséhez beállítása `"condition": "IsAggregate=TRUE"`. Megadott processzorsebességgel rendelkező, például a négy-vCPU virtuális gépek, a második logikai processzor metrika beszerzése beállítása `"condition": "Name=\\"1\\""`. A tartományban vannak logikai processzor számok `[0..n-1]`.

### <a name="builtin-metrics-for-the-memory-class"></a>a beépített metrikákat a memória-osztály

A memória az osztály a mérőszámok memóriafelhasználás a lapozást, és áttelepíteni a forráskörnyezetból információkat biztosít.

A számláló | Jelentése
------- | -------
AvailableMemory | Rendelkezésre álló fizikai memória MIB
PercentAvailableMemory | A teljes memória százalékos rendelkezésre álló fizikai memória
UsedMemory | Használatban lévő fizikai memória (MiB)
PercentUsedMemory | A teljes memória százalékos a használatban lévő fizikai memória
PagesPerSec | Teljes lapozófájl (olvasás/írás)
PagesReadPerSec | Lapok olvasni háttértár (lapozófájl programfájlt, leképezett fájlt, stb.)
PagesWrittenPerSec | Lapok írni a biztonsági tár (lapozófájl, leképezett fájlt, stb.)
AvailableSwap | Nem használt lapozóterület (MiB)
PercentAvailableSwap | Nem használt lapozóterület teljes lapozófájl-kapacitás százalékában
UsedSwap | Használatban lévő lapozóterület (MiB)
PercentUsedSwap | Használatban lévő lapozóterület teljes lapozófájl-kapacitás százalékában

Ez az osztály a mérőszámok csak egyetlen példány van. A "feltétel" attribútum nem hasznos beállításokkal rendelkezik, és megadni.

### <a name="builtin-metrics-for-the-network-class"></a>a hálózati osztály beépített metrikák

A hálózati osztály a mérőszámok tevékenységről szolgáltat információkat hálózati az egyes hálózati adaptereken indítása óta. LAD nem biztosít sávszélesség metrikákat, amelyek a gazdagép-metrikák kérhető.

A számláló | Jelentése
------- | -------
BytesTransmitted | Rendszerindítás óta küldött bájtok száma összesen
BytesReceived | Rendszerindítás óta fogadott összes bájt
BytesTotal | Küldött vagy indítása óta fogadott bájtok teljes száma
PacketsTransmitted | Rendszerindítás óta küldött csomagok száma összesen
PacketsReceived | Rendszerindítás óta fogadott csomagok száma összesen
TotalRxErrors | A fogadási hibák száma indítása óta
TotalTxErrors | -Küldési hibák száma indítása óta
TotalCollisions | Rendszerindítás óta a hálózati portok által jelentett ütközések száma

 Bár ez az osztály van instanced, LAD nem támogatja az összes hálózati eszköz gyűjtődnek rögzítésével hálózati metrikákat. Állítsa be a metrika egy adott felület esetében eth0, például az beszerzése `"condition": "InstanceID=\\"eth0\\""`.

### <a name="builtin-metrics-for-the-filesystem-class"></a>a fájlrendszer osztály beépített metrikák

A fájlrendszer osztály a mérőszámok filesystem használati információkat biztosít. Abszolút és százalékos értéket jelentett, akkor szokásos felhasználóhoz (nem a legfelső szintű) megjelenik.

A számláló | Jelentése
------- | -------
FreeSpace | Szabad lemezterület (bájt)
UsedSpace | A felhasznált lemezterület (bájt)
PercentFreeSpace | Szabad terület százalékos aránya
PercentUsedSpace | A felhasznált terület százalékos aránya
PercentFreeInodes | Nem használt Inode-OK százaléka
PercentUsedInodes | Összesítve közötti összes fájlrendszerek lefoglalt (használatban) Inode-OK százaléka
BytesReadPerSecond | Másodpercenként olvasott bájtok száma
BytesWrittenPerSecond | Másodpercenként írt bájtok
BytesPerSecond | Bájt nem írható és olvasható / másodperc
ReadsPerSecond | Olvasási műveletek másodpercenkénti száma
WritesPerSecond | Írási műveletek másodpercenkénti száma
TransfersPerSecond | Olvasási vagy írási műveletek másodpercenkénti száma

Összesített értékeket fájlrendszerek keresztül érhető el úgy, hogy `"condition": "IsAggregate=True"`. Például egy adott csatlakoztatott fájlrendszer értékei "/ mnt", úgy, hogy szerezhetők `"condition": 'Name="/mnt"'`.

### <a name="builtin-metrics-for-the-disk-class"></a>a lemez osztály beépített metrikák

A lemez osztály a mérőszámok eszköz lemezhasználati információkat biztosít. A statisztikai információk a teljes meghajtót vonatkozik. Ha egy eszközön több fájlrendszereket, az eszköznek a számlálók vannak, gyakorlatilag az összes gyűjtődnek.

A számláló | Jelentése
------- | -------
ReadsPerSecond | Olvasási műveletek másodpercenkénti száma
WritesPerSecond | Írási műveletek másodpercenkénti száma
TransfersPerSecond | Teljes műveletek másodpercenkénti száma
AverageReadTime | Olvasási művelet átlagos másodpercben
AverageWriteTime | Írási művelet átlagos másodpercben
AverageTransferTime | Művelet átlagos másodpercben
AverageDiskQueueLength | Várólistára helyezett lemezen műveletek átlagos száma
ReadBytesPerSecond | A másodpercenként beolvasott bájtok száma
WriteBytesPerSecond | Másodpercenként írt bájtok száma
BytesPerSecond | Olvassa el és másodpercenként írt bájtok száma

Minden lemezeken összesített értékeket szerezhető be úgy, hogy `"condition": "IsAggregate=True"`. Ahhoz, hogy egy adott eszköz (például/dev/sdf1) adatait, állítsa be `"condition": "Name=\\"/dev/sdf1\\""`.

## <a name="installing-and-configuring-lad-30-via-cli"></a>Telepítése és konfigurálása LAD 3.0 parancssori felület használatával

Ha a védett beállítások PrivateConfig.json fájlban, és a nyilvános konfigurációs adatait a PublicConfig.json, futtassa ezt a parancsot:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

A parancs feltételezi, hogy használja az Azure CLI Azure Resource Manager (arm) módját. LAD konfigurálása a klasszikus üzembe helyezési modell (ASM) virtuális gépeket, váltson "asm" módra (`azure config mode asm`), és hagyja ki ezt a parancsot az erőforráscsoport neve. További információkért lásd: a [platformfüggetlen parancssori felület dokumentáció](https://docs.microsoft.com/azure/xplat-cli-connect).

## <a name="an-example-lad-30-configuration"></a>Egy példa LAD 3.0 konfiguráció

A fenti definíciók alapján, ez a minta LAD 3.0 bővítménykonfiguráció rövid. Szeretné alkalmazni ezt a mintát a helyzet, akkor érdemes használni a saját tárfiók neve, a fiók SAS-jogkivonat és a EventHubs SAS-tokenje.

### <a name="privateconfigjson"></a>PrivateConfig.json

Ezek a személyes beállítások konfigurálása:

* a storage-fiók
* a megfelelő fiók SAS-jogkivonat
* több fogadók esetében (JsonBlob vagy az SAS-tokenje EventHubs)

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

### <a name="publicconfigjson"></a>PublicConfig.json

A nyilvános beállítások okozhat a LAD:

* A százalékos processzoridő és használt-terület metrikák feltöltése a `WADMetrics*` tábla
* Töltse fel üzenetek a syslog létesítmény "user" és a súlyosság "Infó" a `LinuxSyslog*` tábla
* Töltse fel az elnevezett nyers OMI lekérdezési eredmények (PercentProcessorTime és PercentIdleTime) `LinuxCPU` tábla
* Töltse fel a fájl sorainak hozzáfűzött `/var/log/myladtestlog` számára a `MyLadTestLog` tábla

Minden esetben adatokat is feltöltött:

* Az Azure Blob storage (a tároló neve: a JsonBlob fogadó meghatározottak szerint)
* EventHubs végpont (meghatározottak szerint a EventHubs fogadó)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "sampleRateInSeconds": 15,
  "ladCfg": {
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

A `resourceId` konfigurációjában egyeznie kell, hogy a virtuális gép vagy virtuálisgép-méretezési állítsa be.

* Az Azure platform metrikák diagramkészítési és riasztás tudja az erőforrás-azonosítója a virtuális gép dolgozik. Várhatóan a keresési kulcs megtalálta az adatokat a virtuális gép az erőforrás-azonosítója használatával.
* Azure automatikus skálázás használatakor, az erőforrás-azonosítója az automatikus skálázás konfigurációban meg kell egyeznie az erőforrás-azonosítója LAD használják.
* Az erőforrás-azonosítója beépített LAD által írt JsonBlobs nevét.

## <a name="view-your-data"></a>Az adatok megtekintése

Az Azure portál segítségével teljesítményadatainak megjelenítéséhez, vagy állítson be riasztásokat:

![Kép](./media/diagnostic-extension/graph_metrics.png)

A `performanceCounters` adatok mindig egy Azure Storage táblázatban vannak tárolva. Az Azure Storage API-k sok nyelvekhez és platformokhoz érhetők el.

JsonBlob mosdók küldött adatok blobot, amely a nevű tárfiók tárolja a [beállítások védett](#protected-settings). A tárfiókban tárolt adatok bármely Azure Blob Storage API-k használatával is felhasználhatnak.

Emellett a felhasználói felület eszközök segítségével érik el az adatokat az Azure Storage:

* A Visual Studio Server Explorer.
* [A Microsoft Azure Tártallózó](https://azurestorageexplorer.codeplex.com/ "Azure Tártallózó").

A Microsoft Azure Tártallózó munkamenet pillanatképe jeleníti meg a létrehozott Azure Storage-táblákat és a tárolók egy megfelelően konfigurált LAD 3.0 bővítmény a teszteléshez használt virtuális Gépen. A kép nem felel meg pontosan a [LAD 3.0 mintakonfiguráció](#an-example-lad-30-configuration).

![Kép](./media/diagnostic-extension/stg_explorer.png)

Tekintse meg a megfelelő [EventHubs dokumentáció](../../event-hubs/event-hubs-what-is-event-hubs.md) megtudhatja, hogyan EventHubs végpont közzétett üzenetek felhasználását.

## <a name="next-steps"></a>Következő lépések

* A metrika értesítések [Azure figyelő](../../monitoring-and-diagnostics/insights-alerts-portal.md) a gyűjtött metrikáihoz.
* Hozzon létre [diagramok figyelési](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) a metrikáihoz.
* Megtudhatja, hogyan [hozzon létre egy virtuálisgép-méretezési csoport](/azure/virtual-machines/linux/tutorial-create-vmss) a mérőszámok segítségével vezérelheti, automatikus skálázást.
