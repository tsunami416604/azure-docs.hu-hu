---
title: Több forrásból származó erőforrás-megosztás (CORS) az Azure Cosmos DB-ben
description: Ez a cikk ismerteti, hogyan konfigurálhatja a cross-origin erőforrás-megosztás (CORS) az Azure Cosmos DB az Azure Portal és az Azure Resource Manager-sablonok használatával.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: dech
ms.openlocfilehash: 7a487cb10965a379a0a418efaa061be88c5d10dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77082981"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Eredetközi erőforrás-megosztás (CORS) konfigurálása

Az originek közötti erőforrás-megosztás (CORS) egy HTTP-szolgáltatás, amely lehetővé teszi, hogy az egyik tartományban futó webalkalmazások hozzáférjenek egy másik tartomány erőforrásaihoz. A webböngészők azonos eredetű házirendként ismert biztonsági korlátozást alkalmaznak, amely megakadályozza, hogy egy weblap egy másik tartományban API-kat hívjon meg. A CORS azonban biztonságos módot biztosít arra, hogy az eredeti tartomány egy másik tartományAPI-kat hívjon meg. Az Azure Cosmos DB Core (SQL) API-ja mostantól támogatja a cross-origin erőforrás-megosztást (CORS) az "allowedOrigins" fejléc használatával. Miután engedélyezte a CORS-támogatást az Azure Cosmos-fiókhoz, csak a hitelesített kérelmek et értékeli ki a rendszer annak megállapítására, hogy azok a megadott szabályok szerint engedélyezettek-e.

Konfigurálhatja a kereszt-eredetű erőforrás-megosztási (CORS) beállítást az Azure Portalon vagy egy Azure Resource Manager-sablonból. A Core (SQL) API-t használó Cosmos-fiókok esetén az Azure Cosmos DB támogatja a JavaScript-kódtárat, amely node.js és böngészőalapú környezetekben is működik. Ez a könyvtár most már kihasználhatja a CORS-támogatás előnyeit az Átjáró mód használatakor. A szolgáltatás használatához nincs szükség ügyféloldali konfigurációra. A CORS-támogatással a böngészőből származó erőforrások közvetlenül hozzáférhetnek az Azure Cosmos DB-hez a [JavaScript-kódtáron](https://www.npmjs.com/package/@azure/cosmos) keresztül vagy közvetlenül a [REST API-ból](https://docs.microsoft.com/rest/api/cosmos-db/) az egyszerű műveletekhez.

> [!NOTE]
> A CORS-támogatás csak az Azure Cosmos DB Core (SQL) API-hoz alkalmazható és támogatott. Nem alkalmazható az Azure Cosmos DB API-k cassandra, Gremlin vagy MongoDB, mivel ezek a protokollok nem http-t használnak az ügyfél és a kiszolgáló közötti kommunikációhoz.

## <a name="enable-cors-support-from-azure-portal"></a>CORS-támogatás engedélyezése az Azure Portalról

Az alábbi lépésekkel engedélyezheti az Originek közötti erőforrás-megosztást az Azure Portal használatával:

1. Keresse meg az Azure cosmos DB-fiókját. Nyissa ki a **CORS** pengét.

2. Adja meg az origins, amely az Azure Cosmos DB-fiókhoz több eredetű hívásokat adhat meg. Például, `https://www.mydomain.com` `https://mydomain.com`, `https://api.mydomain.com`. Helyettesítő karaktert "\*is használhat az összes eredet engedélyezéséhez, és válassza a **Küldés lehetőséget.** 

   > [!NOTE]
   > Jelenleg nem használhat helyettesítő karaktereket a tartománynév részeként. Például `https://*.mydomain.net` a formátum még nem támogatott. 

   ![Az originális erőforrások megosztásának engedélyezése az Azure Portalon](./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png)

## <a name="enable-cors-support-from-resource-manager-template"></a>CORS-támogatás engedélyezése az Erőforrás-kezelő sablonból

A CORS resource manager sablon használatával történő engedélyezéséhez adja hozzá a "cors" szakaszt "allowedOrigins" tulajdonsággal bármely meglévő sablonhoz. A következő JSON egy példa egy sablont, amely létrehoz egy új Azure Cosmos-fiókot cors engedélyezve van.

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "cors": [
      {
        "allowedOrigins": "*"
      }
    ]
  }
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>Az Azure Cosmos DB JavaScript-kódtár használata böngészőből

Ma az Azure Cosmos DB JavaScript-kódtár csak a csomaggal együtt szállított könyvtár CommonJS-verzióját használja. Ahhoz, hogy ezt a tárat a böngészőből használhassa, egy olyan eszközt kell használnia, mint az Összesítés vagy a Webpack, hogy böngészőkompatibilis tárat hozzon létre. Egyes Node.js könyvtárak kell böngésző gúnyolódik számukra. Az alábbi példa egy webpack konfigurációs fájlt mutat be, amely rendelkezik a szükséges próbabeállításokkal.

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
 
Az alábbiakban egy [kódminta,](https://github.com/christopheranderson/cosmos-browser-sample) amely typescript és webpack az Azure Cosmos DB JavaScript SDK könyvtár létrehozásához todo alkalmazás, amely küld valós idejű frissítéseket, amikor új elemek jönnek létre.
Ajánlott eljárásként ne használja az elsődleges kulcsot az Azure Cosmos DB-vel való kommunikációhoz a böngészőből. Ehelyett használja az erőforrás-jogkivonatokat a kommunikációhoz. Az erőforrás-jogkivonatokról az [Azure Cosmos DB-hez való hozzáférés védelme](secure-access-to-data.md#resource-tokens) című cikkben olvashat bővebben.

## <a name="next-steps"></a>További lépések

Az Azure Cosmos-fiók védelmének egyéb módjairól az alábbi cikkekben olvashat:

* [Konfigurálja a tűzfal at Azure Cosmos DB](how-to-configure-firewall.md) cikk.

* [Virtuális hálózat és alhálózati hozzáférés konfigurálása az Azure Cosmos DB-fiókjához](how-to-configure-vnet-service-endpoint.md)
