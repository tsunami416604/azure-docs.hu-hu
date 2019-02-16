---
title: Az Azure Compute - Linux diagnosztikai bővítmény |} A Microsoft Docs
description: Hogyan konfigurálható az Azure Linux diagnosztikai bővítmény (LAD) begyűjtése és az Azure-ban futó Linux rendszerű virtuális gépek naplózása.
services: virtual-machines-linux
author: abhijeetgaiha
manager: sankalpsoni
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: agaiha
ms.openlocfilehash: 434971e707cdca62c76ede9f295e7af20aa4cc3f
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313534"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Linux diagnosztikai bővítmény használatával figyelheti a metrikák és naplók

Ez a dokumentum ismerteti a 3.0-s vagy újabb, a Linux diagnosztikai bővítmény verziója.

> [!IMPORTANT]
> 2.3-as és a régebbi verzióval kapcsolatos információkért lásd: [ebben a dokumentumban](../linux/classic/diagnostic-extension-v2.md).

## <a name="introduction"></a>Bevezetés

A Linux diagnosztikai bővítmény segítségével egy felhasználó figyelése a Microsoft Azure-ban futó Linux virtuális gép állapotát. Az alábbi képességekkel rendelkezik:

* Rendszer teljesítmény-mérőszámokat gyűjti össze a virtuális Gépet, és a megadott tábla kijelölt tárfiókban tárolja azokat.
* Alkalmazásnapló-események lekéri a syslog, és a egy adott tábla a kijelölt tárfiókban tárolja azokat.
* Lehetővé teszi a felhasználók számára a gyűjtött és feltöltött adatok metrikáinak testreszabása.
* Lehetővé teszi a felhasználóknak a syslog létesítmények és a súlyossági szintek az események által gyűjtött és feltöltött testreszabásához.
* Lehetővé teszi a felhasználók egy kijelölt tárolótáblában megadott naplófájlok feltöltéséhez.
* Támogatja az mérőszámokban és naplófájlokban az események küldése tetszőleges EventHub végpontok és a blobok JSON-formátumú a kijelölt tárfiókban.

Ez a bővítmény mind az Azure üzembe helyezési modellel működik.

## <a name="installing-the-extension-in-your-vm"></a>A bővítmény telepítése a virtuális gépen

Ez a bővítmény az Azure PowerShell-parancsmagokat, Azure CLI-szkriptek, ARM-sablonok vagy az Azure portal használatával engedélyezheti. További információkért lásd: [bővítmény szolgáltatásai](features-linux.md).

Ezen telepítési utasításokat és a egy [letölthető, mintául szolgáló konfigurációs](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) LAD 3.0-s verziójának konfigurálása:

* rögzítését és tárolását a metrikák, LAD 2.3; által megadva
* egy fájl rendszermérőszámokat, LAD 3.0; új hasznos készletét rögzítése
* az alapértelmezett a rendszernaplók gyűjtése LAD 2.3; által engedélyezett rögzítése
* Engedélyezze az Azure portal felületének diagramkészítési, és a VM-metrikák riasztás céljából.

A letölthető konfigurációs csak egy példa; Módosítsa azt a saját igényeinek megfelelően.

### <a name="prerequisites"></a>Előfeltételek

* **Az Azure Linux-ügynök 2.2.0-ás verzió vagy újabb**. A legtöbb Azure-beli virtuális gép Linuxos katalógus lemezképek tartalmaznak 2.2.7 verzió vagy újabb. Futtatás `/usr/sbin/waagent -version` a virtuális gépen telepített verzió megerősítéséhez. Ha a virtuális gép a vendégügynök egy régebbi verziója fut, hajtsa végre a [ezek az utasítások](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) frissíteni.
* **Azure parancssori felület (CLI)**. [Az Azure parancssori felület beállítása](https://docs.microsoft.com/cli/azure/install-azure-cli) környezet a gépen.
* A wget parancs, ha már nincs: Futtassa az `sudo apt-get install wget` parancsot.
* Meglévő Azure-előfizetése és a egy meglévő tárfiókot, benne az adatok tárolásához.
* Támogatott Linux-disztribúciók listáját be van kapcsolva https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic#supported-linux-distributions

### <a name="sample-installation"></a>Minta telepítése

Töltse ki a megfelelő paramétereket lévő első három sorát, majd hajtsa végre ezt a legfelső szintű parancsfájlt:

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

A minta konfigurálásához és annak tartalmát, hogy az URL-cím változhatnak. Töltse le a portálbeállítások JSON-fájl egy másolatát, és testre szabni az igényeinek. Bármely sablonok vagy az automation hozhat létre a saját példányát ahelyett, hogy minden alkalommal, amikor letölti a URL-CÍMRE kell használnia.

### <a name="updating-the-extension-settings"></a>A bővítmény-beállítások frissítése

Miután módosította a védett vagy a nyilvános beállításokat, telepíthet a a virtuális gép ugyanazt a parancsot futtatja. Ha bármi módosul, a beállítások, a frissített beállításokat kapnak a bővítményt. LAD újra betölti a konfigurációt, és újraindítja az magát.

### <a name="migration-from-previous-versions-of-the-extension"></a>Áttelepítés korábbi verziókról a bővítmény

A bővítmény legújabb verziója **3.0**. **Az összes olyan régi verzió (2.x) elavultak, és lehet, hogy közzé nem tett napon vagy azután 2018. július 31-ig**.

> [!IMPORTANT]
> Ez a bővítmény kompatibilitástörő változásokat vezet be, a bővítmény konfigurációját. Egy ilyen módosítás a bővítményt; a biztonság növelése érdekében Ennek eredményeképpen visszamenőleges kompatibilitást 2.x sikerült nem fenn kell tartani. Emellett a bővítmény kiadója ehhez a kiterjesztéshez eltér attól a közzétevő a 2.x verziójához.
>
> Ez a bővítmény új verziója 2.x át, távolítsa el a régi bővítményt (alatt a régi közzétevő neve), majd 3-as verziójú a bővítmény telepítése.

Javaslatok:

* A bővítmény engedélyezve van az automatikus alverzió frissítés telepítése.
  * A klasszikus üzemi modell virtuális gépek adja meg "3.*" verzióval Azure XPLAT parancssori felület vagy a Powershellen keresztül a bővítmény telepítésekor.
  * Az Azure Resource Manager-alapú modell virtuális gépek, adjon meg a(z) "autoUpgradeMinorVersion": true "az a virtuális gép központi telepítési sablont.
* Használjon egy új/eltérő tárfiókot LAD 3.0. Több kis között inkompatibilitás merül fel LAD 2.3-as és LAD 3.0, amelyek megosztása a feladatátvételnek fiók:
  * LAD 3.0 tárolja a syslog-események egy tábla egy másik névvel.
  * A karakterláncok a counterSpecifier `builtin` metrikák LAD 3.0 eltérőek.

## <a name="protected-settings"></a>Védett beállításai

Konfigurációs adatok ezen készlete a bizalmas adatokat, amelyeket védeni kell a nyilvánosság elől, például a tároló hitelesítő adatait tartalmazza. Ezeket a beállításokat a továbbított adatok köre és a bővítmény titkosított formában tárolja.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Name (Név) | Érték
---- | -----
storageAccountName | A bővítmény írja adatokat, amelyben a tárfiók neve.
storageAccountEndPoint | (nem kötelező) A végpont azonosítja a felhőbe, amelyben a tárfiók létezik. Ha ez a beállítás hiányzik, LAD alapértelmezés szerint az Azure nyilvános felhő `https://core.windows.net`. Használja a tárfiókot az Azure Germany, az Azure Government vagy Azure China, megfelelően állítsa ezt az értéket.
storageAccountSasToken | Egy [fiók SAS-jogkivonatát](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) Blob és Table szolgáltatások (`ss='bt'`), a tárolók és objektumok érvényes (`srt='co'`), mely engedélyezi hozzáadása, létrehozása, listázása, frissítése és írási engedélyekkel (`sp='acluw'`). Tegye *nem* közé tartozik a vezető kérdőjel (?).
mdsdHttpProxy | (nem kötelező) HTTP-proxyadatok csatlakozni a megadott tárfiók és a végpont a bővítmény engedélyezéséhez szükséges.
sinksConfig | (nem kötelező) Alternatív destinations, amelyhez metrikákkal és eseményekkel is üzembe helyezhető részletei. Minden egyes adatfogadó a bővítmény által támogatott konkrét részleteit az alábbi szakaszok a terjed ki.


> [!NOTE]
> A bővítmény egy Azure-beli sablon üzembe helyezésekor, a storage-fiók és a SAS-jogkivonatot kell létrehozni előre és majd átadja a sablonhoz. Nem lehet üzembe helyezése egy virtuális gép, tárfiók, és nem a bővítmény konfigurálása egyetlen sablonban. A sablonon belül egy SAS-token létrehozása jelenleg nem támogatott.

Egyszerűen összeállíthatja a szükséges SAS-jogkivonatát az Azure Portalon keresztül.

1. Az általános célú tárfiók, akit be kíván írni a bővítmény kiválasztása
1. Válassza a "Közös hozzáférésű jogosultságkód" a bal oldali menü Beállítások részéből
1. Győződjön meg a fentieknek megfelelő szakaszait
1. A "Generate-SAS" gombra.

![image](./media/diagnostics-linux/make_sas.png)

A generált SAS másolja be a storageAccountSasToken mező; Távolítsa el a vezető kérdőjel ("?").

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

Ez az opcionális szakasz határozza meg a további destinations, amelyhez a bővítmény elküldi a gyűjtött adatokat. A "sink" tömb a minden további adatokat fogadó objektumot tartalmaz. A "type" attribútum meghatározza, hogy az objektum más attribútumokat.

Elem | Érték
------- | -----
név | Egy karakterlánc lehet hivatkozni a fogadó máshol a bővítmény konfigurációjába.
type | A folyamatban meghatározott fogadó típusa. Meghatározza, hogy a többi értéket (ha vannak) az ilyen típusú példányok.

A Linux diagnosztikai bővítmény 3.0-s verziója támogatja a két fogadó típusa: Az EventHub, és JsonBlob.

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

A "SAS URL-címmel" bejegyzés tartalmazza a teljes URL-címet, SAS-token, az eseményközpont, amelyhez adatokat közzé kell tenni. LAD szükséges egy SAS-szabályzat, amely lehetővé teszi, hogy a Küldés elnevezési jogcím. Példa:

* Hozzon létre egy Event Hubs-névtér neve `contosohub`
* Event Hub létrehozása a névtér neve `syslogmsgs`
* Megosztott hozzáférési szabályzat létrehozása az eseményközpontban nevű `writer` , amely lehetővé teszi a küldési jogcím

Ha a létrehozott SAS jó UTC szerint éjfélig 2018. január 1., amíg az SAS URL-címmel érték lehet:

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

További információ a SAS-tokeneket generáló Event hubs: [ezen a weblapon](../../event-hubs/event-hubs-authentication-and-security-model-overview.md).

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

A blobok Azure storage felé irányuló JsonBlob fogadóba másolt adatokat tárolja. Minden példánya LAD minden fogadó név óránként egy blobot hoz létre. Minden egyes blob mindig egy objektum szintaktikailag érvényes JSON-tömböt tartalmaz. Új bejegyzések szolgáltatásfrissítést kerülnek a tömbhöz. Blobok pedig a fogadó azonos nevű tárolóban vannak tárolva. A blob-tároló neve az Azure storage szabályok vonatkoznak a JsonBlob fogadóként nevei: 3 és 63 kisbetűs alfanumerikus ASCII-karakterek és kötőjelek szerepelhetnek között.

## <a name="public-settings"></a>Nyilvános beállításai

Ez a struktúra a bővítmény által gyűjtött információk szabályozó beállítások különböző blokkokat tartalmaz. Minden beállítás nem kötelező. Ha megad `ladCfg`, meg kell adnia `StorageAccount`.

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
Tárfiók | A bővítmény írja adatokat, amelyben a tárfiók neve. Ugyanazzal a névvel kell lennie, mivel a megadott a [védett beállításainál](#protected-settings).
mdsdHttpProxy | (nem kötelező) Ugyanaz, mint a a [védett beállításainál](#protected-settings). A nyilvános érték felülbírálja a titkos értéket, ha a beállítása. Helyezze el, amelyek tartalmazzák a titkos kulcs jelszavát, például a proxybeállításokat a [védett beállításainál](#protected-settings).

A fennmaradó elemek az alábbi szakaszok részletesebben ismertetik.

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

Ez nem kötelező struktúra szabályozza a metrikák és naplók történő továbbítását az Azure-mérőszámok szolgáltatást, és egyéb adatok összegyűjtése fogadók. Meg kell adnia vagy `performanceCounters` vagy `syslogEvents` vagy mindkettőt. Meg kell adnia a `metrics` struktúra.

Elem | Érték
------- | -----
eventVolume | (nem kötelező) A tároló a táblán belül létrehozott partíciók számát szabályozza. Musí mít jednu z `"Large"`, `"Medium"`, vagy `"Small"`. Ha nincs megadva, az alapértelmezett értéke `"Medium"`.
sampleRateInSeconds | (nem kötelező) Az alapértelmezett gyakoriság közötti nyers (unaggregated) metrikák gyűjteménye. A legkisebb támogatott mintavételi gyakoriság érték 15 másodperc. Ha nincs megadva, az alapértelmezett értéke `15`.

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
resourceId | A virtuális gép vagy a virtuálisgép-méretezési csoport az Azure Resource Manager erőforrás-azonosítója, amelyhez tartozik a virtuális gép beállítása. Ezzel a beállítással lehet is megadott, ha bármely JsonBlob fogadó használatban van-e a konfiguráció.
scheduledTransferPeriod | A gyakoriság, ahol az összesített metrikái számított és Azure-metrika, egy van 8601 időintervallum kifejezett át vannak. A legkisebb átvitel időszak 60 másodperc, azaz PT1M. Meg kell adnia legalább egy scheduledTransferPeriod.

A minták a performanceCounters szakaszban megadott metrikák legyenek gyűjtve 15 másodpercenként, vagy a mintát, értékelje az explicit módon definiálva a számára. Ha több scheduledTransferPeriod gyakoriságok jelenik meg (ahogy a példában), minden Összesítés számított egymástól függetlenül.

#### <a name="performancecounters"></a>PerformanceCounters

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

Ez az opcionális szakasz szabályozza a mérőszámok gyűjtését. Nyers minták összesítik az egyes [scheduledTransferPeriod](#metrics) előállításához ezeket az értékeket:

* középérték
* minimum
* maximum
* utolsó összegyűjtött érték
* a nyers, összesített kiszámításához használt minták száma

Elem | Érték
------- | -----
fogadóként | (nem kötelező) Melyik LAD küld összesített eredmények metrika fogadóként nevei vesszővel tagolt listája. Az összes összesített mérőszámok egyes felsorolt fogadó vannak közzétéve. Lásd: [sinksConfig](#sinksconfig). Példa: `"EHsink1, myjsonsink"`.
type | A mérőszám a tényleges szolgáltató azonosítja.
osztály | "Számláló", és azonosítja az adott metrika belül a szolgáltatói névteret.
számláló | "Class", és azonosítja az adott metrika belül a szolgáltatói névteret.
counterSpecifier | Azonosítja az adott metrika az Azure-mérőszámok névtérben.
feltétel | (nem kötelező) Kiválaszt egy adott példányán, az objektumot, amelyhez a metrika vonatkozik, vagy az összesítés kiválasztja az adott objektum minden példányán. További információkért lásd: a `builtin` metrikadefinícióinak beolvasása.
sampleRate | VAN, amely beállítja a változási gyakoriság, amellyel ez a metrika-nyers minták gyűjtött 8601 időszak. Ha nincs megadva, az adatgyűjtési időköz értéke alapján [sampleRateInSeconds](#ladcfg). A legrövidebb támogatott mintavételi gyakoriság érték 15 másodperc (PT15S).
egység | Ezek a karakterláncok egyike lehet: "Count", "Memória", "Másodperc", "Százaléka", "CountPerSecond", "BytesPerSecond", "Ezredmásodperces". A metrika az egység határozza meg. Az összegyűjtött adatok várhatóan ezt a kiegészítő egységet a gyűjtött adatok értékeit. LAD figyelmen kívül hagyja ezt a mezőt.
displayName | A címke (a a kapcsolódó területi beállításban megadott nyelven) ezeket az adatokat az Azure-mérőszámok van csatlakoztatva. LAD figyelmen kívül hagyja ezt a mezőt.

A counterSpecifier tetszőleges azonosító, amely. Fogyasztók mérőszámokat, például az Azure portal diagramkészítési és counterSpecifier riasztási funkció, használja a "key", amely egy metrika vagy egy metrika egy példányát azonosítja. A `builtin` mérőszámokat, javasoljuk, használjon counterSpecifier értékek kezdődő `/builtin/`. Ha már gyűjti a metrika egy adott példányát, ajánlott counterSpecifier értéke csatlakoztat a példány azonosítóját. Néhány példa:

* `/builtin/Processor/PercentIdleTime` -A jelentés átlagolja az összes Vcpu üresjárati idő
* `/builtin/Disk/FreeSpace(/mnt)` – Szabad terület a /mnt fájlrendszer
* `/builtin/Disk/FreeSpace` – Szabad terület átlagolja az összes csatlakoztatott fájlrendszerek

LAD és az Azure Portalon nem vár semmilyen mintának megfelelő counterSpecifier érték. Legyen, következetesen hogyan hozhat létre counterSpecifier értékeket.

Ha `performanceCounters`, LAD mindig írja az adatokat az Azure storage-táblába. JSON-blobok és/vagy Eseményközpontokból írt ugyanazokat az adatokat is használhat, de nem tiltható le adatokat egy táblához. A diagnosztikai bővítmény összes példánya azonos tárfióknevet használatára konfigurált, és adja hozzá a metrikák és naplók végpont ugyanahhoz a táblához. Túl sok virtuális gépek tábla ugyanazon a partíción ír, az Azure képes szabályozni a írások ehhez a partícióhoz. A eventVolume beállítás megállítását között 1 (kicsi), 10 (közepes), vagy 100 (nagy) a különböző partíciók bejegyzések révén. Általában a "Közepes" is annak érdekében, hogy a rendszer nem szabályozza a forgalom megfelel. Az Azure Portalon az Azure-mérőszámok funkcióját adatokat használja a jelen táblázatban lévő gráfok létrehozásához, vagy riasztások aktiválása céljából. A tábla neve a az összefűzés karakterláncokra:

* `WADMetrics`
* A tábla tárolja az összesített értékekre "scheduledTransferPeriod"
* `P10DV2S`
* Egy dátumot, a képernyőn "ÉÉÉÉHHNN", amely a 10 naponta

Ilyenek például `WADMetricsPT1HP10DV2S20170410` és `WADMetricsPT1MP10DV2S20170609`.

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

Ez az opcionális szakasz meghatározza a syslog naplózási események gyűjtését. A szakasz elhagyása esetén syslog-események nem minden rögzíti.

A syslogEventConfiguration gyűjtemény minden syslog létesítmény a lényeges egy bejegyzést tartalmaz. Ha minSeverity egy adott létesítmény nem "NONE", vagy az adott létesítmény nem jelenik meg az elem minden, az adott létesítmény események nem lesznek rögzítve.

Elem | Érték
------- | -----
fogadóként | Nevét, amelyhez egyéni naplózási esemény közzé lesz téve fogadóként vesszővel tagolt listája. Minden egyes listázott fogadó syslogEventConfiguration korlátozásai megfelelő összes naplózási eseményt vannak közzétéve. Példa: "EHforsyslog"
kódban | Syslog létesítmény nevét (például a "LOG\_felhasználói" vagy "LOG\_LOCAL0"). A "létesítmény" című szakaszában talál a [syslog man lap](http://man7.org/linux/man-pages/man3/syslog.3.html) teljes listája.
minSeverity | A syslog súlyossági szint (például a "LOG\_ERR" vagy "LOG\_adatok"). "A szint" című szakaszában talál a [syslog man lap](http://man7.org/linux/man-pages/man3/syslog.3.html) teljes listája. A bővítmény rögzíti a megadott szintű vagy afeletti a létesítmény küldött események.

Ha `syslogEvents`, LAD mindig írja az adatokat az Azure storage-táblába. JSON-blobok és/vagy Eseményközpontokból írt ugyanazokat az adatokat is használhat, de nem tiltható le adatokat egy táblához. Ez a táblázat a particionálási viselkedés megegyezik a leírtak szerint `performanceCounters`. A tábla neve a az összefűzés karakterláncokra:

* `LinuxSyslog`
* Egy dátumot, a képernyőn "ÉÉÉÉHHNN", amely a 10 naponta

Ilyenek például `LinuxSyslog20170410` és `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

Ez az opcionális szakasz azt szabályozza, tetszőleges végrehajtásának [OMI](https://github.com/Microsoft/omi) lekérdezéseket.

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
névtér | (nem kötelező) Az OMI a következő névtér belül, amely a lekérdezés végrehajtja. Ha nincs megadva, az alapértelmezett értéke "gyökér/scx", által megvalósított a [a System Center többplatformos szolgáltatók](http://scx.codeplex.com/wikipage?title=xplatproviders&referringTitle=Documentation).
lekérdezés | Az OMI a következő lekérdezés végrehajtására.
tábla | (nem kötelező) Az Azure storage-táblát, a kijelölt tárfiókban (lásd: [védett beállításainál](#protected-settings)).
frequency | (nem kötelező) A lekérdezés végrehajtásának között eltelt másodpercek száma. Alapértelmezett értéke 300 (5 perc); minimális érték 15 másodperc.
fogadóként | (nem kötelező) Metrika eredmények nyers minta közzétételéről további fogadóként nevei vesszővel tagolt listája. Ezek a minták nyers nincs összesítés számított a bővítményt, vagy az Azure-mérőszámok.

"Table" vagy "fogadók", vagy mindkettőt meg kell adni.

### <a name="filelogs"></a>fileLogs

Azt szabályozza, hogy a naplófájlok a rögzítést. LAD új sorok rögzíti, az oktatóprogram a fájlt, és írja őket a táblázat sorainak és/vagy bármely megadott fogadóként (JsonBlob vagy EventHub).

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
fájl | Figyelt és rögzített a naplófájl teljes elérési útját. Az elérési útját kötelező nevet adni egyetlen fájl; nem egy könyvtár nevet és nem tartalmazhat helyettesítő karaktereket.
tábla | (nem kötelező) A kijelölt tárfiókban (teljesítjük, a védett konfiguráció), a "kisebb" a fájl új sorok írt, amelybe az Azure-os tárolótábla.
fogadóként | (nem kötelező) Mely log vonalakká küldött további fogadóként nevei vesszővel tagolt listája.

"Table" vagy "fogadók", vagy mindkettőt meg kell adni.

## <a name="metrics-supported-by-the-builtin-provider"></a>A beépített predict szolgáltató által támogatott mérőszámok

A beépített predict metrika szolgáltató legjobban érdekli felhasználók széles körének metrikák forrásaként szolgál. Ezek a metrikák öt széles körű osztályok sorolhatók:

* Processzor
* Memory (Memória)
* Network (Hálózat)
* Fájlrendszer
* Lemez

### <a name="builtin-metrics-for-the-processor-class"></a>a beépített mérőszámok a processzor-osztály

A mérőszámok a processzor osztály a virtuális gép processzor használati információkat biztosít. Százalékos összesítésekor az eredmény az átlagos processzorok esetében. Két vcpu-nkénti virtuális gépen Ha egy vCPU 100 %-os foglalt volt, és a másik tétlen, 100 % volt a jelentett PercentIdleTime pedig 50. Ha minden egyes vCPU foglalt azonos időszakára vonatkozó 50 % volt, a jelentett eredmény is pedig 50. Négy vcpu-nkénti rendelkező virtuális gép, egy vCPU 100 % foglalt, és a többi tétlen a jelentett PercentIdleTime 75 lenne.

számláló | Jelentés
------- | -------
PercentIdleTime | Az, hogy a processzorok is végrehajtása a kernel üresjárati hurok összesítési időszak alatt az idő százaléka
PercentProcessorTime | Egy nem várakozó szál végrehajtása idő százaléka
PercentIOWaitTime | Várakozás az i/o-műveletek végrehajtásához idő százaléka
PercentInterruptTime | Hardver/szoftver megszakítások, DPC-k (késleltetett eljáráshívások) végrehajtása idő százaléka
PercentUserTime | Nem üresjárati idő az összesítési időszak alatt időt töltött az több normál prioritással felhasználó
PercentNiceTime | Nem üresjárati időt százalékos töltött (jó) alacsonyabb prioritású
PercentPrivilegedTime | Nem üresjárati időt százalékos töltött védett (kernel) módban

Az első négy számlálók 100 %-os kell összeg. Az utolsó három is számlálók összege 100 %; Ezek feloszthatja PercentProcessorTime PercentIOWaitTime és PercentInterruptTime összege.

Szerezze be az összes processzor vonatkozó összesített érték csak egyetlen mértéket, állítsa `"condition": "IsAggregate=TRUE"`. Megadott processzorsebességgel rendelkező, például a négy vcpu-nkénti virtuális gépek, a második logikai processzor metrika beszerzése beállítása `"condition": "Name=\\"1\\""`. Logikai processzor-szám, a tartomány `[0..n-1]`.

### <a name="builtin-metrics-for-the-memory-class"></a>a beépített metrikáit a memória-osztály

A metrikák memória osztályát lapozás és érvényesítheti a memóriahasználat információkat biztosít.

számláló | Jelentés
------- | -------
AvailableMemory | MIB fizikai memória
PercentAvailableMemory | A teljes memória százalékos rendelkezésre álló fizikai memória
UsedMemory | Használatban lévő fizikai memória (MiB)
PercentUsedMemory | Használatban lévő fizikai memória teljes memória százalékában
PagesPerSec | Teljes lapozófájl (olvasás/írás)
PagesReadPerSec | Oldalak olvasni háttértár (lapozófájl programfájlt, leképezett fájlt, stb.)
PagesWrittenPerSec | A háttértárban (lapozófájl, leképezett fájlt, stb.) írt lapok
AvailableSwap | A fel nem használt lapozóterület (MiB)
PercentAvailableSwap | A fel nem használt lapozóterület százalékos arányában teljes lapozófájl-kapacitás
UsedSwap | Használatban lévő lapozóterület (MiB)
PercentUsedSwap | Használatban lévő lapozófájl-kapacitás százalékos arányában teljes lapozófájl-kapacitás

Ez az osztály metrikák csak egyetlen példány van. A "feltétel" attribútum nem hasznos beállításokkal rendelkezik, és ki lehet hagyni.

### <a name="builtin-metrics-for-the-network-class"></a>a hálózati osztály beépített metrikái

Metrikák hálózati osztályát rendszerindítás óta hálózati tevékenység információt nyújt az egyes hálózati adapterek. LAD nem biztosít sávszélesség-mérőszámok, amely a gazda mérőszámok lekérhetők.

számláló | Jelentés
------- | -------
BytesTransmitted | Rendszerindítás óta küldött bájtok teljes száma
BytesReceived | Rendszerindítás óta fogadott bájtok teljes száma
BytesTotal | Küldött vagy fogadott rendszerindítás óta az összes bájt
PacketsTransmitted | Rendszerindítás óta küldött az összes csomag
PacketsReceived | Rendszerindítás óta fogadott teljes csomag
TotalRxErrors | Rendszerindítás óta fogadási hibák száma
TotalTxErrors | -Küldési hibák száma rendszerindítás óta
TotalCollisions | Az ütközések rendszerindítás óta a hálózati portok által jelentett száma

 Bár ez az osztály van instanced, LAD rögzítését hálózati metrikáinak vonatkozó összesített érték összes hálózati eszköz nem támogatja. Szerezze be a metrikák, eth0, például egy adott illesztő számára állítsa `"condition": "InstanceID=\\"eth0\\""`.

### <a name="builtin-metrics-for-the-filesystem-class"></a>a fájlrendszer osztály beépített metrikái

A fájlrendszer osztály mérőszámok fájlrendszer használati információkat biztosít. Abszolút és a százalékos értékek jelentett, akkor megjelenik egy szokásos felhasználónak (nem legfelső szintű).

számláló | Jelentés
------- | -------
FreeSpace | Szabad lemezterület (bájt)
UsedSpace | A felhasznált lemezterület (bájt)
PercentFreeSpace | Szabad terület százalékos aránya
PercentUsedSpace | A felhasznált terület százalékos aránya
PercentFreeInodes | A fel nem használt Inode-OK százaléka
PercentUsedInodes | Összeadáshoz összes fájlrendszereit között (használatban lévő) lefoglalt Inode-OK százaléka
BytesReadPerSecond | Másodpercenként beolvasott bájtok száma
BytesWrittenPerSecond | Másodpercenként írt bájtok száma
Bájt/s | Olvassa el, és másodpercenként írt bájtok
ReadsPerSecond | Olvasási műveletek száma másodpercenként
WritesPerSecond | Írási műveletek száma másodpercenként
TransfersPerSecond | Olvasási vagy írási műveletek száma másodpercenként

Az összes fájl rendszerekből összesített értékeket is beszerezhetők beállításával `"condition": "IsAggregate=True"`. Például egy adott csatlakoztatott fájlrendszer értékei "/ mnt", beállításával szerezhető `"condition": 'Name="/mnt"'`.

### <a name="builtin-metrics-for-the-disk-class"></a>a lemez osztály beépített metrikái

A lemez osztály mérőszámok eszköz lemezhasználat információkat biztosít. A statisztika a teljes meghajtót vonatkoznak. Ha egy eszközön több fájlrendszereket, az eszközön a számlálók lényegében, összesített érték azokat.

számláló | Jelentés
------- | -------
ReadsPerSecond | Olvasási műveletek száma másodpercenként
WritesPerSecond | Írási műveletek száma másodpercenként
TransfersPerSecond | Teljes műveletek száma másodpercenként
AverageReadTime | Átlagos másodperc / olvasási művelet
AverageWriteTime | Írási művelet átlagos másodperc
AverageTransferTime | Művelet átlagos másodperc
AverageDiskQueueLength | Várólistára helyezett lemezes műveletek átlagos száma
ReadBytesPerSecond | Másodpercenként olvasott bájtok száma
WriteBytesPerSecond | Másodpercenként írt bájtok száma
Bájt/s | Olvassa el, és másodpercenként írt bájtok száma

Minden lemezre kiterjedő összesített értékeket is beszerezhetők beállításával `"condition": "IsAggregate=True"`. Egy adott eszközhöz (például/dev/sdf1) tartozó információk lekérése, állítsa `"condition": "Name=\\"/dev/sdf1\\""`.

## <a name="installing-and-configuring-lad-30-via-cli"></a>Telepítésével és konfigurálásával LAD 3.0 parancssori felületén

Ha a védett beállítások PrivateConfig.json fájlban és a nyilvános konfigurációs adatait PublicConfig.json, a következő parancs futtatásával:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

A parancs feltételezi, hogy az Azure CLI Azure Resource Management-(arm) módját használja. LAD konfigurálása a klasszikus üzemi modell (ASM) virtuális gépek, "asm" üzemmódra váltás (`azure config mode asm`), és hagyja ki az erőforráscsoport nevét a parancsban. További információkért lásd: a [platformfüggetlen CLI dokumentációját](https://docs.microsoft.com/azure/xplat-cli-connect).

## <a name="an-example-lad-30-configuration"></a>Konfiguráció például LAD 3.0

Az előző definíciók alapján, a következő egy példa LAD 3.0 bővítmény konfigurációja néhány magyarázattal. A alkalmazni ezt a mintát az eset, használjon a saját tárfiók neve, a fiók SAS-token és a EventHubs SAS-tokeneket.

### <a name="privateconfigjson"></a>PrivateConfig.json

Ezek a személyes beállítások konfigurálása:

* Storage-fiók
* a megfelelő SAS-jogkivonata
* több fogadóként (JsonBlob vagy az SAS-tokeneket EventHubs)

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

Ezek a beállítások nyilvános LAD, ok:

* A processzoridő %-os és a használt – lemezterület-metrikák feltöltése a `WADMetrics*` tábla
* A syslog létesítmény "user" és a súlyosság "info" üzeneteket töltse fel a `LinuxSyslog*` tábla
* Töltse fel a megnevezett nyers OMI lekérdezési eredmények (PercentProcessorTime és PercentIdleTime) `LinuxCPU` tábla
* Töltse fel a fájl sorainak hozzáfűzött `/var/log/myladtestlog` , a `MyLadTestLog` tábla

Minden esetben adatokat is töltenek fel:

* Az Azure Blob storage (a tároló neve az a JsonBlob fogadó meghatározottak szerint)
* EventHubs-végpont (teljesítjük, a EventHubs fogadó)

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

A `resourceId` konfigurációjában meg kell egyeznie, hogy a virtuális gép vagy virtuálisgép-méretezési csoport állítsa be.

* Az Azure platform metrikák diagramkészítési és riasztási tudja, hogy az erőforrás-azonosító, a virtuális gép, amelyen dolgozik. Azt szeretné, hogy az adatok keressen a virtuális gépet az erőforrás-azonosító a keresési kulcs vár.
* Az Azure automatikus méretezés használatakor az erőforrás-azonosító, az automatikus skálázási konfiguráció meg kell egyeznie a LAD által használt erőforrás-azonosító.
* Az erőforrás-azonosító LAD által írt JsonBlobs nevei be van építve.

## <a name="view-your-data"></a>Adatok megtekintése

Az Azure portal használatával teljesítményadatainak megjelenítéséhez, vagy a riasztások beállítását:

![image](./media/diagnostics-linux/graph_metrics.png)

A `performanceCounters` adatok mindig vannak tárolva egy Azure Storage-táblába. Az Azure Storage API-k számos nyelvekhez és platformokhoz érhetők el.

JsonBlob fogadóként küldött adatok tárolása a tárfiókban szereplő blobok a [védett beállításainál](#protected-settings). A blob-adatokat bármely Azure Blob Storage API-k használatával is használhatók.

Ezek az eszközök felhasználói felület segítségével emellett elérni az adatokat az Azure Storage-ban:

* Visual Studio Server Explorer.
* [A Microsoft Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/ "az Azure Storage Explorer").

Ez egy Microsoft Azure Storage Explorer munkamenet pillanatképe látható a létrehozott Azure Storage-táblák és a egy megfelelően konfigurált LAD 3.0-bővítményt a tárolók tesztelési virtuális gép. A rendszerkép nem felel meg pontosan a [LAD 3.0 mintakonfiguráció](#an-example-lad-30-configuration).

![image](./media/diagnostics-linux/stg_explorer.png)

Olvassa el a megfelelő [EventHubs dokumentáció](../../event-hubs/event-hubs-what-is-event-hubs.md) megtudhatja, hogyan lehet az EventHubs-végpontra közzé.

## <a name="next-steps"></a>További lépések

* A metrikai riasztások létrehozása [Azure Monitor](../../monitoring-and-diagnostics/insights-alerts-portal.md) a gyűjtött metrikák.
* Hozzon létre [figyelési diagramokat](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) a metrikákkal.
* Ismerje meg, hogyan [hozzon létre egy virtuálisgép-méretezési csoportot](../linux/tutorial-create-vmss.md) a mérőszámok segítségével szabályozhatja az automatikus skálázást.
