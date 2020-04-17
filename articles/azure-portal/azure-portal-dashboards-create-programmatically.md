---
title: Azure-irányítópultok szoftveres létrehozása
description: Az Azure Portalon egy irányítópultot használhat sablonként az Azure-irányítópultok programozott létrehozásához. JSON-hivatkozást tartalmaz.
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: mtillman
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 9ec9a4daad139a4930174ba9e3445e1cda1f8c54
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461310"
---
# <a name="programmatically-create-azure-dashboards"></a>Azure-irányítópultok szoftveres létrehozása

Ez a cikk végigvezeti az Azure-irányítópultok programozott létrehozásának és közzétételének folyamatán. Az alábbi irányítópultra az egész dokumentum hivatkozik.

![minta-irányítópult](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Áttekintés

Az [Azure Portalon](https://portal.azure.com) megosztott irányítópultok a virtuális gépekhez és a tárfiókokhoz hasonlóan [erőforrások.](../azure-resource-manager/management/overview.md) Az erőforrásokat programozott módon kezelheti az [Azure Resource Manager REST API-k](/rest/api/), az Azure [CLI](/cli/azure)és az [Azure PowerShell-parancsok](/powershell/azure/get-started-azureps)használatával.

Az erőforrás-kezelés megkönnyítése érdekében számos szolgáltatás ezekre az API-kra épül. Ezen API-k és eszközök mindegyike lehetőséget kínál az erőforrások létrehozására, felsorolására, beolvasására, módosítására és törlésére. Mivel az irányítópultok erőforrások, kiválaszthatja kedvenc API-ját vagy eszközét.

Bármelyik eszközt is használja, hogy hozzon létre egy irányítópultot programozott módon, létrehoz egy JSON-ábrázolást az irányítópult-objektumról. Ez az objektum az irányítópultcsempéivel kapcsolatos információkat tartalmazza. Ez magában foglalja a méreteket, beosztásokat, erőforrásokat, amelyekhez kötődnek, valamint a felhasználók testreszabását.

A JSON-dokumentum létrehozásának legpraktikusabb módja az Azure Portal használata. A csempéket interaktív módon is hozzáadhatja és elhelyezheti. Ezután exportálja a JSON-t, és hozzon létre egy sablont az eredményből parancsfájlokban, programokban és központi telepítési eszközökben való későbbi használatra.

## <a name="create-a-dashboard"></a>Irányítópult létrehozása

Irányítópult létrehozásához válassza **Dashboard** az [Irányítópult](https://portal.azure.com) lehetőséget az Azure Portal menüjében, majd az Új **irányítópult parancsra.**

![új irányítópult parancs](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

A csempegyűjtemény segítségével csempéket kereshet és adhat hozzá. A csempék húzással kerülnek hozzáadásra. Egyes csempék húzási fogóponttal támogatják az átméretezést.

![húzása a fogópontnak a méret módosításához](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

Mások fix méretű közül lehet választani a helyi menüben.

![méretezés, helyi menü a méret módosításához](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Az irányítópult megosztása

Az irányítópult konfigurálása után a következő lépés az irányítópult közzététele a **Megosztás** paranccsal.

![irányítópult megosztása](./media/azure-portal-dashboards-create-programmatically/share-command.png)

A **Megosztás** lehetőséget választva kiválaszthatja, hogy melyik előfizetésben és erőforráscsoportban szeretne közzétenni. Írási hozzáféréssel kell rendelkeznie a kiválasztott előfizetéshez és erőforráscsoporthoz. További információ: [Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és az Azure Portal használatával című témakörben.](../role-based-access-control/role-assignments-portal.md)

![módosítások a megosztáson és a hozzáférésen](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Az irányítópult JSON-ábrázolásának beolvasása

A közzététel csak néhány másodpercet vesz igénybe. Ha ez megtörtént, a következő lépés a JSON **letöltése** a Letöltés paranccsal.

![letöltés JSON képviselet](./media/azure-portal-dashboards-create-programmatically/download-command.png)

## <a name="create-a-template-from-the-json"></a>Sablon létrehozása a JSON-ból

A következő lépés egy sablon létrehozása ebből a JSON-ból. Használja ezt a sablont programozott módon a megfelelő erőforrás-kezelési API-kkal, parancssori eszközökkel vagy a portálon belül.

Nem kell teljes mértékben megérteni az irányítópult JSON-struktúrát egy sablon létrehozásához. A legtöbb esetben meg szeretné őrizni az egyes csempék szerkezetét és konfigurációját. Ezután paraméterezi az Azure-erőforrások készletét, amelyekre a csempék mutatnak. Tekintse meg az exportált JSON-irányítópultot, és keresse meg az Azure-erőforrás-azonosítók összes előfordulását. A példa irányítópult több csempe, amely minden pont egy Azure virtuális gép. Ennek az az oka, hogy az irányítópult csak ezt az egyetlen erőforrást vizsgálja. Ha a dokumentum végén található JSON mintában "/előfizetések" kifejezésre keres, az azonosító több előfordulását is megtalálja.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Ha a jövőben bármely virtuális géphez közzé szeretné tenni ezt az irányítópultot, paraméterezse a karakterlánc minden előfordulását a JSON-on belül.

Az API-k nak két módszere van, amelyek erőforrásokat hoznak létre az Azure-ban:

* Az elengedhetetlen API-k egyszerre csak egy erőforrást hoznak létre. További információ: [Resources](/rest/api/resources/resources).
* Sablonalapú központi telepítési rendszer, amely egyetlen API-hívással több, függő erőforrást hoz létre. További információ: [Erőforrások üzembe helyezése erőforrás-kezelői sablonokkal és az Azure PowerShell használatával](../azure-resource-manager/resource-group-template-deploy.md)című témakörben olvashat.

A sablonalapú telepítés támogatja a paraméterezést és a templatingot. Ezt a megközelítést használjuk ebben a cikkben.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Programozott módon hozzon létre irányítópultot a sablonból egy sablon központi telepítésével

Az Azure lehetővé teszi több erőforrás üzembe helyezésének vezénylése. Hozzon létre egy központi telepítési sablont, amely kifejezi az üzembe helyezhető erőforrások készletét és a köztük lévő kapcsolatokat.  Az egyes erőforrások JSON formátuma megegyezik, mintha egyenként hozna létre őket. A különbség az, hogy a sablon nyelve hozzáad néhány fogalmat, például változókat, paramétereket, alapvető függvényeket és egyebeket. Ez a kiterjesztett szintaxis csak sablontelepítés környezetében támogatott. Ez nem működik, ha használják a korábban tárgyalt elengedhetetlen API-k. További [információ: Az Azure Resource Manager-sablonok szerkezetének és szintaxisának megismerése című témakörben olvashat.](../azure-resource-manager/resource-group-authoring-templates.md)

A paraméterezést a sablon paraméterszintaxisával kell elvégezni.  Az itt látható módon felülírja az erőforrás-azonosító összes példányát.

Példa JSON-tulajdonságra kódolt erőforrásazonosítóval:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Példa A JSON-tulajdonság paraméterezett verzióvá konvertálva sablonparaméterek alapján

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Deklarálja a szükséges sablonmetaadatokat és a JSON-sablon tetején található paramétereket az ehhez hasonlóan:

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
A sablon konfigurálása után telepítse az alábbi módszerek valamelyikével:

* [REST API-k](/rest/api/resources/deployments)
* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure CLI](/cli/azure/group/deployment#az-group-deployment-create)
* [Az Azure Portal sablon telepítési lapja](https://portal.azure.com/#create/Microsoft.Template)

Ezután megjelenik a példa irányítópult JSON két verziója. Az első az a verzió, amelyet a portálról exportáltunk, és amely már egy erőforráshoz volt kötve. A második a sablon verziója, amely programozott módon kötődik bármely virtuális géphez, és az Azure Resource Manager használatával telepíthető.

### <a name="json-representation-of-our-example-dashboard-before-templating"></a>A példairányítópult JSON-ábrázolása a templating előtt

Ez a példa azt mutatja be, hogy mire számíthat, ha követte-e ezt a cikket. Az utasítások exportálták a már üzembe helyezett irányítópult JSON-ábrázolását. A kódolt erőforrás-azonosítók azt mutatják, hogy ez az irányítópult egy adott Azure virtuális gépre mutat.

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

### <a name="template-representation-of-our-example-dashboard"></a>A példairányítópult sablonábrázolása

Az irányítópult sablonverziója három paramétert határozott `virtualMachineName` `virtualMachineResourceGroup`meg `dashboardName`, és a .  A paraméterek segítségével ezt az irányítópultot egy másik Azure virtuális gépen minden alkalommal, amikor üzembe helyezi. Ez az irányítópult programozott módon konfigurálható és telepíthető bármely Azure virtuális gépre. A szolgáltatás teszteléséhez másolja a következő sablont, és illessze be az [Azure Portal sablon telepítési lapjára.](https://portal.azure.com/#create/Microsoft.Template)

Ez a példa önmagában telepítegy irányítópultot, de a sablon nyelve lehetővé teszi több erőforrás üzembe helyezését, és egy vagy több irányítópult oka egy vagy több kötegelése.

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

Most, hogy látott egy példát egy paraméterezett sablon használatával egy irányítópult üzembe helyezéséhez, megpróbálhatja telepíteni a sablont az [Azure Resource Manager REST API-jaival](/rest/api/), az Azure [CLI-vel](/cli/azure)vagy az [Azure PowerShell-parancsokkal.](/powershell/azure/get-started-azureps)
