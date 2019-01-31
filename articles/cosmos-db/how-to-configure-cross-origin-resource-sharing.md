---
title: Eltérő eredetű erőforrások megosztása (CORS) az Azure Cosmos DB
description: Ez a cikk bemutatja, hogyan konfigurálhatja az eltérő eredetű erőforrások megosztása (CORS) az Azure Cosmos DB az Azure portal és az Azure Resource Manager-sablonok használatával.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2018
ms.author: dech
ms.openlocfilehash: 689b0bb08e300efc7c5e93dbf346040b82a7c52d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468478"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Eltérő eredetű erőforrások megosztása (CORS) konfigurálása 

Eltérő eredetű erőforrások megosztása (CORS) egy HTTP-funkció, amely lehetővé teszi egy adott tartományban futó webes alkalmazás egy másik tartományban lévő erőforrások eléréséhez. Webböngészők azonoseredet-, amely megakadályozza az API-k egy másik tartományban származó weblap néven ismert biztonsági korlátozással akadályozzák meg. Azonban a CORS biztonságos megoldást nyújt, hogy a forrástartomány API-k hívásához egy másik tartományban található. Fő SQL API-t az Azure Cosmos DB mostantól támogatja az eltérő eredetű erőforrások megosztása (CORS) a "allowedOrigins" fejléc használatával. Miután engedélyezte a CORS-támogatás az Azure Cosmos-fiókja, csak a hitelesített kérelmeket meghatározni, hogy azok a megadott szabályok szerint engedélyezettek értékeli ki.

Konfigurálhatja az eltérő eredetű erőforrások megosztásának (CORS) beállítása az Azure Portalon vagy Azure Resource Manager-sablon. Fő SQL API-t az Azure Cosmos DB támogatja a egy JavaScript-függvénytárat, amely egyaránt használható a Node.js és a böngésző alapú környezetekben. Ebben a könyvtárban most CORS-támogatás előnyeinek életbe átjáró mód használatakor. Nem tartozik a funkció használatához szükséges: ügyféloldali konfiguráció. CORS-támogatás, az erőforrások közvetlenül a böngészőből közvetlenül férhet hozzá az Azure Cosmos DB segítségével a [JavaScript-kódtár](https://www.npmjs.com/package/@azure/cosmos) vagy közvetlenül a [REST API-val](https://docs.microsoft.com/rest/api/cosmos-db/) egyszerű műveletekhez. 

## <a name="enable-cors-support-from-azure-portal"></a>Az Azure Portalról CORS-támogatásának engedélyezése

A következő lépések használatával engedélyezze az eltérő eredetű erőforrások megosztása az Azure portal használatával:

1. Keresse meg az Azure cosmos DB-fiók. Nyissa meg a **CORS** panelen.

2. Adja meg, hogy az Azure Cosmos DB-fiók eltérő eredetű hívásokat indíthatna vesszővel elválasztott listáját. Ha például `https://www.mydomain.com`, `https://mydomain.com`, `https://api.mydomain.com`. Helyettesítő karakter is használható "\*" összes forrás engedélyezéséhez, és válassza ki **küldés**. 

   > [!NOTE]
   > Jelenleg a helyettesítő karakterek nem használható a tartománynév részeként. Például `https://*.mydomain.net` formátum jelenleg nem támogatott. 
   
   ![Engedélyezze a eredetű erőforrások megosztása az Azure portal használatával](./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png)
 
## <a name="enable-cors-support-from-resource-manager-template"></a>A Resource Manager-sablon a CORS-támogatásának engedélyezése

CORS engedélyezése Resource Manager-sablon használatával, bármely létező sablonhoz adja hozzá a "cors" szakasz "allowedOrigins" tulajdonság. A következő JSON-ra, amelyek egy sablon, amely létrehoz egy új Azure Cosmos-fiókot a CORS engedélyezése mellett.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "name": "test",
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "apiVersion": "2015-04-08",
            "location": "East US 2",
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "Session",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "test-eastus2",
                        "failoverPriority": 0,
                        "locationName": "East US 2"
                    }
                ],
                "cors": [
                    {
                        "allowedOrigins": "*"
                    }
                ]
            },
            "dependsOn": [
            ]
        }
    ]
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>Egy böngészőből az Azure Cosmos DB a JavaScript-kódtár használatával

Az Azure Cosmos DB a JavaScript-kódtár még ma, csak a tár a csomag rendszerrel szállított CommonJS verziója van. Használja az ebben a könyvtárban, a böngészőből, egy eszközt, például a Rollup vagy webpack használatával készült használatával hozzon létre egy böngésző kompatibilis kódtár rendelkezik. Egyes Node.js-kódtárak böngésző mocks őket van. Az alábbiakban látható egy példa, amely rendelkezik a szükséges utánzatként funkcionáló beállításokat webpack használatával készült konfigurációs fájlt.

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  node: {
    net: "mock",
    tls: "mock"
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist")
  }
};
```
 
Íme egy [kódminta](https://github.com/christopheranderson/cosmos-browser-sample) használó TypeScript és a webpack használatával készült és az Azure Cosmos DB a JavaScript SDK-t hozhat létre egy Teendőkezelő alkalmazást, amely valós idejű frissítéseket küld, amikor új elem jön létre.
Ajánlott eljárásként ne használja a az elsődleges kulcsot az Azure Cosmos DB böngészőből való kommunikációhoz. Ehelyett használja az erőforrás-jogkivonatokat való kommunikációhoz. Erőforrás-jogkivonatokat kapcsolatos további információkért lásd: [Azure Cosmos DB-hez való hozzáférés biztosítása](secure-access-to-data.md#resource-tokens) cikk.

## <a name="next-steps"></a>További lépések

Az Azure Cosmos-fiókhoz egyéb módjaival kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Windows tűzfal konfigurálása az Azure Cosmos DB](how-to-configure-firewall.md) cikk.

* [Virtuális hálózat és alhálózat-alapú hozzáférés az Azure Cosmos DB-fiók konfigurálása](how-to-configure-vnet-service-endpoint.md)
    

