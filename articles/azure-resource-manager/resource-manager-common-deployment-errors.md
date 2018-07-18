---
title: Gyakori Azure üzembehelyezési hibák elhárítása |} A Microsoft Docs
description: Ismerteti, hogyan lehet gyakori hibák megoldásához, amikor az erőforrások üzembe helyezése az Azure-bA az Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: központi telepítési hiba, az azure-telepítés, üzembe helyezése az Azure-bA
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/16/2018
ms.author: tomfitz
ms.openlocfilehash: 562e8e49d769f15ba0b965bfb03c0d56076c78f1
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39091322"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Gyakori Azure-beli hibák az Azure Resource Manager hibaelhárítása

Ez a cikk azt ismerteti, hogy néhány gyakori Azure üzembe helyezési hibák előfordulhatnak, és a hibák elhárításához nyújt. Ha a hibakód nem találja a központi telepítési hiba, tekintse meg [hibakód keresése](#find-error-code).

## <a name="error-codes"></a>Hibakódok

| Hibakód | Kezelés | További információ |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Hajtsa végre a storage-fiókok vonatkozó elnevezési korlátozás. | [Oldja meg a tárfiók neve](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Ellenőrizze a rendelkezésre álló tár fiók tulajdonságait. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | A fürt vagy régió nem rendelkezik elérhető erőforrások, vagy nem támogatja a kért Virtuálisgép-méretet. Próbálja megismételni a kérést később, vagy kérje meg egy másik Virtuálisgép-méretet. | [Kiépítés és foglalással kapcsolatos problémák Linux rendszerben](../virtual-machines/linux/troubleshoot-deployment-new-vm.md), [kiépítési és foglalással kapcsolatos problémák Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) és [foglalási hibák elhárítása](../virtual-machines/windows/allocation-failure.md)|
| AnotherOperationInProgress | Várjon, amíg a párhuzamos művelet végrehajtásához. | |
| AuthorizationFailed | A fióknév vagy a szolgáltatásnév nem rendelkezik megfelelő hozzáférési jogosultsággal a telepítés befejezéséhez. Ellenőrizze a fiók tartozik a szerepkör és a hozzáférés az üzembe helyezés hatálya. | [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md) |
| Hibás kérés | Üzembe helyezés értékek, amelyek nem egyeznek, mi várható Resource Manager által küldött. Ellenőrizze a belső állapotüzenet a hibaelhárítás. | [Sablon hivatkozása](/azure/templates/) és [támogatott helyek](resource-manager-templates-resources.md#location) |
| Ütközés | A kért műveletet az erőforrás jelenlegi állapotában nem engedélyezett. Ha például a lemezek átméretezése engedélyezett csak a virtuális gép létrehozásakor, vagy ha a virtuális gép fel van szabadítva. | |
| DeploymentActive | Várjon, amíg befejeződik ez az erőforráscsoport párhuzamos üzembe helyezés. | |
| Sikertelen | A "deploymentfailed" hiba, amely nem biztosít a részletek a hiba megoldásához szükséges általános hiba. Tekintse meg a hibaüzenet részleteiben talál egy hibakód, amely további információkat biztosít. | [Hibakód keresése](#find-error-code) |
| DeploymentQuotaExceeded | Ha eléri a korlátot, az adott erőforráscsoport esetében 800 központi telepítések, törölje a központi telepítések az előzményekben tekintheti át, hogy már nincs rá szükség. Bejegyzések törölheti az előzményekből [az csoport központi telepítésének törlése](/cli/azure/group/deployment#az_group_deployment_delete) Azure CLI-hez, vagy [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment) a PowerShellben. Az üzembe helyezési előzmények bejegyzés törlése nem befolyásolja az üzembe helyezés erőforrásokat. | |
| DnsRecordInUse | A DNS-rekord nevének egyedinek kell lennie. Adjon meg egy másik nevet, vagy módosítsa a meglévő rekord. | |
| ImageNotFound | Ellenőrizze a virtuális gép beállításai. |  |
| InUseSubnetCannotBeDeleted | Ez a hiba jelentkezhetnek, ha a program megpróbált frissíteni egy erőforrást, de a kérelem feldolgozása törlésével és az erőforrás létrehozásához. Ellenőrizze, hogy az összes változatlan érték megadásához. | [Erőforrás frissítése](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Hozzáférési jogkivonat beszerzése a megfelelő bérlő számára. A jogkivonat csak kérheti le a fiókjához tartozó bérlő. | |
| InvalidContentLink | Nagy valószínűséggel próbált összekapcsolása egy beágyazott sablont, amely nem érhető el. Ellenőrizze a beágyazott sablon a megadott URI-t. Ha a sablon egy storage-fiók már létezik, győződjön meg arról, az URI-t érhető el. Szükség lehet a SAS-jogkivonatát adja át. | [Összekapcsolt sablonok](resource-group-linked-templates.md) |
| InvalidParameter | Az erőforrás megadott értékek egyike nem egyezik a várt értékkel. Ezt a hibát okozhat például számos különböző feltételeket. Például lehet, hogy a jelszó nem elegendő, vagy lehet, hogy egy blob neve helytelen. A hibaüzenetben határozza meg, melyik értéket ki kell javítani kell. | |
| InvalidRequestContent | Az üzembe helyezés értékek nem várt vagy a hiányzó értékeket tartalmaznak szükséges értékeket. Erősítse meg az értékeket az erőforrástípushoz. | [Sablon hivatkozása](/azure/templates/) |
| InvalidRequestFormat | A hibakeresési naplózást engedélyező az üzembe helyezés végrehajtásakor, és ellenőrizze a kérelem tartalma. | [Hibakeresési naplózás](#enable-debug-logging) |
| InvalidResourceNamespace | Ellenőrizze a megadott erőforrás névtere a **típus** tulajdonság. | [Sablon hivatkozása](/azure/templates/) |
| InvalidResourceReference | Az erőforrás még nem létezik, vagy helytelenül hivatkozott. Ellenőrizze, hogy hozzáadjon egy függőséget kell. Ellenőrizze, hogy használatára a **referencia** függvényt tartalmazza a szükséges paramétereket a forgatókönyvhöz. | [Függőségek feloldása](resource-manager-not-found-errors.md) |
| InvalidResourceType | Ellenőrizze az erőforrás írja be az a **típus** tulajdonság. | [Sablon hivatkozása](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Regisztrálja az előfizetését az erőforrás-szolgáltatónál. | [Oldja meg a regisztráció](resource-manager-register-provider-errors.md) |
| InvalidTemplate | Ellenőrizze a hibákat a sablon szintaxisát. | [Érvénytelen a sablon feloldása](resource-manager-invalid-template-errors.md) |
| InvalidTemplateCircularDependency | Távolítsa el a felesleges függőségek. | [Körkörös függőségek feloldása](resource-manager-invalid-template-errors.md#circular-dependency) |
| LinkedAuthorizationFailed | Ellenőrizze, hogy ha a fiók megegyezik az erőforráscsoport telepíti, akkor ugyanazt bérlőhöz tartozik. | |
| LinkedInvalidPropertyId | Az erőforrás erőforrás-azonosítója nem megfelelően van feloldása. Ellenőrizze, hogy az erőforrás-azonosítóhoz, beleértve a előfizetés-azonosító, erőforráscsoport-nevet, erőforrás típusa, szülő erőforrás nevét (ha szükséges) és erőforrás nevét adja meg az összes szükséges értékeket. | |
| LocationRequired | Adja meg az erőforráscsoport helyét. | [Hely beállítása](resource-manager-templates-resources.md#location) |
| MismatchingResourceSegments | Győződjön meg arról, hogy beágyazott erőforrás neve és típusa a szegmensek megfelelő számú rendelkezik. | [Oldja meg az erőforrás-szegmensek](resource-manager-invalid-template-errors.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Ellenőrizze az erőforrás-szolgáltató regisztrációs állapota és a támogatott helyek. | [Oldja meg a regisztráció](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Regisztrálja az előfizetését az erőforrás-szolgáltatónál. | [Oldja meg a regisztráció](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Ellenőrizze az erőforrás-szolgáltató regisztrációs állapotát. | [Oldja meg a regisztráció](resource-manager-register-provider-errors.md) |
| NotFound | Előfordulhat, hogy kísérlet, a függő erőforrások és a egy szülő erőforrás üzembe helyezéséhez. Ellenőrizze, hogy hozzáadjon egy függőséget szüksége. | [Függőségek feloldása](resource-manager-not-found-errors.md) |
| OperationNotAllowed | A központi telepítést, amely meghaladja a kvótát, az előfizetés, erőforráscsoport vagy régió művelettel próbálkozik. Ha lehetséges javítsa ki a központi telepítés a kvóták belülre. Ellenkező esetben fontolja meg a kvóták módosítás kér. | [Oldja meg a kvóták](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Ellenőrizze, hogy a szülő erőforrás létezik, a gyermek-erőforrások létrehozása előtt. | [Oldja meg a szülő erőforrás](resource-manager-parent-resource-errors.md) |
| PrivateIPAddressInReservedRange | A megadott IP-cím egy Azure által igényelt címtartományt tartalmaz. Módosítsa az IP-cím fenntartott tartomány elkerülése érdekében. | [IP-címek](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | A megadott IP-cím az alhálózat tartományon kívül esik. Módosítsa az IP-címet az alhálózat címtartományba. | [IP-címek](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Néhány tulajdonság nem módosítható egy üzembe helyezett erőforráson. Amikor frissíti egy erőforrást, korlátozza az engedélyezett tulajdonságainak módosításait. | [Erőforrás frissítése](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Az előfizetése tartalmaz egy erőforrás-szabályzatot, amely megakadályozza az üzembe helyezés során végrehajtani kívánt művelet. Keresse meg a szabályzatot, amely blokkolja a műveletet. Ha lehetséges módosítsa a központi telepítést az felel meg a korlátozásokat a szabályzat alól. | [Oldja meg a házirendek](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Adja meg az erőforrás nevét, amely nem tartalmaz foglalt név. | [Fenntartott erőforrásnevek](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Várjon, amíg a törlés befejeződik. | |
| ResourceGroupNotFound | Ellenőrizze a központi telepítés a célként megadott erőforráscsoport nevét. Azt már léteznie kell az előfizetésben. Ellenőrizze az előfizetési környezet. | [Az Azure CLI](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/azurerm.profile/set-azurermcontext) |
| NemTalálhatóErőforrás | Az üzembe helyezés hivatkozik egy erőforrás, amely nem oldható fel. Ellenőrizze, hogy használatára a **referencia** függvényt tartalmazza a paramétereket a forgatókönyvhöz szükséges. | [Hivatkozások feloldása](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | Az üzembe helyezés próbál létre erőforrásokat, amelyek a kvóta az előfizetés, erőforráscsoport vagy régió. Ha lehetséges javítsa ki infrastruktúráját arra, hogy a kvóták belül. Ellenkező esetben fontolja meg a kvóták módosítás kér. | [Oldja meg a kvóták](resource-manager-quota-errors.md) |
| SkuNotAvailable | Válassza ki a Termékváltozat (például a virtuális gép mérete), amely a kijelölt helyen érhető el. | [Oldja meg a Termékváltozat](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Adjon meg egy egyedi nevet a tárfióknak. | [Oldja meg a tárfiók neve](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | Adjon meg egy egyedi nevet a tárfióknak. | [Oldja meg a tárfiók neve](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Ellenőrizze az előfizetés, erőforráscsoport és a használni kívánt tárfiók neve. | |
| SubnetsNotInSameVnet | Virtuális gép legfeljebb egy virtuális hálózatot. Több hálózati adapter üzembe helyezésekor, ellenőrizze, hogy az azonos virtuális hálózathoz tartoznak. | [Több hálózati adapter](../virtual-machines/windows/multiple-nics.md) |
| TemplateResourceCircularDependency | Távolítsa el a felesleges függőségek. | [Körkörös függőségek feloldása](resource-manager-invalid-template-errors.md#circular-dependency) |
| TooManyTargetResourceGroups | Csökkentse az erőforráscsoportok egyetlen központi telepítés számát. | [Erőforráscsoportok közötti üzembe helyezés](resource-manager-cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Hibakód keresése

Hibák fogadhat két típusa van:

* érvényesítési hibák
* telepítési hibák

Érvényesítési hibák merülnek fel az üzembe helyezés előtt meg lehet határozni forgatókönyvek. A sablonban, vagy túllépné az előfizetési kvóták erőforrásokat üzembe helyezni kívánt tartalmazzák szintaxishibáinak. Telepítési hibák merülnek fel a feltételek a központi telepítési folyamat során. Ezek tartalmazzák az elérni kívánt olyan erőforrások, amelyek párhuzamosan lesz üzembe helyezve.

Mindkét típusú hibák, amellyel a telepítés hibáinak hibakódot adja vissza. Mindkét típusú hibák jelennek meg a [tevékenységnapló](resource-group-audit.md). Azonban érvényesítési hibák nem jelennek meg az üzembe helyezési előzmények, mert az üzembe helyezés soha nem indította el.

### <a name="validation-errors"></a>Érvényesség-ellenőrzési hibák

Amikor üzembe helyezése a portálon keresztül, az értékek elküldése után látható érvényesítési hiba.

![Hiba történt a portál érvényesítésekor megjelenítése](./media/resource-manager-common-deployment-errors/validation-error.png)

Válassza ki az üzenetet, további részletekért. Az alábbi ábrán látható egy **InvalidTemplateDeployment** hiba és a egy üzenet, amely azt jelzi, hogy egy házirend központi telepítés letiltva.

![érvényesítési részletek megjelenítése](./media/resource-manager-common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Telepítési hibák

A művelet átmennek az ellenőrzésen, de a telepítés során nem sikerül, kap egy központi telepítési hiba.

Alkalmazástelepítési hibakódok és üzenetek a PowerShell-lel megjelenítéséhez használja:

```azurepowershell-interactive
(Get-AzureRmResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Alkalmazástelepítési hibakódok és üzenetek az Azure CLI-vel megjelenítéséhez használja:

```azurecli-interactive
az group deployment operation list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

A portálon válassza ki az értesítést.

![által okozott hiba](./media/resource-manager-common-deployment-errors/notification.png)

Akkor az üzembe helyezéssel kapcsolatos további részleteket láthat. Válassza a a hibával kapcsolatos információkat keres.

![nem sikerült telepíteni](./media/resource-manager-common-deployment-errors/deployment-failed.png)

Megjelenik a hibaüzenet és hibakódok. Figyelje meg, hogy van két hibakódok. Az első hibakód (**"deploymentfailed"**) egy általános hiba, amely nem biztosítja a részletek a hiba megoldásához szükséges. A második hibakódot (**StorageAccountNotFound**) biztosítja a szükséges adatokat. 

![a hiba részletei](./media/resource-manager-common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>A hibakeresési naplózást engedélyező

Néha szüksége további információt a kérések és válaszok megtudhatja, mi történt. Üzembe helyezés során a kérheti, hogy további információkat naplózza a központi telepítés során. 

### <a name="powershell"></a>PowerShell

A PowerShell-lel, állítsa be a **DeploymentDebugLogLevel** az összes paramétert, ResponseContent vagy RequestContent.

```powershell
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Vizsgálja meg a tartalmat a következő parancsmagot a kérelmet:

```powershell
(Get-AzureRmResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Vagy a tartalmat a választ:

```powershell
(Get-AzureRmResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Ez az információ segítségével meghatározhatja, hogy akár egy értéket a sablonban helytelenül beállítása alapján történik.

### <a name="azure-cli"></a>Azure CLI

Jelenleg az Azure parancssori felület nem támogatja a hibakeresési naplózás bekapcsolását, de kérheti le a hibakeresési naplózás.

Vizsgálja meg az üzembe helyezési műveletek a következő paranccsal:

```azurecli
az group deployment operation list \
  --resource-group examplegroup \
  --name exampledeployment
```

Vizsgálja meg a kérelem tartalma a következő paranccsal:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Vizsgálja meg a válasz tartalma a következő paranccsal:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Beágyazott sablont

Hibakeresési információ beágyazott sablon naplózása, használja a **debugSetting** elemet.

```json
{
    "apiVersion": "2016-09-01",
    "name": "nestedTemplate",
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "{template-uri}",
            "contentVersion": "1.0.0.0"
        },
        "debugSetting": {
           "detailLevel": "requestContent, responseContent"
        }
    }
}
```

## <a name="create-a-troubleshooting-template"></a>Hibaelhárítási sablon létrehozása

Bizonyos esetekben a legegyszerűbben úgy, hogy a sablon hibaelhárítása bizonyos részeit, a teszteléséhez. Létrehozhat egy egyszerűsített sablont, amely lehetővé teszi, hogy arra koncentrálhasson, a részt, amely Ön szerint okozza a hibát. Tegyük fel például, hogy hiba azért küldtük Önnek, amikor egy erőforrásra hivatkozik. Ahelyett, hogy egy teljes sablon kezelésével foglalkoznak, hozzon létre egy sablont, amely a rész a problémát okozó adja vissza. Ez segít meghatározni e átadott a megfelelő paramétereket a sablonban függvények használatával megfelelően, és az erőforrás lekérése várt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

Vagy tegyük fel, amely Ön szerint nem megfelelően állítsa be a függőségek kapcsolódó telepítési hibák merültek fel. Tesztelje a sablon bontásával egyszerűsített sablonokat. Először hozzon létre egy sablont, amely csak egyetlen erőforrás (például egy SQL Server) telepít. Ha meggyőződött arról, hogy helyesen definiálva erőforrás van, adjon hozzá egy erőforrást, amelyektől függ (például egy SQL Database). Ha két erőforrások helyesen definiálva van, adjon hozzá más függő erőforrások (például naplózási házirendek). Egyes tesztelési környezet között, ellenőrizze, hogy a függőségek megfelelő tesztelése az erőforráscsoport törlése.


## <a name="next-steps"></a>További lépések
* Naplózási műveletek kapcsolatos további információkért lásd: [auditálási műveletek a Resource Manager](resource-group-audit.md).
* Üzembe helyezés során a hibák megállapításához műveleteivel kapcsolatos tudnivalókért lásd: [üzembehelyezési műveletek megtekintése](resource-manager-deployment-operations.md).
