---
title: Állítsa be a telepítési sorrendet, az Azure-erőforrások |} Microsoft Docs
description: Útmutatás állítja be egy erőforrást egy másik erőforrástól függő erőforrások a megfelelő sorrendben legyenek telepítve. Ellenőrizze a telepítés során.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''
ms.assetid: 34ebaf1e-480c-4b4d-9bf6-251bd3f8f2cf
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: tomfitz
ms.openlocfilehash: d5a9bde85e894f2f4283348771dc5cacc7a08f23
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824655"
---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>Adja meg a ahhoz, hogy az Azure Resource Manager sablonokban erőforrások telepítése
A megadott erőforrás lehet más erőforrások, amelyek már léteznie kell az erőforrás van telepítve. Például egy SQL server léteznie kell egy SQL-adatbázis telepítése előtt. Ez a kapcsolat egy erőforrást, mint a más erőforrástól függő megjelölésével határozza meg. A függőség megadhatja a **dependsOn** elem, vagy használatával a **hivatkozás** függvény. 

Erőforrás-kezelő kiértékeli az erőforrások közti függőségeket, és telepíti azokat a függő sorrendben. Ha erőforrások nem egymástól függenek, erőforrás-kezelő telepíti azokat párhuzamosan. Csak akkor kell megadni telepített erőforrások függőségek ugyanabban a sablonban. 

## <a name="dependson"></a>dependsOn
A sablonon belül a dependsOn elem teszi meghatározni egy erőforrást, egy vagy több erőforrást egy függ. Az érték lehet erőforrásnevek vesszővel tagolt listája. 

A következő példában egy virtuálisgép-méretezési csoport, amely egy adott terheléselosztóhoz, a virtuális hálózati és a több tárfiókot létrehozó hurkot függ. Ezeket az erőforrásokat nem az alábbi példában látható módon, de a sablonban máshol léteznie kell.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "apiVersion": "2016-03-30",
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

A fenti példában egy függőséget szerepel-e az erőforrásokat, a másolási ciklust nevű keresztül létrehozott **storageLoop**. Egy vonatkozó példáért lásd: [erőforrások több példánya létrehozása az Azure Resource Manager](resource-group-create-multiple.md).

Függőségek meghatározásakor az erőforrás-szolgáltató névtere és a félreérthetőség elkerülése érdekében erőforrástípus lehetnek. Elmagyarázza, a terheléselosztó és a virtuális hálózat, amelyek az azonos nevű más erőforrások, például a következő formátumban kell megadni:

```json
"dependsOn": [
  "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
]
``` 

Akkor lehet, hogy az erőforrások közötti kapcsolatok DependsOn tulajdonság használatával kell dönteni, de napjainkban fontos tudni, miért végzett azt. Dokumentálása, milyen erőforrásokat kötik, például a DependsOn tulajdonság nem a megfelelő módszert. Nem lehet lekérdezni, mely erőforrásokat telepítést követően a DependsOn tulajdonság elemben definiált. DependsOn tulajdonság használatával, potenciálisan befolyásolja a központi telepítés alkalmával mert függőségi viszonyban párhuzamos két erőforrások erőforrás-kezelő nem telepítheti. A dokumentum erőforrásainak kapcsolatai helyette használja a [erőforrás linking](/rest/api/resources/resourcelinks).

## <a name="child-resources"></a>Gyermek-erőforrások
Az erőforrás-tulajdonság adja meg, amely kapcsolódik a múltbeli erőforrás gyermekerőforrásait teszi lehetővé. Gyermek erőforrások csak meghatározott öt szintnél mélyebb lehet. Fontos megjegyezni, hogy a nem a gyermek-erőforrás és a szülő erőforrás között jön létre egy implicit függőség. A gyermek-erőforrás a szülő erőforrás után telepítésre van szüksége, a dependsOn tulajdonság függőséget explicit módon meg kell adni. 

Minden szülő erőforrás csak bizonyos típusú erőforrások gyermekerőforrásait fogad el. Az elfogadott erőforrás típusok vannak megadva a [sablonséma](https://github.com/Azure/azure-resource-manager-schemas) a szülő erőforrás. A gyermek erőforrástípus neve tartalmazza a szülő erőforrástípus nevét, mint **Microsoft.Web/sites/config** és **Microsoft.Web/sites/extensions** mindkét alsóbb szintű erőforrásai vannak a **Microsoft.Web/sites**.

A következő példában egy SQL server és SQL-adatbázis. Figyelje meg, hogy egy explicit függőség között nincs definiálva a SQL database és SQL server, annak ellenére, hogy az adatbázis egy alárendelt kiszolgáló.

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "apiVersion": "2014-04-01-preview",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "Database"
        },
        "apiVersion": "2014-04-01-preview",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
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

## <a name="reference-and-list-functions"></a>referencia- és funkciók
A [függvényre](resource-group-template-functions-resource.md#reference) lehetővé teszi, hogy a kifejezés értéke származik más JSON név-érték párok vagy futásidejű erőforrásokat. A [lista * funkciók](resource-group-template-functions-resource.md#listkeys-listsecrets-and-list) a list művelet származó visszaadott értékekre erőforrás.  Referencia- és kifejezések implicit módon deklarálja, hogy egy erőforrást egy másik, ugyanazt a sablont a hivatkozott erőforrás telepítésekor függ, és a név (nem erőforrás-azonosító) által hivatkozott. A hivatkozás vagy a lista függvényekké adja meg az erőforrás-azonosító, ha egy implicit hivatkozási nem jön létre.

A hivatkozás függvény általános formátuma:

```json
reference('resourceName').propertyPath
```

A listKeys függvény általános formátuma:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

A következő példában a CDN-végpontok explicit módon függ a CDN-profilt, és implicit módon függ a webes alkalmazás.

```json
{
    "name": "[variables('endpointName')]",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "apiVersion": "2016-04-02",
    "dependsOn": [
            "[variables('profileName')]"
    ],
    "properties": {
        "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
        ...
    }
```

Függőségeket használhatja ezt az elemet vagy a dependsOn elem, de nem használhatók egyszerre ugyanazon függő erőforrás kell. Amikor csak lehetséges, használjon egy implicit hivatkozási szükségtelen-függőség felvétele elkerülése érdekében.

További tudnivalókért lásd: [függvényre](resource-group-template-functions-resource.md#reference).

## <a name="recommendations-for-setting-dependencies"></a>Javaslatok függőségek beállítása

Amikor eldönti, milyen függőségek beállításához, kövesse az alábbi iránymutatásokat:

* A lehető legkevesebb függőségek beállítása.
* Állítsa be a gyermek-erőforrás a szülő erőforrás függ.
* Használja a **hivatkozás** funkciót, és adjon át kell megosztani egy tulajdonság erőforrások közötti implicit függőségek beállítani az erőforrás nevét. Ne adja hozzá egy explicit függőség (**dependsOn**) Ha már megadott egy implicit függőség. Ez a megközelítés csökkenti a szükségtelen függőségek kockázatot. 
* Amikor az erőforrás nem lehet a függőség beállítása **létrehozott** nélkül egy másik erőforrás funkciói. Függőség nem állítható be, ha az erőforrások csak interaktív telepítés után.
* Lehetővé függőségek cascade explicit módon beállítás nélkül. Például a virtuális gép virtuális hálózati adapteren, valamint a virtuális hálózati adapter függ a virtuális hálózat és a nyilvános IP-címeket. Ezért a virtuális gép telepített összes három erőforrások, de nincs explicit módon állítja be a virtuális gép összes három erőforrástól függ. Ez a megközelítés a függőségi sorrend tisztázza, és megkönnyíti a sablon később módosítható.
* Ha a telepítés előtt értéket lehet meghatározni, próbálja üzembe helyezni a függőség nélküli erőforrás. Például ha egy konfigurációs értéke egy másik erőforrás nevét, nem szükség lehet egy függőséget. Ez az útmutató nem mindig működik, mert egyes erőforrásokat ellenőriznie az egyéb erőforrásokat. Ha hibaüzenetet kap, hozzáadjon egy függőséget. 

Erőforrás-kezelő körkörös függőségi viszony azonosítja a sablon érvényesítése során. Ha hibaüzenet jelenik meg, amely meghatározza, hogy, hogy létezik-e körkörös függőséget, értékelje ki, hogy összes függőséget nem szükségesek, és el kell távolítani a sablont. Függőségek eltávolítása nem működik, ha úgy kerülheti el körkörös függőségi viszony néhány telepítési művelet áthelyezése gyermekszintű erőforrása, amely után az erőforrásokat, amelyek a körkörös függőségi viszonyban vannak telepítve. Tegyük fel például, két olyan virtuális gépet telepít, de tulajdonságokat meg kell adni az egyes, amely a másikra hivatkozik. A következő sorrendben telepíthetők:

1. vm1
2. vm2
3. Vm1 kiterjesztés vm1 és vm2 virtuális gépnek függ. A bővítmény beállítása, amely azt lekérése vm2 virtuális gépnek vm1 értékeket.
4. Bővítmény vm2 virtuális gépnek a vm1 és vm2 virtuális gépnek függ. A bővítmény beállítása értékek, amelyek azt lekérése vm1 vm2 virtuális gépnek.

Értékelése a telepítési sorrendet, és a függőségi hibák feloldására vonatkozó információkért lásd: [hibaelhárítás általános az Azure-telepítés az Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="next-steps"></a>További lépések
* Függőségek telepítése során hibaelhárítással kapcsolatos információkért lásd: [hibaelhárítás általános az Azure-telepítés az Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Azure Resource Manager-sablonok létrehozásával kapcsolatos további tudnivalókért lásd: [sablonok készítése](resource-group-authoring-templates.md). 
* A sablon elérhető funkciókat listájáért lásd: [sablonfüggvényei](resource-group-template-functions.md).

