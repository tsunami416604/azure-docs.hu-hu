---
title: Azure-irányítópultok programozott létrehozása | Microsoft Docs
description: Az Azure-irányítópultok programozott létrehozásához használhatja a Azure Portal irányítópultját sablonként. JSON-hivatkozást tartalmaz.
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
ms.date: 09/01/2017
ms.author: mblythe
ms.openlocfilehash: 498e0255cfa289f7d8ccb93040980c362cf510a0
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640346"
---
# <a name="programmatically-create-azure-dashboards"></a>Azure-irányítópultok programozott létrehozása

Ez a dokumentum végigvezeti az Azure-irányítópultok programozott létrehozásának és közzétételének folyamatán. Az alábbi irányítópult az egész dokumentumra hivatkozik.

![minta-irányítópult](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Áttekintés

Az Azure-ban megosztott irányítópultok olyan [erőforrások](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) , mint a Virtual Machines és a Storage-fiókok.  Ezért a [Azure Resource Manager REST API](/rest/api/)-kkal, az [Azure CLI](https://docs.microsoft.com/cli/azure)-vel, [Azure PowerShell parancsokkal](https://docs.microsoft.com/powershell/azure/get-started-azureps)és számos [Azure Portal](https://portal.azure.com) funkcióval is kezelhetők, amelyek ezen API-kra épülnek, így egyszerűbbé teszik az erőforrások kezelését.  

Ezen API-k és eszközök mindegyike lehetőséget kínál az erőforrások létrehozására, listázására, lekérésére, módosítására és törlésére.  Mivel az irányítópultok erőforrások, kiválaszthatja kedvenc API-ját/eszközét.

Függetlenül attól, hogy melyik eszközt használja, létre kell hoznia egy JSON-ábrázolást az irányítópult-objektumhoz, mielőtt bármilyen erőforrás-létrehozási API-t meghívhat. Ez az objektum a részekre vonatkozó információkat tartalmaz (más néven csempék) az irányítópulton. Ez magában foglalja a méreteket, a pozíciókat, a hozzájuk kötött erőforrásokat, valamint a felhasználók testreszabását.

A JSON-dokumentum felépítésének leghatékonyabb módja a [portál](https://portal.azure.com/) használata a csempék interaktív hozzáadásához és elhelyezéséhez. Ezután exportálja a JSON-t. Végül létre kell hoznia egy sablont az eredményből a parancsfájlok, programok és központi telepítési eszközök későbbi használatához.

## <a name="create-a-dashboard"></a>Irányítópult létrehozása

Új irányítópult létrehozásához használja az új irányítópult parancsot a portál fő képernyőjén.

![új irányítópult-parancs](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Ezután a csempe-katalógus segítségével megkeresheti és hozzáadhatja a csempéket. A csempék a húzással lesznek hozzáadva. Néhány csempe támogatja az átméretezést egy húzással, míg mások támogatják a helyi menüben megjelenő javítások méretét.

### <a name="drag-handle"></a>Fogantyú húzása
![fogantyú húzása](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>Rögzített méretek a helyi menün keresztül
![méretek helyi menü](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Az irányítópult megosztása

Miután a következő lépésekkel konfigurálta az irányítópultot, tegye közzé az irányítópultot (a megosztás parancs használatával), majd az erőforrás-kezelővel hívja le a JSON-t.

![megosztási parancs](./media/azure-portal-dashboards-create-programmatically/share-command.png)

A megosztás parancsra kattintva megjelenik egy párbeszédpanel, amely arra kéri, hogy válassza ki a közzétenni kívánt előfizetést és erőforráscsoportot. Ne feledje, hogy [írási hozzáféréssel kell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) rendelkeznie a kiválasztott előfizetéshez és erőforráscsoporthoz.

![megosztás és hozzáférés](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Az irányítópult JSON-ábrázolásának beolvasása

A közzététel csak néhány másodpercet vesz igénybe.  Ha elkészült, a következő lépés az [erőforrás-kezelő](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade) megnyitása a JSON beolvasásához.

![erőforrás-kezelő tallózása](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

Az erőforrás-kezelőben navigáljon a kiválasztott előfizetéshez és erőforráscsoporthoz. Ezután kattintson az újonnan közzétett irányítópult-erőforrásra a JSON megjelenítéséhez.

![erőforrás-kezelő JSON](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>Sablon létrehozása a JSON-ből

A következő lépés egy sablon létrehozása ebből a JSON-ből, hogy programozott módon újra felhasználható legyen a megfelelő erőforrás-kezelési API-kkal, parancssori eszközökkel vagy a portálon.

Sablon létrehozásához nem szükséges teljes mértékben megérteni az irányítópult JSON-struktúráját. A legtöbb esetben meg kell őriznie az egyes csempék szerkezetét és konfigurációját, majd parametrizálja kell a rájuk mutató Azure-erőforrások készletét. Tekintse meg az exportált JSON-irányítópultot, és keresse meg az Azure-erőforrás-azonosítók összes előfordulását. A példában szereplő irányítópult több csempét tartalmaz, amelyek mindegyike egyetlen Azure-beli virtuális gépen található. Ennek az az oka, hogy az irányítópult csak ezt az egyetlen erőforrást vizsgálja. Ha a JSON-t (a dokumentum végén található) keresi a "/Subscriptions" kifejezésre, akkor az azonosító több előfordulását is megtalálhatja.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Ha az irányítópultot a jövőben bármely virtuális gépre közzé szeretné tenni, a karakterlánc minden előfordulását parametrizálja kell a JSON-n belül. 

Az API-k két olyan változata létezik, amely erőforrásokat hoz létre az Azure-ban. Olyan [kényszerítő API](https://docs.microsoft.com/rest/api/resources/resources) -k, amelyek egyszerre létrehoznak egy erőforrást, valamint egy [sablon alapú központi telepítési](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) rendszert, amely egyetlen API-hívással képes több, függő erőforrások létrehozására. Az utóbbi natív módon támogatja a paraméterezés és a sablon használatát, így a példánkban használjuk.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Irányítópult létrehozása sablon alapján a sablon központi telepítése alapján

Az Azure számos erőforrás üzembe helyezését teszi lehetővé. Létre kell hoznia egy központi telepítési sablont, amely a telepítendő erőforrások készletét és a köztük lévő kapcsolatokat is meghatározza.  Az egyes erőforrások JSON-formátuma ugyanaz, mintha egyenként létrehozta őket. A különbség az, hogy a [sablon nyelve](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) néhány olyan fogalmat is hozzáad, mint például a változók, paraméterek, alapszintű függvények és egyebek. Ez a kiterjesztett szintaxis csak a sablon központi telepítésének kontextusában támogatott, és nem működik, ha a korábban tárgyalt, kötelező API-kkal van használatban.

Ha ezt az útvonalat használja, akkor a paraméterezés a sablon paraméterének szintaxisával kell elvégezni.  Az erőforrás-azonosító összes példányát lecseréli az itt látható módon.

### <a name="example-json-property-with-hard-coded-resource-id"></a>Példa JSON-tulajdonságra a nehezen kódolt erőforrás-azonosítóval
`id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>Példa a JSON-tulajdonság paraméteres verzióra konvertálása sablon paraméterei alapján

`id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"`

Emellett meg kell adnia néhány szükséges sablon-metaadatot és a JSON-sablon tetején található paramétereket is:

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```

__A dokumentum végén a teljes, munkasablon látható.__

A sablon kiépítése után a [REST API](https://docs.microsoft.com/rest/api/resources/deployments)-kkal, a [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)-lel, az [Azure CLI](https://docs.microsoft.com/cli/azure/group/deployment#az-group-deployment-create)-vel vagy a [portál sablonjának telepítési lapjával](https://portal.azure.com/#create/Microsoft.Template)telepítheti azt.

Íme a példában szereplő irányítópult JSON két verziója. Az első az a verzió, amelyet az erőforráshoz már kötött portálról exportáltunk. A második a sablon verziója, amely programozott módon köthető bármely virtuális géphez, és üzembe helyezhető Azure Resource Manager használatával.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>Példaként szolgáló irányítópult JSON-ábrázolása (sablon előtt)

Itt láthatja, hogy a korábbi utasításokat követve beolvassa a már telepített irányítópult JSON-ábrázolását. Jegyezze fel a rögzített erőforrás-azonosítókat, amelyek azt mutatják, hogy ez az irányítópult egy adott Azure-beli virtuális gépen mutat.

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
        "metadata": { }
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

### <a name="template-representation-of-our-example-dashboard"></a>A példában szereplő irányítópult sablon-ábrázolása

Az irányítópult sablonjának verziója három, __virtualMachineName__, __virtualMachineResourceGroup__és __dashboardName__nevű paramétert adott meg.  A paraméterek lehetővé teszik, hogy az irányítópultot egy másik Azure-beli virtuális gépen mutassa be minden egyes telepítésekor. A paraméteres azonosítók úgy vannak kiemelve, hogy az irányítópult programozott módon konfigurálható és üzembe helyezhető, hogy bármely Azure-beli virtuális gép mutasson. A szolgáltatás tesztelésének legegyszerűbb módja az alábbi sablon másolása és beillesztése a [Azure Portal sablonjának üzembe helyezési lapjába](https://portal.azure.com/#create/Microsoft.Template). 

Ez a példa egy irányítópultot helyez üzembe önmagával, de a sablon nyelve lehetővé teszi több erőforrás üzembe helyezését, valamint egy vagy több irányítópult egymás melletti elhelyezését. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}


```
