---
title: SQLRuleAction szintaxis hivatkozás az Azure Service Busban
description: Ez a cikk az SQLRuleAction szintaxisra mutató hivatkozást tartalmaz. A műveletek SQL-nyelvű szintaxissal vannak megírva, amely et egy közvetített üzenettel szemben hajt végre.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 37615e39577ef60cccc9df91b61a6aa24ca794d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759628"
---
# <a name="sqlruleaction-syntax-reference-for-azure-service-bus"></a>SQLRuleAction szintaxis hivatkozás az Azure Service Bus-hoz

Az *SqlRuleAction* az [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) osztály egy példánya, és az SQL-language alapú szintaxissal írt műveletek készletét jelöli, amelyet egy [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)hajt végre.   
  
Ez a cikk az SQL-szabály műveletnyelvhelyességének részleteit sorolja fel.  
  
```  
<statements> ::=
    <statement> [, ...n]  
  
```  
  
```  
<statement> ::=
    <action> [;]
    Remarks
    -------
    Semicolon is optional.  
  
```  
  
```  
<action> ::=
    SET <property> = <expression>
    REMOVE <property>  
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
  
-   `<scope>`választható karakterlánc, amely a hatókörét `<property_name>`jelzi. Az érvényes `sys` `user`értékek vagy . Az `sys` érték azt a `<property_name>` rendszerhatókört jelzi, ahol a [BrokeredMessage osztály](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)köztulajdonneve. `user`jelzi a felhasználói `<property_name>` hatókört, ahol a [BrokeredMessage osztály](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) szótár ának kulcsa. `user`a hatókör az `<scope>` alapértelmezett hatókör, ha nincs megadva.  
  
### <a name="remarks"></a>Megjegyzések  

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
  
 Ez minden olyan karakterláncot jelent, amely betűvel kezdődik, és amelyet egy vagy több aláhúzás/betű/számjegy követ.  
  
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
  
     Az alábbi példák hosszú állandókra mutatnak be példákat:  
  
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
  
A logikai állandókat a `TRUE` `FALSE`vagy a kulcsszavak jelölik. Az értékek a `System.Boolean`tárolódnak .  
  
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

- A SET új tulajdonság létrehozására vagy egy meglévő tulajdonság értékének frissítésére szolgál.
- Az REMOVE tulajdonság eltávolítására szolgál.
- A SET implicit átalakítást hajt végre, ha lehetséges, ha a kifejezéstípus és a meglévő tulajdonságtípus eltérő.
- A művelet sikertelen, ha nem létező rendszertulajdonságokra hivatkozik.
- A művelet nem sikertelen, ha nem létező felhasználói tulajdonságokra hivatkozik.
- A nem létező felhasználói tulajdonság belsőleg "Ismeretlen" állapotúként lesz kiértékelve, az operátorok kiértékelésekor az [SQLFilter-rel](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) megegyező szemantikát követve.

## <a name="next-steps"></a>További lépések

- [SQLRuleAction osztály](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [SQLFilter osztály](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
