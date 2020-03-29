---
title: SQL-állandók az Azure Cosmos DB-ben
description: Megtudhatja, hogy az Azure Cosmos DB SQL-lekérdezési állandói hogyan használhatók egy adott adatérték ábrázolására
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: cca62c358037dbe99fd16746ee081b1540161df2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873420"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Az Azure Cosmos DB SQL-lekérdezési állandói  

 Az állandó, más néven konstans vagy skaláris érték egy adott adatértéket jelölő szimbólum. Az állandó formátuma az általa képviselt érték adattípusától függ.  
  
 **Támogatott skaláris adattípusok:**  
  
|**Típus**|**Értékek sorrendje**|  
|-|-|  
|**Meghatározatlan**|Egyetlen érték: **nem definiált**|  
|**Null**|Egyetlen érték: **null**|  
|**Logikai**|Értékek: **hamis**, **igaz**.|  
|**Szám**|Dupla pontosságú lebegőpontos szám, IEEE 754 szabvány.|  
|**Sztring**|Nulla vagy több Unicode karakterből álló sorozat. A karakterláncokat egy- vagy idézőjelek közé kell tenni.|  
|**Tömb**|Nulla vagy több elem sorozata. Minden elem bármilyen skaláris adattípus értéke lehet, kivéve **az Undefined értéket.**|  
|**Objektum**|Nulla vagy több név/érték pár rendezetlen halmaza. A név Unicode karakterlánc, az érték bármilyen skaláris adattípus lehet, kivéve **az Undefined értéket.**|  
  
## <a name="syntax"></a><a name="bk_syntax"></a>Szintaxis
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
##  <a name="arguments"></a><a name="bk_arguments"></a>Érvek
  
* `<undefined_constant>; Undefined`  
  
  Az Undefined típusú meg határozatlan értéket jelöli.  
  
* `<null_constant>; null`  
  
  **Null**típusú **null** értéket jelöl.  
  
* `<boolean_constant>`  
  
  Logikai típusú állandót jelöl.  
  
* `false`  
  
  Logikai típusú **hamis** értéket jelöl.  
  
* `true`  
  
  A Logikai típus **valódi** értékét jelöli.  
  
* `<number_constant>`  
  
  Állandót jelöl.  
  
* `decimal_literal`  
  
  A decimális konstansok decimális jelöléssel vagy tudományos jelöléssel ábrázolt számok.  
  
* `hexadecimal_literal`  
  
  A hexadecimális konstansok a "0x" előtaggal, majd egy vagy több hexadecimális számjegyekkel ábrázolt számok.  
  
* `<string_constant>`  
  
  String típusú állandót jelöl.  
  
* `string _literal`  
  
  A karakterlánc-konstansok olyan Unicode karakterláncok, amelyeket nulla vagy több Unicode karakter vagy escape-sorozat jelöl. A karakterlánc-konstansok egyszeres idézőjelek (aposztróf: ' ) vagy dupla idézőjelek (idézőjelek: » közé vannak titeket mellékelve.  
  
  A következő escape-szekvenciák megengedettek:  
  
|**Escape-sorozat**|**Leírás**|**Unicode karakter**|  
|-|-|-|  
|\\'|aposztróf (')|U+0027|  
|\\"|idézőjel (")|U+0022|  
|\\\ |fordított solidus\\( )|U+005C|  
|\\/|solidus (/)|U+002F|  
|\b|Backspace|U+0008|  
|\f|űrlap-hírcsatorna|U+000C|  
|\n|vonali előtolás|U+000A|  
|\r|kocsi vissza|U+000D|  
|\t|Lapon|U+0009|  
|\uXXXX|4 hexadecimális számjegy által definiált Unicode karakter.|U+XXXX|  

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB .NET-mintái](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dokumentumadatok modellezése](modeling-data.md)
