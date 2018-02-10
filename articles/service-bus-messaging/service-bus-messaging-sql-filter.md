---
title: Az Azure Service Bus SQLFilter szintaxis referencia |} Microsoft Docs
description: SQLFilter nyelvtan adatait.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/05/2018
ms.author: sethm
ms.openlocfilehash: ec9d728eb31eb979e82bfb53cf619f823750e65c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="sqlfilter-syntax"></a>SQLFilter szintaxis

A *SqlFilter* objektum példánya a [SqlFilter osztály](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), és egy SQL nyelvi alapú kifejezést kiértékelt jelöli egy [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Egy SqlFilter támogatja az SQL-92 szabvány egy részét.  
  
 Ez a témakör SqlFilter nyelvtan részleteit sorolja fel.  
  
```  
<predicate ::=  
      { NOT <predicate> }  
      | <predicate> AND <predicate>  
      | <predicate> OR <predicate>  
      | <expression> { = | <> | != | > | >= | < | <= } <expression>  
      | <property> IS [NOT] NULL  
      | <expression> [NOT] IN ( <expression> [, ...n] )  
      | <expression> [NOT] LIKE <pattern> [ESCAPE <escape_char>]  
      | EXISTS ( <property> )  
      | ( <predicate> )  
  
```  
  
```  
<expression> ::=  
      <constant>   
      | <function>  
      | <property>  
      | <expression> { + | - | * | / | % } <expression>  
      | { + | - } <expression>  
      | ( <expression> )  
  
```  
  
```  
<property> :=   
       [<scope> .] <property_name>  
  
```  
  
## <a name="arguments"></a>Argumentumok  
  
-   `<scope>`egy nem kötelező karakterlánc, amely a hatóköre a `<property_name>`. Érvényes értékek a következők `sys` vagy `user`. A `sys` érték rendszerek ahol `<property_name>` egy nyilvános tulajdonság neve a [BrokeredMessage osztály](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user`jelzi a felhasználó hatókör ahol `<property_name>` kulcs a [BrokeredMessage osztály](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) szótárban. `user`hatókör esetén az alapértelmezett hatókör `<scope>` nincs megadva.  
  
## <a name="remarks"></a>Megjegyzések

Egy nem létező rendszertulajdonság elérésére tett kísérlet hiba, kiszolgáló, míg egy nem létező felhasználói tulajdonságot elérésére tett kísérlet nem hiba. Ehelyett egy nem létező felhasználói tulajdonságot belsőleg történik ismeretlen érték. Ismeretlen érték kifejezetten operátor kiértékelése közben a rendszer kezeli.  
  
## <a name="propertyname"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumentumok  

 `<regular_identifier>`a karakterlánc a következő reguláris kifejezésnek jelképezi:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
Ebben a nyelvtanban azt jelenti, hogy bármilyen karakterlánc, amely betűvel kezdődik, és egy vagy több aláhúzás/betűvel vagy számjeggyel követi.  
  
`[:IsLetter:]`azt jelenti, hogy bármely Unicode karaktert, amely Unicode betűvel van kategóriába sorolni. `System.Char.IsLetter(c)`Visszaadja `true` Ha `c` egy Unicode betűjele.  
  
`[:IsDigit:]`azt jelenti, hogy bármely Unicode karaktert, mint egy decimális számjegyet van besorolva. `System.Char.IsDigit(c)`Visszaadja `true` Ha `c` Unicode számjegy.  
  
A `<regular_identifier>` nem lehet fenntartott kulcsszó.  
  
`<delimited_identifier>`van bármilyen karakterlánc, amely szimpla balra vagy jobbra szögletes zárójelek ([]). Záró szögletes zárójel két jobb oldali kapcsos zárójeleket jelzi. A következő példákban `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>`van bármilyen, amelyek dupla idézőjelek közé zárt karakterlánc. Dupla idézőjel azonosítóban ki kettő darab idézőjelre. Nem ajánlott, hogy használja a határolójeles azonosítók, mert azt egy karakterlánc-konstansra könnyen összetéveszthetők. Ha lehetséges használja a tagolt azonosítója. Az alábbiakban egy példát `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>Minta  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Megjegyzések
  
`<pattern>`a string típusúként kiértékelt kifejezésnek kell lennie. A LIKE operátor mintaként szolgál.      A következő helyettesítő karaktereket tartalmazhat:  
  
-   `%`: Bármilyen karakterlánc nulla vagy több.  
  
-   `_`: Bármilyen karakter.  
  
## <a name="escapechar"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Megjegyzések  

`<escape_char>`1 hosszúságú karakterláncként kiértékelt kifejezésnek kell lennie. A LIKE operátor helyettesítő karakterek szolgál.  
  
 Például `property LIKE 'ABC\%' ESCAPE '\'` megfelelő `ABC%` ahelyett, hogy egy karakterláncot kezdetű `ABC`.  
  
## <a name="constant"></a>állandó  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumentumok  
  
-   `<integer_constant>`egy olyan karakterlánc, amely nem az idézőjelek közé zárt, és nem tartalmaz a tizedesjegyek számát. Az értékek tárolt `System.Int64` belső, és hajtsa végre ugyanezt a porttartományt.  
  
     Hosszú állandókat az alábbiak:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>`egy olyan karakterlánc, szám, amely nem az idézőjelek közé zárt, és tartalmaz a tizedesvessző. Az értékek tárolt `System.Double` belső, és kövesse a ugyanazon tartomány pontosság.  
  
     Egy jövőbeli verziójában ez a szám tárolódhat a különböző adattípusú támogatja a pontos szám szemantikáját, így nem támaszkodhat a tényen az alapul szolgáló adattípusa `System.Double` a `<decimal_constant>`.  
  
     A következő példák decimális állandók:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>`egy szám nyelven írt tudományos jelölés van. Az értékek tárolt `System.Double` belső, és kövesse a ugyanazon tartomány pontosság. A következő példák hozzávetőleges száma állandók:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="booleanconstant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Megjegyzések  

A kulcsszavak jelölik a logikai állandók **igaz** vagy **hamis**. Az értékek tárolt `System.Boolean`.  
  
## <a name="stringconstant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Megjegyzések  

A karakterlánckonstansokat egyetlen idézőjelek közé vannak, és a érvényes Unicode-karaktereket tartalmaz. Egy olyan karakterlánc-konstansra ágyazott szimpla idézőjel szerepel, mint két darab szimpla idézőjelek között.  
  
## <a name="function"></a>függvény  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Megjegyzések
  
A `newid()` működéséhez értéket ad vissza egy **System.Guid** állítja elő a `System.Guid.NewGuid()` metódust.  
  
A `property(name)` függvény által hivatkozott tulajdonságának `name`. A `name` értéke lehet bármely érvényes kifejezés, amely egy karakterláncértéket ad vissza.  
  
## <a name="considerations"></a>Megfontolandó szempontok
  
Vegye figyelembe a következőket [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) szemantika:  
  
-   A tulajdonságnevek nem különböztetik meg.  
  
-   Operátorok hajtsa végre a C# implicit konverzió szemantikáját amikor csak lehetséges.  
  
-   Rendszer olyan nyilvános tulajdonságok felfedett [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) példányok.  
  
    Vegye figyelembe a következőket `IS [NOT] NULL` szemantika:  
  
    -   `property IS NULL`kiértékelése `true` , ha a tulajdonság nem létezik, vagy a tulajdonság értéke `null`.  
  
### <a name="property-evaluation-semantics"></a>Tulajdonság értékelési szemantikájának módosítása  
  
-   Kísérlet egy nem létező rendszer tulajdonságának kiértékelése jelez a [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) kivétel.  
  
-   Egy nem létező tulajdonságot belsőleg történik **ismeretlen**.  
  
 Ismeretlen értékelése az aritmetikai operátor:  
  
-   A bináris operátor, ha a bal vagy jobb oldalán operandusok kiértékelése **ismeretlen**, akkor az eredmény **ismeretlen**.  
  
-   Az egyoperandusú operátorokat, ha egy operandus kiértékelése **ismeretlen**, akkor az eredmény **ismeretlen**.  
  
 A bináris összehasonlító operátorok ismeretlen értékelése:  
  
-   Ha a bal vagy jobb oldalán operandusok kiértékelése **ismeretlen**, akkor az eredmény **ismeretlen**.  
  
 Az ismeretlen értékelési `[NOT] LIKE`:  
  
-   Ha bármely operandus kiértékelése **ismeretlen**, akkor az eredmény **ismeretlen**.  
  
 Az ismeretlen értékelési `[NOT] IN`:  
  
-   Ha a bal oldali operandusa kiértékelése **ismeretlen**, akkor az eredmény **ismeretlen**.  
  
 Az ismeretlen értékelési **és** operátor:  
  
```  
+---+---+---+---+  
|AND| T | F | U |  
+---+---+---+---+  
| T | T | F | U |  
+---+---+---+---+  
| F | F | F | F |  
+---+---+---+---+  
| U | U | F | U |  
+---+---+---+---+  
```  
  
 Az ismeretlen értékelési **vagy** operátor:  
  
```  
+---+---+---+---+  
|OR | T | F | U |  
+---+---+---+---+  
| T | T | T | T |  
+---+---+---+---+  
| F | T | F | U |  
+---+---+---+---+  
| U | T | U | U |  
+---+---+---+---+  
```  
  
### <a name="operator-binding-semantics"></a>Operátor kötés szemantikájának módosítása
  
-   Összehasonlító operátorok például `>`, `>=`, `<`, `<=`, `!=`, és `=` hajtsa végre a azonos szemantikákkal, a C# operátor adatok típusa előléptetések és implicit konverzió kötelező.  
  
-   Aritmetikai operátor például `+`, `-`, `*`, `/`, és `%` hajtsa végre a azonos szemantikákkal, a C# operátor adatok típusa előléptetések és implicit konverzió kötelező.

## <a name="next-steps"></a>További lépések

- [SQLFilter osztály (.NET-keretrendszer)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [SQLFilter osztály (.NET-szabvány)](/dotnet/api/microsoft.azure.servicebus.filters.sqlfilter)
- [SQLRuleAction class](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)