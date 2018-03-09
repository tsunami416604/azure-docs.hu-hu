---
title: "Munkafolyamat definícióját nyelvi séma - Azure Logic Apps |} Microsoft Docs"
description: "Az Azure Logic Apps a munkafolyamat-definíció séma alapján munkafolyamatokat meghatározása"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 26c94308-aa0d-4730-97b6-de848bffff91
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/21/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 994b8946078ed9b4c8aa965a3bc0e117ba3185c0
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="workflow-definition-language-schema-for-azure-logic-apps"></a>Az Azure Logic Apps munkafolyamat Definition Language séma

Egy munkafolyamat-definíciót tartalmaz a tényleges logika, amely végrehajtja a Logic Apps alkalmazást részeként. Ez a definíció tartalmaz egy vagy több eseményindítókat, amelyek indítsa el a logikai alkalmazást, illetve egy vagy több műveleteket az a logikai alkalmazás érvénybe.  
  
## <a name="basic-workflow-definition-structure"></a>A munkafolyamat alapvető szerkezete

Íme egy munkafolyamat-definíció felépítése:  
  
```json
{
    "$schema": "<schema-of the-definition>",
    "contentVersion": "<version-number-of-definition>",
    "parameters": { <parameter-definitions-of-definition> },
    "triggers": [ { <definition-of-flow-triggers> } ],
    "actions": [ { <definition-of-flow-actions> } ],
    "outputs": { <output-of-definition> }
}
```
  
> [!NOTE]
> A [munkafolyamat felügyeleti REST API](https://docs.microsoft.com/rest/api/logic/workflows) dokumentáció létrehozása és kezelése a logic app munkafolyamatok szóló információt tartalmaz.
  
|Elem neve|Szükséges|Leírás|  
|------------------|--------------|-----------------|  
|$schema|Nem|A JSON-fájl, amely az adatdefiníciós nyelv verziója a helyét adja meg. Ezen a helyen, ha a definíció külsőleg hivatkozik. Itt az a hely, a dokumentum: <p>`https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json`|  
|contentVersion|Nem|Megadja a definíció verzióját. Amikor telepít egy munkafolyamatot, a definíció használatával, ezt az értéket segítségével győződjön meg arról, hogy a megfelelő meghatározása szolgál.|  
|paraméterek|Nem|Megadja a definíció adatok bemeneteként használt paraméterek. Legfeljebb 50 paraméterekkel definiálható.|  
|Eseményindítók|Nem|Az eseményindítók a munkafolyamat kezdeményező adatait adja meg. Legfeljebb 10 eseményindítók meghatározása.|  
|műveletek|Nem|Itt adhatja meg, a folyamat végrehajtja elvégzett műveletekről. Legfeljebb 250 műveletek definiálhatók.|  
|kimenetek|Nem|Az üzembe helyezett erőforrás adatainak megadása Legfeljebb 10 kimenetek definiálhatók.|  
  
## <a name="parameters"></a>Paraméterek

Ebben a szakaszban adja meg a munkafolyamat-definíciót a központi telepítéskor használt paraméterek. Minden paraméter deklarálni kell az ebben a szakaszban a többi szakasza pedig a definíció használható.  
  
A következő példa egy paraméterdefiníció szerkezetét mutatja:  

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": <default-value-of-parameter>,
        "allowedValues": [ <array-of-allowed-values> ],
        "metadata" : { "key": { "name": "value"} }
    }
}
```

|Elem neve|Szükséges|Leírás|  
|------------------|--------------|-----------------|  
|type|Igen|**Típus**: karakterlánc <p> **Deklaráció**: `"parameters": {"parameter1": {"type": "string"}` <p> **Specifikáció**: `"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **Típus**: securestring <p> **Deklaráció**: `"parameters": {"parameter1": {"type": "securestring"}}` <p> **Specifikáció**: `"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **Típus**: int <p> **Deklaráció**: `"parameters": {"parameter1": {"type": "int"}}` <p> **Specifikáció**: `"parameters": {"parameter1": {"value" : 5}}` <p> **Típus**: logikai <p> **Deklaráció**: `"parameters": {"parameter1": {"type": "bool"}}` <p> **Specifikáció**: `"parameters": {"parameter1": { "value": true }}` <p> **Típus**: tömb <p> **Deklaráció**: `"parameters": {"parameter1": {"type": "array"}}` <p> **Specifikáció**: `"parameters": {"parameter1": { "value": [ array-of-values ]}}` <p> **Típus**: objektum <p> **Deklaráció**: `"parameters": {"parameter1": {"type": "object"}}` <p> **Specifikáció**: `"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **Típus**: secureobject <p> **Deklaráció**: `"parameters": {"parameter1": {"type": "object"}}` <p> **Specifikáció**: `"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **Megjegyzés:** a `securestring` és `secureobject` típusok nem lehet megjeleníteni a `GET` műveletek. Jelszavak, kulcsok és titkos kulcsok ehhez a típushoz kell használnia.|  
|defaultValue|Nem|Adja meg a paraméter alapértelmezett értéke, ha nincs érték megadva az erőforrás létrehozása idején.|  
|allowedValues|Nem|A paraméter megengedett értékei tömbjét adja meg.|  
|Metaadatok|Nem|Adja meg a paraméter, például a olvasható leírás vagy a Visual Studio vagy egyéb eszközök által használt tervezési idejű adatok további információt.|  
  
Ez a példa bemutatja, hogyan használhatja a paraméter a szervezet szakaszban egy művelet:  
  
```json
"body" :
{
  "property1": "@parameters('parameter1')"
}
```

 Paraméterek kimenetek is használható.  
  
## <a name="triggers-and-actions"></a>Triggerek és műveletek  

Eseményindítók és műveletek megadása a részvételre alkalmas hívások munkafolyamat végrehajtása. Ez a szakasz kapcsolatos részletekért lásd: [munkafolyamat-műveleteket és eseményindítók](logic-apps-workflow-actions-triggers.md).
  
## <a name="outputs"></a>Kimenetek  

Kimenetek információt visszaadható egy munkafolyamat futtatásához adja meg. Például ha egy adott állapot vagy minden egyes futtatásához nyomon követni kívánt értéket, a futtatási kimenetek telepíthet is adatokat. Az adatok a felügyeleti REST API-t a rendszert futtató, és a felügyeleti felhasználói Felületét a rendszert futtató, Azure-portálon jelenik meg. Ezek a más külső rendszerekkel, például a Power bi kimenetek is áramolhasson az irányítópultok létrehozásához. Kimenetek vannak *nem* válaszolni a bejövő kérelmeket a szolgáltatás REST API használatával. Egy bejövő kérelem használatával válaszolni a `response` művelet típusa, például:
  
```json
"outputs": {  
  "key1": {  
    "value": "value1",  
    "type" : "<type-of-value>"  
  }  
} 
```

|Elem neve|Szükséges|Leírás|  
|------------------|--------------|-----------------|  
|key1|Igen|Meghatározza a kimeneti kulcs azonosítóját. Cserélje le **key1** a kimeneti azonosításához használni kívánt névvel.|  
|érték|Igen|A kimeneti értékét adja meg.|  
|type|Igen|A megadott érték típusát határozza meg. Lehetséges típusú értékek a következők: <ul><li>`string`</li><li>`securestring`</li><li>`int`</li><li>`bool`</li><li>`array`</li><li>`object`</li></ul>|
  
## <a name="expressions"></a>Kifejezések  

Lehet, hogy a JSON-értékek meghatározásában szövegkonstans, vagy a definíció használatakor kiértékelt kifejezés el. Példa:  
  
```json
"name": "value"
```

 vagy  
  
```json
"name": "@parameters('password') "
```

> [!NOTE]
> Néhány kifejezés, amely nem létezik a végrehajtási elején futásidejű műveletek értékeik beolvasása sikertelen. Használhat **funkciók** az alábbi értékek némelyikét beolvasása érdekében.  
  
Kifejezések bárhol megjelenhet egy JSON karakterláncértéket, és egy másik JSON-érték mindig eredményez. Amikor egy JSON-érték meghatározása egy kifejezést kell a szervezet a kifejezés ki kell olvasni, a-kukac eltávolításával (@). Egy szöveges karakterlánc van szüksége a kezdetű @, hogy karakterlánc használatával kell megjelölni@. Az alábbi példák bemutatják, hogyan kifejezések kiértékelése.  
  
|JSON-érték|Eredmény|  
|----------------|------------|  
|"paraméterek"|A "parameters" karaktert adja vissza.|  
|"[1] Paraméterek"|A "parameters [1]" karaktert adja vissza.|  
|"@@"|Egy 1 tartalmazó karakterlánc ' @' adja vissza.|  
|" @"|Egy 2 tartalmazó karakterlánc ' @' adja vissza.|  
  
A *köztes karakterlánc*, akkor is megjelenhet, kifejezések adott kifejezések csomagolni vannak karakterláncok belül `@{ ... }`. Példa: <p>`"name" : "First Name: @{parameters('firstName')} Last Name: @{parameters('lastName')}"`

Az eredménye mindig egy karakterláncot, amely hasonló Ez a funkció lehetővé teszi a `concat` függvény. Tegyük fel, hogy az Ön által definiált `myNumber` , `42` és `myString` , `sampleString`:  
  
|JSON-érték|Eredmény|  
|----------------|------------|  
|"@parameters('myString')"|Beolvasása `sampleString` karakterláncként.|  
|"@{parameters('myString')}"|Beolvasása `sampleString` karakterláncként.|  
|"@parameters("SajatSzam")"|Beolvasása `42` , egy *szám*.|  
|"@{parameters('myNumber')}"|Beolvasása `42` , egy *karakterlánc*.|  
|"Válasz: @{parameters('myNumber')}"|A karakterláncot ad vissza, `Answer is: 42`.|  
|"@concat(" Válasz: ", string(parameters('myNumber')))"|A karakterláncot ad vissza `Answer is: 42`|  
|"Válasz: @@ {parameters('myNumber')}"|A karakterláncot ad vissza, `Answer is: @{parameters('myNumber')}`.|  
  
## <a name="operators"></a>Operátorok  

Operátorok a következők: a kifejezések vagy funkciók belül használható karaktereket. 
  
|Operátor|Leírás|  
|--------------|-----------------|  
|.|A pont operátor lehetővé teszi a való hivatkozáshoz egy objektum tulajdonságai|  
|?|A kérdőjel operátor null hivatkozhat egy objektum futásidejű hiba nélkül teszi lehetővé. Ebben a kifejezésben használható például null eseményindító kimenetek kezeléséhez: <p>`@coalesce(trigger().outputs?.body?.property1, 'my default value')`|  
|'|A szimpla idézőjel való szövegkonstanssal burkolása az egyetlen lehetőség. Kifejezések belül idézőjelek nem használható, mert ez az absztrakt ütközik a JSON ajánlat, amely a teljes kifejezést burkolja.|  
|[]|A szögletes zárójelek segítségével kapja az értékét egy adott indexű tömbben. Például, ha egy műveletet, amelyet továbbküld `range(0,10)`a számára a `forEach` függvény, ez a funkció segítségével beolvasásának tömbök kívül:  <p>`myArray[item()]`|  
  
## <a name="functions"></a>Functions  

Ön is meghívhatja kifejezések függvényeket. A következő táblázat a funkciók használható kifejezésben.  
  
|Kifejezés|Próbaverzió|  
|----------------|----------------|  
|"@function('Hello')"|Meghívja a literál karakterlánc Hello első paraméterként a definíció függvény tagja legyen.|  
|"@function(" Az "s ritkán!") "|Meghívja a definíció karaktert függvény tagja "ritkán!" az első paraméterként|  
|"@function().prop1"|Tul1 tulajdonságának értékét adja vissza a `myfunction` a definíció tagja.|  
|"@function('Hello').prop1"|"Hello" karaktert definition függvény tagja meghívja az első paraméterként, és a tul1 tulajdonság az objektum beolvasása.|  
|"@function(parameters('Hello'))"|Kiértékeli a Hello paraméter és érték átadása függvény|  
  
### <a name="referencing-functions"></a>Hivatkozási funkciók  

Ezek a funkciók segítségével kimenetének létrehozása a logikai alkalmazás vagy a logikai alkalmazás létrehozása az átadott értékek lévő egyéb műveletek használatával hivatkozik. Az adatok például hivatkozhat egy másik használandó egy lépésben.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|paraméterek|A paraméter értékét a definícióban megadott adja vissza. <p>`parameters('password')` <p> **Számú paraméter**: 1 <p> **Név**: paraméter <p> **Leírás**: kötelező. A paraméter-értékekhez neve.|  
|művelet|Lehetővé teszi, hogy annak értéke származik más JSON-név és érték párok vagy az aktuális futásidejű művelet eredményének kifejezés. A tulajdonság a következő példa a propertyPath által képviselt nem kötelező megadni. A propertyPath nincs megadva, a hivatkozás akkor teljes művelet objektumhoz. Ez a funkció csak akkor használható belül tegye-csak egy művelet feltételeket. <p>`action().outputs.body.propertyPath`|  
|műveletek|Lehetővé teszi, hogy annak értéke származik más JSON-név és érték párok vagy a futásidejű művelet eredményének kifejezés. Ezek a kifejezések explicit módon deklarálja, hogy egy-egy művelettel függ-e egy másik művelet. A tulajdonság a következő példa a propertyPath által képviselt nem kötelező megadni. A propertyPath nincs megadva, a hivatkozás akkor teljes művelet objektumhoz. Függőségeket használhatja ezt az elemet vagy a feltételek elem, de nem szeretné használni, mind az azonos függő erőforráshoz. <p>`actions('myAction').outputs.body.propertyPath` <p> **Számú paraméter**: 1 <p> **Név**: a művelet neve <p> **Leírás**: kötelező. A művelet, amelynek az értékét a neve. <p> A műveleti objektum a rendelkezésre álló tulajdonságok a következők: <ul><li>`name`</li><li>`startTime`</li><li>`endTime`</li><li>`inputs`</li><li>`outputs`</li><li>`status`</li><li>`code`</li><li>`trackingId`</li><li>`clientTrackingId`</li></ul> <p>Tekintse meg a [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=850646) meg azokat a tulajdonságokat.|
|trigger|Lehetővé teszi, hogy a kifejezés értéke származik más JSON-név és érték párok vagy a futásidejű eseményindító kimenetét. A tulajdonság a következő példa a propertyPath által képviselt nem kötelező megadni. A propertyPath nincs megadva, a hivatkozás akkor teljes eseményindító objektumhoz. <p>`trigger().outputs.body.propertyPath` <p>A trigger ráfordítások használni, ha a függvény a kimenetek, hogy az előző adja vissza. Azonban, ha egy eseményindító feltételében használni a `trigger` függvény az aktuális végrehajtási kimenetének adja vissza. <p> Az eseményindító objektum rendelkezésre álló tulajdonságok a következők: <ul><li>`name`</li><li>`scheduledTime`</li><li>`startTime`</li><li>`endTime`</li><li>`inputs`</li><li>`outputs`</li><li>`status`</li><li>`code`</li><li>`trackingId`</li><li>`clientTrackingId`</li></ul> <p>Tekintse meg a [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=850644) meg azokat a tulajdonságokat.|
|actionOutputs|Ez a funkció csak a rövid szintaxist `actions('actionName').outputs` <p> **Számú paraméter**: 1 <p> **Név**: a művelet neve <p> **Leírás**: kötelező. A művelet, amelynek az értékét a neve.|  
|actionBody és törzsét.|Ezek a funkciók a rövid szintaxist `actions('actionName').outputs.body` <p> **Számú paraméter**: 1 <p> **Név**: a művelet neve <p> **Leírás**: kötelező. A művelet, amelynek az értékét a neve.|  
|triggerOutputs|Ez a funkció csak a rövid szintaxist `trigger().outputs`|  
|triggerBody|Ez a funkció csak a rövid szintaxist `trigger().outputs.body`|  
|Elem|Ismétlődő művelet használni, amikor ez a funkció, amely a művelet a közelítés a tömbben elemét adja vissza. Például ha egy futtató művelet egyes elemekhez tartozó üzenetek tömbje, használhatja ezt a szintaxist: <p>`"input1" : "@item().subject"`| 
  
### <a name="collection-functions"></a>Gyűjtemény-funkciók  

Ezek a funkciók hatnak a gyűjtemények, és általában tömbök, karakterláncok és egyes esetekben szótárak vonatkozik.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|tartalmazza a következőt:|Igaz értéket ad eredményül, ha a szótár tartalmaz-e, kulcslista értéket tartalmaz, vagy karakterlánc karakterláncrészletet tartalmazza. Például, a függvény `true`: <p>`contains('abacaba','aca')` <p> **Számú paraméter**: 1 <p> **Név**: gyűjteményen belül <p> **Leírás**: kötelező. A gyűjteményt, amelyben keresni. <p> **Számú paraméter**: 2. régiója <p> **Név**: objektum keresése <p> **Leírás**: kötelező. Az objektum belül található a **gyűjteményben**.|  
|Hossza|A tömb vagy karakterlánc elemek számát adja vissza. Például, a függvény `3`:  <p>`length('abc')` <p> **Számú paraméter**: 1 <p> **Név**: gyűjtemény <p> **Leírás**: kötelező. A gyűjtemény, amelynek hossza eléréséhez.|  
|üres|Igaz értéket ad eredményül, ha az objektum, a tömb vagy karakterlánc üres. Például, a függvény `true`: <p>`empty('')` <p> **Számú paraméter**: 1 <p> **Név**: gyűjtemény <p> **Leírás**: kötelező. Annak ellenőrzése, hogy az üres gyűjtemény.|  
|metszetének|Visszaadja egy egyetlen tömb vagy objektum, amely a tömb, vagy az átadott objektumok közötti közös elemmel rendelkezik. Például, a függvény `[1, 2]`: <p>`intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])` <p>A függvény paramétereinek lehet egy objektum vagy egy lemezkészlet tömbök (nem keverékével is). Ha két objektum ugyanazzal a névvel, a végső objektum megjelenik az utolsó objektum ezzel a névvel. <p> **Számú paraméter**: 1... *n* <p> **Név**: gyűjtemény *n* <p> **Leírás**: kötelező. A gyűjtemények kiértékelése. Az objektum összes gyűjtemény megjelenjen az eredményben továbbított kell lennie.|  
|a UNION|Egy egyetlen tömb vagy objektum tömb vagy objektum kerülnek átadásra a függvény minden elemét adja vissza. Például, a függvény `[1, 2, 3, 10, 101]`: <p>`union([1, 2, 3], [101, 2, 1, 10])` <p>A paraméterek, a függvény egy objektum vagy egy lemezkészlet tömbök (nem ezek keveréke) lehet. Ha ezzel a névvel, a végső kimenetet a két objektum, a végső objektum az utolsó objektum ezen a néven jelenik meg. <p> **Számú paraméter**: 1... *n* <p> **Név**: gyűjtemény *n* <p> **Leírás**: kötelező. A gyűjtemények kiértékelése. Olyan objektum, amely akkor jelenik meg, a gyűjtemények bármelyikében is megjelenik az eredményben.|  
|első|Az első elem a tömb vagy az átadott karakterlánc visszaadása. Például, a függvény `0`: <p>`first([0,2,3])` <p> **Számú paraméter**: 1 <p> **Név**: gyűjtemény <p> **Leírás**: kötelező. A gyűjteményt, amelyben az első objektumot igénybe vehet.|  
|utolsó|Az utolsó elem a tömb vagy az átadott karakterlánc visszaadása. Például, a függvény `3`: <p>`last('0123')` <p> **Számú paraméter**: 1 <p> **Név**: gyűjtemény <p> **Leírás**: kötelező. A gyűjtemény utolsó dimenzióobjektumot érvénybe.|  
|hajtsa végre a megfelelő|Visszaadja az első **száma** az átadott tömb vagy karakterlánc elemeit. Például, a függvény `[1, 2]`:  <p>`take([1, 2, 3, 4], 2)` <p> **Számú paraméter**: 1 <p> **Név**: gyűjtemény <p> **Leírás**: kötelező. A gyűjtemény első érvénybe honnan **száma** objektumok. <p> **Számú paraméter**: 2. régiója <p> **Név**: száma <p> **Leírás**: kötelező. A történő objektumok száma a **gyűjtemény**. Pozitív egész számnak kell lennie.|  
|Kihagyása|A elemeket adja vissza, a tömb indextől kezdődő **száma**. Például, a függvény `[3, 4]`: <p>`skip([1, 2 ,3 ,4], 2)` <p> **Számú paraméter**: 1 <p> **Név**: gyűjtemény <p> **Leírás**: kötelező. Kihagyja az első gyűjtemény **száma** a következő helyről objektumokat. <p> **Számú paraméter**: 2. régiója <p> **Név**: száma <p> **Leírás**: kötelező. Előre hozása távolítsa el az objektumok száma **gyűjtemény**. Pozitív egész számnak kell lennie.|  
|csatlakozás|Az egyes elemek tömb tartományhoz elválasztó, például ez visszaadja a karakterláncot ad vissza, `"1,2,3,4"`:<br /><br /> `join([1, 2, 3, 4], ',')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: gyűjtemény<br /><br /> **Leírás**: kötelező. A gyűjtemény elemeit csatlakozni.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: elválasztó<br /><br /> **Leírás**: kötelező. Elemek határoló karakterláncot.|  
  
### <a name="string-functions"></a>Karakterlánc

A következő funkciók csak karakterláncok vonatkoznak. Néhány gyűjtemény funkciókat is használhatja, ha a karakterláncok.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|Concat|Tetszőleges számú karakterláncok együtt egyesíti. Például, ha a paraméter 1 `p1`, a függvény `somevalue-p1-somevalue`: <p>`concat('somevalue-',parameters('parameter1'),'-somevalue')` <p> **Számú paraméter**: 1... *n* <p> **Név**: karakterlánc *n* <p> **Leírás**: kötelező. A karakterláncok egyesítése egyetlen karakterlánccá egyesít.|  
|Substring|Egy karakterlánc karakterből álló alkészletet ad vissza. Például, a függvény `abc`: <p>`substring('somevalue-abc-somevalue',10,3)` <p> **Számú paraméter**: 1 <p> **Név**: karakterlánc <p> **Leírás**: kötelező. A karakterláncot, ahol a substring használatban van. <p> **Számú paraméter**: 2. régiója <p> **Név**: kezdőindex <p> **Leírás**: kötelező. Ahol a substring megkezdődik az 1. paraméter indexe. <p> **Számú paraméter**: 3 <p> **Név**: hossza <p> **Leírás**: kötelező. A substring hosszát.|  
|cserélje le|Lecseréli a karakterlánc egy adott karakterláncot. Például, a függvény `the new string`: <p>`replace('the old string', 'old', 'new')` <p> **Számú paraméter**: 1 <p> **Név**: karakterlánc <p> **Leírás**: kötelező. A karakterlánc, amely a 2. paraméter keresendő és frissül az paraméterrel 3, 2. paraméter 1 paraméter található. <p> **Számú paraméter**: 2. régiója <p> **Név**: régi karakterlánc <p> **Leírás**: kötelező. A karakterlánc lecseréli paraméter 3, ha van egyezés paraméter 1 <p> **Számú paraméter**: 3 <p> **Név**: új karakterlánc <p> **Leírás**: kötelező. Cserélje le a karakterlánc a 2. paraméter, ha van egyezés paraméter 1 használt karakterlánc.|  
|GUID|Ez a függvény karakterláncot hoz létre globálisan egyedi (GUID). Ez a funkció például ennek a GUID Azonosítónak hozhat létre: `c2ecc88d-88c8-4096-912c-d6f2e2b138ce` <p>`guid()` <p> **Számú paraméter**: 1 <p> **Név**: formátum <p> **Leírás**: nem kötelező. Egyetlen formátummegadó jelző [a Guid értékének formázása](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). A formátum-paraméter "N", "D", "B", "P" vagy "X" lehet. Ha formátum nem áll rendelkezésre, "D" szolgál.|  
|toLower|Tartalmazó karakterláncot kisbetűssé alakítja. Például, a függvény `two by two is four`: <p>`toLower('Two by Two is Four')` <p> **Számú paraméter**: 1 <p> **Név**: karakterlánc <p> **Leírás**: kötelező. A kis-és alacsonyabb alakítandó karakterláncot. Ha egy karaktert a karakterlánc nem rendelkezik a kisbetűs megfelelőjét, a karakter megtalálható változatlan marad, a visszaadott karakterlánc.|  
|toUpper|Tartalmazó karakterláncot nagybetűssé alakítja. Például, a függvény `TWO BY TWO IS FOUR`: <p>`toUpper('Two by Two is Four')` <p> **Számú paraméter**: 1 <p> **Név**: karakterlánc <p> **Leírás**: kötelező. A felső kis-és alakítandó karakterláncot. Ha egy karaktert a karakterlánc nem rendelkezik nagybetűs egyenértékű, a karakter megtalálható változatlan marad, a visszaadott karakterlánc.|  
|indexof|A karakterlánc eset belüli értéket indexe insensitively található. Például, a függvény `7`: <p>`indexof('hello, world.', 'world')` <p> **Számú paraméter**: 1 <p> **Név**: karakterlánc <p> **Leírás**: kötelező. A értéket tartalmazó karakterlánc. <p> **Számú paraméter**: 2. régiója <p> **Név**: karakterlánc <p> **Leírás**: kötelező. Az érték indexe.|  
|lastindexof|A karakterlánc eset belüli értéket utolsó indexe insensitively található. Például, a függvény `3`: <p>`lastindexof('foofoo', 'foo')` <p> **Számú paraméter**: 1 <p> **Név**: karakterlánc <p> **Leírás**: kötelező. A értéket tartalmazó karakterlánc. <p> **Számú paraméter**: 2. régiója <p> **Név**: karakterlánc <p> **Leírás**: kötelező. Az érték indexe.|  
|startswith elemnek|Ellenőrzi, hogy a karakterlánc kezdődik-e érték eset insensitively. Például, a függvény `true`: <p>`startswith('hello, world', 'hello')` <p> **Számú paraméter**: 1 <p> **Név**: karakterlánc <p> **Leírás**: kötelező. A értéket tartalmazó karakterlánc. <p> **Számú paraméter**: 2. régiója <p> **Név**: karakterlánc <p> **Leírás**: kötelező. A karakterlánc értéke kezdődik.|  
|megadott módon végződő|Ellenőrzi, hogy ha a karakterlánc végződik érték eset insensitively. Például, a függvény `true`: <p>`endswith('hello, world', 'world')` <p> **Számú paraméter**: 1 <p> **Név**: karakterlánc <p> **Leírás**: kötelező. A értéket tartalmazó karakterlánc. <p> **Számú paraméter**: 2. régiója <p> **Név**: karakterlánc <p> **Leírás**: kötelező. Az érték a karakterlánc végén is.|  
|felosztás|Felosztja a karakterláncot, amely egy elválasztó. Például, a függvény `["a", "b", "c"]`: <p>`split('a;b;c',';')` <p> **Számú paraméter**: 1 <p> **Név**: karakterlánc <p> **Leírás**: kötelező. A karakterlánc, amely van szétosztva. <p> **Számú paraméter**: 2. régiója <p> **Név**: karakterlánc <p> **Leírás**: kötelező. Az elválasztó.|  
  
### <a name="logical-functions"></a>Logikai funkciók  

Ezek a funkciók feltételek belül lehetnek hasznosak, és bármilyen típusú logika kiértékeléséhez használható.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|egyenlő|Igaz értéket ad eredményül, ha két érték egyenlő. Például ha parameter1 someValue, a függvény `true`: <p>`equals(parameters('parameter1'), 'someValue')` <p> **Számú paraméter**: 1 <p> **Név**: 1 objektum <p> **Leírás**: kötelező. Az objektum összehasonlítandó **objektum 2**. <p> **Számú paraméter**: 2. régiója <p> **Név**: 2 objektum <p> **Leírás**: kötelező. Az objektum összehasonlítandó **objektum 1**.|  
|kevesebb|Igaz értéket ad eredményül, ha az első argumentum nem kisebb, mint a második. Vegye figyelembe, hogy értékek csak lehetnek típusa egész szám, lebegőpontos vagy karakterlánc. Például, a függvény `true`: <p>`less(10,100)` <p> **Számú paraméter**: 1 <p> **Név**: 1 objektum <p> **Leírás**: kötelező. Az objektum ellenőrizze, hogy van legalább **objektum 2**. <p> **Számú paraméter**: 2. régiója <p> **Név**: 2 objektum <p> **Leírás**: kötelező. Annak ellenőrzése, hogy az nagyobb, mint az objektum **objektum 1**.|  
|lessOrEquals|Igaz értéket ad eredményül, ha az első argumentum értéke nagyobb, mint a második. Vegye figyelembe, hogy értékek csak lehetnek típusa egész szám, lebegőpontos vagy karakterlánc. Például, a függvény `true`: <p>`lessOrEquals(10,10)` <p> **Számú paraméter**: 1 <p> **Név**: 1 objektum <p> **Leírás**: kötelező. Annak ellenőrzése, hogy az kisebb vagy egyenlő objektum **objektum 2**. <p> **Számú paraméter**: 2. régiója <p> **Név**: 2 objektum <p> **Leírás**: kötelező. Nagyobb vagy egyenlő esetén ellenőrizze az objektum **objektum 1**.|  
|nagyobb|Igaz értéket ad eredményül, ha az első argumentum értéke nagyobb, mint a második. Vegye figyelembe, hogy értékek csak lehetnek típusa egész szám, lebegőpontos vagy karakterlánc. Például, a függvény `false`:  <p>`greater(10,10)` <p> **Számú paraméter**: 1 <p> **Név**: 1 objektum <p> **Leírás**: kötelező. Annak ellenőrzése, hogy az nagyobb, mint az objektum **objektum 2**. <p> **Számú paraméter**: 2. régiója <p> **Név**: 2 objektum <p> **Leírás**: kötelező. Az objektum ellenőrizze, hogy van legalább **objektum 1**.|  
|greaterOrEquals|Igaz értéket ad eredményül, ha az első argumentum nagyobb vagy egyenlő a második. Vegye figyelembe, hogy értékek csak lehetnek típusa egész szám, lebegőpontos vagy karakterlánc. Például, a függvény `false`: <p>`greaterOrEquals(10,100)` <p> **Számú paraméter**: 1 <p> **Név**: 1 objektum <p> **Leírás**: kötelező. Nagyobb vagy egyenlő esetén ellenőrizze az objektum **objektum 2**. <p> **Számú paraméter**: 2. régiója <p> **Név**: 2 objektum <p> **Leírás**: kötelező. Annak ellenőrzése, hogy az kisebb vagy egyenlő, mint az objektum **objektum 1**.|  
|és|Igaz értéket ad eredményül, ha mindkét paraméter értéke igaz. A logikai mindkét argumentumot kell. Például, a függvény `false`: <p>`and(greater(1,10),equals(0,0))` <p> **Számú paraméter**: 1 <p> **Név**: logikai 1 <p> **Leírás**: kötelező. Az első argumentum kell lennie a `true`. <p> **Számú paraméter**: 2. régiója <p> **Név**: logikai 2 <p> **Leírás**: kötelező. A második argumentum lehet `true`.|  
|vagy|Igaz értéket ad eredményül, ha bármelyik paraméter értéke true. A logikai mindkét argumentumot kell. Például, a függvény `true`: <p>`or(greater(1,10),equals(0,0))` <p> **Számú paraméter**: 1 <p> **Név**: logikai 1 <p> **Leírás**: kötelező. Az első argumentum, amelyek esetleg `true`. <p> **Számú paraméter**: 2. régiója <p> **Név**: logikai 2 <p> **Leírás**: kötelező. A második argumentum lehet `true`.|  
|nem|Igaz értéket ad eredményül, ha a paraméterek `false`. A logikai mindkét argumentumot kell. Például, a függvény `true`: <p>`not(contains('200 Success','Fail'))` <p> **Számú paraméter**: 1 <p> **Név**: logikai <p> **Leírás**: IGAZ értéket ad vissza, ha a paraméterek `false`. A logikai mindkét argumentumot kell. Ez a függvény visszaadja `true`:  `not(contains('200 Success','Fail'))`|  
|if|E kifejezés eredményezett alapján egy megadott értéket adja vissza `true` vagy `false`.  Például, a függvény `"yes"`: <p>`if(equals(1, 1), 'yes', 'no')` <p> **Számú paraméter**: 1 <p> **Név**: kifejezés <p> **Leírás**: kötelező. A kifejezés egy logikai érték, amely meghatározza, melyik értéket kell visszaadnia. <p> **Számú paraméter**: 2. régiója <p> **Név**: igaz <p> **Leírás**: kötelező. A visszatérési érték, ha a kifejezés `true`. <p> **Számú paraméter**: 3 <p> **Név**: hamis <p> **Leírás**: kötelező. A visszatérési érték, ha a kifejezés `false`.|  
  
### <a name="conversion-functions"></a>Átalakítás funkciók  

Ezek a függvények minden nyelven natív típusai közötti átváltásra használhatók:  
  
- karakterlánc  
  
- integer  
  
- Lebegőpontos  
  
- logikai  
  
- Tömbök  
  
- szótárak  

-   Űrlap  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|int|A paraméter átalakítása egy egész számot. Ez a funkció például 100 karakterlánc helyett egy számot adja vissza: <p>`int('100')` <p> **Számú paraméter**: 1 <p> **Név**: érték <p> **Leírás**: kötelező. Az érték, amely alakítja át egy egész számot.|  
|karakterlánc|A paraméter alakítható át karakterlánccá. Például, a függvény `'10'`: <p>`string(10)` <p>Az objektum is konvertálhatók karakterlánccá. Például ha a `myPar` paraméter egy tulajdonság az objektum `abc : xyz`, akkor ez a függvény visszaadja `{"abc" : "xyz"}`: <p>`string(parameters('myPar'))` <p> **Számú paraméter**: 1 <p> **Név**: érték <p> **Leírás**: kötelező. Az érték, amely karakterlánccá alakítja át.|  
|json|A paraméternek JSON típusú értékké konvertálni és fordítottja `string()`. Például, a függvény `[1,2,3]` tömb, nem pedig egy karakterlánc: <p>`json('[1,2,3]')` <p>Hasonlóképpen átválthat a karakterlánc objektum. Például, a függvény `{ "abc" : "xyz" }`: <p>`json('{"abc" : "xyz"}')` <p> **Számú paraméter**: 1 <p> **Név**: karakterlánc <p> **Leírás**: kötelező. A karakterlánc, amely natív típusa értékre alakítja át. <p>A `json()` függvény túl a bemeneti XML használatát támogatja. Ha például a paraméter értékét: <p>`<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>` <p>a JSON formátumúvá alakul: <p>`{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|Lebegőpontos|A paraméter argumentum konvertálható lebegőpontos szám. Például, a függvény `10.333`: <p>`float('10.333')` <p> **Számú paraméter**: 1 <p> **Név**: érték <p> **Leírás**: kötelező. Az érték, amely a lebegőpontos számként alakítja át.|  
|logikai érték|A paraméter átalakítása olyan logikai érték. Például, a függvény `false`: <p>`bool(0)` <p> **Számú paraméter**: 1 <p> **Név**: érték <p> **Leírás**: kötelező. Az érték, amely egy logikai érték alakítja át.|  
|a Base64|A bemeneti karakterlánc a base64 alakot adja vissza. Például, a függvény `c29tZSBzdHJpbmc=`: <p>`base64('some string')` <p> **Számú paraméter**: 1 <p> **Név**: karakterlánc-1 <p> **Leírás**: kötelező. A karakterlánc helyére base64 kódolása.|  
|base64ToBinary|A base64 kódolású karakterlánc bináris alakot adja vissza. Például, a függvény a bináris megjelenítése `some string`: <p>`base64ToBinary('c29tZSBzdHJpbmc=')` <p> **Számú paraméter**: 1 <p> **Név**: karakterlánc <p> **Leírás**: kötelező. A base64 kódolású karakterlánc.|  
|base64ToString|A based64 kódolású karakterlánc karakterlánc alakot adja vissza. Például, a függvény `some string`: <p>`base64ToString('c29tZSBzdHJpbmc=')` <p> **Számú paraméter**: 1 <p> **Név**: karakterlánc <p> **Leírás**: kötelező. A base64 kódolású karakterlánc.|  
|Bináris|Egy bináris értéket képviselő alakot adja vissza.  Például, a függvény a bináris megjelenítése `some string`: <p>`binary('some string')` <p> **Számú paraméter**: 1 <p> **Név**: érték <p> **Leírás**: kötelező. Az érték, amely bináris alakítja át.|  
|dataUriToBinary|Egy adat-URI azonosító egy bináris alakot adja vissza. Például, a függvény a bináris megjelenítése `some string`: <p>`dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')` <p> **Számú paraméter**: 1 <p> **Név**: karakterlánc <p> **Leírás**: kötelező. Az adatok átalakítása a bináris megjelenítése URI.|  
|dataUriToString|Egy adat-URI azonosító egy karakterlánc alakot adja vissza. Például, a függvény `some string`: <p>`dataUriToString('data:;base64,c29tZSBzdHJpbmc=')` <p> **Számú paraméter**: 1 <p> **Név**: karakterlánc<p> **Leírás**: kötelező. Az adatok átalakítása karakteres URI.|  
|dataUri|Egy adat-URI azonosító a értékét adja vissza. Például, a függvény az adat-URI azonosító `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=`: <p>`dataUri('some string')` <p> **Számú paraméter**: 1<p> **Név**: érték<p> **Leírás**: kötelező. A érték konvertálása az adat-URI azonosító.|  
|decodeBase64|Egy bemeneti based64 karakterlánc karakterlánc alakot adja vissza. Például, a függvény `some string`: <p>`decodeBase64('c29tZSBzdHJpbmc=')` <p> **Számú paraméter**: 1 <p> **Név**: karakterlánc <p> **Leírás**: egy bemeneti based64 karakterlánc karakterlánc alakot adja vissza.|  
|encodeUriComponent|URL-Kilépés az átadott karakterlánc. Például, a függvény `You+Are%3ACool%2FAwesome`: <p>`encodeUriComponent('You Are:Cool/Awesome')` <p> **Számú paraméter**: 1 <p> **Név**: karakterlánc <p> **Leírás**: kötelező. Az URL nem biztonságos karakter átléptetése a karakterláncot.|  
|decodeUriComponent|UN-URL-Kilépés az átadott karakterlánc. Például, a függvény `You Are:Cool/Awesome`: <p>`decodeUriComponent('You+Are%3ACool%2FAwesome')` <p> **Számú paraméter**: 1 <p> **Név**: karakterlánc <p> **Leírás**: kötelező. A karakterlánc az URL nem biztonságos karaktereket dekódolására.|  
|decodeDataUri|Egy bemeneti adatokat URI karakterlánc bináris alakot adja vissza. Például, a függvény a bináris megjelenítése `some string`: <p>`decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')` <p> **Számú paraméter**: 1 <p> **Név**: karakterlánc <p> **Leírás**: kötelező. A bináris megjelenítése a dekódolás dataURI.|  
|uriComponent|Egy URI-t adja vissza egy értékének kódolva. Például, a függvény `You+Are%3ACool%2FAwesome`: <p>`uriComponent('You Are:Cool/Awesome')` <p> **Számú paraméter**: 1<p> **Név**: karakterlánc <p> **Leírás**: kötelező. A karakterlánc URI-kódolt.|  
|uriComponentToBinary|Visszaadja a bináris megjelenítése egy URI-kódolású karakterlánc. Például, a függvény a bináris megjelenítése `You Are:Cool/Awesome`: <p>`uriComponentToBinary('You+Are%3ACool%2FAwesome')` <p> **Számú paraméter**: 1 <p> **Név**: karakterlánc<p> **Leírás**: kötelező. Az URI-kódolású karakterlánc.|  
|uriComponentToString|Visszaadja a URI karakterlánc-ábrázolása kódolású karakterlánc. Például, a függvény `You Are:Cool/Awesome`: <p>`uriComponentToString('You+Are%3ACool%2FAwesome')` <p> **Számú paraméter**: 1<p> **Név**: karakterlánc<p> **Leírás**: kötelező. Az URI-kódolású karakterlánc.|  
|xml|Térjen vissza az értéket XML-ábrázolása. Például ezt a funkciót XML-kódot eredményezzen tartalom által képviselt `'\<name>Alan\</name>'`: <p>`xml('\<name>Alan\</name>')` <p>A `xml()` függvény túl a bemeneti JSON-objektum használatát támogatja. Például a paraméter `{ "abc": "xyz" }` alakítja át az XML-tartalom: `\<abc>xyz\</abc>` <p> **Számú paraméter**: 1<p> **Név**: érték<p> **Leírás**: kötelező. Az érték átalakítása XML.|  
|tömb|A paraméter átalakítása tömb. Például, a függvény `["abc"]`: <p>`array('abc')` <p> **Számú paraméter**: 1 <p> **Név**: érték <p> **Leírás**: kötelező. Az érték, amely a tömb alakítja át.|
|createArray|A paraméter egy tömb jön létre. Például, a függvény `["a", "c"]`: <p>`createArray('a', 'c')` <p> **Számú paraméter**: 1... *n* <p> **Név**: bármely *n* <p> **Leírás**: kötelező. Az értékek egy tömb egységgé kombinálják.|
|triggerFormDataValue|Űrlap-adatok, vagy a képernyő-kódolású eseményindító kimenetét a kulcsnév megfelelő egyetlen értéket ad vissza.  Ha egyszerre több megfelelő azt fogja hiba.  Például a következő visszaadható `bar`: `triggerFormDataValue('foo')`<br /><br />**Számú paraméter**: 1<br /><br />**Név**: kulcs neve<br /><br />**Leírás**: kötelező. Az űrlap adatérték vissza kulcs neve.|
|triggerFormDataMultiValues|Űrlap-adatok, vagy a képernyő-kódolású eseményindító kimenetét a kulcsnév egyező tömbjét adja vissza.  Például a következő visszaadható `["bar"]`: `triggerFormDataValue('foo')`<br /><br />**Számú paraméter**: 1<br /><br />**Név**: kulcs neve<br /><br />**Leírás**: kötelező. Az űrlap adatértékek vissza kulcs neve.|
|triggerMultipartBody|Az eseményindító több részből álló kimeneti adja vissza a szervezet egy részére.<br /><br />**Számú paraméter**: 1<br /><br />**Név**: Index<br /><br />**Leírás**: kötelező. Az index, az elem beolvasása.|
|formDataValue|Az űrlap-adatok vagy a képernyő-kódolású műveleti kimenet kulcsnév megfelelő egyetlen értéket ad vissza.  Ha egyszerre több megfelelő azt fogja hiba.  Például a következő visszaadható `bar`: `formDataValue('someAction', 'foo')`<br /><br />**Számú paraméter**: 1<br /><br />**Név**: a művelet neve<br /><br />**Leírás**: kötelező. Az űrlap-adatok vagy a képernyő-kódolású válasz művelet neve.<br /><br />**Számú paraméter**: 2. régiója<br /><br />**Név**: kulcs neve<br /><br />**Leírás**: kötelező. Az űrlap adatérték vissza kulcs neve.|
|formDataMultiValues|Az űrlap-adatok vagy a képernyő-kódolású műveleti kimenet kulcsnév egyező tömbjét adja vissza.  Például a következő visszaadható `["bar"]`: `formDataMultiValues('someAction', 'foo')`<br /><br />**Számú paraméter**: 1<br /><br />**Név**: a művelet neve<br /><br />**Leírás**: kötelező. Az űrlap-adatok vagy a képernyő-kódolású válasz művelet neve.<br /><br />**Számú paraméter**: 2. régiója<br /><br />**Név**: kulcs neve<br /><br />**Leírás**: kötelező. Az űrlap adatértékek vissza kulcs neve.|
|multipartBody|A szervezet egy részére egy több részből álló kimeneti művelet adja vissza.<br /><br />**Számú paraméter**: 1<br /><br />**Név**: a művelet neve<br /><br />**Leírás**: kötelező. A művelet több részből álló választ neve.<br /><br />**Számú paraméter**: 2. régiója<br /><br />**Név**: Index<br /><br />**Leírás**: kötelező. Az index, az elem beolvasása.|

### <a name="manipulation-functions"></a>Adatkezelési függvények
 
XML- és objektumok alkalmazni ezeket a funkciókat.
 
|Függvény neve|Leírás|  
|-------------------|-----------------| 
|Egyesítés|Az argumentumként átadott az első nem null objektumot ad vissza. **Megjegyzés:**: üres karakterlánc értéke nem null. Például, ha nincsenek megadva paraméterek 1 és 2, a függvény `fallback`:  <p>`coalesce(parameters('parameter1'), parameters('parameter2') ,'fallback')` <p> **Számú paraméter**: 1... *n* <p> **Név**: objektum*n* <p> **Leírás**: kötelező. Az objektumok kereséséhez null.|
|addProperty|Visszaad egy objektumot, és további tulajdonsága. Ha a tulajdonság már létezik a futási időben hibát fog jelezni. Például, a függvény az objektum `{ "abc" : "xyz", "def": "uvw" }`: <p>`addProperty(json('{"abc" : "xyz"}'), 'def', 'uvw')` <p> **Számú paraméter**: 1 <p> **Név**: objektum <p> **Leírás**: kötelező. Az objektum hozzáadása az új tulajdonság. <p> **Számú paraméter**: 2. régiója <p> **Név**: tulajdonság neve <p> **Leírás**: kötelező. Az új tulajdonság neve. <p> **Számú paraméter**: 3 <p> **Név**: érték <p> **Leírás**: kötelező. Rendelje hozzá az új tulajdonság értéke.|
|setProperty|Egy további vagy egy meglévő tulajdonságának beállítása a megadott érték az objektum beállítása/beolvasása. Például, a függvény az objektum `{ "abc" : "uvw" }`: <p>`setProperty(json('{"abc" : "xyz"}'), 'abc', 'uvw')` <p> **Számú paraméter**: 1 <p> **Név**: objektum <p> **Leírás**: kötelező. Az objektum használandó állítsa be a tulajdonságot.<p> **Számú paraméter**: 2. régiója <p> **Név**: tulajdonság neve<p> **Leírás**: kötelező. Új vagy meglévő tulajdonságának neve. <p> **Számú paraméter**: 3 <p> **Név**: érték <p> **Leírás**: kötelező. Az érték a tulajdonság hozzárendelése.|
|removeProperty|Visszaad egy objektumot eltávolítani tulajdonsággal. Ha eltávolítja a tulajdonság nem létezik az eredeti objektum ad vissza. Például, a függvény az objektum `{ "abc" : "xyz" }`: <p>`removeProperty(json('{"abc" : "xyz", "def": "uvw"}'), 'def')` <p> **Számú paraméter**: 1 <p> **Név**: objektum <p> **Leírás**: kötelező. Az objektum eltávolítása a tulajdonságot.<p> **Számú paraméter**: 2. régiója <p> **Név**: tulajdonság neve <p> **Leírás**: kötelező. Eltávolítja a tulajdonság nevét. <p>|
|XPath|Az xpath-kifejezés az érték, amely az xpath kifejezés értéke megfelelő XML-csomópontnak tömbjét adja vissza. <p> **1. példa** <p>Tegyük fel, a paraméter értékének `p1` az XML-kód egy karakterlánc-ábrázolása: <p>`<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>` <p>Ezt a kódot: `xpath(xml(parameters('p1')), '/lab/robot/name')` <p>adja vissza <p>`[ <name>R1</name>, <name>R2</name> ]` <p>Amikor ezt a kódot: <p>`xpath(xml(parameters('p1')), ' sum(/lab/robot/parts)')` <p>adja vissza <p>`13` <p> <p> **2. példa** <p>Adja meg a következő XML-tartalom: <p>`<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>` <p>Ezt a kódot: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')` <p>vagy ezt a kódot: <p>`@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')` <p>adja vissza <p>`<Location xmlns="http://abc.com">xyz</Location>` <p>És ezzel a kóddal: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')` <p>adja vissza <p>``xyz`` <p> **Számú paraméter**: 1 <p> **Név**: Xml <p> **Leírás**: kötelező. Az XML, amelyiken az XPath-kifejezés kiértékelése. <p> **Számú paraméter**: 2. régiója <p> **Név**: XPath <p> **Leírás**: kötelező. Az XPath-kifejezés kiértékelése.|

### <a name="math-functions"></a>Matematikai függvények  

Ezeket a funkciókat használhatja bármelyik típusú számot: **egész számok** és **képest az előtérben**.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|hozzáadás|A két számok eredményét adja vissza. Például, a függvény `20.333`: <p>`add(10,10.333)` <p> **Számú paraméter**: 1 <p> **Név**: Summand 1 <p> **Leírás**: kötelező. A hozzáadni kívánt számot **Summand 2**. <p> **Számú paraméter**: 2. régiója <p> **Név**: Summand 2 <p> **Leírás**: kötelező. A hozzáadni kívánt számot **Summand 1**.|  
|Sub|A két szám különbsége eredményét adja vissza. Például, a függvény `-0.333`: <p>`sub(10,10.333)` <p> **Számú paraméter**: 1 <p> **Név**: Minuend <p> **Leírás**: kötelező. A szám, amely **Subtrahend** törlődik. <p> **Számú paraméter**: 2. régiója <p> **Név**: Subtrahend <p> **Leírás**: kötelező. Távolítsa el a számot a **Minuend**.|  
|MUL számú|Az eredményt ad vissza a két szám szorzásával. Például, a függvény `103.33`: <p>`mul(10,10.333)` <p> **Számú paraméter**: 1 <p> **Név**: Multiplicand 1 <p> **Leírás**: kötelező. A szám szorzóját **Multiplicand 2** együtt. <p> **Számú paraméter**: 2. régiója <p> **Név**: Multiplicand 2 <p> **Leírás**: kötelező. A szám szorzóját **Multiplicand 1** együtt.|  
|div|A két szám osztásával kapott eredményét adja vissza. Például, a függvény `1.0333`: <p>`div(10.333,10)` <p> **Számú paraméter**: 1 <p> **Név**: osztandó <p> **Leírás**: kötelező. A szám nullával a **osztó**. <p> **Számú paraméter**: 2. régiója <p> **Név**: osztó <p> **Leírás**: kötelező. A szám felosztása a **osztandó** által.|  
|MOD|A két szám (modulo) felosztásával után a maradékot adja vissza. Például, a függvény `2`: <p>`mod(10,4)` <p> **Számú paraméter**: 1 <p> **Név**: osztandó <p> **Leírás**: kötelező. A szám nullával a **osztó**. <p> **Számú paraméter**: 2. régiója <p> **Név**: osztó <p> **Leírás**: kötelező. A szám felosztása a **osztandó** által. A felosztás után ezeket a többi használatban van.|  
|perc|Ez a függvény hívásakor két különböző mintát van. <p>Itt `min` egy tömb vesz igénybe, és a függvény visszaadja `0`: <p>`min([0,1,2])` <p>Azt is megteheti, ez a funkció is igénybe vehet, értékeket, és visszaadja a vesszővel tagolt listáját `0`: <p>`min(0,1,2)` <p> **Megjegyzés:**: minden értékeknek kell lenniük, számok, ha a paraméter egy tömb, a tömb rendelkezik, hogy csak a számokat. <p> **Számú paraméter**: 1 <p> **Név**: gyűjtemény vagy érték <p> **Leírás**: kötelező. Vagy tömbje értékek a legkisebb értéket keresi, vagy egy első értékét. <p> **Számú paraméter**: 2... *n* <p> **Név**: érték *n* <p> **Leírás**: nem kötelező. Ha az első paraméter értéke, átadhatók további értékek, és a minimális átadott értékek adja vissza.|  
|max.|Ez a függvény hívásakor két különböző mintát van. <p>Itt `max` egy tömb vesz igénybe, és a függvény visszaadja `2`: <p>`max([0,1,2])` <p>Azt is megteheti, ez a funkció is igénybe vehet, értékeket, és visszaadja a vesszővel tagolt listáját `2`: <p>`max(0,1,2)` <p> **Megjegyzés:**: minden értékeknek kell lenniük, számok, ha a paraméter egy tömb, a tömb rendelkezik, hogy csak a számokat. <p> **Számú paraméter**: 1 <p> **Név**: gyűjtemény vagy érték <p> **Leírás**: kötelező. Vagy tömbje értékek a legnagyobb értéket keresi, vagy egy első értékét. <p> **Számú paraméter**: 2... *n* <p> **Név**: érték *n* <p> **Leírás**: nem kötelező. Ha az első paraméter értéke, átadhatók további értékek, és a maximális átadott értékek adja vissza.|  
|tartomány|Egy adott értéket egész számok tömbje állít elő. A visszaadott tömb hosszának megadása. <p>Például, a függvény `[3,4,5,6]`: <p>`range(3,4)` <p> **Számú paraméter**: 1 <p> **Név**: kezdőindex <p> **Leírás**: kötelező. A tömb első egész szám. <p> **Számú paraméter**: 2. régiója <p> **Név**: száma <p> **Leírás**: kötelező. Ez az érték egész szám, amely a tömb száma.|  
|VÉL|Hoz létre egy véletlenszerű egész (csak az első vége között lehet) a megadott tartományban. Például ezt a funkciót adhat vissza vagy `0` vagy "1": <p>`rand(0,2)` <p> **Számú paraméter**: 1 <p> **Név**: minimális <p> **Leírás**: kötelező. A visszaadható legkisebb egész. <p> **Számú paraméter**: 2. régiója <p> **Név**: maximális <p> **Leírás**: kötelező. Ez az érték a következő egész számra a legnagyobb egész számot visszatérő utánra esik.|  
 
### <a name="date-functions"></a>Dátum-funkciók  

|Függvény neve|Leírás|  
|-------------------|-----------------|  
|utcnow|Például egy karakterlánc adja vissza az aktuális időbélyeg: `2017-03-15T13:27:36Z`: <p>`utcnow()` <p> **Számú paraméter**: 1 <p> **Név**: formátum <p> **Leírás**: nem kötelező. Vagy egy [egyetlen formátum megadása](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formátum mintát](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) azt jelzi, hogy az időbélyegző értékének formázásának módja. Ha formátum nem áll rendelkezésre, az ISO 8601 formátumban ("no") használatos.|  
|masodpercekHozzaadasa|Az átadott karakterlánc időbélyeg ad hozzá egy ennyi ideig. Azon másodpercek számát pozitív vagy negatív is lehet. Alapértelmezés szerint eredménye karakterláncként ISO 8601 formátumban ("no"), kivéve, ha a formátummegadóval való ábrázoláshoz valósul meg. Például: `2015-03-15T13:27:00Z`: <p>`addseconds('2015-03-15T13:27:36Z', -36)` <p> **Számú paraméter**: 1 <p> **Név**: időbélyeg <p> **Leírás**: kötelező. Az idő tartalmazó karakterlánc. <p> **Számú paraméter**: 2. régiója <p> **Név**: másodperc <p> **Leírás**: kötelező. Az időtartamot másodpercben, adja hozzá. Negatív kivonandó időnél másodperc lehet. <p> **Számú paraméter**: 3 <p> **Név**: formátum <p> **Leírás**: nem kötelező. Vagy egy [egyetlen formátum megadása](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formátum mintát](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) azt jelzi, hogy az időbélyegző értékének formázásának módja. Ha formátum nem áll rendelkezésre, az ISO 8601 formátumban ("no") használatos.|  
|addminutes|Az átadott karakterlánc időbélyeg ad hozzá egy egész számot hány perc. A percet lehet pozitív vagy negatív. Alapértelmezés szerint eredménye karakterláncként ISO 8601 formátumban ("no"), kivéve, ha a formátummegadóval való ábrázoláshoz valósul meg. Például: `2015-03-15T14:00:36Z`: <p>`addminutes('2015-03-15T13:27:36Z', 33)` <p> **Számú paraméter**: 1 <p> **Név**: időbélyeg <p> **Leírás**: kötelező. Az idő tartalmazó karakterlánc. <p> **Számú paraméter**: 2. régiója <p> **Név**: perc <p> **Leírás**: kötelező. A hozzáadandó percek számát. Negatív kivonandó időnél perc lehet. <p> **Számú paraméter**: 3 <p> **Név**: formátum <p> **Leírás**: nem kötelező. Vagy egy [egyetlen formátum megadása](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formátum mintát](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) azt jelzi, hogy az időbélyegző értékének formázásának módja. Ha formátum nem áll rendelkezésre, az ISO 8601 formátumban ("no") használatos.|  
|addhours|Az átadott karakterlánc időbélyeg ad hozzá az órát egész szám. Az órát lehet pozitív vagy negatív. Alapértelmezés szerint eredménye karakterláncként ISO 8601 formátumban ("no"), kivéve, ha a formátummegadóval való ábrázoláshoz valósul meg. Például: `2015-03-16T01:27:36Z`: <p>`addhours('2015-03-15T13:27:36Z', 12)` <p> **Számú paraméter**: 1 <p> **Név**: időbélyeg <p> **Leírás**: kötelező. Az idő tartalmazó karakterlánc. <p> **Számú paraméter**: 2. régiója <p> **Név**: üzemideje (óra) <p> **Leírás**: kötelező. A hozzáadandó órák száma. Negatív kivonandó időnél óra lehet. <p> **Számú paraméter**: 3 <p> **Név**: formátum <p> **Leírás**: nem kötelező. Vagy egy [egyetlen formátum megadása](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formátum mintát](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) azt jelzi, hogy az időbélyegző értékének formázásának módja. Ha formátum nem áll rendelkezésre, az ISO 8601 formátumban ("no") használatos.|  
|napokHozzaadasa|Az átadott karakterlánc időbélyeg ad hozzá egy nap egész szám. Hány napig lehet pozitív vagy negatív. Alapértelmezés szerint eredménye karakterláncként ISO 8601 formátumban ("no"), kivéve, ha a formátummegadóval való ábrázoláshoz valósul meg. Például: `2015-03-13T13:27:36Z`: <p>`adddays('2015-03-15T13:27:36Z', -2)` <p> **Számú paraméter**: 1 <p> **Név**: időbélyeg <p> **Leírás**: kötelező. Az idő tartalmazó karakterlánc. <p> **Számú paraméter**: 2. régiója <p> **Név**: napok <p> **Leírás**: kötelező. A hozzáadni kívánt napok száma. Negatív kivonandó időnél nap lehet. <p> **Számú paraméter**: 3 <p> **Név**: formátum <p> **Leírás**: nem kötelező. Vagy egy [egyetlen formátum megadása](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formátum mintát](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) azt jelzi, hogy az időbélyegző értékének formázásának módja. Ha formátum nem áll rendelkezésre, az ISO 8601 formátumban ("no") használatos.|  
|formatDateTime|Dátum formátumú karakterláncot ad vissza. Alapértelmezés szerint eredménye karakterláncként ISO 8601 formátumban ("no"), kivéve, ha a formátummegadóval való ábrázoláshoz valósul meg. Például: `2015-03-15T13:27:36Z`: <p>`formatDateTime('2015-03-15T13:27:36Z', 'o')` <p> **Számú paraméter**: 1 <p> **Név**: dátuma <p> **Leírás**: kötelező. A dátum tartalmazó karakterlánc. <p> **Számú paraméter**: 2. régiója <p> **Név**: formátum <p> **Leírás**: nem kötelező. Vagy egy [egyetlen formátum megadása](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formátum mintát](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) azt jelzi, hogy az időbélyegző értékének formázásának módja. Ha formátum nem áll rendelkezésre, az ISO 8601 formátumban ("no") használatos.|  
|startOfHour|Az órát kezdetét adja vissza. az átadott karakterlánc időbélyeg. Például `2017-03-15T13:00:00Z`:<br /><br /> `startOfHour('2017-03-15T13:27:36Z')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: időbélyeg<br /><br /> **Leírás**: kötelező. Ez az az idő tartalmazó karakterlánc.<br /><br />**Számú paraméter**: 2. régiója<br /><br /> **Név**: formátum<br /><br /> **Leírás**: nem kötelező. Vagy egy [egyetlen formátum megadása](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formátum mintát](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) azt jelzi, hogy az időbélyegző értékének formázásának módja. Ha formátum nem áll rendelkezésre, az ISO 8601 formátumban ("no") használatos.|  
|startOfDay|Az átadott karakterlánc időbélyeg nap kezdetét adja vissza. Például `2017-03-15T00:00:00Z`:<br /><br /> `startOfDay('2017-03-15T13:27:36Z')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: időbélyeg<br /><br /> **Leírás**: kötelező. Ez az az idő tartalmazó karakterlánc.<br /><br />**Számú paraméter**: 2. régiója<br /><br /> **Név**: formátum<br /><br /> **Leírás**: nem kötelező. Vagy egy [egyetlen formátum megadása](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formátum mintát](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) azt jelzi, hogy az időbélyegző értékének formázásának módja. Ha formátum nem áll rendelkezésre, az ISO 8601 formátumban ("no") használatos.| 
|startOfMonth|A hónap kezdetét adja vissza. az átadott karakterlánc időbélyeg. Például `2017-03-01T00:00:00Z`:<br /><br /> `startOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: időbélyeg<br /><br /> **Leírás**: kötelező. Ez az az idő tartalmazó karakterlánc.<br /><br />**Számú paraméter**: 2. régiója<br /><br /> **Név**: formátum<br /><br /> **Leírás**: nem kötelező. Vagy egy [egyetlen formátum megadása](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formátum mintát](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) azt jelzi, hogy az időbélyegző értékének formázásának módja. Ha formátum nem áll rendelkezésre, az ISO 8601 formátumban ("no") használatos.| 
|DayOfWeek|A karakterlánc időbélyeg összetevője hét napját adja vissza.  Vasárnap 0, a hétfői 1, és így tovább. Például `3`:<br /><br /> `dayOfWeek('2017-03-15T13:27:36Z')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: időbélyeg<br /><br /> **Leírás**: kötelező. Ez az az idő tartalmazó karakterlánc.| 
|DayOfMonth|A hónap összetevőjét karakterlánc időbélyeg napját adja vissza. Például `15`:<br /><br /> `dayOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: időbélyeg<br /><br /> **Leírás**: kötelező. Ez az az idő tartalmazó karakterlánc.| 
|DayOfYear|A karakterlánc időbélyeg év összetevőjét napját adja vissza. Például `74`:<br /><br /> `dayOfYear('2017-03-15T13:27:36Z')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: időbélyeg<br /><br /> **Leírás**: kötelező. Ez az az idő tartalmazó karakterlánc.| 
|Ticks|Megjelenjenek-e a tulajdonság egy karakterlánc Timestamp adja vissza. Például `1489603019`:<br /><br /> `ticks('2017-03-15T18:36:59Z')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: időbélyeg<br /><br /> **Leírás**: kötelező. Ez az az idő tartalmazó karakterlánc.| 
  
### <a name="workflow-functions"></a>Munkafolyamat-funkciók  

Ezek a funkciók segítséget nyújt a futási időben magát a munkafolyamatot adatainak beolvasása.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|listCallbackUrl|Meghívni az eseményindító vagy művelet hívására karakterláncot ad vissza. <p> **Megjegyzés:**: Ez a függvény csak a használható egy **httpWebhook** és **apiConnectionWebhook**, nem a egy **manuális**, **ismétlődési**, **http**, vagy **apiConnection**. <p>Például a `listCallbackUrl()` működéshez adja vissza: <p>`https://prod-01.westus.logic.azure.com:443/workflows/1235...ABCD/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxx...xxx` |  
|munkafolyamat|Ez a funkció teszi lehetővé a részletek magát a munkafolyamat a futási időben. <p> A munkafolyamat-objektumot a rendelkezésre álló tulajdonságok a következők: <ul><li>`name`</li><li>`type`</li><li>`id`</li><li>`location`</li><li>`run`</li></ul> <p> Értékét a `run` tulajdonsága egy objektum a következő tulajdonságokkal: <ul><li>`name`</li><li>`type`</li><li>`id`</li></ul> <p>Tekintse meg a [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=525617) meg azokat a tulajdonságokat.<p> Például futtassa az aktuális nevét, amelyet a `"@workflow().run.name"` kifejezés. |

## <a name="next-steps"></a>További lépések

[Munkafolyamat-műveletek és eseményindítók](logic-apps-workflow-actions-triggers.md)
