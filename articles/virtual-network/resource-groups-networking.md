---
title: "A hálózati erőforrás-szolgáltató áttekintése |} Microsoft Docs"
description: "További tudnivalók az új hálózati erőforrás-szolgáltató az Azure Resource Manager"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 79bf09da-4809-45cb-8d21-705616ef24dc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: 2428c707ddeed281fddd1e57bc5574603f0b9b1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="network-resource-provider"></a>Hálózatierőforrás-szolgáltató
Napjaink üzleti sikeres, a egy megerősítő kell azt a képességet, és nagy méretű hálózati alkalmazások kezelése a gyors, rugalmas, biztonságos és ismételhető módon. Az Azure Resource Manager eszközzel hozhat létre ilyen alkalmazások, mint az erőforrások erőforráscsoportok egyetlen gyűjtemény. Ilyen erőforrások kezelése különböző erőforrás-szolgáltató az erőforrás-kezelő használatával.

Az Azure Resource Manager támaszkodik a különböző erőforrás-szolgáltatók számára az erőforrások eléréséhez. Nincsenek három fő erőforrás-szolgáltatókon: hálózati, tárolási és számítási. Ez a dokumentum ismerteti a jellemzőit és a hálózatierőforrás-szolgáltatónál, előnyei többek között:

* **Metaadatok** – információkat is hozzáadhat erőforrásokat címkék használatával. Ezekkel a címkékkel erőforráscsoport-sablonok és előfizetések közötti erőforrás-használat nyomon követésére használható.
* **Hatékonyabb vezérlését a hálózati** - hálózati erőforrások lazán, és részletesebb módon szabályozhatja. Ez azt jelenti, hogy a hálózati erőforrások kezelése több beleszólása van.
* **Gyorsabb konfigurációs** -hálózati erőforrások vannak lazán, mert hozhat létre és levezényelni a hálózati erőforrások párhuzamosan. Ez jelentősen lecsökkentette konfigurációs idő.
* **Szerepköralapú hozzáférés-vezérlés** -RBAC biztosít az alapértelmezett szerepköröket, meghatározott biztonsági hatókörrel, amellett, hogy a biztonságos Management egyedi szerepkörök létrehozását lehetővé.
* **Egyszerűbb kezelés és a telepítés** -könnyebben telepítheti és kezelheti az alkalmazásokat, mivel is létrehozhat egész alkalmazáscsoportokat erőforráscsoport erőforrásainak egyetlen gyűjteményeként. És gyorsabb üzembe helyezéséhez, egyszerűen adja meg a sablon JSON-adattartalmat telepítése óta.
* **Gyors testreszabási** -stílusú deklaratív sablonok segítségével megismételhető és gyors testreszabási központi telepítésének engedélyezése.
* **A testreszabáshoz megismételhető** -stílusú deklaratív sablonok segítségével megismételhető és gyors testreszabási központi telepítésének engedélyezése.
* **Felügyeleti felületek** -az erőforrások kezeléséhez használhatja a következő illesztők bármelyikét:
  * REST-alapú API
  * PowerShell
  * .NET SDK
  * Node.JS SDK
  * Java SDK
  * Azure CLI
  * Betekintő portál
  * Erőforrás-kezelő sablon nyelve

## <a name="network-resources"></a>Hálózati erőforrások
Most függetlenül is felügyelhetők hálózati erőforrások, így ahelyett hogy azokat egyetlen számítási erőforrás (virtuális gép) kezelhetők. Ez biztosítja, hogy nagyobb fokú rugalmasságot és agilitást az erőforráscsoport egy összetett és nagy méretű méretezési infrastruktúra létrehozása.

Egy üzembe helyezési minta egy többrétegű alkalmazást magában foglaló konceptuális ábrázolása az alábbi táblázat mutatja. Az egyes erőforrások jelenik meg, például a hálózati adapterek, a nyilvános IP-címek és a virtuális gépek, egymástól függetlenül is felügyelhetők.

![Hálózati erőforrás-modellje](./media/resource-groups-networking/Figure2.png)

Minden erőforrás azokat egy közös tulajdonságokat, és az egyéni tulajdonság. Az általános tulajdonságok a következők:

| Tulajdonság | Leírás | Példaértékek |
| --- | --- | --- |
| **név** |Egyedi erőforrás neve. Minden erőforrás saját vonatkozó elnevezési korlátozás rendelkezik. |PIP01, VM01, NIC01 |
| **hely** |Azure-régió, ahol az erőforrás van |westus, eastus |
| **azonosítója** |Egyedi alapú URI-azonosító |következő<subGUID>/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP |

Ellenőrizheti az erőforrások az alábbi szakaszokban található egyes tulajdonságait.

[!INCLUDE [virtual-networks-nrp-pip-include](../../includes/virtual-networks-nrp-pip-include.md)]

[!INCLUDE [virtual-networks-nrp-nic-include](../../includes/virtual-networks-nrp-nic-include.md)]

[!INCLUDE [virtual-networks-nrp-nsg-include](../../includes/virtual-networks-nrp-nsg-include.md)]

[!INCLUDE [virtual-networks-nrp-udr-include](../../includes/virtual-networks-nrp-udr-include.md)]

[!INCLUDE [virtual-networks-nrp-vnet-include](../../includes/virtual-networks-nrp-vnet-include.md)]

[!INCLUDE [virtual-networks-nrp-dns-include](../../includes/virtual-networks-nrp-dns-include.md)]

[!INCLUDE [virtual-networks-nrp-lb-include](../../includes/virtual-networks-nrp-lb-include.md)]

[!INCLUDE [virtual-networks-nrp-appgw-include](../../includes/virtual-networks-nrp-appgw-include.md)]

[!INCLUDE [virtual-networks-nrp-vpn-include](../../includes/virtual-networks-nrp-vpn-include.md)]

[!INCLUDE [virtual-networks-nrp-tm-include](../../includes/virtual-networks-nrp-tm-include.md)]

## <a name="management-interfaces"></a>Felügyeleti felületek
Az Azure-hálózati erőforrások különböző felületek segítségével kezelheti. Ebben a dokumentumban tárgyaljuk ezeket felületek fonókábel: REST API-t, és a sablonokat.

### <a name="rest-api"></a>REST API
A korábban említett csatolókat, beleértve a REST API-t, a .NET SDK, Node.JS SDK, Java SDK, PowerShell, CLI, Azure Portal és sablonok számos hálózati erőforrások is felügyelhetők.

A Rest API felelnek meg a HTTP 1.1 protokoll-meghatározása. Az API-t általános URI szerkezete alatt jelenik meg:

    https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

És a zárójelek közé foglalt paraméterek megfelelnek a következő elemeket:

* **előfizetés-azonosító** -Azure-előfizetése azonosítóját.
* **erőforrás-szolgáltató-névtér** -használt szolgáltató névterét. A hálózati erőforrás-szolgáltató értéke *Microsoft.Network*.
* **terület-neve** – az Azure-régiót neve

A következő HTTP-metódus támogatja a REST API hívása esetén:

* **PUT** használt hozzon létre egy adott típusú erőforrást, az erőforrás-tulajdonság módosításához vagy erőforrások közötti társítás módosítása –.
* **ELSŐ** - használt kiosztott erőforrás információinak lekérése érdekében.
* **Törlés** - használt törölje valamelyik meglévő erőforrására.

A kérelem és a válasz egy JSON-adattartalom formátuma megfelel. További részletekért lásd: [Azure erőforrás-kezelési API-k](https://msdn.microsoft.com/library/azure/dn948464.aspx).

### <a name="resource-manager-template-language"></a>Erőforrás-kezelő sablon nyelve
Mellett imperatively (API-k vagy SDK) keresztül erőforrások kezelése, használhatja a deklaratív programozási stílus és hálózati erőforrások kezelése a Resource Manager sablon nyelv használatával.

A minta egy sablon megjelenítése alább –

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "<version-number-of-template>",
      "parameters": { <parameter-definitions-of-template> },
      "variables": { <variable-definitions-of-template> },
      "resources": [ { <definition-of-resource-to-deploy> } ],
      "outputs": { <output-of-template> }    
    }

A sablon elsősorban JSON-leírásuk az erőforrások és a paraméterek keresztül beszúrta példány értéke. Az alábbi példa segítségével hozzon létre egy virtuális hálózatot 2 alhálózatokon.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
          "location": {
            "type": "String",
            "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
            "metadata" : {
              "Description" : "Deployment location"
            }
          },
          "virtualNetworkName":{
            "type" : "string",
            "defaultValue":"myVNET",
            "metadata" : {
              "Description" : "VNET name"
            }
          },
          "addressPrefix":{
            "type" : "string",
            "defaultValue" : "10.0.0.0/16",
            "metadata" : {
              "Description" : "Address prefix"
            }

          },
          "subnet1Name": {
            "type" : "string",
            "defaultValue" : "Subnet-1",
            "metadata" : {
              "Description" : "Subnet 1 Name"
            }
          },
          "subnet2Name": {
            "type" : "string",
            "defaultValue" : "Subnet-2",
            "metadata" : {
              "Description" : "Subnet 2 name"
            }
          },
          "subnet1Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.0.0/24",
            "metadata" : {
              "Description" : "Subnet 1 Prefix"
            }
          },
          "subnet2Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.1.0/24",
            "metadata" : {
              "Description" : "Subnet 2 Prefix"
            }
          }
        },
        "resources": [
        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[parameters('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[parameters('subnet1Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet1Prefix')]"
                }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet2Prefix')]"
                }
              }
            ]
          }
        }
        ]
    }

A paraméterértékek manuálisan adjanak a sablon használatakor rendelkezik, vagy egy paraméterfájl is használhat. Az alábbi példában látható a fenti sablon használni kívánt paraméterértékeket lehetséges készlete:

    {
      "location": {
          "value": "East US"
      },
      "virtualNetworkName": {
          "value": "VNET1"
      },
      "subnet1Name": {
          "value": "Subnet1"
      },
      "subnet2Name": {
          "value": "Subnet2"
      },
      "addressPrefix": {
          "value": "192.168.0.0/16"
      },
      "subnet1Prefix": {
          "value": "192.168.1.0/24"
      },
      "subnet2Prefix": {
          "value": "192.168.2.0/24"
      }
    }


A sablonokkal fő előnyei a következők:

* Egy összetett infrastruktúra deklaratív Style erőforráscsoportban is létrehozható. A vezénylési létrehozásához az erőforrások, például a függőségi felügyeleti erőforrás-kezelő által kell kezelni.
* Az infrastruktúra is létrehozható egy ismételhető módon különböző régiókban és régión belül paraméterek egyszerűen módosításával.
* A deklaratív stílus rövidebb átfutási idő a sablonok létrehozása és az infrastruktúra terítésével vezet.

A minta-sablonok, lásd: [Azure gyors üzembe helyezési sablonokat](https://github.com/Azure/azure-quickstart-templates).

A Resource Manager sablon nyelvi további információkért lásd: [Azure Resource Manager sablon nyelvi](../azure-resource-manager/resource-group-authoring-templates.md).

A fenti mintasablon használja a virtuális hálózati és alhálózati erőforrásokat. Nincsenek más hálózati erőforrásokhoz, használhatja az alábbiak szerint:

### <a name="using-a-template"></a>Egy sablon használatával
Telepíthet szolgáltatások az Azure-bA sablonból powershellel, AzureCLI, vagy egy központi telepítése a Githubból kattintva elvégzésével. A Githubon sablonból szolgáltatások telepítéséhez hajtsa végre az alábbi lépéseket:

1. Nyissa meg a template3 fájlt a Githubról. Tegyük fel, nyissa meg a [, két alhálózattal a virtuális hálózati](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network).
2. Kattintson a **az Azure telepítéséhez**, és jelentkezzen be Azure-portálon a hitelesítő adataival.
3. Ellenőrizze a sablont, és kattintson **mentése**.
4. Kattintson a **paraméterek szerkesztése** és adjon meg egy helyet, például a *USA nyugati régiója*, hálózatok és alhálózatok.
5. Szükség esetén módosítsa a **CÍMELŐTAGJA** és **SUBNETPREFIX** paramétereket, és kattintson **OK**.
6. Kattintson a **válasszon egy erőforráscsoportot** pedig az erőforráscsoport hozzá szeretné adni a hálózatok és alhálózatok parancsra. Alternatív megoldásként létrehozhat egy új erőforráscsoportot kattintva **, vagy hozzon létre új**.
7. Kattintson a **Create** (Létrehozás) gombra. Figyelje meg, a csempe megjelenítése **kiépítés sablon-üzembehelyezés**. Miután a telepítés befejeződött, megjelenik az alábbi hasonló képernyő.

![Sablon üzembe helyezési minta](./media/resource-groups-networking/Figure6.png)

## <a name="next-steps"></a>Következő lépések
[Az Azure Resource Manager sablon nyelve](../azure-resource-manager/resource-group-authoring-templates.md)

[Az Azure hálózatkezelés – általánosan használt sablon](https://github.com/Azure/azure-quickstart-templates)

[Az Azure Resource Manager és klasszikus üzembe helyezési összehasonlítása](../azure-resource-manager/resource-manager-deployment-model.md)

[Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md)

