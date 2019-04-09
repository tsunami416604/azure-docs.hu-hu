---
title: Az Azure Resource Manager áttekintése | Microsoft Docs
description: Ismerteti, hogyan használja az Azure Resource Manager eszközt erőforrások telepítésére, felügyeletére és hozzáférés-vezérlésére az Azure portálon.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 76df7de1-1d3b-436e-9b44-e1b3766b3961
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2019
ms.author: tomfitz
ms.openlocfilehash: d4af11f5956d548ec25eb0dd9b553e2cf56b195f
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59058543"
---
# <a name="azure-resource-manager-overview"></a>Az Azure Resource Manager áttekintése

Az Azure Resource Manager egy üzembe helyezési és kezelési szolgáltatás az Azure-hoz. Amely lehetővé teszi, hogy létrehozása, frissítése és törlése az Azure-előfizetésében erőforrások konzisztens felügyeleti réteget biztosít. Használhatja a hozzáférés-vezérlés, naplózási és címkézési szolgáltatásokat biztonságát, és az erőforrások rendszerezéséhez az üzembe helyezés után.

A portal, PowerShell, Azure CLI-vel, REST API-k vagy ügyféloldali SDK-k segítségével műveleteket van szüksége, amikor az Azure Resource Manager API kezeli a kérést. Az összes kéréseket kezeli az azonos API-n keresztül, mert megjelenik konzisztens eredmények és a különböző eszközök funkciói. A portálon elérhető összes funkciót is érhetők el a PowerShell, az Azure CLI-vel, a REST API-k és a ügyféloldali SDK-k segítségével. Az eredetileg API-kon keresztül kiadott funkciók a kezdeti kiadástól számított 180 napig jelennek meg a portálon.

Az alábbi képen látható hogyan minden olyan eszközt az Azure Resource Manager API-val kezelheti. Az API továbbítja a kérelmeket a Resource Manager szolgáltatásnak, amely hitelesíti és engedélyezi azokat. Erőforrás-kezelő majd irányítja a kérelmeket a megfelelő szolgáltatásra.

![A Resource Manager kérelmi modellje](./media/resource-group-overview/consistent-management-layer.png)

## <a name="terminology"></a>Terminológia

Ha új felhasználója az Azure Resource Managernek, találkozhat néhány olyan kifejezéssel, amelyet még nem ismer.

* **erőforrás** – Egy olyan kezelhető elem, amely az Azure-on keresztül érhető el. Virtuális gépek, tárfiókok, webalkalmazások, adatbázisok és virtuális hálózatok erőforrások példák.
* **erőforráscsoport** – Egy olyan tároló, amely egy Azure-megoldáshoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazza ezeket az erőforrásokat, amelyeket szeretne kezelése csoportként. Eldöntheti, hogyan kell elosztani az erőforrásokat az erőforráscsoportok teszi a szervezet számára a leginkább megfelelő alapján. Lásd: [erőforráscsoportok](#resource-groups).
* **erőforrás-szolgáltató** -egy szolgáltatás, amely Azure-erőforrások. Ha például egy gyakori erőforrás-szolgáltató van **Microsoft.Compute**, amelyek például a virtuális gép típusú erőforrást. **A Microsoft.Storage** egy másik gyakori erőforrás-szolgáltató. Lásd: [erőforrás-szolgáltatók](#resource-providers).
* **Resource Manager-sablon** – egy JavaScript Object Notation (JSON) fájl, amely meghatározza egy vagy több erőforrás egy erőforráscsoportba vagy előfizetésbe való üzembe helyezéséhez. A sablon erőforrások konzisztens és ismétlődő telepítésére használandó. Lásd: [sablonalapú telepítés](#template-deployment).
* **deklaratív szintaxis** – Egy olyan szintaxis, amellyel anélkül határozhatja meg, mit szeretne létrehozni, hogy ehhez programozási parancsok sorozatát kellene megírnia. A Resource Manager-sablon a deklaratív szintaxis egy példája. A fájlban meghatározhatja az Azure-ra telepíteni kívánt infrastruktúra tulajdonságait.

## <a name="the-benefits-of-using-resource-manager"></a>A Resource Manager használatának előnyei

A Resource Manager számos előnyt kínál:

* A megoldás összes erőforrását egy csoportként telepítheti, felügyelheti és figyelheti meg az erőforrások különálló kezelése helyett.
* A megoldást ismételten telepítheti a fejlesztési életciklus során, és biztos lehet abban, hogy az erőforrások telepítése konzisztens lesz.
* Az infrastruktúrát szkriptek helyett deklaratív sablonok segítségével is kezelheti.
* Meghatározhatja az erőforrások közötti függőségeket, hogy azok a megfelelő sorrendben legyenek telepítve.
* Hozzáférés-vezérlést alkalmazhat az összes szolgáltatásra az erőforráscsoportban, mivel a szerepköralapú hozzáférés-vezérlés (RBAC) natív módon integrálva van a felügyeleti platformba.
* Címkékkel láthatja el az erőforrásokat, így logikusan rendszerezhető az előfizetés összes erőforrása.
* Az azonos címkén osztozó erőforrások csoportjának költségeit megtekintve jól átláthatók a szervezet számlái.

## <a name="understand-management-scope"></a>Megismerheti a felügyeleti hatóköre

Az Azure biztosít a felügyeleti hatókör négy szintű: felügyeleti csoportok, előfizetések, erőforráscsoportok és erőforrásokat. A [felügyeleti csoportok](../governance/management-groups/index.md) szintje egyelőre előzetes verzióban érhető el. Az alábbi ábra ezekre a rétegekre mutat egy példát.

![Hatókör](./media/resource-group-overview/scope-levels.png)

Felügyeleti beállításokat a hatókörszintek bármelyikéhez megadhat. A kiválasztott szint határozza meg, milyen széles körben lesz alkalmazva a beállítás. Az alacsonyabb szintek öröklik a magasabb szintek beállításait. Például, amikor alkalmaz egy [házirend](../governance/policy/overview.md) az előfizetéséhez, a szabályzat érvényes az összes erőforráscsoportra és az előfizetése. A szabályzat alkalmazásakor az erőforráscsoport, amely a házirend alkalmazva az erőforráscsoportot és az ahhoz tartozó összes erőforrást. Egy másik erőforráscsoportot azonban nem rendelkezik a szabályzat-hozzárendelést.

## <a name="guidance"></a>Útmutatás

Az alábbi javaslatokat követve teljes mértékben kihasználhatja a Resource Manager előnyeit a megoldásaival végzett munka során.

* Az infrastruktúrát a Resource Manager-sablonok deklaratív szintaxisán keresztül határozhatja meg és telepítheti imperatív parancsok helyett.
* Meghatározhatja az összes telepítési és konfigurációs lépést a sablonban. Nem szükséges manuális lépéseket megadnia a megoldás beállításához.
* Imperatív parancsok futtatásával kezelheti az erőforrásokat, például elindíthat vagy leállíthat egy alkalmazást vagy gépet.
* Az azonos életciklussal rendelkező erőforrásokat egy erőforráscsoportba rendezheti. Címkék segítségével tetszés szerint rendezheti az erőforrásokat.

Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance?toc=%2fazure%2fazure-resource-manager%2ftoc.json) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.

Resource Manager-sablonok létrehozásával kapcsolatos ajánlások, lásd: [gyakorlati tanácsok az Azure Resource Manager-sablon](template-best-practices.md).

## <a name="resource-groups"></a>Erőforráscsoportok
Néhány fontos tényezőt érdemes figyelembe venni az erőforráscsoport meghatározásakor:

* A csoportban lévő összes erőforrásnak azonos életciklussal kell rendelkeznie. Egyszerre fogja őket telepíteni, frissíteni és törölni. Ha egy erőforrásnak, például egy adatbázis-kiszolgálónak különböző fejlesztési ciklusban kell léteznie, azt másik erőforráscsoportba kell elhelyezni.
* Az egyes erőforrások csak egy erőforráscsoportban létezhetnek.
* Az erőforráscsoporthoz bármikor hozzáadhat, vagy onnan eltávolíthat egy erőforrást.
* Az erőforrásokat áthelyezheti az egyik erőforráscsoportból a másikba. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](resource-group-move-resources.md).
* Egy erőforráscsoport tartalmazhatja a különböző régiókban található erőforrásokat.
* Az erőforráscsoport segítségével meghatározhatja a hozzáférés-vezérlési hatókört felügyeleti műveletekhez.
* Egy erőforrás más erőforráscsoportok erőforrásaival is interakcióba tud lépni. Ez az interakció gyakori, amikor a két erőforrás kapcsolódik, de nem ugyanaz az életciklusuk (például amikor egy webalkalmazás csatlakozik egy adatbázishoz).

Egy erőforráscsoport létrehozásakor meg kell adnia az erőforráscsoport helyét. Most felmerülhet Önben a kérdés, hogy „Miért van szüksége egy erőforráscsoportnak helyre? Ha pedig az erőforrások rendelkezhetnek az erőforrástól eltérő hellyel, akkor miért számít egyáltalán az erőforráscsoport helye?” Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Ezért ha az erőforráscsoport számára megad egy helyet, akkor a metaadatok tárolási helyét adja meg. Megfelelőségi okokból szükség lehet arra, hogy az adatokat egy adott régióban tárolja.

Ha az erőforráscsoport átmenetileg nem érhető el, mert a metaadatok nem érhető el az erőforráscsoportban lévő erőforrásokat nem frissíthető. Más régiókban található erőforrások továbbra is elvárt módon működnek, de azokat nem lehet frissíteni. A kockázat minimalizálása érdekében keresse meg az erőforráscsoport és erőforrások ugyanabban a régióban.

## <a name="resource-providers"></a>Erőforrás-szolgáltatók

Mindegyik erőforrás-szolgáltató műveletek ezen erőforrások használata és az erőforrások készletét kínálja. Ha például kulcsokat és titkos kulcsokat szeretne tárolni, a **Microsoft.KeyVault** erőforrás-szolgáltatót fogja használni. Ez az erőforrás-szolgáltató egy **vaults** nevű erőforrástípust biztosít a kulcstároló létrehozásához.

Az erőforrástípus nevének formátuma: **{erőforrás-szolgáltató}/{erőforrástípus}**. A kulcstartó erőforrástípus **Microsoft.KeyVault/vaults**.

Mielőtt elkezdi erőforrásai üzembe helyezését, ismerje meg az elérhető erőforrás-szolgáltatókat. Az erőforrások és az erőforrás-szolgáltatók nevének ismerete segíthet az Azure-ban üzembe helyezni kívánt erőforrások meghatározásában. Emellett ismernie kell az egyes erőforrástípusok érvényes helyeit és API-verzióit. További információkért lásd az [erőforrás-szolgáltatókat és a típusaikat](resource-manager-supported-services.md) ismertető cikket.

Erőforrás-szolgáltatók által kínált a műveletek tekintse meg a [Azure REST API-k](/rest/api/azure/).

## <a name="template-deployment"></a>Sablonalapú telepítés

A Resource Managerrel egy olyan sablont hozhat létre (JSON formátumban), amely meghatározza az Azure-megoldás infrastruktúráját és konfigurációját. A sablonok segítségével a megoldás a teljes életciklusa során ismételten üzembe helyezhető, és az erőforrások üzembe helyezése biztosan konzisztens lesz.

A sablont, és hogyan formázásával kapcsolatos további információkért lásd: [struktúra és az Azure Resource Manager-sablonok szintaxisát](resource-group-authoring-templates.md). Az erőforrástípusok JSON-szintaxisának megtekintéséért lásd [az Azure Resource Manager-sablonokban az erőforrások meghatározásával kapcsolatos](/azure/templates/) témakört.

Erőforrás-kezelő feldolgozza a sablont, mint bármilyen más kérelmet. Elemzi a sablont, és a szintaxisát átalakítja a megfelelő erőforrás-szolgáltatók számára szükséges REST API-műveletekké. Például amikor a Resource Manager megkap egy sablont, amely a következő erőforrás-definíciót tartalmazza:

```json
"resources": [
  {
    "apiVersion": "2016-01-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {
    }
  }
]
```

Átalakítja a definíciót a következő REST API-műveletté, amelyet a rendszer elküld a Microsoft.Storage erőforrás-szolgáltatónak:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "properties": {
  }
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage"
}
```

A sablonok és erőforráscsoportok meghatározási módja teljes mértékben Öntől függ, illetve attól, hogyan szeretné kezelni a megoldást. Például egyetlen sablon segítségével üzembe helyezheti a háromszintű alkalmazását egyetlen erőforráscsoportra.

![háromrétegű sablon](./media/resource-group-overview/3-tier-template.png)

Azonban nem kell meghatároznia a teljes infrastruktúrát egyetlen sablonban. Gyakran érdemes felosztani a telepítési követelményeket konkrét, célspecifikus sablonokra. Ezeket a sablonokat egyszerűen újból felhasználhatja különböző megoldásokhoz. Egy adott megoldás telepítéséhez hozzon létre egy fősablont, amely összekapcsolja az összes szükséges sablont. Az alábbi kép bemutatja, hogyan telepíthető egy háromszintű megoldás egy olyan fölérendelt sablon segítségével, amely három beágyazott sablont tartalmaz.

![beágyazott rétegsablon](./media/resource-group-overview/nested-tiers-template.png)

Ha elképzeli, hogy a rétegek külön életciklusokkal rendelkeznek, akkor a három szintet különálló erőforráscsoportokra telepítheti. Ügyeljen rá, hogy az erőforrások továbbra is kapcsolódhatnak egyéb erőforráscsoportok erőforrásaihoz.

![rétegsablon](./media/resource-group-overview/tier-templates.png)

A beágyazott sablonokkal kapcsolatos további információkért lásd: [Kapcsolt sablonok használata az Azure Resource Manager eszközben](resource-group-linked-templates.md).

Az Azure Resource Manager a függőségek elemzésével biztosítja, hogy az erőforrások a megfelelő sorrendben jöjjenek létre. Ha egy erőforrás egy másik erőforráshoz tartozó értéket használ fel (például egy virtuális gép, amely egy tárfiókot igényel a lemezekhez), akkor beállíthat egy függőséget. További információ: [Függőségek meghatározása az Azure Resource Manager sablonokban](resource-group-define-dependencies.md).

A sablonokat az infrastruktúra frissítésére is használhatja. Hozzáadhat például egy erőforrást a megoldáshoz, valamint hozzáadhat konfigurációs szabályokat a már telepített erőforrásokhoz. Ha a sablon egy erőforrás, amely már létezik, a Resource Manager frissíti a meglévő erőforrást, egy új létrehozása helyett.

A Resource Manager bővítményeket biztosít olyan forgatókönyvek esetére, amikor további műveletek szükségesek, például egy adott szoftver telepítése, amelyet nem tartalmaz a beállítás. Ha már használ valamilyen konfigurációfelügyeleti szolgáltatást, mint a DSC, Chef vagy Puppet, bővítmények segítségével folytathatja a munkát az adott szolgáltatással. További információ a virtuális gépi bővítményekről: [A virtuális gépi bővítmények és funkcióik áttekintése](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Amikor létrehoz egy megoldást a portálról, az automatikusan tartalmaz egy telepítési sablont. Nem szükséges teljesen új sablont létrehoznia, mivel kezdetben használhatja a sablont a megoldásához, majd testre szabhatja az adott igényeknek megfelelően. A minta: [a rövid útmutató: Létrehozása és üzembe helyezése Azure Resource Manager-sablonok az Azure portal használatával](./resource-manager-quickstart-create-templates-use-the-portal.md). Egy meglévő erőforráscsoport sablonjának lekéréséhez is exportálhatja az erőforráscsoport aktuális állapotát, vagy megtekintheti az adott telepítéshez felhasznált sablont. Az [exportált sablon](./manage-resource-groups-portal.md#export-resource-groups-to-templates) megtekintése hasznos információkat nyújt a sablon szintaxisáról.

Végül a sablon az alkalmazás forráskódjának részévé válik. Elhelyezheti a forráskódraktárban, és frissítheti az alkalmazás továbbfejlesztésekor. A sablont a Visual Studio eszközben szerkesztheti.

A sablon meghatározása után készen áll, hogy üzembe helyezze az erőforrásokat az Azure-ban. Az erőforrások üzembe helyezéséhez lásd:

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](resource-group-template-deploy.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure parancssori felületével](resource-group-template-deploy-cli.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Portallal](resource-group-template-deploy-portal.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Manager REST API-val](resource-group-template-deploy-rest.md)

## <a name="safe-deployment-practices"></a>Biztonságos üzembehelyezési gyakorlatok

Összetett szolgáltatások Azure-ban való üzembe helyezésekor előfordulhat, hogy több régióban üzembe kell helyeznie a szolgáltatást, a következő lépés előtt pedig ellenőriznie kell annak állapotát. A [Azure Deployment Managerrel](deployment-manager-overview.md) koordinálhatja a szolgáltatás előkészített kibocsátását. A szolgáltatás kibocsátásának előkészítésével a régiókban történő üzembe helyezés előtt azonosíthat potenciális problémákat. Ha nem szeretné megtenni ezeket az óvintézkedéseket, válassza az előző szakasz üzembe helyezési műveleteit.

A Deployment Manager jelenleg privát előzetes verzióban érhető el.

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerte, hogyan használhatja az Azure Resource Manager eszközt erőforrások telepítésére, felügyeletére és hozzáférés-vezérlésére az Azure-ban. A következő cikkből megtudhatja, hogyan hozhatja létre első Azure Resource Manager-sablonját.

> [!div class="nextstepaction"]
> [Gyors útmutató: Létrehozása és üzembe helyezése Azure Resource Manager-sablonok az Azure portal használatával](./resource-manager-quickstart-create-templates-use-the-portal.md)
