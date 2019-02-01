---
title: Ajánlott eljárások az Azure Resource Manager-sablonok
description: Ismerteti az ajánlott megközelítéseket, az Azure Resource Manager-sablonok készítése. Javaslatot tesz a sablonok használatával kapcsolatos gyakori problémák elkerülése érdekében.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: tomfitz
ms.openlocfilehash: 9b136c73afc08e05694aed99d57139f77466788d
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490379"
---
# <a name="azure-resource-manager-template-best-practices"></a>Ajánlott eljárások az Azure Resource Manager sablon

Ez a cikk a Resource Manager-sablon létrehozására vonatkozó javaslatokat nyújt. Ezekkel az ajánlásokkal segítséget nyújt a gyakori problémák elkerülése érdekében, amikor a megoldás üzembe helyezése sablon használatával.

Annak a szabályozására, az Azure-előfizetésekre vonatkozó javaslatokról, lásd: [Azure enterprise scaffold: Előíró előfizetés-irányítás](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json).

Javaslatok az összes Azure-felhő környezetben működő sablonok létrehozásáról, lásd: [felhőalapú konzisztencia fejlesztése az Azure Resource Manager-sablonokkal](templates-cloud-consistency.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="parameters"></a>Paraméterek
Ebben a szakaszban található információk hasznos lehet, ha [paraméterek](resource-manager-templates-parameters.md).

### <a name="general-recommendations-for-parameters"></a>Általános javaslatok paraméterek

* Minimalizálja a paraméterek használatát. Ehelyett használjon változók vagy konstans értékeket azokhoz a tulajdonságokhoz, nem szükséges központi telepítése során adható meg.

* Nagybetűs használata a paraméterek nevei.

* Használja a paramétereket a beállításokat, amelyek a környezetet, például a Termékváltozat, a méretét vagy a kapacitás függően változnak.

* Egyszerű azonosító megadása kívánt erőforrásnevek paramétereket használja.

* Adja meg a metaadatokban minden paraméter leírása:

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

* Adja meg, amelyek nem érzékeny paraméterek alapértelmezett értékeit. Adjon meg egy alapértelmezett értéket, könnyebben helyezheti üzembe a sablont, és a felhasználók a sablon meg egy példát egy megfelelő értéket. Bármilyen egy paraméter alapértelmezett értéke az összes felhasználó számára az alapértelmezett központi telepítési beállítás érvényesnek kell lennie. 
   
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

* Adjon meg egy nem kötelező paraméter, hogy ne használjon egy üres karakterlánc alapértelmezés szerint. Ehelyett használjon Szövegkonstansérték vagy Sablonnyelv-kifejezés érték létrehozásához.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* Egy paraméter nem használható az API-verzió, az erőforrástípushoz. Erőforrás-tulajdonságok és értékek verziószám alapján változhat. A Kódszerkesztő az IntelliSense nem állapítható meg a megfelelő sémát, amikor az API-version paraméter értéke. Ehelyett a sablonban rögzítse szoftveresen az API-verzió.

* Használat `allowedValues` megtörhetik a cikk folytonosságát. Csak akkor használja, ha meg kell győződnie arról, hogy egyes értékek nem szerepelnek a az engedélyezett beállítások. Ha `allowedValues` túl széles körben blokkolhatja érvényes központi telepítések nem tartja a listája naprakész.

* A sablonban a paraméter neve megegyezik a PowerShell telepítési parancs egy paramétert, Resource Manager az utótag hozzáadásával az elnevezési ütközés feloldása **FromTemplate** sablon paraméteréhez. Például, ha nevű paraméter adja meg az **ResourceGroupName** a sablonban ütközik a **ResourceGroupName** paramétert a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) parancsmagot. Üzembe helyezés során az kéri, hogy adjon meg egy értéket a **ResourceGroupNameFromTemplate**.

### <a name="security-recommendations-for-parameters"></a>Biztonsági javaslatok paraméterek

* Paraméterek mindig használja a felhasználónevek és jelszavak (vagy titkos adatokat).

* Használat `securestring` jelszavak és a titkos kulcsok. Ha a bizalmas adatokat egy JSON-objektumot adja át, a `secureObject` típusa. A biztonságos karakterláncot vagy biztonságos objektumtípusok Sablonparaméterek erőforrás üzembe helyezés után nem lehet olvasni. 
   
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

* Alapértelmezett értékek felhasználónevek, jelszavak vagy igénylő értéket nem ad meg egy `secureString` típusa.

* Nem adja meg, amelyek növelik a támadási felület az alkalmazás tulajdonságok alapértelmezett értékeit.

### <a name="location-recommendations-for-parameters"></a>Paraméterek helye javaslatok

* Egy paraméter használatával adja meg az erőforrások helyét, és adja meg az alapértelmezett értéket `resourceGroup().location`. Egy hely paraméter megadása lehetővé teszi a felhasználók a sablon számára adjon meg egy helyet, amely üzembe engedéllyel rendelkeznek.

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

* Ne adjon meg `allowedValues` a hely paraméter számára. A megadott helyeken nem érhető el az összes felhő.

* A hely paraméter értékét használja erőforrásokhoz tartozó, valószínű, hogy ugyanazon a helyen. Ez a megközelítés minimálisra csökkenti a helyre vonatkozó adatokat adja meg a rendszer kéri a felhasználóktól számát.

* Az olyan erőforrásokhoz, amelyek nem érhetők el minden helyen külön paraméterrel használja, vagy szövegkonstans hely értéket adjon meg.

## <a name="variables"></a>Változók

A következő információ hasznos lehet a végzett munka során [változók](resource-manager-templates-variables.md):

* Változók használata a sablonban egynél többször használatához szükséges értékeket. Ha az érték csak egyszer legyen használva, a kódolt érték a sablon olvashatóbbá teszi.

* Az értékeket, amelyeket a sablonokban használható függvények összetett elhelyezkedését hozhat létre változókat használni. A sablon rendkívül könnyebben olvasható, ha az összetett kifejezések csak akkor jelenik meg a változókat.

* A változók ne használjon `apiVersion` erőforrás. Az API-verziót az erőforrás-séma határozza meg. Gyakran előfordul a verzió nem módosítható az erőforrás tulajdonságainak módosítása nélkül.

* Nem használhatja a [referencia](resource-group-template-functions-resource.md#reference) működni a **változók** a sablon szakaszában. A **referencia** funkció használatából az értékét az erőforrás futási idejű állapota. Azonban változók elhárulnak a sablon kezdeti elemzése során. Szerkezet értékek ennek az igényét a **referencia** függvényt közvetlenül a **erőforrások** vagy **kimenete** szakaszában a sablont.

* Például a változókat az erőforrás nevének egyedinek kell lennie.

* Használja a [változók a másolási ciklust](resource-group-create-multiple.md#variable-iteration) hozhat létre egy JSON-objektumok ismétlődő minta.

* Távolítsa el a nem használt változókat.

## <a name="resource-dependencies"></a>Erőforrás-függőségek

Amikor eldönti, milyen [függőségek](resource-group-define-dependencies.md) beállításához használja az alábbi irányelveket:

* Használja a **referencia** funkciót, és adja át az erőforrás nevét kell megosztani egy tulajdonság-erőforrások közötti egy implicit függőség beállításához. Ne adjon hozzá egy explicit `dependsOn` elem, amikor már definiált az implicit függ. Ez a megközelítés csökkenti a szükségtelen függőségek kockázatát.

* A gyermek-erőforrás beállítása a szülő erőforrástól függ-e.

* Az erőforrások a [feltétel elem](resource-manager-templates-resources.md#condition) hamis értékre van állítva a függőségi sorrend automatikusan törlődnek. Állítsa be a függőségeket, ha az erőforrás minden esetben üzembe van helyezve.

* Az eszközkorlátozásokkal függőségek cascade anélkül, hogy explicit módon állítja be őket. Például a virtuális gép virtuális hálózati adapter függ, és a virtuális hálózati adaptert egy virtuális hálózat és a nyilvános IP-címek függ. Ezért a virtuális gépet üzembe helyezett összes három erőforrást, de nincs explicit módon állítja be a virtuális gép összes három erőforrástól függ. Ez a megközelítés a függőségi sorrend tisztázza, és megkönnyíti a sablon később módosíthatja.

* Ha telepítés előtt értéket lehet meghatározni, próbálja meg az erőforrás függőség nélküli telepítése. Például ha egy konfigurációs értéket kell egy másik erőforrás nevét, nincs szüksége lehet egy függőségi. Ez az útmutató mindig nem működik, mert egyes erőforrások ellenőrizze a többi erőforrás létezik-e. Ha hibaüzenetet kap, adjon hozzá egy függőséget.

## <a name="resources"></a>További források

A következő információ hasznos lehet a végzett munka során [erőforrások](resource-manager-templates-resources.md):

* Adjon meg más közreműködőkkel az erőforrás rendeltetésének megismerése érdekében **megjegyzések** az egyes erőforrások a sablonban:
   
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

* Ha egy *nyilvános végpontot* (például egy Azure Blob storage nyilvános végpont), a sablonban *ne rögzítse szoftveresen* a névteret. Használja a **referencia** függvény dinamikusan beolvasni a névteret. Ez a módszer használatával a végpont a sablonban manuális módosítása nélkül helyezheti üzembe a sablont a különböző nyilvános névtér-környezetekben. Az API-verzió beállítása ugyanarra a verzióra, amely a storage-fiókot használja a sablonban:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Ha ugyanazt a sablont hoz létre a storage-fiók van telepítve, nem kell a szolgáltatói névtér adja meg, ha az erőforrás hivatkozik. Az alábbi példa bemutatja az egyszerűsített Szintaxis:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Ha más nyilvános névtér használatára beállított értékeket a sablonban, módosítsa ezeket az értékeket, hogy azonos **referencia** függvény. Például beállíthatja a **storageUri** a virtuális gép diagnosztikai profiljának tulajdonságát:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Egy meglévő tárfiókot, amely egy másik erőforráscsoportban található is lehet hivatkozni:

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Nyilvános IP-címek hozzárendelése a virtuális gép csak akkor, ha egy alkalmazás írja elő. Ha csatlakozni szeretne egy virtuális gépet (VM) a hibakereséshez, vagy a felügyeleti vagy felügyeleti célokra, használja a bejövő NAT-szabályokat, a virtuális hálózati átjáró vagy a jumpbox.
   
     Virtuális gépekhez való csatlakozás kapcsolatos további információkért lásd:
   
   * [Virtuális gépek futtatása egy N szintű architektúrához az Azure-ban](../guidance/guidance-compute-n-tier-vm.md)
   * [A WinRM-elérés beállítása virtuális gépekhez az Azure Resource Manager](../virtual-machines/windows/winrm.md)
   * [A virtuális gép külső hozzáférés engedélyezése az Azure portal használatával](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [A virtuális gép külső hozzáférés engedélyezése a PowerShell használatával](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [A Linux rendszerű virtuális gép külső hozzáférés engedélyezése az Azure CLI-vel](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* A **domainNameLabel** nyilvános IP-címek tulajdonsága egyedinek kell lennie. A **domainNameLabel** értéket kell csak 3 és 63 karakter közötti lehet, és kövesse a reguláris kifejezés által meghatározott szabályok: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Mivel a **uniqueString** függvény létrehoz egy karakterlánc, amely 13 karakterből, a **dnsPrefixString** paraméter értéke legfeljebb 50 karakter hosszúságú lehet:

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

* Amikor jelszót ad hozzá egy egyéni szkriptek futtatására szolgáló bővítmény, használja a **commandToExecute** tulajdonságot a **protectedSettings** tulajdonság:
   
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
   > Győződjön meg arról, hogy a titkok titkosítását, ha a azok paraméterként a virtuális gépek és a bővítményeket, használja a **protectedSettings** tulajdonságát a megfelelő bővítményeket.
   > 
   > 

## <a name="outputs"></a>Kimenetek

Ha egy sablon használatával hozzon létre nyilvános IP-címek,- [szakasz kimenete](resource-manager-templates-outputs.md) , amely az IP-cím és a teljesen minősített tartománynevét (FQDN) adatait adja vissza. Nyilvános IP-címek és teljes tartománynevek könnyen hozzáférhet az üzembe helyezést követően a kimeneti értékeket is használhat.

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

* A Resource Manager-sablonfájl szerkezete kapcsolatos információkért lásd: [struktúra és az Azure Resource Manager-sablonok szintaxisát](resource-group-authoring-templates.md).
* Javaslatok az összes Azure-felhő környezetben működő sablonok létrehozásáról, lásd: [felhőalapú konzisztencia fejlesztése az Azure Resource Manager-sablonokkal](templates-cloud-consistency.md).
