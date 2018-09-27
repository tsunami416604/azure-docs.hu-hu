---
title: Azure-irányítópultok szoftveres létrehozása |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre programozott módon az Azure-irányítópultok.
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
ms.openlocfilehash: a9ca57ce916dc3f9bc8d451063596519d1d6649c
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47391342"
---
# <a name="programmatically-create-azure-dashboards"></a>Azure-irányítópultok szoftveres létrehozása

Ez a dokumentum végigvezeti a programozott módon létrehozása és közzététele az Azure-irányítópultok. Alább látható az irányítópult teljes dokumentumban hivatkozik.

![a minta-irányítópult](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Áttekintés

Megosztott irányítópultok Azure is [erőforrások](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) hasonlóan a virtuális gépek és a storage-fiókok.  Ezért azokat programozott módon keresztül kezelhetők a [Azure Resource Manager REST API-k](/rest/api/), a [Azure CLI-vel](https://docs.microsoft.com/cli/azure), [Azure PowerShell-parancsok](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azurermps-4.2.0), és számos [ Az Azure portal](https://portal.azure.com) funkciói könnyebbé tenni az erőforrás-kezelés ezen API-k épülnek.  

Minden egyes ezen API-k és eszközök kínál módon hozhat létre, a listában, beolvasása, módosításához és törléséhez az erőforrásokat.  Mivel az irányítópultok erőforrásokat, válassza ki a kedvenc API-hoz, illetve eszköz használata.

Melyik eszközt használja, függetlenül kell hozhat létre egy JSON-reprezentációja az irányítópult-objektumból, bármely erőforrás-létrehozás API meghívása előtt. Ez az objektum tartalmaz információt a részek (más néven) csempék) az irányítópulton. Méretek, pozíciók, erőforrások vannak kötve és bármely felhasználó testreszabásokat tartalmaz.

A legtöbb gyakorlati legegyszerűbb módja, építse fel a JSON-dokumentumnak [a portál](https://portal.azure.com/) interaktív módon adja hozzá, és helyezze a csempéket. Majd exportálja a JSON-fájllal. Végül hozza létre a sablon a parancsfájlok, programok és központi telepítési eszközök későbbi használatra az eredményből.

## <a name="create-a-dashboard"></a>Irányítópult létrehozása

Hozzon létre egy új irányítópultot, a paranccsal az új irányítópult a portál fő képernyőjén.

![új irányítópult-parancs](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

A csempe katalógus majd használatával kereshet és adhat hozzá a csempéket. Csempék adhatók húzásával. Egyes csempék támogatja a átméretezése húzza leírót, míg más modelljei támogatási javítások által látható a saját helyi menü segítségével.

### <a name="drag-handle"></a>A csomóponthúzási leíró
![A csomóponthúzási leíró](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>Rögzített méretű helyi menüjében
![méretek helyi menü](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Az irányítópult megosztása

Miután konfigurálta az irányítópult a következő lépések a következők tehet közzé az irányítópultot (a megosztás parancs használatával), és az erőforrás-kezelő használatával beolvassa a JSON tetszőlegesen a.

![a megosztás parancs](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Egy párbeszédpanel, amely arra kéri, hogy melyik előfizetést és erőforráscsoportot csoporthoz való közzétételéhez válassza a Megosztás parancsra jeleníti meg. Vegye figyelembe, hogy [írási hozzáféréssel kell rendelkeznie](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) a kiválasztott előfizetésben és erőforráscsoportban csoporthoz.

![megosztás és hozzáférés](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>A JSON-reprezentációja az irányítópult beolvasása

Közzététel csak néhány másodpercet vesz igénybe.  Amikor kész van, a következő lépésre ugráshoz-e a [erőforrás-kezelő](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade) beolvasni a JSON-fájllal.

![Keresse meg az erőforrás-kezelő](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

Az erőforrás-kezelő keresse meg a kiválasztott előfizetésben és erőforráscsoportban csoport. Ezután kattintson az újonnan közzétett irányítópultot erőforráson megjelenítéséhez a JSON-fájllal.

![a JSON-erőforrás-kezelő](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>Hozzon létre egy sablont a JSON-ból

A következő lépés, hogy hozzon létre egy sablont a JSON-ból, így azt korábbit újból használhatna programozott módon a megfelelő erőforrás-kezelés API-k, parancssori eszközöket, vagy a portálon.

Nem kell teljesen értelmezésekor irányítópult JSON-sablon létrehozása. A legtöbb esetben szeretné megőrizni a rendszer szerkezetének és konfigurációjának csempéket, és majd paraméterezni vagyunk mutató Azure-erőforrások készletét. Tekintse meg az exportált JSON-irányítópulton, és keresse meg az Azure erőforrás-azonosítók összes előfordulását. A példa irányítópulton szerepelnek olyan csempék több, hogy minden pont egy Azure virtuális gépen. Ennek oka az irányítópultról csak az egyetlen erőforrás megvizsgál. Ha a minta json (tartalmazza a dokumentum végén található) a "/ előfizetések", úgy találja, hogy többszöri felbukkanását forrástároló.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Ez minden olyan virtuális gép irányítópult közzététele a későbbiekben szüksége belül a JSON-ban Ez a karakterlánc összes előfordulásának paraméterezni. 

Nincsenek erőforrások létrehozása az Azure-ban API-két változatban érhetők el. [Imperatív API-k](https://docs.microsoft.com/rest/api/resources/resources) , hozzon létre egy erőforrást, egyszerre, és a egy [sablonalapú telepítés](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) rendszer, amely a létrehozás vezényelhető az egyetlen API hívással több, a függő erőforrások. Az utóbbi natív módon támogatja a paraméterezést és sablonalapú, a jelen példában használjuk.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Programozott módon a sablonból, egy sablon üzemelő példány használatával az irányítópult létrehozása

Az Azure lehetővé teszi több erőforrást központi telepítésének vezénylésére. A központi telepítési sablont, amely kifejezi az erőforrásokat üzembe helyezéséhez, valamint az azok közötti kapcsolatokat hoz létre.  Az egyes erőforrások JSON formátuma nem ugyanaz, mintha azok hoz egyenként. A különbség az, hogy a [sablon nyelvi](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) néhány fogalommal, mint a változókat, paraméterek, alapvető funkciókat ad hozzá. Ezt a kiegészítő szintaxis csak akkor támogatott a sablon központi telepítés keretében, és nem használható, ha a fentiekben említett imperatív API-kat használják.

Ez az útvonal fog, akkor a paraméterezést kell elvégezni a sablon paraméter szintaxis használatával.  Lecseréli az erőforrás-azonosító korábban itt látható módon talált összes példányát.

### <a name="example-json-property-with-hard-coded-resource-id"></a>Példa JSON-tulajdonságot a szokott erőforrás-azonosító
`id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>Sablon paraméterei alapján paraméteres verzióra konvertált példa JSON-tulajdonság

`id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"`

Emellett kell deklarálnia néhány szükséges sablon metaadatait, és ehhez hasonló json-sablon tetején a paramétereket:

```json

{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

__Láthatja, hogy a teljes, működő sablon Ez a dokumentum végén található.__

Miután Ön rendelkezik kialakított a sablon használatával telepíthet a [REST API-k](https://docs.microsoft.com/rest/api/resources/deployments), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/group/deployment#az-group-deployment-create), vagy a [portál sablon üzembe helyezés lap ](https://portal.azure.com/#create/Microsoft.Template).

Az alábbiakban a példa irányítópult JSON két verziója. Az első verziója, amely azt a portálról, amely már volt olyan erőforráshoz kötött exportálja. A második pedig a tanúsítványsablon verziójának programozott módon köthető, virtuális Gépeket, és telepített, az Azure Resource Managerrel.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>A példa irányítópult (előtt sablonalapú) JSON-ábrázolása

Ez a várttól is megtekintheti, ha Ön az utasítások korábbi beolvasni egy irányítópultot, amely már telepítve van a JSON-ábrázolását. Megjegyzés: a kódolt erőforrás-azonosítók, amelyek megmutatják, hogy ezt az irányítópultot, egy adott Azure virtuális gép mutat.

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

### <a name="template-representation-of-our-example-dashboard"></a>A példa irányítópult sablon ábrázolása

Az irányítópult a tanúsítványsablon verziójának definiált nevű három paraméterei __virtualMachineName__, __virtualMachineResourceGroup__, és __dashboardName__.  A paraméterek lehetővé teszik ezt az irányítópultot pont minden alkalommal, amikor telepít egy másik Azure virtuális gépen. A paraméteres azonosítók megjelenítése, hogy ez az irányítópult programozott módon konfigurálhatók és mutasson a minden Azure virtuális gépen telepített ki vannak emelve. A legegyszerűbben úgy tesztelheti ezt a funkciót, hogy a következő sablon másolja és illessze be azt a [Azure Portalon a sablon üzembe helyezés lap](https://portal.azure.com/#create/Microsoft.Template). 

Ebben a példában önmagában helyez üzembe egy irányítópultot, de a sablon nyelvének lehetővé teszi több erőforrások üzembe helyezése, és a egy vagy több irányítópulttal jelentésvászon oldalán csomagot őket. 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
