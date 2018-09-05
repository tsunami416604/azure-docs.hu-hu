---
title: Az Azure Service Bus SQLFilter szintaxis referenciája |} A Microsoft Docs
description: SQLFilter szintaxis kapcsolatos adatokat.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/05/2018
ms.author: spelluru
ms.openlocfilehash: c94ffed753ebf8fddbd553977c5d733f2306971d
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43698332"
---
# <a name="sqlfilter-syntax"></a>SQLFilter-szintaxis

A *SqlFilter* objektum egy példányát a [SqlFilter osztály](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), és a egy SQL-nyelv alapján végzett szűrés kifejezés kiértékelt jelöli egy [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Egy SQL-92 normál egy részét támogatja.  
  
 Ez a témakör felsorolja a SqlFilter szintaxis részleteit.  
  
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
  
-   `<scope>` egy nem kötelező karakterlánc, amely a hatóköre a `<property_name>`. Érvényes értékek a következők `sys` vagy `user`. A `sys` érték rendszerek, ahol `<property_name>` egy nyilvános tulajdonság neve a [BrokeredMessage osztály](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` azt jelzi, hogy a felhasználói hatókör ahol `<property_name>` kulcsa, a [BrokeredMessage osztály](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) szótárban. `user` hatókör esetén az alapértelmezett hatókör `<scope>` nincs megadva.  
  
## <a name="remarks"></a>Megjegyzések

Az egy nem létező rendszertulajdonság elérésére tett kísérlet nem megfelelő, bár az egy nem létező felhasználói tulajdonságot elérésére tett kísérlet nem hiba. Ehelyett egy nem létező felhasználói tulajdonságot belsőleg abban az esetben minősül ismeretlen érték. Ismeretlen érték kezelik, speciálisan operátor kiértékelés során.  
  
## <a name="propertyname"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumentumok  

 `<regular_identifier>` a következő reguláris kifejezésnek által jelölt karakterlánc:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
Ez a szintaxis azt jelenti, hogy bármilyen karakterlánc, amely betűvel kezdődik, és a egy vagy több aláhúzás/levél/számjegy követ.  
  
`[:IsLetter:]` azt jelenti, hogy bármilyen Unicode betűvel kategorizált Unicode-karakter. `System.Char.IsLetter(c)` adja vissza `true` Ha `c` Unicode betűvel van.  
  
`[:IsDigit:]` azt jelenti, hogy bármely Unicode karaktert, kategorizált decimális számjegyet. `System.Char.IsDigit(c)` adja vissza `true` Ha `c` Unicode számjegyet.  
  
A `<regular_identifier>` nem lehet foglalt kulcsszó.  
  
`<delimited_identifier>` van bármilyen karakterlánc, amely a bal vagy jobb szögletes zárójelek ([]) közé van zárva. Záró szögletes zárójel jelenik meg két jobb oldali szögletes zárójeleket. Az alábbi példák `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` van bármilyen, az idézőjelek közé zárt karakterlánc. Egy dupla idézőjel azonosítójában jelenik meg két dupla idézőjelekkel együtt. Nem ajánlott a határolójeles azonosítókat használni, mert azt egy karakterlánc-konstansra könnyen összetéveszthetők. Ha lehetséges használjuk egy tagolt azonosítóját. Az alábbiakban egy példát a `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>A minta  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Megjegyzések
  
`<pattern>` egy kifejezés, amely abban az esetben minősül karakterláncnak kell lennie. A LIKE operátor szerepel a mintaként szolgál.      A következő helyettesítő karaktereket tartalmazhat:  
  
-   `%`: Nulla vagy több karaktert bármilyen karakterlánc.  
  
-   `_`: Bármely egy karakter.  
  
## <a name="escapechar"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Megjegyzések  

`<escape_char>` egy kifejezés, amely abban az esetben minősül 1 hosszúságú karakterláncnak kell lennie. A LIKE operátor szerepel a helyettesítő karakterek szolgál.  
  
 Ha például `property LIKE 'ABC\%' ESCAPE '\'` megegyezik `ABC%` ahelyett, hogy egy karakterlánc kezdetű `ABC`.  
  
## <a name="constant"></a>állandó  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumentumok  
  
-   `<integer_constant>` egy karakterlánc, szám, amely nem az idézőjelek közé zárt, és nem tartalmaznak a tizedesvessző. Az értékek, tárolódnak `System.Int64` belső használatra, és hajtsa végre az egyező tartományba.  
  
     Hosszú állandókat a következők:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` egy olyan szám, amely nem az idézőjelek közé zárt, és tartalmazzák a tizedesvesszőtől karakterlánc. Formájában tárolja az értékeket `System.Double` belső használatra, és kövesse a ugyanazon tartomány/pontosság.  
  
     Egy jövőbeli verziójában ez a szám lehet, hogy kell tárolni a különböző adattípusú támogatják a pontos szám szemantikát, ezért meg kell nem támaszkodhat, az alapul szolgáló adattípusa `System.Double` a `<decimal_constant>`.  
  
     Tizedes tört állandókat a következők:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` egy szám nyelven írt tudományos jelöléssel van. Formájában tárolja az értékeket `System.Double` belső használatra, és kövesse a ugyanazon tartomány/pontosság. Hozzávetőleges száma állandókat a következők:  
  
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

A kulcsszavak képviseli logikai állandók **igaz** vagy **hamis**. Az értékek, tárolódnak `System.Boolean`.  
  
## <a name="stringconstant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Megjegyzések  

A karakterlánc-állandókat szimpla idézőjelek közé kell tenni, és bármilyen érvényes Unicode-karaktert tartalmazhat. Egy egyszeres idézőjel vagy aposztróf ágyazva egy karakterlánc-konstansra cube.addmeasurecolumn két szimpla idézőjelek között.  
  
## <a name="function"></a>függvény  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Megjegyzések
  
A `newid()` függvény visszaad egy **System.Guid** által generált a `System.Guid.NewGuid()` metódust.  
  
A `property(name)` függvény által hivatkozott tulajdonság értékét adja vissza `name`. A `name` értéke lehet bármely érvényes kifejezés, amely egy karakterláncértéket ad vissza.  
  
## <a name="considerations"></a>Megfontolandó szempontok
  
Vegye figyelembe a következőket [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) szemantikát:  
  
-   A tulajdonságnevek megkülönböztetik a kis-és nagybetűket.  
  
-   Operátorok hajtsa végre a C# implicit konverzió szemantikát, amikor csak lehetséges.  
  
-   Rendszer olyan érhető el a nyilvános tulajdonságok [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) példányok.  
  
    Vegye figyelembe a következőket `IS [NOT] NULL` szemantikát:  
  
    -   `property IS NULL` abban az esetben minősül `true` , ha a tulajdonság nem létezik, vagy a tulajdonság értéke `null`.  
  
### <a name="property-evaluation-semantics"></a>Értékelés szemantika tulajdonság  
  
-   Kísérlet egy nem létező rendszertulajdonság kiértékelheti, hogy jelez egy [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) kivétel.  
  
-   Egy nem létező tulajdonságot belsőleg abban az esetben minősül **ismeretlen**.  
  
 Ismeretlen értékelése az aritmetikai operátor:  
  
-   Binární operátory, ha a bal oldalon, illetve a operandusok jobb oldalán a **ismeretlen**, akkor az eredmény **ismeretlen**.  
  
-   Az egyoperandusú operátorokat, ha egy operandus **ismeretlen**, akkor az eredmény **ismeretlen**.  
  
 A bináris összehasonlító operátorok ismeretlen értékelése:  
  
-   Ha a bal oldalon, illetve a jobb oldalán található operandusok **ismeretlen**, akkor az eredmény **ismeretlen**.  
  
 Az ismeretlen értékelési `[NOT] LIKE`:  
  
-   Ha bármely operand **ismeretlen**, akkor az eredmény **ismeretlen**.  
  
 Az ismeretlen értékelési `[NOT] IN`:  
  
-   Ha a bal oldali operandusához **ismeretlen**, akkor az eredmény **ismeretlen**.  
  
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
  
### <a name="operator-binding-semantics"></a>Operátor kötés szemantikát
  
-   Összehasonlító operátorok például `>`, `>=`, `<`, `<=`, `!=`, és `=` hajtsa végre az azonos szemantikákkal, kötelező adatok típusa promóciókat és implicit konverzió a C# operátort.  
  
-   Aritmetikai operátor például `+`, `-`, `*`, `/`, és `%` hajtsa végre az azonos szemantikákkal, kötelező adatok típusa promóciókat és implicit konverzió a C# operátort.

## <a name="next-steps"></a>További lépések

- [SQLFilter osztály (.NET-keretrendszer)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [SQLFilter osztály (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [SQLRuleAction osztályban](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
