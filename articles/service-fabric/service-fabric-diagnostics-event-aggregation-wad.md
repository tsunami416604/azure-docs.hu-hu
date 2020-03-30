---
title: Eseményösszesítés a Windows Azure diagnosztikával
description: Ismerje meg az események összesítését és gyűjtését a WAD használatával az Azure Service Fabric-fürtök figyeléséhez és diagnosztikájához.
author: srrengar
ms.topic: conceptual
ms.date: 04/03/2018
ms.author: srrengar
ms.openlocfilehash: b9a448ff41c66fa3a38c124f7acde062bacbe9ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282496"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Eseményösszesítés és -gyűjtés a Windows Azure Diagnosztika használatával
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Ha egy Azure Service Fabric-fürtöt futtat, célszerű a naplókat egy központi helyen lévő összes csomópontról gyűjteni. A naplók központi helyen történő elhelyezése segít a fürtben, illetve az adott fürtben futó alkalmazásokban és szolgáltatásokban felmerülő problémák elemzésében és elhárításában.

A naplók feltöltésének és gyűjtésének egyik módja a Windows Azure Diagnostics (WAD) bővítmény használata, amely feltölti a naplókat az Azure Storage-ba, és rendelkezik a naplók küldésére az Azure Application Insights vagy az Event Hubs számára. Egy külső folyamat is használhatja az eseményeket a tárolóból, és helyezze el őket egy elemzési platform termék, például [az Azure Monitor naplók](../log-analytics/log-analytics-service-fabric.md) vagy más log-elemzési megoldás.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek
Ebben a cikkben a következő eszközöket használjuk:

* [Azure Resource Manager](../azure-resource-manager/management/overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Resource Manager-sablon](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-platform-events"></a>Service Fabric platformesemények
A Service Fabric néhány [beépített naplózási csatornát](service-fabric-diagnostics-event-generation-infra.md)állít be, amelyek közül a következő csatornák előre konfigurálva vannak a kiterjesztéssel, hogy figyelési és diagnosztikai adatokat küldjenek egy tárolótáblába vagy máshonnan:
  * [Működési események:](service-fabric-diagnostics-event-generation-operational.md)a Service Fabric platform által végrehajtott magasabb szintű műveletek. Ilyenpéldául az alkalmazások és szolgáltatások létrehozása, a csomópont állapotának módosításaés a frissítési információk. Ezek a Windows (ETW) eseménykövetésenaplóként kerülnek kibocsátva
  * [Megbízható szereplők programozási modell események](service-fabric-reliable-actors-diagnostics.md)
  * [Megbízható szolgáltatások programozási modell eseményei](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>A Diagnosztika bővítmény üzembe helyezése a portálon keresztül
A naplók gyűjtésének első lépése a Diagnosztikai bővítmény üzembe helyezése a virtuális gép méretezési csoport csomópontjaiban a Service Fabric-fürtben. A Diagnosztikai bővítmény minden virtuális gép naplóit gyűjti, és feltölti őket a megadott tárfiókba. A következő lépések ismertetik, hogyan valósíthatom meg ezt az azure portalon és az Azure Resource Manager-sablonokon keresztül az új és a meglévő fürtök esetében.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>A Diagnosztika bővítmény üzembe helyezése a fürt létrehozásának részeként az Azure Portalon keresztül
A fürt létrehozásakor a fürt konfigurációs lépésében bontsa ki a választható beállításokat, és győződjön meg arról, hogy **a** Diagnosztika be van kapcsolva (ez az alapértelmezett beállítás).

![Az Azure Diagnosztika beállításai a portálon a fürt létrehozásához](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

Javasoljuk, hogy töltse le a sablont, mielőtt az utolsó lépésben a **Létrehozás gombra** kattintana. További információt a [Service Fabric-fürt beállítása Azure Resource Manager-sablon használatával](service-fabric-cluster-creation-via-arm.md). A sablonra azért van szükség, hogy milyen csatornákon (fent felsorolt) módosítsunk az adatok gyűjtéséhez.

![Fürtsablon](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Most, hogy összesíti az eseményeket az Azure Storage-ban, állítsa be az [Azure Monitor-naplókat,](service-fabric-diagnostics-oms-setup.md) hogy betekintést nyerjen és lekérdezhesse őket az Azure Monitor naplók portálján

>[!NOTE]
>Jelenleg nincs mód a táblákba küldött események szűrésére vagy ápolására. Ha nem valósít meg egy folyamatot az események eltávolításához a táblából, a tábla tovább növekszik (az alapértelmezett korlát 50 GB). Utasításokat, hogyan kell változtatni ezt [tovább alább ebben a cikkben](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Emellett van egy példa egy adatápolási szolgáltatás fut a [Watchdog minta,](https://github.com/Azure-Samples/service-fabric-watchdog-service)és azt javasoljuk, hogy írjon egyet magadnak is, kivéve, ha van egy jó oka, hogy tárolja naplók túl 30 vagy 90 napos időkereten túl.



## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>A Diagnosztika bővítmény üzembe helyezése az Azure Resource Manageren keresztül

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Fürt létrehozása a diagnosztikai kiterjesztéssel
Fürt erőforrás-kezelővel történő létrehozásához hozzá kell adnia a Diagnosztikai konfigurációJSON-t a teljes Erőforrás-kezelő sablonhoz. Az Erőforrás-kezelő sablonmintáinak részeként diagnosztikai konfigurációval rendelkező mintaöt vm-es erőforrás-erőforrás-kezelősablont biztosítunk. Ezen a helyen az Azure-minták gyűjtemény: [Ötcsomópontos fürt diagnosztikai erőforrás-kezelő sablon minta.](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/)

Az Erőforrás-kezelő sablon diagnosztika-beállításának megtekintéséhez nyissa meg az azuredeploy.json fájlt, és keresse meg az **IaaSDiagnostics fájlt.** Fürt létrehozásához ezzel a sablonnal, válassza ki az Üzembe helyezés az **Azure-ba** gombot elérhető az előző linken.

Másik lehetőségként letöltheti az Erőforrás-kezelő minta, módosíthatja azt, és hozzon létre egy fürtet a módosított sablon segítségével a `New-AzResourceGroupDeployment` parancsot egy Azure PowerShell-ablakban. A parancsnak átadott paramétereket lásd a következő kódban. Az erőforráscsoport PowerShell használatával történő központi telepítéséről az [Erőforráscsoport telepítése az Azure Resource Manager sablonnal](../azure-resource-manager/templates/deploy-powershell.md)című témakörben olvashat részletesen.

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>A diagnosztikai bővítmény hozzáadása meglévő fürthöz
Ha olyan meglévő fürttel rendelkezik, amely nem rendelkezik telepítve a Diagnosztika szolgáltatással, hozzáadhatja vagy frissítheti azt a fürtsablonon keresztül. Módosítsa a meglévő fürt létrehozásához használt Erőforrás-kezelő sablont, vagy töltse le a sablont a portálról a korábban leírtak szerint. Módosítsa a template.json fájlt a következő feladatok végrehajtásával:

Új tárolási erőforrás hozzáadása a sablonhoz az erőforrások szakaszhoz való hozzáadással.

```json
{
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
    "name": "[parameters('applicationDiagnosticsStorageAccountType')]"
    "tier": "standard"
  },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Ezután adja hozzá a paraméterek szakaszt közvetlenül `supportLogStorageAccountName`a tárfiók-definíciók után, a között. Cserélje le a helyőrző szöveges *tárfiók nevét itt* a kívánt tárfiók nevére.

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
Ezután frissítse `VirtualMachineProfile` a template.json fájl szakaszát a következő kód hozzáadásával a bővítmények tömbjében. Ügyeljen arra, hogy egy vesszőt adjon hozzá az elején vagy a végén, attól függően, hogy hol van behelyezve.

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
                },
                {
                    "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
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

A template.json fájl leírása szerint történő módosítása után tegye közzé újra az Erőforrás-kezelő sablont. Ha a sablonexportálás történt, a deploy.ps1 fájl futtatása újra közzéteszi a sablont. Üzembe helyezés után győződjön meg arról, hogy **a Kiépítésállapot** **sikeres.**

> [!TIP]
> Ha tárolókat szeretne telepíteni a fürtbe, engedélyezze a WAD számára a docker-statisztikák felvételét a **WadCfg > DiagnosticMonitorConfiguration** szakaszhoz való hozzáadásával.
>
>```json
>"DockerSources": {
>    "Stats": {
>        "enabled": true,
>        "sampleRate": "PT1M"
>    }
>},
>```

### <a name="update-storage-quota"></a>Tárterületkvóta frissítése

Mivel a bővítmény által feltöltött táblák a kvóta leütéséig nőnek, érdemes lehet a kvóta méretének csökkentése. Az alapértelmezett érték 50 GB, és a `overallQuotaInMB` sablonban konfigurálható a`DiagnosticMonitorConfiguration`

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Naplógyűjtési konfigurációk
További csatornákból származó naplók is rendelkezésre állnak a gyűjtemény, íme néhány a leggyakoribb konfigurációk hozhat létre a sablonban az Azure-ban futó fürtök.

* Működési csatorna – Alap: Alapértelmezés szerint engedélyezve, a Service Fabric és a fürt által végrehajtott magas szintű műveletek, beleértve a csomópont közelgő eseményeit, egy új alkalmazás üzembe helyezését vagy egy frissítés-visszaállítást stb. Az események listáját az [Operational Channel Events (Műveleti csatorna eseményei) (Operational Channel Events) (Műveleti csatorna eseményei) (Operational Channel Events) (Műveleti csatorna eseményei) (Operational Channel Events) (](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-operational)
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Működési csatorna – részletes: Ez magában foglalja az állapotjelentéseket és a terheléselosztási döntéseket, valamint az alap működési csatornában lévő mindent. Ezeket az eseményeket a rendszer vagy a kód hozza létre az állapot- vagy terhelésjelentési API-k , például a [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) vagy a [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx)használatával. Ha meg szeretné tekinteni ezeket az eseményeket a Visual Studio diagnosztikai eseménynaplójában, adja hozzá a "Microsoft-ServiceFabric:4:0x40000000000000000000008" értéket az ETW-szolgáltatók listájához.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Adat- és üzenetcsatorna – Alap: Az üzenetküldésben (jelenleg csak a ReverseProxy- és adatelérési úton) létrehozott kritikus naplók és események, valamint a részletes operatív csatornanaplók. Ezek az események a kérelem feldolgozási hibák és egyéb kritikus problémák a ReverseProxy, valamint a feldolgozott kérelmek. **Ez a mi ajánlásunk az átfogó naplózásra.** Ha meg szeretné tekinteni ezeket az eseményeket a Visual Studio diagnosztikai eseménynaplójában, adja hozzá a "Microsoft-ServiceFabric:4:0x4000000000000000000000010" értéket az ETW-szolgáltatók listájához.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Adat& üzenetküldési csatorna - Részletes: Részletes csatorna, amely tartalmazza az összes nem kritikus naplók az adatok és az üzenetküldés a fürtben és a részletes működési csatorna. Az összes fordított proxyesemény részletes hibaelhárítását a [fordított proxydiagnosztikai útmutató](service-fabric-reverse-proxy-diagnostics.md)ismerteti.  Ha meg szeretné tekinteni ezeket az eseményeket a Visual Studio diagnosztikai eseménymegjelenítőjében, adja hozzá a "Microsoft-ServiceFabric:4:0x40000000000000000020" értéket az ETW-szolgáltatók listájához.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Ez a csatorna nagyon nagy mennyiségű események, amely lehetővé teszi az eseménygyűjtés ebből a részletes csatornáról azt eredményezi, hogy sok nyomkövetések gyorsan jön létre, és a tárolókapacitás térhet el. Csak akkor kapcsolja be, ha feltétlenül szükséges.


Ahhoz, hogy a **base operational channel** a mi ajánlásátfogó naplózás a legkevesebb zajt, `EtwManifestProviderConfiguration` A a `WadCfg` sablon a következőkre néz ki:

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
              },
              {
                "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
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

## <a name="collect-from-new-eventsource-channels"></a>Adatgyűjtés az új EventSource csatornákról

A Diagnosztika frissítéséhez gyűjtse a naplókat az új EventSource-csatornákról, amelyek egy új alkalmazást képviselnek, amelyet telepíteni készül, hajtsa végre ugyanazokat a lépéseket, mint korábban egy meglévő fürt diagnosztika beállítása.

Frissítse `EtwEventSourceProviderConfiguration` a template.json fájl szakaszát az új EventSource-csatornák bejegyzéseinek hozzáadásához, mielőtt a Konfigurációs frissítést a `New-AzResourceGroupDeployment` PowerShell paranccsal alkalmazná. Az eseményforrás neve a Visual Studio által létrehozott ServiceEventSource.cs fájlban lévő kód részeként van definiálva.

Ha például az eseményforrás neve My-Eventsource, adja hozzá a következő kódot, hogy a My-Eventsource-ból származó eseményeket egy MyDestinationTableName nevű táblába helyezze.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Teljesítményszámlálók vagy eseménynaplók gyűjtéséhez módosítsa az Erőforrás-kezelő sablont a [Windows létrehozása virtuális gép létrehozása figyeléssel és diagnosztikával](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)az Azure Resource Manager sablon használatával című részben található példák használatával. Ezután tegye közzé újra az Erőforrás-kezelő sablont.

## <a name="collect-performance-counters"></a>Teljesítményszámlálók gyűjtése

Teljesítménymutatók gyűjtéséhez a fürtből, adja hozzá a teljesítményszámlálókat a "WadCfg > DiagnosticMonitorConfiguration" a fürt Erőforrás-kezelő sablonban. [Lásd: Teljesítményfigyelés a WAD-val](service-fabric-diagnostics-perf-wad.md) a módosítási `WadCfg` lépéseket, hogy összegyűjtse az adott teljesítményszámlálók. Referencia [szolgáltatás fabric teljesítményszámlálók](service-fabric-diagnostics-event-generation-perf.md) teljesítményszámlálók, amelyek azt javasoljuk, hogy összegyűjtse.
  
Ha egy Application Insights-fogadót használ, az alábbi szakaszban leírtak szerint, és szeretné, hogy ezek a metrikák megjelenjenek az Application Insightsban, majd győződjön meg róla, hogy adja hozzá a fogadó nevét a "sinks" szakaszban a fent látható módon. Ez automatikusan elküldi a teljesítményszámlálók, amelyek egyénileg konfigurálva az Application Insights erőforrás.


## <a name="send-logs-to-application-insights"></a>Naplók küldése az Application Insightsnak

### <a name="configuring-application-insights-with-wad"></a>Az Application Insights konfigurálása a WAD-val

>[!NOTE]
>Ez jelenleg csak windowsos fürtökre vonatkozik.

A WAD-ból az Azure Application Insightsba történő adatok küldésének két elsődleges módja van, amely et úgy érhet el, hogy egy Application Insights-fogadót ad hozzá a WAD-konfigurációhoz az Azure Portalon vagy egy Azure Resource Manager-sablonon keresztül.

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Application Insights instrumentation kulcs hozzáadása fürt létrehozásakor az Azure Portalon

![AIKey hozzáadása](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Fürt létrehozásakor, ha a diagnosztika be van kapcsolva "Be", egy nem kötelező mező tanod egy Application Insights Instrumentation kulcs jelenik meg. Ha az Application Insights-kulcsot ide illeszti be, az Application Insights-fogadó automatikusan konfigurálva lesz a fürt üzembe helyezéséhez használt Resource Manager-sablonban.

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>Az Application Insights-fogadó hozzáadása az Erőforrás-kezelő sablonhoz

Az Erőforrás-kezelő sablon "WadCfg" elemében adjon hozzá egy "Fogadó" elemet a következő két módosítással:

1. Adja hozzá a fogadó konfigurációját `DiagnosticMonitorConfiguration` közvetlenül a deklarálása után:

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

2. A mosogató felvétele `DiagnosticMonitorConfiguration` a következő sor `DiagnosticMonitorConfiguration` hozzáadásával `WadCfg` a (közvetlenül a `EtwProviders` deklarált) között:

    ```json
    "sinks": "applicationInsights"
    ```

Az előző kódrészletekben az "applicationInsights" nevet használták a fogadó leírására. Ez nem követelmény, és mindaddig, amíg a fogadó neve szerepel a "sinks", beállíthatja a nevet bármilyen karakterlánc.

Jelenleg a fürtből származó naplók **nyomkövetésként** jelennek meg az Application Insights naplómegjelenítőjében. Mivel a platformról érkező nyomkövetések többsége "Információs" szintű, érdemes lehet módosítani a fogadó konfigurációját úgy is, hogy csak "Figyelmeztetés" vagy "Hiba" típusú naplókat küldjön. Ezt úgy lehet megtenni, hogy "Csatornákat" ad hozzá a mosogatóhoz, amint [azt ez](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)a cikk is mutatja.

>[!NOTE]
>Ha helytelen Application Insights-kulcsot használ a portálon vagy az Erőforrás-kezelő sablonban, manuálisan kell módosítania a kulcsot, és frissítenie kell a fürtöt / újratelepíteni.

## <a name="next-steps"></a>További lépések

Miután helyesen konfigurálta az Azure-diagnosztika, látni fogja az adatokat a Storage-táblák az ETW és EventSource naplók. Ha úgy dönt, hogy az Azure Monitor naplók, Kibana, vagy bármely más adatelemzési és vizualizációs platform, amely nincs közvetlenül konfigurálva az Erőforrás-kezelő sablonban, győződjön meg róla, hogy állítsa be a platformot, amelyet az ön által választott olvasni az adatokat ezekből a tárolótáblákból. Ezt az Azure Monitor naplók viszonylag triviális, és ismerteti az [Esemény és a napló elemzés.](service-fabric-diagnostics-event-analysis-oms.md) Az Application Insights ebben az értelemben egy kicsit különleges eset, mivel a Diagnosztikai bővítmény konfigurációjának részeként konfigurálható, ezért olvassa el a [megfelelő cikket,](service-fabric-diagnostics-event-analysis-appinsights.md) ha úgy dönt, hogy a aI.

>[!NOTE]
>Jelenleg nincs mód a táblába küldött események szűrésére vagy ápolására. Ha nem valósít meg egy folyamatot az események eltávolításához a táblából, a tábla tovább növekszik. Jelenleg van egy példa egy adatápolási szolgáltatás fut a [Watchdog minta,](https://github.com/Azure-Samples/service-fabric-watchdog-service)és azt javasoljuk, hogy írjon egyet magadnak is, kivéve, ha van egy jó oka, hogy tárolja naplók túl 30 vagy 90 napos időkereten belül.

* [Ismerje meg, hogyan gyűjthet teljesítményszámlálókat vagy naplókat a Diagnosztika bővítmény használatával](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Eseményelemzés és vizualizáció az Application Insights segítségével](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Eseményelemzés és vizualizáció az Azure Monitor-naplókkal](service-fabric-diagnostics-event-analysis-oms.md)
* [Eseményelemzés és vizualizáció az Application Insights segítségével](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Eseményelemzés és vizualizáció az Azure Monitor-naplókkal](service-fabric-diagnostics-event-analysis-oms.md)
