---
title: Az Azure Service Fabric esemény összesítés a Windows Azure Diagnostics |} A Microsoft Docs
description: További tudnivalók összesítése és WAD használatával monitorozást és diagnosztikát az Azure Service Fabric-fürtök események gyűjtése.
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
ms.openlocfilehash: afc833775894a01e8061401fe7601267f09edded
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243244"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Események összesítése és -gyűjteményt Windows Azure Diagnostics használatával
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Egy Azure Service Fabric-fürtön futtatja, esetén érdemes egy központi helyen összes csomópontja a naplók gyűjtését. A naplók kellene egy központi helyen segítségével elemezheti és a fürtben, vagy az alkalmazások és szolgáltatások a fürtben futó problémák elhárítása.

Fel-és naplók gyűjtése az egyik módja, hogy használja a Windows Azure Diagnostics (WAD) bővítmény, amely feltölti a naplókat az Azure Storage, és a naplók elküldése az Azure Application Insights és az Event Hubs lehetősége is van. Használhatja egy külső folyamatba, olvassa az eseményeket a storage-ból, és helyezze el őket olyan elemzési felhőplatform-megoldás, mint például [naplózza az Azure Monitor](../log-analytics/log-analytics-service-fabric.md) vagy egy másik log-elemzési megoldás.

## <a name="prerequisites"></a>Előfeltételek
A következő eszközök vannak a cikk ezt használja:

* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Resource Manager-sablon](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-platform-events"></a>A Service Fabric platformot események
A Service Fabric beállítja azt a néhány [-a-beépített naplózási csatornák](service-fabric-diagnostics-event-generation-infra.md), amely a következő csatornák előre konfigurált, a bővítmény küldése a figyelési és diagnosztikai adatokat egy tárolótáblában vagy máshol is:
  * [A működési események](service-fabric-diagnostics-event-generation-operational.md): magasabb szintű műveletek, amelyek a Service Fabric platformot hajt végre. Ilyenek például az alkalmazások és a szolgáltatások, a csomópont állapota megváltozik és a frissítési információk létrehozása. Ezek vannak többszöröseként kibocsátott esemény-nyomkövetése Windows (ETW) naplók
  * [A Reliable Actors programozási modell események](service-fabric-reliable-actors-diagnostics.md)
  * [A Reliable Services programozási modell események](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>A diagnosztikai bővítményt a portálon keresztül üzembe helyezése
Az első lépés a naplók gyűjtésére a diagnosztikai bővítményt a Service Fabric-fürt virtuális gép méretezési készlet csomópontjain telepítendő. A diagnosztikai bővítmény naplókat gyűjti össze az egyes virtuális Gépeken, és feltölti őket az Ön által megadott tárfiókot. Hogyan teheti ezt az Azure Portalon és az Azure Resource Manager-sablonok segítségével új és meglévő fürtök lépései.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>A fürt létrehozása az Azure Portalon keresztül részeként a diagnosztikai bővítmény telepítése
A fürt a fürt konfigurációs lépésben létrehozásakor bontsa ki a választható beállításokat, és győződjön meg arról, hogy diagnosztikai beállítása **a** (az alapértelmezett beállítás).

![A portálon a fürt létrehozása az Azure diagnosztikai beállítások](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

Határozottan javasoljuk, hogy a sablon letöltése **létrehozása gombra való kattintás előtt** az utolsó lépésben. Részletekért tekintse meg a [Service Fabric-fürt beállítása az Azure Resource Manager-sablon használatával](service-fabric-cluster-creation-via-arm.md). A sablont, hogy módosításait mely csatornákon (fent) származó adatok gyűjtésére van szüksége.

![Fürt sablon](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Most, hogy meg van összesítésével események az Azure Storage [beállítása az Azure Monitor naplóira](service-fabric-diagnostics-oms-setup.md) információkhoz juthat, és lekérdezheti, ha az Azure Monitor naplózza a portálon

>[!NOTE]
>Nem lehet szűrni vagy a táblák küldött események karcsúsítása jelenleg nem. Ha egy folyamat események eltávolítja a tábla nem alkalmazza, a tábla egyre nő (az alapértelmezett korlát a következő 50 GB-ot). Ennek a következők módosítása útmutatást [használja a lentebb ebben a cikkben](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Emellett van egy futó karcsúsítási szolgáltatás például a [figyelő minta](https://github.com/Azure-Samples/service-fabric-watchdog-service), ajánlott, hogy Ön írása ilyennel, kivéve, ha van egy jó oka, hogy 30 vagy 90 nap határidőn túli naplók tárolására.



## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>A diagnosztikai bővítmény az Azure Resource Manager üzembe helyezése

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>A diagnosztikai bővítmény-fürt létrehozása
Fürt létrehozása a Resource Manager használatával, hozzá kell a diagnosztikai konfigurációs JSON-fájlt a teljes Resource Manager-sablont. Diagnosztikai konfiguráció a Resource Manager-sablonminták részeként hozzáadja biztosítunk egy mintát öt virtuális fürt Resource Manager-sablon. Azt az Azure-minták galériájában található ezen a helyen tekintheti meg: [Diagnosztika a Resource Manager-sablon minta az ötcsomópontos fürt](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

A Resource Manager-sablonban a diagnosztikai beállítás megtekintéséhez nyissa meg az azuredeploy.json fájlt, és keresse meg **IaaSDiagnostics**. Ez a sablon használatával a fürt létrehozásához válassza a **üzembe helyezés az Azure** gombra a fenti hivatkozáson érhető el.

Azt is megteheti, töltse le a Resource Manager-mintát, módosításához és a fürt létrehozása a módosított sablon használatával a `New-AzureRmResourceGroupDeployment` parancsot egy Azure PowerShell-ablakban. Tekintse meg a következő kódot a paraméterek, amelyek adja át a következő paranccsal. Egy erőforráscsoport PowerShell használatával történő központi telepítéséről részletes információkért tekintse meg a cikket [egy erőforráscsoportot az Azure Resource Manager-sablon üzembe helyezése](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>A diagnosztikai bővítmény hozzáadása egy meglévő fürthöz
Ha meglévő fürtöt, amely nem rendelkezik telepített diagnosztikai, adja hozzá, vagy frissítse a fürt sablon használatával. Módosítsa a Resource Manager-sablon, amellyel a meglévő fürt létrehozásához, vagy letöltheti a sablont a portálról korábban leírtaknak megfelelően. Módosítsa a template.json fájlt az alábbi feladatok végrehajtásával:

Új storage-erőforrás hozzáadása a sablonhoz az erőforrás szakasz való hozzáadásával.

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

 Ezután adja hozzá a paraméterek szakasz után a storage-fiók definíciókat, között `supportLogStorageAccountName`. Cserélje le a helyőrző szöveg *ide kerül a tárfiók neve* szeretné a tárfiók nevére.

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
Ezután frissítse a `VirtualMachineProfile` a template.json fájlt adja hozzá a következő kódot a bővítmények tömbön belüli szakaszában. Ügyeljen arra, hogy egy vessző hozzáadásával elején vagy végén, attól függően, hol csatlakoztatva van.

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

Miután a leírtak szerint módosítja a template.json fájlt, tegye közzé újra a Resource Manager-sablon. Ha a sablon exportálva lett, addig a deploy.ps1 fájlt futtatja az a sablon. Miután telepít, győződjön meg arról, hogy **ProvisioningState** van **sikeres**.

> [!TIP]
> Ha a tárolók üzembe helyezése a fürtön, csomópontmetrikák felvételéhez adja hozzá ezt a docker-stats WAD engedélyezése a **WadCfg > DiagnosticMonitorConfiguration** szakaszban.
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

A táblák a bővítmény által óta növekszik elérte a kvótát, amíg érdemes lehet megfontolni a kvóta méretét. Az alapértelmezett érték 50 GB és konfigurálható a sablon alapján a `overallQuotaInMB` eleménél `DiagnosticMonitorConfiguration`

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Napló gyűjtése konfigurációk
További csatornák származó naplók is elérhetők a gyűjteményhez, Íme néhány a leggyakoribb használható konfigurációkat az Azure-ban futó fürtök a sablonban.

* Műveleti csatorna - alapja: Magas szintű, a Service Fabric és a fürtöt, egy csomópontot hamarosan, egy új alkalmazást, üzembe helyezéséhez vagy egy frissítési visszaállítási események által végrehajtott műveletek alapértelmezés szerint engedélyezve van-e stb. Események listája, tekintse meg a [műveleti csatorna események](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-operational).
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Műveleti csatorna - részletes: Ez magában foglalja a rendszerállapot-jelentések és a terheléselosztási döntéseket hozhat, valamint az alap műveleti csatorna mindent. Ezeket az eseményeket az állapotfigyelő használatával vagy a rendszer, vagy a kód által előállított, vagy például betölteni a jelentéskészítő API-kat [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) vagy [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Ezeket az eseményeket a diagnosztikai eseménynapló a Visual Studióban adjon hozzá megtekintése "a Microsoft-ServiceFabric:4:0x4000000000000008" ETW-szolgáltatók listájára.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Adatok és a csatorna - alapja: Kritikus fontosságú naplókat és az esemény jön létre az üzenetküldés (jelenleg csak a ReverseProxy) és az adatok elérési útja, emellett a részletes műveleti csatorna naplókhoz. Ezek az események olyan kérelemfeldolgozási hibák és a Reverseproxyhoz más kritikus fontosságú problémáit, valamint a feldolgozott kérések. **Ez az átfogó naplózási Javaslataink**. Ezeket az eseményeket a Visual Studio diagnosztikai eseménynapló megtekintéséhez adja hozzá a "Microsoft-ServiceFabric:4:0x4000000000000010" ETW-szolgáltatók listájára.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Adat & csatorna - részletes: Részletes csatorna, amely tartalmazza a nem kritikus fontosságú naplók az adatok és a fürt és a részletes műveleti csatorna-üzenetkezeléstől. Részletes hibaelhárítási az összes fordított proxy esemény tekintse meg a [fordított proxy diagnosztikai útmutató](service-fabric-reverse-proxy-diagnostics.md).  Ezeket az eseményeket a Visual Studio diagnosztikai eseménynapló megtekintéséhez adja hozzá a "Microsoft-ServiceFabric:4:0x4000000000000020" ETW-szolgáltatók listájára.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Ez a csatorna egy nagyon nagy mennyiségű esemény van, engedélyezése eseménygyűjtés, ez a részletes nyomkövetési gyorsan létrehozott rengeteg csatorna eredményez, és felhasználhatja a tárolási kapacitás. Csak kapcsolja be ezt a feltétlenül szükség.


Ahhoz, hogy a **alap műveleti csatorna** Javaslataink a lehető legkevesebb zaj, teljes körű naplózás a `EtwManifestProviderConfiguration` a a `WadCfg` a sablon a következő jelenne meg:

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
```

## <a name="collect-from-new-eventsource-channels"></a>Új EventSource csatornák gyűjtése

Frissíteni a diagnosztikai naplók gyűjtését új EventSource csatornák, amelyek egy új alkalmazást, hogy Ön kapcsolatos üzembe helyezéséhez végezhet azonos fürt a meglévő diagnosztika beállítása az előzőleg ismertetett lépéseket.

Frissítse a `EtwEventSourceProviderConfiguration` szakaszban lévő bejegyzéseket hozzáadása előtt a konfiguráció alkalmazásához új EventSource csatornák használatával módosítsa a template.json fájlt a `New-AzureRmResourceGroupDeployment` PowerShell-parancsot. Az eseményforrás nevét a kód a Visual Studio által létrehozott ServiceEventSource.cs fájl részeként van definiálva.

Például az eseményforrás neve saját Eventsource, adja hozzá a következő kódot a saját Eventsource eseményei helyezze MyDestinationTableName nevű táblát.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Gyűjtendő teljesítményszámlálókat vagy az eseménynaplókat, módosítsa a Resource Manager-sablon használatával a megadott példák [Windows virtuális gép létrehozása figyelési és diagnosztikai funkciókkal Azure Resource Manager-sablon használatával](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ezt követően tegye közzé újra a Resource Manager-sablon.

## <a name="collect-performance-counters"></a>Teljesítményszámlálók gyűjtése

Teljesítmény-mérőszámokat gyűjthet a fürtről, a teljesítményszámlálók hozzáadása a "WadCfg > DiagnosticMonitorConfiguration" az a Resource Manager-sablon a fürt számára. Lásd: [teljesítményfigyelés WAD használatával](service-fabric-diagnostics-perf-wad.md) lépéseit módosítása a `WadCfg` összegyűjtéséhez a teljesítményszámlálókat. Hivatkozás [Service Fabric – teljesítményszámlálók](service-fabric-diagnostics-event-generation-perf.md) teljesítmény listáját teljesítményszámlálók, amelyek esetében azt javasoljuk, gyűjtéséhez.
  
Ha egy Application Insights fogadó használja az alábbi szakaszban leírtak szerint, és szeretné ezeket a metrikákat az Application Insights jelenik meg, majd mindenképp adja hozzá a fogadó nevét a "fogadóként" szakaszban jelennek meg. A teljesítményszámlálók, amelyek külön-külön vannak konfigurálva az Application Insights-erőforrásra automatikusan elküldése.


## <a name="send-logs-to-application-insights"></a>Naplók küldése az Application Insights

### <a name="configuring-application-insights-with-wad"></a>Az Application Insights konfigurálása WAD használatával

>[!NOTE]
>Ez jelenleg csak alkalmazható Windows-fürtök.

Kétféleképpen elsődleges WAD adatokat küldeni az Azure Application Insights, amely egy Application Insights fogadó WAD konfigurációját az Azure Portalon vagy az Azure Resource Manager-sablon hozzáadásával érhető el.

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Adja hozzá az Application Insights-kialakítási kulcsot, ha a fürt létrehozása az Azure Portalon

![Egy AIKey hozzáadása](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

A fürt létrehozásakor, ha diagnosztika be van kapcsolva "" mező nem kötelező megadnia egy Application Insights-kialakítási kulcsot jelennek meg. Az Application Insights-kulcs Ide illessze be azt, ha az Application Insights fogadó konfigurálása automatikusan történik meg, amellyel a fürt üzembe helyezése Resource Manager-sablon a.

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>Az Application Insights fogadó a Resource Manager-sablon hozzáadása

A "WadCfg" Resource Manager-sablon vegyen fel egy "Sink" által többek között a következő két módosításokat:

1. A fogadó konfigurációra deklaráló után közvetlenül a `DiagnosticMonitorConfiguration` befejeződött:

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. A fogadó közé tartozik a `DiagnosticMonitorConfiguration` adja hozzá a következő sort a a `DiagnosticMonitorConfiguration` , a `WadCfg` (közvetlenül előtt a `EtwProviders` deklarált):

    ```json
    "sinks": "applicationInsights"
    ```

Mindkét a megelőző kódrészletek, a "applicationInsights" nevet használt a fogadó ismertetik. Ez nem követelmény, és mindaddig, amíg a fogadó neve "fogadóként" szerepel, a név és bármilyen karakterlánc megadható.

Jelenleg a fürt naplóinak megjelenjen **nyomkövetések** Application Insights naplófájl-megjelenítőjében. Mivel a bejövő nyomok a platformról a legtöbb "Tájékoztatási szintű" szint, is érdemes lehet csak a "Figyelmeztetés" vagy "Error". a naplók elküldése a fogadó konfiguráció módosítása Ezt megteheti a fogadó "Csatorna" hozzáadásával ahogyan az is [Ez a cikk](../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

>[!NOTE]
>Ha a portálon vagy a Resource Manager-sablon használatával egy megfelelő Application Insights-kulcs, akkor manuálisan módosíthatja a kulcsot, és a fürt frissítése / telepíteni.

## <a name="next-steps"></a>További lépések

Miután konfigurálta az Azure diagnostics megfelelően, az adatok megjelennek a tárolási táblák az ETW, EventSource naplókat. Ha az Azure Monitor naplók, Kibana vagy bármely más elemzési és vizualizációs adatplatform, amely nem közvetlenül történik meg a Resource Manager-sablon használatát választja, győződjön meg arról, olvassa el a storage-táblák adatait a választott platformnak beállításához. Ezzel az Azure Monitor-naplókat, nagyon viszonylag egyszerű, és ezen [esemény- és elemzési](service-fabric-diagnostics-event-analysis-oms.md). Az Application Insights jelenleg egy kis egy különleges esetben, abban az értelemben, a diagnosztikai bővítmény konfigurációjának részeként konfigurálhatók, mivel így tekintse meg a [megfelelő cikk](service-fabric-diagnostics-event-analysis-appinsights.md) Ha úgy dönt, hogy használható a mesterséges Intelligencia.

>[!NOTE]
>Jelenleg nem lehet szűrni vagy karcsúsítása az a tábla küldött események. Ha egy folyamat események eltávolítja a tábla nem alkalmazza, a tábla továbbra is nő. Jelenleg egy futó karcsúsítási szolgáltatás például a [figyelő minta](https://github.com/Azure-Samples/service-fabric-watchdog-service), ajánlott, hogy Ön írása ilyennel, kivéve, ha van egy jó oka, hogy 30 vagy 90 nap határidőn túli naplók tárolására.

* [Ismerje meg, hogyan teljesítményszámlálók vagy a naplók gyűjtésére a diagnosztikai bővítmény használatával](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Esemény elemzése és vizualizációs az Application insights segítségével](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Esemény elemzése és képi megjelenítése a Azure Monitor naplóira](service-fabric-diagnostics-event-analysis-oms.md)
