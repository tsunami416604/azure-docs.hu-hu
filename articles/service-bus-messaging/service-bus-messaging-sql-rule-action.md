---
title: A SQLRuleAction szintaxisának referenciája Azure Service Bus
description: Ez a cikk a SQLRuleAction szintaxisát ismerteti. A műveletek olyan SQL-Language-alapú szintaxisban íródnak, amelyet egy felügyelt üzeneten végeztek el.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 61fa6e046b4d4a0ba91bf8608c846755026d07ec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341578"
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
  
-   `<scope>`egy opcionális karakterlánc, amely a hatókörét jelzi `<property_name>` . Az érvényes értékek a következők: `sys` vagy `user` . Az `sys` érték azt a rendszerhatókört jelöli, ahol a a `<property_name>` [BrokeredMessage osztály](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)egy nyilvános tulajdonságának neve. `user`Megadja a felhasználói hatókört, ahol a a `<property_name>` [BrokeredMessage osztály](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) szótárának kulcsa. `user`a hatókör az alapértelmezett hatókör, ha nincs `<scope>` megadva.  
  
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
 `<regular_identifier>`a következő reguláris kifejezéssel jelölt karakterlánc:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 Ez olyan karakterláncot jelent, amely betűvel kezdődik, és egy vagy több aláhúzás/betű/számjegy követi.  
  
 `[:IsLetter:]`olyan Unicode-karakter, amely Unicode betűként van kategorizálva. `System.Char.IsLetter(c)`a értéket adja vissza `true` `c` , ha az egy Unicode betű.  
  
 `[:IsDigit:]`olyan Unicode-karakter, amely decimális számjegyként van kategorizálva. `System.Char.IsDigit(c)`a értéket adja vissza `true` `c` , ha az egy Unicode számjegy.  
  
 A `<regular_identifier>` nem lehet foglalt kulcsszó.  
  
 `<delimited_identifier>`a bal/jobb oldali szögletes zárójelek ([]) közé zárt karakterlánc. A jobb oldali szögletes zárójel két jobb oldali szögletes zárójelként jelenik meg. Az alábbi példák a következőkre mutatnak `<delimited_identifier>` :  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>`minden olyan karakterlánc, amely dupla idézőjelekkel van ellátva. Az azonosító dupla idézőjele két idézőjel. Az idézőjelek használata nem ajánlott, mert könnyen összetéveszthető karakterlánc-konstanssal. Ha lehetséges, használjon tagolt azonosítót. Az alábbi példa a következőket szemlélteti `<quoted_identifier>` :  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>minta  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Megjegyzések
  
 `<pattern>`karakterláncként kiértékelt kifejezésnek kell lennie. A rendszer mintaként használja a hasonló operátorhoz.      A következő helyettesítő karaktereket tartalmazhatja:  
  
-   `%`: Nulla vagy több karakterből álló karakterlánc.  
  
-   `_`: Egyetlen karakter.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Megjegyzések
  
 `<escape_char>`egy olyan kifejezésnek kell lennie, amely 1. hosszúságú sztringként van kiértékelve. A hasonló operátorhoz tartozó Escape-karakterként használható.  
  
 Például a `property LIKE 'ABC\%' ESCAPE '\'` egyezés `ABC%` helyett a karakterrel kezdődő sztring szerepel `ABC` .  
  
## <a name="constant"></a>állandó  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumentumok  
  
-   `<integer_constant>`egy olyan számsorozat, amely nem idézőjelek közé esik, és nem tartalmaz decimális pontokat. Az értékeket belsőleg tárolja a rendszer `System.Int64` , és ugyanazt a tartományt követi.  
  
     A következő példák hosszú állandókat mutatnak be:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>`egy olyan számokból álló karakterlánc, amely nem idézőjelek közé esik, és tizedes pontot tartalmaz. Az értékeket belsőleg tárolja a rendszer `System.Double` , és kövesse ugyanazt a tartományt/pontosságot.  
  
     Egy későbbi verzióban ezt a számot egy másik adattípusban tárolhatja a pontos számú szemantika támogatásához, ezért nem szabad az alapul szolgáló adattípusra támaszkodni `System.Double` `<decimal_constant>` .  
  
     A következő példák decimális konstansokra mutatnak:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>`egy tudományos jelöléssel írt szám. Az értékeket belsőleg tárolja a rendszer `System.Double` , és kövesse ugyanazt a tartományt/pontosságot. Az alábbi példákban megközelítheti a szám konstansait:  
  
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
  
A logikai konstansok a kulcsszavak vagy a alapján jelennek meg `TRUE` `FALSE` . Az értékek a következőképpen tárolódnak: `System.Boolean` .  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Megjegyzések
  
A karakterlánc-konstansok szimpla idézőjelek közé vannak lefoglalva, és tartalmaznak bármilyen érvényes Unicode-karaktert. Egy karakterlánc-konstansba ágyazott idézőjelek két szimpla idézőjelet jelölnek.  
  
## <a name="function"></a>A  függvény  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Megjegyzések  

A `newid()` függvény a metódus által generált **System. GUID azonosítót** adja vissza `System.Guid.NewGuid()` .  
  
A `property(name)` függvény a által hivatkozott tulajdonság értékét adja vissza `name` . Az `name` érték bármely érvényes kifejezés lehet, amely egy karakterlánc-értéket ad vissza.  
  
## <a name="considerations"></a>Megfontolandó szempontok

- A SET egy új tulajdonság létrehozására szolgál, vagy egy meglévő tulajdonság értékét frissíti.
- Az Eltávolítás a tulajdonságok eltávolítására szolgál.
- Ha lehetséges, akkor a SET implicit konverziót hajt végre, ha a kifejezés típusa és a meglévő tulajdonság típusa eltérő.
- A művelet meghiúsul, ha nem létező Rendszertulajdonságok lettek hivatkozva.
- A művelet nem sikerül, ha a nem létező felhasználói tulajdonságok hivatkoztak.
- A nem létező felhasználói tulajdonságokat belsőleg "ismeretlen" értékként értékeli ki a rendszer, és a [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) a kezelők kiértékelése során megjelenő szemantikat követve.

## <a name="next-steps"></a>További lépések

- [SQLRuleAction osztály](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [SQLFilter osztály](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
