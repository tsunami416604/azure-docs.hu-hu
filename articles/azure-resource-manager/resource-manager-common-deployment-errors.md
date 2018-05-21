---
title: Az Azure-telepítés gyakori hibák elhárítása |} Microsoft Docs
description: Ismerteti, hogyan lehet gyakori hibák megoldásához erőforrások Azure-ban Azure Resource Manager használatával történő központi telepítésekor.
services: azure-resource-manager
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: központi telepítési hiba, az azure-telepítés, telepítse az azure
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: 3ecc1a9557c7854a0771decb3cc7f7597bcd87dd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Hibaelhárítás általános az Azure-telepítés az Azure Resource Manager eszközzel

Ez a cikk ismerteti a gyakori az Azure-telepítés hibák észlelhetnek, és javítsa ki a hibákat az információival. Ha a hiba kódja nem találhatók a központi telepítési hiba, tekintse meg [hibakód keresése](#find-error-code).

## <a name="error-codes"></a>Hibakódok

| Hibakód | Kezelés | További információ |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Kövesse a tárfiókok vonatkozó elnevezési korlátozás. | [Oldja meg a tárfiók neve](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Ellenőrizze a rendelkezésre álló tár fiók tulajdonságait. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | A fürt vagy a régió nincs forrásanyag is elérhető, vagy nem támogatja a kért Virtuálisgép-méretet. Próbálja megismételni a kérést később, vagy kérje meg egy másik Virtuálisgép-méretet. | [Kiépítés és lefoglalás problémái Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md), [Windows kiépítés és lefoglalás problémái](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) és [fellépő lefoglalási hibák elhárítása](../virtual-machines/windows/allocation-failure.md)|
| AnotherOperationInProgress | Várjon, amíg egyidejű művelet elvégzéséhez. | |
| AuthorizationFailed | A fióknév vagy egyszerű szolgáltatásnév nincs engedélye a központi telepítés befejezéséhez. Ellenőrizze a szerepkör a fiókja tagja, és a hozzáférése a központi telepítés hatókör. | [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md) |
| Hibás kérés | Központi telepítés értékek, amelyek nem egyeznek, mi várható erőforrás-kezelő által küldött. Ellenőrizze a belső hibaállapot-üzeneteket a hibaelhárítás támogatásához. | [Sablonra való hivatkozást](/azure/templates/) és [támogatott helyek](resource-manager-templates-resources.md#location) |
| Ütközés | A kért műveletet az erőforrás a jelenlegi állapotban nem engedélyezett. Például a lemezek átméretezése engedélyezett csak akkor, ha a virtuális gép létrehozása, vagy ha a virtuális gép felszabadítása. | |
| DeploymentActive | Várjon, amíg párhuzamos központi telepítést, hogy ez az erőforráscsoport befejezéséhez. | |
| Sikertelen | A "deploymentfailed" hiba: Általános hiba, amely nem adja meg a hiba megoldásához szükséges adatokat. Keresse meg a hiba részletei hibakódot nyújt részletesebb információt. | [Hibakód keresése](#find-error-code) |
| DeploymentQuotaExceeded | Ha Ön eléri a erőforráscsoportra 800 központi telepítések, központi telepítések törlése az előzmények már nem szükséges. Az előzmények bejegyzéseinek törölheti [az csoport központi telepítésének törlése](/cli/azure/group/deployment#az_group_deployment_delete) Azure CLI használata esetén vagy [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment) a PowerShellben. Bejegyzést az üzemelő példány előzményeinek a törlése nem érinti a telepítés erőforrások. | |
| DnsRecordInUse | A DNS-rekord nevének egyedinek kell lennie. Adjon meg egy másik nevet, vagy módosítsa a meglévő bejegyzést. | |
| ImageNotFound | Ellenőrizze a virtuális gép kép beállításait. |  |
| InUseSubnetCannotBeDeleted | Ez a hiba jelentkezhetnek, ha megpróbált frissíteni egy erőforrást, de törlésével és az erőforrás létrehozása dolgozza fel a kérelmet. Ügyeljen arra, hogy az összes változatlan értékeket megadni. | [Erőforrás frissítése](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Szerezze be a hozzáférési tokent a megfelelő bérlő számára. Csak a jogkivonat lekérheti a bérlő, a fiókja tagja. | |
| InvalidContentLink | Valószínűleg próbált összekapcsolása egy beágyazott sablont, amely nem érhető el. Ellenőrizze a beágyazott sablon megadott URI. Ha a sablon egy tárfiókot, ellenőrizze, hogy az URI elérhető. Szükség lehet egy SAS-jogkivonat átadni. | [csatolt sablonok](resource-group-linked-templates.md) |
| InvalidParameter | Az erőforrás megadott értékek egyike nem egyezik a várt értékkel. Ez a hiba oka lehet számos különböző feltételeket. Például lehet, hogy a jelszó megfelelő, vagy lehet, hogy a blob neve helytelen. Tekintse meg a hibaüzenetet, annak eldöntéséhez, amelynek ki kell javítani szükséges. | |
| InvalidRequestContent | A központi telepítés értékek nem várt vagy a hiányzó értékeket tartalmaznak szükséges értékeket. Ellenőrizze az erőforrás típusára vonatkozó értékeket. | [Hivatkozása](/azure/templates/) |
| InvalidRequestFormat | A hibakeresési naplózást engedélyező, a központi telepítés végrehajtása közben, és ellenőrizze a kérelem tartalma. | [Hibakeresési naplózás](#enable-debug-logging) |
| InvalidResourceNamespace | A megadott erőforrás-névtér ellenőrzése a **típus** tulajdonság. | [Hivatkozása](/azure/templates/) |
| InvalidResourceReference | Az erőforrás még nem létezik, vagy helytelenül hivatkozott. Ellenőrizze, hogy hozzáadjon egy függőséget szükséges. Ellenőrizze, hogy Ön miként használja a a **hivatkozás** függvény a forgatókönyvhöz szükséges paramétereket tartalmazza. | [-Függőségek feloldása](resource-manager-not-found-errors.md) |
| InvalidResourceType | Ellenőrizze az erőforrás írja be az a **típus** tulajdonság. | [Hivatkozása](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Az előfizetés regisztrálása az erőforrás-szolgáltató. | [Hárítsa el a regisztrációs](resource-manager-register-provider-errors.md) |
| InvalidTemplate | Ellenőrizze a hibákat a sablon szintaxisát. | [Érvénytelen a sablon feloldása](resource-manager-invalid-template-errors.md) |
| InvalidTemplateCircularDependency | Távolítsa el a felesleges függőségeket. | [Körkörös függőségek feloldása](resource-manager-invalid-template-errors.md#circular-dependency) |
| LinkedAuthorizationFailed | Ellenőrizze, hogy ha a fiókja tagja ugyanannak a bérlőnek a telepíti erőforráscsoportként működnek. | |
| LinkedInvalidPropertyId | Az erőforrás-azonosítója egy erőforrás nem megfelelően van feloldása. Ellenőrizze, hogy értékeket ad meg minden szükséges erőforrás-azonosítóhoz, beleértve az előfizetés-azonosító, az erőforráscsoport neve, erőforrástípus, szülő erőforrás neve (ha szükséges) és erőforrás neve. | |
| LocationRequired | Adjon meg egy helyet, az erőforrás. | [Hely beállítása](resource-manager-templates-resources.md#location) |
| MismatchingResourceSegments | Győződjön meg arról, hogy beágyazott erőforrás rendelkezik megfelelő számú szegmenseinek nevét és típusát. | [Hárítsa el a szegmensek erőforrás](resource-manager-invalid-template-errors.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Ellenőrizze az erőforrás-szolgáltató regisztrációs állapotát, és a támogatott helyek. | [Hárítsa el a regisztrációs](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Az előfizetés regisztrálása az erőforrás-szolgáltató. | [Hárítsa el a regisztrációs](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Erőforrás-szolgáltató regisztráció állapotának ellenőrzése. | [Hárítsa el a regisztrációs](resource-manager-register-provider-errors.md) |
| notFound | Ön próbált meg telepíteni a függő erőforrások és a szülő erőforrás párhuzamosan. Ellenőrizze, hogy hozzáadjon egy függőséget szüksége. | [-Függőségek feloldása](resource-manager-not-found-errors.md) |
| OperationNotAllowed | A központi telepítést, amely meghaladja a kvótát, az előfizetés, erőforráscsoportban vagy régió művelettel próbálkozik. Ha lehetséges javítsa ki a környezetet, hogy a kvóták belül maradnak. Ellenkező esetben fontolja meg a kért módosítva lett a kvótákat. | [Hárítsa el a kvóták](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Győződjön meg arról, hogy a szülő erőforrás létezik-e a gyermek-erőforrás létrehozása előtt. | [Hárítsa el a szülő erőforrás](resource-manager-parent-resource-errors.md) |
| PrivateIPAddressInReservedRange | A megadott IP-cím az Azure által igényelt címtartománya tartalmazza. Fenntartott tartomány elkerülése érdekében IP-címének módosítása. | [IP-címek](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | A megadott IP-cím az alhálózat tartományon kívül esik. Alhálózati tartományba eső IP-címének módosítása. | [IP-címek](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Néhány tulajdonság üzembe helyezett erőforrás nem módosítható. Egy erőforrás frissítésekor korlátozza a feljogosított tulajdonságainak módosítása. | [Erőforrás frissítése](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | A előfizetése tartalmaz egy erőforrás-házirendet, amely megakadályozza a központi telepítése során végrehajtani kívánt műveletet. Keresse meg a házirendet, amely blokkolja a műveletet. Ha lehetséges módosíthatja a környezetet, hogy megfeleljen a korlátozások vonatkoznak a szabályzat alól. | [Hárítsa el a házirendek](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Adjon meg egy erőforrás nevét, amely tartalmazza az foglalt név. | [Fenntartott erőforrások neve](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Várjon, amíg befejeződik a törlésre. | |
| ResourceGroupNotFound | Ellenőrizze a központi telepítés a célként megadott erőforráscsoport nevét. Ez már léteznie kell az előfizetéshez. Ellenőrizze az előfizetési kontextust. | [Az Azure CLI](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/azurerm.profile/set-azurermcontext) |
| NemTalálhatóErőforrás | A központi telepítés hivatkozik a erőforrása, amely nem oldható fel. Ellenőrizze, hogy Ön miként használja a a **hivatkozás** függvény a forgatókönyvhöz szükséges paramétereket tartalmazza. | [Hivatkozások feloldása](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | A központi telepítés próbál létrehozni, amelyek mérete meghaladja a kvótát, az előfizetés, erőforráscsoportban vagy régió erőforrásokat. Ha lehetséges javítsa ki a kvóták belül marad az infrastruktúra. Ellenkező esetben fontolja meg a kért módosítva lett a kvótákat. | [Hárítsa el a kvóták](resource-manager-quota-errors.md) |
| SkuNotAvailable | Válassza ki a Termékváltozat (például a virtuális gép méretét), amely a kijelölt helyen érhető el. | [Hárítsa el a Termékváltozat](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Adjon meg egy egyedi nevet a tárfióknak. | [Oldja meg a tárfiók neve](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | Adjon meg egy egyedi nevet a tárfióknak. | [Oldja meg a tárfiók neve](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Ellenőrizze az előfizetés, erőforráscsoport és a használni kívánt tárfiók nevére. | |
| SubnetsNotInSameVnet | A virtuális gépek csak rendelkezhet egy virtuális hálózatot. Több hálózati adapterrel való telepítésekor, ellenőrizze, hogy az azonos virtuális hálózatban tartoznak. | [Több hálózati adapter](../virtual-machines/windows/multiple-nics.md) |
| TemplateResourceCircularDependency | Távolítsa el a felesleges függőségeket. | [Körkörös függőségek feloldása](resource-manager-invalid-template-errors.md#circular-dependency) |
| TooManyTargetResourceGroups | Csökkentse az erőforráscsoportok egyetlen központi telepítés számát. | [Erőforráscsoportok közötti üzembe helyezés](resource-manager-cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Hibakód keresése

A hibák fogadhat két típusa van:

* Érvényesítési hiba
* Telepítési hibák

Érvényesítési hibák merülnek fel a szolgáltatásokat, amelyek a központi telepítés előtt meg lehet határozni. Szintaktikai hibákat a sablont, vagy szeretné telepíteni az erőforrásokat, amelyek túllépné az előfizetés kvóták tartoznak. Telepítési hibák feltételek, a telepítési folyamat során előforduló merülhetnek fel. Tartalmaznak egy párhuzamos telepített erőforrás elérésére tett kísérlet.

Mindkét típusú hibák hibakódot, amelyekkel a telepítés hibáinak. Mindkét típusú hibák jelennek meg a [tevékenységnapló](resource-group-audit.md). Azonban érvényesítési hiba jelenik meg a központi telepítés előzményei mert soha nem indult el a központi telepítés.

### <a name="validation-errors"></a>Érvényesség-ellenőrzési hibák

A portálon keresztül telepítésekor érvényesítési hiba láthatja az értékek elküldése után.

![portál érvényesítési hibaüzenet megjelenítése](./media/resource-manager-common-deployment-errors/validation-error.png)

Válassza ki a további részleteket az üzenetet. Az alábbi képen látható egy **InvalidTemplateDeployment** hiba, és egy házirend jelző üzenet blokkolva központi telepítés.

![érvényesítési részletek megjelenítése](./media/resource-manager-common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Telepítési hibák

A művelet a teljesíti az ellenőrző, de nem sikerül a telepítés során, tekintse meg az értesítéseket a hibát. Válassza ki az értesítést.

![értesítési hiba](./media/resource-manager-common-deployment-errors/notification.png)

Megjelenik a telepítéssel kapcsolatos további részletekért. A beállítás a hibával kapcsolatban további információt.

![központi telepítése nem sikerült](./media/resource-manager-common-deployment-errors/deployment-failed.png)

Megjelenik a hibaüzenet és hibakódok. Figyelje meg, hogy van két hibakódok. Az első hibakód (**"deploymentfailed"**) egy általános hiba, amely nem biztosítja a hiba megoldásához szükséges adatokat. A második hibakód (**StorageAccountNotFound**) kell részleteit. 

![a hiba részletei](./media/resource-manager-common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>A hibakeresési naplózást engedélyező

Néha kell a kérelem és válasz megtudhatja, mi a probléma további információt. PowerShell vagy Azure CLI segítségével kérheti, hogy további információkat kerül a központi telepítés során.

- PowerShell

   A PowerShellben, állítsa be a **DeploymentDebugLogLevel** az összes paramétert, ResponseContent vagy RequestContent.

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   Vizsgálja meg a tartalom a következő parancsmaggal kérelem:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   Vagy a tartalom a válasz:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   Ezen információk segítségével eldöntheti, hogy a sablonban lévő érték helytelen beállítása.

- Azure CLI

   Vizsgálja meg a telepítési műveleteket az alábbi paranccsal:

  ```azurecli
  az group deployment operation list --resource-group ExampleGroup --name vmlinux
  ```

- Beágyazott sablon

   Beágyazott sablon hibakeresési adatok naplózására, használja a **debugSetting** elemet.

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

## <a name="create-a-troubleshooting-template"></a>Hibaelhárítási-sablon létrehozása

Bizonyos esetekben a hibaelhárítás a sablon legkönnyebben teszteléséhez része. Létrehozhat egy egyszerűsített sablont, amely lehetővé teszi, hogy jobban összpontosíthat az a része, amely úgy véli okozza a hibát. Tegyük fel, hogy hiba azért kapta, való hivatkozáskor erőforrás. Ahelyett, hogy egy teljes sablon foglalkozik, a része, a problémát okozó visszaadó sablont létrehozni. Ez segít meghatározni, hogy átadott a megfelelő paraméterek sablon függvénnyel megfelelően, és az erőforrás lekérése várt.

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

Vagy tegyük fel, amely azt feltételezi, helytelenül állította be a függőségek kapcsolódó központi telepítési hibái áll kapcsolatban. Tesztelje a sablon ossza egyszerűsített sablonok. Először hozzon létre egy sablont, amely csak egyetlen erőforrás (például egy SQL Server) telepít. Ha meggyőződött arról, hogy helyesen definiálva erőforrást is tartalmaz, amelyektől függ (például egy SQL-adatbázis) erőforrás hozzáadása. Ha két erőforrások helyesen definiálva van, adja hozzá a más függő erőforrások (például naplózási házirendek). Minden egyes próbatelepítés Between törölje a csoportot, hogy biztosan megfelelően tesztelése a függőségeket.


## <a name="next-steps"></a>További lépések
* Műveletek naplózásával kapcsolatos további tudnivalókért lásd: [naplózási műveletek a Resource Manager](resource-group-audit.md).
* Azokról a műveletekről, a hibák megállapításához központi telepítése során további tudnivalókért lásd: [üzembe helyezési műveleteinek megtekintése](resource-manager-deployment-operations.md).
