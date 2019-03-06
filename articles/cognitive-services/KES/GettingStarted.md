---
title: 'Példa: Első lépések – Knowledge Exploration Service API'
titlesuffix: Azure Cognitive Services
description: A Knowledge Exploration Service (KES) API használatával az akadémiai kiadványok közötti interaktív kereséshez készíthető motor.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: sample
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: a48fecd910d87af23a9385270878b0d1b6737007
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452538"
---
# <a name="get-started-with-the-knowledge-exploration-service"></a>Első lépések a Knowledge Exploration Service szolgáltatással

Ez az útmutató végigvezeti egy motor létrehozásának lépésein az akadémiai kiadványok közötti interaktív kereséshez a Knowledge Exploration Service (KES) használatával. A parancssori eszközt, [`kes.exe`](CommandLine.md), és minden példa fájlt telepíthet a [Knowledge Exploration Service SDK](https://www.microsoft.com/en-us/download/details.aspx?id=51488) csomagból.

Az akadémiai kiadványok példa a Microsoft kutatói által közzétett 1000 tudományos dolgozatból álló mintát tartalmazza.  Minden egyes tanulmányhoz cím, kiadás éve, szerzők és kulcsszavak tartoznak. Mindegyik szerzőt egy azonosító, név és a publikáció időpontjában aktuális munkaviszony kapcsolat képviseli. Minden kulcsszóhoz szinonima halmaz társítható (például a „support vector machine” társítható a „svm” szinonimájával).

## <a name="define-the-schema"></a>A séma megadása

A séma a tartományban lévő objektumok attribútum felépítését ismerteti. A JSON-fájl formátumban levő minden attribútumra megadja a nevet és az adattípust. Az alábbi példa az *Academic.schema* fájl tartalmát mutatja.

```json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

Itt a *Cím*, *Év* és *Kulcsszó* attribútumokat rendre string, integer és string típusnak definiáljuk. Szerzők Azonosítóját, nevét és kapcsolatot képviseli, mert meghatározhatja *Szerző* összetett attribútumaként három alárendelt attribútumokkal: *Author.Id*, *Author.Name*, és *Author.Affiliation*.

Alapértelmezés szerint az attribútumok valamennyi az adattípusra létező műveletet támogatják, beleértve az *equals*, *starts_with* és *is_between* műveleteket is. Mivel a szerző azonosító csak belső használatra szolgál, változtassa meg az alapértelmezést és az egyedüli indexelt műveletnek az *equals*-t adja meg.

A *Kulcsszó* attribútumra az attribútumdefinícióban a *Keyword.syn* szinonima fájl megadásával engedélyezze a kanonikus kulcsszó értékekre a szinonima társítást. Ez a fájl a kanonikus és szinonima értékpárok listáját tartalmazza:

```json
...
["support vector machine","support vector machines"]
["support vector machine","support vector networks"]
["support vector machine","support vector regression"]
["support vector machine","support vector"]
["support vector machine","svm machine learning"]
["support vector machine","svm"]
["support vector machine","svms"]
["support vector machine","vector machine"]
...
```

A sémadefinícióval kapcsolatos további információk: [Sémaformátum](SchemaFormat.md).

## <a name="generate-data"></a>Adatok generálása

Az adatfájl az indexelendő kiadványok listáját adja meg, mindegyik sora egy kiadvány attribútum értékeit adja meg [JSON formátumban](http://json.org/).  Az alábbi példa az *Academic.data* adatfájl egy sora, az olvashatóság érdekében formázva:

```
...
{
    "logprob": -16.714,
    "Title": "the world wide telescope",
    "Year": 2001,
    "Author": [
        {
            "Id": 717694024,
            "Name": "alexander s szalay",
            "Affiliation": "microsoft"
        },
        {
            "Id": 2131537204,
            "Name": "jim gray",
            "Affiliation": "microsoft"
        }
    ]
}
...
```

Ebben a kódrészletben, a kiadvány *Cím* és *Év* attribútumait adja meg rendre JSON-karakterlánc és szám alakban. A többszörös értékekhez JSON-tömböket használunk. Mivel az *Author* összetett attribútum, mindegyik értéket a részattribútumaiból felépülő JSON objektum képvisel. Ha az attribútum értéke hiányzik, mint a *Kulcsszó* esetében, az attribútum kizárható a JSON ábrázolásból.

A különböző tanulmányok valószínűségeinek megkülönböztetéséhez adja meg a relatív logaritmikus valószínűséget a beépített *logprob* attribútum alapján. Adott *p* 0 és 1 közötti valószínűségre a logaritmikus valószínűség kiszámítása a log(*p*) szerint történik, ahol a log() a természetes logaritmusfüggvényt jelenti.

További információ: [Adatformátumok](DataFormat.md).

## <a name="build-a-compressed-binary-index"></a>Bináris tömörített index létrehozása

Miután a séma- és adatfájlunk megvan, létrehozhatjuk az adatobjektumok bináris tömörített indexét a [`kes.exe build_index`](CommandLine.md#build_index-command) használatával. Ebben a példában az *Academic.index* indexfájlt az *Academic.schema* bemeneti séma- és az *Academic.data* adatfájlból készítjük el. Használja az alábbi parancsot:

`kes.exe build_index Academic.schema Academic.data Academic.index`

Azure-on kívül gyors prototípuskészítéséhez az [ `kes.exe build_index` ](CommandLine.md#build_index-command) létrehozhat kis helyi indexeket a legfeljebb 10 000 objektumot tartalmazó adatfájlokból. Nagyobb méretű adatfájloknál vagy futtatja a parancsot a [Windows VM-en az Azure-ban](../../../articles/virtual-machines/windows/quick-create-portal.md) belül vagy távoli fordítást végezhet az Azure-ban. További információkért lásd: skálázás be.

## <a name="use-an-xml-grammar-specification"></a>Az XML nyelvtan specifikáció használata

A nyelvtan megadja a természetes nyelvű lekérdezések halmazát, amelyeket a szolgáltatás értelmezni tud, valamint azt is, milyen szemantikai lekérdezési kifejezésekre történik ezek lefordítása. Ebben a példában az *academic.xml* fájlban megadott nyelvtant használjuk:

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="Academic.schema" name="academic"/>

  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>

    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>

    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>

      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>

        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>

        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>

  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="lt" name="year"/>
          </item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="gt" name="year"/>
          </item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```

A nyelvet megadó szintaxisról bővebben: [Nyelvtan formátuma](GrammarFormat.md).

## <a name="compile-the-grammar"></a>A nyelvtan lefordítása

Miután kész az XML nyelvi specifikáció, a [`kes.exe build_grammar`](CommandLine.md#build_grammar-command) használatával lefordítható bináris nyelvtanra. Ne feledje, ha a nyelvtan sémát importál, akkor a séma fájlnak ugyanazon az útvonalon kell lennie, ahol a nyelvtan XML is van. Ebben a példában az *Academic.grammar* bináris nyelvtan fájlt készítjük el az *Academic.xml* bemeneti XML-nyelvtan fájlból. Használja az alábbi parancsot:

`kes.exe build_grammar Academic.xml Academic.grammar`

## <a name="host-the-grammar-and-index-in-a-web-service"></a>A nyelvtan és az index tárolása webszolgáltatásban

Gyors prototípuskészítéshez a nyelvtan és az index elhelyezhető a helyi gép egy webszolgáltatásán a [`kes.exe host_service`](CommandLine.md#host_service-command) használatával. Utána a szolgáltatás [webes API-k](WebAPI.md) segítségével elérhető az adathelyesség és nyelvtan tervezés helyességének ellenőrzésére. Ebben a példában az *Academic.grammar* nyelvtani fájlnak és az *Academic.index* indexfájlnak az http://localhost:8000/ ad helyet. Használja az alábbi parancsot:

`kes.exe host_service Academic.grammar Academic.index --port 8000`

Ez elindítja a webszolgáltatás helyi példányát. A szolgáltatást interaktívan tesztelheti úgy, hogy böngészőben meglátogatja a `http::localhost:<port>` szolgáltatást. További információkért tekintse meg a tesztelési szolgáltatás.

A különböző [webes API-k](WebAPI.md) emellett közvetlenül is meghívhatók természetes nyelvi értelmezésre, lekérdezés kiegészítésre, strukturált lekérdezések kiértékelésére és hisztogram kiszámításra. A szolgáltatás leállításához írja be a `kes.exe host_service` parancssorba a „quit” szót, vagy nyomja le a Ctrl + C billentyűkombinációt. Néhány példa:

* [http://localhost:8000/interpret?query=papers by susan t dumais](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20dumais)
* [http://localhost:8000/interpret?query=papers by susan t d&complete=1](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20d&complete=1)
* [http://localhost:8000/evaluate?expr=Composite(Author.Name=='susan t dumais')&attributes=Title,Year,Author.Name,Author.Id&count=2](http://localhost:8000/evaluate?expr=Composite%28Author.Name==%27susan%20t%20dumais%27%29&attributes=Title,Year,Author.Name,Author.Id&count=2)
* [http://localhost:8000/calchistogram?expr=And(Composite(Author.Name=='susan t dumais'),Year>=2013)&attributes=Year,Keyword&count=4](http://localhost:8000/calchistogram?expr=And%28Composite%28Author.Name=='susan%20t%20dumais'%29,Year>=2013%29&attributes=Year,Keyword&count=4)

Azure-on kívül [ `kes.exe host_service` ](CommandLine.md#host_service-command) az indexek legfeljebb 10 000 objektumra korlátozódnak. Egyéb korlátot jelent a másodpercenkénti 10 API-kérés és az összesen 1000 kérés, ami után a folyamat automatikusan véget ér. Ezeknek a korlátozásoknak a megkerülésére futtassa a parancsot a [Windows VM-en az Azure-ban](../../../articles/virtual-machines/windows/quick-create-portal.md) környezeten belül, vagy a [`kes.exe deploy_service`](CommandLine.md#deploy_service-command) paranccsal tegye át Azure felhőszolgáltatásra. További információkért lásd: a szolgáltatás telepítéséhez.

## <a name="scale-up-to-host-larger-indices"></a>Válasszon nagyobb méretet nagyobb indexek tárolására

Ha a `kes.exe` futtatása Azure-n kívül történik, az index maximális mérete 10 000 objektum. Az Azure használatával készíthet és tárolhat nagyobb indexet. Regisztráljon az [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/). Visual Studio vagy MSDN előfizetőként [előfizetői kedvezményeket érvényesíthet](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Ezeket minden hónapban néhány Azure-kredittel jutalmazzuk.

Azure-fiók esetén a `kes.exe` hozzáférés engedélyezéséhez [töltse le az Azure közzétételi beállítások fájlt](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) az Azure portálról. Ha a rendszer kéri, jelentkezzen be a megfelelő Azure-fiókba. Mentse el a fájlt *AzurePublishSettings.xml* néven a munkakönyvtárba, ahonnan a `kes.exe` fut.

Kétféleképpen hozhat létre és üzemeltethet nagy indexeket. Az első, hogy a séma és adatfájlokat az Azure-beli Windows virtuális gépen készíti elő. Ezután futtassa `kes.exe build_index` helyileg hozhat létre az indexet a virtuális gép méretének korlátozása nélkül. Az eredményül kapott index on lehet üzemeltetni helyileg a virtuális gép használatával `kes.exe host_service` gyors prototípuskészítéséhez újra korlátozása nélkül. Részletes lépések leírása: [Azure-beli Virtuálisgép-oktatóanyag](../../../articles/virtual-machines/windows/quick-create-portal.md).

A második módszer a távoli Azure fordítás, a [ `kes.exe build_index` ](CommandLine.md#build_index-command) használatával és a `--remote` paraméter megadásával. Ez megadja az Azure virtuális gép méretét. A `--remote` paraméter megadása esetén a parancs ekkora méretű ideiglenes Azure virtuális gépet hoz létre. Ezután felépíti az indexet a virtuális gépen, feltölti az indexet a cél blobtárolóba, majd a végén törli a virtuális gépet. Az Azure-előfizetés számára a virtuális gép költsége az index felépítési idejére kerül felszámításra.

Ezzel a távoli Azure építés funkcióval a [ `kes.exe build_index` ](CommandLine.md#build_index-command) bármilyen környezetben futtatható. A távoli építés végzésekor a bemeneti séma és az adat argumentumok lehetnek helyi fájl elérési útvonalon vagy [Azure blobtároló](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) URL-címen. A kimeneti index argumentuma csak blobtároló URL lehet. Azure tárfiók létrehozása: [Tudnivalók az Azure tárfiókokról](../../storage/common/storage-create-storage-account.md). Fájlok blobtárolóba és vissza történő másolásához használja a [AzCopy](../../storage/common/storage-use-azcopy.md) segédprogramot.

Ebben a példában feltételezzük, hogy a következő blobtártároló konténer már létezik: http://&lt;*account*&gt;.blob.core.windows.net/&lt;*container*&gt;/. Tartalmazza az *Academic.schema* nevű sémát, a *Keywords.syn* hivatkozott szinonima fájlt, és az *Academic.full.data* teljes méretű adatfájlt. A teljes indexet távolról a következő paranccsal lehet létrehozni:

`kes.exe build_index http://<account>.blob.core.windows.net/<container>/Academic.schema http://<account>.blob.core.windows.net/<container>/Academic.full.data http://<account>.blob.core.windows.net/<container>/Academic.full.index --remote <vm_size>`

Vegye figyelembe, hogy a virtuális gép egy ideiglenes hozhat létre az index 5 – 10 percig is eltarthat. Gyors prototípuskészítéséhez a következőket teheti:
- Fejleszthet helyileg kisebb adathalmazzal bármilyen gépen.
- Manuálisan [hozzon létre egy Azure virtuális gépet](../../../articles/virtual-machines/windows/quick-create-portal.md), [csatlakozzon hozzá](../../../articles/virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) távoli asztalon keresztül, telepítse a [Knowledge Exploration Service SDK](https://www.microsoft.com/en-us/download/details.aspx?id=51488)-t és futtassa [ `kes.exe` ](CommandLine.md)-t a virtuális gépen.

A lapozás lassítja az építési folyamatot. A lapozás elkerülése érdekében használjon az index készítéshez használt adatfájl méretének háromszorosa méretű RAM-mal rendelkező virtuális gépet. Az index tárolt méreténél 1 GB RAM-mal több memóriával rendelkező virtuális gépet használjon. Elérhető virtuálisgép-méretek listája: [Virtuális gépek méretei](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

## <a name="deploy-the-service"></a>A szolgáltatás üzembe helyezése

Miután megvan a nyelvtan és az index, készen áll a szolgáltatás üzembe helyezése egy Azure felhőszolgáltatásban. Új Azure felhőszolgáltatás létrehozása: [Felhőszolgáltatás létrehozása és üzembe helyezése](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md). Ne adjon meg ezen a ponton még telepítőcsomagot.  

Miután létrehozta a felhőszolgáltatást, a [`kes.exe deploy_service`](CommandLine.md#deploy_service-command) használatával a telepítheti a szolgáltatást. Az Azure-felhőszolgáltatásban két üzembe helyezési pont van: üzemelési és előkészítési. Az élő felhasználói forgalmat fogadó szolgáltatást először a előkészítési pontra kell telepíteni. Várja meg, amíg a szolgáltatás elindul és inicializálja magát. Ezután elküldhet néhány kérést az üzembe helyezés ellenőrzésére és annak ellenőrzésére, hogy átmegy-e az alapteszteken.

[Cserélje](../../../articles/cloud-services/cloud-services-nodejs-stage-application.md) fel az előkészítési és üzemelési pontok tartalmát, hogy az élő forgalom most már az újonnan üzembe helyezett szolgáltatásra menjen. Ugyanezt az eljárást ismételje meg, amikor a szolgáltatást új adatokkal frissített verzióját telepíti. Ahogy minden más Azure-felhőszolgáltatásnál, igény szerint itt is használhatja az Azure portálon az [automatikus skálázás](../../../articles/cloud-services/cloud-services-how-to-scale-portal.md) konfigurálását.

Ebben a példában az *Academic* indexet egy már meglévő felhőszolgáltatás előkészítési pontján helyezzük üzembe a *<vm_size>* virtuális gépekkel. Használja az alábbi parancsot:

`kes.exe deploy_service http://<account>.blob.core.windows.net/<container>/Academic.grammar http://<account>.blob.core.windows.net/<container>/Academic.index <serviceName> <vm_size> --slot Staging`

Elérhető virtuálisgép-méretek listája: [Virtuális gépek méretei](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

A szolgáltatás üzembe helyezése után meghívhatja a különböző [webes API-kat](WebAPI.md) a természetes nyelvi értelmezésre, lekérdezés kiegészítésre, strukturált lekérdezések kiértékelésére és hisztogram kiszámításra.  

## <a name="test-the-service"></a>A szolgáltatás tesztelése

Az élő szolgáltatás hibakereséséhez keresse meg a gazdagépet egy webböngészőben. Egy helyi host_service szolgáltatással üzembe helyezett szolgáltatáshoz, talál `http://localhost:<port>/`.  Egy Azure-felhőszolgáltatásban deploy_service szolgáltatással üzembe helyezett, látogasson el `http://<serviceName>.cloudapp.net/`.

Ez az oldal statisztikai információkat tartalmaz az alapvető API-hívásokról, valamint a szolgáltatáson helyet foglaló nyelvtanról és indexről. Az oldal tartalmaz egy interaktív kereső felületet is, amely bemutatja a webes API-k használatát. A kérést írja a kereső mezőbe és megtekintheti az [értelmezési](interpretMethod.md), [kiértékelési](evaluateMethod.md) és [számítási hisztogram](calchistogramMethod.md) API-hívások eredményeit. Az oldal HTML forrása példa arra is, hogy a webes API-k alkalmazásba integrálásával hogyan lehet gazdag, interaktív keresési élményt biztosítani.


