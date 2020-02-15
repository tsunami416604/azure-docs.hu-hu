---
title: Erőforrások telepítési sorrendjének beállítása
description: Ismerteti, hogyan lehet egy erőforrást egy másik erőforrástól függőként beállítani az üzembe helyezés során, hogy az erőforrások megfelelően legyenek telepítve a megfelelő sorrendben.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: ffd6d6c65a1cbe9578b5f9162d29f3238e27ea71
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207689"
---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>Erőforrások üzembe helyezési sorrendjének meghatározása Azure Resource Manager-sablonokban

Erőforrás telepítésekor előfordulhat, hogy az üzembe helyezése előtt meg kell győződnie arról, hogy más erőforrások is léteznek. SQL-adatbázis üzembe helyezése előtt például SQL Serverre van szükség. Ezt a kapcsolatot úgy definiálhatja, hogy a másik erőforrástól függőként megjelöl egy erőforrást. A **dependsOn** elemmel vagy a **hivatkozási** függvénnyel határozhatja meg a függőséget.

A Resource Manager kiértékeli az erőforrások közötti függőségeket, majd azokat függőségi sorrendben üzembe helyezi. Ha az erőforrások között nincs függőségi viszony, akkor a Resource Manager párhuzamosan helyezi üzembe azokat. Csak az ugyanabban a sablonban üzembe helyezett erőforrások függőségeit kell meghatároznia.

## <a name="dependson"></a>dependsOn

A sablonon belül a dependsOn elem lehetővé teszi, hogy egy erőforrást egy vagy több erőforrástól függőként definiáljon. Az érték az erőforrásnevek vesszővel tagolt listája. A lista a [feltételesen üzembe helyezett](conditional-resource-deployment.md)erőforrásokat is tartalmazhatja. Ha egy feltételes erőforrás nincs telepítve, Azure Resource Manager automatikusan eltávolítja a szükséges függőségekről.

Az alábbi példa egy virtuálisgép-méretezési készletet mutat be, amely egy terheléselosztó, egy virtuális hálózat és egy több Storage-fiókot létrehozó hurok alapján van függőben. Ezek a más erőforrások nem jelennek meg a következő példában, de a sablonban máshol is léteznie kell.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "apiVersion": "2016-03-30",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "tags": {
    "displayName": "VMScaleSet"
  },
  "dependsOn": [
    "[variables('loadBalancerName')]",
    "[variables('virtualNetworkName')]",
    "storageLoop",
  ],
  ...
}
```

Az előző példában egy **storageLoop**nevű másolási hurokon keresztül létrehozott erőforrások függősége szerepel. Példaként tekintse meg az [erőforrások több példányának létrehozása a Azure Resource Managerban](copy-resources.md)című témakört.

A függőségek meghatározásakor belefoglalhatja az erőforrás-szolgáltatói névteret és az erőforrástípust is a kétértelműség elkerülése érdekében. Például egy olyan terheléselosztó és virtuális hálózat tisztázásához, amelynek neve azonos a többi erőforrás nevével, használja a következő formátumot:

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
```

Habár előfordulhat, hogy a dependsOn használatával leképezi az erőforrások közötti kapcsolatokat, fontos tisztában lennie azzal, hogy miért csinálja. Ha például szeretné dokumentálni, hogy az erőforrások hogyan kapcsolódnak egymáshoz, a dependsOn nem a megfelelő megközelítés. Az üzembe helyezés után nem lehet lekérdezni, hogy mely erőforrások lettek meghatározva a dependsOn elemben. A dependsOn használatával lehetséges a telepítési idő, mivel a Resource Manager párhuzamosan két, függőséggel rendelkező erőforrásban üzemel.

## <a name="child-resources"></a>Gyermek erőforrások

Az erőforrások tulajdonság lehetővé teszi a definiált erőforráshoz kapcsolódó alárendelt erőforrások megadását. A gyermek erőforrások csak öt szint mélységben adhatók meg. Fontos megjegyezni, hogy egy implicit telepítési függőség nem jön létre egy alárendelt erőforrás és a fölérendelt erőforrás között. Ha a szülő erőforrás után telepíteni kell a gyermek erőforrást, explicit módon meg kell határoznia, hogy a dependsOn tulajdonságtól függ.

Minden szülő erőforrás csak bizonyos típusú erőforrásokat fogad el alárendelt erőforrásként. Az elfogadott erőforrástípusok a szülő erőforrás [sablon sémájában](https://github.com/Azure/azure-resource-manager-schemas) vannak megadva. A gyermek erőforrástípus neve tartalmazza a szülő erőforrástípus nevét, például a **Microsoft. Web/Sites/config** és a **Microsoft. Web/Sites/Extensions** is a **Microsoft. Web/Sites**alárendelt erőforrásai.

Az alábbi példa egy SQL Servert és egy SQL-adatbázist mutat be. Figyelje meg, hogy az SQL-adatbázis és az SQL Server között explicit függőség van meghatározva annak ellenére, hogy az adatbázis a kiszolgáló gyermeke.

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "apiVersion": "2014-04-01-preview",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "apiVersion": "2014-04-01-preview",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
        "tags": {
          "displayName": "Database"
        },
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
        }
      }
    ]
  }
]
```

## <a name="reference-and-list-functions"></a>hivatkozás és lista függvények

A [Reference függvény](template-functions-resource.md#reference) lehetővé teszi, hogy egy kifejezés más JSON-nevekből és-érték párokból vagy futásidejű erőforrásokból származtatsa az értékét. A [lista * függvények](template-functions-resource.md#list) egy adott erőforráshoz adnak vissza értékeket egy list műveletből.  A hivatkozás és a lista kifejezések implicit módon deklarálják, hogy egy erőforrás egy másiktól függ, ha a hivatkozott erőforrás ugyanabban a sablonban van telepítve, és a neve (nem erőforrás-azonosító). Ha az erőforrás-azonosítót a hivatkozás vagy a lista függvénybe adja át, az implicit hivatkozás nem jön létre.

A Reference függvény általános formátuma:

```json
reference('resourceName').propertyPath
```

A Listkeys műveletének beolvasása függvény általános formátuma:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

A következő példában egy CDN-végpont kifejezetten a CDN-profiltól függ, és implicit módon egy webalkalmazástól függ.

```json
{
    "name": "[variables('endpointName')]",
    "apiVersion": "2016-04-02",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "dependsOn": [
      "[variables('profileName')]"
    ],
    "properties": {
      "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
      ...
    }
```

A függőségek megadásához használhatja ezt az elemet vagy az dependsOn elemet is, de nem kell mindkettőt használnia ugyanahhoz a függő erőforráshoz. Ha lehetséges, használjon egy implicit hivatkozást, hogy elkerülje a szükségtelen függőségek hozzáadását.

További információért lásd a [Reference függvényt](template-functions-resource.md#reference).

## <a name="circular-dependencies"></a>Körkörös függőségek

A Resource Manager körkörös függőségeket azonosít a sablon érvényesítése során. Ha olyan hibaüzenetet kap, amely szerint a körkörös függőség létezik, értékelje ki a sablont, és ellenőrizze, hogy nincs-e szükség függőségre, és hogy el lehet-e távolítani. Ha a függőségek eltávolítása nem működik, elkerülheti a körkörös függőségeket úgy, hogy áthelyez néhány központi telepítési műveletet a körkörös függőséggel rendelkező erőforrások után üzembe helyezett alárendelt erőforrásokra. Tegyük fel például, hogy két virtuális gépet telepít, de tulajdonságokat kell megadnia, amelyek a másikra hivatkoznak. Ezeket a következő sorrendben helyezheti üzembe:

1. vm1
2. vm2
3. A VM1 bővítmény a VM1 és a VM2 függvénytől függ. A bővítmény beállítja azokat az értékeket a VM1, amelyeket a VM2-ből kap.
4. A VM2 bővítmény a VM1 és a VM2 függvénytől függ. A bővítmény beállítja azokat az értékeket a VM2, amelyeket a VM1-ből kap.

További információ a telepítési sorrend kiértékeléséről és a függőségi hibák megoldásáról: [gyakori Azure-telepítési hibák elhárítása Azure Resource Managerokkal](common-deployment-errors.md).

## <a name="next-steps"></a>Következő lépések

* Az oktatóanyag lépéseinek megismeréséhez tekintse meg az [oktatóanyag: Azure Resource Manager sablonok létrehozása függő erőforrásokkal](template-tutorial-create-templates-with-dependent-resources.md)című témakört.
* A függőségek beállításával kapcsolatos javaslatokért lásd: [Azure Resource Manager sablon ajánlott eljárásai](template-best-practices.md).
* Az üzembe helyezés során felmerülő hibák elhárításáról további információt [Az Azure telepítési hibáinak elhárítása a Azure Resource Manager](common-deployment-errors.md)használatával című témakörben talál.
* Azure Resource Manager sablonok létrehozásával kapcsolatos további tudnivalókért lásd: [sablonok készítése](template-syntax.md).
* A sablonban elérhető függvények listáját itt tekintheti meg: [sablon függvények](template-functions.md).

