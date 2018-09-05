---
title: Az Azure-ban SQLRuleAction szintaxis referenciája |} A Microsoft Docs
description: SQLRuleAction szintaxis kapcsolatos adatokat.
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
ms.openlocfilehash: 01833a51425f6a02c5ed781f4b10b22dc292f45d
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696397"
---
# <a name="sqlruleaction-syntax"></a>SQLRuleAction-szintaxis

A *SqlRuleAction* példánya a [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) osztály és jelöli azokat az SQL-nyelve műveletek alapján hajtja végre az szintaxist egy [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).   
  
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
  
-   `<scope>` egy nem kötelező karakterlánc, amely a hatóköre a `<property_name>`. Érvényes értékek a következők `sys` vagy `user`. A `sys` érték rendszerek, ahol `<property_name>` egy nyilvános tulajdonság neve a [BrokeredMessage osztály](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` azt jelzi, hogy a felhasználói hatókör ahol `<property_name>` kulcsa, a [BrokeredMessage osztály](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) szótárban. `user` hatókör esetén az alapértelmezett hatókör `<scope>` nincs megadva.  
  
### <a name="remarks"></a>Megjegyzések  

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
  
 Ez azt jelenti, hogy bármilyen karakterlánc, amely betűvel kezdődik, és a egy vagy több aláhúzás/levél/számjegy követ.  
  
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
  
A kulcsszavak képviseli logikai állandók `TRUE` vagy `FALSE`. Az értékek, tárolódnak `System.Boolean`.  
  
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

- Hozzon létre egy új tulajdonságot, vagy frissíteni egy meglévő tulajdonság értéke használatos.
- REMOVE segítségével távolítsa el a tulajdonságot.
- SET Ha lehetséges végez implicit konverzió, ha a kifejezés típusa és a meglévő tulajdonság típusát különböző.
- A művelet sikertelen lesz, ha nem létező Rendszertulajdonságok hivatkozott.
- A művelet sikertelen, ha nem létező felhasználói tulajdonságok hivatkozott.
- Egy nem létező felhasználói tulajdonságot abban az esetben "Ismeretlen" belsőleg, az azonos szemantikákkal, a következő [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) operátorok kiértékelése során.

## <a name="next-steps"></a>További lépések

- [SQLRuleAction osztályban](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [SQLFilter osztályban](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
