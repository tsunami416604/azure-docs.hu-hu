---
title: Ajánlott eljárások Azure Resource Manager-sablonokhoz
description: A Azure Resource Manager sablonok létrehozásához ajánlott megközelítéseket ismerteti. Javaslatokat nyújt a gyakori problémák elkerülésére a sablonok használatakor.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2019
ms.author: tomfitz
ms.openlocfilehash: cdec216187050a449f23f72474e0265acce14c5f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "67867393"
---
# <a name="azure-resource-manager-template-best-practices"></a>Azure Resource Manager sablon – ajánlott eljárások

Ez a cikk a Resource Manager-sablon összeállításával kapcsolatos javaslatokat tartalmaz. Ezek a javaslatok segítenek elkerülni a gyakori problémákat, amikor sablont használ a megoldás üzembe helyezéséhez.

Az Azure-előfizetések szabályozásával kapcsolatos javaslatokért [lásd: Azure Enterprise állvány: Előfizetési előfizetések](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)irányítása.

Az összes Azure-beli felhőalapú környezetben működő sablonok létrehozásával kapcsolatos javaslatokért lásd: [Azure Resource Manager-sablonok fejlesztése a Felhőbeli konzisztencia](templates-cloud-consistency.md)érdekében.

## <a name="template-limits"></a>Sablonok korlátai

Korlátozza a sablon méretét 4 MB-ra, az egyes paramétereket pedig 64 KB-ra. A 4 MB-os korlát a sablon végső állapotára vonatkozik, miután az ismétlődő erőforrás-definíciókkal bővült, valamint a változók és paraméterek értékeit. 

A következőket is korlátozhatja:

* 256 paraméterek
* 256 változók
* 800-erőforrások (beleértve a példányszámot)
* 64 kimeneti értékek
* 24 576 karakter egy sablon kifejezésében

Az egyes sablonokra vonatkozó korlátokat egy beágyazott sablon használatával lehet meghaladni. További információ: az [Azure-erőforrások üzembe helyezéséhez csatolt sablonok használata](resource-group-linked-templates.md). A paraméterek, változók és kimenetek számának csökkentése érdekében több értéket is egyesítheti egy objektumban. További információ: [objektumok paraméterként](resource-manager-objects-as-parameters.md).

## <a name="resource-group"></a>Resource group

Amikor erőforrásokat telepít egy erőforráscsoporthoz, az erőforráscsoport az erőforrásokkal kapcsolatos metaadatokat tárolja. A metaadatokat az erőforráscsoport helye tárolja.

Ha az erőforráscsoport régiója átmenetileg nem érhető el, az erőforráscsoport erőforrásai nem frissíthetők, mert a metaadatok nem érhetők el. A más régiókban lévő erőforrások továbbra is a várt módon fognak működni, de nem frissítheti őket. A kockázat minimalizálásához keresse meg az erőforráscsoportot és az erőforrásokat ugyanabban a régióban.

## <a name="parameters"></a>Paraméterek
Az ebben a szakaszban található információk hasznosak lehetnek, ha [paraméterekkel](resource-group-authoring-templates.md#parameters)dolgozik.

### <a name="general-recommendations-for-parameters"></a>Paraméterekre vonatkozó általános javaslatok

* Csökkentse a paraméterek használatát. Ehelyett használjon változókat vagy literál értékeket azokhoz a tulajdonságokhoz, amelyeket nem kell megadni az üzembe helyezés során.

* Használjon teve-esetet a paraméterek neveihez.

* Paraméterek használata olyan beállításokhoz, amelyek a környezettől függően változnak, például SKU, size vagy Capacity.

* Használja az egyszerű azonosításhoz megadni kívánt erőforrásnevek paramétereit.

* Adja meg a metaadatokban szereplő összes paraméter leírását:

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* A nem bizalmas paraméterek alapértelmezett értékének meghatározása. Az alapértelmezett érték megadásával megkönnyíti a sablon üzembe helyezését, a sablon felhasználói pedig egy megfelelő értéket láthatnak. Egy paraméter alapértelmezett értékének érvényesnek kell lennie az alapértelmezett telepítési konfigurációban lévő összes felhasználó számára. 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Egy opcionális paraméter megadásához ne használjon üres karakterláncot alapértelmezett értékként. Ehelyett használjon literál értéket vagy nyelvi kifejezést egy érték létrehozásához.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* Ne használjon paramétert az API-verzióhoz az erőforrástípus esetében. Az erőforrás-tulajdonságok és az értékek verziószáma eltérő lehet. Az IntelliSense egy szerkesztőprogramban nem tudja meghatározni a megfelelő sémát, ha az API-verzió értéke paraméter. Ehelyett a sablonban rögzített API-verziót kell használnia.

* Használjon `allowedValues` takarékosan. Csak akkor használja, ha meg kell győződnie arról, hogy néhány érték nem szerepel az engedélyezett beállítások között. Ha túl széles `allowedValues` körben használja, letilthatja az érvényes központi telepítéseket, ha nem tartja naprakészen a listát.

* Ha a sablon egyik paramétere megegyezik a PowerShell üzembe helyezési parancsában szereplő paraméterekkel, az erőforrás-kezelő feloldja ezt az elnevezési ütközést úgy, hogy hozzáadja a Postfix **FromTemplate** a sablon paraméterhez. Ha például egy **ResourceGroupName** nevű paramétert tartalmaz a sablonban, az ütközik a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) parancsmag **ResourceGroupName** paraméterével. Az üzembe helyezés során a rendszer kéri, hogy adjon meg egy értéket a **ResourceGroupNameFromTemplate**számára.

### <a name="security-recommendations-for-parameters"></a>Paraméterekkel kapcsolatos biztonsági javaslatok

* Mindig használja a felhasználónevek és jelszavak (vagy titkos kódok) paramétereit.

* Minden `securestring` jelszóhoz és titokhoz használható. Ha bizalmas adatokat továbbít egy JSON-objektumban, használja a `secureObject` típust. A biztonságos karakterláncot vagy biztonságos objektumtípust tartalmazó sablon-paraméterek nem olvashatók be az erőforrás-telepítés után. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Ne adja meg az alapértelmezett értékeket a felhasználónevek, jelszavak vagy bármely olyan érték számára `secureString` , amelyhez típus szükséges.

* Ne adja meg az alkalmazás támadási felületét növelő tulajdonságok alapértelmezett értékeit.

### <a name="location-recommendations-for-parameters"></a>Paraméterekre vonatkozó javaslatok

* Egy paraméter használatával adja meg az erőforrások helyét, és állítsa be az alapértelmezett értéket `resourceGroup().location`. A Location paraméter megadása lehetővé teszi a sablon felhasználói számára, hogy olyan helyet adjon meg, amelyben a telepítésük engedéllyel rendelkezik.

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   },
   ```

* Ne határozza `allowedValues` meg a Location paramétert. Előfordulhat, hogy a megadott webhelyek nem érhetők el az összes felhőben.

* Használja a Location paraméter értékét azon erőforrások esetében, amelyeknek valószínűleg ugyanazon a helyen kell lenniük. Ez a megközelítés lekicsinyíti a felhasználók számára a helyadatok megadására vonatkozó kérések számát.

* Az összes helyen nem elérhető erőforrások esetében használjon külön paramétert, vagy határozzon meg egy literális helyet.

## <a name="variables"></a>Változók

A következő információk hasznosak lehetnek a [változók](resource-group-authoring-templates.md#variables)használatakor:

* Használjon változókat olyan értékekhez, amelyeket többször kell használni a sablonban. Ha egy értéket csak egyszer használ, a rögzített érték megkönnyíti a sablon olvasását.

* Használjon változókat a sablon függvények összetett elrendezése alapján létrehozott értékekhez. A sablon könnyebben olvasható, ha a komplex kifejezés csak a változókban jelenik meg.

* Ne használjon változókat `apiVersion` erőforráson. Az API verziója határozza meg az erőforrás sémáját. A verzió gyakran nem módosítható az erőforrás tulajdonságainak módosítása nélkül.

* A [hivatkozási](resource-group-template-functions-resource.md#reference) függvény nem használható a sablon **változók** szakaszában. A **Reference** függvény az erőforrás futásidejű állapotáról származtatja az értékét. A változók azonban a sablon kezdeti elemzése során is megoldhatók. Olyan értékeket kell megadnia, amelyeknek a **hivatkozási** függvényt közvetlenül a sablon **erőforrások** vagy **kimenetek** szakaszában kell megadniuk.

* Adja meg azokat az erőforrásnevek változóit, amelyeknek egyedinek kell lenniük.

* Használjon egy [másolási hurkot a változók között](resource-group-create-multiple.md#variable-iteration) , hogy ismétlődő JSON-objektumokat hozzon létre.

* Távolítsa el a nem használt változókat.

## <a name="resource-dependencies"></a>Erőforrás-függőségek

A beállított függőségek [](resource-group-define-dependencies.md) meghatározásakor kövesse az alábbi irányelveket:

* Használja a **hivatkozási** függvényt, és adja meg az erőforrás nevét, és adjon meg egy implicit függőséget olyan erőforrások között, amelyeknek meg kell osztaniuk egy tulajdonságot. Ha már definiált `dependsOn` implicit függőséget, ne adjon hozzá explicit elemet. Ez a megközelítés csökkenti a szükségtelen függőségek kockázatát.

* Adja meg a gyermek erőforrást a szülő erőforrástól függőként.

* A False értékre beállított [állapotú](resource-group-authoring-templates.md#condition) erőforrásokat a rendszer automatikusan eltávolítja a függőségi sorrendből. Állítsa be a függőségeket úgy, hogy az erőforrás mindig telepítve legyen.

* A függőségek kaszkádolt beállítása explicit módon történő beállítás nélkül. A virtuális gép például egy virtuális hálózati adaptertől függ, és a virtuális hálózati adapter a virtuális hálózattól és a nyilvános IP-címektől függ. Ezért a virtuális gépet mindhárom erőforrás után telepíti a rendszer, de a virtuális gépet a mindhárom erőforrástól függőként nem állítja be explicit módon. Ez a megközelítés tisztázza a függőségi sorrendet, és megkönnyíti a sablon későbbi módosítását.

* Ha az üzembe helyezés előtt meg lehet határozni egy értéket, próbálja meg az erőforrást függőség nélkül telepíteni. Ha például egy konfigurációs értéknek egy másik erőforrás nevére van szüksége, lehet, hogy nincs szüksége függőségre. Ez az útmutató nem mindig működik, mert egyes erőforrások ellenőrzik a másik erőforrás létezését. Ha hibaüzenetet kap, vegyen fel egy függőséget.

## <a name="resources"></a>További források

A következő információk hasznosak lehetnek az erőforrásokkal való [](resource-group-authoring-templates.md#resources)munka során:

* Annak érdekében, hogy más közreműködők is megértsék az erőforrás célját , a sablonban szereplő egyes erőforrások megjegyzéseit kell megadnia:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Ha *nyilvános végpontot* használ a sablonban (például egy Azure Blob Storage nyilvános végpontot), *ne* a névteret. A névtér dinamikus beolvasásához használja a **Reference** függvényt. Ezt a módszert használhatja a sablon különböző nyilvános névtérbeli környezetekben történő üzembe helyezéséhez anélkül, hogy manuálisan módosítaná a végpontot a sablonban. Állítsa az API-verziót ugyanarra a verzióra, amelyet a sablonban használt Storage-fiókhoz használ:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Ha a Storage-fiók ugyanabban a sablonban van telepítve, amelyet Ön hoz létre, nem kell megadnia a szolgáltatói névteret az erőforrásra való hivatkozáskor. Az alábbi példa az egyszerűsített szintaxist szemlélteti:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Ha a sablonban más olyan érték van beállítva, amely nyilvános névtér használatára van konfigurálva, módosítsa ezeket az értékeket úgy, hogy az ugyanazt a **hivatkozási** függvényt tükrözze. Beállíthatja például a virtuális gép diagnosztikai profiljának **storageUri** tulajdonságát:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Hivatkozhat egy másik erőforráscsoporthoz tartozó meglévő Storage-fiókra is:

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Csak akkor rendeljen nyilvános IP-címeket a virtuális géphez, ha egy alkalmazáshoz szükség van. A virtuális géphez (VM) való kapcsolódáshoz, illetve felügyeleti vagy felügyeleti célból a bejövő NAT-szabályokat, a virtuális hálózati átjárókat vagy a Jumpbox használhatja.
   
     A virtuális gépekhez való csatlakozásról további információt a következő témakörben talál:
   
   * [Virtuális gépek futtatása N szintű architektúrához az Azure-ban](../guidance/guidance-compute-n-tier-vm.md)
   * [WinRM-hozzáférés beállítása virtuális gépekhez Azure Resource Manager](../virtual-machines/windows/winrm.md)
   * [Külső hozzáférés engedélyezése a virtuális géphez a Azure Portal használatával](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Külső hozzáférés engedélyezése a virtuális géphez a PowerShell használatával](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Külső hozzáférés engedélyezése linuxos virtuális géphez az Azure CLI használatával](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* A nyilvános IP-címek **domainnamelabel értékkel** tulajdonságának egyedinek kell lennie. A **domainnamelabel értékkel** értékének 3 és 63 karakter közöttinek kell lennie, és követnie kell az ebben a reguláris `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`kifejezésben megadott szabályokat:. Mivel a **uniqueString** függvény 13 karakter hosszúságú karakterláncot hoz létre, a **dnsPrefixString** paraméter 50 karakterre van korlátozva:

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* Amikor jelszót ad hozzá egy egyéni parancsfájl-bővítményhez, használja a **commandToExecute** tulajdonságot a **protectedsettingsfromkeyvault** tulajdonságban:
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > Annak biztosítása érdekében, hogy a titkos kódok titkosítva legyenek a virtuális gépek és bővítmények paraméterként való átadásakor, használja a megfelelő bővítmények **protectedsettingsfromkeyvault** tulajdonságát.
   > 
   > 

## <a name="outputs"></a>Kimenetek

Ha a nyilvános IP-címek létrehozásához sablont használ, adjon meg egy [kimenet szakaszt](resource-group-authoring-templates.md#outputs) , amely az IP-cím és a teljes tartománynév (FQDN) adatait adja vissza. Nyilvános IP-címek és teljes tartománynevek könnyen hozzáférhet az üzembe helyezést követően a kimeneti értékeket is használhat.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="next-steps"></a>További lépések

* További információ a Resource Manager-sablonfájl struktúrájáról: [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](resource-group-authoring-templates.md).
* Az összes Azure-beli felhőalapú környezetben működő sablonok létrehozásával kapcsolatos javaslatokért lásd: [Azure Resource Manager-sablonok fejlesztése a Felhőbeli konzisztencia](templates-cloud-consistency.md)érdekében.
