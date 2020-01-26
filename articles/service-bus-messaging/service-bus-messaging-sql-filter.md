---
title: Azure Service Bus SQLFilter szintaxisának leírása | Microsoft Docs
description: Ez a cikk a SQLFilter nyelvtani adatait ismerteti. A SqlFilter az SQL-92 szabvány egy részhalmazát támogatja.
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
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759663"
---
# <a name="sqlfilter-syntax"></a>SQLFilter-szintaxis

A *SqlFilter* objektum a [SqlFilter osztály](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)egy példánya, és egy SQL Language-alapú szűrési kifejezést jelöl, amelyet a rendszer egy [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)kiértékel. A SqlFilter az SQL-92 szabvány egy részhalmazát támogatja.  
  
 Ez a témakör a SqlFilter nyelvtanának részleteit sorolja fel.  
  
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
  
-   `<scope>` egy opcionális karakterlánc, amely a `<property_name>`hatókörét jelzi. Az érvényes értékek `sys` vagy `user`. A `sys` érték azt a rendszerhatókört jelöli, amelyben `<property_name>` a [BrokeredMessage osztály](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)nyilvános tulajdonságának neve. `user` azt a felhasználói hatókört jelöli, ahol a `<property_name>` a [BrokeredMessage osztály](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) -szótár kulcsa. `user` hatókör az alapértelmezett hatókör, ha nincs megadva `<scope>`.  
  
## <a name="remarks"></a>Megjegyzések

Egy nem létező rendszertulajdonság elérésére tett kísérlet hibát jelez, míg egy nem létező felhasználói tulajdonság elérésére tett kísérlet nem hiba. Ehelyett egy nem létező felhasználói tulajdonságot belsőleg kiértékel a rendszer ismeretlen értékként. Az operátorok kiértékelése során az ismeretlen értéket külön kezeli a rendszer.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumentumok  

 `<regular_identifier>` a következő reguláris kifejezés által jelölt sztring:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
Ez a nyelvtan olyan karakterláncot jelent, amely betűvel kezdődik, és egy vagy több aláhúzás/levél/számjegy követi.  
  
`[:IsLetter:]` a Unicode-levélként kategorizált Unicode-karaktereket. a `System.Char.IsLetter(c)` `true`t ad vissza, ha a `c` egy Unicode betű.  
  
`[:IsDigit:]` minden olyan Unicode-karaktert jelent, amely decimális számjegyként van kategorizálva. a `System.Char.IsDigit(c)` `true`t ad vissza, ha a `c` Unicode számjegy.  
  
Egy `<regular_identifier>` nem lehet foglalt kulcsszó.  
  
`<delimited_identifier>` a bal/jobb oldali szögletes zárójelek ([]) közé zárt karakterlánc. A jobb oldali szögletes zárójel két jobb oldali szögletes zárójelként jelenik meg. A következő példák a `<delimited_identifier>`ra mutatnak:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` bármely olyan karakterlánc, amely dupla idézőjelekkel van ellátva. Az azonosító dupla idézőjele két idézőjel. Az idézőjelek használata nem ajánlott, mert könnyen összetéveszthető karakterlánc-konstanssal. Ha lehetséges, használjon tagolt azonosítót. A következő példa a `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>minta  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Megjegyzések
  
a `<pattern>`nak karakterláncként kiértékelt kifejezésnek kell lennie. A rendszer mintaként használja a hasonló operátorhoz.      A következő helyettesítő karaktereket tartalmazhatja:  
  
-   `%`: nulla vagy több karakterből álló karakterlánc.  
  
-   `_`: egyetlen karakter.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Megjegyzések  

a `<escape_char>`nak olyan kifejezésnek kell lennie, amely 1. hosszúságú sztringként van kiértékelve. A hasonló operátorhoz tartozó Escape-karakterként használható.  
  
 A `property LIKE 'ABC\%' ESCAPE '\'` például a `ABC`karakterrel kezdődő karakterlánc helyett `ABC%`.  
  
## <a name="constant"></a>állandó  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumentumok  
  
-   `<integer_constant>` egy olyan számsorozat, amely nem idézőjelek közé esik, és nem tartalmaz tizedesvesszőt. Az értékeket `System.Int64` belsőleg tárolja a rendszer, és ugyanazt a tartományt követi.  
  
     Ilyenek például a hosszú állandók:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` egy olyan számsorozat, amely nem idézőjelek közé esik, és tizedesvesszőt tartalmaz. Az értékek a `System.Double` belsőleg tárolódnak, és ugyanazt a tartományt/pontosságot követik.  
  
     Egy későbbi verzióban ezt a számot egy másik adattípusban tárolhatja a pontos számú szemantika támogatásához, ezért nem szabad az a tény, hogy az alapul szolgáló adattípus `System.Double` a `<decimal_constant>`.  
  
     A következő példák decimális konstansokra mutatnak:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` egy tudományos jelöléssel írt szám. Az értékek a `System.Double` belsőleg tárolódnak, és ugyanazt a tartományt/pontosságot követik. Az alábbi példákban megközelítheti a szám konstansait:  
  
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

A logikai konstansokat a **true** vagy a **false**kulcsszó jelöli. Az értékek `System.Boolean`ként vannak tárolva.  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Megjegyzések  

A karakterlánc-konstansok szimpla idézőjelek közé vannak lefoglalva, és tartalmaznak bármilyen érvényes Unicode-karaktert. Egy karakterlánc-konstansba ágyazott idézőjelek két szimpla idézőjelet jelölnek.  
  
## <a name="function"></a>függvény  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Megjegyzések
  
A `newid()` függvény a `System.Guid.NewGuid()` metódus által generált **System. GUID azonosítót** adja vissza.  
  
A `property(name)` függvény a `name`által hivatkozott tulajdonság értékét adja vissza. A `name` érték bármely érvényes kifejezés lehet, amely egy karakterlánc-értéket ad vissza.  
  
## <a name="considerations"></a>Megfontolandó szempontok
  
Vegye figyelembe a következő [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) szemantikai műveleteket:  
  
-   A tulajdonságok nevei kis-és nagybetűk megkülönböztetése nélkül.  
  
-   Ha lehetséges C# , az operátorok implicit átalakítási szemantikaot követnek.  
  
-   A rendszer tulajdonságai a [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) -példányokban elérhető nyilvános tulajdonságok.  
  
    Vegye figyelembe az alábbi `IS [NOT] NULL` szemantikai műveleteket:  
  
    -   `property IS NULL` kiértékelése `true`, ha a tulajdonság nem létezik, vagy a tulajdonság értéke `null`.  
  
### <a name="property-evaluation-semantics"></a>Tulajdonság-értékelési szemantika  
  
- Egy nem létező rendszertulajdonság kiértékelésére tett kísérlet egy [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) kivételt jelez.  
  
- A nem létező tulajdonságok belső kiértékelése **ismeretlenként**történik.  
  
  Ismeretlen kiértékelés az aritmetikai operátorokban:  
  
- A bináris operátorok esetében, ha az operandusok bal és/vagy jobb oldalán az **ismeretlen**érték van kiértékelve, akkor az eredmény **ismeretlen**.  
  
- Egyoperandusú operátorok esetén, ha egy operandust **ismeretlenként**értékelnek ki, akkor az eredmény **ismeretlen**.  
  
  Ismeretlen kiértékelés a bináris összehasonlító operátorok esetében:  
  
- Ha az operandusok bal és/vagy jobb oldalán az **ismeretlen**érték van kiértékelve, akkor az eredmény **ismeretlen**.  
  
  Ismeretlen kiértékelés a `[NOT] LIKE`ban:  
  
- Ha bármely operandus **ismeretlenként**van kiértékelve, akkor az eredmény **ismeretlen**.  
  
  Ismeretlen kiértékelés a `[NOT] IN`ban:  
  
- Ha a bal oldali operandus értéke **ismeretlen**, akkor az eredmény **ismeretlen**.  
  
  Ismeretlen kiértékelés a **és** operátorban:  
  
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
  
 Ismeretlen kiértékelés **vagy** operátor:  
  
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
  
### <a name="operator-binding-semantics"></a>Operátor kötési szemantika
  
-   Az összehasonlító operátorok, például a `>`, a `>=`, a `<`, a `<=`, a `!=`és a `=` C# ugyanazokat a szemantikai műveleteket hajtják végre, mint az adattípusok és az implicit konverziók.  
  
-   Az aritmetikai operátorok, például a `+`, a `-`, a `*`, a `/`és a `%` C# ugyanazokat a szemantikai műveleteket hajtják végre, mint az operátor kötése az adattípusok és az implicit konverziók esetében.

## <a name="next-steps"></a>Következő lépések

- [SQLFilter osztály (.NET-keretrendszer)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [SQLFilter osztály (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [SQLRuleAction osztály](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
