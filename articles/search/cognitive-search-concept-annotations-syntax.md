---
title: Jegyzet bemenetei között meg és a egy kognitív keresés folyamatban, az Azure Search kimenetek |} A Microsoft Docs
description: A jegyzet szintaxist és a hivatkozás jegyzetre bemeneteit és kimeneteit, a képességek alkalmazási lehetőségét, a kognitív keresés folyamat az Azure Search az útmutató ismerteti.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 1ccc1fb20cb08cfd97d58984676ef4006e693118
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801947"
---
# <a name="how-to-reference-annotations-in-a-cognitive-search-skillset"></a>Egy cognitive search indexmezők széljegyzetet referencia útmutató

Ebből a cikkből megismerheti, hogyan való hivatkozáshoz szakértelem definíciókban, jegyzetek példák segítségével különböző forgatókönyveket mutatja be. A dokumentum tartalma tranzakciós képességek készletével, jegyzetek, lekérdezi renderelésre. Jegyzetek a további alsóbb rétegbeli Adatbővítés bemenetként használt, vagy egy index kimeneti mezője leképezve. 
 
Ebben a cikkben szereplő példák alapulnak a *tartalom* mező automatikusan generált [Azure Blob-indexelők](search-howto-indexing-azure-blob-storage.md) a dokumentumleképezési fázis részeként. Kontextusban való megnevezésekor dokumentumok egy Blob-tárolóból, formátum használata például `"/document/content"`, ahol a *tartalom* mező része a *dokumentum*. 

## <a name="background-concepts"></a>Háttér-fogalmak

Előtt tekintse át a szintaxist, most nyissa meg újra jobb megértése érdekében ez a cikk későbbi részében megadott példák néhány fontos fogalmakat.

| Időtartam | Leírás |
|------|-------------|
| Továbbfejlesztett dokumentum | Az entitásokkal dokumentum olyan, egy belső szerkezetét és a folyamat egy dokumentumot kapcsolódó összes jegyzet tárolására használt. Úgy gondolja, hogy egy képi elemekben gazdag dokumentum a jegyzeteket fának. Általában a létrehozott egy korábbi jegyzet jegyzet alárendelt válik.<p/>Továbbfejlesztett dokumentumok csak indexmezők végrehajtási időtartamának léteznek. Ha tartalmat a keresési index van rendelve, a képi elemekben gazdag dokumentum már nincs rá szükség. Bár nem dolgozhat képi elemekben gazdag dokumentumok közvetlenül, hasznos a dokumentumok a szellemi modell rendelkezik egy képességcsoport létrehozása során. |
| Adatbővítés környezet | A környezet, amelyben a Adatbővítés történik, amelynek elem bővített van. Alapértelmezés szerint a Adatbővítés összefüggésben van az `"/document"` szint, egyes dokumentumok hatóköre. Szakértelem futtatásakor, válik, hogy szakértelem kimeneteire [tulajdonságok meghatározott környezet](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>1. példa: Egyszerű jegyzet referencia

Az Azure Blob storage tegyük fel, amely megnevezett entitások felismerése használatával kiolvasni kívánt személyek nevét mutató hivatkozásokat tartalmazó fájlok különböző. Az alábbi, szakértelem definícióban `"/document/content"` a teljes dokumentum képviselő szöveges alakot van, és "felhasználók" egy kivonása személyek azonosított entitások teljes nevét.

Mivel az alapértelmezett környezet `"/document"`, a listáját, akikkel már alakban lehet hivatkozni `"/document/people"`. Ebben az esetben `"/document/people"` jegyzet, most kell leképezve egy mezőt az indexben, illetve az azonos készségeitől egy másik szakértelem használatban van.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      }
    ]
  }
```

<a name="example-2"></a>

## <a name="example-2-reference-an-array-within-a-document"></a>2. példa: Hivatkozás egy dokumentum egy tömb

Ebben a példában az előzőt, hogyan meghívása egy Adatbővítés lépést több alkalommal keresztül ugyanazt a dokumentumot épül. Tegyük fel, az előző példában létrehozott egyetlen dokumentum 10 személyek nevű karakterláncok tömbje. Előfordulhat, hogy ésszerű a következő lépésben egy második Adatbővítés, amely a vezetéknevet kigyűjti a teljes nevet. Nincsenek 10 nevek, mert azt szeretné, ezt a lépést, která bude volána 10 alkalommal ebben a dokumentumban egyszer minden egyes személy számára. 

A megfelelő számú ismétlések meghívni, állítsa be a környezetet, `"/document/people/*"`, amelyben a csillag (`"*"`) jelöli a csomópontokon a jelentéstétellel dokumentumban lévő leszármazottainak készletét, `"/document/people"`. Bár a szakértelem csak akkor van definiálva, a képességek tömbben nevezzük a dokumentumon belül minden tagja esetében mindaddig, amíg az összes tag feldolgozása után.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the last name from a full name",
    "uri": "http://names.azurewebsites.net/api/GetLastName",
    "context" : "/document/people/*",
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "fullname",
        "source": "/document/people/*"
      }
    ],
    "outputs": [
      {
        "name": "lastname",
        "targetName": "last"
      }
    ]
  }
```

Ha jegyzetek tömbök vagy karakterláncok gyűjteményei, érdemes célként meghatározott tagok helyett a teljes tömb. A fenti példában létrehoz egy nevű jegyzet `"last"` képviseli a környezet minden egyes csomópont alatt. Tekintse meg a családba tartozó jegyzetek szeretné, használhatja a szintaxis `"/document/people/*/last"`. Szeretne hivatkozni egy adott megjegyzés, használhatja az explicit index: `"/document/people/1/last`"való hivatkozáshoz az első, aki a dokumentum azonosított vezetékneve. Figyelje meg, hogy ez a szintaxis a tömbök 0-indexelt".

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>3. példa: Referencia tagok v poli

Néha szüksége át őket egy adott szakértelem egy adott típusú összes jegyzet csoportosításához. Fontolja meg, amely azonosítja a 2. példa a kinyert utolsó nevéből leggyakoribb Vezetéknév elméleti egyéni műveleteket. Az egyéni ismeretek csak a legutóbbi nevét, adjon meg a környezetben `"/document"` és a bemeneti, `"/document/people/*/lastname"`.

Vegye figyelembe, hogy a számossága `"/document/people/*/lastname"` nagyobb, mint amely dokumentumot. Előfordulhatnak 10 lastname csomópont közben van ez a dokumentum csak egy dokumentum-csomópont. Ebben az esetben a rendszer automatikusan létrehoz egy tömbjét `"/document/people/*/lastname"` tartalmazó összes elemét a dokumentumban.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the most common string from an array of strings",
    "uri": "http://names.azurewebsites.net/api/MostCommonString",
    "context" : "/document",
    "inputs": [
      {
        "name": "strings",
        "source": "/document/people/*/lastname"
      }
    ],
    "outputs": [
      {
        "name": "mostcommon",
        "targetName": "common-lastname"
      }
    ]
  }
```



## <a name="see-also"></a>Lásd még
+ [Hogyan integrálható az egyéni ismeretek-felderítési bővítést folyamatba](cognitive-search-custom-skill-interface.md)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
+ [Képességcsoport (REST) létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Hogyan hidaljuk mezők leképezése egy indexbe](cognitive-search-output-field-mapping.md)
