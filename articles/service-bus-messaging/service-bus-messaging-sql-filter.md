---
title: Az Azure Service Bus SQLFilter szintaxisának hivatkozása | Microsoft dokumentumok
description: Ez a cikk az SQLFilter nyelvtanával kapcsolatos részleteket tartalmaz. Az SqlFilter az SQL-92 szabvány egy részét támogatja.
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
ms.date: 09/05/2018
ms.author: spelluru
ms.openlocfilehash: d5a8e165fcee23c5feecd5935983dd77d3ec6c30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759663"
---
# <a name="sqlfilter-syntax"></a>SQLFilter-szintaxis

Az *SqlFilter-objektum* az [SqlFilter osztály](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)egy példánya, amely egy [BrokeredMessage rendszerrel kiértékelt](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)SQL-nyelvalapú szűrőkifejezést jelöl. Az SqlFilter az SQL-92 szabvány egy részét támogatja.  
  
 Ez a témakör az SqlFilter nyelvtanának részleteit sorolja fel.  
  
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
  
-   `<scope>`választható karakterlánc, amely a hatókörét `<property_name>`jelzi. Az érvényes `sys` `user`értékek vagy . Az `sys` érték azt a `<property_name>` rendszerhatókört jelzi, ahol a [BrokeredMessage osztály](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)köztulajdonneve. `user`a `<property_name>` [BrokeredMessage osztályszótár](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) egyik kulcsa a felhasználói hatókört jelzi. `user`a hatókör az `<scope>` alapértelmezett hatókör, ha nincs megadva.  
  
## <a name="remarks"></a>Megjegyzések

A nem létező rendszertulajdon elérésére tett kísérlet hiba, míg egy nem létező felhasználói tulajdonság elérésére tett kísérlet nem hiba. Ehelyett egy nem létező felhasználói tulajdonság belsőleg kiértékelése ismeretlen értékként történik. Egy ismeretlen értéket a kezelő értékelése során kezel a rendszer.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumentumok  

 `<regular_identifier>`a következő reguláris kifejezéssel ábrázolt karakterlánc:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
Ez a nyelvtan minden olyan karakterláncot jelent, amely betűvel kezdődik, és amelyet egy vagy több aláhúzás/betű/számjegy követ.  
  
`[:IsLetter:]`minden Unicode karaktert jelent, amely Unicode betűként van kategorizálva. `System.Char.IsLetter(c)`visszaadja, `true` ha `c` Unicode betű.  
  
`[:IsDigit:]`bármely Tizedesjegyként kategorizált Unicode karaktert jelent. `System.Char.IsDigit(c)`ha Unicode-jegy, akkor ad vissza. `true` `c`  
  
A `<regular_identifier>` nem lehet fenntartott kulcsszó.  
  
`<delimited_identifier>`minden olyan karakterlánc, amely bal/jobb szögletes zárójelben van ([]). A jobb oldali szögletes zárójel két jobb oldali szögletes zárójelként jelenik meg. Az alábbi példák: `<delimited_identifier>`  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>`minden olyan karakterlánc, amely idézőjelek közé van zárva. Az azonosítóban a dupla idézőjel két idézőjelként jelenik meg. Nem ajánlott az idézett azonosítók használata, mert könnyen összetéveszthető egy karakterlánc-állandóval. Ha lehetséges, használjon tagolt azonosítót. Az alábbi példa `<quoted_identifier>`a következőkre mutat be:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>Minta  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Megjegyzések
  
`<pattern>`karakterláncként kiértékelt kifejezésnek kell lennie. Ezt használják, mint a minta a LIKE operátor.      A következő helyettesítő karaktereket tartalmazhatja:  
  
-   `%`: Nulla vagy több karakterből álló karakterlánc.  
  
-   `_`: Bármilyen karakter.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Megjegyzések  

`<escape_char>`olyan kifejezésnek kell lennie, amelyet hossz-karakterláncként értékel ki 1. A LIKE operátor menekülési karaktereként használja.  
  
 Például `property LIKE 'ABC\%' ESCAPE '\'` a `ABC%` kezdő karakterlánc helyett `ABC`nem egyezik.  
  
## <a name="constant"></a>Állandó  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumentumok  
  
-   `<integer_constant>`Olyan számsorozat, amely nem idézőjelek közé van tizedesszámba. Az értékek belsőként `System.Int64` tárolódnak, és ugyanazt a tartományt követik.  
  
     Ezek a példák a hosszú állandók:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>`Olyan számsorozat, amely nem idézőjelek közé van bezárva, és tizedesvesszőt tartalmaz. Az értékek belső `System.Double` legeltetése belsőleg tárolódik, és ugyanazt a tartományt/pontosságot követik.  
  
     Egy későbbi verzióban ez a szám egy másik adattípusban tárolható a pontos számszemantika támogatása érdekében, ezért ne hagyatkozzon arra a tényre, hogy `System.Double` az alapul szolgáló adattípus a hoz alapul. `<decimal_constant>`  
  
     Az alábbi példák tizedes állandókra mutatnak be:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>`Tudományos jelöléssel írt szám. Az értékek belső `System.Double` legeltetése belsőleg tárolódik, és ugyanazt a tartományt/pontosságot követik. Az alábbi példák hozzávetőleges számállandókra mutatnak be példákat:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Megjegyzések  

A logikai állandókat az **IGAZ** vagy a **HAMIS**kulcsszavak jelölik. Az értékek a `System.Boolean`tárolódnak .  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Megjegyzések  

A karakterlánc-állandók aposztrófok közé vannak foglalva, és érvényes Unicode karaktereket tartalmaznak. A karakterlánc-állandóba ágyazott egyetlen idézőjel két aposztrófként jelenik meg.  
  
## <a name="function"></a>Funkció  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Megjegyzések
  
A `newid()` függvény a metódus által létrehozott `System.Guid.NewGuid()` **System.Guid** értéket adja vissza.  
  
A `property(name)` függvény a. által hivatkozott `name`tulajdonság értékét adja eredményül. Az `name` érték bármely érvényes kifejezés lehet, amely karakterláncértéket ad vissza.  
  
## <a name="considerations"></a>Megfontolandó szempontok
  
Vegye figyelembe a következő [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) szemantikát:  
  
-   A tulajdonságnevek nem különböznek a kis- és nagybetűktől.  
  
-   Az operátorok a C# implicit konverziós szemantikát követik, amikor csak lehetséges.  
  
-   A rendszertulajdonságok a [BrokeredMessage-példányokban](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) elérhető nyilvános tulajdonságok.  
  
    Vegye figyelembe `IS [NOT] NULL` a következő szemantikát:  
  
    -   `property IS NULL`kiértékelése `true` úgy történik, mintha a tulajdonság nem létezne, vagy a tulajdonság értéke a `null`.  
  
### <a name="property-evaluation-semantics"></a>Ingatlanértékelési szemantika  
  
- Egy nem létező rendszertulajdonság kiértékelésére tett kísérlet [filterexception](/dotnet/api/microsoft.servicebus.messaging.filterexception) kivételt eredményez.  
  
- A nem létező tulajdonságot a program **belsőleg ismeretlenként**értékeli ki.  
  
  Ismeretlen értékelés aritmetikai operátoroknál:  
  
- Bináris operátorok esetén, ha az operandusok bal és/vagy jobb oldala **ismeretlennek**minősül , akkor az eredmény **ismeretlen**.  
  
- Az unáris operátorok esetében, ha egy operandust **ismeretlenként**értékelnek, akkor az eredmény **ismeretlen**.  
  
  Ismeretlen kiértékelés bináris összehasonlító operátorokban:  
  
- Ha az operandusok bal és/vagy jobb oldala **ismeretlenként**van kiértékelve , akkor az eredmény **ismeretlen**.  
  
  Ismeretlen `[NOT] LIKE`értékelés:  
  
- Ha bármely operandusismeretlennek minősül , akkor az eredmény **ismeretlen**. **unknown**  
  
  Ismeretlen `[NOT] IN`értékelés:  
  
- Ha a bal oldali operandus **t ismeretlenként**értékeli ki , akkor az eredmény **ismeretlen**.  
  
  Ismeretlen értékelés az **ÉS** operátorban:  
  
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
  
 Ismeretlen értékelés a **vagy operátorban:**  
  
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
  
### <a name="operator-binding-semantics"></a>Operátorkötési szemantika
  
-   Az összehasonlító operátorok, `<` `<=`például `>` `>=` `=` , , , , `!=`, és ugyanazt a szemantikát követik, mint a C# operátorkötés az adattípus-promóciókban és az implicit konverziókban.  
  
-   A számtani operátorok, `+` `-`például , , `*`, , és `/` `%` ugyanazt a szemantikát követik, mint a C# operátor kötése az adattípus-promóciókban és az implicit konverziókban.

## <a name="next-steps"></a>További lépések

- [SQLFilter osztály (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [SQLFilter osztály (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [SQLRuleAction osztály](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
