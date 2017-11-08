---
title: "Az Azure Service Fabric esemény összesítési a Windows Azure diagnosztikai |} Microsoft Docs"
description: "További tudnivalók összesítésére és események gyűjtése ÜVEGVATTA figyelési és diagnosztika Azure Service Fabric-fürt segítségével."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: e417458a16a5f23d8b89cbf87ab2713fab352046
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Esemény összesítésére és az adatgyűjtést, a Windows Azure diagnosztikai
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Amikor egy Azure Service Fabric-fürtöt használ, célszerű gyűjteni a egy központi helyen található minden csomópontot. A naplók rendelkezik egy központi helyen segítségével elemezheti és a fürt, vagy a futó alkalmazások és szolgáltatások az adott fürtben található a problémák elhárításához.

Egy feltöltése és naplógyűjtéshez módja a Windows Azure diagnosztikai (ÜVEGVATTA) bővítménnyel Azure Storage naplók feltöltését, és naplókat küld Azure Application Insights vagy az Event Hubs lehetősége is van. Kiolvassa az eseményeket a tárolóból, és helyezze el őket egy analysis platform termék, például a külső folyamat is használhatja [OMS Naplóelemzési](../log-analytics/log-analytics-service-fabric.md) vagy egy másik napló elemzési megoldás.

## <a name="prerequisites"></a>Előfeltételek
Ezek az eszközök hajthatók végre műveleteket ebben a dokumentumban:

* [Az Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) (Azure Felhőszolgáltatások azonban az helyes útmutatást és példákat tartalmaz)
* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Az Azure Resource Manager-ügyfél](https://github.com/projectkudu/ARMClient)
* [Azure Resource Manager-sablon](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="log-and-event-sources"></a>Naplófájl és esemény források

### <a name="service-fabric-platform-events"></a>A Service Fabric platform események
A bemutatott [Ez a cikk](service-fabric-diagnostics-event-generation-infra.md), a Service Fabric beállítja, néhány out-of-az-box naplózási csatornák, amelyek a következő csatornák vannak könnyen konfigurálva ÜVEGVATTA küldeni a figyelési és diagnosztikai adatokat tároló táblára vagy máshol:
  * A működési események: magasabb szintű műveletek a Service Fabric-platformról hajtja végre. Például alkalmazások és a szolgáltatások, a csomópont állapotváltozások és a frissítési információkat létrehozását. Ezek kibocsátott mint esemény Windows (nyomkövetés) naplók
  * [Reliable Actors programozási modell események](service-fabric-reliable-actors-diagnostics.md)
  * [Megbízható Services-programozási modell események](service-fabric-reliable-services-diagnostics.md)

### <a name="application-events"></a>Alkalmazás-események
 Az alkalmazások és szolgáltatások kódból kibocsátott, és a Visual Studio sablonok találhatók az EventSource segítőosztály írhatók eseményeket. Az EventSource naplók írásával az alkalmazásról további információkért lásd: [figyelő és diagnosztizálhatja a helyi számítógép fejlesztési telepítő szolgáltatások](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

## <a name="deploy-the-diagnostics-extension"></a>A diagnosztika bővítmény telepítése
Az első lépés a naplók gyűjtésére központi telepítése a diagnosztika bővítmény minden egyes a Service Fabric-fürt a virtuális gépen. A diagnosztika bővítmény naplókat gyűjt mindegyik virtuális gépre, és feltölti a megadott tárfiók. A lépések kissé függően hogy használ-e az Azure portálon vagy az Azure Resource Manager változhat. A lépéseket is függően változnak, hogy a központi telepítés része a fürt létrehozása, vagy egy már létező fürt. Vizsgáljuk meg az egyes forgatókönyv lépéseit.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>A fürt létrehozása az Azure portálon keresztül részeként diagnosztika bővítmény telepítése
A diagnosztikai beállítások panel segítségével központilag telepíteni a diagnosztika bővítményt a virtuális gépeket a fürt fürt létrehozásának részeként, az alábbi ábrának - győződjön meg arról, hogy diagnosztikai beállításai **a** (az alapértelmezett beállítás). A fürt létrehozása után ezeket a beállításokat nem módosíthatja a portál használatával.

![A fürt létrehozása a portálon az Azure diagnosztikai beállításai](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics.png)

A fürt létrehozásakor a portál használatával erősen ajánlott, hogy töltse le a sablon **OK gombra kattintás előtt** a fürt létrehozásához. További információkért tekintse meg [Azure Resource Manager-sablonok használatával a Service Fabric-fürt beállított](service-fabric-cluster-creation-via-arm.md). A módosítások később, a sablon lesz szüksége, mert nem módosítja a portál használatával.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>A fürt létrehozásának részeként diagnosztika bővítmény telepítése Azure Resource Manager használatával
Fürt létrehozásához erőforrás-kezelő használatával kell hozzáadnia a diagnosztika konfigurációs JSON a teljes fürt Resource Manager-sablon, a fürt létrehozása előtt. Egy minta öt-Virtuálisgép-fürt Resource Manager sablon nyújtunk a Resource Manager sablon minták részeként hozzáadott diagnosztika konfigurációjával. Ezen a helyen az Azure-minták oldalon láthatja: [öt csomópontból álló fürt diagnosztika Resource Manager sablon példával](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad).

A diagnosztika beállítást a Resource Manager-sablon megtekintéséhez nyissa meg az azuredeploy.json fájlt, és keresse meg **IaaSDiagnostics**. A fürt létrehozásához a sablon használatával, jelölje ki a **az Azure telepítéséhez** gomb érhető el az előző kapcsolat.

Azt is megteheti, töltse le az erőforrás-kezelő minta, módosítani és hozzon létre egy fürtöt a módosított sablon használatával a `New-AzureRmResourceGroupDeployment` parancsot egy Azure PowerShell-ablakban. Tekintse meg a következő kódot a paraméterek, amelyeket átad a a parancsot. Az erőforráscsoport PowerShell használatával történő központi telepítéséről részletes információkért lásd: a cikk [központi telepítése egy erőforráscsoportot az Azure Resource Manager sablon](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>A diagnosztika bővítmény telepítése egy meglévő fürthöz
Ha egy meglévő fürtöt, amely nem rendelkezik telepített diagnosztika, vagy ha azt szeretné, hogy egy meglévő konfigurációjának módosítása, adja hozzá, vagy frissítheti azt. Módosítsa a Resource Manager-sablon, amellyel a meglévő fürt létrehozása, vagy a sablon letöltéséről a portálon, a fentebb leírt módon. A template.json fájl módosítása a következő feladatok végrehajtásával.

A sablon a források szakaszában ad hozzá egy új tárolási erőforrás hozzáadása.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 A következő után vesz fel a Paraméterek szakaszban csak a tárolási fiók definíciók között `supportLogStorageAccountName` és `vmNodeType0Name`. Cserélje le a helyőrzőket *ide kerül a tárfiók neve* a tárfiók nevével.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Ezt követően frissítse a `VirtualMachineProfile` a template.json fájl a következő kódrészletet a bővítmények tömbön belüli szakaszában. Ne feledje hozzáadni vesszővel elején vagy végén, attól függően, hogy hol csatlakoztatva van-e.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Lásd a template.json fájl módosítása, után közzé a Resource Manager-sablon. Ha a sablon exportált, a sablon a deploy.ps1 fájlt futtatja addig. Miután telepít, győződjön meg arról, hogy **ProvisioningState** van **sikeres**.

## <a name="collect-health-and-load-events"></a>Állapot gyűjtése és események betöltése

A Service Fabric 5.4 kiadástól kezdve, állapotát és a terheléselosztási metrika események állnak rendelkezésre a gyűjteményhez. Ezek az események tükrözze az állapotfigyelő segítségével a rendszer vagy a kód által előállított eseményeket vagy nem tölthető be, mint jelentéskészítési API-k [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) vagy [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Ez lehetővé teszi, hogy összesítésére és idővel állapotának megtekintése és a riasztás állapotát vagy a betöltési események alapján. Ezeket az eseményeket a Visual Studio diagnosztikai eseménynapló adja hozzá megtekintése "Microsoft-ServiceFabric:4:0x4000000000000008" ETW-szolgáltatók listáját.

A fürt az események összegyűjtésére, módosítsa a `scheduledTransferKeywordFilter` a Resource Manager sablon WadCfg a `4611686018427387912`.

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387912",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

## <a name="collect-reverse-proxy-events"></a>Fordított proxy eseményeinek gyűjtése

A Service Fabric 5.7 kiadástól kezdve [fordított proxy](service-fabric-reverseproxy.md) események gyűjtemény adat & Messaging csatornákon keresztül érhetők el. 

A fordított proxy a fő adatok & Messaging-csatornán keresztül - feldolgozási hibák és a kritikus fontosságú problémáit tükröző csak hibaesemények leküldéses értesítések. A részletes csatorna a fordított proxy által feldolgozott összes kérelem kapcsolatos részletes eseményeket tartalmazza. 

Megtekintéséhez a Visual Studio diagnosztikai eseménynapló hiba események hozzáadása "Microsoft-ServiceFabric:4:0x4000000000000010" ETW-szolgáltatók listáját. A – kéréstelemetria frissítés a Microsoft-ServiceFabric bejegyzés ETW szolgáltató listában "Microsoft-ServiceFabric:4:0x4000000000000020".

Azure-ban futó fürtök:

A nyomkövetési adatokat abban a fő adatok & Messaging csatorna átvételéhez, módosítsa a `scheduledTransferKeywordFilter` érték a Resource Manager sablon WadCfg `4611686018427387920`.

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387920",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

Minden Kérelemfeldolgozás eseményeinek gyűjtése, kapcsolja be az adatok & Messaging - részletes csatorna módosításával a `scheduledTransferKeywordFilter` érték a Resource Manager sablon WadCfg `4611686018427387936`.

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387936",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

Jelen gyűjtését események engedélyezése részletes nyomkövetések gyors létrehozás alatt számos csatorna eredményez, és felhasználhat a tárolási kapacitást. Csak kapcsolja be ezt a feltétlenül szükséges.
Részletes fordított proxy eseményeket, tekintse meg a [fordított proxy diagnosztika útmutató](service-fabric-reverse-proxy-diagnostics.md).

## <a name="collect-from-new-eventsource-channels"></a>Új EventSource csatornák gyűjtése

Frissíteni a diagnosztikai naplók összegyűjtésére új EventSource csatornák, amelyek megfelelnek egy új alkalmazást, hogy Ön éppen kapcsolatos telepítéséhez hajtsa végre azonos fürt a meglévő diagnosztikai beállításának korábban ismertetett lépéseket.

Frissítse a `EtwEventSourceProviderConfiguration` az template.json fájl bejegyzés hozzáadása előtt, a konfiguráció új EventSource csatornák használatával módosítsa a `New-AzureRmResourceGroupDeployment` PowerShell-parancsot. A forrás nevét a kódot, a Visual Studio által létrehozott ServiceEventSource.cs fájl részeként van definiálva.

Ha a forrás saját Eventsource neve, adja hozzá például a következő kódot a saját Eventsource eseményei helyezzen MyDestinationTableName nevű tábla.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Gyűjti az teljesítményszámlálók és az eseménynaplók, módosítsa a Resource Manager-sablon a megadott példák felhasználásával [figyelése és diagnosztika Windows virtuális gép létrehozása Azure Resource Manager-sablon használatával](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). A Resource Manager-sablon, majd közzé.

## <a name="collect-performance-counters"></a>A teljesítményszámlálók adatainak összegyűjtése

Teljesítményadatok gyűjtéséhez a fürtről, a teljesítményszámlálók hozzáadása a "WadCfg > DiagnosticMonitorConfiguration" a Resource Manager sablon a fürt számára. Lásd: [ÜVEGVATTA teljesítményfigyelés](service-fabric-diagnostics-perf-wad.md) lépéseket módosítani a `WadCfg` specifikus teljesítményszámlálók adatainak összegyűjtése. Hivatkozás [Service Fabric teljesítményszámlálók](service-fabric-diagnostics-event-generation-perf.md) teljesítmény listáját, amelyek teljesítményszámlálók javasoljuk a összegyűjtése.
  
Ha Ön egy Application Insights fogadó használja a következő szakaszban leírtak szerint, és ezeket a metrikákat az Application Insights megjelennek, majd győződjön meg arról, a fogadó név hozzáadásához, ahogy fent látható "mosdók" szakaszában. Ez küld a teljesítményszámlálót mutat be, külön-külön vannak konfigurálva az Application Insights-erőforrást.


## <a name="send-logs-to-application-insights"></a>Az Application Insights elküldeni a naplókat

Figyelés és diagnosztikai adatok küldése az Application Insights (AI) teheti a ÜVEGVATTA konfiguráció részeként. Ha úgy dönt, hogy az esemény elemzése és a képi megjelenítés AI használni, olvassa el a [esemény elemzése és az Application insights szolgáltatással a képi megjelenítés](service-fabric-diagnostics-event-analysis-appinsights.md) egy AI fogadó beállítása a "WadCfg" részeként.

## <a name="next-steps"></a>Következő lépések

Miután konfigurálta az Azure diagnostics megfelelően, adatait jeleníti meg a tárolási táblákba a ETW és EventSource naplókból. Ha OMS, Kibana vagy bármely más elemzés és a képi megjelenítés adatplatform, amely közvetlenül nincs konfigurálva a Resource Manager-sablon használatát választja, győződjön meg arról, hogy az Ön által választott, olvassa el a tárolási táblák adatait a platform beállításához. Az OMS nagyon viszonylag egyszerű, és az ismertetése [esemény és a naplófájlok elemzése OMS keresztül](service-fabric-diagnostics-event-analysis-oms.md). Az Application Insights egy bit egy különleges esetben abban az értelemben, a diagnosztika bővítménykonfiguráció részeként konfigurálhatók, mivel így tekintse meg a [megfelelő cikk](service-fabric-diagnostics-event-analysis-appinsights.md) Ha úgy dönt, hogy AI használja.

>[!NOTE]
>Jelenleg nincs mód való vagy készítsen fel a figyelmet, hogy a tábla küldött. Egy folyamat leállását események eltávolítja a tábla nem valósítja meg, ha a tábla egyre nő. Jelenleg egy futó karcsúsítási szolgáltatás például a [figyelő minta](https://github.com/Azure-Samples/service-fabric-watchdog-service), és javasolt, hogy lehet írni egy a saját kezűleg is, kivéve ha egy jó 30 vagy 90 nap határidőn túli naplók tárolásához.

* [Megtudhatja, hogyan gyűjti a teljesítményszámlálók és a naplókat a diagnosztika bővítmény segítségével](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Esemény elemzése és a képi megjelenítés az Application insights szolgáltatással](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Esemény elemzése és az OMS képi megjelenítés](service-fabric-diagnostics-event-analysis-oms.md)