---
title: Az Azure-irányítópultok szerkezete | Microsoft dokumentumok
description: Az Azure-irányítópult JSON-struktúrájának áttekintése egy példa irányítópult használatával. Az erőforrás-tulajdonságokra való hivatkozást tartalmazza.
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: mtillman
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 12/20/2019
ms.author: mblythe
ms.openlocfilehash: 18125e119e7ffdd2f8fa8ca3c5c1b12c8c9a94e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75640363"
---
# <a name="the-structure-of-azure-dashboards"></a>Az Azure-irányítópultok struktúrája
Ez a dokumentum végigvezeti az Azure-irányítópult szerkezetén, példaként a következő irányítópultot használva:

![minta-irányítópult](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Mivel a megosztott [Azure-irányítópultok erőforrások,](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)ez az irányítópult JSON-ként is képviselhető.  A következő JSON a fent látható irányítópultot jelöli.

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

## <a name="common-resource-properties"></a>Közös erőforrás-tulajdonságok

Bontsuk le a JSON vonatkozó szakaszait.  A legfelső szintű tulajdonságok, __id,__ __név__, __típus__, __hely__és __címkék__ tulajdonságai meg vannak osztva az összes Azure-erőforrás-típus között. Ez azt, hogy nem sok köze van az irányítópult tartalmához.

### <a name="the-id-property"></a>Az Azonosító tulajdonság

Az Azure-erőforrás-azonosító, az [Azure-erőforrások elnevezési konvencióinak függvényében.](/azure/architecture/best-practices/resource-naming) Amikor a portál létrehoz egy irányítópultot, általában egy azonosítót választ guid formájában, de programozott módon szabadon használhatja az érvényes nevet. 

### <a name="the-name-property"></a>A name tulajdonság
A név az erőforrás-azonosító azon szegmense, amely nem tartalmazza az előfizetést, az erőforrástípust vagy az erőforráscsoport adatait. Lényegében ez az erőforrás-azonosító utolsó szegmense.

### <a name="the-type-property"></a>A típus tulajdonság
Minden irányítópult __Microsoft.Portal/dashboards__típusú.

### <a name="the-location-property"></a>A hely tulajdonsága
Más erőforrásokkal ellentétben az irányítópultok nem rendelkeznek futásidejű összetevővel.  Az irányítópultok esetében a hely azt az elsődleges földrajzi helyet jelzi, amely az irányítópult JSON-ábrázolását tárolja. Az értéknek az [előfizetési erőforrás helyAPI-jával](https://docs.microsoft.com/rest/api/resources/subscriptions)lehívható helykódok egyikének kell lennie.

### <a name="the-tags-property"></a>A címkék tulajdonság
A címkék az Azure-erőforrások gyakori jellemzője, amelyek lehetővé teszik az erőforrás tetszőleges névértékpárok szerinti rendszerezését. Az irányítópultok esetében van egy rejtett __cím__nevű speciális címke. Ha az irányítópulton ez a tulajdonság ki van töltve, akkor a portálon az irányítópult megjelenítendő neveként lesz használatos. Az Azure-erőforrás-azonosítók nem nevezhetők át, de a címkék nem. Ez a címke lehetővé teszi, hogy az irányítópulton renamable megjelenítendő név legyen.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>A tulajdonságobjektum
A tulajdonságobjektum két tulajdonságot, __lencséket__ és __metaadatokat__tartalmaz. A __lencsék__ tulajdonság az irányítópultcsempéivel kapcsolatos információkat tartalmaz.  A __metaadat-tulajdonság__ a lehetséges jövőbeli funkciókhoz érhető el.

### <a name="the-lenses-property"></a>A lencsék tulajdonság
A __lencsék__ tulajdonság tartalmazza az irányítópultot. Ne feledje, hogy a példában szereplő objektívek objektum a "0" nevű tulajdonságot tartalmazza. A lencsék olyan csoportosítási koncepció, amely jelenleg nincs megvalósítva az irányítópultokon. Egyelőre az összes irányítópulton van ez az egyetlen tulajdonság az objektív objektumon, ismét "0".

### <a name="the-lens-object"></a>Az objektív objektum
A "0" alatti objektum két tulajdonságot tartalmaz, __a sorrendet__ és __az alkatrészeket.__  Az irányítópultok aktuális verziójában a __sorrend__ mindig 0. Az __alkatrésztulajdonság__ egy objektumot tartalmaz, amely meghatározza az irányítópult egyes részeit (más néven csempéket).

Az __alkatrészobjektum__ minden alkatrészhez tartalmaz egy tulajdonságot, ahol a tulajdonság neve egy szám. Ez a szám nem jelentős. 

### <a name="the-part-object"></a>Az alkatrész objektum
Minden egyes részobjektumnak van __egy pozíciója__és __metaadatai__.

### <a name="the-position-object"></a>A pozíció objektum
A __beosztás__ tulajdonság az __x__, __y__, __rowSpan__és __colSpan__megfogalmazott rész méretét és helyét tartalmazza. Az értékek rácsegységekben vannak kifejezve. Ezek a rácsegységek akkor láthatók, ha az irányítópult testreszabási módban van, ahogy az itt látható. Ha azt szeretné, hogy egy mozaik szélessége két rácsegységből, egy rácsegység magasságából és egy helyből legyen az irányítópult bal felső sarkában, akkor a pozícióobjektum így néz ki:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![rács-egységek](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>A metaadat-objektum
Minden rész rendelkezik egy metaadat-tulajdonsággal, egy objektumnak csak egy kötelező tulajdonsága van, __amelyet típusnak__neveznek. Ez a karakterlánc megmondja a portálnak, hogy melyik csempét jelenítse meg. A példa irányítópultaz alábbi típusú csempéket használ:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart`– A figyelési mutatók megjelenítésére szolgál
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart`- Használt jelenik meg a szöveg vagy kép alapvető formázáslisták, linkek, stb
1. `Extension[azure]/HubsExtension/PartType/VideoPart`- Használt mutatni videókat a YouTube, Channel9, és bármilyen más típusú videó, hogy működik egy HTML video tag.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart`– Egy Azure virtuális gép nevének és állapotának megjelenítésére szolgál.

Minden alkatrésztípusnak saját konfigurációja van. A lehetséges konfigurációs tulajdonságokat bemenetnek , __beállításoknak__és __eszköznek__ __nevezzük__. 

### <a name="the-inputs-object"></a>A bemeneti objektum
A bemeneti objektum általában olyan információkat tartalmaz, amelyek egy csempét egy erőforráspéldányhoz kötnek.  A virtuális gép része a minta irányítópulton tartalmaz egy bemeneti, amely az Azure-erőforrás-azonosító t a kötés kifejezésére.  Ez az erőforrás-azonosító formátum konzisztens az összes Azure-erőforrás között.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
A metrikadiagram-rész egyetlen bemenettel rendelkezik, amely kifejezi a kötésre kötelezett erőforrást, valamint a megjelenített metrika(k) adatait. Itt van a bemeneti a csempe, amely bemutatja a hálózati be- és hálózati kimenő metrikák.

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

### <a name="the-settings-object"></a>A beállítások objektum
A beállításobjektum egy alkatrész konfigurálható elemeit tartalmazza.  A minta irányítópulton a Markdown rész beállításokat használ az egyéni markdown-tartalom, valamint egy konfigurálható cím és felirat tárolására.

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

Hasonlóképpen, a videocsempe saját beállításokkal rendelkezik, amelyek a lejátszandó videóra mutató mutatót, az automatikus lejátszási beállítást és a választható címadatokat tartalmazzák.

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

### <a name="the-asset-object"></a>Az eszközobjektum
Az első osztályú kezelhető portálobjektumokhoz (más néven eszközökhöz) kötött csempék ezt a kapcsolatot az eszközobjektumon keresztül fejezik ki.  A példa irányítópulton a virtuális gép csempe tartalmazza ezt az eszköz leírását.  Az __idInputName__ tulajdonság közli a portállal, hogy az azonosító bemenettartalmazza az eszköz egyedi azonosítóját, ebben az esetben az erőforrás-azonosítót. A legtöbb Azure-erőforrás-típus rendelkezik a portálon definiált eszközökkel.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`
