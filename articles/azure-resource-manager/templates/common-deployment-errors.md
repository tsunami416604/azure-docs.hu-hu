---
title: Gyakori üzembehelyezési hibák elhárítása
description: Ez a témakör ismerteti, hogyan oldhatja meg a gyakori hibákat, amikor erőforrásokat telepít az Azure-ba az Azure Resource Manager használatával.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: bc1568c53cdb5518f694d77a2f28f3cf77296ee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460381"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Gyakori Azure-beli üzembe helyezési hibák elhárítása az Azure Resource Managerrel

Ez a cikk néhány gyakori Azure-telepítési hibát ismertet, és a hibák elhárításához nyújt tájékoztatást. Ha nem találja a telepítési hiba hibakódját, olvassa el [a Hibakód keresése című témakört.](#find-error-code)

Ha egy hibakóddal kapcsolatos információt keres, és ezeket az információkat nem tartalmazza ebben a cikkben, tudassa velünk. Az oldal alján visszajelzést hagyhat. A visszajelzést a GitHub-problémák követik nyomon.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="error-codes"></a>Hibakódok

| Hibakód | Kezelés | További információ |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Kövesse a tárfiókok elnevezési korlátozásait. | [Tárfiók nevének feloldása](error-storage-account-name.md) |
| AccountProperty nem állítható be | Ellenőrizze a rendelkezésre álló tárfiók tulajdonságait. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| Nem sikerült lefoglalás | A fürt vagy a régió nem rendelkezik elérhető erőforrásokkal, vagy nem támogatja a kért virtuális gép méretét. Próbálkozzon újra a kéréssel egy későbbi időpontban, vagy kérjen egy másik virtuális gép méretét. | [Linux kiépítési és elosztási problémái,](../../virtual-machines/linux/troubleshoot-deployment-new-vm.md) [Windows-ellátási és -kiosztási problémák kiépítése és](../../virtual-machines/windows/troubleshoot-deployment-new-vm.md) [kiosztása, valamint a foglalási hibák elhárítása](../../virtual-machines/troubleshooting/allocation-failure.md)|
| Egy másikművelet folyamatban | Várja meg, amíg az egyidejű művelet befejeződik. | |
| EngedélyezésFailed | A fiók vagy az egyszerű szolgáltatás nem rendelkezik elegendő hozzáféréssel a központi telepítés befejezéséhez. Ellenőrizze, hogy a fiók milyen szerepkörhöz tartozik, és hogy milyen hozzáféréssel rendelkezik az üzembe helyezési hatókörhöz.<br><br>Ez a hibaüzenet akkor jelenhet meg, ha egy szükséges erőforrás-szolgáltató nincs regisztrálva. | [Azure szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/role-assignments-portal.md)<br><br>[Regisztráció feloldása](error-register-resource-provider.md) |
| BadRequest (Hibás kérés) | Olyan telepítési értékeket küldött, amelyek nem egyeznek meg az Erőforrás-kezelő által elvárt értékekkel. A belső állapotüzenetben segítséget talál a hibaelhárításhoz. | [Sablonhivatkozás](/azure/templates/) és [támogatott helyek](resource-location.md) |
| Ütközés | Olyan műveletet kér, amely nem engedélyezett az erőforrás aktuális állapotában. Például a lemez átméretezése csak akkor engedélyezett, ha virtuális gép létrehozása, vagy ha a virtuális gép felszabadított. | |
| DeploymentActiveAndUneditable | Várja meg, amíg az erőforráscsoport egyidejű központi telepítése befejeződik. | |
| A telepítésfailedCleanUp | Ha teljes módban telepíti, a sablonban nem található erőforrások törlődnek. Ez a hibaüzenet akkor jelenik meg, ha nem rendelkezik megfelelő engedélyekkel a sablonban nem szereplő összes erőforrás törléséhez. A hiba elkerülése érdekében módosítsa a telepítési módot növekményesre. | [Az Azure Resource Manager telepítési módjai](deployment-modes.md) |
| DeploymentNameInvalidCharacters | A központi telepítés neve csak betűt, számjegyet, "-", """-t vagy "_" tartalmazhat. | |
| DeploymentNameLengthLimitTúllép | A központi telepítés neve 64 karakterből állhat.  | |
| A telepítés sikertelen | A DeploymentFailed hiba egy általános hiba, amely nem adja meg a hiba megoldásához szükséges részleteket. Keresse meg a hiba részleteit egy hibakódot, amely további információkat tartalmaz. | [Hibakód keresése](#find-error-code) |
| DeploymentQuotaExceeded | Ha eléri a 800 központi telepítések erőforráscsoportonként, törölje a központi telepítések az előzményekből, amelyek már nincs szükség. | [Hiba elhárítása, ha a telepítésszáma meghaladja a 800-at](deployment-quota-exceeded.md) |
| DnsRecordInUse | A DNS-rekord névnek egyedinek kell lennie. Adjon meg másik nevet. | |
| A kép nem található | Ellenőrizze a virtuális gép képbeállításait. |  |
| InUseSubnetCannotBeDeleted | Előfordulhat, hogy ez a hibaüzenet egy erőforrás frissítése során jelenik meg, és a kérés feldolgozása az erőforrás törlésével és létrehozásával történik. Győződjön meg arról, hogy minden változatlan értéket megad. | [Erőforrás frissítése](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Hozzáférési jogkivonat beszerezni a megfelelő bérlő. Csak a fiókhoz tartozó bérlőtől kaphatja le a jogkivonatot. | |
| InvalidContentLink | Valószínűleg olyan beágyazott sablonra próbált hivatkozni, amely nem érhető el. Ellenőrizze a beágyazott sablonhoz megadott URI-t. Ha a sablon létezik egy tárfiókban, győződjön meg arról, hogy az URI elérhető. Előfordulhat, hogy egy SAS-jogkivonatot kell átadnia. Jelenleg nem csatolhat olyan sablonhoz, amely egy [Azure Storage-tűzfal](../../storage/common/storage-network-security.md)mögötti tárfiókban található. Fontolja meg a sablon áthelyezése egy másik tárházba, például a GitHubra. | [Hivatkozott sablonok](linked-templates.md) |
| Érvénytelen telepítési hely | Az előfizetés iszintjén történő üzembe helyezéskor egy korábban használt központi telepítési névhez más helyet adott meg. | [Előfizetési szintű telepítések](deploy-to-subscription.md) |
| InvalidParameter paraméter | Az egyik megadott értékek egyike nem felel meg a várt értéknek. Ez a hiba számos különböző feltételből eredhet. Előfordulhat például, hogy egy jelszó nem elegendő, vagy egy blobnév helytelen. A hibaüzenetnek jeleznie kell, hogy melyik értéket kell kijavítani. | |
| InvalidRequestContent | A központi telepítési értékek vagy olyan értékeket tartalmaznak, amelyeket a rendszer nem ismer fel, vagy a szükséges értékek hiányoznak. Erősítse meg az erőforrástípus értékeit. | [Sablonreferencia](/azure/templates/) |
| ÉrvénytelenKéréses formátum | Engedélyezze a hibakeresési naplózást a központi telepítés futtatásakor, és ellenőrizze a kérelem tartalmát. | [Hibakeresési naplózás](#enable-debug-logging) |
| ÉrvénytelenErőforrás-névtér | Ellenőrizze a **típustulajdonságban** megadott erőforrásnévteret. | [Sablonreferencia](/azure/templates/) |
| ÉrvénytelenErőforrás-referencia | Az erőforrás vagy még nem létezik, vagy helytelenül hivatkozik. Ellenőrizze, hogy hozzá kell-e adnia egy függőséget. Ellenőrizze, hogy a **referenciafüggvény** használata tartalmazza-e a forgatókönyvhöz szükséges paramétereket. | [Függőségek feloldása](error-not-found.md) |
| InvalidResourceType | Ellenőrizze a **típustulajdonságban** megadott erőforrástípust. | [Sablonreferencia](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Regisztrálja az előfizetést az erőforrás-szolgáltatónál. | [Regisztráció feloldása](error-register-resource-provider.md) |
| InvalidTemplate | Ellenőrizze a sablon szintaxisát a hibákért. | [Érvénytelen sablon feloldása](error-invalid-template.md) |
| InvalidTemplateCircularDependency | Távolítsa el a szükségtelen függőségeket. | [Körkörös függőségek feloldása](error-invalid-template.md#circular-dependency) |
| A csatolt engedélyezési hiba sikertelen | Ellenőrizze, hogy a fiók ugyanahhoz a bérlőhöz tartozik-e, mint az az erőforráscsoport, amelybe telepíti. | |
| LinkedInvalidPropertyId azonosító | Az erőforrás-azonosító nem oldható fel megfelelően. Ellenőrizze, hogy megadja-e az erőforrás-azonosítóhoz szükséges összes értéket, beleértve az előfizetés-azonosítót, az erőforráscsoport nevét, az erőforrás típusát, a szülőerőforrás nevét (ha szükséges) és az erőforrás nevét. | |
| Helyszükséges | Adja meg az erőforrás helyét. | [Hely beállítása](resource-location.md) |
| Nem megfelelőerőforrás-szegmensek | Győződjön meg arról, hogy a beágyazott erőforrás nevében és típusában megfelelő számú szegmens található. | [Erőforrás-szegmensek feloldása](error-invalid-template.md#incorrect-segment-lengths)
| MissingRegistrationForLocation (Hiányzóregisztrációhely) | Ellenőrizze az erőforrás-szolgáltató regisztrációs állapotát és a támogatott helyeket. | [Regisztráció feloldása](error-register-resource-provider.md) |
| MissingSubscriptionRegistration | Regisztrálja az előfizetést az erőforrás-szolgáltatónál. | [Regisztráció feloldása](error-register-resource-provider.md) |
| NoRegisteredProviderFound | Ellenőrizze az erőforrás-szolgáltató regisztrációs állapotát. | [Regisztráció feloldása](error-register-resource-provider.md) |
| NotFound | Előfordulhat, hogy egy függő erőforrást próbál telepíteni egy szülőerőforrással párhuzamosan. Ellenőrizze, hogy hozzá kell-e adnia egy függőséget. | [Függőségek feloldása](error-not-found.md) |
| A művelet nem engedélyezett | A központi telepítés olyan műveletet kísérel meg, amely meghaladja az előfizetés, erőforráscsoport vagy régió kvótáját. Ha lehetséges, vizsgálja felül a telepítést, hogy a kvótákon belül maradjon. Ellenkező esetben fontolja meg a kvóták módosításának kérését. | [Kvóták feloldása](error-resource-quota.md) |
| ParentResourceNotFound | A gyermekerőforrások létrehozása előtt győződjön meg arról, hogy létezik fölérendelt erőforrás. | [Szülőerőforrás feloldása](error-parent-resource.md) |
| PasswordTooLong | Lehet, hogy túl sok karaktert tartalmazó jelszót választott ki, vagy a jelszó értékét biztonságos karakterláncra konvertálta, mielőtt paraméterként átadta volna. Ha a sablon tartalmaz egy **biztonságos karakterlánc-paramétert,** nem kell az értéket biztonságos karakterláncsá konvertálnia. Adja meg a jelszó értékét szövegként. |  |
| PrivátIPAddressinreservedRange | A megadott IP-cím tartalmaz egy címtartományt az Azure által igényelt. Módosítsa az IP-címet a fenntartott tartomány elkerülése érdekében. | [IP-címek](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | A megadott IP-cím kívül esik az alhálózati tartományon. Módosítsa az IP-címet úgy, hogy az alhálózati tartományba essen. | [IP-címek](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Egyes tulajdonságok nem módosíthatók egy üzembe helyezett erőforráson. Egy erőforrás frissítésekor korlátozza a módosításokat az engedélyezett tulajdonságokra. | [Erőforrás frissítése](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Az előfizetés tartalmaz egy erőforrás-szabályzatot, amely megakadályozza a telepítés során végrehajtani kívánt műveletet. Keresse meg a műveletet letiltja a házirendet. Ha lehetséges, módosítsa a központi telepítést, hogy megfeleljen a házirend korlátainak. | [Házirendek megoldása](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | Adjon meg egy olyan erőforrásnevet, amely nem tartalmaz fenntartott nevet. | [Fenntartott erőforrásnevek](error-reserved-resource-name.md) |
| Erőforráscsoporttörlés | Várja meg, amíg befejeződik a törlés. | |
| ResourceGroup Nem található | Ellenőrizze a központi telepítés célerőforrás-csoportjának nevét. A célerőforrás-csoportnak már léteznie kell az előfizetésben. Ellenőrizze az előfizetési környezetet. | [Azure CLI](/cli/azure/account?#az-account-set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| Erőforrás nem található | A központi telepítés olyan erőforrásra hivatkozik, amely nem oldható fel. Ellenőrizze, hogy a **referenciafüggvény** használata tartalmazza-e a forgatókönyvhöz szükséges paramétereket. | [Hivatkozások feloldása](error-not-found.md) |
| ResourceQuotaExceeded | A központi telepítés olyan erőforrásokat próbál létrehozni, amelyek meghaladják az előfizetés, erőforráscsoport vagy régió kvótáját. Ha lehetséges, vizsgálja felül az infrastruktúrát, hogy a kvótákon belül maradjon. Ellenkező esetben fontolja meg a kvóták módosításának kérését. | [Kvóták feloldása](error-resource-quota.md) |
| SkuNotAvailable | Válassza ki a termékváltozat (például a virtuális gép mérete), amely elérhető a kiválasztott helyen. | [Termékváltozat feloldása](error-sku-not-available.md) |
| StorageAccountMár létezik | Adjon meg egy egyedi nevet a tárfióknak. | [Tárfiók nevének feloldása](error-storage-account-name.md)  |
| Már elvégzett tárház | Adjon meg egy egyedi nevet a tárfióknak. | [Tárfiók nevének feloldása](error-storage-account-name.md) |
| StorageAccountNem alapítva | Ellenőrizze az előfizetést, az erőforráscsoportot és a használni kívánt tárfiók nevét. | |
| AlhálózatokNotInSameVnet | Egy virtuális gép csak egy virtuális hálózattal rendelkezhet. Több hálózati adapter ek telepítésekor győződjön meg arról, hogy ugyanahhoz a virtuális hálózathoz tartoznak. | [Több hálózati adapter](../../virtual-machines/windows/multiple-nics.md) |
| Előfizetés nincs regisztrálva | Hálózati erőforrások telepítésekor a Microsoft.Network erőforrás-szolgáltató automatikusan regisztrálva lesz az előfizetésben. Néha az automatikus regisztráció nem fejeződik be időben. Az időszakos hiba elkerülése érdekében a telepítés előtt regisztrálja a Microsoft.Network erőforrás-szolgáltatót. | [Regisztráció feloldása](error-register-resource-provider.md) |
| TemplateResourceCircularDependency | Távolítsa el a szükségtelen függőségeket. | [Körkörös függőségek feloldása](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | Egyetlen központi telepítéshez erőforráscsoportok számának csökkentése. | [Erőforráscsoportok közötti üzembe helyezés](cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Hibakód keresése

Kétféle hibáról kaphat értesítést:

* érvényesítési hiba
* üzembehelyezési hiba

Az érvényesítési hibák olyan forgatókönyvekből adódnak, amelyek az üzembe helyezés előtt határozhatók meg. Ezek közé tartoznak a sablon szintaxishibái, vagy az olyan erőforrások üzembe helyezése, amelyek túllépik az előfizetése kvótáit. Az üzembehelyezési hibák az üzembehelyezési folyamat során lépnek fel. Ezek közé tartozik például egy olyan erőforrás elérésére tett kísérlet, amelynek az üzembe helyezése párhuzamosan zajlik.

Mindkét típusú hiba az üzembe helyezés hibaelhárításához használható hibakódot ad vissza. Mindkét típusú hiba megjelenik a [tevékenységnaplóban](../management/view-activity-logs.md). Az érvényesítési hibák azonban nem jelennek meg az üzembe helyezési előzmények között, mert az üzembe helyezés el sem indult.

### <a name="validation-errors"></a>Érvényesítési hibák

Amikor a portálon keresztül végzi el az üzembe helyezést, az értékek megadása után jelenik meg az érvényesítési hiba.

![portál érvényesítési hibájának megjelenítése](./media/common-deployment-errors/validation-error.png)

További információért válassza ki az üzenetet. Az alábbi képen egy **InvalidTemplateDeployment** hiba és egy üzenet jelenik meg, amely azt jelzi, hogy a házirend letiltotta a központi telepítést.

![érvényesítési részletek megjelenítése](./media/common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Telepítési hibák

Ha a művelet megfelel az ellenőrzés követelményeinek, de az üzembe helyezés során meghiúsul, akkor üzembehelyezési hiba jelenik meg.

Az üzembehelyezési hibakódok és üzenetek megtekintése a PowerShell-lel:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Az üzembehelyezési hibakódok és üzenetek megtekintése az Azure CLI-vel:

```azurecli-interactive
az deployment group operation list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

A portálon válassza ki az értesítést.

![értesítési hiba](./media/common-deployment-errors/notification.png)

A központi telepítéssel kapcsolatos további részletek jelennek meg. Válassza ki a beállítást, hogy többet tudjon meg a hibáról.

![a telepítés nem sikerült](./media/common-deployment-errors/deployment-failed.png)

Megjelenik a hibaüzenet és a hibakódok. Figyelje meg, hogy két hibakód látható. Az első hibakód (**DeploymentFailed**) egy általános hiba, amely nem adja meg a hiba megoldásához szükséges részleteket. A második hibakód (**StorageAccountNotFound**) megadja a szükséges részleteket.

![hiba részletei](./media/common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Hibakeresési naplózás engedélyezése

Néha több információra van szüksége a kérésről és a válaszról, hogy megtudja, mi volt a hiba. A központi telepítés során kérheti, hogy a központi telepítés során további információkat naplózza.

### <a name="powershell"></a>PowerShell

A PowerShellben állítsa a **DeploymentDebugLogLevel** paramétert Az Összes, a ResponseContent vagy a RequestContent beállításra.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Vizsgálja meg a kérelem tartalmát a következő parancsmaggal:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Vagy a válasz tartalma:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Ez az információ segíthet annak meghatározásában, hogy a sablon egy értéke helytelenül van-e beállítva.

### <a name="azure-cli"></a>Azure CLI

Jelenleg az Azure CLI nem támogatja a hibakeresési naplózás bekapcsolását, de lekérheti a hibakeresési naplózást.

Vizsgálja meg a telepítési műveleteket a következő paranccsal:

```azurecli
az deployment group operation list \
  --resource-group examplegroup \
  --name exampledeployment
```

Vizsgálja meg a kérelem tartalmát a következő paranccsal:

```azurecli
az deployment group operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Vizsgálja meg a válasz tartalmát a következő paranccsal:

```azurecli
az deployment group operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Beágyazott sablon

Beágyazott sablon hibakeresési adatainak naplózásához használja a **debugSetting** elemet.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2016-09-01",
  "name": "nestedTemplate",
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

## <a name="create-a-troubleshooting-template"></a>Hibaelhárító sablon létrehozása

Bizonyos esetekben a sablon hibaelhárításának legegyszerűbb módja annak, hogy tesztelje annak egyes részeit. Létrehozhat egy egyszerűsített sablont, amely lehetővé teszi, hogy arra a részre összpontosítson, amelyről úgy gondolja, hogy a hibát okozza. Tegyük fel például, hogy hibaüzenetet kap egy erőforrásra való hivatkozáskor. Ahelyett, hogy egy teljes sablonnal foglalkozna, hozzon létre egy sablont, amely visszaadja a problémát okozó részt. Ez segíthet annak meghatározásában, hogy a megfelelő paramétereket adja-e át, helyesen használja-e a sablonfüggvényeket, és megkapja-e a várt erőforrást.

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

Vagy tegyük fel, hogy olyan telepítési hibákat kap, amelyekről úgy gondolja, hogy helytelenül beállított függőségekhez kapcsolódnak. Tesztelje a sablont egyszerűsített sablonokra bontásával. Először hozzon létre egy sablont, amely csak egyetlen erőforrást (például egy SQL Servert) telepít. Ha biztos benne, hogy az erőforrás megfelelően van definiálva, adjon hozzá egy erőforrást, amely attól függ (például egy SQL-adatbázis). Ha a két erőforrás t, megfelelően van definiálva, adjon hozzá további függő erőforrásokat (például naplózási házirendeket). Az egyes teszttelepítések között törölje az erőforráscsoportot, és győződjön meg arról, hogy megfelelően teszteli a függőségeket.

## <a name="next-steps"></a>További lépések

* Hibaelhárítási oktatóanyag on-mail, olvassa el [az oktatóanyag: Erőforrás-kezelő sablontelepítésekkel kapcsolatos hibaelhárítási témakört.](template-tutorial-troubleshoot.md)
* A naplózási műveletekről a [Naplózási műveletek az Erőforrás-kezelővel (Audit operations with Resource Manager) témakörben](../management/view-activity-logs.md)olvashat.
* A központi telepítés során fellépő hibák meghatározására irányuló műveletekről a [Telepítési műveletek megtekintése című témakörben](deployment-history.md)olvashat.
