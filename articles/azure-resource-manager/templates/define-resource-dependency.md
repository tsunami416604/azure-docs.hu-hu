---
title: Erőforrások telepítési sorrendjének beállítása
description: Ez a témakör azt ismerteti, hogy az erőforrások üzembe helyezése során hogyan állíthatók be egy erőforrás függővé egy másik erőforrástól, így biztosítható, hogy az erőforrások a megfelelő sorrendben legyenek telepítve.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: f11f79df875492a568a76f494dfffb4a163f64cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153284"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>Erőforrások ARM-sablonokban való üzembe helyezésének sorrendjének meghatározása

Egy erőforrás üzembe helyezésekor előfordulhat, hogy meg kell győződnie arról, hogy más erőforrások léteznek, mielőtt üzembe helyezi. Például egy SQL-adatbázis telepítése előtt sql-kiszolgálóra van szükség. Ezt a kapcsolatot úgy határozhatja meg, hogy az egyik erőforrást a másik erőforrástól függőként jelöli meg. Függőséget a **dependsOn** elemmel vagy a **referenciafüggelemmel** adhat meg.

A Resource Manager kiértékeli az erőforrások közötti függőségeket, majd azokat függőségi sorrendben üzembe helyezi. Ha az erőforrások között nincs függőségi viszony, akkor a Resource Manager párhuzamosan helyezi üzembe azokat. Csak meg kell adnia az ugyanabban a sablonban üzembe helyezett erőforrások függőségeit.

## <a name="dependson"></a>dependsOn

A sablonon belül a dependsOn elem lehetővé teszi, hogy egy erőforrást egy vagy több erőforrástól függőként definiáljon. Értéke az erőforrásnevek vesszővel tagolt listája. A lista tartalmazhat [feltételesen telepített erőforrásokat.](conditional-resource-deployment.md) Ha egy feltételes erőforrás nincs telepítve, az Azure Resource Manager automatikusan eltávolítja azt a szükséges függőségek.

A következő példa egy virtuálisgép-méretezési készletet mutat be, amely egy terheléselosztótól, a virtuális hálózattól és egy több tárfiókot létrehozó ciklustól függ. Ezek az egyéb erőforrások nem jelennek meg a következő példában, de a sablonban máshol kell létezniük.

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

Az előző példában egy függőség szerepel az erőforrások, amelyek egy copy loop nevű **storageLoop**keresztül jönnek létre. Például lásd: [Több erőforráspéldány létrehozása az Azure Resource Manager ben.](copy-resources.md)

Függőségek definiálásakor az erőforrás-szolgáltató névterét és erőforrástípusát is felveheti a kétértelműség elkerülése érdekében. Például egy olyan terheléselosztó és virtuális hálózat tisztázásához, amelynek neve megegyezhet más erőforrások nevével, használja a következő formátumot:

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
```

Bár lehet, hogy hajlamos a függOn leképezése közötti kapcsolatok az erőforrások, fontos megérteni, hogy miért csinálja. Például, hogy dokumentálja, hogyan erőforrások kapcsolódnak egymáshoz, dependsOn nem a megfelelő megközelítés. Nem lehet lekérdezni, hogy mely erőforrások vannak definiálva a dependsOn elem üzembe helyezés után. A dependsOn használatával potenciálisan hatással lehet a telepítési időre, mivel az Erőforrás-kezelő nem telepíti párhuzamosan két függőségi erőforrást.

## <a name="child-resources"></a>Gyermekforrások

Az erőforrások tulajdonság lehetővé teszi a definiált erőforráshoz kapcsolódó gyermekerőforrások megadását. A gyermekerőforrások csak öt szint mélyszintet határozhatnak meg. Fontos megjegyezni, hogy egy implicit központi telepítési függőség nem jön létre a gyermek erőforrás és a szülő erőforrás között. Ha a szülőerőforrás után telepíteni kell a gyermekerőforrást, explicit módon meg kell adnia a függőséget a dependsOn tulajdonsággal.

Minden szülőerőforrás csak bizonyos erőforrástípusokat fogad el gyermekerőforrásként. Az elfogadott erőforrástípusok a szülő erőforrás [sablonsémájában](https://github.com/Azure/azure-resource-manager-schemas) vannak megadva. A gyermekerőforrás-típus neve tartalmazza a szülő erőforrástípus nevét, például a **Microsoft.Web/sites/config** és a **Microsoft.Web/sites/extensions** a **Microsoft.Web/sites**gyermekerőforrása.

A következő példa egy SQL-kiszolgálót és egy SQL-adatbázist mutat be. Figyelje meg, hogy explicit függőség van definiálva az SQL-adatbázis és az SQL-kiszolgáló között, még akkor is, ha az adatbázis a kiszolgáló gyermeke.

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

## <a name="reference-and-list-functions"></a>hivatkozási és listafüggvények

A [referencia függvény](template-functions-resource.md#reference) lehetővé teszi, hogy egy kifejezés az értékét más JSON-név- és értékpárokból vagy futásidejű erőforrásokból származtassa. A [lista* függvények](template-functions-resource.md#list) egy erőforrás értékeit adják vissza egy listaműveletből.  A hivatkozási és listakifejezések implicit módon deklarálják, hogy az egyik erőforrás egy másiktól függ, amikor a hivatkozott erőforrás ugyanabban a sablonban van telepítve, és a neve (nem az erőforrásazonosítója) hivatkozik rá. Ha átadja az erőforrás-azonosítót a referencia- vagy listafüggvényeknek, nem jön létre implicit hivatkozás.

A referenciafüggvény általános formátuma a következő:

```json
reference('resourceName').propertyPath
```

A listKeys függvény általános formátuma:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

A következő példában a CDN-végpont kifejezetten a CDN-profiltól függ, és implicit módon egy webalkalmazástól függ.

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

Használhatja ezt az elemet, vagy a dependsOn elem függőségek megadásához, de nem kell használni mindkettőt ugyanahhoz a függő erőforráshoz. Amikor csak lehetséges, használjon implicit hivatkozást a szükségtelen függőségek hozzáadásának elkerüléséhez.

További információ: [Referencia függvény](template-functions-resource.md#reference).

## <a name="circular-dependencies"></a>Körkörös függőségek

Az Erőforrás-kezelő azonosítja a körkörös függőségeket a sablon érvényesítése során. Ha olyan hibaüzenetet kap, amely szerint létezik körkörös függőség, értékelje ki a sablont, és nézze meg, hogy nincs-e szükség függőségre, és eltávolítható-e. Ha a függőségek eltávolítása nem működik, elkerülheti a körkörös függőségeket, ha egyes telepítési műveleteket áthelyez olyan gyermekerőforrásokba, amelyek a körkörös függőségű erőforrások után vannak üzembe helyezve. Tegyük fel például, hogy két virtuális gépet telepít, de mindegyiken be kell állítania a tulajdonságokat, amelyek a másikra hivatkoznak. Ezeket a következő sorrendben telepítheti:

1. vm1
2. vm2
3. A vm1-es bővítmény a vm1 és a vm2-től függ. A bővítmény a vm1-es vm1-es értékeit állítja be, amelyeket a vm2-ből kap.
4. A vm2-es bővítmény a vm1 és a vm2-től függ. A bővítmény a vm1-ből leadott vm2-értékeket állít be.

A telepítési sorrend felméréséről és a függőségi hibák elhárításáról az [Azure Resource Manager gyakori Azure-telepítési hibák elhárítása című témakörben](common-deployment-errors.md)talál további információt.

## <a name="next-steps"></a>További lépések

* Az oktatóanyag on-át című [oktatóanyag: Azure Resource Manager-sablonok létrehozása függő erőforrásokkal](template-tutorial-create-templates-with-dependent-resources.md)című témakörben olvashat.
* A függőségek beállításakor az [Azure Resource Manager-sablon ajánlott eljárásacímű témakörben olvashat.](template-best-practices.md)
* A függőségek üzembe helyezés során történő hibaelhárításáról az [Azure Resource Manager gyakori Azure-telepítési hibáinak elhárítása című témakörben](common-deployment-errors.md)olvashat.
* Az Azure Resource Manager-sablonok létrehozásáról a Sablonok készítése című [témakörben olvashat.](template-syntax.md)
* A sablonban elérhető függvények listáját a [Sablonfüggvények című témakörben olvashatja.](template-functions.md)

