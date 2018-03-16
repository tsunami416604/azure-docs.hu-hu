---
title: "Az Azure Resource Manager sablon struktúra és szintaxis |} Microsoft Docs"
description: "A struktúra és az Azure Resource Manager-sablonok deklaratív JSON-szintaxis használatával tulajdonságait ismerteti."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2017
ms.author: tomfitz
ms.openlocfilehash: b5438080f71fa8f5c4f03006b75b826f1cfa576a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="resources-section-of-azure-resource-manager-templates"></a>Azure Resource Manager-sablonok források szakasza

Erőforrások területen adja meg az erőforrások telepítése vagy frissítése. Ez a szakasz kérheti le bonyolult, mert ismernie kell a típusok esetében helyez üzembe adja meg a megfelelő értékeket.

## <a name="available-properties"></a>Rendelkezésre álló tulajdonságok

Meghatározhatja az erőforrások az alábbi szerkezettel:

```json
"resources": [
  {
      "condition": "<boolean-value-whether-to-deploy>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": "<number-of-iterations>",
          "mode": "<serial-or-parallel>",
          "batchSize": "<number-to-deploy-serially>"
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Elem neve | Szükséges | Leírás |
|:--- |:--- |:--- |
| feltétel | Nem | Logikai érték, amely azt jelzi, hogy telepítve van-e az erőforrás. |
| apiVersion |Igen |Az erőforrás létrehozásához használt a REST API verziója. |
| type |Igen |Az erőforrás típusát. Ezt az értéket az erőforrás-szolgáltató és az erőforrástípus névtere kombinációja (például **Microsoft.Storage/storageAccounts**). |
| név |Igen |Az erőforrás nevét. A név URI összetevő korlátozások RFC3986 definiált kell követnie. Emellett Azure-szolgáltatások elérhetővé tenni az erőforrásnév kívül felek ellenőrzése a nevét, győződjön meg arról, hogy nincs egy másik identitás hamisításának kísérlet. |
| location |Változó |Támogatja a megadott erőforráscsoport földrajzi elhelyezkedését. Kiválaszthatja a rendelkezésre álló helyeken, de általában érdemes válasszon egyet, amelynek mérete megközelítőleg a felhasználók. Általában is érdemes helyezendő erőforrásokat, amelyek ugyanabban a régióban kapcsolatba egymással. A legtöbb erőforrás szükséges egy helyre, de néhány típust (például egy szerepkör-hozzárendelés) igényel egy helyre. |
| tags |Nem |Az erőforrás társított címkékkel. Címkékkel hogy logikusan rendszerezhesse az erőforrások az előfizetésből. |
| Megjegyzések |Nem |A Megjegyzések a a sablonban lévő erőforrások dokumentálása |
| másolás |Nem |Ha egynél több példány van szükség, az olyan erőforrások száma létrehozásához. Az alapértelmezett mód párhuzamos. Adja meg a soros módban, ha nem szeretné, hogy az összes vagy egy időben üzembe helyezendő erőforrásokat. További információkért lásd: [erőforrások több példánya létrehozása az Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Nem |Ehhez az erőforráshoz központi telepítése előtt telepíteni kell erőforrások. Erőforrás-kezelő kiértékeli az erőforrások közti függőségeket, és telepíti azokat a megfelelő sorrendben. Ha nincsenek függő erőforrások, párhuzamos központi telepítés. Az érték lehet egy vesszővel elválasztott lista erőforrás nevét vagy egyedi erőforrás-azonosítók. Ez a sablon üzembe helyezett erőforrások csak felsorolása Erőforrások, amelyek nincsenek meghatározva a sablonban már léteznie kell. Kerülje a szükségtelen függőségek hozzáadásával még a központi telepítés lassú, és hozzon létre körkörös függőségi viszony. A beállítás függőségek útmutatást lásd: [függőségek meghatározása az Azure Resource Manager-sablonok](resource-group-define-dependencies.md). |
| properties |Nem |Erőforrás-specifikus konfigurációs beállításokat. A tulajdonságok értékeit ugyanazok, mint a REST API művelet (PUT metódust) létrehozni az erőforrást a kérés törzsében meg az értékeket. Egy tulajdonság több példányát létrehozni egy másolatot tömb is megadható. |
| erőforrás |Nem |A múltbeli erőforrástól függő gyermekszintű erőforrása. Csak olyan típusú erőforrások a szülő erőforrás sémája által számukra engedélyezett. A gyermek-erőforrás teljesen minősített típusú tartalmaz szülő erőforrástípusra, például **Microsoft.Web/sites/extensions**. A szülő erőforrás függőség nem utal. Függőséget explicit módon meg kell adni. |

## <a name="resource-specific-values"></a>Erőforrás-specifikus értékeket

A **apiVersion**, **típus**, és **tulajdonságok** minden erőforrás típusa eltérő. Ezekhez a tulajdonságokhoz tartozó értékek meghatározásáról, lásd: [sablonra való hivatkozást](/azure/templates/).

## <a name="resource-names"></a>Erőforrások neve
Általában három típusú erőforrásnevek a Resource Manager használata:

* Erőforrás neve, amely egyedinek kell lennie.
* Erőforrás neve, amely nem kell egyedinek lennie, de válassza ki, amelyek segítenek azonosítani azokat az erőforrás nevének.
* Erőforrás neve, amely lehet általános.

### <a name="unique-resource-names"></a>Egyedi erőforrás neve
Egy adat-hozzáférési végponttal rendelkezik erőforrás típussal egyedi erőforrás nevét kell megadnia. Néhány gyakori erőforrástípusok esetében, amelyek egyedi nevet kell megadni a következők:

* Azure Storage<sup>1</sup> 
* Web Apps funkció az Azure App Service-ben
* SQL Server
* Azure Key Vault
* Azure Redis Cache
* Azure Batch
* Azure Traffic Manager
* Azure Search
* Azure HDInsight

<sup>1</sup> tárfiókneveket is kisbetűnek kell lennie, 24 karakter vagy kevesebb, és nem rendelkezik a kötőjel.

A név megadásakor, manuálisan hozzon létre egy egyedi nevet, vagy használja a [uniqueString()](resource-group-template-functions-string.md#uniquestring) nevet generálni az adott függvényt. Érdemes azt is egy előtagot, vagy hogy utótag a **uniqueString** eredménye. Az egyedi név módosítása segítségével további könnyen azonosíthatja az erőforrás típusa a neve. Például egy egyedi nevet a tárfiók hozhat létre a következő változó használatával:

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>Az azonosításhoz erőforrásnevek
Egyes erőforrástípusok esetében érdemes nevét, de a nevek nem rendelkeznek egyedinek kell lennie. Ezen erőforrás esetében adja meg a nevet, amely azonosítja az erőforrás-környezetben, mind az erőforrástípus.

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

### <a name="generic-resource-names"></a>Általános erőforrás neve
Erőforrás esetében, amelyek többnyire keresztül érhető el egy másik erőforráscsoportban használhatja az általános neve nem változtatható a sablonban. Például beállíthatja egy szabványos, általános nevet tűzfalszabályok SQL-kiszolgálón:

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="location"></a>Hely
Ha a sablonok telepítésével, meg kell adnia az egyes erőforrások helyét. Különböző típusú támogatott különböző helyeken. Az adott erőforrástípus előfizetés számára elérhető helyek listáját megjelenítéséhez használja az Azure PowerShell vagy az Azure parancssori felület. 

Az alábbi példában PowerShell a helyek az beszerzése a `Microsoft.Web\sites` erőforrás típusa:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Az alábbi példában Azure CLI 2.0 a helyek az beszerzése a `Microsoft.Web\sites` erőforrás típusa:

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

Után annak meghatározásához, hogy az erőforrások támogatott helyek, a sablonban beállított helyről. Állítsa ezt az értéket a legegyszerűbb módja, amely támogatja a erőforrástípusok helyen hozzon létre egy erőforráscsoportot, és mindegyik helyen `[resourceGroup().location]`. A sablon különböző helyeken való újbóli, és nem módosítható a sablonból vagy a paraméterek az értékeket. 

A következő példa bemutatja, amelyek az erőforráscsoportot és ugyanazon a helyen a rendszer:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

Ha szeretné kódba foglalni a hely a sablonban, adja meg a támogatott régiók egyikéhez sem nevét. A következő példa bemutatja egy tárfiókot, északi középső Régiójában mindig központilag telepített:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="tags"></a>Címkék
[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

### <a name="add-tags-to-your-template"></a>Címkék hozzáadása a sablon

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="child-resources"></a>Gyermek-erőforrások

Egyes erőforrástípusok belül is meghatározhat gyermekerőforrásait tömbjét. Gyermek erőforrások olyan erőforrások, csak egy másik erőforrás keretén belül található. Például egy SQL-adatbázis nem létezhet egy SQL server nélkül így az adatbázis egy alárendelt kiszolgáló. Megadhatja, hogy az adatbázis, a kiszolgáló meghatározása.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

Amikor beágyazott, a típusuk értéke `databases` , de a teljes erőforrás típusa `Microsoft.Sql/servers/databases`. Nincs megadva `Microsoft.Sql/servers/` mivel feltételezzük, hogy a szülő erőforrás típusból. A gyermek-erőforrás neve legyen `exampledatabase` , de a teljes nevet tartalmazza a szülő nevének. Nincs megadva `exampleserver` mivel feltételezzük, hogy a szülő erőforrás.

A gyermek erőforrástípus formátuma: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

A gyermek-erőforrás neve formátuma: `{parent-resource-name}/{child-resource-name}`

De azt nem kell meghatároznia az adatbázis-kiszolgálón belül. Megadhatja, hogy a gyermek-erőforrás legfelső szintjén. Előfordulhat, hogy ezt a módszert használja, ha az a szülő erőforrás nem ugyanazt a sablont, vagy ha szeretné használni `copy` több gyermek-erőforrás létrehozása. Ezt a módszert használja adja meg a teljes erőforrás típusát, és a szülő erőforrás neve tartalmazza a gyermek-erőforrás neve.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

Amikor egy erőforrást egy teljesen minősített hivatkozást hozhat létre, típusa és neve a szegmensek egyesítése sorrendje nem egyszerűen a két összefűzése.  A névtér után inkább sorozatát *típusnév/* a legjobban megfelel a legkevésbé egyedi párok:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Példa:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` megfelelő `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` helytelen

## <a name="recommendations"></a>Javaslatok
A következő információkat az erőforrásokkal való munka során lehet hasznos:

* Adjon meg más közreműködők a erőforrás megismerése érdekében **megjegyzések** a sablonban az egyes erőforrások:
   
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

* Ha egy *nyilvános végpontot* a sablonban (például egy Azure Blob storage nyilvános végpontot), *do nem rögzített kód* a névtér. Használja a **hivatkozás** függvény dinamikusan beolvasni a névteret. A sablon telepítéséhez a különböző nyilvános névtér-környezetekben a végpont a sablonban manuális módosítása nélkül használhatja ezt a módszert használja. Állítsa be az API-verzió Ön a sablon a tárfiók által használt verziójával megegyező verzióra:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Ha ugyanazt a sablont hoz létre a tárfiók van telepítve, nem kell adja meg a szolgáltató névterének neve, amikor az erőforrás hivatkozik. A következő példa bemutatja a egyszerűsített szintaxist:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Ha a sablont más értékek, amelyek egy nyilvános névtér használatára van konfigurálva, ezek helyett megfelelően azonos **hivatkozás** függvény. Például beállíthatja a **storageUri** a virtuális gép diagnosztikai profiljának tulajdonsága:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Meglévő tárfiókot, amely egy másik erőforráscsoportban található is hivatkozhat:

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Nyilvános IP-címek hozzárendelése a virtuális gép csak akkor, ha egy alkalmazás írja elő. A virtuális gép (VM) hibakereséshez vagy felügyeleti vagy felügyeleti célokra való kapcsolódáshoz használja a bejövő NAT-szabályok, a virtuális hálózati átjáró vagy egy jumpbox.
   
     Virtuális gépekhez való csatlakozás kapcsolatos további információkért lásd:
   
   * [Futtassa a virtuális gépek Azure-ban N szintű architektúrája](../guidance/guidance-compute-n-tier-vm.md)
   * [A WinRM-hozzáférés beállítása az Azure Resource Manager virtuális gépekhez](../virtual-machines/windows/winrm.md)
   * [A virtuális gép külső hozzáférés engedélyezése az Azure portál használatával](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [A virtuális gép külső hozzáférés engedélyezése a PowerShell használatával](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [A Linux virtuális gép külső hozzáférés engedélyezése az Azure parancssori felület használatával](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)
* A **domainNameLabel** nyilvános IP-címekhez tulajdonságnak egyedinek kell lennie. A **domainNameLabel** érték 3 és 63 karakter között kell, és kövesse a reguláris kifejezés által meghatározott szabályok: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Mivel a **uniqueString** függvény karakterláncot hoz létre, amely 13 karakterből áll, a **dnsPrefixString** paraméter értéke legfeljebb 50 karakter hosszú lehet:

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

* Ha jelszót ad hozzá egy egyéni parancsprogramok futtatására szolgáló bővítmény, használja a **commandToExecute** tulajdonságot a **protectedSettings** tulajdonság:
   
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
   > Győződjön meg arról, hogy titkos kulcsok titkosítását, ha azok paraméterként virtuális gépek és bővítmények, használja a **protectedSettings** tulajdonsága a megfelelő bővítményeket.
   > 
   > 


## <a name="next-steps"></a>További lépések
* A különböző megoldástípusokhoz használható teljes sablonok megtekintéséhez lásd: [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/).
* A sablonon belül használhatja a functions szolgáltatással kapcsolatos részletekért lásd: [Azure Resource Manager Sablonfüggvényei](resource-group-template-functions.md).
* Egyesítenie több sablon üzembe helyezése során, lásd: [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).
* Szükség lehet egy másik erőforráscsoportban található erőforrások használatával. Ez a forgatókönyv nem közös, ha a storage-fiókok vagy több erőforrás csoporttal megosztott virtuális hálózatok. További információkért lásd: a [resourceId függvény](resource-group-template-functions-resource.md#resourceid).
* Erőforrás neve vonatkozó megkötésekkel kapcsolatos további információkért lásd: [Azure-erőforrások elnevezési szabályai ajánlott](../guidance/guidance-naming-conventions.md).