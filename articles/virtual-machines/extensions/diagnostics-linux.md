---
title: Azure számítás – Linux diagnosztikai bővítmény | Microsoft Docs
description: Az Azure Linux diagnosztikai bővítmény (LAD) konfigurálása az Azure-ban futó Linux rendszerű virtuális gépek metrikáinak és naplózási eseményeinek összegyűjtéséhez.
services: virtual-machines-linux
author: abhijeetgaiha
manager: sankalpsoni
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: gwallace
ms.openlocfilehash: 19aa0877c7c37083a6206e094aced40542d0ef72
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092685"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>A Linux diagnosztikai bővítmény használata a metrikák és naplók figyelésére

Ez a dokumentum a Linux diagnosztikai bővítmény 3,0-es és újabb verzióját ismerteti.

> [!IMPORTANT]
> A 2,3-es és régebbi verzióval kapcsolatos információkért tekintse meg [ezt a dokumentumot](../linux/classic/diagnostic-extension-v2.md).

## <a name="introduction"></a>Bevezetés

A Linux diagnosztikai bővítmény segítségével a felhasználók figyelheti Microsoft Azure-on futó Linux rendszerű virtuális gépek állapotát. A következő képességekkel rendelkezik:

* Összegyűjti a rendszerteljesítményi mérőszámokat a virtuális gépről, és egy kijelölt Storage-fiókban tárolja őket egy adott táblában.
* Beolvassa a naplózási eseményeket a syslog-ből, és a kijelölt Storage-fiók egy adott táblájába tárolja azokat.
* Lehetővé teszi a felhasználók számára a gyűjtött és feltöltött adatmérőszámok testreszabását.
* Lehetővé teszi a felhasználók számára a syslog-létesítmények és a gyűjtött és feltöltött események súlyossági szintjeinek testreszabását.
* Lehetővé teszi a felhasználóknak a megadott naplófájlok feltöltését egy kijelölt Storage-táblába.
* A lehetővé teszi metrikák és naplózási események küldését tetszőleges EventHub-végpontokra és JSON-formátumú blobokra a kijelölt Storage-fiókban.

Ez a bővítmény az Azure üzembe helyezési modelljeivel is működik.

## <a name="installing-the-extension-in-your-vm"></a>A bővítmény telepítése a virtuális gépen

Ezt a bővítményt a Azure PowerShell-parancsmagok, az Azure CLI-parancsfájlok, az ARM-sablonok vagy a Azure Portal használatával engedélyezheti. További információ: bővítmények [szolgáltatásai](features-linux.md).

Ezek a telepítési utasítások és egy [letölthető minta konfiguráció](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) , amely a Lad 3,0-et konfigurálja:

* rögzítheti és tárolhatja ugyanazokat a mérőszámokat, mint a LAD 2,3;
* rögzítse a fájlrendszer metrikáinak hasznos készletét, amely az új a LAD 3,0;
* a LAD 2,3; által engedélyezett alapértelmezett syslog-gyűjtemény rögzítése
* a virtuális gépek metrikáinak ábrázolására és riasztására szolgáló Azure Portal használatának engedélyezése.

A letölthető konfiguráció csupán egy példa; módosítsa a saját igényeinek megfelelően.

### <a name="prerequisites"></a>Előfeltételek

* Az **Azure Linux Agent 2.2.0-es vagy újabb verziója**. A legtöbb Azure-beli virtuális gép Linux-katalógusa tartalmaz 2.2.7 vagy újabb verziót. A `/usr/sbin/waagent -version` futtatásával erősítse meg a virtuális gépre telepített verziót. Ha a virtuális gép a vendég ügynök egy régebbi verzióját futtatja, a frissítéshez kövesse az [alábbi utasításokat](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) .
* **Azure parancssori felület (CLI)** . [Állítsa be az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) -környezetet a gépen.
* A wget parancs, ha még nem tette meg: Futtassa az `sudo apt-get install wget` parancsot.
* Egy meglévő Azure-előfizetés és egy meglévő Storage-fiók az adattároláshoz.
* A támogatott Linux-disztribúciók listája https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic#supported-linux-distributions

### <a name="sample-installation"></a>Minta telepítése

Töltse ki a megfelelő paramétereket az első három sorban, majd hajtsa végre a parancsfájlt root-ként:

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
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finallly tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

A minta konfigurációjának és tartalmának URL-címe változhat. Töltse le a portál beállítások JSON-fájljának másolatát, és szabja testre az igényeinek megfelelően. Az Ön által létrehozott összes sablonnak vagy automatizálásnak a saját példányát kell használnia, és nem kell minden alkalommal letöltenie az adott URL-címet.

### <a name="updating-the-extension-settings"></a>A bővítmény beállításainak frissítése

Miután módosította a védett vagy a nyilvános beállításokat, telepítse őket a virtuális gépre ugyanazon parancs futtatásával. Ha bármilyen változás történt a beállításokban, a rendszer elküldi a frissített beállításokat a bővítménynek. A LAD újra betölti a konfigurációt, és újraindul.

### <a name="migration-from-previous-versions-of-the-extension"></a>Áttelepítés a bővítmény korábbi verzióiból

A bővítmény legújabb verziója **3,0**. A **régi verziók (2. x) elavultak, és a 2018. július 31-ig vagy azt követően nem**tehetők közzé.

> [!IMPORTANT]
> Ez a bővítmény bevezeti a bővítmény konfigurációjának feltörésének változásait. Egy ilyen változás történt a bővítmény biztonságának javítása érdekében; Ennek eredményeképpen a 2. x verzióra visszamenőleges kompatibilitás nem tartható karban. Emellett a bővítmény közzétevője nem azonos a 2. x verzió közzétevője verziójával.
>
> Ha 2. x verzióról kíván áttérni a bővítmény ezen új verziójára, el kell távolítania a régi bővítményt (a régi közzétevő neve alatt), majd telepítenie kell a bővítmény 3. verzióját.

Javaslatok

* Telepítse a bővítményt, ha engedélyezve van az alverzió automatikus frissítése.
  * A klasszikus üzembe helyezési modellben a virtuális gépeken a "3. *" verziót kell megadni, ha a bővítményt az Azure XPLAT CLI vagy a PowerShell használatával telepíti.
  * Azure Resource Manager üzembe helyezési modell virtuális gépeken a virtuális gép központi telepítési sablonjában a "autoUpgradeMinorVersion": true "értéket kell tartalmaznia.
* Új/eltérő Storage-fiók használata a LAD 3,0-hez. Több kis inkompatibilitás van a LAD 2,3 és a LAD 3,0 között, amelyek megosztanak egy fiókot:
  * A LAD 3,0 egy másik nevű táblában tárolja a syslog-eseményeket.
  * A `builtin` metrikák counterSpecifier karakterláncai eltérnek a 3,0.

## <a name="protected-settings"></a>Védett beállítások

A konfigurációs adatok ezen készlete olyan bizalmas információkat tartalmaz, amelyeket védeni kell a nyilvános nézetből, például a tárolási hitelesítő adatokat. Ezeket a beállításokat a kiterjesztés titkosított formában továbbítja és tárolja.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Name (Név) | Value
---- | -----
storageAccountName | Annak a Storage-fióknak a neve, amelybe az adatkiterjesztést írta.
storageAccountEndPoint | választható A felhőt azonosító végpont, amelyben a Storage-fiók létezik. Ha ez a beállítás nem érhető el, a LAD alapértelmezett értéke az Azure nyilvános `https://core.windows.net`felhő. Ha Azure Germany-, Azure Government-vagy Azure China-beli Storage-fiókot szeretne használni, ennek megfelelően állítsa be ezt az értéket.
storageAccountSasToken | Egy [fiók sas](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) -jogkivonata a blob és`ss='bt'`Table Services () számára, amely a`srt='co'`tárolók és objektumok () számára érvényes, amely a hozzáadási, létrehozási`sp='acluw'`, listázási, frissítési és írási engedélyeket () biztosítja. Ne foglalja bele a kezdő kérdőjelet (?).
mdsdHttpProxy | választható A HTTP-proxy azon adatai, amelyek lehetővé teszik a bővítménynek a megadott Storage-fiókhoz és végponthoz való csatlakozást.
sinksConfig | választható Azon alternatív célhelyek részletei, amelyekhez mérőszámokat és eseményeket lehet továbbítani. A bővítmény által támogatott adattárolók konkrét részletei az alábbi szakaszokban találhatók.

Ha SAS-tokent szeretne beolvasni egy Resource Manager-sablonon belül, használja a **listAccountSas** függvényt. Példa a sablonra: [példa a függvények listázására](../../azure-resource-manager/resource-group-template-functions-resource.md#list-example).

A szükséges SAS-tokent egyszerűen létrehozhatja a Azure Portalon keresztül.

1. Válassza ki azt az általános célú Storage-fiókot, amelyhez a bővítményt írni szeretné
1. A bal oldali menü beállítások részében válassza a "közös hozzáférésű aláírás" lehetőséget.
1. Tegye meg a megfelelő szakaszokat az előzőekben leírtak szerint
1. Kattintson az "SAS előállítása" gombra.

![image](./media/diagnostics-linux/make_sas.png)

Másolja a generált SAS-t a storageAccountSasToken mezőbe; eltávolítja a kezdő kérdőjelet ("?").

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

Ez a választható szakasz azokat a további célhelyeket határozza meg, amelyekhez a bővítmény elküldi az általa gyűjtött adatokat. A "fogadó" tömb tartalmaz egy objektumot minden további adatfogadóhoz. A "type" attribútum meghatározza az objektum többi attribútumát.

Elem | Value
------- | -----
name | A bővítmény konfigurációjában máshol a fogadóhoz való hivatkozáshoz használt karakterlánc.
type | A definiált fogadó típusa. Meghatározza az ilyen típusú példányok egyéb értékeit (ha vannak ilyenek).

A Linux diagnosztikai bővítmény 3,0-es verziója két fogadó típust támogat: EventHub és JsonBlob.

#### <a name="the-eventhub-sink"></a>A EventHub fogadó

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

A "sas URL" bejegyzés tartalmazza a teljes URL-címet, beleértve az SAS-tokent is, az Event hub számára, amelyre közzé kell tenni az adott adat. A LAD-nek szüksége van egy olyan házirendre, amely engedélyezi a küldési jogcímet. Példa:

* Hozzon létre egy nevű Event Hubs névteret`contosohub`
* Hozzon létre egy Event hubot a nevű névtérben.`syslogmsgs`
* Hozzon létre egy megosztott hozzáférési szabályzatot az nevű `writer` esemény-hubhoz, amely engedélyezi a jogcím küldését.

Ha a SAS-t a 2018-as éjféli UTC szerint hozta létre, akkor a sas URL értéke a következő lehet:

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

A Event Hubs SAS-jogkivonatok létrehozásával kapcsolatos további információkért tekintse meg [ezt a weblapot](../../event-hubs/event-hubs-authentication-and-security-model-overview.md).

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

Az JsonBlob-fogadóba irányított adattárolók tárolása a blobokban történik az Azure Storage-ban. A LAD minden példánya minden órában létrehoz egy blobot minden egyes fogadó nevénél. Az egyes Blobok mindig az objektum szintaktikai érvényes JSON-tömbjét tartalmazzák. Az új bejegyzések a tömbhöz való atomi hozzáadásával lettek hozzáadva. A Blobok tárolása a fogadóval megegyező nevű tárolóban történik. A blob-tárolók neveihez tartozó Azure Storage-szabályok a JsonBlob-mosdók nevére érvényesek: 3 és 63 kisbetűs alfanumerikus ASCII-karakter vagy kötőjel.

## <a name="public-settings"></a>Nyilvános beállítások

Ez a struktúra különböző beállításokat tartalmaz, amelyek a bővítmény által gyűjtött adatokat vezérlik. Az egyes beállítások megadása nem kötelező. Ha megadja `ladCfg`, azt is meg kell adnia `StorageAccount`.

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Elem | Value
------- | -----
StorageAccount | Annak a Storage-fióknak a neve, amelybe az adatkiterjesztést írta. A [védett beállításokban](#protected-settings)megadott névnek meg kell egyeznie.
mdsdHttpProxy | választható Ugyanaz, mint a [védett beállításokban](#protected-settings). A nyilvános értéket felülbírálja a magánhálózati érték, ha be van állítva. Helyezzen el egy titkos kulcsot (például egy jelszót) tartalmazó proxybeállításokat a [védett beállításokban](#protected-settings).

A többi elemet a következő szakaszokban részletesen ismertetjük.

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

Ez a választható struktúra szabályozza a metrikák és naplók összegyűjtését az Azure metrika szolgáltatásba és más adatnyelők számára történő kézbesítéshez. A `performanceCounters` vagy`syslogEvents` a vagy a mindkettőt kell megadnia. Meg kell adnia a `metrics` struktúrát.

Elem | Value
------- | -----
eventVolume | választható A Storage-táblán belül létrehozott partíciók számának szabályozása. A (,) `"Large"`vagy `"Medium"` `"Small"`a () egyikének kell lennie. Ha nincs megadva, az alapértelmezett érték `"Medium"`:.
sampleRateInSeconds | választható A nyers (nem aggregált) mérőszámok gyűjteménye közötti alapértelmezett időköz. A legkisebb támogatott mintavételi sebesség 15 másodperc. Ha nincs megadva, az alapértelmezett érték `15`:.

#### <a name="metrics"></a>metrikák

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Elem | Value
------- | -----
resourceId | A virtuális gép vagy a virtuálisgép-méretezési csoport Azure Resource Manager erőforrás-azonosítója, amelyre a virtuális gép tartozik. Ezt a beállítást akkor is meg kell adni, ha a konfigurációban bármilyen JsonBlob-fogadó használatban van.
scheduledTransferPeriod | Az összesített metrikák kiszámításának és az Azure-mérőszámokra való átadásának gyakorisága 8601-as Időintervallumként kifejezve. A legkisebb átvitel időtartama 60 másodperc, azaz PT1M. Legalább egy scheduledTransferPeriod meg kell adnia.

A performanceCounters szakaszban megadott mérőszámok mintáit 15 másodpercenként vagy a számlálóhoz explicit módon meghatározott mintavételi sebességgel gyűjti a rendszer. Ha több scheduledTransferPeriod-frekvencia is megjelenik (ahogy a példában is látható), az egyes összesítéseket egymástól függetlenül számítjuk ki.

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

Ez a választható szakasz szabályozza a metrikák gyűjteményét. A nyers mintákat minden [scheduledTransferPeriod](#metrics) összesíti az alábbi értékek előállításához:

* középérték
* minimum
* maximum
* utolsó összegyűjtött érték
* az Összesítés kiszámításához használt nyers minták száma

Elem | Value
------- | -----
fogadóként | választható Egy vesszővel elválasztott lista azoknak a nyelőknek a neveiről, amelyekhez a LAD összesített metrikai eredményeket küld. Minden összesített metrika közzé lesz téve az egyes felsorolt fogadók számára. Lásd: [sinksConfig](#sinksconfig). Példa: `"EHsink1, myjsonsink"`.
type | A metrika tényleges szolgáltatóját azonosítja.
Osztály | A "Counter" kifejezéssel együtt a szolgáltató névterében lévő adott mérőszámot azonosítja.
számláló | A "class" kifejezéssel együtt a szolgáltató névterében lévő adott mérőszámot azonosítja.
counterSpecifier | Az Azure-metrikák névterében lévő adott mérőszámot azonosítja.
condition | választható Kiválasztja az objektum egy adott példányát, amelyre a metrika vonatkozik, vagy kiválasztja az összesítést az adott objektum összes példánya között. További információ: `builtin` metrika-definíciók.
Mintavételi | Az 8601-es intervallum, amely meghatározza, hogy a rendszer milyen mértékben gyűjtsön nyers mintákat a metrikához. Ha nincs beállítva, a gyűjtési időközt a [sampleRateInSeconds](#ladcfg)érték határozza meg. A legrövidebb támogatott mintavételi sebesség 15 másodperc (PT15S).
egység | A következő karakterláncok egyikének kell lennie: "Count", "Bytes", "Seconds", "százalék", "CountPerSecond", "BytesPerSecond", "ezredmásodperc". Meghatározza a metrika egységét. Az összegyűjtött adatok felhasználói a begyűjtött adatértékeket az egységnek megfelelően elvárják. A mező figyelmen kívül hagyja ezt a mezőt.
displayName | A címkét (a társított területi beállítás által megadott nyelven) az Azure-Mérőszámokban lévő adatokhoz kell csatolni. A mező figyelmen kívül hagyja ezt a mezőt.

A counterSpecifier tetszőleges azonosító. A metrikák felhasználói, például az Azure Portal ábrázolási és riasztási funkció, a counterSpecifier használja "kulcsként", amely egy metrika vagy egy metrika egy példányát azonosítja. A `builtin` metrikák esetében ajánlott olyan counterSpecifier-értékeket használni, amelyek a `/builtin/`következővel kezdődnek:. Ha egy metrika egy adott példányát gyűjti be, javasoljuk, hogy a példány azonosítóját csatolja a counterSpecifier értékhez. Néhány példa:

* `/builtin/Processor/PercentIdleTime`– Az összes vCPU átlagos üresjárati idő
* `/builtin/Disk/FreeSpace(/mnt)`– Szabad terület a/mnt fájlrendszer számára
* `/builtin/Disk/FreeSpace`– Az összes csatlakoztatott fájlrendszer esetében átlagosan szabad terület

Sem a LAD, sem a Azure Portal a counterSpecifier értéket várja a mintázatnak megfelelően. Konzisztensnek kell lennie a counterSpecifier értékeinek összeállításában.

Ha megadja `performanceCounters`, a Lad mindig az Azure Storage-ban lévő táblába ír egy adatot. A JSON-blobokban és/vagy Event Hubsban is megadhatja ugyanazokat az adatfájlokat, de nem tilthatja le az adattárolást egy táblába. A diagnosztikai bővítmény minden olyan példánya, amely ugyanazt a Storage-fióknevet és-végpontot használja, hozzá kell adnia a metrikákat és a naplókat ugyanahhoz a táblához. Ha túl sok virtuális gép ír ugyanarra a táblázatos partícióra, az Azure képes a partíción lévő írások szabályozására. A eventVolume beállítás azt eredményezi, hogy a bejegyzések 1 (kis), 10 (közepes) vagy 100 (nagyméretű) különböző partíciók között oszlanak el. A "közepes" érték általában elegendő ahhoz, hogy a forgalom ne legyen szabályozva. A Azure Portal Azure mérőszámok funkciója a táblázat adatait használja a diagramok létrehozásához vagy riasztások elindításához. A tábla neve a következő sztringek összefűzése:

* `WADMetrics`
* A táblázatban tárolt összesített értékek "scheduledTransferPeriod"
* `P10DV2S`
* Egy "ÉÉÉÉHHNN" formátumú dátum, amely 10 naponta változik

Ilyenek például `WADMetricsPT1MP10DV2S20170609` `WADMetricsPT1HP10DV2S20170410` a és a.

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

Ez a választható szakasz a naplózási események a syslog-ből történő gyűjtését vezérli. Ha a szakasz nincs megadva, a rendszer egyáltalán nem rögzíti a syslog-eseményeket.

A syslogEventConfiguration-gyűjtemény egyetlen bejegyzést tartalmaz minden olyan syslog-létesítményhez, amely az összes fontos eszközt felvette. Ha a minSeverity "nincs" egy adott létesítmény esetében, vagy ha a létesítmény egyáltalán nem jelenik meg az elemben, akkor a rendszer nem rögzíti az adott létesítményből származó eseményeket.

Elem | Value
------- | -----
fogadóként | Vesszővel tagolt lista azoknak a fogadóknak a neveiről, amelyeken az egyes naplózási események közzé vannak téve. A syslogEventConfiguration korlátozásait egyeztető összes naplózási eseményt közzéteszi az egyes felsorolt fogadók. Példa: "EHforsyslog"
kódban | A syslog-létesítmény neve (például "log\_user" vagy "log\_LOCAL0"). A teljes listához tekintse meg a [syslog man oldalának](http://man7.org/linux/man-pages/man3/syslog.3.html) "létesítmény" szakaszát.
minSeverity | Syslog súlyossági szintje (például "log\_err" vagy "log\_info"). Tekintse meg a [syslog man oldalának](http://man7.org/linux/man-pages/man3/syslog.3.html) "szint" szakaszát a teljes listához. A bővítmény rögzíti a létesítménynek a megadott szinten vagy felett eljuttatott eseményeket.

Ha megadja `syslogEvents`, a Lad mindig az Azure Storage-ban lévő táblába ír egy adatot. A JSON-blobokban és/vagy Event Hubsban is megadhatja ugyanazokat az adatfájlokat, de nem tilthatja le az adattárolást egy táblába. Az ehhez a táblához tartozó particionálási viselkedés megegyezik a című témakörben leírtak `performanceCounters`szerint. A tábla neve a következő sztringek összefűzése:

* `LinuxSyslog`
* Egy "ÉÉÉÉHHNN" formátumú dátum, amely 10 naponta változik

Ilyenek például `LinuxSyslog20170609` `LinuxSyslog20170410` a és a.

### <a name="perfcfg"></a>perfCfg

Ez a választható szakasz vezérli a [](https://github.com/Microsoft/omi) tetszőleges típusú adatlekérdezések végrehajtását.

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

Elem | Value
------- | -----
névtér | választható Az a-t tartalmazó-névtér, amelyen belül a lekérdezés végrehajtása történik. Ha nincs megadva, az alapértelmezett érték a [System Center platformfüggetlen szolgáltatók](https://scx.codeplex.com/wikipage?title=xplatproviders&referringTitle=Documentation)által megvalósított "root/SCX".
query | A végrehajtandó a kipróbálható adatlekérdezés.
table | választható Az Azure Storage-tábla a kijelölt Storage-fiókban (lásd a [védett beállításokat](#protected-settings)).
frequency | választható A lekérdezés végrehajtása közötti másodpercek száma. Az alapértelmezett érték 300 (5 perc); a minimális érték 15 másodperc.
fogadóként | választható A további mosdók neveinek vesszővel tagolt listája, amelybe a nyers minta metrikájának eredményeit közzé kell tenni. A nyers minták összesítését a bővítmény vagy az Azure-metrika számítja ki.

Meg kell adni a "Table" vagy a "mosogató", vagy mindkettőt.

### <a name="filelogs"></a>fileLogs

A naplófájlok rögzítését vezérli. A LAD rögzíti az új szövegsorok írását a fájlba, és a táblázat soraiba és/vagy bármely megadott mosogatóba (JsonBlob vagy EventHub) írja azokat.

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Elem | Value
------- | -----
file | A figyelni és rögzíteni kívánt naplófájl teljes elérési útja. Az elérési útnak egyetlen fájlt kell megadnia; nem lehet könyvtárat átnevezni, és nem tartalmazhat helyettesítő karaktereket.
table | választható Az Azure Storage-tábla a kijelölt Storage-fiókban (a védett konfigurációban megadott módon), amelybe a fájl "farok" új sorai íródnak.
fogadóként | választható Vesszővel tagolt lista azoknak a további mosogatóknak a neveiről, amelyeknek a naplózási sorai elküldése megtörténjen.

Meg kell adni a "Table" vagy a "mosogató", vagy mindkettőt.

## <a name="metrics-supported-by-the-builtin-provider"></a>A beépített szolgáltató által támogatott metrikák

A beépített metrikai szolgáltató a felhasználók széles köréhez legérdekesebb mérőszámok forrása. Ezek a metrikák öt átfogó osztályba sorolhatók:

* Processzor
* Memory (Memória)
* Network (Hálózat)
* Fájlrendszer
* Lemez

### <a name="builtin-metrics-for-the-processor-class"></a>a processzor osztályának beépített metrikái

A metrikák processzor-osztálya információt nyújt a virtuális gép processzor-használatáról. A százalékok összesítése során az eredmény az összes processzor átlaga. Két vCPU virtuális gépen, ha egy vCPU 100%-ban foglalt, míg a másik a 100%-os üresjáratban volt, a jelentett PercentIdleTime a következő lesz: 50. Ha az egyes vCPU ugyanazon időszakra vonatkozóan 50%-ban foglalt, akkor a jelentett eredmény a következő lesz: 50. Egy négy vCPU virtuális gépen egy vCPU 100%-os foglalt és a többi tétlen, a jelentett PercentIdleTime a következő lesz: 75.

számláló | Jelentés
------- | -------
PercentIdleTime | Az összesítési időszakban a kernel üresjárati ciklusát végrehajtó munkafolyamatok százalékos aránya
PercentProcessorTime | Nem tétlen szálat végrehajtó idő százalékos aránya
PercentIOWaitTime | Az i/o-műveletek befejezésére várakozási idő százalékos aránya
PercentInterruptTime | Hardver-/szoftver-megszakítások és-DPC végrehajtási idejének százalékos aránya (késleltetett eljárási hívások)
PercentUserTime | A nem tétlen idő az összesítési időszakban, a felhasználó által a normál prioritásban eltöltött idő százalékos aránya
PercentNiceTime | A nem tétlen időpontig a lecsökkentett (Nizza) prioritásban eltöltött százalékarány
PercentPrivilegedTime | A nem üresjárati idő, a rendszerjogosultságú (kernel) módban eltöltött százalék

Az első négy számláló összege 100%. Az utolsó három számláló a 100%-ot is összegzi; a PercentProcessorTime, a PercentIOWaitTime és a PercentInterruptTime összegét osztják meg.

Ha egyetlen mérőszámot szeretne beolvasni az összes processzor között, `"condition": "IsAggregate=TRUE"`állítsa be a következőt:. Egy adott processzor metrikájának beszerzéséhez, például egy négy vCPU virtuális gép második logikai processzorához, állítsa be `"condition": "Name=\\"1\\""`a következőt:. A logikai processzorok száma a tartományban `[0..n-1]`van.

### <a name="builtin-metrics-for-the-memory-class"></a>beépített metrikák a memória osztályhoz

A metrikák memória osztálya információt nyújt a memória kihasználtságáról, a lapozásról és a cseréről.

számláló | Jelentés
------- | -------
AvailableMemory | Rendelkezésre álló fizikai memória a MiB-ben
PercentAvailableMemory | Rendelkezésre álló fizikai memória a teljes memória százalékában
UsedMemory | Használatban lévő fizikai memória (MiB)
PercentUsedMemory | Használatban lévő fizikai memória a teljes memória százalékában
PagesPerSec | Lapozófájlok összesen (olvasás/írás)
PagesReadPerSec | Az áruházból beolvasott lapok (fájl, programfájl, leképezett fájl stb.)
PagesWrittenPerSec | Az áruházba írt lapok (fájl cseréje, leképezett fájl stb.)
AvailableSwap | Nem használt lapozófájl (MiB)
PercentAvailableSwap | Fel nem használt swap-terület a teljes swap százalékaként
UsedSwap | Használatban lévő swap-terület (MiB)
PercentUsedSwap | Használatban lévő swap-terület a teljes swap százalékaként

A metrikák ezen osztálya csak egyetlen példánnyal rendelkezik. A "Condition" attribútum nem rendelkezik hasznos beállításokkal, és el kell hagyni.

### <a name="builtin-metrics-for-the-network-class"></a>a hálózati osztály beépített metrikái

A metrikák hálózati osztálya a rendszerindítás óta az egyes hálózati adaptereken lévő hálózati tevékenységekről nyújt információt. A LAD nem tesz elérhetővé sávszélesség-metrikákat, amelyek a gazdagép metrikái alapján kérhetők le.

számláló | Jelentés
------- | -------
BytesTransmitted | A rendszerindítás óta elindított bájtok száma összesen
BytesReceived | A rendszerindítás óta fogadott bájtok összesen
BytesTotal | A rendszerindítás óta küldött vagy fogadott bájtok összesen
PacketsTransmitted | A rendszerindítás óta küldött csomagok összesen
PacketsReceived | A rendszerindítás óta fogadott csomagok összesen
TotalRxErrors | Fogadási hibák száma a rendszerindítás óta
TotalTxErrors | Küldési hibák száma a rendszerindítás óta
TotalCollisions | A hálózati portok által a rendszerindítás óta jelentett ütközések száma

 Bár ez az osztály példányt tartalmaz, a LAD nem támogatja az összes hálózati eszközre összesített hálózati metrikák rögzítését. Egy adott csatoló (például ETH0) metrikáinak beszerzéséhez állítsa be `"condition": "InstanceID=\\"eth0\\""`a következőt:.

### <a name="builtin-metrics-for-the-filesystem-class"></a>a fájlrendszer osztályának beépített metrikái

A metrikák fájlrendszer-osztálya információt nyújt a fájlrendszer használatáról. Az abszolút és a százalékos értékek a jelentések szerint a szokásos felhasználó számára (nem root) jelennek meg.

számláló | Jelentés
------- | -------
FreeSpace | Rendelkezésre álló lemezterület bájtban
UsedSpace | Felhasznált lemezterület bájtban
PercentFreeSpace | Szabad terület százalékos aránya
PercentUsedSpace | Felhasznált terület százalékos aránya
PercentFreeInodes | A fel nem használt inode százaléka
PercentUsedInodes | Az összes fájlrendszerben összefoglalt lefoglalt (használatban lévő) inode százalékaránya
BytesReadPerSecond | Olvasott bájtok másodpercenként
BytesWrittenPerSecond | Másodpercenként írt bájtok száma
BytesPerSecond | Olvasott vagy írt bájtok másodpercenként
ReadsPerSecond | Olvasási műveletek másodpercenként
WritesPerSecond | Írási műveletek másodpercenként
TransfersPerSecond | Olvasási vagy írási műveletek másodpercenként

A rendszer az összes fájlrendszer összesített értékeit a beállítással `"condition": "IsAggregate=True"`szerezheti be. Az adott csatlakoztatott fájlrendszer, például a "/mnt" értékeit a beállítással `"condition": 'Name="/mnt"'`lehet beolvasni. 

**MEGJEGYZÉS**: Ha a JSON helyett az Azure Portalt használja, a helyes feltétel mező űrlap a name = '/mnt '.

### <a name="builtin-metrics-for-the-disk-class"></a>beépített metrikák a lemez osztályhoz

A metrikák lemezes osztálya információt nyújt a lemezes eszközök használatáról. Ezek a statisztikák a teljes meghajtóra vonatkoznak. Ha több fájlrendszer van egy eszközön, az eszköz számlálói hatékonyan, összesítve lesznek az összesre.

számláló | Jelentés
------- | -------
ReadsPerSecond | Olvasási műveletek másodpercenként
WritesPerSecond | Írási műveletek másodpercenként
TransfersPerSecond | Műveletek másodpercenkénti száma
AverageReadTime | Olvasási művelet átlagos másodpercenkénti száma
AverageWriteTime | Írási művelet átlagos másodpercenkénti száma
AverageTransferTime | Művelet átlagos másodpercenkénti száma
AverageDiskQueueLength | A várólistán lévő lemezes műveletek átlagos száma
ReadBytesPerSecond | Olvasott bájtok másodpercenkénti száma
WriteBytesPerSecond | A másodpercenként írt bájtok száma
BytesPerSecond | Olvasott vagy írt bájtok másodpercenkénti száma

Az összes lemez összesített értékei a beállítás `"condition": "IsAggregate=True"`alapján szerezhetők be. Egy adott eszközre vonatkozó információk (például/dev/sdf1) lekéréséhez állítsa be `"condition": "Name=\\"/dev/sdf1\\""`a következőt:.

## <a name="installing-and-configuring-lad-30-via-cli"></a>LAD 3,0 telepítése és konfigurálása a CLI-n keresztül

Feltételezve, hogy a védett beállítások a PrivateConfig. JSON fájlban vannak, és a nyilvános konfigurációs adatok a PublicConfig. JSON fájlban találhatók, futtassa ezt a parancsot:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

A parancs feltételezi, hogy az Azure CLI Azure Resource Management (ARM) üzemmódját használja. A klasszikus üzembe helyezési modell (ASM) virtuális gépei konfigurálásához váltson az "ASM" módra`azure config mode asm`(), és hagyja ki az erőforráscsoport nevét a parancsban. További információkért lásd a többplatformos [CLI dokumentációját](https://docs.microsoft.com/azure/xplat-cli-connect).

## <a name="an-example-lad-30-configuration"></a>Példa LAD 3,0-konfigurációra

Az előző definíciók alapján Íme egy példa a 3,0-es, néhány magyarázattal ellátott bővítmény-konfigurációra. Ha alkalmazni szeretné a mintát az esetére, használja a saját Storage-fiók nevét, a fiók SAS-tokenjét és a EventHubs SAS-tokeneket.

### <a name="privateconfigjson"></a>PrivateConfig.json

Ezek a magánhálózati beállítások a következőket konfigurálják:

* egy Storage-fiók
* egy megfelelő fiók SAS-jogkivonata
* több mosogató (JsonBlob vagy EventHubs SAS-tokenekkel)

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

Ezek a nyilvános beállítások a következőt okozzák:

* Százalék-processzor-idő és felhasznált lemezterület-metrikák feltöltése a `WADMetrics*` táblába
* Üzenetek feltöltése a syslog-létesítményből a "user" és a "fontosság `LinuxSyslog*` " információkkal a táblába
* Nyers PercentProcessorTime-és PercentIdleTime-lekérdezési eredmények feltöltése a nevesített `LinuxCPU` táblázatba
* A fájlban `/var/log/myladtestlog` lévő hozzáfűzött sorok feltöltése a `MyLadTestLog` táblába

A rendszer minden esetben feltölti az adatfájlokat a következőre:

* Azure Blob Storage (a tároló neve a JsonBlob-fogadóban definiált)
* EventHubs-végpont (a EventHubs-fogadóban megadott módon)

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

`resourceId` A konfigurációnak meg kell egyeznie a virtuális gép vagy a virtuálisgép-méretezési csoport nevével.

* Az Azure platform metrikái a diagramok és a riasztások megismerik annak a virtuális gépnek a resourceId, amelyen dolgozik. A rendszer a resourceId a keresési kulcs használatával keresi meg a virtuális gép adatait.
* Ha az Azure automatikus méretezést használja, az automatikus skálázási konfiguráció resourceId meg kell egyeznie a LAD által használt resourceId.
* A resourceId a LAD által írt JsonBlobs nevére épül.

## <a name="view-your-data"></a>Az adatai megtekintése

A Azure Portal használatával megtekintheti a teljesítményadatokat, vagy beállíthatja a riasztásokat:

![image](./media/diagnostics-linux/graph_metrics.png)

Az `performanceCounters` adattárolást mindig egy Azure Storage-táblában tárolja a rendszer. Az Azure Storage API-jai számos nyelven és platformon elérhetők.

A JsonBlob elküldhető adattárolók a [védett beállítások](#protected-settings)nevű Storage-fiókban található blobokban vannak tárolva. A Blobok adatai bármely Azure Blob Storage API használatával felhasználhatók.

Emellett használhatja ezeket a felhasználói felületi eszközöket az Azure Storage-ban tárolt adatok eléréséhez:

* Visual Studio Server Explorer.
* [Microsoft Azure Storage Explorer] (https://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer").

A Microsoft Azure Storage Explorer-munkamenet ezen pillanatképe a generált Azure Storage-táblákat és-tárolókat mutatja be egy, a teszt virtuális gépen megfelelően konfigurált, LAD 3,0-es bővítménnyel. A rendszerkép nem egyezik pontosan a [LAD 3,0](#an-example-lad-30-configuration)-konfigurációval.

![image](./media/diagnostics-linux/stg_explorer.png)

A EventHubs-végponton közzétett üzenetek felhasználásának megismeréséhez tekintse meg a vonatkozó [EventHubs dokumentációját](../../event-hubs/event-hubs-what-is-event-hubs.md) .

## <a name="next-steps"></a>További lépések

* Metrikai riasztások [](../../monitoring-and-diagnostics/insights-alerts-portal.md) létrehozása Azure monitorban a begyűjtött mérőszámokhoz.
* [Figyelési diagramok](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) létrehozása a mérőszámokhoz.
* Megtudhatja, hogyan [hozhat létre virtuálisgép](../linux/tutorial-create-vmss.md) -méretezési készletet a metrikák használatával az automatikus skálázás szabályozásához.
