---
title: Az Azure Diagnostics bővítmény konfigurációs séma verzióelőzményei
description: Az Azure Virtual Machines, a Virtuálisgép-méretezési csoportok, a Service Fabric és Cloud Services teljesítményszámlálók gyűjtése nem megfelelő.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/20/2018
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 42ed050606dd242e43903ed980a5670b4aa6f3df
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53343173"
---
# <a name="azure-diagnostics-extension-configuration-schema-versions-and-history"></a>Az Azure Diagnostics bővítmény konfigurációs séma verziók és előzményei
Ezt oldal indexek az Azure Diagnostics bővítmény séma verziója le szállítani, a Microsoft Azure SDK csomag részeként.  

> [!NOTE]
> Az Azure Diagnostics bővítmény az a komponens, teljesítményszámlálók és más statisztikáin összegyűjtéséhez használt:
> - Azure-alapú virtuális gépek
> - Virtual Machine Scale Sets
> - Service Fabric
> - Cloud Services
> - Network Security Groups (Hálózati biztonsági csoportok)
>
> Ez a lap csak akkor jelentősége, ha ezek a szolgáltatások valamelyikét használja.

Egyéb Microsoft-diagnosztika termékek, mint az Azure Monitor az Application Insights és a Log Analytics az Azure Diagnostics bővítmény használnak. További információkért lásd: [figyelési eszközök áttekintése a Microsoft](../../azure-monitor/overview.md).

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Az Azure SDK-t és a diagnosztika verziók szállítási diagram  

|Azure SDK version | Diagnosztikai bővítmény verziója | Modell|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |beépülő modul|  
|2.0 - 2.4         |1.0                            |beépülő modul|  
|2.5               |1.2                            |Bővítmény|  
|2.6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2.8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|
|2.96              |1.11                           |"|


 Az Azure Diagnostics 1.0-s verzió először szállítani, ami azt jelenti, hogy az Azure SDK telepítésekor kapott a verzió, az Azure diagnostics – a beépülő modul modelljének Önnek azt.  

 Kezdve SDK 2.5-ös (1.2-es verzió diagnosztikai), az Azure diagnostics bővítmény modellre történt. Az eszközöket, új funkciók használatához csak újabb Azure SDK-k érhető el, de az Azure diagnostics használatával bármely szolgáltatással szeretné folytattuk a munkát a szállítási címhez tartozó legújabb közvetlenül az Azure-ból. Például bárki más is használja az SDK 2.5-ös lenne tölt be az előző táblázatban látható, függetlenül attól, ha az újabb szolgáltatást a legújabb verzióra.  

## <a name="schemas-index"></a>Sémák index  
Az Azure diagnostics különböző verziói különböző konfigurációs sémák használják.

[Diagnosztikai 1.0 konfigurációs séma](diagnostics-extension-schema-1dot0.md)  

[Diagnosztikai 1.2-es konfigurációs séma](diagnostics-extension-schema-1dot2.md)  

[Diagnosztikai 1.3 és újabb konfigurációs séma](diagnostics-extension-schema-1dot3.md)  

## <a name="version-history"></a>Verzióelőzmények

### <a name="diagnostics-extension-111"></a>A diagnosztikai bővítmény 1.11
Az Azure Monitor-fogadó támogatása. Ez a fogadó csak akkor érvényes, a teljesítményszámlálók. Lehetővé teszi, hogy a virtuális gép, VMSS vagy a felhőalapú szolgáltatás az Azure monitornak egyéni mérőszámokként gyűjtött teljesítményszámlálók küldése. Az Azure Monitor-fogadó támogatja:
* Az Azure Monitor használatával küldött összes teljesítményszámlálók beolvasása a [Azure Monitor-metrikák API-k.](https://docs.microsoft.com/rest/api/monitor/metrics/list)
* Az Azure Monitor használatával az új riasztást küld minden teljesítményszámlálót küldött [egységes riasztások kezelőfelület](../../azure-monitor/platform/alerts-overview.md) az Azure monitorban
* Helyettesítő operátor kezelni a teljesítményszámlálók a metrika a "Példány" dimenzió. Például ha összegyűjtött a "logikai lemez (\*) / DiskWrites/mp" számláló tudni szűrőt, és minden egyes logikai lemez (C:, d, stb.) a "Példány" dimenzión diagram vagy a Lemezírások/mp riasztások felosztása

Az Azure Monitor definiálhatja a diagnosztikai bővítmény konfigurációja egy új fogadó
```json
"SinksConfig": {
    "Sink": [
        {
            "name": "AzureMonitorSink",
            "AzureMonitor": {}
        },
    ]
}
```

```XML
<SinksConfig>  
  <Sink name="AzureMonitorSink">
      <AzureMonitor/>
  </Sink>
</SinksConfig>
```
> [!NOTE]
> Klasszikus virtuális gépeket és a klasszikus Felhőszolgáltatás konfigurálása az Azure Monitor gyűjtőjéhez szükséges további paramétereket kell megadni a diagnosztikai bővítmény privát konfigurációja.
>
> A további részletekért olvassa referencia a [részletes diagnosztikai bővítmény séma dokumentációt.](diagnostics-extension-schema-1dot3.md)

Ezután konfigurálhatja a teljesítményszámlálók, az Azure Monitor-fogadó át szeretné irányítani.
```json
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "AzureMonitorSink",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT1M",
            "unit": "percent"
        }
    ]
},
```
```XML
<PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
</PerformanceCounters>
```

### <a name="diagnostics-extension-19"></a>A diagnosztikai bővítmény 1.9
Támogatás hozzáadva a Docker.


### <a name="diagnostics-extension-181"></a>1.8.1. a diagnosztikai bővítmény
Megadhatja a saját config tárfiókkulcs helyett SAS-jogkivonatát. SAS-token megadása esetén a tárfiók-kulcsot a rendszer figyelmen kívül hagyja.


```json
{
    "storageAccountName": "diagstorageaccount",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    }
}
```

```xml
<PrivateConfig>
    <StorageAccount name="diagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    <SecondaryStorageAccounts>
        <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
</PrivateConfig>
```


### <a name="diagnostics-extension-18"></a>A diagnosztikai bővítmény 1.8-as verziója
Új tárolási típus PublicConfig. StorageType lehet *tábla*, *Blob*, *TableAndBlob*. *Tábla* az alapértelmezett beállítás.


```json
{
    "WadCfg": {
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```xml
<PublicConfig>
    <WadCfg />
    <StorageAccount>diagstorageaccount</StorageAccount>
    <StorageType>TableAndBlob</StorageType>
</PublicConfig>
```


### <a name="diagnostics-extension-17"></a>A diagnosztikai bővítmény 1.7-es
Megjelent az EventHub irányíthatja.

### <a name="diagnostics-extension-15"></a>A diagnosztikai bővítmény 1.5-ös
Hozzáadva az fogadóként elemet, és lehetővé teszi diagnosztikai adatok küldése [Application Insights](../../application-insights/app-insights-cloudservices.md) megkönnyíti a problémák diagnosztizálása az alkalmazás, valamint a rendszer és az infrastruktúra szint között.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>1.3-tól az Azure SDK 2.6-os és a diagnosztikai bővítmény
A Visual Studio Felhőszolgáltatás-projektek a következő változások történtek. (Ezek a változások is vonatkoznak az Azure SDK újabb verziói.)

* A helyi emulátor mostantól támogatja a diagnosztikát. Ez a módosítás azt jelenti, hogy a diagnosztikai adatok gyűjtésére, és győződjön meg arról, az alkalmazás létrehozása a megfelelő nyomkövetések közben meg fejlesztése és tesztelése a Visual Studióban. A kapcsolati karakterlánc `UseDevelopmentStorage=true` lehetővé teszi diagnosztikai adatok gyűjtése a futtatása közben a felhőszolgáltatási projektet a Visual Studióban az Azure storage emulator használatával. Az összes diagnosztikai adatgyűjtés (fejlesztési tárterülettel) storage-fiókban.
* A diagnosztikai tárfiók kapcsolati sztringje (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) ismét a szolgáltatás konfigurációs (.cscfg) fájljában tárolódnak. Az Azure SDK 2.5-ös a diagnosztikai tárfiók van megadva a diagnostics.wadcfgx fájlt.

Nincsenek hogyan működött a kapcsolati karakterláncot, az Azure SDK 2.4 és korábbi verziók, és hogyan működik az Azure SDK 2.6-os és újabb verziók közötti fontosabb különbségeket.

* Azure SDK 2.4 és korábbi a kapcsolati karakterlánc lett megadva futásidőben a diagnosztikai beépülő modul a tárolási fiók adatok átvitelére a diagnosztikai naplók.
* Azure SDK 2.6-os vagy újabb a Visual Studio diagnosztikai kapcsolati karakterláncát használja a diagnosztikai bővítmény konfigurálása a megfelelő tárfiókadatok közzététele során. A kapcsolati karakterláncot adja meg a különböző storage-fiókot használó Visual Studio közzététel során különböző szolgáltatáskonfiguráció teszi lehetővé. Azonban a diagnosztikai beépülő modul (az Azure SDK 2.5-ös) után már nem érhető el, mert a .cscfg fájlban önmagában nem engedélyezhető a diagnosztikai bővítményt. A bővítmény eszközök, például a Visual Studio vagy a PowerShell segítségével külön engedélyeznie kell.
* A diagnosztikai bővítmény konfigurálása a PowerShell-lel, a folyamat leegyszerűsítése a Visual Studio csomag kimenetét a nyilvános konfigurációs XML-t a diagnosztikai bővítményt, az egyes szerepkörökhöz is tartalmaz. A Visual Studio diagnosztikai kapcsolati karakterláncát használja a tárfiók-információ található a nyilvános konfigurációban feltöltéséhez. A nyilvános konfigurációs fájlok jönnek létre a kiterjesztéseket tartalmazó mappájába, és az alábbi mintát PaaSDiagnostics. <RoleName>. PubConfig.xml. A PowerShell-alapú telepítések Ez a minta használatával minden egyes konfiguráció leképezése egy szerepkört.
* A kapcsolati karakterláncot a .cscfg fájlban is használják az Azure Portalon a diagnosztikai adatok eléréséhez, így is megjelennek a **figyelés** fülre. A kapcsolati karakterlánc monitorozási adatok részletes megjelenítéséhez a portálon a szolgáltatás konfigurálására van szükség.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Azure SDK 2.6-os vagy újabb áttelepítése projektek
Ről való áttelepítés esetén az Azure SDK 2.5-ös az Azure SDK 2.6-os vagy újabb, ha a .wadcfgx fájlban megadott diagnosztikai tárfiók, majd marad van. A rugalmas különböző storage-fiókot használni a különböző tárolási konfigurációk előnyeit kell manuálisan adja hozzá a kapcsolati karakterláncot a projekthez. Ha a projekt migráláshoz az Azure SDK 2.4 vagy korábban az Azure SDK 2.6-os, a diagnosztikai kapcsolati karakterláncok megmaradnak. Megjegyzendő, a módosítások hogyan kapcsolati karakterláncok kezeli az Azure SDK 2.6 megadott az előző szakaszban található.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Hogyan határozza meg a Visual Studióban a diagnosztikai tárfiók
* Ha diagnosztikai kapcsolati karakterláncot a .cscfg fájlban van megadva, a Visual Studio használja a diagnosztikai bővítmény konfigurálása a közzététel során, és a nyilvános konfigurációs xml-fájlok létrehozásakor csomagolási során.
* Ha nincsenek diagnosztikai kapcsolati karakterlánc van megadva a .cscfg fájlban, majd a Visual Studio visszaáll a .wadcfgx fájlban megadott tárfiók konfigurálása a diagnosztikai bővítmény közzétételéhez, és a nyilvános konfigurációs xml-fájlok létrehozása Amikor becsomagolásához.
* A diagnosztika kapcsolati karakterláncot a .cscfg fájlban élvez a .wadcfgx fájlban a tárfiók. Ha diagnosztikai kapcsolati karakterláncot a .cscfg fájlban van megadva, a Visual Studio használ, amely, és figyelmen kívül hagyja a storage-fiókban lévő .wadcfgx.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Mit jelent a "Fejlesztés tárolási kapcsolatok karakterláncainak frissítése..." jelölőnégyzet?
A jelölőnégyzetet a **fejlesztési storage kapcsolati karakterláncok frissítése diagnosztika és a gyorsítótárazás Microsoft Azure storage-fiók hitelesítő adataival a Microsoft Azure közzététel során** frissítéséhez bármely fejlesztői kényelmes megoldást kínál az tárfiók kapcsolati karakterláncainak a közzététel során megadott Azure storage-fiókban.

Tegyük fel például, hogy bejelöli ezt a jelölőnégyzetet, és a diagnosztika kapcsolati karakterláncot adja meg `UseDevelopmentStorage=true`. A projekt az Azure-ba való közzétételekor a Visual Studio automatikusan frissíti a diagnosztikai kapcsolati karakterlánc a tárfiók a közzétételi varázsló megadott. Azonban ha egy valódi tárfiók van megadva, a diagnosztikai kapcsolati karakterláncot, majd a fiókot használja.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diagnosztikai funkciók eltérései Azure SDK 2.4 és a korábbi és az Azure SDK 2.5-ös és újabb verziók
Ha frissít a projekt az Azure SDK 2.4 az Azure SDK 2.5-ös vagy újabb, meg kell figyelembe kell vennie az alábbi eltérések a diagnosztikai funkciót.

* **Konfigurációs API-jai elavultak** – diagnosztika programozható konfigurációja érhető el az Azure SDK 2.4 vagy régebbi verziójú, de az Azure SDK 2.5-ös és újabb elavult. Ha a diagnosztikai konfigurációja jelenleg definiált kód, szüksége lesz a teljesen új az áttelepített projektben ahhoz, hogy diagnosztikai beállítást a munka folytatásához újra. A diagnosztika konfigurációs fájlt az Azure SDK 2.4 diagnostics.wadcfg, de diagnostics.wadcfgx Azure SDK 2.5-ös és újabb verziók.
* **Cloud service-alkalmazások diagnosztikai adatait csak a szerepkör szintjén, a példány szintjén konfigurálható.**
* **Minden alkalommal, amikor telepíti az alkalmazását, a diagnosztikai konfigurációja frissül** – funkcióparitási problémák léphetnek fel, ha a Server Explorer a diagnostics konfigurációjának módosítása, és ezt követően telepítse újra az alkalmazást.
* **Az Azure SDK 2.5-ös és újabb összeomlási memóriaképek vannak-e konfigurálva a diagnosztikai konfigurációs fájlban, nem a kódban** – ha van konfigurálva a kódban összeomlási memóriaképek, kell manuális átvitele a konfiguráció code-ból a konfigurációs fájlt, mert a összeomlási memóriaképek Azure SDK 2.6-os az áttelepítés során nem kerülnek.
