---
title: Telepítési parancsfájlok használata a sablonokban | Microsoft Docs
description: az üzembe helyezési parancsfájlok használata Azure Resource Manager-sablonokban.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: jgao
ms.openlocfilehash: 125fefbb1d83db8b6114b2d09f5bd6da885159ba
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547642"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Telepítési parancsfájlok használata a sablonokban (előzetes verzió)

Ismerje meg, hogyan használhatók az üzembe helyezési parancsfájlok az Azure Resource templates szolgáltatásban. Egy `Microsoft.Resources/deploymentScripts`nevű új erőforrástípus használatával a felhasználók a sablonok központi telepítései során futtathatják a telepítési parancsfájlokat, és áttekinthetik a végrehajtás eredményeit. Ezek a parancsfájlok olyan egyéni lépések végrehajtásához használhatók, mint például a következők:

- felhasználók hozzáadása egy címtárhoz
- alkalmazás-regisztráció létrehozása
- adatsík műveletek végrehajtása, például a Blobok vagy a vetőmag-adatbázisok másolása
- licenckulcs megkeresése és érvényesítése
- önaláírt tanúsítvány létrehozása
- objektum létrehozása az Azure AD-ben
- IP-címterület kikeresése egyéni rendszerből

Az üzembe helyezési parancsfájl előnyei:

- Egyszerű kód, használat és hibakeresés. Az üzembe helyezési parancsfájlokat kedvenc fejlesztői környezetekben is kifejlesztheti. A szkriptek sablonokba vagy külső parancsfájlokban is beágyazva lehetnek.
- Megadhatja a parancsfájl nyelvét és platformját. Jelenleg csak Azure PowerShell telepítési parancsfájlok támogatottak a Linux-környezetben.
- A parancsfájlok végrehajtásához használt identitások megadásának engedélyezése. Jelenleg csak az [Azure-felhasználóhoz rendelt felügyelt identitás](../../active-directory/managed-identities-azure-resources/overview.md) támogatott.
- A parancssori argumentumok parancsfájlba való átadásának engedélyezése.
- Megadhatja a parancsfájlok kimeneteit, és visszaküldheti azokat az üzembe helyezéshez.

> [!NOTE]
> Az üzembe helyezési parancsfájl jelenleg előzetes verzióban érhető el. A használatához regisztrálnia kell [az előzetes](https://aka.ms/armtemplatepreviews)verzióra.

> [!IMPORTANT]
> A parancsfájlok végrehajtásához és a hibaelhárításhoz két üzembe helyezési parancsfájl-erőforrás, egy Storage-fiók és egy tároló-példány jön létre ugyanabban az erőforráscsoporthoz. Ezeket az erőforrásokat általában a parancsfájl-szolgáltatás törli, amikor a telepítési parancsfájl végrehajtása egy terminál állapotba kerül. Az erőforrások számlázása az erőforrások törlése után történik. További információért lásd: [telepítési parancsfájl-erőforrások tisztítása](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Előfeltételek

- **Felhasználó által hozzárendelt felügyelt identitás a közreműködő szerepkörrel az előfizetés szintjén**. Ez az identitás az üzembe helyezési parancsfájlok végrehajtásához használatos. A létrehozáshoz tekintse meg a [felhasználó által hozzárendelt felügyelt identitás létrehozása a Azure Portal használatával](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)vagy az [Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)használatával vagy a [Azure PowerShell használatával](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)című témakört. A sablon üzembe helyezésekor szüksége lesz az azonosító AZONOSÍTÓra. Az identitás formátuma:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Használja az alábbi PowerShell-szkriptet az azonosító beszerzéséhez az erőforráscsoport nevének és az identitás nevének megadásával.

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  $id = (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name idName).Id
  ```

- **Azure PowerShell verzió: 2.7.0, 2.8.0 vagy 3.0.0**. A sablonok telepítéséhez nincs szükség ezekre a verziókra. Ezek a verziók azonban az üzembe helyezési parancsfájlok helyi teszteléséhez szükségesek. Lásd: [a Azure PowerShell modul telepítése](/powershell/azure/install-az-ps). Előre konfigurált Docker-rendszerképet használhat.  Lásd: a [fejlesztési környezet konfigurálása](#configure-development-environment).

## <a name="resource-schema"></a>Erőforrás-séma

A következő JSON egy példa.  A sablon legújabb sémája [itt](/azure/templates/microsoft.resources/deploymentscripts)található.

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "myDeploymentScript",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "azPowerShellVersion": "2.8",
    "arguments": "[concat('-name ', parameters('name'))]",
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ",
    "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> A példa demonstrációs célra szolgál.  a **scriptContent** és a **primaryScriptUris** nem létezhet egyetlen sablonban sem.

Tulajdonság értékének részletei:

- **Identitás**: az üzembe helyezési parancsfájl szolgáltatás felhasználó által hozzárendelt felügyelt identitást használ a parancsfájlok végrehajtásához. Jelenleg csak a felhasználó által hozzárendelt felügyelt identitás támogatott.
- **Típus: adja**meg a parancsfájl típusát. Jelenleg csak Azure PowerShell-parancsfájlok támogatottak. Az érték a **AzurePowerShell**.
- **forceUpdateTag**: ennek az értéknek a módosítása a sablon központi telepítései között kényszeríti a telepítési parancsfájl ismételt végrehajtását. Használja a newGuid () vagy a utcNow () függvényt, amelyet a paraméter defaultValue értékeként kell beállítani. További információ: [parancsfájl futtatása](#run-script-more-than-once)többször.
- **azPowerShellVersion**: Itt adhatja meg a használni kívánt Azure PowerShell modul verzióját. Az üzembe helyezési parancsfájl jelenleg a 2.7.0, a 2.8.0 és a 3.0.0 verziót támogatja.
- **argumentumok**: határozza meg a paraméterek értékeit. Az értékeket szóközök választják el egymástól.
- **scriptContent**: adja meg a parancsfájl tartalmát. Külső parancsfájl futtatásához használja a `primaryScriptUri` helyet. Példák: [beágyazott parancsfájl használata](#use-inline-scripts) és [külső parancsfájl használata](#use-external-scripts).
- **primaryScriptUri**: adjon meg egy nyilvánosan elérhető URL-címet az elsődleges PowerShell-parancsfájlhoz a PowerShell fájlkiterjesztés támogatott bővítménnyel.
- **supportingScriptUris**: adja meg a nyilvánosan elérhető URL-címek egy olyan tömbjét, amely támogatja a `ScriptContent` vagy `PrimaryScriptUri`meghívott PowerShell-fájlokat.
- **időtúllépés**: adja meg az [ISO 8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601)megadott maximálisan engedélyezett parancsfájl-végrehajtási időt. Az alapértelmezett érték a **P1D**.
- **cleanupPreference**. Adja meg a telepítési erőforrások törlésének előnyét, ha a parancsfájl végrehajtása terminál állapotba kerül. Az alapértelmezett beállítás **mindig**, ami azt jelenti, hogy a rendszer a terminál állapota (sikeres, sikertelen, megszakított) ellenére törli az erőforrásokat. További információ: [üzembe helyezési parancsfájl erőforrásainak tisztítása](#clean-up-deployment-script-resources).
- **retentionInterval**: adja meg azt az időközt, ameddig a szolgáltatás megtartja a telepítési parancsfájl erőforrásait, miután a telepítési parancsfájl végrehajtása eléri a terminál állapotát. Az üzembe helyezési parancsfájl erőforrásai törlődnek, ha ez az időtartam lejár. Az időtartam az [ISO 8601 minta](https://en.wikipedia.org/wiki/ISO_8601)alapján történik. Az alapértelmezett érték a **P1D**, ami hét napot jelent. Ezt a tulajdonságot akkor használja a rendszer, ha a cleanupPreference értéke *OnExpiration*. A *OnExpiration* tulajdonság jelenleg nincs engedélyezve. További információ: [üzembe helyezési parancsfájl erőforrásainak tisztítása](#clean-up-deployment-script-resources).

## <a name="use-inline-scripts"></a>Beágyazott parancsfájlok használata

A következő sablon egyetlen erőforrással van definiálva a `Microsoft.Resources/deploymentScripts` típussal.

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json?range=1-54)]

> [!NOTE]
> Mivel a beágyazott telepítési parancsfájlok idézőjelek közé vannak ágyazva, az üzembe helyezési parancsfájlokban lévő karakterláncokat csak egyszeres idézőjelek közé kell foglalni. A PowerShell escape-karaktere **&#92;** . Azt is megteheti, hogy a karakterlánc-helyettesítést használja az előző JSON-mintában látható módon. Tekintse meg a name paraméter alapértelmezett értékét.

A parancsfájl egy paramétert fogad, és kiírja a paraméter értékét. A **DeploymentScriptOutputs** a kimenetek tárolására szolgál.  A kimenetek szakaszban az **érték** sorban látható, hogyan férhet hozzá a tárolt értékekhez. a `Write-Output` hibakeresés céljára szolgál. A kimeneti fájl elérésének megismeréséhez tekintse meg az [üzembe helyezési parancsfájlok hibakeresését](#debug-deployment-scripts)ismertető témakört.  A tulajdonságok leírását lásd: [erőforrás-séma](#resource-schema).

A parancsfájl futtatásához válassza a **kipróbálás** lehetőséget a Cloud Shell megnyitásához, majd illessze be a következő kódot a rendszerhéj ablaktáblába.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

A kimenet a következőképpen fog kinézni:

![Resource Manager-sablon üzembe helyezési parancsfájl Hello World output](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Külső parancsfájlok használata

A beágyazott parancsfájlok mellett külső parancsfájlokat is használhat. Jelenleg csak a **ps1** fájlkiterjesztés PowerShell-szkriptek támogatottak. Külső parancsfájlok használatához cserélje le a `scriptContent`t a `primaryScriptUri`ra. Példa:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Ha példát szeretne látni, válassza az [itt](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json)lehetőséget.

A külső parancsfájloknak elérhetőnek kell lenniük.  Az Azure Storage-fiókokban tárolt parancsfájlok biztonságossá tételéhez tekintse meg az [oktatóanyag: összetevők védelme Azure Resource Manager sablon központi telepítése](./template-tutorial-secure-artifacts.md)című témakört.

## <a name="use-supporting-scripts"></a>Támogató parancsfájlok használata

A bonyolult logikai műveleteket egy vagy több támogató parancsfájlba is elkülönítheti. A `supportingScriptURI` tulajdonság lehetővé teszi, hogy szükség esetén az URI-k tömbjét adja meg a támogató parancsfájl-fájlokhoz:

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

A támogató parancsfájlok a beágyazott parancsfájlokból és az elsődleges parancsfájlokból is meghívhatók.

A rendszer a támogató fájlokat a futtatókörnyezet azscripts/azscriptinput másolja. Relatív elérési út használatával hivatkozhat a beágyazott parancsfájlokból és az elsődleges parancsfájlokból származó támogató fájlokra.

## <a name="work-with-outputs-from-deployment-scripts"></a>Az üzembe helyezési parancsfájlok kimenetének használata

A következő sablon bemutatja, hogyan adhat át értékeket két deploymentScripts-erőforrás között:

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-basic.json?range=1-84)]

Az első erőforrásban definiál egy **$DeploymentScriptOutputs**nevű változót, és a kimeneti értékek tárolására használja azt. A sablonban lévő másik erőforrás kimeneti értékének eléréséhez használja a következőt:

```json
reference('<ResourceName>').output.text
```

## <a name="debug-deployment-scripts"></a>Üzembe helyezési parancsfájlok hibakeresése

A parancsfájl-szolgáltatás létrehoz egy [Storage-fiókot](../../storage/common/storage-account-overview.md) és egy tároló- [példányt](../../container-instances/container-instances-overview.md) a parancsfájlok futtatásához. Mindkét erőforrás rendelkezik az **azscripts** utótaggal.

![Resource Manager-sablon telepítési parancsfájljának erőforrásainak nevei](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

A felhasználói parancsfájl, a végrehajtás eredményei és az stdout-fájl a Storage-fiók Files megosztásában tárolódik. Van egy **azscripts**nevű mappa. A mappában két további mappa található a bemeneti és a kimeneti fájlokhoz: **azscriptinput** és **azscriptoutput**.

A kimeneti mappa egy **executionresult. JSON** fájlt és a parancsfájl kimeneti fájlját tartalmazza. A parancsfájl-végrehajtási hibaüzenetet a **executionresult. JSON**fájlban tekintheti meg. A kimeneti fájl csak akkor jön létre, ha a parancsfájl végrehajtása sikeresen megtörtént. A bemeneti mappa egy PowerShell-parancsfájlt és a felhasználói telepítési parancsfájlokat tartalmaz. A felhasználói telepítési parancsfájlt lecserélheti egy módosítottra, majd újra futtathatja az üzembe helyezési parancsfájlt az Azure Container instanceból.

Az üzembe helyezési parancsfájl erőforrás-telepítési információit az erőforráscsoport szintjén és az előfizetés szintjén szerezheti be REST API használatával:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

A következő példa a [ARMClient](https://github.com/projectkudu/ARMClient)használja:

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

Az eredmény az alábbihoz hasonlóan fog kinézni:

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-status.json?range=1-48)]

A kimenet megjeleníti a központi telepítési állapotot, valamint az üzembe helyezési parancsfájl erőforrás-azonosítóit.

A következő REST API a naplót adja vissza:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Ez csak az üzembe helyezési parancsfájl erőforrásainak törlése előtt működik.

Ha meg szeretné tekinteni a deploymentScripts-erőforrást a portálon, válassza a **rejtett típusok megjelenítése**lehetőséget:

![Resource Manager-sablon telepítési parancsfájlja, rejtett típusok megjelenítése, portál](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Üzembe helyezési parancsfájl erőforrásainak karbantartása

Az üzembe helyezési parancsfájl létrehoz egy Storage-fiókot és egy tároló-példányt, amely a telepítési parancsfájlok végrehajtásához és a hibakeresési adatok tárolásához használatos. Ez a két erőforrás ugyanabban az erőforráscsoportban jön létre, mint a kiépített erőforrások, és a parancsfájl a szkript lejárta után törli. Szabályozhatja ezeknek az erőforrásoknak a életciklusát.  Amíg nem törli őket, mindkét erőforrásért díjat kell fizetnie. Az árakra vonatkozó információkért lásd: [Container instances díjszabás](https://azure.microsoft.com/pricing/details/container-instances/) és az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).

Ezeknek az erőforrásoknak a életciklusát a sablon következő tulajdonságai vezérlik:

- **cleanupPreference**: Ha a parancsfájl végrehajtása egy terminál állapotba kerül, a rendszer törli a beállítást.  A támogatott értékek a következők:

  - **Mindig**: törölje az erőforrásokat, ha a parancsfájl végrehajtása terminál állapotba kerül. Mivel a deploymentScripts-erőforrás továbbra is megtalálható az erőforrások tisztítása után, a rendszerparancsfájl a parancsfájlok végrehajtásának eredményeit, például az stdout-t, a kimeneteket, a visszatérési értéket, az erőforrások törlését pedig az ADATBÁZISba másolja.
  - **OnSuccess**: csak akkor törölje az erőforrásokat, ha a parancsfájl végrehajtása sikeres. A hibakeresési adatok megkereséséhez továbbra is hozzáférhet az erőforrásokhoz.
  - **OnExpiration**: csak akkor törölje az erőforrásokat, ha a **retentionInterval** -beállítás lejár. Ez a tulajdonság jelenleg le van tiltva.

- **retentionInterval**: adja meg azt az időintervallumot, ameddig a rendszer megőrzi a parancsfájl-erőforrást, majd azután, hogy lejárt és törölve lesz.

> [!NOTE]
> Az üzembe helyezési parancsfájl erőforrásai más célra való használata nem ajánlott.

## <a name="run-script-more-than-once"></a>Parancsfájl többszöri futtatása

A telepítési parancsfájl végrehajtása egy idempotens művelet. Ha a deploymentScripts erőforrás-tulajdonságok egyike sincs (beleértve a beágyazott parancsfájlt is), a parancsfájl nem lesz végrehajtva a sablon újbóli telepítésekor. Az üzembe helyezési parancsfájl szolgáltatás összehasonlítja a sablonban lévő erőforrás-neveket az ugyanabban az erőforráscsoport meglévő erőforrásaival. Két lehetőség közül választhat, ha ugyanazt az üzembe helyezési parancsfájlt többször szeretné végrehajtani:

- Módosítsa a deploymentScripts-erőforrás nevét. Használja például az [utcNow](./template-functions-string.md#utcnow) -sablon függvényt az erőforrás neve vagy az erőforrás neve részeként. Az erőforrás nevének módosítása új deploymentScripts-erőforrást hoz létre. A parancsfájl-végrehajtás előzményeinek megőrzése jó.

    > [!NOTE]
    > A utcNow függvény csak a paraméter alapértelmezett értékében használható.

- Egy másik értéket kell megadnia a `forceUpdateTag` sablon tulajdonságban.  Használja például a utcNow értéket.

> [!NOTE]
> Írja be a idempotens telepítési parancsfájlokat. Ez biztosítja, hogy ha véletlenül újra futnak, a rendszer nem fog változásokat okozni. Ha például az üzembe helyezési parancsfájl egy Azure-erőforrás létrehozásához használatos, ellenőrizze, hogy az erőforrás nem létezik-e a létrehozása előtt, így a parancsfájl sikeres lesz, vagy nem hozza létre újra az erőforrást.

## <a name="configure-development-environment"></a>A fejlesztési környezet konfigurálása

Az üzembe helyezési parancsfájl jelenleg a 2.7.0, a 2.8.0 és a 3.0.0 Azure PowerShell verzióját támogatja.  Ha rendelkezik Windows-számítógéppel, telepítheti a támogatott Azure PowerShell verziók egyikét, és megkezdheti az üzembe helyezési parancsfájlok fejlesztését és tesztelését.  Ha nem rendelkezik Windows-számítógéppel, vagy ha nem rendelkezik a telepített Azure PowerShell verziók egyikével sem, használhat egy előre konfigurált Docker-tároló rendszerképet. Az alábbi eljárás bemutatja, hogyan konfigurálhatja a Docker-rendszerképet Windows rendszeren. Linux és Mac rendszereken az interneten talál információt.

1. Telepítse a [Docker Desktopot](https://www.docker.com/products/docker-desktop) a fejlesztői számítógépen.
1. Nyissa meg a Docker Desktopot.
1. Válassza ki a Docker Desktop ikont a tálcáról, majd válassza a **Beállítások**lehetőséget.
1. Válassza a **megosztott meghajtók**lehetőséget, válassza ki a tárolók számára elérhető helyi meghajtót, majd kattintson az **alkalmaz** gombra.

    ![Resource Manager-sablon telepítési parancsfájl Docker-meghajtója](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Adja meg a Windows rendszerbeli hitelesítő adatait a parancssorban.
1. Nyisson meg egy terminál ablakot, vagy a parancssort vagy a Windows PowerShellt (ne használja a PowerShell ISE-t).
1. Kérje le a telepítési parancsfájl tárolójának rendszerképét a helyi számítógépre:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    A példa a 2.7.0 verzióját használja.

1. Futtassa helyileg a Docker-rendszerképet.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Cserélje le **&lt;gazdagép-illesztőprogram betűjelét >** és **&lt;gazdagép könyvtárának nevére >** egy meglévő mappát a megosztott meghajtón.  Leképezi a mappát a tároló **/Data** mappájába. Példák a D:\docker leképezésére:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **– Ez azt jelenti, hogy** a tároló rendszerképét életben tartja.

1. Válassza a **megosztás** lehetőséget, amikor megjelenik a kérdés.
1. Futtasson egy PowerShell-parancsfájlt az alábbi képernyőképen látható módon (mivel a d:\docker mappában található HelloWorld. ps1 fájl)

    ![Resource Manager-sablon üzembe helyezési parancsfájl Docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

A PowerShell-parancsfájl sikeres tesztelése után üzembe helyezési parancsfájlként is használható.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan használhatja a telepítési parancsfájlokat. Útmutató az üzembe helyezési parancsfájlhoz:

> [!div class="nextstepaction"]
> [Oktatóanyag: telepítési parancsfájlok használata Azure Resource Manager-sablonokban](./template-tutorial-deployment-script.md)