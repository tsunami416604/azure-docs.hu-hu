---
title: "Az Azure Diagnostics kiterjesztés konfigurációs séma verziója és előzmények |} Microsoft Docs"
description: "Kapcsolódik az Azure virtuális gépek, a Virtuálisgép-méretezési készlet, a Service Fabric és a felhőalapú szolgáltatások teljesítményszámlálói gyűjtése."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/16/2017
ms.author: robb
ms.openlocfilehash: 119e8a237f24cdc80a1ab8e376f2b308c9eada05
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-diagnostics-extention-configuration-schema-versions-and-history"></a>Az Azure Diagnostics kiterjesztés konfigurációs séma verziók és előzményei
A lap indexek Azure Diagnostics bővítmény séma verziók rendszerrel szállított, a Microsoft Azure SDK részeként.  

> [!NOTE]
> Az Azure Diagnostics kiterjesztés összetevőt használja a teljesítményszámlálók és más Statisztika gyűjtése:
> - Azure-alapú virtuális gépek 
> - Virtual Machine Scale Sets
> - Service Fabric 
> - Cloud Services 
> - Network Security Groups (Hálózati biztonsági csoportok)
> 
> Ezen a lapon csak fontos, ha ilyen szolgáltatást használ.

Az Azure Diagnostics kiterjesztést más Microsoft-diagnosztika termékekkel, például Azure figyelő, az Application Insights és Naplóelemzési használja. További információ: [figyelés eszközök áttekintése a Microsoft](monitoring-overview.md).

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>A diagram szállítási Azure SDK-t és a diagnosztika verziók  

|Az Azure SDK-verzió | Diagnosztika kiterjesztés verziója | Modell|  
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



 Az Azure Diagnostics 1.0-s verziója a beépülő modul modelljének – ami azt jelenti, hogy az Azure SDK telepítésekor portáltól Azure diagnostics verziója először szállítani szállított vele.  

 Egy bővítmény modell Azure diagnostics (diagnosztika 1.2-es verzió), SDK 2.5 kezdve lett. Új szolgáltatások magukat, hogy az eszközök csak Azure SDK-k újabb érhető el, de bármely szolgáltatás használata az Azure diagnostics volna átvételéhez szállítási legfrissebb közvetlenül az Azure-ból. Például SDK 2.5 továbbra is felhasználójánál volna tölthet előző táblázatban látható, függetlenül a újabb funkciók használata a legújabb verzióra.  

## <a name="schemas-index"></a>Sémák index  
Az Azure diagnostics különböző verziói különböző konfigurációs sémák használja. 

[Diagnosztika 1.0 konfigurációs séma](azure-diagnostics-schema-1dot0.md)  

[Diagnosztika 1.2-es konfigurációs séma](azure-diagnostics-schema-1dot2.md)  

[1.3 diagnosztikai és a későbbi konfigurációs séma](azure-diagnostics-schema-1dot3-and-later.md)  

## <a name="version-history"></a>Verzióelőzmények


### <a name="diagnostics-extension-19"></a>Diagnosztika bővítmény 1.9 
Docker támogatja.


### <a name="diagnostics-extension-181"></a>1.8.1 diagnosztika bővítmény 
Megadhatja a saját config egy SAS-token helyett a tárfiók kulcsára. Ha egy SAS-jogkivonat van megadva, a rendszer figyelmen kívül hagyja a tárfiók kulcsára.


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


### <a name="diagnostics-extension-18"></a>Diagnosztika bővítmény 1.8 
A hozzáadott tárolási típus PublicConfig. StorageType lehet *tábla*, *Blob*, *TableAndBlob*. *Tábla* az alapértelmezett beállítás.


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


### <a name="diagnostics-extension-17"></a>Diagnosztika bővítmény 1.7 
Mostantól lehetőség van a Eventhubhoz való irányításához.

### <a name="diagnostics-extension-15"></a>1.5-ös diagnosztika bővítmény
Az mosdók elemet, és diagnosztikai adatok küldése hozzáadott [Application Insights](../application-insights/app-insights-cloudservices.md) így könnyebben eseményadatokat az alkalmazást, valamint a rendszer és az infrastruktúra szint között.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Az Azure SDK 2.6 és diagnosztika bővítmény 1.3 
A Visual Studio Felhőszolgáltatás-projektek a következő változások történtek. (Ezek a változások vonatkoznak Azure SDK újabb verziói.)

* A helyi emulátor mostantól támogatja a diagnosztika. Ez azt jelenti, hogy diagnosztikai adatokat gyűjteni, és győződjön meg arról, az alkalmazás hoz létre a megfelelő nyomkövetési adatokat, amíg Ön fejlesztése és tesztelése a Visual Studio. A kapcsolati karakterlánc `UseDevelopmentStorage=true` diagnosztikai adatok gyűjtésének lehetővé teszi a futtatása közben a felhőszolgáltatási projektet a Visual Studio az Azure storage emulator használatával. Minden diagnosztikai adatgyűjtés (fejlesztés) tárolási tárfiókban.
* A diagnosztika tárolási fiók kapcsolati karakterlánc (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) még egyszer a szolgáltatás konfigurációs (.cscfg) fájljában tárolódik. Az Azure SDK 2.5 a diagnosztikai tárfiók a diagnostics.wadcfgx fájlban lett megadva.

Bizonyos különbségek vannak a figyelmet a jelentősebb hogyan a kapcsolati karakterláncot az Azure SDK 2.4 és a korábbi munkaidő-e, és hogyan működik az Azure SDK 2.6-os és újabb verziók között.

* Azure SDK 2.4 és korábbi a kapcsolati karakterlánc volt megadva a futásidejű a diagnosztika beépülő modul által megszerezni a diagnosztikai naplók átvitelére a tárfiók adatait.
* Azure SDK 2.6 vagy újabb rendszerben a diagnosztika kapcsolati karakterlánc segítségével Visual Studio konfigurálása a diagnosztika bővítmény közzététele során a megfelelő tárolási fiók adatait. A kapcsolati karakterláncot adja meg a Visual Studio fogja használni, amikor a közzétételi különböző szolgáltatáskonfiguráció eltérő tárfiókokból teszi lehetővé. Azonban a diagnosztika beépülő modul (az Azure SDK 2.5) után már nem érhető el, mert a .cscfg fájlban önmagában nem engedélyezi a diagnosztika bővítményt. Engedélyezi a bővítményt, például a PowerShell vagy Visual Studio eszközök keresztül külön-külön kell.
* A diagnosztika bővítmény konfigurálása a PowerShell segítségével egyszerűbbé, hogy a csomag kimenetét a Visual Studio a nyilvános konfigurációs XML-t a diagnosztika bővítmény, az egyes szerepkörökhöz is tartalmaz. A Visual Studio a diagnosztika kapcsolati karakterlánc használatával feltöltése a tárfiók adatait a nyilvános konfigurációs szerepel. A nyilvános konfigurációs fájlok jönnek létre a bővítmények mappában, és kövesse a minta PaaSDiagnostics. <RoleName>. PubConfig.xml. A PowerShell-alapú telepítések ebben a mintában segítségével minden egyes konfigurációs hozzárendelése egy szerepkörhöz.
* A kapcsolati karakterláncot a .cscfg fájlban is használják az Azure-portálon a diagnosztikai adatok eléréséhez, így megjeleníthető a **figyelés** fülre. A kapcsolati karakterlánc részletes figyelési adatait a portálon megjeleníteni kívánt szolgáltatás konfigurálásához szükséges.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Áttelepítése projektek Azure SDK 2.6 vagy újabb
Az áttelepítés alatt az Azure SDK 2.5 az Azure SDK 2.6 vagy újabb kiadására, ha a .wadcfgx fájlban megadott diagnosztikai tárfiók, majd marad van. Különböző tárolókonfigurációkkal eltérő tárfiókokból rugalmasan előnyeit, meg kell manuálisan a kapcsolati karakterlánc hozzáadása a projekthez. Ha az Azure SDK 2.4 vagy korábbi verzióról Azure SDK 2.6, amelybe migrálna projekt, a diagnosztika kapcsolati karakterláncok megmaradnak. Vegye azonban figyelembe változásait hogyan kapcsolati karakterláncok számít az Azure SDK 2.6 megadott az előző szakaszban.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Hogyan határozza meg a Visual Studio a diagnosztikai tárfiók
* A diagnosztika kapcsolódási karakterlánc esetén a .cscfg fájlban, Visual Studio használja a diagnosztika bővítményének beállítását közzétételekor, és a nyilvános konfigurációs XML-fájlok során a csomag létrehozásakor.
* Nincs diagnosztika kapcsolódási karakterlánc esetén a .cscfg fájlban, majd a Visual Studio visszaáll a diagnosztika bővítmény közzététele, és a nyilvános konfigurációs XML-fájlok létrehozása, amikor csomagolás konfigurálása a .wadcfgx fájlban megadott tárfiók segítségével.
* A diagnosztika kapcsolati karakterláncot a .cscfg fájlban élvez a tárfiók a .wadcfgx fájlban. A diagnosztika kapcsolódási karakterlánc esetén a .cscfg fájlban, Visual Studio ezen, és figyelmen kívül hagyja a .wadcfgx storage-fiókot.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>A "frissítés fejlesztési tárolási kapcsolati karakterláncok..." funkciója jelölőnégyzet elvégezni?
Jelölőnégyzetét **fejlesztési tárolási kapcsolati karakterláncok frissítése a diagnosztika és a gyorsítótár Microsoft Azure storage-fiók hitelesítő adataival történő Microsoft Azure közzététel** bármely fejlesztési tárfiók kapcsolati karakterláncainak frissítéséhez az Azure storage-fiók közzététele során megadott kényelmes módot biztosít.

Tegyük fel például, a bejelöli ezt a jelölőnégyzetet, és a diagnosztika kapcsolati karakterlánc határoz meg `UseDevelopmentStorage=true`. A projektet az Azure-ba való közzétételekor a Visual Studio automatikusan frissíti a diagnosztika kapcsolati karakterláncot a közzététel varázslóban megadott tárfiók. Azonban ha egy valódi tárfiók diagnosztika-kapcsolati karakterláncként megadva, majd, hogy a fiókot használja.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diagnosztikai funkciók eltérései Azure SDK 2.4 és a korábbi és az Azure SDK 2.5-ös és újabb verziók
Frissítés a projekthez az Azure SDK 2.4 az Azure SDK 2.5-ös vagy újabb kiadására, meg kell figyelembe kell vennie az alábbi diagnosztikai funkciót különbségeket.

* **Konfigurációs API-k elavultak** – programozható konfigurációja diagnosztikai Azure SDK 2.4 vagy korábbi verzió érhető el, de az Azure SDK 2.5-ös vagy újabb elavult. Ha a diagnosztika jelenleg definiált kódban, szüksége lesz, hogy engedélyezzék ezeket a beállításokat az áttelepített projekt ahhoz, hogy diagnosztika a teljesen új gombra. A diagnosztika konfigurációs fájl az Azure SDK 2.4 diagnostics.wadcfg, de diagnostics.wadcfgx az Azure SDK 2.5 és újabb verzióihoz.
* **Diagnosztika a felhőalapú szolgáltatás alkalmazások csak a szerepkörök szintjén, a példány szintjén nem állítható be.**
* **Minden alkalommal, amikor telepíti az alkalmazást, a diagnosztikai konfigurációja frissül** – Ha a Server Explorer a diagnosztika konfigurációjának módosítása, és ezután telepítse újra az alkalmazást paritású problémák léphetnek.
* **Azure SDK 2.5-ös vagy újabb rendszerben összeomlási memóriaképek vannak-e konfigurálva a diagnosztika konfigurációs fájlban, nem a kódban** – Ha összeomlási memóriaképek konfigurálva a kódban, összekapcsolta manuális átvitele a konfigurációs kódot a konfigurációs fájlban, mert a összeomlási memóriaképek Azure SDK 2.6 az áttelepítés során nem kerülnek.

