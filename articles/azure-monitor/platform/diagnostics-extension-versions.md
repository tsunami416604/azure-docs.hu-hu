---
title: A Windows Azure Diagnosztika bővítmény (WAD) konfigurációs séma verzióelőzményei
description: Fontos, hogy az Azure virtuális gépek, a virtuális gép méretezési készletek, a szolgáltatás-háló és a felhőszolgáltatások perf számlálók gyűjtése.
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/29/2020
ms.openlocfilehash: 4dd91363cdebf18e6303238816e8269065a6a317
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672242"
---
# <a name="windows-azure-diagnostics-extension-wad-configuration-schema-versions-and-history"></a>A Windows Azure Diagnosztika bővítmény (WAD) konfigurációs sémaverziói és előzményei
Ez a cikk a Microsoft Azure SDK részeként szállított [Azure Diagnostics bővítmény Windows (WAD)](diagnostics-extension-overview.md) sémaverzióinak verzióelőzményeit tartalmazza.  


## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Az Azure SDK és a diagnosztikai verziók szállítási diagramja  

|Az Azure SDK verziója | Diagnosztikai bővítmény verziója | Modell|  
|------------------|-------------------------------|------|  
|1,x               |1.0                            |Plug-in|  
|2.0 - 2.4         |1.0                            |Plug-in|  
|2.5               |1.2                            |Kiterjesztés|  
|2,6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2,8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|
|2.96              |1.11                           |"|


 Az Azure Diagnostics 1.0-s verziója először egy beépülő modul modellben szállított , ami azt jelenti, hogy az Azure SDK telepítésekor az Azure-diagnosztika verzióját szállította vele együtt.  

 Az SDK 2.5-ös (1.2-es diagnosztikai verzió) kezdetű Azure-diagnosztika egy bővítménymodellre került. Az új funkciók használatára szolgáló eszközök csak az újabb Azure SDK-kban voltak elérhetők, de az Azure diagnosztikát használó szolgáltatások közvetlenül az Azure-ból veszik fel a legújabb szállítási verziót. Ha például még mindig az SDK 2.5-öt használja, az az előző táblázatban látható legújabb verziót tölti be, függetlenül attól, hogy az újabb funkciókat használja-e.  

## <a name="schemas-index"></a>Sémaindex  
Az Azure-diagnosztika különböző verziói különböző konfigurációs sémákat használnak. Az 1.0-s és 1.2-es séma elavult. Az 1.3-as és újabb verzióiról a [Diagnosztika 1.3-as és újabb konfigurációs séma című](diagnostics-extension-schema-windows.md) témakörben talál további információt.  

## <a name="version-history"></a>Verzióelőzmények

### <a name="diagnostics-extension-111"></a>Diagnosztikai kiterjesztés 1.11
Az Azure Monitor-fogadó támogatása hozzáadva. Ez a fogadó csak teljesítményszámlálókra vonatkozik. Lehetővé teszi a virtuális gép, a VMSS vagy a felhőszolgáltatás on-kon gyűjtött teljesítményszámlálók küldését az Azure Monitoregyéni metrikákként. Az Azure Monitor-fogadó támogatja:
* Az [Azure Monitor metrikaapi-kon](https://docs.microsoft.com/rest/api/monitor/metrics/list) keresztül az Azure Monitornak küldött összes teljesítményszámláló lekérése.
* Az Azure Monitornak küldött összes teljesítményszámláló [riasztása](../../azure-monitor/platform/alerts-overview.md) az Azure Monitor új egyesített riasztások szolgáltatásán keresztül
* A helyettesítő karakter operátorok teljesítményszámlálókban való kezelése a metrika "Példány" dimenziójaként. Ha például összegyűjtötte a\*"LogicalDisk( )/DiskWrites/sec" számlálót, akkor szűrheti és feloszthatja a "Példány" dimenziót, hogy minden logikai lemez (C:, D:, stb.) lemezírásai/mp-en ábrázolja vagy riasztást kapjon.

Az Azure Monitor definiálása új fogadóként a diagnosztikai bővítmény konfigurációjában
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
> Az Azure Monitor-fogadó konfigurálása a klasszikus virtuális gépek és a klasszikus CLoud szolgáltatás további paramétereket kell meghatározni a diagnosztikai bővítmény privát konfiguráció.
>
> További részletekért kérjük, tekintse meg a [részletes diagnosztikai bővítmény séma dokumentációját.](diagnostics-extension-schema-windows.md)

Ezután konfigurálhatja a teljesítményszámlálók at az Azure Monitor-fogadóba irányítva.
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

### <a name="diagnostics-extension-19"></a>Diagnosztikai kiterjesztés 1,9
Docker-támogatás hozzáadva.


### <a name="diagnostics-extension-181"></a>Diagnosztikai kiterjesztés 1.8.1
A privát konfigurációban a tárfiók kulcsa helyett SAS-jogkivonatot adhat meg. Ha egy SAS-jogkivonat van megadva, a tárfiók kulcs figyelmen kívül hagyja.


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


### <a name="diagnostics-extension-18"></a>Diagnosztikai bővítmény 1,8
Tárolótípusa hozzáadva a PublicConfig-hoz. A StorageType lehet *Table*, *Blob*, *TableAndBlob*. *A tábla* az alapértelmezett.


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


### <a name="diagnostics-extension-17"></a>Diagnosztikai kiterjesztés 1.7
Hozzáadva az EventHubra való útvonalhoz való útvonal lehetősége.

### <a name="diagnostics-extension-15"></a>Diagnosztikai bővítmény 1,5
Hozzáadta a sinks elemet, és képes diagnosztikai adatokat küldeni az [Application Insights](../../azure-monitor/app/cloudservices.md) könnyebben diagnosztizálhatja a problémákat az alkalmazáson keresztül, valamint a rendszer és az infrastruktúra szintjén.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Az Azure SDK 2.6 és 1.3 diagnosztikai bővítmény
A Visual Studio Cloud Service-projektjei esetében a következő módosítások történtek. (Ezek a módosítások az Azure SDK későbbi verzióira is vonatkoznak.)

* A helyi emulátor most antól támogatja a diagnosztikát. Ez a módosítás azt jelenti, hogy diagnosztikai adatokat gyűjthet, és biztosíthatja, hogy az alkalmazás létrehozza a megfelelő nyomkövetéseket a Visual Studióban való fejlesztés és tesztelés során. A kapcsolati karakterlánc `UseDevelopmentStorage=true` lehetővé teszi a diagnosztikai adatgyűjtést, miközben a felhőalapú szolgáltatásprojektet a Visual Studióban futtatja az Azure storage-emulátor használatával. Az összes diagnosztikai adatokat gyűjti a (Development Storage) tárfiókban.
* A diagnosztikai tárfiók kapcsolati karakterlánca (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) ismét a szolgáltatáskonfigurációs (.cscfg) fájlban tárolódik. Az Azure SDK 2.5-ben a diagnosztikai tárfiók a diagnostics.wadcfgx fájlban lett megadva.

Van néhány figyelemre méltó különbség a között, ahogyan a kapcsolati karakterlánc működött az Azure SDK 2.4-es és korábbi, és hogyan működik az Azure SDK 2.6-os és újabb.

* Az Azure SDK 2.4-es és korábbi, a kapcsolati karakterlánc ot a diagnosztikai bővítmény futásidőben használta a diagnosztikai fiók adatainak lehívásához diagnosztikai naplók átviteléhez.
* Az Azure SDK 2.6-os és újabb verzióiban a Visual Studio a diagnosztikai kapcsolati karakterláncot használja a diagnosztikai bővítmény konfigurálásához a megfelelő tárfiók-adatokkal a közzététel során. A kapcsolati karakterlánc lehetővé teszi, hogy különböző tárfiókokat határozzon meg a Visual Studio által a közzétételkor használt különböző szolgáltatáskonfigurációkhoz. Mivel azonban a diagnosztikai bővítmény már nem érhető el (az Azure SDK 2.5 után), a .cscfg fájl önmagában nem tudja engedélyezni a diagnosztikai bővítményt. A bővítményt külön kell engedélyeznie olyan eszközökön keresztül, mint a Visual Studio vagy a PowerShell.
* A diagnosztikai bővítmény PowerShell-lel való konfigurálásának egyszerűsítése érdekében a Visual Studio csomagkimenete az egyes szerepkörök diagnosztikai bővítményéhez is tartalmazza a nyilvános konfigurációs XML-t. A Visual Studio a diagnosztikai kapcsolati karakterlánc segítségével feltölti a nyilvános konfigurációban lévő tárfiók adatait. A nyilvános konfigurációs fájlok a Bővítmények mappában `PaaSDiagnostics.<RoleName>.PubConfig.xml`jönnek létre, és követik a mintát. Bármely PowerShell-alapú központi telepítések használhatja ezt a mintát az egyes konfigurációk szerepkörhöz.
* A .cscfg fájlban lévő kapcsolati karakterláncot az Azure Portal is használja a diagnosztikai adatok eléréséhez, hogy azok megjelenjenek a **Figyelés** lapon. A kapcsolati karakterlánc szükséges a szolgáltatás konfigurálásához a részletes figyelési adatok megjelenítéséhez a portálon.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Projektek áttelepítése az Azure SDK 2.6-os és újabb verzióiba
Az Azure SDK 2.5-ről az Azure SDK 2.6-os vagy újabb verzióra történő áttelepítésekor, ha a .wadcfgx fájlban megadott diagnosztikai tárfiókkal rendelkezik, akkor az ott is marad. A különböző tárkonfigurációk különböző tárfiókok használatának rugalmasságának kihasználásához manuálisan kell hozzáadnia a kapcsolati karakterláncot a projekthez. Ha egy projektet az Azure SDK 2.4-es vagy korábbi Azure SDK 2.6-ról telepít át, a diagnosztikai kapcsolati karakterláncok megmaradnak. Azonban vegye figyelembe a kapcsolati karakterláncok azure SDK 2.6 az előző szakaszban megadott módon a változásokat.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Hogyan határozza meg a Visual Studio a diagnosztikai tárfiókot?
* Ha a .cscfg fájlban diagnosztikai kapcsolati karakterlánc van megadva, a Visual Studio arra használja, hogy konfigurálja a diagnosztikai bővítményt a közzétételkor és a nyilvános konfigurációs XML-fájlok csomagolás közbeni létrehozásakor.
* Ha a .cscfg fájlban nincs megadva diagnosztikai kapcsolati karakterlánc, akkor a Visual Studio visszaáll a .wadcfgx fájlban megadott tárfiók használatára a diagnosztikai kiterjesztés konfigurálásához a közzétételkor, és a nyilvános konfigurációs XML-fájlok létrehozásához. csomagoláskor.
* A .cscfg fájldiagnosztikai kapcsolati karakterlánc elsőbbséget élvez a .wadcfgx fájlban lévő tárfiókkal szemben. Ha a .cscfg fájlban diagnosztikai kapcsolati karakterlánc van megadva, akkor a Visual Studio ezt használja, és figyelmen kívül hagyja a .wadcfgx-ben lévő tárfiókot.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Mit jelent a "Frissítés fejlesztési tárolási kapcsolat karakterláncok ..." jelölőnégyzet nem?
A microsoft **Azure-ban történő közzétételkor a Microsoft Azure-fiók hitelesítő adataival való frissítésfejlesztési tárkapcsolati karakterláncok frissítése a Microsoft Azure-ban történő közzétételkor** a fejlesztési tárfiók csatlakozási karakterláncai frissítésével kényelmesen frissíthetők a közzététel során megadott Azure storage-fiókkal.

Tegyük fel például, hogy bejelöli ezt a `UseDevelopmentStorage=true`jelölőnégyzetet, és a diagnosztikai kapcsolati karakterlánc megadja a paramétert. Amikor közzéteszi a projektet az Azure-ban, a Visual Studio automatikusan frissíti a diagnosztikai kapcsolati karakterláncot a Közzététel varázslóban megadott tárfiókkal. Ha azonban egy valós tárfiók lett megadva a diagnosztikai kapcsolati karakterláncként, akkor a rendszer ezt a fiókot használja.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diagnosztikai funkciók közötti különbségek az Azure SDK 2.4 és korábbi és az Azure SDK 2.5 és újabb
Ha az Azure SDK 2.4-ről az Azure SDK 2.5-ös vagy újabb verzióra frissíti a projektet, vegye szem előtt a következő diagnosztikai funkciók közötti különbségeket.

* **A konfigurációs API-k elavultak** – A diagnosztika programozott konfigurációja elérhető az Azure SDK 2.4-es vagy korábbi verzióiban, de az Azure SDK 2.5-ös és újabb verzióiban elavult. Ha a diagnosztikai konfiguráció jelenleg a kódban van definiálva, újra kell konfigurálnia ezeket a beállításokat az áttelepített projektben ahhoz, hogy a diagnosztika tovább működjön. Az Azure SDK 2.4 diagnosztikai konfigurációs fájlja a diagnostics.wadcfg és az Azure SDK 2.5-ös és újabb diagnosztikai.wadcfgx.
* **A felhőszolgáltatás-alkalmazások diagnosztikája csak a szerepkör szintjén konfigurálható, a példány szintjén nem.**
* **Az alkalmazás minden telepítésekor frissül a diagnosztikai konfiguráció** – Ez paritásproblémákat okozhat, ha módosítja a diagnosztikai konfigurációt a Kiszolgálókezelőből, majd újratelepíti az alkalmazást.
* **Az Azure SDK 2.5-ös és újabb verzióiban az összeomlási memóriaképek a diagnosztikai konfigurációs fájlban vannak konfigurálva, nem pedig kódban** – Ha az összeomlási memóriaképek kódban vannak konfigurálva, manuálisan kell átvinnie a konfigurációt a kódból a konfigurációs fájlba, mert az összeomlási memóriaképek nem kerülnek átvitelre az Azure SDK 2.6-ba való áttelepítés során.

