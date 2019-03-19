---
title: Újból felhasználhatja a sablonok felhőkben – Azure Resource Manager
description: Fejlesztés az Azure Resource Manager-sablonok, amelyek különböző felhőalapú környezetek konzisztens módon működnek. Új létrehozása vagy meglévő sablonok frissítése az Azure Stackhez.
services: azure-resource-manager
documentationcenter: na
author: marcvaneijk
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18
ms.openlocfilehash: 4b1c829a417d050b4d931611d9f2952e01582f04
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58089471"
---
# <a name="develop-azure-resource-manager-templates-for-cloud-consistency"></a>Fejlesztés a felhőben konzisztencia az Azure Resource Manager-sablonokkal

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Az Azure egyik legfőbb előnye konzisztencia. Fejlesztési befektetéseit egy helyen egy másik újrafelhasználható. A sablon lehetővé teszi az üzemelő példányok egységes és megismételhető környezetekben, beleértve a globális Azure, Azure szuverén felhőkben és az Azure Stack. Újból felhasználhatja a sablonok felhőkben, azonban szüksége felhőspecifikus függőségek érdemes figyelembe venni, ahogy ez az útmutató ismerteti.

A Microsoft számos különféle helyen, beleértve az intelligens, nagyvállalati használatra kész felhőalapú szolgáltatásokat kínál:

* A globális Azure által támogatott platform régiókban világszerte, a Microsoft által felügyelt adatközpontok egyre növekvő hálózatában.
* Elkülönített független felhőkben, mint az Azure Germany, az Azure Government és Azure China (az Azure 21Vianet által üzemeltetett). Szuverén felhőkben a nagyszerű funkcióját, amely a globális Azure-ügyfelek hozzáférhetnek a legtöbb olyan egységes platformot biztosít.
* Az Azure Stack, hibrid felhőplatform, amely lehetővé teszi Azure-szolgáltatások továbbítására a szervezet adatközpontjából. Vállalatok állítsa be az Azure Stack saját adatközpontokban, vagy Azure-szolgáltatásainak használata a szolgáltatók futó Azure Stack saját létesítményekben (más néven üzemeltetett régiók).

Ezek a felhők középpontjában az Azure Resource Manager biztosít egy API-t, amely lehetővé teszi számos különböző felhasználói felületek és az Azure platform közötti kommunikációra. Ez az API hatékony infrastruktúra mint kód képességeket biztosít. Bármilyen típusú erőforrások, amelyeket az Azure felhőalapú platformon érhető el telepíthető és az Azure Resource Manager konfigurálva. Egyetlen sablonnal telepítheti és konfigurálhatja a teljes alkalmazás teljes működési állapotot.

![Azure-környezetek](./media/templates-cloud-consistency/environments.png)

A konzisztencia, a globális Azure-ban, a független felhőkben üzemeltetett felhők, és az adatközpontban található egy felhő segít az Azure Resource Manager előnyeit. Ezek a felhők közötti fejlesztési befektetéseit felhasználhatja az erőforrás sablonalapú üzembe helyezését és konfigurálását beállításakor.

Azonban annak ellenére, hogy a globális, független, üzemeltetett, és a hibrid felhők konzisztens szolgáltatásokat nyújtanak, nincs minden felhő azonosak. Ennek eredményeképpen csak egy adott felhőben elérhető funkciók a függőségeket tartalmazó sablon is létrehozhat.

Ez az útmutató többi ismerteti a tervezésekor az Azure Stackhez készült új vagy frissíteni a meglévő Azure Resource Manager-sablonok fejlesztése területeken. A feladatlista általában a következő elemeket kell tartalmaznia:

* Győződjön meg arról, hogy az funkciók, a végpontok, szolgáltatások és egyéb erőforrások a sablonban érhető el a központi telepítés célhelyet.
* Store beágyazott sablonok és összetevők konfigurációs hozzáférhető helyen, a felhők közötti hozzáférés biztosítása érdekében.
* Használja a dinamikus hivatkozások fix kódolása hivatkozások és elemek helyett.
* Győződjön meg arról, a sablon paramétereit, használja a cél-felhő működik.
* Győződjön meg arról, hogy az erőforrás-specifikus tulajdonságok állnak rendelkezésre az cél-felhőkben.

Bevezetés az Azure Resource Manager-sablonok, lásd: [sablonalapú telepítés](resource-group-overview.md#template-deployment).

## <a name="ensure-template-functions-work"></a>Győződjön meg, hogy a sablonokban használható függvények használata

A Resource Manager-sablonnal alapvető szintaxisa JSON. Sablonok használata felülbírálja a JSON-t, a szintaxist kifejezések és függvények a kiterjesztése. A sablon nyelvi processzor gyakran frissül a további funkciók támogatásához. A rendelkezésre álló sablonokban használható függvények részletes leírását lásd: [Azure Resource Manager-sablonfüggvények](resource-group-template-functions.md).

Az Azure Resource Manager bevezetett új sablonokban használható függvények nem azonnal elérhetővé válik a független felhőkben vagy az Azure Stacken. Egy sablon sikeres üzembe helyezéséhez a sablonban hivatkozott összes függvényt a célfelhő kell érhető el. 

Azure Resource Manager-képességek mindig vezetjük be globális Azure-ban először. A következő PowerShell-parancsfájl segítségével győződjön meg arról, hogy újonnan bevezetett sablonfüggvények is elérhetők az Azure Stack: 

1. Győződjön meg arról, a GitHub-adattár klónja: [ https://github.com/marcvaneijk/arm-template-functions ](https://github.com/marcvaneijk/arm-template-functions).

1. Ha az adattár helyi klónjával, csatlakozzon a cél Azure Resource Manager PowerShell használatával.

1. Importálja a modult a psm1 kiterjesztésű, és hajtsa végre a Test-AzureRmureRmTemplateFunctions parancsmagot:

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

A szkript üzembe helyezi a több, sablonok, csak az egyedi sablonfüggvények tartalmazó kis méretben. A szkript a kimenetét a támogatott és nem érhető el a sablonokban használható függvények jelentések.

## <a name="working-with-linked-artifacts"></a>A csatolt összetevők használata

A sablon társított összetevők hivatkozásokat tartalmaznak, és egy másik sablonba összekapcsoló üzembe helyezési erőforrás tartalmazhat. Resource Manager által futásidőben (más néven beágyazott sablont) a csatolt sablonok beolvasása. A sablon virtuális gép (VM) bővítmények összetevők mutató hivatkozásokat is tartalmazhat. Ezek az összetevők által a virtuális Gépet a VM-bővítmény konfigurációja a sablon üzembe helyezése során a futtatott Virtuálisgép-bővítmény beolvasása. 

A következő szakaszok ismertetik a felhőalapú konzisztencia szempontjai a fő központi telepítési sablont a külső összetevők-sablonok fejlesztése során.

### <a name="use-nested-templates-across-regions"></a>Beágyazott sablonokkal régióban

Sablonok bontható kicsi, újrafelhasználható sablonok, amelyek mindegyike egy adott céllal rendelkezik, és központi telepítési forgatókönyv felhasználhatók. Központi telepítés végrehajtásához adja meg a fő vagy a fő sablont néven egyetlen sablonban. Azt adja meg az erőforrásokat, például a virtuális hálózatok, virtuális gépek és webalkalmazások üzembe helyezéséhez. A fő sablont egy másik sablont, ami azt jelenti, beágyazhatja a sablonok mutató hivatkozást is tartalmazhat. Hasonlóképpen egy beágyazott sablont más sablonokra mutató hivatkozásokat is tartalmazhat. Legfeljebb öt szintnél mélyebb ágyazhatja be.

A következő kód bemutatja, hogyan a templateLink paraméter mutat egy beágyazott sablont:

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
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

Az Azure Resource Manager kiértékeli a futásidőben a fő sablont, és kéri le, és minden beágyazott sablon kiértékeli. Amikor az összes beágyazott sablonokat olvassa be, a sablon lett simítva, és további feldolgozás céljából a rendszer kezdeményezi.

### <a name="make-linked-templates-accessible-across-clouds"></a>Tegyen elérhetővé hivatkozott sablonok több felhő között

Fontolja meg, hol és hogyan tárolhatja bármilyen társított sablonok használata. Futásidőben, lekéri az Azure Resource Manager –, és ezért a közvetlen hozzáférésre van szüksége – azzal összekapcsolt sablonok. Általános gyakorlat, hogy a beágyazott sablonok a GitHub használatával. Egy GitHub-adattár URL-cím keresztül nyilvánosan elérhető fájlokat tartalmazza. Bár ez a módszer a nyilvános felhőben és a független felhőkben való működik, lehet, hogy az Azure Stack-környezet egy vállalati hálózaton vagy egy leválasztott távoli helyen, minden kimenő Internet-hozzáféréssel nem található. Ezekben az esetekben az Azure Resource Manager beolvasni a beágyazott sablonokkal meghiúsul. 

Jobb eljárás a több felhőre kiterjedő környezetekben, hogy a hivatkozott sablonok, amely a cél felhőben elérhető helyen tárolja. Ideális esetben az összes üzembe helyezési összetevők karbantartani, és folyamatos integráció/folyamatos fejlesztési (CI/CD) folyamat helyeztek üzembe. Azt is megteheti beágyazott sablonok tárolhatja egy blob storage-tároló, amelyből az Azure Resource Manager le lehet kérdezni őket. 

Mivel a blob storage minden felhő egy másik végpont teljesen minősített tartománynevét (FQDN) használ, konfigurálja a sablon helyét, a hivatkozott sablonok két paraméterrel. Paraméterek elfogadhatja a felhasználói bevitel üzembe helyezéskor. Sablonok általában lett létrehozva, és közösen olyan több személyek, ezért ajánlott egy standard ezeket a paramétereket a nevet használja. Elnevezési konvenciók segíteni a sablonok több újrafelhasználható régiók, felhők és szerzők között.

Az alábbi kódban `_artifactsLocation` jelentéskészítéssel kapcsolatos összes összetevőt tartalmazó egyetlen helyre mutat. Figyelje meg, hogy adott alapértelmezett értéket. Üzembe helyezéskor, ha nincs a bemeneti érték van megadva `_artifactsLocation`, az alapértelmezett érték lesz érvényben. A `_artifactsLocationSasToken` bemenetként szolgál a `sasToken`. Az alapértelmezett érték egy üres karakterlánccal forgatókönyvek kell lennie, a `_artifactsLocation` védetté – például egy nyilvános GitHub-adattárból.

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

Az alap URI egyesítésével létrehozott hivatkozásokat a sablonban (az a `_artifactsLocation` paraméter) az összetevő relatív elérési úttal rendelkező és a `_artifactsLocationSasToken`. A következő kód bemutatja, hogyan adja meg a hivatkozásra kattintva a beágyazott sablont uri sablonfüggvény használatával:

```json
"resources": [
  {
    "name": "shared",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
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

Ez a módszer használata esetén az alapértelmezett érték a `_artifactsLocation` paramétert használja. Ha a hivatkozott sablonok kell kérhető le egy másik helyet, a paraméter bemeneti segítségével üzembe helyezéskor felülbírálhatja az alapértelmezett értéket – nem változik a sablon önmagában van szükség.

### <a name="use-artifactslocation-instead-of-hardcoding-links"></a>_ArtifactsLocation hardcoding hivatkozások helyett használjon

Amellett, hogy a beágyazott sablonok, az URL-címet használja a `_artifactsLocation` paraméter alapként szolgál, az összes kapcsolódó összetevők, a központi telepítési sablont. Egyes Virtuálisgép-bővítmények a sablon kívül tárolt parancsfájl mutató hivatkozást. Ezek a bővítmények a kapcsolódó hivatkozásokat kell. Például az egyéni parancsfájl és PowerShell DSC bővítmény kapcsolása előfordulhat, hogy egy külső parancsfájl a Githubon látható módon: 

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

Hardcoding a parancsfájlt a hivatkozások potenciálisan megakadályozza, hogy a sablon üzembe helyezése sikeresen megtörtént egy másik helyre. A VM-erőforrás konfigurálása során a virtuális Gépen futó Virtuálisgép-ügynök a Virtuálisgép-bővítmény a társított összes parancsfájl letöltési kezdeményez, és majd tárolja a parancsfájlok a virtuális gép helyi lemezen. Ezen megközelítés függvények beágyazott sablont hivatkozások mint korábban "Használata beágyazott sablonok régióban" szakaszban leírt.

Erőforrás-kezelő futásidőben beágyazott sablonokat kérdezi le. A Virtuálisgép-bővítmények bármilyen külső összetevők lekérésének végzi a Virtuálisgép-ügynök. Amellett, hogy az összetevő lekérésének különböző kezdeményező a megoldás a Sablondefiníció megegyezik. Használja a _artifactsLocation paraméterrel együtt az alapútvonal, az alapértelmezett érték összetevők tárolására (beleértve a VM-bővítmény parancsfájlok) és a `_artifactsLocationSasToken` a sasToken bemeneti paramétere.

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

Egy abszolút URI létrehozásához, az előnyben részesített módszer, hogy a uri függvénye, helyett a concat függvénye. Felhőbeli konzisztencia azáltal, hogy a Virtuálisgép-bővítmény parancsfájlokra mutató szoftveresen kötött a uri függvénye, ez a funkció a sablonban van konfigurálva.

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

Ezt a módszert használja az összes üzembe helyezési összetevők, konfigurációs szkripteket, beleértve magát a sablon ugyanazon a helyen tárolható. A hely összes hivatkozást módosításához csak kell egy másik alap URL-címet adja meg a _artifactsLocation paraméterek_.

## <a name="factor-in-differing-regional-capabilities"></a>Kéttényezős eltérő regionális képességek

Rugalmas fejlesztés és a frissítések és az Azure-ban bevezetett új szolgáltatás folyamatos folyamat [eltérőek régióiban lehetnek](https://azure.microsoft.com/regions/services/) szolgáltatások vagy frissítések állnak rendelkezésre. Szigorú belső tesztelés után új szolgáltatások vagy a meglévő szolgáltatások frissítések általában be egy érvényesítési programban részt vevő ügyfelek kis körben. Sikeres ügyfél-ellenőrzést a szolgáltatások vagy frissítések egy Azure-régiók részhalmazát belül elérhetővé majd bevezetett további régiók, jelennek meg, a független felhőkben, és potenciálisan elérhetővé teszi az Azure Stack ügyfelei is.

Ismerete, hogy az Azure-régiók és a felhők eltérő lehet az elérhető szolgáltatások, is bizonyos proaktív kapcsolatos döntések a sablonokat. Nagyszerű hely az induláshoz van a rendelkezésre álló erőforrás-szolgáltatók, a felhőhöz megvizsgálásával. Erőforrás-szolgáltató kiderül, hogy az erőforrások és az Azure-szolgáltatások rendelkezésre álló műveletek készletét.

Sablon üzembe helyez, és konfigurálja az erőforrásokat. Erőforrás-szolgáltató által biztosított erőforrástípust. Például a számítási erőforrás-szolgáltató (Microsoft.Compute) kínál, például a virtuális gép és availabilitySets több erőforrástípusok. Mindegyik erőforrás-szolgáltató API-biztosít az Azure Resource Manager egy közös szerződés által meghatározott összes erőforrás-szolgáltató egy egységes, egységes szerzői használatát teszi lehetővé. Előfordulhat azonban, a globális Azure-ban elérhető erőforrás-szolgáltató nem érhető el szuverén felhő vagy egy Azure Stack-régióban.

![Erőforrás-szolgáltatók](./media/templates-cloud-consistency/resource-providers.png) 

Győződjön meg arról, hogy egy adott felhőre érhető el az erőforrás-szolgáltatók, futtassa a következő szkriptet az Azure parancssori felület ([CLI](/cli/azure/install-azure-cli)):

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

A következő PowerShell-parancsmag segítségével is elérhető erőforrás-szolgáltatók lásd:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>Az összes erőforrástípus verziójának ellenőrzése

Tulajdonságcsoport közös minden erőforrástípus esetén, de az egyes erőforrások is rendelkezik a saját konkrét tulajdonságok. Új funkciók és a kapcsolódó tulajdonságok esetenként egy új API-verzió keresztül meglévő erőforrástípusok kerülnek. A sablon egy erőforrás rendelkezik a saját API-verzió tulajdonság - `apiVersion`. A verziókezelés biztosítja, hogy a sablon egy meglévő erőforrás-konfiguráció nem befolyásolja a módosításokat a platformon.

A meglévő erőforrástípusok globális Azure-ban bevezetett új API-verziókban azonnal nem érhető el minden régióban, szuverén felhők vagy az Azure Stack. Megtekintheti az elérhető erőforrás-szolgáltatók, erőforrástípusok és egy felhőalapú API-verziók listáját, az erőforrás-kezelő használhatja az Azure Portalon. Keresse meg az erőforrás-kezelő a minden szolgáltatás menüben. Bontsa ki a szolgáltatók csomópontra az elérhető erőforrás-szolgáltatók, erőforrástípusok és API-verziók vissza az adott felhő erőforrás-kezelőben.

Minden erőforrás esetében az egy adott felhőre, az Azure CLI-ben elérhető API-verzió listázásához, futtassa a következő szkriptet:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

A következő PowerShell-parancsmagot is használhatja:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Tekintse meg az erőforrások helyeként paraméterrel

A sablon mindig helyezünk üzembe egy adott régióban található erőforráscsoportot. Amellett, hogy a központi telepítést önmagában az egyes erőforrások sablonban is megadhatja a régióban történő üzembe helyezést helye tulajdonsággal rendelkezik. A sablon a felhő konzisztencia fejlesztése, kell dinamikus úgy lehet hivatkozni az erőforrások helyeként, mivel minden egyes Azure Stack egyedi helynevek is tartalmazhatnak. Általában erőforrások telepítése és az erőforráscsoport ugyanabban a régióban, de támogató régiók rendelkezésre állása – egyéb felhasználási helyzetek, erőforrások elosztva régiók hasznos lehet.

Annak ellenére is megadhatná, a régiók neveit az erőforrás-tulajdonságok megadásakor egy sablon, ez a módszer nem garantálja, hogy a sablon is üzembe helyezhetők más Azure Stack-környezetben, mert a régió neve nagy valószínűséggel nem létezik ott.

Különböző régiókban befogadásához adja hozzá egy bemeneti paraméter helye a sablon alapértelmezett értékkel. Az alapértelmezett érték lesz használható, ha nem ad meg értéket üzembe helyezés során. 

A függvénye `[resourceGroup()]` az alábbi kulcs-érték párokat tartalmazó objektumot adja vissza:

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

A hely kulcsa az objektum az alapértelmezett érték a bemeneti paraméter hivatkozva Azure Resource Manager, futásidőben, felülírja a `[resourceGroup().location]` sablonfüggvény nevét a sablon üzembe, az erőforráscsoport helyét.

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
    "name": "storageaccount1",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "location": "[parameters('location')]",
    ...
```

Ezzel a sablonnal függvénnyel, nélkül üzembe helyezheti a sablon a felhőben, akár előre, hogy a régiók neveit. Emellett a sablon egy adott erőforrás helyét az erőforráscsoport helye eltérhet. Ebben az esetben segítségével konfigurálhatja, hogy az adott erőforráshoz, további bemeneti paraméterek használatával, miközben ugyanazt a sablont az egyéb erőforrásokat is használható a kezdeti helyen a bemeneti paraméter.

### <a name="track-versions-using-api-profiles"></a>API-profilok használatával verziók követése

Az elérhető erőforrás-szolgáltatók és a kapcsolódó API-verziókat, amelyek szerepelnek az Azure Stack nyomon követheti, hogy nehéz lehet. Például a írása, a legújabb API-verzió idején **Microsoft.Compute/availabilitySets** Azure-ban az `2018-04-01`, míg a rendelkezésre álló Azure és az Azure Stack közös API-verzió `2016-03-30`. A közös API-verzió **Microsoft.Storage/storageAccounts** összes az Azure és az Azure Stack-helyek között megosztott van `2016-01-01`, míg az Azure-ban a legújabb API-verzió `2018-02-01`.

Ebből kifolyólag Resource Manager bevezette a API-profilok sablonokat. API-profilok nélkül az egyes erőforrások sablonban van konfigurálva egy `apiVersion` elem az adott erőforrás az API-verzió.

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
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "name": "myavailabilityset",
            "type": "Microsoft.Compute/availabilitySets",
            "apiVersion": "2016-03-30",
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

Az API-profil verziót egyetlen API-verzió gyakori Azure és az Azure Stack resource típusonként alias funkcionál. API-verziót, az egyes erőforrások megadása a sablonokban, helyett csak az API Profilverzió megadott nevű új legfelső szintű elem `apiProfile` , és hagyja ki a `apiVersion` elem az egyes erőforrások.

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
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "name": "myavailabilityset",
            "type": "Microsoft.Compute/availabilitySets",
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

Az API-profil biztosítja, hogy az API-verziók érhetők el a helyszínen, így nem kell manuálisan ellenőrizheti az egy adott helyen rendelkezésre álló apiVersions. Annak érdekében, hogy az API-profil által hivatkozott API-verziók szerepelnek az Azure Stack-környezet, az Azure Stack-operátorok kell tartania a vonatkozó támogatási házirend alapján megoldás naprakész. Ha a rendszer legfeljebb hat hónap elavult, akkor számít nem megfelelő, és a környezet frissíteni kell.

Az API-profil nem a sablon egy szükséges elem. Akkor is, ha az elem hozzáadásához csak lesz-e az erőforrások, amelyekre nem `apiVersion` van megadva. Ez az elem lehetővé teszi, hogy fokozatos módosításokat, de nem szükséges módosítania meglévő sablonok.

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
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "name": "myavailabilityset",
            "type": "Microsoft.Compute/availabilitySets",
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

## <a name="check-endpoint-references"></a>Ellenőrizze a végpont-hivatkozások

Erőforrások a platform más szolgáltatások is hivatkoznak. Például egy nyilvános IP-Címmel rendelkezhet hozzárendelt nyilvános DNS-nevet. A nyilvános felhő, a független felhőkben és az Azure Stack megoldásokat rendelkezik a saját egyedi végpont-névterek. A legtöbb esetben egy erőforráshoz csak egy előtagot szükséges bemenetként a sablonban. Futásidőben, Azure Resource Manager a végpont egy érték hozzáfűzése egy azt. Néhány végpont értéket kell meghatározni a sablonban. 

> [!NOTE]
> Fejleszthet felhőalapú konzisztencia sablonokat, nem a kapcsolódó végpont névterek.

A következők gyakori végpontja névtér explicit módon megadott erőforrás létrehozásakor kell a következő két példa:

* Storage-fiókok (blob, üzenetsor, tábla és fájl)
* Adatbázisok és a redis gyorsítótár kapcsolati karakterláncai

Végpont névterek is használható a kimenetben, egy sablon információkat, a felhasználó az üzembe helyezés befejeződésekor. Gyakori példák a következők:

* Storage-fiókok (blob, üzenetsor, tábla és fájl)
* Kapcsolati karakterláncok (MySql, SQL Server, Rendszerkarbantartás, egyéni, Értesítésiközpont, ServiceBus, EventHub, ApiHub, DocDb, Redis, PostgreSQL)
* Traffic Manager
* nyilvános IP-cím domainNameLabel
* Felhőszolgáltatások

Általában kerülje a sablonban szoftveresen kötött végpontok. Az ajánlott eljárás, hogy a hivatkozás függvénye dinamikusan beolvasni a végpontok. Ha például leggyakrabban a végpont szoftveresen kötött a storage-fiókok a végpont névtér. Minden tárfióknak van egy egyedi teljes tartománynév elkülönített változó összefűzésével előállítjuk a végpont névtérhez a tárfiók nevére. Blob storage-fiók neve a felhőbeli függően különböző teljes tartománynevek mystorageaccount1 eredményez:

* **mystorageaccount1.BLOB.Core.Windows.NET** létrehozásakor a globális Azure-felhőben.
* **mystorageaccount1.BLOB.Core.chinacloudapi.CN** létrehozásakor az Azure China felhőben.

A következő hivatkozás függvénye lekéri a tárolásierőforrás-szolgáltató a végpont névteret:

```json
"diskUri":"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

A storage-fiók végpontját a szoftveresen kötött értékét lecserélésével a `reference` sablonfüggvény, használhatja ugyanazt a sablont a végpont-hivatkozás módosítása nélkül sikeresen különböző környezetekben üzembe.

### <a name="refer-to-existing-resources-by-unique-id"></a>Tekintse meg a meglévő erőforrások egyedi azonosító alapján

Emellett olvassa el egy meglévő erőforrást az ugyanazon vagy másik erőforrás-csoportba, és ugyanahhoz az előfizetéshez vagy egy másik előfizetést, az ugyanabban a felhőben az ugyanazon bérlőn belüli belül. Az erőforrás-tulajdonságok lekéréséhez egyedi azonosítója az erőforrásán kell használnia. A `resourceId` sablonfüggvény kérdezi le a következő kód azt mutatja be, például az SQL Server erőforrás egyedi azonosítója: 

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

Ezután a `resourceId` belül működik a `reference` sablonfüggvény az adatbázis tulajdonságainak lekérése. A visszaadott objektum tartalmazza a `fullyQualifiedDomainName` tulajdonságot, amely teljes körű végpont értékét fogja tárolni. Ez az érték beolvasott futásidőben, és a felhőbeli végpont környezetspecifikus névteret biztosít. A kapcsolati karakterláncot, anélkül, hogy a végpont névtér hardcoding definiálásához hivatkozhatunk közvetlenül a kapcsolati karakterláncot, amint az a visszatérési objektumának azon tulajdonságát:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Fontolja meg az erőforrás-tulajdonságok

Azure Stack környezeteken belül meghatározott erőforrások egyedi tulajdonságokat, akkor figyelembe kell vennie a sablonban rendelkeznek.

### <a name="ensure-vm-images-are-available"></a>Győződjön meg, hogy a Virtuálisgép-rendszerképek érhetők el

Az Azure egy gazdag Virtuálisgép-rendszerképek kiválasztása biztosít. Ezek a lemezképek létrehozása és üzembe helyezés a Microsoft és partnerei által készített. A lemezképek a virtuális gépek alapját alkotják a platformon. Azonban csak a rendelkezésre álló paraméterek hivatkozzon egy felhő-konzisztens sablon – az adott, a közzétevő, ajánlat és a Termékváltozat a Virtuálisgép-lemezkép érhető el, a globális Azure, Azure szuverén felhők vagy az Azure Stack megoldás.

A helyen elérhető Virtuálisgép-rendszerképek listájának lekéréséhez futtassa a következő Azure CLI-parancsot:

```azurecli-interactive
az vm image list -all
```

Ugyanazt a listát az Azure PowerShell-parancsmaggal kérheti [Get-AzureRmVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) , és adja meg a kívánt helyet a `-Location` paraméter. Példa:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

Ez a parancs néhány perc alatt az összes elérhető rendszerkép vissza a globális Azure-felhő Nyugat-európai régióban vesz igénybe.

Ha a Virtuálisgép-lemezképek elérhetővé az Azure Stackhez, a rendelkezésre álló tár fel szeretné használni. Még a legkisebb skálázási egység befogadásához Azure Stack teszi válassza ki a lemezképeket szeretne hozzáadni egy környezethez.

Az alábbi kódmintában látható egy egységes megközelítést tekintse meg a közzétevő, ajánlat és az Azure Resource Manager-sablonokkal Termékváltozat paraméterek:

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

### <a name="check-local-vm-sizes"></a>Helyi Virtuálisgép-méretek ellenőrzése

A sablon a felhő konzisztencia fejlesztése, ellenőrizze, hogy a Virtuálisgép-méretet szeretne érhető el az összes cél környezetben kell. Virtuálisgép-méretek olyan csoportosított teljesítményt nyújt és képességeket. Egyes Virtuálisgép-méretek attól függ, hogy a hardver, amely a virtuális Gépet futtat. Például ha azt szeretné, GPU-optimalizált virtuális gép üzembe helyezése, a hipervizort futtató hardver kell rendelkeznie a hardver gpu-kkal.

Ha a Microsoft elérhetővé tette az új méret a virtuális Gépet, amely bizonyos hardveres függőségekkel rendelkezik, a virtuális gép mérete általában szeretné elérhetővé tenni először az Azure-felhő-régiók részhalmazát a. Később teszi elérhetővé más régiók és a felhőben. Ahhoz, hogy a Virtuálisgép-méret létezik mindegyik felhőben telepít, az elérhető méretek a következő Azure CLI-paranccsal kérheti le:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Azure PowerShell esetén használja:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

Elérhető szolgáltatások teljes listáját lásd: [elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable).

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Ellenőrizze az Azure Stack az Azure Managed Disks használata

Felügyelt lemezek leírójának tárolása egy Azure-bérlőhöz. Explicit módon a storage-fiók létrehozásához, és adja meg az URI-t a virtuális merevlemez (VHD) a felügyelt lemezek használatával implicit módon hajtsa végre ezeket a műveleteket, amikor telepít egy virtuális Gépet. Felügyelt lemezeket úgy, hogy minden a lemezek virtuális gépekről történő különböző tárolási egységeket egyazon rendelkezésre állási javíthatja a rendelkezésre állási. Ezenkívül meglévő VHD-k konvertálható standardról Premium storage-jelentősen mínusz az üzemkimaradás.

Bár a felügyelt lemezek az Azure Stack tervbe, hogy jelenleg nem támogatottak. Vannak, amíg is fejleszthet felhőalapú összeomlásbiztos sablonok az Azure Stack explicit módon adja meg a VHD-k használatával a `vhd` elem a VM-erőforrás, ahogyan az a sablonban:

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

Ezzel szemben a sablonban, adja meg egy felügyelt lemez konfigurációját, távolítsa el a `vhd` a lemez konfigurációs elemet.

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

Is alkalmazza a módosításokat [adatlemezek](../virtual-machines/windows/using-managed-disks-template-deployments.md).

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Győződjön meg arról, hogy a Virtuálisgép-bővítmények érhetők el az Azure Stackben

A felhő konzisztencia egy másik szempont, használatát [virtuálisgép-bővítmények](../virtual-machines/windows/extensions-features.md) konfigurálása egy virtuális Gépen belül az erőforrásokat. Nem minden Virtuálisgép-bővítmények érhetők el az Azure Stackben. A sablon adhatja meg a Virtuálisgép-bővítmény, függőségeket és a sablonon belül feltételek létrehozása a dedikált erőforrásokat.

Például ha azt szeretné, a Microsoft SQL Servert futtató virtuális gép konfigurálása, a Virtuálisgép-bővítmény konfigurálhatja az SQL Server részeként a sablon üzembe helyezéséhez. Fontolja meg, mi történik, ha a központi telepítési sablont is tartalmaz, a virtuális gépen futó SQL Server-adatbázis létrehozásához konfigurált alkalmazáskiszolgáló. Amellett, hogy az alkalmazáskiszolgálók is használja a VM-bővítmény, konfigurálhatja úgy függőséget az alkalmazáskiszolgáló, a sikeres lépjen vissza az SQL Server rendszerű virtuális gép bővítmény erőforrás. Ez a megközelítés biztosítja, hogy az SQL Servert futtató virtuális gép állítva és elérhető a kiszolgáló az adatbázis létrehozásához átvitelekor.

A sablon a deklaratív módszer lehetővé teszi az erőforrások és az azok közötti függőségeket, a befejezési állapotát határozza meg, míg a platform gondoskodik a szükséges függőségek logikát.

#### <a name="check-that-vm-extensions-are-available"></a>Ellenőrizze, hogy elérhetők-e a Virtuálisgép-bővítmények

A Virtuálisgép-bővítmények számos különböző típusú található. A felhő konzisztencia sablon fejlesztésekor ügyeljen arra, hogy csak az olyan bővítményeket használ érhető el minden régióban a sablon célokat.

Kérdezheti le egy adott régióban elérhető Virtuálisgép-bővítmények (ebben a példában `myLocation`), az alábbi Azure CLI-parancsot:

```azurecli-interactive
az vm extension image list --location myLocation
```

Is futtathat az Azure PowerShell [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) parancsmagot, és `-Location` , adja meg a virtuálisgép-lemezkép helyét. Példa:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Győződjön meg arról, hogy elérhetők-e a verziók

Mivel a Virtuálisgép-bővítmények Resource Manager-erőforrások belső, saját API-verziók rendelkeznek. Ahogy a következő kód bemutatja, a VM-bővítmény típusa a Microsoft.Compute erőforrás-szolgáltató egy beágyazott erőforrást.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

A VM-bővítmény erőforrás API-verziója, amelyekre a sablonnal együtt tervezi az összes helyen jelen kell lennie. A hely függőség az erőforrás-szolgáltató API-verzió, az "Az összes erőforrástípus verzióját hitelesítés" szakaszban korábban tárgyalt rendelkezésre állási hasonlóan működik.

Az elérhető API-verzióit a VM-bővítmény erőforrás listájának lekéréséhez használja a [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) parancsmagot a **Microsoft.Compute** erőforrás-szolgáltató látható módon:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

A virtual machine scale sets is használhatja a Virtuálisgép-bővítmények. Az ugyanazon a helyszínre vonatkozó feltételek vonatkoznak. A sablon a felhő konzisztencia fejlesztése, győződjön meg arról, az API-verziók érhetők el az összes hely való telepítését tervezi. Beolvasni a VM-bővítmény erőforrás-méretezési csoportokhoz az API-verziók, ugyanezt a parancsmagot, mielőtt, de adja meg a virtuálisgép-méretezési csoport erőforrástípus beállítja a látható módon:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Minden egyes adott bővítmény egyben verziószámmal. Ez a verzió jelenik meg a `typeHandlerVersion` a Virtuálisgép-bővítmény tulajdonságát. Győződjön meg arról, hogy a verzió megadott, a `typeHandlerVersion` elem a sablon Virtuálisgép-bővítmények érhetők el a helyeken, ha azt tervezi, hogy a sablon üzembe helyezéséhez. A következő kód például adja meg az 1.7-es verzióra:

```json
{
    "name": "MyCustomScriptExtension",
    "type": "extensions",
    "apiVersion": "2016-03-30",
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

Az elérhető verziók egy adott Virtuálisgép-bővítmény listájának lekéréséhez használja a [Get-AzureRmVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage) parancsmagot. Az alábbi példa lekéri az elérhető a PowerShell DSC (a Desired State Configuration) VM-bővítmény-verziók **myLocation**:

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

Közzétevők listájának lekéréséhez használja a [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) parancsot. Kérelem típusa, használja a [Get-AzureRmVMExtensionImageType](/powershell/module/az.compute/get-azvmextensionimagetype) commend.

## <a name="tips-for-testing-and-automation"></a>Tippek a teszteléshez és automatizálás

Nyomon követéséhez az összes kapcsolódó beállításokat, képességekre és korlátozásokra sablon készítése során kihívást. Az általános megközelítés a következő fejlesztéséhez és teszteléséhez sablonok egyetlen felhő szemben, mielőtt más helyeken célozzák meg. Azonban a korábbi, amely teszteli történik az Authoring Tool folyamat, kevesebb hibaelhárítási és kód újraírását, a fejlesztői csapat kell elvégeznie. Üzemelő példánya, amely a hely függőségeit miatt nem sikerül hibaelhárítása időigényes lehet. Ezért javasoljuk, hogy automatizált tesztelés, a lehető leghamarabb a szerzői műveletekhez részben ciklusban. Végső soron kevesebb fejlesztési időt és kevesebb erőforrást kell, és a felhő-konzisztens összetevők még értékesebb válnak.

Az alábbi képen például a fejlesztési folyamat integrált fejlesztőkörnyezet (IDE) használatával csapatok számára. Az ütemterv különböző szakaszaiban különböző teszttípushoz lesznek végrehajtva. Itt két fejlesztők ugyanazt a megoldást is dolgozunk, de ebben a forgatókönyvben egy fejlesztői vagy egy nagy csapat egyformán vonatkozik. Minden fejlesztő általában helyi másolatot készít egy központi tárházban egyenként a többi befolyásolása nélkül a helyi példány dolgozhatnak ki ugyanazokat a fájlokat az e engedélyezése.

![Munkafolyamat](./media/templates-cloud-consistency/workflow.png) 

Vegye figyelembe a következőket a teszteléshez és automatizálási:

* Ellenőrizze a tesztelési eszközök használata. Például a Visual Studio Code és a Visual Studio tartalmazza az IntelliSense, és egyéb funkciókat, amelyek segítségével ellenőrizze a sablonokat.
* A helyi IDE a fejlesztés során a kód minőségének javításához elemzését statikus kódot az egységteszteket és integráció az egységteszteket. 
* Kezdeti fejlesztése során még jobb élményt biztosít, egységteszteket és integrációs vizsgálatok csak figyelmeztetnek Ha problémát talál, és folytassa a tesztek a. Ezzel a módszerrel a problémák látott azonosíthatja és rangsorolhatja a módosításokat, más néven (TDD) központi telepítés tesztközpontú sorrendjét.
* Vegye figyelembe, hogy néhány tesztet az Azure Resource Manager internetkapcsolat nélkül is elvégezhető. Mások, például a tesztelési sablontelepítés, igényelnek erőforrás-kezelő elvégezni bizonyos műveleteket, amelyek nem hajtható végre offline.
* A központi telepítési sablont szemben az érvényesítés API tesztelése nem egy tényleges telepítés egyenlő. Emellett akkor is, ha telepít egy sablont egy helyi fájlból, a sablonban beágyazott sablonok mutató hivatkozásokat olvassa be Resource Manager által közvetlenül, és Virtuálisgép-bővítmények által hivatkozott összetevők blobnevet szerint a központilag telepített virtuális Gépen futó Virtuálisgép-ügynök.

## <a name="next-steps"></a>További lépések

* [Az Azure Resource Manager-sablon kapcsolatos szempontok](../azure-stack/user/azure-stack-develop-templates.md)
* [Ajánlott eljárások az Azure Resource Manager-sablonok](resource-group-authoring-templates.md)
