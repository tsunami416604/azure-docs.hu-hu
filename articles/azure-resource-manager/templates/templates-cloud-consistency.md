---
title: Sablonok újrafelhasználása felhők között
description: Olyan Azure Resource Manager-sablonokat fejleszthet, amelyek egységesen működnek a különböző felhőalapú környezetekben. Hozzon létre új vagy meglévő sablonokat az Azure Stack.
author: marcvaneijk
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18
ms.openlocfilehash: c5095efef5d4bef44993bdd9cd52dbdef17378a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156106"
---
# <a name="develop-arm-templates-for-cloud-consistency"></a>ARM-sablonok kidolgozása a felhőkonvaló konzisztenciához

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Az Azure egyik legfontosabb előnye a konzisztencia. Az egyik helyszín fejlesztési beruházásai egy másik helyen újrafelhasználhatók. Az Azure Resource Manager (ARM) sablon konzisztenssé és megismételhetővé teszi az üzembe helyezéseket a környezetekben, beleértve a globális Azure-t, az Azure szuverén felhőket és az Azure Stacket. A sablonok felhők közötti újrafelhasználásához azonban figyelembe kell vennie a felhőspecifikus függőségeket, ahogy azt ez az útmutató ismerteti.

A Microsoft intelligens, nagyvállalati használatra kész felhőszolgáltatásokat kínál számos helyen, többek között:

* A globális Azure-platform, amelyet a Microsoft által felügyelt adatközpontok egyre növekvő hálózata támogat a világ különböző régióiban.
* Elkülönített szuverén felhők, például az Azure Germany, az Azure Government és az Azure China 21Vianet. A szuverén felhők konzisztens platformot biztosítanak a globális Azure-ügyfelek által elérhető legnagyszerűbb funkciók nagy részével.
* Az Azure Stack, egy hibrid felhőplatform, amely lehetővé teszi az Azure-szolgáltatások nyújtását a szervezet adatközpontjából. A vállalatok saját adatközpontjaikban állíthatják be az Azure Stacket, vagy igénybe vehetik a szolgáltatóktól származó Azure-szolgáltatásokat, és az Azure Stacket üzemeltetik a létesítményeikben (más néven üzemeltetett régiókban).

Az azure resource manager az összes ilyen felhők középpontjában egy API-t biztosít, amely lehetővé teszi a felhasználói felületek széles skáláját az Azure platformmal való kommunikációra. Ez az API hatékony infrastruktúra-mint-kód képességeket biztosít. Bármilyen típusú erőforrás érhető el az Azure felhőplatformon telepíthető és konfigurálható az Azure Resource Manager. Egyetlen sablonnal üzembe helyezheti és konfigurálhatja a teljes alkalmazást egy működési végponti állapotba.

![Azure-környezetek](./media/templates-cloud-consistency/environments.png)

A globális Azure, a szuverén felhők, a hosztolt felhők és az adatközpont felhők konzisztenciája segít az Azure Resource Manager előnyeinek kihasználásában. A sablonalapú erőforrás-telepítés és -konfigurálás beállításakor újra felhasználhatja a fejlesztési beruházásokat ezeken a felhőkön keresztül.

Annak ellenére azonban, hogy a globális, szuverén, üzemeltetett és hibrid felhők konzisztens szolgáltatásokat nyújtanak, nem minden felhő azonos. Ennek eredményeképpen létrehozhat egy sablont, amely csak egy adott felhőben elérhető funkcióktól függ.

Ez az útmutató további ismerteti azokat a területeket, amelyeket figyelembe kell venni, amikor új ARM-sablonok kifejlesztését vagy frissítését tervezi az Azure Stackhez. Az ellenőrzőlistának általában a következő elemeket kell tartalmaznia:

* Ellenőrizze, hogy a sablonban található függvények, végpontok, szolgáltatások és egyéb erőforrások elérhetők-e a céltelepítési helyeken.
* A beágyazott sablonokat és konfigurációs összetevőket akadálymentes helyeken tárolhatja, biztosítva a hozzáférést a felhők között.
* A hivatkozások és elemek kódolása helyett dinamikus hivatkozásokat használjon.
* Győződjön meg arról, hogy a sablon paramétereket használ a munka a célfelhőkben.
* Ellenőrizze, hogy az erőforrás-specifikus tulajdonságok elérhetők-e a célfelhők.

Az ARM-sablonok bemutatása [a Sablon központi telepítése](overview.md)című témakörben olvashat.

## <a name="ensure-template-functions-work"></a>A sablonfüggvények működésének biztosítása

Az ARM-sablon alapvető szintaxisa a JSON. A sablonok a JSON egy sorát használják, kiterjesztve a szintaxist kifejezésekkel és függvényekkel. A sablonnyelvi processzort gyakran frissítik további sablonfüggvények támogatása érdekében. A rendelkezésre álló sablonfüggvények részletes magyarázatát az [ARM sablonfüggvények című témakörben olvashatja.](template-functions.md)

Az Azure Resource Manager bevezetve bevezetett új sablonfüggvények nem érhetők el azonnal a szuverén felhőkben vagy az Azure Stackben. A sablon sikeres üzembe helyezéséhez a sablonban hivatkozott összes függvénynek elérhetőnek kell lennie a célfelhőben.

Az Azure Resource Manager képességei mindig először a globális Azure-ba kerülnek bevezetésre. A következő PowerShell-parancsfájl segítségével ellenőrizheti, hogy az újonnan bevezetett sablonfüggvények is elérhetők-e az Azure Stackben:

1. Készítsen klónt a GitHub-tárházról: [https://github.com/marcvaneijk/arm-template-functions](https://github.com/marcvaneijk/arm-template-functions).

1. Miután rendelkezik a tárház helyi klónjával, csatlakozzon a cél Azure Resource Manager powershell.

1. Importálja a psm1 modult, és hajtsa végre a Test-AzureRmTemplateFunctions parancsmagát:

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

A parancsfájl több, kis méretű sablont telepít, amelyek mindegyike csak egyedi sablonfüggvényeket tartalmaz. A parancsfájl kimenete a támogatott és nem érhető el sablonfüggvényeket jelenti.

## <a name="working-with-linked-artifacts"></a>Csatolt összetevők kelése

A sablonok csatolt összetevőkre mutató hivatkozásokat tartalmazhatnak, és tartalmazhatnak egy másik sablonra mutató telepítési erőforrást. A csatolt sablonokat (más néven beágyazott sablonokat) az Erőforrás-kezelő futásidőben olvassa be. A sablonok a virtuális gép (VM) bővítményei összetevőire mutató hivatkozásokat is tartalmazhatnak. Ezeket a műtermékeket a virtuális gépen belül futó virtuális gép a sablon üzembe helyezése során a virtuális gép bővítménykonfigurációja által lekért rendszerlekéréseket tartalmazza.

A következő szakaszok ismertetik a felhőkontancia szempontjait a fő telepítési sablonon kívüli összetevőket tartalmazó sablonok fejlesztése kor.

### <a name="use-nested-templates-across-regions"></a>Beágyazott sablonok használata a régiók között

A sablonok kis méretű, újrafelhasználható sablonokra bonthatók, amelyek mindegyike meghatározott célt szolgál, és újra felhasználható a központi telepítési forgatókönyvek között. Központi telepítés végrehajtásához meg kell adnia egy sablont, amelyet fő vagy fősablonnak nevezünk. Meghatározza a üzembe helyezhető erőforrásokat, például a virtuális hálózatokat, a virtuális gépeket és a webalkalmazásokat. A fő sablon tartalmazhat egy másik sablonra mutató hivatkozást is, ami azt jelenti, hogy beágyazhatja a sablonokat. Hasonlóképpen a beágyazott sablonok más sablonokra mutató hivatkozásokat is tartalmazhatnak. Legfeljebb öt szint mélyre ágyazható.

A következő kód bemutatja, hogyan hivatkozik a templateLink paraméter egy beágyazott sablonra:

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/vNet.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

Az Azure Resource Manager kiértékeli a fő sablon futásidőben, és lekéri és kiértékeli az egyes beágyazott sablonokat. Az összes beágyazott sablon beolvasása után a sablon összeolvasztásra kerül, és további feldolgozást kezdeményez.

### <a name="make-linked-templates-accessible-across-clouds"></a>Csatolt sablonok elérhetővé tétele a felhők között

Gondolja át, hogy hol és hogyan tárolhatja a használt csatolt sablonokat. Futásidőben az Azure Resource Manager lekéri – és ezért közvetlen hozzáférést igényel – a csatolt sablonokhoz. Általános gyakorlat, hogy a GitHub használatával tárolja a beágyazott sablonokat. A GitHub-tárház olyan fájlokat tartalmazhat, amelyek nyilvánosan érhetők el egy URL-címen keresztül. Bár ez a technika jól működik a nyilvános felhő és a szuverén felhők, az Azure Stack-környezetben lehet, hogy egy vállalati hálózaton vagy egy leválasztott távoli helyen, anélkül, hogy a kimenő internet-hozzáférés. Ezekben az esetekben az Azure Resource Manager nem sikerül beolvasni a beágyazott sablonokat.

A felhőközi telepítések jobb gyakorlata, ha a csatolt sablonokat a célfelhő számára elérhető helyen tárolja. Ideális esetben az összes üzembe helyezési összetevők karbantartása és üzembe helyezése folyamatos integrációs/folyamatos fejlesztési (CI/CD) csővezeték. Másik lehetőségként tárolhatja a beágyazott sablonokat egy blob storage tárolóban, ahonnan az Azure Resource Manager lekérheti őket.

Mivel a blob storage minden felhőben egy másik végpont teljesen minősített tartománynevet (FQDN), konfigurálja a sablont a csatolt sablonok helyét két paraméterrel. A paraméterek a telepítéskor is fogadhatnak felhasználói bevitelt. A sablonokat általában több személy írja le és osztja meg, ezért ajánlott egy szabványos nevet használni ezekhez a paraméterekhez. Az elnevezési konvenciók segítségével a sablonok újrafelhasználhatóbbá tehetők a régiók, felhők és szerzők között.

A következő kódban `_artifactsLocation` egyetlen helyre mutat, amely tartalmazza az összes központi telepítéssel kapcsolatos összetevőt. Figyelje meg, hogy egy alapértelmezett érték van megadva. A telepítéskor, ha nincs megadva `_artifactsLocation`bemeneti érték a számára, a program az alapértelmezett értéket használja. `_artifactsLocationSasToken` Az a bemeneti `sasToken`a. Az alapértelmezett érték nek egy üres karakterláncnak kell lennie olyan esetekben, ahol a `_artifactsLocation` nincs biztosítva – például egy nyilvános GitHub-tárház.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-custom-script-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

A sablonban a csatolások az alap URI `_artifactsLocation` (a paraméterből) és a `_artifactsLocationSasToken`műtermék relatív elérési útjának és a . A következő kód bemutatja, hogyan adható meg a beágyazott sablonra mutató hivatkozás az uri sablonfüggvény használatával:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "name": "shared",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "[uri(parameters('_artifactsLocation'), concat('nested/vnet.json', parameters('_artifactsLocationSasToken')))]",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

Ezzel a megközelítéssel a paraméter `_artifactsLocation` alapértelmezett értékét használja a rendszer. Ha a csatolt sablonokat egy másik helyről kell beolvasni, a paraméterbemenet a központi telepítés idején használható az alapértelmezett érték felülbírálására – magának a sablonnak a módosítására nincs szükség.

### <a name="use-_artifactslocation-instead-of-hardcoding-links"></a>_artifactsLocation használata a merevlemez-kódolási hivatkozások helyett

A beágyazott sablonokhoz való használat mellett `_artifactsLocation` a paraméterben lévő URL-cím a központi telepítési sablon összes kapcsolódó összetevőjének alapjaként szolgál. Egyes virtuálisgép-bővítmények tartalmaznak egy hivatkozást a sablonon kívül tárolt parancsfájlra. Ezeknél a bővítményeknél nem szabad kódolni a hivatkozásokat. Például az egyéni parancsfájl és a PowerShell DSC-bővítmények a GitHubon egy külső parancsfájlra hivatkozhatnak, ahogy az látható:

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
    ]
  }
}
```

A parancsfájlra mutató hivatkozások kemény kódolása megakadályozhatja, hogy a sablon sikeresen egy másik helyre telepítsen. A virtuális gép erőforrás konfigurálása során a virtuális gép ben futó virtuális gép a virtuális gép bővítményben hivatkozott összes parancsfájl letöltését kezdeményezi, majd a parancsfájlokat a virtuális gép helyi lemezén tárolja. Ez a megközelítés úgy működik, mint a beágyazott sablonhivatkozások, amelyeket korábban a "Beágyazott sablonok használata régiók között" című szakaszban ismertetett.

Az Erőforrás-kezelő futásidőben olvassa be a beágyazott sablonokat. Virtuálisgép-bővítmények esetén a külső összetevők lekérését a virtuálisgép-ügynök végzi. A műtermék-lekérés különböző kezdeményezője mellett a sablondefinícióban szereplő megoldás ugyanaz. Használja a _artifactsLocation paramétert az alapelérési út alapértelmezett értékével, ahol az összes összetevő tárolódik (beleértve a virtuálisgép-bővítmény parancsfájljait) és a `_artifactsLocationSasToken` sasToken bemeneti paraméterét.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

Egy műtermék abszolút URI-értékének létrehozásához az előnyben részesített módszer az uri sablon függvény használata a concat sablon függvény helyett. A virtuális gép bővítményében lévő parancsfájlokra mutató, kódolva lévő hivatkozások lecserélésével az uri sablon függvényével, a sablonban ez a funkció felhőbeli konzisztenciára van konfigurálva.

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "[uri(parameters('_artifactsLocation'), concat('scripts/configure-music-app.ps1', parameters('_artifactsLocationSasToken')))]"
    ]
  }
}
```

Ezzel a megközelítéssel az összes telepítési összetevők, beleértve a konfigurációs parancsfájlok, tárolhatók ugyanazon a helyen a sablon maga. Az összes hivatkozás helyének módosításához csak egy másik alap URL-címet kell megadnia az _összetevőkhely paramétereihez._

## <a name="factor-in-differing-regional-capabilities"></a>Tényező a különböző regionális képességekben

Az Azure-ba bevezetett frissítések és új szolgáltatások agilis fejlesztésével és folyamatos folyamatával a [régiók eltérhetnek](https://azure.microsoft.com/regions/services/) a szolgáltatások és frissítések rendelkezésre állásában. Szigorú belső tesztelés után az érvényesítési programban részt vevő ügyfelek kis közönsége általában új szolgáltatásokat vagy frissítéseket vezet be a meglévő szolgáltatásokhoz. A sikeres ügyfél-érvényesítést követően a szolgáltatások vagy frissítések elérhetővé válikk az Azure-régiók egy alcsoportján belül, majd több régióban is elérhetővé válikk, megjelennek a szuverén felhőkben, és potenciálisan elérhetővé tették az Azure Stack-ügyfelek számára is.

Annak tudatában, hogy az Azure-régiók és -felhők eltérhetnek a rendelkezésre álló szolgáltatások, hozhat néhány proaktív döntéseket a sablonokat. Egy jó kiindulópont lehet a felhőhöz rendelkezésre álló erőforrás-szolgáltatók vizsgálatával. Az erőforrás-szolgáltató azt mondja, hogy az Azure-szolgáltatás hoz rendelkezésre álló erőforrások és műveletek készlete.

A sablonok erőforrásokat telepítenek és konfigurálnak. Az erőforrástípust egy erőforrás-szolgáltató biztosítja. Például a számítási erőforrás-szolgáltató (Microsoft.Compute), több erőforrástípust biztosít, például a virtualMachines és availabilitySets. Minden erőforrás-szolgáltató egy közös szerződés sel definiált API-t biztosít az Azure Resource Manager számára, amely egységes, egységes szerzői élményt biztosít az összes erőforrás-szolgáltató számára. Előfordulhat azonban, hogy a globális Azure-ban elérhető erőforrás-szolgáltató nem érhető el egy szuverén felhőben vagy egy Azure Stack-régióban.

![Erőforrás-szolgáltatók](./media/templates-cloud-consistency/resource-providers.png)

Az adott felhőben elérhető erőforrás-szolgáltatók ellenőrzéséhez futtassa a következő parancsfájlt az Azure parancssori felületén ([CLI):](/cli/azure/install-azure-cli)

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

A következő PowerShell-parancsmag használatával is megtekintheti az elérhető erőforrás-szolgáltatókat:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>Az összes erőforrástípus verziójának ellenőrzése

A tulajdonságok készlete minden erőforrástípushoz közös, de minden erőforrásnak saját adott tulajdonságai is vannak. Az új szolgáltatások és a kapcsolódó tulajdonságok időnként egy új API-verzión keresztül kerülnek a meglévő erőforrástípusokba. A sablonban lévő erőforrások saját API-verziótulajdonsággal rendelkeznek – `apiVersion`. Ez a verziószámozás biztosítja, hogy a sablon meglévő erőforrás-konfigurációját ne befolyásolják a platform változásai.

Előfordulhat, hogy a globális Azure-ban meglévő erőforrástípusokhoz bevezetett új API-verziók nem érhetők el azonnal minden régióban, szuverén felhőben vagy Az Azure Stackben. A felhőhöz elérhető erőforrás-szolgáltatók, erőforrástípusok és API-verziók listájának megtekintéséhez használhatja az Azure Portalon az Erőforrás-kezelőt. Keresés az Erőforrás-kezelő között a Minden szolgáltatás menüben. Bontsa ki a Szolgáltatók csomópontot az Erőforrás-kezelőben, hogy az összes rendelkezésre álló erőforrás-szolgáltatót, erőforrástípusát és API-verzióját visszaadja az adott felhőben.

Az Azure CLI adott felhőjében az összes erőforrástípus elérhető API-verziójának listázásához futtassa a következő parancsfájlt:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

A következő PowerShell-parancsmag is használható:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Tekintse meg a paraméterrel rendelkező erőforráshelyeket

A sablon mindig egy régióban található erőforráscsoportba van telepítve. Maga a központi telepítés mellett a sablonban lévő minden erőforrás rendelkezik egy helytulajdonsággal is, amely a telepíthető régió megadásához használható. A sablon felhőkonzisztencia fejlesztéséhez dinamikus módon kell hivatkoznia az erőforrás-helyekre, mivel minden Azure Stack tartalmazhat egyedi helyneveket. Általában erőforrások üzembe helyezése ugyanabban a régióban, mint az erőforráscsoport, de a forgatókönyvek, például a régiók közötti alkalmazások elérhetőségét, hasznos lehet az erőforrások régiók közötti elosztásához.

Annak ellenére, hogy a régióneveket kódolhatja, amikor megadja az erőforrás-tulajdonságokat egy sablonban, ez a megközelítés nem garantálja, hogy a sablon telepíthető más Azure Stack-környezetekben, mert a régió neve valószínűleg nem létezik.

A különböző régiók befogadásához adjon hozzá egy bemeneti paraméter helyét az alapértelmezett értékkel rendelkező sablonhoz. Ha a központi telepítés során nincs megadva érték, a rendszer az alapértelmezett értéket használja.

A sablonfüggvény `[resourceGroup()]` a következő kulcs-/értékpárokat tartalmazó objektumot adja vissza:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

A bemeneti paraméter defaultValue objektumának helykulcsára hivatkozva az Azure Resource Manager futásidőben `[resourceGroup().location]` lecseréli a sablonfüggvényt annak az erőforráscsoportnak a helyére, amelybe a sablon telepítve van.

```json
"parameters": {
  "location": {
    "type": "string",
    "metadata": {
      "description": "Location the resources will be deployed to."
    },
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    ...
```

Ezzel a sablonfunkcióval a sablont bármely felhőbe telepítheti anélkül, hogy előre ismerné a régióneveket. Ezenkívül a sablonban lévő adott erőforrás helye eltérhet az erőforráscsoport helyétől. Ebben az esetben az adott erőforráshoz további bemeneti paraméterek használatával konfigurálható, míg az ugyanabban a sablonban lévő többi erőforrás továbbra is a kezdeti helybeviteli paramétert használja.

### <a name="track-versions-using-api-profiles"></a>Verziók nyomon követése API-profilok használatával

Nagy kihívást jelenthet az Azure Stackben található összes rendelkezésre álló erőforrás-szolgáltató és kapcsolódó API-verzió nyomon követése. Például az írás időpontjában a **Microsoft.Compute/availabilitySets** legújabb API-verziója az Azure-ban, `2018-04-01`míg az `2016-03-30`Azure és az Azure Stack számára közös API-verzió a. A **Microsoft.Storage/storageAccounts** általános API-verziója az Azure és `2016-01-01`az Azure Stack összes `2018-02-01`helye között a , míg az Azure legújabb API-verziója a .

Ezért az Erőforrás-kezelő bevezette az API-profilok fogalmát a sablonokba. API-profilok nélkül a sablon minden egyes `apiVersion` erőforrása egy olyan elemmel van konfigurálva, amely leírja az adott erőforrás API-verzióját.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "metadata": {
          "description": "Location the resources will be deployed to."
      },
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "name": "mystorageaccount",
      "location": "[parameters('location')]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    },
    {
      "type": "Microsoft.Compute/availabilitySets",
      "apiVersion": "2016-03-30",
      "name": "myavailabilityset",
      "location": "[parameters('location')]",
      "properties": {
        "platformFaultDomainCount": 2,
        "platformUpdateDomainCount": 2
      }
    }
  ],
  "outputs": {}
}
```

Az API-profil verziója az Azure és az Azure Stack számára közös erőforrástípusonként egyetlen API-verzió aliasaként működik. Ahelyett, hogy egy sablon minden egyes erőforrásához api-verziót ad meg, `apiProfile` csak az `apiVersion` API-profil verziót adja meg egy új gyökérelemben, és hagyja ki az egyes erőforrások elemét.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

Az API-profil biztosítja, hogy az API-verziók elérhetők a helyek között, így nem kell manuálisan ellenőrizni e apiVersions, amelyek egy adott helyen érhetők el. Annak érdekében, hogy az API-profil által hivatkozott API-verziók egy Azure Stack-környezetben legyenek jelen, az Azure Stack-operátorok a támogatási szabályzat alapján naprakészen kell tartaniuk a megoldást. Ha egy rendszer több mint hat hónappal elavult, akkor a rendszer nem felel meg, és a környezetet frissíteni kell.

Az API-profil nem szükséges elem egy sablonban. Még ha hozzá is adja az elemet, akkor `apiVersion` is csak olyan erőforrásokhoz lesz használva, amelyekhez nincs megadva. Ez az elem lehetővé teszi a fokozatos módosításokat, de nem igényel módosításokat a meglévő sablonokon.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

## <a name="check-endpoint-references"></a>Végpont-hivatkozások ellenőrzése

Az erőforrások a platformon lévő más szolgáltatásokra mutató hivatkozásokat is tartalmaznak. Egy nyilvános IP-címhez például nyilvános DNS-név rendelhető. A nyilvános felhő, a szuverén felhők és az Azure Stack-megoldások saját külön végpontnévterekkel rendelkeznek. A legtöbb esetben egy erőforrás csak egy előtagot igényel a sablon bemeneteként. Futásidőben az Azure Resource Manager hozzáfűzi a végpont értékét. Néhány végpontértéket explicit módon meg kell adni a sablonban.

> [!NOTE]
> A felhőbeli konzisztencia sablonjainak fejlesztéséhez ne kódolja a végpontnévtereket.

Az alábbi két példa olyan gyakori végpontnévterek, amelyeket az erőforrás létrehozásakor explicit módon meg kell adni:

* Tárfiókok (blob, várólista, tábla és fájl)
* Kapcsolati karakterláncok adatbázisokhoz és Azure Cache for Redis

A végpontnévterek a sablon kimenetében is használhatók a felhasználó számára a központi telepítés befejezésekor. Az alábbi példák gyakoriak:

* Tárfiókok (blob, várólista, tábla és fájl)
* Kapcsolati karakterláncok (MySql, SQLServer, SQLAzure, Custom, NotificationHub, ServiceBus, EventHub, ApiHub, DocDb, RedisCache, PostgreSQL)
* Traffic Manager
* nyilvános IP-cím domainNameLabel címkéje
* Felhőszolgáltatások

Általában kerülje a sablon ban lévő kódolt végpontokat. Az ajánlott eljárás a referenciasablon függvény használata a végpontok dinamikus beolvasásához. Például a végpont leggyakrabban hardcoded a tárfiókok végpontnévtere. Minden tárfiók rendelkezik egy egyedi fqdn, amely úgy épül fel, hogy összefűzi a nevét a tárfiók a végpont névtér. A mystorageaccount1 nevű blobstorage-fiók a felhőtől függően különböző teljes tartománynokat eredményez:

* **mystorageaccount1.blob.core.windows.net,** amikor a globális Azure-felhőben jön létre.
* **mystorageaccount1.blob.core.chinacloudapi.cn** az Azure China 21Vianet felhőben történő létrehozásakor.

A következő referenciasablon-függvény lekéri a végpontnévteret a tárolóerőforrás-szolgáltatótól:

```json
"diskUri":"[concat(reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))).primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

A tárfiók végpontjának kódolt értékét `reference` a sablonfüggvénnyel lecserélve használhatja ugyanazt a sablont a különböző környezetekbe való sikeres üzembe helyezéshez anélkül, hogy módosítaná a végponthivatkozást.

### <a name="refer-to-existing-resources-by-unique-id"></a>A meglévő erőforrások hivatkozása egyedi azonosító szerint

Hivatkozhat egy meglévő erőforrásra ugyanabból vagy egy másik erőforráscsoportból, és ugyanazon az előfizetésen vagy egy másik előfizetésen belül, ugyanazon a bérlőn belül ugyanabban a felhőben. Az erőforrás tulajdonságainak beolvasásához magának az erőforrásnak az egyedi azonosítóját kell használnia. A `resourceId` sablonfüggvény egy erőforrás, például az SQL Server egyedi azonosítóját olvassa be, ahogy azt a következő kód mutatja:

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

Ezután a `resourceId` `reference` sablonfüggvényen belüli függvénnyel lekérheti az adatbázis tulajdonságait. A visszáruobjektum a `fullyQualifiedDomainName` teljes végpontértéket tartalmazó tulajdonságot tartalmazza. Ez az érték futásidőben történik, és biztosítja a felhőkörnyezet-specifikus végpontnévteret. Ha a végpontnévtér hardcoding nélküli kapcsolati karakterláncát szeretné definiálni, a visszatérési objektum tulajdonságára közvetlenül a kapcsolati karakterláncban hivatkozhat, ahogy az látható:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Erőforrás-tulajdonságok mérlegelése

Az Azure Stack-környezeteken belüli erőforrások egyedi tulajdonságokkal rendelkeznek, amelyeket figyelembe kell vennie a sablonban.

### <a name="ensure-vm-images-are-available"></a>A virtuális géplemezei elérhetővé

Az Azure a virtuális géplemezképek gazdag választékát kínálja. Ezeket a lemezképeket a Microsoft és a partnerek hozták létre és készítették elő a központi telepítéshez. A lemezképek képezik a platformon lévő virtuális gépek alapját. Azonban egy felhő-konzisztens sablon csak a rendelkezésre álló paraméterekre vonatkozik, különösen a közzétevő, az ajánlat és a virtuális gép lemezképeinek termékváltozatára, amelyek a globális Azure, az Azure szuverén felhők vagy egy Azure Stack-megoldás számára érhetők el.

A rendelkezésre álló virtuálisgép-lemezképek listájának lekéréséhez futtassa a következő Azure CLI parancsot:

```azurecli-interactive
az vm image list -all
```

Ugyanazt a listát az Azure PowerShell-parancsmagget-AzureRmVMImagePublisher segítségével, és `-Location` megadhatja a kívánt helyet a paraméterrel. [Get-AzureRmVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) Példa:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

Ez a parancs néhány percet vesz igénybe a globális Azure-felhő nyugat-európai régiójában elérhető összes elérhető kép visszaküldése.

Ha ezeket a virtuális géplemezképeket elérhetővé tette az Azure Stack számára, az összes rendelkezésre álló tárterület felhasználásra kerül. Még a legkisebb méretű egység befogadására, az Azure Stack lehetővé teszi, hogy válassza ki a környezethez hozzáadni kívánt képeket.

A következő kódminta egy konzisztens megközelítést mutat be a közzétevő, az ajánlat és a Termékváltozat paramétereire való hivatkozáshoz az ARM-sablonokban:

```json
"storageProfile": {
    "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2016-Datacenter",
    "version": "latest"
    }
}
```

### <a name="check-local-vm-sizes"></a>A helyi virtuális gépméretek ellenőrzése

A sablon felhőbeli konzisztencia fejlesztéséhez győződjön meg arról, hogy a virtuális gép kívánt mérete minden célkörnyezetben elérhető. A virtuális gép méretei a teljesítményjellemzők és képességek csoportosítása. Egyes virtuális gép méretek attól függ, hogy a hardver, amelyen a virtuális gép fut. Ha például egy GPU-ra optimalizált virtuális gép telepítéséhez szükséges, a hipervizort futtató hardvernek rendelkeznie kell a hardver GPU-kkal.

Amikor a Microsoft bevezet egy új méretű virtuális gép, amely bizonyos hardverfüggőségek, a virtuális gép mérete általában elérhetővé válik először egy kis régiók az Azure-felhőben. Később más régiók és felhők számára is elérhetővé válik. Annak érdekében, hogy a virtuális gép mérete minden felhőben létezik, a rendelkezésre álló méretek a következő Azure CLI paranccsal:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Az Azure PowerShell használatához használja:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

Az elérhető szolgáltatások teljes listáját a [Régiónként elérhető Termékek (Termékek) (Termékek régiónként) (Termékek régiónként) (Termékek elérhető ) ( Termék elérhető](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable).

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Az Azure felügyelt lemezeinek használatának ellenőrzése az Azure Stackben

Felügyelt lemezek kezeli a tároló egy Azure-bérlő. Ahelyett, hogy explicit módon létrehozna egy tárfiókot, és megadná a virtuális merevlemez (VHD) URI-ját, a felügyelt lemezek segítségével implicit módon végrehajthatja ezeket a műveleteket, amikor virtuális gépet telepít. A felügyelt lemezek növelik a rendelkezésre állást azáltal, hogy a virtuális gépekösszes lemezét ugyanabban a rendelkezésre állási csoportban helyezik el különböző tárolóegységekbe. Emellett a meglévő Virtuálisgépek standardról prémium szintű tárterületre konvertálhatók lényegesen kevesebb állásidővel.

Bár a felügyelt lemezek az Azure Stack ütemtervét, jelenleg nem támogatottak. Amíg ezek nem, felhő-konzisztens sablonokat fejleszthet az Azure `vhd` Stackhez, ha explicit módon adja meg a virtuális gépeket a virtuális gép erőforrás sablonjának elemével, ahogy az látható:

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "name": "osdisk",
    "vhd": {
      "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
    },
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Ezzel szemben a felügyelt lemez konfigurációjának sablonban való megadásához távolítsa el az `vhd` elemet a lemezkonfigurációból.

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Ugyanezek a változások vonatkoznak [az adatlemezekre](../../virtual-machines/windows/using-managed-disks-template-deployments.md)is .

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Annak ellenőrzése, hogy a virtuálisgép-bővítmények elérhetők-e az Azure Stackben

A felhőkonzisztencia egy másik szempont a [virtuálisgép-bővítmények](../../virtual-machines/windows/extensions-features.md) használata a virtuális gépen belüli erőforrások konfigurálásához. Nem minden virtuálisgép-bővítmény érhető el az Azure Stackben. A sablon megadhatja a virtuális gép bővítményhez dedikált erőforrásokat, függőségeket és feltételeket hozva létre a sablonon belül.

Ha például Microsoft SQL Server t futtató virtuális gép konfigurálását szeretné, a Virtuálisgép-bővítmény a sablon központi telepítésének részeként konfigurálhatja az SQL Servert. Vegye figyelembe, mi történik, ha a központi telepítési sablon egy olyan alkalmazáskiszolgálót is tartalmaz, amely úgy van beállítva, hogy adatbázist hozzon létre az SQL Server t futtató virtuális gépen. Amellett, hogy az alkalmazáskiszolgálók virtuálisgép-bővítményt is használ, konfigurálhatja az alkalmazáskiszolgáló függőségét az SQL Server virtuálisgép-bővítmény erőforrás sikeres visszatérésénél. Ez a megközelítés biztosítja, hogy az SQL Server t futtató virtuális gép konfigurálva legyen, és elérhető legyen, amikor az alkalmazáskiszolgálónak az adatbázis létrehozására van utasítva.

A sablon deklaratív megközelítése lehetővé teszi az erőforrások és azok közötti függőségek végső állapotának meghatározását, míg a platform gondoskodik a függőségekhez szükséges logikáról.

#### <a name="check-that-vm-extensions-are-available"></a>Annak ellenőrzése, hogy rendelkezésre állnak-e virtuálisgép-bővítmények

A virtuálisgép-bővítmények számos típusa létezik. A felhőkonzisztencia sablonjának fejlesztése kor győződjön meg arról, hogy csak a sablon által megcélult régiókban elérhető bővítményeket használja.

Egy adott régióhoz elérhető virtuálisgép-bővítmények listájának beolvasásához (ebben a `myLocation`példában) futtassa a következő Azure CLI parancsot:

```azurecli-interactive
az vm extension image list --location myLocation
```

Az Azure PowerShell [Get-AzureRmVmImagePublisher parancsmagja](/powershell/module/az.compute/get-azvmimagepublisher) is `-Location` futtatható, és a virtuálisgép-lemezkép helyének megadásához használható. Példa:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Annak ellenőrzése, hogy a verziók elérhetők-e

Mivel a virtuálisgép-bővítmények első féltől származó Erőforrás-kezelő erőforrások, saját API-verziókkal rendelkeznek. Ahogy a következő kód mutatja, a virtuális gép bővítmény típusa egy beágyazott erőforrás a Microsoft.Compute erőforrás-szolgáltató.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2015-06-15",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

A virtuálisgép-bővítmény erőforrás API-verziójának jelen kell lennie a sablonnal megcélzott összes helyen. A helyfüggőség úgy működik, mint az erőforrás-szolgáltató API-verziójának elérhetősége, amelyet korábban tárgyalt a "Minden erőforrástípus verziójának ellenőrzése" című szakaszban.

A virtuálisgép-bővítmény erőforrás elérhető API-verzióinak listájának beolvasásához használja a [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) parancsmalapot a **Microsoft.Compute** erőforrás-szolgáltatóval az alábbi módon:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

Virtuálisgép-bővítmények virtuálisgép-méretezési csoportokban is használhatja. Ugyanazok a helyfeltételek érvényesek. A sablon felhőkonzisztencia fejlesztéséhez győződjön meg arról, hogy az API-verziók minden olyan helyen elérhetők, ahol telepíteni szeretne. A virtuálisgép-bővítmény erőforrás API-verzióinak méretezési készletekhez való beolvasásához használja ugyanazt a parancsmalapot, mint korábban, de adja meg a virtuálisgép-méretezési készlet erőforrástípusát az ábrán látható módon:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Minden egyes bővítmény is verziószámozott. Ez a verzió `typeHandlerVersion` a virtuális gép bővítménytulajdonságában jelenik meg. Győződjön meg arról, `typeHandlerVersion` hogy a sablon virtuálisgép-bővítményeinek elemében megadott verzió elérhető azokon a helyeken, ahol a sablont telepíteni kívánja. A következő kód például az 1.7-es verziót határozza meg:

```json
{
    "type": "extensions",
    "apiVersion": "2016-03-30",
    "name": "MyCustomScriptExtension",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        ...
```

Egy adott virtuálisgép-bővítmény elérhető verzióinak listájának lekéréséhez használja a [Get-AzureRmVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage) parancsmag. A következő példa a PowerShell DSC (Desired State Configuration) virtuálisgép-bővítmény elérhető verzióit olvassa be a **myLocation helyről:**

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

A közzétevők listájának lefelvételéhez használja a [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) parancsot. A kérelem típusa, használja a [Get-AzureRmVMExtensionImageType](/powershell/module/az.compute/get-azvmextensionimagetype) dicséret.

## <a name="tips-for-testing-and-automation"></a>Tippek a teszteléshez és az automatizáláshoz

A sablon készítése során fontos nyomon követni az összes kapcsolódó beállítást, képességet és korlátozást. A közös megközelítés az, hogy a sablonok fejlesztése és tesztelése egyetlen felhő, mielőtt más helyeken is célzott. Azonban minél korábban, hogy a tesztek végrehajtása a szerzői folyamat, annál kevesebb hibaelhárítási és kódátírás a fejlesztői csapat kell tennie. A helyfüggőségek miatt sikertelen központi telepítések hibaelhárítása időigényes lehet. Ezért javasoljuk az automatikus tesztelést a szerzői ciklus lehető leghamarabb. Végső soron kevesebb fejlesztési időre és kevesebb erőforrásra lesz szüksége, és a felhőben konzisztens összetevők még értékesebbé válnak.

Az alábbi képen egy tipikus példa egy integrált fejlesztői környezetet (IDE) használó csapat fejlesztési folyamatára mutat be. Az idővonal különböző szakaszaiban a különböző teszttípusok végrehajtása történik. Itt két fejlesztő dolgozik ugyanazon a megoldáson, de ez a forgatókönyv egyaránt vonatkozik egyetlen fejlesztőre vagy egy nagy csapatra. Minden fejlesztő általában létrehoz egy helyi másolatot a központi tárház, amely lehetővé teszi, hogy mindegyik dolgozni a helyi másolatot anélkül, hogy befolyásolnák a többiek, akik esetleg dolgoznak ugyanazon a fájlon.

![Munkafolyamat](./media/templates-cloud-consistency/workflow.png)

Vegye figyelembe az alábbi tippeket a teszteléshez és az automatizáláshoz:

* Ne használja a vizsgálati eszközök. A Visual Studio Code és a Visual Studio például az IntelliSense-t és más funkciókat is tartalmaz, amelyek segíthetnek a sablonok érvényesítésében.
* A kód minőségének javítása érdekében a fejlesztés során a helyi IDE,végezzen statikus kódelemzés egységtesztek és integrációs tesztek.
* A kezdeti fejlesztés során még jobb élmény érdekében az egységteszteknek és az integrációs teszteknek csak akkor kell figyelmeztetniük, ha problémát találnak, és folytathatják a teszteket. Így azonosíthatja a megoldandó problémákat, és rangsorolhatja a módosítások sorrendjét, más néven tesztvezérelt telepítést (TDD).
* Ne feledje, hogy egyes tesztek elvégezhetők anélkül, hogy csatlakozna az Azure Resource Managerhez. Mások, például a sablon telepítésének tesztelése, megkövetelik az Erőforrás-kezelőtől, hogy bizonyos műveleteket hajtson végre, amelyek nem hajthatók végre kapcsolat nélkül.
* Egy központi telepítési sablon tesztelése az érvényesítési API-val nem egyenlő egy tényleges központi telepítéssel. Továbbá, még akkor is, ha egy sablont egy helyi fájlból telepít, a sablonbeágyazott sablonokra mutató hivatkozásokat közvetlenül az Erőforrás-kezelő olvassa be, és a virtuálisgép-bővítmények által hivatkozott összetevőket az üzembe helyezett virtuális gépen futó virtuálisgép-ügynök olvassa be.

## <a name="next-steps"></a>További lépések

* [Az Azure Resource Manager sablonokkal kapcsolatos szempontok](/azure-stack/user/azure-stack-develop-templates)
* [Gyakorlati tanácsok arm sablonokhoz](template-syntax.md)
