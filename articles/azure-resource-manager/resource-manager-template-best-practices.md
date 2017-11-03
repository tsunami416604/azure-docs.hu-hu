---
title: "Ajánlott eljárások a Resource Manager-sablonok létrehozásához |} Microsoft Docs"
description: "Az Azure Resource Manager-sablonok egyszerűsítésére irányelveket."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 31b10deb-0183-47ce-a5ba-6d0ff2ae8ab3
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: tomfitz
ms.openlocfilehash: a23301ba88279af3f7bf4d353ae808e9eeb0900d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="best-practices-for-creating-azure-resource-manager-templates"></a>Ajánlott eljárások az Azure Resource Manager-sablonok létrehozása
Iránymutatás segítséget nyújtanak az Azure Resource Manager-sablonok, amelyek könnyen használható és megbízható létrehozásához. Az útmutató csak javaslatok. Nincsenek követelmények, kivéve, ha az áttelepítés előtt feljegyzett. A forgatókönyv a következő módszerekkel és példák az egyik egy változata lehet szükség.

## <a name="resource-names"></a>Erőforrások neve
Általában három típusú erőforrásnevek a Resource Manager használata:

* Erőforrás neve, amely egyedinek kell lennie.
* Erőforrás neve, amely nem kell egyedinek lennie, de válassza ki, amelyek segítenek azonosítani azokat a környezet alapján erőforrás nevének.
* Erőforrás neve, amely lehet általános.

 Erőforrás neve vonatkozó megkötésekkel kapcsolatos további információkért lásd: [Azure-erőforrások elnevezési szabályai ajánlott](../guidance/guidance-naming-conventions.md).

### <a name="unique-resource-names"></a>Egyedi erőforrás neve
Egy adat-hozzáférési végponttal rendelkezik erőforrás típussal egyedi erőforrás nevét kell megadnia. Néhány gyakori erőforrástípusok esetében, amelyek egyedi nevet kell megadni a következők:

* Az Azure Storage<sup>1</sup> 
* Web Apps funkció az Azure App Service-ben
* SQL Server
* Azure Key Vault
* Azure Redis Cache
* Azure Batch
* Azure Traffic Manager
* Azure Search
* Az Azure HDInsight

<sup>1</sup> tárfiókneveket is kisbetűnek kell lennie, 24 karakter vagy kevesebb, és nem rendelkezik a kötőjel.

Egy paramétert az erőforrás nevét adja meg, ha az erőforrás telepítésekor meg kell adnia egy egyedi nevet. Másik lehetőségként létrehozhat egy változó, amely használja a [uniqueString()](resource-group-template-functions-string.md#uniquestring) nevet generálni az adott függvényt. 

Érdemes azt is egy előtagot, vagy hogy utótag a **uniqueString** eredménye. Az egyedi név módosítása segítségével további könnyen azonosíthatja az erőforrás típusa a neve. Például egy egyedi nevet a tárfiók hozhat létre a következő változó használatával:

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>Az azonosításhoz erőforrásnevek
Egyes erőforrástípusok esetében érdemes nevét, de a nevek nem rendelkeznek egyedinek kell lennie. Ezen erőforrás esetében adja meg a nevet, amely azonosítja az erőforrás-környezetben, mind az erőforrástípus. Adjon meg egy leíró nevet, hogy könnyebb legyen azonosítani az erőforrás tartozó erőforrások listáját. Ha szeretné használni a különböző központi telepítésénél a különböző erőforrás nevét, egy paraméter használható nevét:

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

Ha nem kell egy nevet a telepítés során adhat, egy változót is használhatja: 

```json
"variables": {
    "vmName": "demoLinuxVM"
}
```

A kódolt érték is használható:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "demoLinuxVM",
  ...
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

## <a name="parameters"></a>Paraméterek
Az alábbi információ segítségével esetleg megállapítható, paraméterek használata:

* Minimalizálása érdekében a paraméterek használatával. Amikor csak lehetséges, használjon egy változó vagy konstansérték. Paraméterek csak ezek helyzetekben használhatja:
   
   * Környezet (SKU, méret, kapacitás) megfelelően változatait használni kívánt beállításokat.
   * Erőforrás neve, amely könnyebbé teszi a beazonosítást szeretne megadni.
   * (Például egy rendszergazda felhasználóneve) más feladatok elvégzéséhez gyakran használt értékek.
   * A titkos kulcsokat (például a jelszavak).
   * A szám vagy tömb erőforrástípus több példánya létrehozásakor használandó.
* -És nagybetűhasználattal teve a paraméterek nevei.
* A metaadatokban minden paraméter leírását adhatja meg:

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

* Adja meg (kivéve a jelszavak és SSH-kulcsok) paraméterek alapértelmezett értéke:
   
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

* Használjon **SecureString** a jelszavak és a titkos kulcsok: 
   
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

* Amikor csak lehetséges, nem egy paraméter segítségével adjon meg helyet. Ehelyett használja a **hely** az erőforráscsoport tulajdonság. Használatával a **resourceGroup () .location** megadó kifejezést vár, a erőforrások a sablonban szereplő erőforrások vannak telepítve az erőforráscsoport és ugyanazon a helyen:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         ...
     }
   ]
   ```
   
   Ha egy erőforrás típusa támogatott helyek csak korlátozott számú, érdemes adjon meg egy érvényes helyet közvetlenül a sablonban. Használata esetén egy **hely** paraméter, a lehető legnagyobb mértékben paraméterérték megosztása erőforrásokat, amelyek valószínűleg ugyanazon a helyen. Ez minimalizálja a szám, ahányszor a felhasználó felkérést kap arra, hogy helyére vonatkozó információkat.
* Kerülje a paraméter vagy változó erőforrástípus API-verzió számára. Erőforrás-tulajdonságok és értékek alapján verziószáma változhat. A kód szerkesztése az IntelliSense nem határozható meg, hogy a megfelelő sémát az API-verzió beállítása egy paraméter vagy változó. Ehelyett a sablonban merevlemez-kódot az API-verzió.

## <a name="variables"></a>Változók
Az alábbi információ segítségével esetleg megállapítható, változók használata:

* Változók használata sablonban egynél többször használni kívánt értékeket. Ha az érték csak egyszer legyen használva, kódolt értéket a sablon olvashatóbbá teszi.
* Nem használhatja a [hivatkozás](resource-group-template-functions-resource.md#reference) működni a **változók** a sablon szakasza. A **hivatkozás** függvény az értékét az erőforrás futásidejű állapot osztályból származik. Azonban változók, megtörténik a sablon kezdeti elemzése során. Szerkezet értékei, amelyen a kell a **hivatkozás** működéséhez közvetlenül a **erőforrások** vagy **kimenete** a sablon szakasza.
* Az erőforrás nevét, amely egyedinek kell lennie, a változókat tartalmazhat [erőforrásnevek](#resource-names).
* Változók összetett objektumokba csoportosíthatja. Használja a **variable.subentry** való hivatkozáshoz egy összetett objektum érték formátuma. Változók segítségével nyomon követheti a kapcsolódó változók. Ez növeli a sablon olvashatóság érdekében is. Íme egy példa:
   
   ```json
   "variables": {
       "storage": {
           "name": "[concat(uniqueString(resourceGroup().id),'storage')]",
           "type": "Standard_LRS"
       }
   },
   "resources": [
     {
         "type": "Microsoft.Storage/storageAccounts",
         "name": "[variables('storage').name]",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "sku": {
             "name": "[variables('storage').type]"
         },
         ...
     }
   ]
   ```
   
   > [!NOTE]
   > Egy összetett objektum nem tartalmazhat egy kifejezés, amely egy összetett objektumot egy értéket a hivatkozásokat. Külön változó erre a célra.
   > 
   > 
   
     Speciális összetett objektumok használatával változókként, tekintse meg a [megosztani az Azure Resource Manager sablonokban állapot](best-practices-resource-manager-state.md).

## <a name="resources"></a>Erőforrások
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

* Címkék segítségével erőforrásokat ad hozzá metaadatokat. Metaadatok használatával adhatja hozzá az erőforrások adatait. Például metaadatokra ahhoz, hogy az erőforrás számlázási adatát is hozzáadhat. További információkért lásd: [az Azure-erőforrások rendszerezése címkék használatával](resource-group-using-tags.md).
* Ha egy *nyilvános végpontot* a sablonban (például egy Azure Blob storage nyilvános végpontot), *do nem rögzített kód* a névtér. Használja a **hivatkozás** függvény dinamikusan beolvasni a névteret. A sablon telepítéséhez a különböző nyilvános névtér-környezetekben a végpont a sablonban manuális módosítása nélkül használhatja ezt a módszert használja. Állítsa be az API-verzió Ön a sablon a tárfiók által használt verziójával megegyező verzióra:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Ha ugyanazt a sablont hoz létre a tárfiók van telepítve, nem kell adja meg a szolgáltató névterének neve, amikor az erőforrás hivatkozik. Ez az a egyszerűsített szintaxist:
   
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

## <a name="outputs"></a>kimenetek
Egy sablon használatával nyilvános IP-címek létrehozása, ha egy **kimenete** szakaszt, amely az IP-cím és a teljesen minősített tartománynevét (FQDN) adatait adja vissza. Egyszerűen beolvashatók a telepítést követően nyilvános IP-címek és teljes tartománynevek kimeneti értékeket is használhat. Ha az erőforrás hivatkozik, használja a létrehozásához használt API-verzió: 

```json
"outputs": {
    "fqdn": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').ipAddress]",
        "type": "string"
    }
}
```

## <a name="single-template-vs-nested-templates"></a>Egy sablon és a beágyazott sablonok
A megoldás üzembe helyezéséhez, használhatja ugyanazt a sablont, vagy egy fő sablont több beágyazott sablonokkal. Beágyazott sablonok olyan közös speciális forgatókönyvekhez. Beágyazott sablon használatával lehetővé teszi a következő előnyökkel jár:

* A célként megadott összetevők megoldás is lebontva.
* Eltérő fő sablonok beágyazott sablonok is felhasználhatja.

Ha a beágyazott sablonok használata mellett dönt, a következő iránymutatás segítséget nyújt a sablon tervezési szabványosítására. Ezeket az irányelveket alapuló [tervezése során az Azure Resource Manager-sablonok minták](best-practices-resource-manager-design-templates.md). Azt javasoljuk, amely rendelkezik a következő sablonokat:

* **Fő sablon** (azuredeploy.json). A bemeneti paramétereket használni.
* **Megosztott erőforrások sablon**. Használatával történő telepítése a megosztott erőforrásokat, amelyek más erőforrások (például: virtuális hálózat és a rendelkezésre állási készlet). Használja a **dependsOn** kifejezés, győződjön meg arról, hogy ez a sablon olyan sablon előtt van-e telepíteni.
* **Nem kötelező erőforrások sablon**. A paraméter (például jumpbox) alapján erőforrások feltételesen telepítéséhez használható.
* **Tag erőforrások sablon**. Minden belül egy alkalmazás réteget példánytípust saját konfigurációval rendelkezik. A réteg belül különböző példány típust határoznak meg. (Például először egy fürtöt hoz létre és további példányt adja hozzá a meglévő fürtből.) Minden példány rendelkezik saját központi telepítési sablont.
* **Parancsfájlok**. Széles körben újrafelhasználható parancsfájlok alkalmazhatók minden példány típusa (például inicializálása és formázása további lemezek). Egy adott testreszabási célra létrehozott egyéni parancsfájlok eltérőek, a példány típusa alapján.

![Beágyazott sablon](./media/resource-manager-template-best-practices/nestedTemplateDesign.png)

További információkért lásd: [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).

## <a name="conditionally-link-to-nested-templates"></a>Beágyazott sablonok feltételesen csatolása
A paraméter segítségével beágyazott sablonok feltételesen kapcsolódik. A paraméter a sablon URI-JÁNAK részévé válik:

```json
"parameters": {
    "newOrExisting": {
        "type": "String",
        "allowedValues": [
            "new",
            "existing"
        ]
    }
},
"variables": {
    "templatelink": "[concat('https://raw.githubusercontent.com/Contoso/Templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
},
"resources": [
    {
        "apiVersion": "2015-01-01",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "[variables('templatelink')]",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
            }
        }
    }
]
```

## <a name="template-format"></a>Sablon formátumban
Ajánlott a sablon egy JSON-érvényesítő keresztül továbbítja. Egy érvényesítő segítségével távolítsa el a felesleges vessző, zárójelek és zárójelek, üzembe helyezése során hibát okozhat. Próbálja [JSONLint](http://jsonlint.com/) vagy a kedvenc linter csomag szerkesztésével környezet (Visual Studio Code, Atom, Sublime Text, Visual Studio).

Akkor is érdemes jobb olvashatóság érdekében a JSON formátumban. A helyi szerkesztő JSON formázó csomag használható. A Visual Studio, a dokumentum formázása, nyomja le az ENTER **Ctrl + K, Ctrl + D**. A Visual Studio Code, nyomja le a **Alt + Shift + F**. Ha a helyi szerkesztő nem formázza a dokumentum, egy [online formázó](https://www.bing.com/search?q=json+formatter).

## <a name="next-steps"></a>Következő lépések
* A megoldás a virtuális gépek újratervezni a útmutatóért lásd: [futtassa egy Windows virtuális Gépet az Azure-ban](../guidance/guidance-compute-single-vm.md) és [Linux virtuális gép futtatása az Azure-ban](../guidance/guidance-compute-single-vm-linux.md).
* A storage-fiók beállításával kapcsolatos útmutatásért lásd: [Azure Storage teljesítményére és méretezhetőségére ellenőrzőlista](../storage/common/storage-performance-checklist.md).
* Vállalati használatát erőforrás-kezelő hatékonyan kezelheti az előfizetéseket, lásd: [Azure enterprise scaffold: részletes utasításokkal megadott előfizetés irányítás](resource-manager-subscription-governance.md).

