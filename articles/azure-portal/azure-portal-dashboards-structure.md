---
title: Azure-irányítópultok struktúrája |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure irányítópultján JSON szerkezete
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: dougeby
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: cwatson
ms.openlocfilehash: 405e0d5184880a00c07de55bd968210fa28e45fc
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393077"
---
# <a name="the-structure-of-azure-dashboards"></a>Azure-irányítópultok struktúrája
Ez a dokumentum végigvezeti egy Azure-irányítópultot, az alábbi példa irányítópulttal szerkezete:

![a minta-irányítópult](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Mivel a megosztott [Azure-irányítópultok forrásanyag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), ezen az irányítópulton is megjeleníthető JSON-fájlként.  A következő JSON az irányítópulton fent ábrázolt jelöli.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": {
            "model": {
                "timeRange": {
                    "value": {
                        "relative": {
                            "duration": 24,
                            "timeUnit": 1
                        }
                    },
                    "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
                }
            }
        }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

## <a name="common-resource-properties"></a>Gyakori erőforrás-tulajdonságok

A JSON vonatkozó szakaszaihoz vezetnek hozzunk felosztania.  A legfelső szintű tulajdonságok __azonosító__, __neve__, __típusa__, __hely__, és __címkék__ a tulajdonságok akkor vannak közösen használja az összes Azure-erőforrástípusra jellemzők. Azt jelenti nem kell annyit, hogy az irányítópult tartalmát.

### <a name="the-id-property"></a>Az id tulajdonság

Az Azure-erőforrás azonosítója, szabályoz a [elnevezési konvenciók Azure-erőforrások](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Amikor a portál létrehoz egy irányítópultot, általában úgy dönt, az azonosító egy guid formájában, de szabadon bármely érvényes név létrehozásakor használja azokat programozott módon. 

### <a name="the-name-property"></a>A name tulajdonság
A név a szegmens az erőforrás-azonosító, amely tartalmazza az előfizetés, erőforrástípust vagy erőforrás-csoport adatai. Alapvetően az erőforrás-azonosító utolsó szegmense.

### <a name="the-type-property"></a>A type tulajdonság
Összes irányítópult típusúak __Microsoft.Portal/dashboards__.

### <a name="the-location-property"></a>A location tulajdonság
Ellentétben más erőforrások az irányítópultok egy futásidejű összetevő nem rendelkezik.  Az irányítópultok a helyet, amely tárolja az irányítópult JSON-reprezentációval elsődleges földrajzi helyét adja meg. Az értéknek kell lennie a hely kódok, amelyek használatával lehet beolvasni a [helyek API az előfizetések erőforráson](https://docs.microsoft.com/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>A címkék tulajdonság
A címkék olyan gyakran elérhető szolgáltatás Azure-erőforrások, amelyek lehetővé teszik az erőforrás tetszőleges név-érték párok szerint rendszerezheti. Az irányítópultok esetében van ilyen nevű speciális címkék __rejtett cím__. Ha az irányítópult kitölti ezt a tulajdonságot, majd használatos a portálon az irányítópulton megjelenített nevet. Az Azure erőforrás-azonosítók nem nevezhető át, de a címkék is. Ez a címke az irányítópulton renamable megjelenített neve, hogy lehetőséget nyújt.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>A Tulajdonságok objektumában
A Tulajdonságok objektumában tartalmaz két tulajdonság __átvilágított felülettel__ és __metaadatok__. A __átvilágított felülettel__ tulajdonság tartalmazza a csempék információ (más néven) részeit) az irányítópulton.  A __metaadatok__ tulajdonság van-e az esetleges későbbi funkciókkal.

### <a name="the-lenses-property"></a>Az átvilágított felülettel tulajdonság
A __átvilágított felülettel__ tulajdonság tartalmazza az irányítópulton. Vegye figyelembe, hogy az átvilágított felülettel objektum ebben a példában a "0" nevű egyetlen tulajdonságot tartalmaz. Átvilágított felülettel is egy csoportosítás fogalom, amely az irányítópultok még nem implementáltuk. Egyelőre Ez egyetlen tulajdonságát az "0" nevű a fókuszt objektum ismét összes irányítópultját rendelkezik.

### <a name="the-lens-object"></a>A fókusz objektum
Alsó a "0" objektum tartalmazza a két tulajdonság __rendelés__ és __részek__.  Az irányítópultok, a jelenlegi verzióban __rendelés__ van mindig 0. A __részek__ tulajdonság, amely meghatározza a egyes részeire (más néven objektumot tartalmaz. csempék) az irányítópulton.

A __részek__ objektum az egyes részek, ahol a tulajdonság neve-e a számos tulajdonságot tartalmaz. Ez a szám nincs jelentős eltérés. 

### <a name="the-part-object"></a>A rész objektum
Minden egyes része objektum rendelkezik egy __pozíció__, és __metaadatok__.

### <a name="the-position-object"></a>Az objektum
A __pozíció__ tulajdonság a méret és elhelyezkedés információkat tartalmaz, a kifejezett rész __x__, __y__, __rowSpan__, és __colSpan__. Az értékek a következők rács egységekben. A rács egységek láthatók, ha az irányítópult a Testreszabás módban van, ahogy az itt látható. Ha azt szeretné, hogy a csempét egy széles két rács egység van, egy egység egy rács magassága és a egy hely felső bal oldali sarokban, majd a pozíció obejct néz ki:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![rács-egységek](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>A metaadat-objektum
Egyes részek metaadat-tulajdonságot, akkor egy objektumnak csak egy szükséges tulajdonság nevű __típus__. Ez a karakterlánc arra utasítja a portálon milyen csempe megjelenítéséhez. A példa irányítópult csempéi az ilyen típusú használja:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` – Megjelenítésére használ a metrikák figyelése
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart` – Használt szöveg és alapszintű formázásával, listák és hivatkozások, a kép megjelenítéséhez stb.
1. `Extension[azure]/HubsExtension/PartType/VideoPart` – A YouTube-on, a channel9-on és bármilyen más típusú, amely egyaránt használható egy videó html-címke a videó videók megjelenítésére szolgál.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart` – Használja a neve és a egy Azure virtuális gép állapotának megjelenítése.

Különböző típusú részét a saját konfigurációs van. A lehetséges konfigurációs tulajdonságok nevezzük __bemenetek__, __beállítások__, és __eszköz__. 

### <a name="the-inputs-object"></a>A bemeneti objektum
A bemeneti objektum általában egy csempe van kötve egy erőforrás-példány információt tartalmazza.  A minta-irányítópult a virtuális gép része egy egyetlen bemenetet, amely az Azure erőforrás-azonosítója használatával a kötés express tartalmazza.  Az erőforrás-azonosító formátuma az összes Azure-erőforrások esetén egységes.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
A metrikák diagram része egy egyetlen bemenetet, amely kifejezi az erőforrás kötést létrehozni, valamint az éppen megjelenített metric(s) kapcsolatos információkat tartalmaz. Íme a bemeneti jelenik-e meg a bejövő és kimenő hálózati forgalom metrikák csempe.

```json
“inputs”:
[
    {
        "name": "queryInputs",
        "value": 
        {
            "timespan": 
            {
                "duration": "PT1H",
                "start": null,
                "end": null
           },
            "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
            "chartType": 0,
            "metrics": 
            [
                {
                    "name": "Network In",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                },
                {
                    "name": "Network Out",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                }
            ]
        }
    }
]

```

### <a name="the-settings-object"></a>A beállítási objektumot
A beállítási objektum egy részének a konfigurálható elemeket tartalmazza.  A minta irányítópultján a Markdown-rész beállításai a egyéni markdown-szöveg és a egy konfigurálható cím és alcím tartalom tárolásához.

```json
"settings": 
{
    "content": 
    {
        "settings": 
        {
            "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
            "title": "Test VM Dashboard",
            "subtitle": "Contoso"
        }
    }
}

```

Ehhez hasonlóan a videó csempét a saját beállítások, amelyek tartalmazzák a videó lejátszásához mutató, egy automatikus lejátszás a beállítás és a választható szoftvercímre vonatkozó információkon rendelkezik.

```json
"settings": 
{
   "content": 
    {
        "settings": 
        {
            "title": "",
            "subtitle": "",
            "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
            "autoplay": false
        }
    }
}

```

### <a name="the-asset-object"></a>Az eszközintelligencia-objektum
Csempék elsőrangú felügyelhető portál objektumot (úgynevezett eszközök) kötött a kifejezett keresztül az eszköz objektum kapcsolattal rendelkeznek.  A virtuális gép csempe példa irányítópulton, az eszköz leírása tartalmazza.  A __idInputName__ tulajdonság arra utasítja a portálon, hogy az azonosító adjon meg egyedi azonosítója az adategységhez, ebben az esetben az erőforrás-azonosítója. A legtöbb Azure-erőforrástípus a portálon meghatározott eszközök rendelkeznek.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`