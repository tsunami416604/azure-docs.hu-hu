---
title: A SQLRuleAction szintaxisának referenciája Azure Service Bus
description: Ez a cikk a SQLRuleAction szintaxisát ismerteti. A műveletek olyan SQL-Language-alapú szintaxisban íródnak, amelyet egy felügyelt üzeneten végeztek el.
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
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759628"
---
# <a name="sqlruleaction-syntax-reference-for-azure-service-bus"></a>Azure Service Bus SQLRuleAction szintaxisának referenciája

A *SqlRuleAction* a [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) osztály egy példánya, amely az SQL-Language-alapú szintaxisban írt, az [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)-on végrehajtott műveletek készletét jelöli.   
  
Ez a cikk az SQL-szabály műveleti nyelvtanának részleteit sorolja fel.  
  
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
  
-   `<scope>` egy opcionális karakterlánc, amely a `<property_name>`hatókörét jelzi. Az érvényes értékek `sys` vagy `user`. A `sys` érték azt a rendszerhatókört jelöli, amelyben `<property_name>` a [BrokeredMessage osztály](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)nyilvános tulajdonságának neve. `user` azt a felhasználói hatókört jelöli, ahol a `<property_name>` a [BrokeredMessage osztály](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) -szótár kulcsa. `user` hatókör az alapértelmezett hatókör, ha nincs megadva `<scope>`.  
  
### <a name="remarks"></a>Megjegyzések  

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
  
 Ez olyan karakterláncot jelent, amely betűvel kezdődik, és egy vagy több aláhúzás/betű/számjegy követi.  
  
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
  
     A következő példák hosszú állandókat mutatnak be:  
  
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
  
A logikai konstansokat a kulcsszavak `TRUE` vagy `FALSE`jelölik. Az értékek `System.Boolean`ként vannak tárolva.  
  
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

- A SET egy új tulajdonság létrehozására szolgál, vagy egy meglévő tulajdonság értékét frissíti.
- Az Eltávolítás a tulajdonságok eltávolítására szolgál.
- Ha lehetséges, akkor a SET implicit konverziót hajt végre, ha a kifejezés típusa és a meglévő tulajdonság típusa eltérő.
- A művelet meghiúsul, ha nem létező Rendszertulajdonságok lettek hivatkozva.
- A művelet nem sikerül, ha a nem létező felhasználói tulajdonságok hivatkoztak.
- A nem létező felhasználói tulajdonságokat belsőleg "ismeretlen" értékként értékeli ki a rendszer, és a [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) a kezelők kiértékelése során megjelenő szemantikat követve.

## <a name="next-steps"></a>Következő lépések

- [SQLRuleAction osztály](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [SQLFilter osztály](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
