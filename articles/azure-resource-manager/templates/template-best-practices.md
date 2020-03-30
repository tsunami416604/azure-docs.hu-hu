---
title: Ajánlott eljárások a sablonokhoz
description: Az Azure Resource Manager-sablonok szerzői ajánlott megközelítéseit ismerteti. Javaslatokat tesz a sablonok használata során felmerülő gyakori problémák elkerülésére.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 870636d6457d842c89f261c2537644c17a335294
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156412"
---
# <a name="arm-template-best-practices"></a>Arm sablon gyakorlati tanácsok

Ez a cikk javaslatokat ad az Azure Resource Manager (ARM) sablon létrehozásáról. Ezek a javaslatok segítenek elkerülni a gyakori problémákat, amikor egy ARM-sablont használ a megoldás üzembe helyezéséhez.

Az Azure-előfizetések szabályozásával kapcsolatos javaslatokért tekintse meg az [Azure vállalati állványzat: Előíró előfizetés-szabályozás.](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

Az Azure [Resource Manager-sablonok fejlesztése a felhőkonzisztenciához](templates-cloud-consistency.md)című témakörben olvashat, amely javaslatokat tartalmaz az összes Azure felhőalapú környezetben dolgozó sablonok létrehozásáról.

## <a name="template-limits"></a>Sablonkorlátok

Korlátozza a sablon méretét 4 MB-ra, és minden paraméterfájlt 64 KB-ra. A 4 MB-os korlát a sablon végleges állapotára vonatkozik, miután iteratív erőforrás-definíciókkal, valamint változók és paraméterek értékekkel bővítette. 

Ön a következőkre is korlátozódik:

* 256 paraméter
* 256 változó
* 800 erőforrás (beleértve a másolások számát)
* 64 kimeneti érték
* 24 576 karakter egy sablonkifejezésben

Egyes sablonkorlátokat beágyazott sablon használatával túlléphet. További információ: [Csatolt sablonok használata azure-erőforrások üzembe helyezésekor.](linked-templates.md) A paraméterek, változók vagy kimenetek számának csökkentése érdekében több értéket egyesíthet egy objektumban. További információ: [Objects as parameters](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

## <a name="resource-group"></a>Erőforráscsoport

Amikor erőforrásokat telepít egy erőforráscsoportba, az erőforráscsoport metaadatokat tárol az erőforrásokról. A metaadatok az erőforráscsoport helyén tárolódnak.

Ha az erőforráscsoport régiója átmenetileg nem érhető el, az erőforráscsoport erőforrásai nem frissíthetők, mert a metaadatok nem érhetők el. A többi régió erőforrásai továbbra is a várt módon fognak működni, de nem frissíthetők. A kockázat minimalizálása érdekében keresse meg az erőforráscsoportot és az erőforrásokat ugyanabban a régióban.

## <a name="parameters"></a>Paraméterek

Az ebben a szakaszban található információk akkor lehetnek [hasznosak, ha paraméterekkel dolgozik.](template-parameters.md)

### <a name="general-recommendations-for-parameters"></a>A paraméterekre vonatkozó általános ajánlások

* Minimalizálja a paraméterek használatát. Ehelyett használjon változókat vagy literális értékeket olyan tulajdonságokhoz, amelyeket nem kell megadni a központi telepítés során.

* Paraméternevekhez használjon tevekis- és kis- és nagybetűket.

* A környezettől függően változó beállításokhoz ,például termékváltozathoz, mérethez vagy kapacitástól függően) használható paraméterek.

* A könnyebb azonosítás érdekében használjon paramétereket a megadandó erőforrásnevekhez.

* Adja meg a metaadatok ban szereplő összes paraméter leírását:

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

* Adja meg a nem érzékeny paraméterek alapértelmezett értékeit. Az alapértelmezett érték megadásával könnyebb telepíteni a sablont, és a sablon felhasználói egy megfelelő értéket látnak. Egy paraméter alapértelmezett értékének az alapértelmezett központi telepítési konfigurációban lévő összes felhasználóra érvényesnek kell lennie. 
   
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

* Választható paraméter megadásához ne használjon üres karakterláncot alapértelmezett értékként. Ehelyett használjon literális értéket vagy nyelvi kifejezést az érték létrehozásához.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* Ne használjon paramétert az API-verzióhoz egy erőforrástípushoz. Az erőforrás tulajdonságai és értékei verziószámtól függően változhatnak. Az IntelliSense egy kódszerkesztőben nem tudja meghatározni a megfelelő sémát, ha az API-verzió paraméterre van állítva. Ehelyett a sablonban az API-verzió kódolása.

* Takarékosan `allowedValues` használja. Csak akkor használja, ha meg kell győződnie arról, hogy bizonyos értékek nem szerepelnek az engedélyezett beállításokban. Ha túl `allowedValues` tágan használja, előfordulhat, hogy blokkolja az érvényes központi telepítések nem tartja naprakészen a listát.

* Ha a sablonban szereplő paraméternév megegyezik a PowerShell központi telepítési parancsának egy paraméterével, az Erőforrás-kezelő ezt az elnevezési ütközést a **FromTemplate** sablonparaméterhez való hozzáadásával oldja fel. Ha például egy **ResourceGroupName** nevű paramétert ad meg a sablonban, az ütközik a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) parancsmag **ResourceGroupName** paraméterével. A telepítés során a rendszer kéri, hogy adjon meg egy értéket a **ResourceGroupNameFromTemplate sablonhoz.**

### <a name="security-recommendations-for-parameters"></a>Biztonsági javaslatok a paraméterekhez

* Mindig használjon paramétereket a felhasználónevekhez és jelszavakhoz (vagy titkos kulcsokhoz).

* Minden `securestring` jelszóhoz és titokhoz használható. Ha bizalmas adatokat ad át egy `secureObject` JSON-objektumban, használja a típust. A biztonságos karakterlánc- vagy biztonságos objektumtípusokkal rendelkező sablonparaméterek nem olvashatók az erőforrás-telepítés után. 
   
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

* Ne adjon meg alapértelmezett értékeket a felhasználónevekhez, jelszavakhoz vagy bármely olyan értékhez, amelyhez típus szükséges. `secureString`

* Ne adjon meg alapértelmezett értékeket olyan tulajdonságokhoz, amelyek növelik az alkalmazás támadási felületét.

### <a name="location-recommendations-for-parameters"></a>A paraméterek helymeghatározási javaslatai

* Paraméter használatával adja meg az erőforrások helyét, és `resourceGroup().location`állítsa az alapértelmezett értéket a értékre. Helyparaméter megadása lehetővé teszi a sablon felhasználói számára, hogy megadják azt a helyet, amelynek engedéllyel rendelkeznek a telepítéshez.

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

* Ne adja `allowedValues` meg a helyparamétert. Előfordulhat, hogy a megadott helyek nem minden felhőben érhetők el.

* Használja a hely paraméter értékét olyan erőforrásokhoz, amelyek valószínűleg ugyanazon a helyen vannak. Ez a megközelítés minimálisra csökkenti, hogy a felhasználókhányszor kérik a helyadatok megadását.

* Olyan erőforrások esetén, amelyek nem minden helyen érhetők el, használjon külön paramétert, vagy adjon meg egy literális helyértéket.

## <a name="variables"></a>Változók

A következő információk hasznosak lehetnek, ha [változókkal dolgozik:](template-variables.md)

* A változónevekhez használjon tevekistost.

* Változókat használhat olyan értékekhez, amelyeket egysablonban többször is fel kell használnia. Ha egy értéket csak egyszer használ, a kódolt érték megkönnyíti a sablon olvasását.

* Változókat használjon a sablonfüggvények összetett elrendezéséből származó értékekhez. A sablon könnyebben olvasható, ha az összetett kifejezés csak változókban jelenik meg.

* Ne használjon változókat egy `apiVersion` erőforráshoz. Az API-verzió határozza meg az erőforrás sémáját. Gyakran nem módosíthatja a verziót az erőforrás tulajdonságainak módosítása nélkül.

* A sablon **változók** szakaszában nem használható a [referencia](template-functions-resource.md#reference) függvény. A **referenciafüggvény** értékét az erőforrás futásidejű állapotából nyeri. A változók azonban a sablon kezdeti elemzéssorán feloldódnak. Olyan értékeket hozhat létre, amelyeknek közvetlenül a sablon **erőforrások** vagy **kimenetek** szakaszában kell a **referenciafüggvényt** létrehozniuk.

* Egyedi nek kell lennie az erőforrásnevek változóinak.

* A [JSON-objektumok ismétlődő](copy-variables.md) mintázatának létrehozásához használjon másolási hurkot a változókban.

* Távolítsa el a nem használt változókat.

## <a name="resource-dependencies"></a>Erőforrás-függőségek

A beállítani uk [lévő függőségek](define-resource-dependency.md) meghatározásakor kövesse az alábbi irányelveket:

* Használja a **referencia** függvényt, és adja át az erőforrás nevét, hogy implicit függőséget állítson be a tulajdonságmegosztáshoz szükséges erőforrások között. Ne adjon hozzá `dependsOn` explicit elemet, ha már definiált implicit függőséget. Ez a megközelítés csökkenti a szükségtelen függőségek kockázatát.

* Gyermekerőforrás beállítása a szülőerőforrástól függőként.

* A [feltételelem hamis](conditional-resource-deployment.md) értékű erőforrások automatikusan törlődnek a függőségi sorrendből. Állítsa be a függőségeket, mintha az erőforrás mindig telepítve lenne.

* Hagyja, hogy a függőségek kaszkádolnak anélkül, hogy kifejezetten beállítanák őket. A virtuális gép például egy virtuális hálózati adaptertől függ, a virtuális hálózati adapter pedig egy virtuális hálózattól és nyilvános IP-címektől. Ezért a virtuális gép mindhárom erőforrás után üzembe kerül, de ne állítsa be explicit módon a virtuális gépet mindhárom erőforrástól függőként. Ez a megközelítés tisztázza a függőségi sorrendet, és megkönnyíti a sablon későbbi módosítását.

* Ha egy érték megadható az üzembe helyezés előtt, próbálja meg függőség nélkül telepíteni az erőforrást. Ha például egy konfigurációs értéknek szüksége van egy másik erőforrás nevére, előfordulhat, hogy nincs szüksége függőségre. Ez az útmutató nem mindig működik, mert egyes erőforrások ellenőrzik a másik erőforrás létezését. Ha hibaüzenetet kap, adjon hozzá függőséget.

## <a name="resources"></a>Források

Az alábbi információk hasznosak lehetnek, ha [erőforrásokkal](template-syntax.md#resources)dolgozik:

* Annak érdekében, hogy más közreműködők jobban megértsék az erőforrás célját, adja meg a **megjegyzéseket** a sablonban az egyes erőforrásokhoz:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-06-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Ha nyilvános *végpontot* (például egy Azure Blob-tároló nyilvános végpontját) használ, *ne kódolja* a névteret. A **hivatkozási** függvénnyel dinamikusan beolvasható a névtér. Ezzel a módszerrel telepítheti a sablont különböző nyilvános névtérkörnyezetekben anélkül, hogy manuálisan módosítaná a végpontot a sablonban. Állítsa be az API-verziót ugyanarra a verzióra, amelyet a sablonban lévő tárfiókhoz használ:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Ha a tárfiók ugyanabban a sablonban van telepítve, amelyet létrehoz, és a tárfiók neve nincs megosztva a sablon egy másik erőforrásával, nem kell megadnia a szolgáltató névterét vagy az apiVersiont, amikor az erőforrásra hivatkozik. A következő példa az egyszerűsített szintaxist mutatja be:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
       }
   }
   ```
     
   Hivatkozhat egy másik erőforráscsoportban lévő meglévő tárfiókra is:

   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('existingStorageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```

* Nyilvános IP-címeket csak akkor rendeljen hozzá egy virtuális géphez, ha egy alkalmazás nak szüksége van rá. Ha hibakereséscéljából, illetve felügyeleti vagy felügyeleti célokra szeretne csatlakozni egy virtuális géphez (VM), használjon bejövő NAT-szabályokat, virtuális hálózati átjárót vagy ugródobozt.
   
     A virtuális gépekhez való csatlakozásról további információt a következő témakörben talál:
   
   * [Virtuális gépek futtatása N szintű architektúrához az Azure-ban](../../guidance/guidance-compute-n-tier-vm.md)
   * [WinRM-hozzáférés beállítása a virtuális gépekhez az Azure Resource Managerben](../../virtual-machines/windows/winrm.md)
   * [Külső hozzáférés engedélyezése a virtuális géphez az Azure Portal használatával](../../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Külső hozzáférés engedélyezése a virtuális géphez a PowerShell használatával](../../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Külső hozzáférés engedélyezése a Linux virtuális géphez az Azure CLI használatával](../../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* A nyilvános IP-címek **domainNameLabel** tulajdonságának egyedinek kell lennie. A **domainNameLabel** értéknek 3 és 63 karakter közöttinek kell lennie, `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`és a reguláris kifejezésben meghatározott szabályokat kell követnie: . Mivel a **uniqueString** függvény 13 karakter hosszú karakterláncot hoz létre, a **dnsPrefixString** paraméter legfeljebb 50 karakterből állhat:

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

* Amikor jelszót ad hozzá egy egyéni parancsfájl-bővítményhez, használja a **commandToExecute** tulajdonságot a **protectedSettings** tulajdonságban:
   
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
   > Annak érdekében, hogy titkos kulcsok titkosítva vannak, amikor paraméterekként a virtuális gépek és bővítmények, használja a **protectedSettings** tulajdonság a megfelelő bővítmények.
   > 
   > 

## <a name="next-steps"></a>További lépések

* A sablonfájl szerkezetéről az [ARM-sablonok szerkezetének és szintaxisának megismerése című témakörben olvashat.](template-syntax.md)
* Az Azure felhőkörnyezetében dolgozó sablonok létrehozásáról az [ARM-sablonok fejlesztése a felhőkonzisztenciához című](templates-cloud-consistency.md)témakörben olvashat.
