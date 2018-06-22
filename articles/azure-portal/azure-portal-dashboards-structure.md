---
title: Azure-irányítópultok felépítését |} Microsoft Docs
description: Ez a cikk ismerteti az Azure irányítópult JSON szerkezete
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
ms.author: adamab
ms.openlocfilehash: 2eb9289957968db04b78087413fb9df8ed1b085b
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301672"
---
# <a name="the-structure-of-azure-dashboards"></a>Azure-irányítópultok szerkezete
Ez a dokumentum végigvezeti egy Azure-irányítópultot, az alábbi példa irányítópulttal szerkezete:

![a minta-irányítópult](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Mivel a megosztott [Azure az irányítópultok olyan erőforrások](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), ez az irányítópult ábrázolhatók JSON-ként.  A következő JSON az irányítópulton fent ábrázolt jelöli.

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

## <a name="common-resource-properties"></a>Erőforrás-tulajdonságok

A JSON vonatkozó szakaszaihoz most lebontva.  A legfelső szintű tulajdonságok __azonosító__, __neve__, __típus__, __hely__, és __címkék__ tulajdonság megosztott összes Azure-erőforrás típusa. Ez azt jelenti, hogy nagy ehhez az irányítópult tartalmú nem rendelkeznek.

### <a name="the-id-property"></a>Az id tulajdonság

Az Azure erőforrás-azonosító, hogy a [elnevezési konvenciói az Azure-erőforrások](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Amikor a portál létrehoz egy irányítópultot, általában úgy dönt, az azonosító egy guid formájában, de szabadon bármilyen érvényes nevet használja, a létrehozott programozott módon. 

### <a name="the-name-property"></a>A name tulajdonság
A név nem az erőforrás-azonosító, amely nem tartalmazza az előfizetés, a erőforrás típusa vagy a csoport erőforrásadatok szegmense. Alapvetően akkor az erőforrás-azonosító utolsó részének.

### <a name="the-type-property"></a>A type tulajdonság
Minden az irányítópultok olyan típusú __Microsoft.Portal/dashboards__.

### <a name="the-location-property"></a>A location tulajdonság
Más erőforrásokat, eltérően irányítópultok egy futásidejű összetevő nem rendelkezik.  Irányítópultok a hely Megadja, hogy az elsődleges földrajzi hely, amely az irányítópult JSON-megjelenítés tárolja. Az érték egyike lehet: a helykódok, amelyek segítségével lehet lekérni a [az előfizetések erőforrás API helyeihez](https://docs.microsoft.com/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>A címkék tulajdonság
Címke az Azure-erőforrások, amelyek lehetővé teszik az erőforrás által tetszőleges név-érték párok rendszerezésére gyakran elérhető szolgáltatás található. Az irányítópultokat, van egy különleges címke nevű __rejtett cím__. Ha az irányítópult fel ezt a tulajdonságot, majd szolgál, a portál az irányítópulton megjelenített neve. Az Azure erőforrás-azonosítók nem nevezhető át, de címkék is. Ezt a címkét akadályozható meg szeretné, hogy az irányítópult renamable megjelenítendő nevét.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>A Tulajdonságok objektum
A tulajdonságok az objektum tartalmaz két tulajdonságok __fókuszainak__ és __metaadatok__. A __fókuszainak__ tulajdonság (más néven a csempék információkat tartalmaz részből) az irányítópulton.  A __metaadatok__ tulajdonság lehetséges jövőbeli szolgáltatások van.

### <a name="the-lenses-property"></a>A fókuszok tulajdonság
A __fókuszainak__ tulajdonsága tartalmazza az irányítópulton. Vegye figyelembe, hogy a kívánt objektumot ebben a példában a "0" nevű egyetlen tulajdonságot tartalmaz. Fókuszainak jelenleg nincs megvalósítva az irányítópultokon csoportosítási elvét. Most a egyetlen tulajdonság a "0" nevű objektumon a fókuszt, ebben az esetben az irányítópultok mindegyik rendelkezik.

### <a name="the-lens-object"></a>A fókuszt objektum
Alsó a "0" objektum tartalmazza a két tulajdonság __rendelés__ és __részek__.  Irányítópultok, a jelenlegi verzióban __rendelés__ van mindig 0. A __részek__ tulajdonság, amely meghatározza a egyes részeire (más néven objektumot tartalmaz tartalmazó csempék éppen) az irányítópulton.

A __részek__ objektum mindkét része, ahol a tulajdonság nevét, az a szám tulajdonságot tartalmaz. Ez a szám nincs jelentős eltérés. 

### <a name="the-part-object"></a>A rész objektum
Minden egyes részben objektumnak egy __pozíció__, és __metaadatok__.

### <a name="the-position-object"></a>A pozíció objektum
A __pozíció__ tulajdonság a méret és elhelyezkedés információkat tartalmaz, az a része kifejezett __x__, __y__, __rowSpan__, és __colSpan__. Az értékek a következők rács egységek tekintetében. A rács egységek láthatók, ha az irányítópultot a Testreszabás módban van, ahogy az itt látható. Ha egy csempére szélessége a két rács egységek rendelkezik, egy rács egység magasságú, és egy helyre a felső bal oldali az irányítópult sarkában, majd a pozíció obejct néz ki:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![rács-egységek](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>A metaadat-objektum
Minden egyes részben olyan metadata tulajdonsággal rendelkezik, az objektum neve csak egy szükséges tulajdonság rendelkezik __típus__. Ez a karakterlánc közli a portálon milyen csempére. A példa irányítópult csempék ilyen típusú használja:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` – Megjelenítésére használ a metrikák figyelése
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart` – Megjelenítésére használ a text vagy képeket egyszerű formázás listák, hivatkozások, stb.
1. `Extension[azure]/HubsExtension/PartType/VideoPart` – Megjelenítésére használ a videók YouTube Channel9 és semmilyen más típusú, amely a videó html-címke videó.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart` – Használja a név és az Azure virtuális gép állapotának megjelenítése.

Minden részének saját konfigurációs rendelkezik. A konfigurációs tulajdonságok nevezzük __bemenetek__, __beállítások__, és __eszköz__. 

### <a name="the-inputs-object"></a>A bemeneti objektum
A bemeneti objektum általában egy erőforrás-példányhoz kötve van egy csempe adatokat tartalmaz.  A minta-irányítópult a virtuális gép részt egy egyetlen bemeneti, amely az Azure erőforrás-azonosító használja a kötési kifejezése tartalmazza.  Az összes Azure-erőforrások az erőforrás-azonosító formátuma nem egységes.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
A metrikák diagram része egy egyetlen bemeneti, amely kifejezi lehet kötést létrehozni, és a megjelenített metric(s) vonatkozó információkat az erőforrás tartozik. Ez a csempe, amely azt a hálózati és hálózati metrikák bemenete.

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
A beállítási objektum egy részének a konfigurálható elemeket tartalmazza.  A minta-irányítópult a Markdown rész beállításai a tartalom, valamint a konfigurálható cím és a felirat egyéni markdown tárolásához.

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

Hasonlóképpen a videó csempe rendelkezik, a saját beállításai, amelyek tartalmazzák a videó lejátszása mutató, az automatikus beállítás, és választható szoftvercímre vonatkozó információkon.

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

### <a name="the-asset-object"></a>Az eszköz objektum
Csempék (más néven eszközök) első kezelhető portál objektumok kötött Ez az eszköz objektum keresztül kifejezett kapcsolatban áll.  A példa irányítópult a virtuális gép csempe a eszköz leírása tartalmazza.  A __idInputName__ tulajdonság tájékoztatja a portálon, hogy az azonosítót bemeneti az adategységhez, ebben az esetben az erőforrás-azonosító egyedi azonosítót tartalmaz. Az Azure erőforrás-típusok a portálon meghatározott eszközök rendelkeznek.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`