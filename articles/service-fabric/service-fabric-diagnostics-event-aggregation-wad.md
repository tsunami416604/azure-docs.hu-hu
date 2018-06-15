---
title: Az Azure Service Fabric esemény összesítési a Windows Azure diagnosztikai |} Microsoft Docs
description: További tudnivalók összesítésére és események gyűjtése ÜVEGVATTA figyelési és diagnosztika Azure Service Fabric-fürt segítségével.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/03/2018
ms.author: srrengar
ms.openlocfilehash: 38a026e8995bb7384c866dcd2f12588ca816009f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205773"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Esemény összesítésére és az adatgyűjtést, a Windows Azure diagnosztikai
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Amikor egy Azure Service Fabric-fürtöt használ, célszerű gyűjteni a egy központi helyen található minden csomópontot. A naplók rendelkezik egy központi helyen segítségével elemezheti és a fürt, vagy a futó alkalmazások és szolgáltatások az adott fürtben található a problémák elhárításához.

Egy feltöltése és naplógyűjtéshez módja a Windows Azure diagnosztikai (ÜVEGVATTA) bővítménnyel Azure Storage naplók feltöltését, és naplókat küld Azure Application Insights vagy az Event Hubs lehetősége is van. Kiolvassa az eseményeket a tárolóból, és helyezze el őket egy analysis platform termék, például a külső folyamat is használhatja [Naplóelemzési](../log-analytics/log-analytics-service-fabric.md) vagy egy másik napló elemzési megoldás.

## <a name="prerequisites"></a>Előfeltételek
A következő eszközök használata ebben a cikkben:

* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Resource Manager-sablon](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-platform-events"></a>A Service Fabric platform események
A Service Fabric beállítja azt a néhány [out-of-az-box naplózási csatornák](service-fabric-diagnostics-event-generation-infra.md), azon a következő csatornák előre konfigurált, a bővítmény küldeni a figyelés és a tárolási táblához vagy máshol diagnosztikai adatok:
  * [A működési események](service-fabric-diagnostics-event-generation-operational.md): magasabb szintű műveletek a Service Fabric-platformról hajtja végre. Például alkalmazások és a szolgáltatások, a csomópont állapotváltozások és a frissítési információkat létrehozását. Ezek kibocsátott mint esemény Windows (nyomkövetés) naplók
  * [Reliable Actors programozási modell események](service-fabric-reliable-actors-diagnostics.md)
  * [Megbízható Services-programozási modell események](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>A portálon keresztül a diagnosztika bővítmény telepítése
A naplók gyűjtésére első lépése a diagnosztika bővítmény a Service Fabric-fürt virtuális gép méretezési készlet csomópontjain telepítendő. A diagnosztika bővítmény naplókat gyűjt mindegyik virtuális gépre, és feltölti a megadott tárfiók. Ennek végrehajtásához az Azure-portál és az Azure Resource Manager-sablonok segítségével új és meglévő fürt lépései.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>A fürt létrehozása az Azure portálon keresztül részeként diagnosztika bővítmény telepítése
A való létrehozásakor a fürthöz, a fürt konfigurációs lépés, bontsa ki a választható beállításokat, és győződjön meg arról, hogy diagnosztikai beállításai **a** (az alapértelmezett beállítás).

![A fürt létrehozása a portálon az Azure diagnosztikai beállításai](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

Erősen ajánlott, hogy töltse le a sablon **, végül kattintson a létrehozás** az utolsó lépésben. További információkért tekintse meg [Azure Resource Manager-sablonok használatával a Service Fabric-fürt beállított](service-fabric-cluster-creation-via-arm.md). A sablon módosításokat milyen csatornákon (fent felsorolt) származó adatok gyűjtésére van szüksége.

![Fürt sablon](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Most, hogy az eseményeket az Azure Storage most összesítése [Naplóelemzési beállítása](service-fabric-diagnostics-oms-setup.md) megismerésében, és lekérdezheti a Log Analytics-portálon

>[!NOTE]
>Jelenleg nincs mód való vagy készítsen fel a figyelmet, hogy a táblázatok küldött. Egy folyamat leállását események eltávolítja a tábla nem valósítja meg, ha a tábla egyre nő (az alapértelmezett maximális érték 50 GB). Ennek a következők módosítása útmutatást [ebben a cikkben alább további](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Pedig egy futó karcsúsítási szolgáltatás például a [figyelő minta](https://github.com/Azure-Samples/service-fabric-watchdog-service), és javasolt, hogy lehet írni egy a saját kezűleg is, kivéve ha egy jó 30 vagy 90 nap határidőn túli naplók tárolásához.

## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>Az Azure Resource Manageren keresztül diagnosztika-bővítmény telepítése

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Hozzon létre egy fürtöt a diagnosztika bővítmény
A fürt létrehozásához erőforrás-kezelő használatával kell hozzáadnia a diagnosztika konfigurációs JSON a teljes erőforrás-kezelő sablont a fürt létrehozása előtt. Egy minta öt-Virtuálisgép-fürt Resource Manager sablon nyújtunk a Resource Manager sablon minták részeként hozzáadott diagnosztika konfigurációjával. Ezen a helyen az Azure-minták oldalon láthatja: [öt csomópontból álló fürt diagnosztika Resource Manager sablon példával](https://azure.microsoft.com/en-in/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

A diagnosztika beállítást a Resource Manager-sablon megtekintéséhez nyissa meg az azuredeploy.json fájlt, és keresse meg **IaaSDiagnostics**. A fürt létrehozásához a sablon használatával, jelölje ki a **az Azure telepítéséhez** gomb érhető el az előző kapcsolat.

Azt is megteheti, töltse le az erőforrás-kezelő minta, módosítani és hozzon létre egy fürtöt a módosított sablon használatával a `New-AzureRmResourceGroupDeployment` parancsot egy Azure PowerShell-ablakban. Tekintse meg a következő kódot a paraméterek, amelyeket átad a a parancsot. Az erőforráscsoport PowerShell használatával történő központi telepítéséről részletes információkért lásd: a cikk [központi telepítése egy erőforráscsoportot az Azure Resource Manager sablon](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>A diagnosztika-bővítmény hozzáadása egy meglévő fürthöz
Ha meglévő fürtöt, amely nem rendelkezik telepített diagnosztika, adja hozzá, vagy frissítse a fürt sablon használatával. Módosítsa a Resource Manager-sablon, amellyel a meglévő fürt létrehozása, vagy a sablon letöltéséről a portálon, a fentebb leírt módon. A template.json fájl módosítása a következő feladatok végrehajtásával:

A sablon a források szakaszában ad hozzá egy új tárolási erőforrás hozzáadása.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 A következő után vesz fel a Paraméterek szakaszban csak a tárolási fiók definíciók között `supportLogStorageAccountName`. Cserélje le a helyőrzőket *ide kerül a tárfiók neve* szeretné a tárfiók nevével.

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
      "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
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

> [!TIP]
> Tároló üzembe helyezése a fürthöz fog, ha engedélyezi a ÜVEGVATTA hozzáadásával a docker statisztikák átvételéhez a **WadCfg > DiagnosticMonitorConfiguration** szakasz.
>
>```json
>"DockerSources": {
>    "Stats": {
>        "enabled": true,
>        "sampleRate": "PT1M"
>    }
>},
>```

### <a name="update-storage-quota"></a>Tárolási kvóta frissítése

A táblákat, a bővítmény által óta nő, amíg a kvóta talált, érdemes lehet figyelembe venni, a kvóta méretének csökkentése. Az alapértelmezett érték 50 GB-os és konfigurálható a sablon alapján a `overallQuotainMB` eleménél `DiagnosticMonitorConfiguration`

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Napló gyűjtemény konfigurációk
A további csatornák érhetők el naplók is gyűjtemény, Íme néhány a leggyakrabban használt használható konfigurációkat részletezik Azure-ban futó fürtök a sablonban.

* Működési csatorna - alap: Alapértelmezett, magas szintű műveleteket végzi el a Service Fabric és a fürt, beleértve az események várható, egy új alkalmazást telepített, a csomópont vagy egy frissítési visszaállítási által engedélyezett stb. Az események listája, [csatorna működési eseményeit](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-operational).
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Részletes működési csatorna -: Rendszerállapot-jelentések és a terheléselosztási döntések, valamint minden, az alapvető működési csatornán ilyenek. Ezeket az eseményeket az állapotfigyelő segítségével a rendszer vagy a kód által előállított vagy nem tölthető be, mint jelentéskészítési API-k [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) vagy [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Ezeket az eseményeket a Visual Studio diagnosztikai eseménynapló adja hozzá megtekintése "Microsoft-ServiceFabric:4:0x4000000000000008" ETW-szolgáltatók listáját.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Adatok és Messaging csatorna - alap: kritikus naplókat és az esemény jön létre a (jelenleg csak a ReverseProxy) üzenetküldési és elérési útja, továbbá részletes működési csatorna naplókba. Ezek az események a feldolgozási hibák és más kritikus fontosságú problémáit a a ReverseProxy, valamint a feldolgozott kérések. **Ez az átfogó naplózási javasoljuk**. Ezek az események a Visual Studio diagnosztikai eseménynapló megtekintéséhez adja hozzá a "Microsoft-ServiceFabric:4:0x4000000000000010" ETW-szolgáltatók listáját.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Adatok & Messaging csatorna - részletes: részletes csatornán, és azokat a nem kritikus naplókat, és a fürt és a részletes működési csatorna üzenetekkel adatokból. Részletes minden fordított proxy eseményt, tekintse meg a [fordított proxy diagnosztika útmutató](service-fabric-reverse-proxy-diagnostics.md).  Ezek az események a Visual Studio diagnosztikai eseménynapló megtekintéséhez adja hozzá a "Microsoft-ServiceFabric:4:0x4000000000000020" ETW-szolgáltatók listáját.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Ez a csatorna nagyon nagy mennyiségű esemény, a jelen engedélyezése eseménygyűjtés részletes nyomkövetések gyors létrehozás alatt számos csatorna eredményez, és felhasználhat a tárolási kapacitást. Csak kapcsolja be ezt a ha feltétlenül szükséges.


Ahhoz, hogy a **kiinduló adatforgalom és Messaging csatorna** átfogó naplózást, az ajánlott megoldás a `EtwManifestProviderConfiguration` a a `WadCfg` a sablon az alábbihoz hasonlóan fog kinézni a következő:

```json
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
                "scheduledTransferKeywordFilter": "4611686018427387928",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricSystemEventTable"
                }
              }
            ]
          }
        }
      },
```

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

Figyelés és diagnosztikai adatok küldése az Application Insights (AI) teheti a ÜVEGVATTA konfiguráció részeként. Ha úgy dönt, hogy az esemény elemzése és a képi megjelenítés AI használni, olvassa el a [egy AI fogadó beállításával](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template) a "WadCfg" részeként.

## <a name="next-steps"></a>További lépések

Miután konfigurálta az Azure diagnostics megfelelően, adatait jeleníti meg a tárolási táblákba a ETW és EventSource naplókból. Ha a Naplóelemzési, Kibana vagy bármely más elemzés és a képi megjelenítés adatplatform, amely közvetlenül nincs konfigurálva a Resource Manager-sablon használatát választja, győződjön meg arról, az Ön által választott, olvassa el a tárolási táblák adatait a platform beállításához. A Naplóelemzési esetében nagyon viszonylag egyszerű, és az ismertetése [esemény és a naplófájlok elemzése](service-fabric-diagnostics-event-analysis-oms.md). Az Application Insights egy bit egy különleges esetben abban az értelemben, a diagnosztika bővítménykonfiguráció részeként konfigurálhatók, mivel így tekintse meg a [megfelelő cikk](service-fabric-diagnostics-event-analysis-appinsights.md) Ha úgy dönt, hogy AI használja.

>[!NOTE]
>Jelenleg nincs mód való vagy készítsen fel a figyelmet, hogy a tábla küldött. Egy folyamat leállását események eltávolítja a tábla nem valósítja meg, ha a tábla egyre nő. Jelenleg egy futó karcsúsítási szolgáltatás például a [figyelő minta](https://github.com/Azure-Samples/service-fabric-watchdog-service), és javasolt, hogy lehet írni egy a saját kezűleg is, kivéve ha egy jó 30 vagy 90 nap határidőn túli naplók tárolásához.

* [Megtudhatja, hogyan gyűjti a teljesítményszámlálók és a naplókat a diagnosztika bővítmény segítségével](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Esemény elemzése és a képi megjelenítés az Application insights szolgáltatással](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Esemény elemzése és Naplóelemzési a képi megjelenítés](service-fabric-diagnostics-event-analysis-oms.md)
