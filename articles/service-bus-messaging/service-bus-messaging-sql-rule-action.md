---
title: Az Azure-ban SQLRuleAction szintaxis referencia |} Microsoft Docs
description: SQLRuleAction nyelvtan adatait.
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
ms.openlocfilehash: 9ac9a2968adfdd8e1fb229ad744bc99914cdcd08
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="sqlruleaction-syntax"></a>SQLRuleAction szintaxis

A *SqlRuleAction* példánya a [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) osztály és jelöli azokat az SQL-nyelve műveletek alapján hajtja végre az szintakszist egy [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).   
  
Ez a cikk az SQL-szabály művelet szintaxis részleteit sorolja fel.  
  
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
  
-   `<scope>`egy nem kötelező karakterlánc, amely a hatóköre a `<property_name>`. Érvényes értékek a következők `sys` vagy `user`. A `sys` érték rendszerek ahol `<property_name>` egy nyilvános tulajdonság neve a [BrokeredMessage osztály](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user`jelzi a felhasználó hatókör ahol `<property_name>` kulcs a [BrokeredMessage osztály](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) szótárban. `user`hatókör esetén az alapértelmezett hatókör `<scope>` nincs megadva.  
  
### <a name="remarks"></a>Megjegyzések  

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
  
 Ez azt jelenti, hogy bármilyen karakterlánc, amely betűvel kezdődik, és egy vagy több aláhúzás/betűvel vagy számjeggyel követi.  
  
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
  
     A következő példák hosszú állandók:  
  
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
  
A kulcsszavak jelölik a logikai állandók `TRUE` vagy `FALSE`. Az értékek tárolt `System.Boolean`.  
  
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

- Hozzon létre egy új tulajdonságot, vagy frissítse az értéket egy meglévő tulajdonság használatos.
- REMOVE segítségével távolítsa el azt a tulajdonságot.
- SET hajtja implicit konverzió lehetőség szerint a kifejezés típusa és a meglévő tulajdonság típusa nem egyezik.
- A művelet sikertelen lesz, ha nem létező Rendszertulajdonságok hivatkozott.
- A művelet sikertelen, ha nem létező felhasználói tulajdonságok hivatkozott.
- Egy nem létező felhasználó tulajdonság ki lesz értékelve "Ismeretlen" belsőleg, az azonos szemantikákkal, a következő [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) operátorok kiértékelése során.

## <a name="next-steps"></a>További lépések

- [SQLRuleAction class](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [SQLFilter osztály](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
