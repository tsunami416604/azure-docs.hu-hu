---
title: Programozott módon az Azure-irányítópultot létrehozni |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan lehet programozott módon az Azure-irányítópultot létrehozni.
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
ms.openlocfilehash: dafada5cecbc6345da46bc3a32fc3b91eb72313a
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295511"
---
# <a name="programmatically-create-azure-dashboards"></a>Programozott módon az Azure-irányítópultot létrehozni

Ebből a dokumentumból a folyamatot, amely programozott módon létrehozása és közzététele az Azure irányítópultok segítségével. Az alább látható irányítópult hivatkozik a dokumentum.

![a minta-irányítópult](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Áttekintés

Irányítópultok Azure azok a megosztott [erőforrások](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) hasonlóan a virtuális gépek és tárfiókok.  Ezért ezek kezelhetők programozott módon keresztül a [Azure Resource Manager REST API-k](/rest/api/), a [Azure CLI](https://docs.microsoft.com/cli/azure), [Azure PowerShell-parancsok](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azurermps-4.2.0), és sok [ Azure-portálon](https://portal.azure.com) szolgáltatások felett erőforrást kezelésének megkönnyítése ezen API-k létrehozása.  

Ezen API-k és eszközök kínál módon hozhat létre, a listában, beolvasása, módosítása, és törli az erőforrást.  Mivel az irányítópultok erőforrásokat, válassza ki a kedvenc API, vagy eszköz használatához.

Függetlenül attól, hogy használja szeretne összeállítani az irányítópult-objektumból JSON-ábrázolását, bármely erőforrás létrehozása API hívása előtt. Ez az objektum (más néven a kijelzők információkat tartalmaz tartalmazó csempék éppen) az irányítópulton. Tartalmazza a mérete, pozíciók, erőforrások vannak kötve, és végzett felhasználói testreszabásokkal.

A legjobb módszer a JSON-dokumentum kialakításához használandó [a portál](https://portal.azure.com/) interaktív adható hozzá, és helyezze a csempéket. Majd exportálja a JSON-NÁ. Végezetül hoz létre egy sablont az eredmény a parancsfájlok, programok és központi telepítési eszközök későbbi használatra.

## <a name="create-a-dashboard"></a>Irányítópult létrehozása

Hozzon létre egy új irányítópult, használja az új irányítópult-parancsot a portál fő képernyőjén.

![új irányítópult parancs](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

A csempe gyűjteményben található, és hozzáadhatja a csempéket használhatja. Csempék húzva adhatók hozzá. Egyes csempék támogatja átméretezése egy húzza leíró, míg mások támogatása javítja által látható a helyi menü segítségével.

### <a name="drag-handle"></a>Húzza a leíró
![Húzza a leíró](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>Rögzített méretű keresztül a helyi menü
![méretek a helyi menü](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Az irányítópult megosztására

Miután konfigurálta az irányítópulton a következő lépésekre közzététele az irányítópulton (a megosztás parancs segítségével), majd az erőforrás-kezelő beolvasása a JSON tetszőlegesen a.

![megosztás parancs](./media/azure-portal-dashboards-create-programmatically/share-command.png)

A Megosztás parancsra jeleníti meg, amely arra kéri, hogy mely közzétételére előfizetésbe és erőforráscsoportba csoport kiválasztása párbeszédpanel. A következőket kell figyelembe venni, hogy [írási hozzáféréssel kell rendelkeznie](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) a választott előfizetésbe és erőforráscsoportba csoporthoz.

![megosztás- és hozzáférés](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>A JSON-megjelenítés irányítópult beolvasása

A közzététel csak néhány másodpercet vesz igénybe.  Ha elkészült, a következő lépésre ugorhat-e a [erőforrás-kezelő](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade) a JSON beolvasása.

![Keresse meg az erőforrás-kezelő](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

Az erőforrás-kezelő keresse meg a választott előfizetésbe és erőforráscsoportba csoport. Ezután kattintson az újonnan közzétett irányítópulton erőforráson, hogy láthatóvá váljon a JSON-NÁ.

![Resource explorer json](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>A JSON-sablon létrehozása

A következő lépés, ha a sablon a JSON formátumból, hogy az újrafelhasználható programozott módon a megfelelő erőforrás Management API-k, parancssori eszközök, vagy a portálon.

Nincs szükség reklámkattintásokat sablon létrehozása az irányítópult JSON struktúrában. A legtöbb esetben szeretné megőrizni a rendszer szerkezetének és konfigurációjának mindegyik mozaiknál, és majd parametrizálja most mutató Azure-erőforrások készletét. Nézze meg az exportált JSON-irányítópult és az Azure erőforrás-azonosítók minden előfordulása. A példa irányítópult van több csempék, hogy az összes egyetlen Azure virtuális gépre mutat. Ennek oka az, az irányítópult csak ellenőrzi, hogy ez az egyetlen erőforrás. Ha a minta json (a dokumentum végén része) a "/ előfizetések", látnia többszöri felbukkanását ezt.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Ehhez az irányítópulthoz a virtuális gépek közzététele a jövőben kell ezt a karakterláncot a JSON belül minden előfordulását parametrizálja. 

Az API-t létrehozni az erőforrásokat az Azure-ban két verziója van. [Feltétlenül szükséges API-k](https://docs.microsoft.com/rest/api/resources/resources) , hozzon létre egy erőforrást, egyszerre, és egy [sablon-alapú üzembe helyezési](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) rendszer, amely lehet levezényelni a létrehozása több, a függő erőforrások egyetlen API-hívással. Az utóbbi natív módon támogatja a (egyszerű) paraméterezéssel és templating, a fenti példában használjuk.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Programozott módon irányítópultot hozhat létre a sablonból sablon központi telepítéssel

Azure lehetővé teszi annak kell levezényelni a több erőforrás központi telepítését. Létrehozhat egy központi telepítési sablont, amely kifejezi központi telepítéséhez, valamint a köztük lévő viszonyt is erőforrások készletét.  A JSON formátum az egyes erőforrások megegyezik, mintha csak egyenként hozott létre őket. A különbség az, hogy a [sablon nyelvi](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) néhány fogalommal, például a változók, a paraméterek, az alapvető funkciók és további hozzáadja. Ezt a kiegészítő szintaxis csak akkor támogatott a sablon-üzembehelyezés környezetében, és nem használható, ha a korábban tárgyalt feltétlenül szükséges API-khoz használt.

Ha ez az útvonal fog, majd (egyszerű) paraméterezéssel el kell végezni a sablon paraméter szintaxis használatával.  Lecseréli az erőforrás-azonosító észleltünk a korábbi, ahogy az itt látható az összes példányát.

### <a name="example-json-property-with-hard-coded-resource-id"></a>Példa JSON tulajdonság a kódolt erőforrás-azonosítót
`id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>Példa JSON tulajdonság konvertálva a paraméteres verziót sablon paraméterek alapján

`id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"`

Is kell deklarálni, néhány szükséges sablont metaadatok és a felső részén a json-sablon a Paraméterek:

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

__Ez a dokumentum végén a teljes, működő sablon tekintheti meg.__

Ha Ön rendelkezik kialakított a sablon használatával telepítheti a [REST API-k](https://docs.microsoft.com/rest/api/resources/deployments), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), a [Azure CLI](https://docs.microsoft.com/cli/azure/group/deployment#az_group_deployment_create), vagy a [portál telepítési sablonlap ](https://portal.azure.com/#create/Microsoft.Template).

Az alábbiakban a példa irányítópult JSON két verziója. Az első verziója, amely azt a portálról, amely olyan erőforráshoz kötött már lett exportálva. A második pedig az, hogy minden virtuális gép szoftveresen kötött és az Azure Resource Manager használatával telepített verziójának.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>A példa irányítópult (előtt templating) JSON-megjelenítés

Ez azért, hogy mire számíthat megjelenítéséhez, ha egy irányítópultot, amely már telepítve van a JSON-ábrázolását lehívása korábbi utasítások végrehajtását. Megjegyzés: a kódolt erőforrás-azonosítók, amelyek megjelenítik a, hogy ez az irányítópult irányul-e egy adott Azure virtuális gép.

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

Az irányítópult sablon verzióját definiálva van a három paramétereket __virtualMachineName__, __virtualMachineResourceGroup__, és __dashboardName__.  A paraméterek lehetővé teszik, hogy ez az irányítópult pont minden alkalommal, amikor telepít egy másik Azure virtuális gépet. A paraméteres azonosítók megjelenítése, hogy ez az irányítópult programozott módon konfigurálhatók és mutasson a minden Azure virtuális gép telepítve vannak kiemelve. Ez a szolgáltatás teszteléséhez a legegyszerűbb módja a következő sablon másolja és illessze be azt a [Azure portál telepítési sablonlap](https://portal.azure.com/#create/Microsoft.Template). 

Ebben a példában az irányítópult önmagában központilag telepíti, de a sablon nyelvének lehetővé teszi, hogy több erőforrás telepítheti, és egy vagy több irányítópultok mentén ügyféloldali csomagot őket. 

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
