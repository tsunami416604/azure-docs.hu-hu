---
title: Ismerkedés a Tudásbázis feltárása szolgáltatással |} Microsoft Docs
description: Tudásbázis feltárása szolgáltatás (KES) segítségével egy motor interaktív keresési élményt teremtsen academic kiadványok kognitív Microsoft-szolgáltatásokban.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 02dc9368eef02d6fa507335ef3171e923412acca
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347626"
---
<a name="getting-started"></a>
# <a name="get-started-with-the-knowledge-exploration-service"></a>A Tudásbázis feltárása szolgáltatás az első lépései
Ebben a bemutatóban feltárása szolgáltatás (KES) létrehozására használhatja a motor interaktív keresési élményt academic kiadványok esetében. A parancssori eszköz telepítése [ `kes.exe` ](CommandLine.md), és minden példa fájlokat a [Tudásbázis feltárása szolgáltatás SDK](https://www.microsoft.com/en-us/download/details.aspx?id=51488).

A academic kiadványok példa 1000 academic által írt cikkeket a Microsoft kutatói által közzétett mintát tartalmaz.  Minden egyes dokumentum címét, a kiadvány év, a szerzők és a kulcsszavak társítva. Minden egyes Szerző egy Azonosítót, nevét és a kiadvány időpontjában kapcsolatot jelöl. Lehet, hogy minden kulcsszó olyan készlete (például a "támogatási vektoros gép" kulcsszó lehet társítani a szinonima "svm") már társítva.

<a name="defining-schema"></a>
## <a name="define-the-schema"></a>A séma meghatározása
A séma leíró a tartományban lévő objektumok attribútum. Azt adja meg a nevét, és az adatok minden attribútum JSON formátumban. A következő példa: a fájl tartalma *Academic.schema*.

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

Itt adhat meg *cím*, *év*, és *kulcsszó* karakterlánc, egész szám, és a karakterlánc attribútummal, illetve. Szerzők Azonosítóját, nevét és kapcsolatot képviseli, mivel meghatározása *Szerző* három alárendelt attribútumokkal rendelkező összetett attribútumaként: *Author.Id*, *Author.Name*, és *Author.Affiliation*.

Alapértelmezés szerint attribútumok érhető el minden műveletet támogat az adattípus, beleértve a *egyenlő*, *starts_with*, és *is_between*. Szerző azonosító csak belső azonosítóként szolgál, mert az alapértelmezett, és adja meg *egyenlő* , az egyetlen indexelve a műveletet.

Az a *kulcsszó* attribútumot, hogy a szinonimák a szinonima fájl megadásával kanonikus kulcsszó értékekre *Keyword.syn* szereplő attribútumdefiníciót. Ez a fájl tartalmazza a kanonikus listája és érték párok szinonimát:

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

A sémadefiníciót kapcsolatos további információkért lásd: [séma formátum](SchemaFormat.md).

<a name="generating-data"></a>
## <a name="generate-data"></a>Adatok létrehozása
Az adatfájl indexelésre, minden sor egy papír attribútum értékének megadásával a kiadványok listáját ismertető [JSON formátumban](http://json.org/).  A következő példa: az adatok fájlból egy sor *Academic.data*, formázott olvashatóság érdekében:

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

Ezt a kódrészletet a adja meg a *cím* és *év* a papír egy JSON-karakterláncban és a szám, attribútum kulcsattribútumokkal. Több értékeket jelölik JSON-tömbök használata. Mivel *Szerző* összetett attribútum, minden egyes érték szerepel az alárendelt attribútumokból JSON-objektum használatával. A hiányzó értékeket, például a attribútumok *kulcsszó* ebben az esetben is ki kell zárni a JSON-megjelenítés.

Megkülönböztetni azokat a különböző által írt cikkeket valószínűségét, adja meg a relatív napló valószínűség a beépített *logprob* attribútum. A valószínűségi megadott *p* 0 és 1 között a napló valószínűséggel napló számítási (*p*), ahol a log() a természetes log függvény.

További információkért lásd: [adatformátum](DataFormat.md).

<a name="building-index"></a>
## <a name="build-a-compressed-binary-index"></a>A tömörített bináris index létrehozása
A következő sémafájl és az után adatfájlt, használatával hozhat létre az adatok objektumok tömörített bináris index [ `kes.exe build_index` ](CommandLine.md#build_index-command). Ebben a példában a indexfájlja build *Academic.index* a bemeneti sémát fájlból *Academic.schema* és adatfájl *Academic.data*. Használja az alábbi parancsot:

`kes.exe build_index Academic.schema Academic.data Academic.index`

A gyors prototípusának Azure,-on kívüli [ `kes.exe build_index` ](CommandLine.md#build_index-command) hozhat létre a kis indexek helyben, legfeljebb 10 000 objektumokat tartalmazó adatok fájlokból. Nagyobb adatfájlok, vagy futtathatja a parancsot belül egy [Windows Azure-ban](../../../articles/virtual-machines/windows/quick-create-portal.md), vagy hajtsa végre a távoli build az Azure-ban. További információkért lásd: [vertikális Felskálázásával](#scaling-up).

<a name="authoring-grammar"></a>
## <a name="use-an-xml-grammar-specification"></a>Az XML-nyelvtan specifikáció használata
A nyelvtan Megadja, hogy a szolgáltatás el tudja értelmezni, valamint hogyan a természetes nyelvű lekérdezéseket Szemantikus lekérdezési kifejezések fordításának természetes nyelvű lekérdezések. Ebben a példában a használja, a megadott nyelvtan *academic.xml*:

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

A nyelvtan specification szintaxissal kapcsolatos további információkért lásd: [Nyelvtanformátum](GrammarFormat.md).

<a name="compiling-grammar"></a>
## <a name="compile-the-grammar"></a>A nyelvtan összeállítása
Miután az XML-nyelvtan specifikáció, állíthat össze, a bináris nyelvtan be használatával [ `kes.exe build_grammar` ](CommandLine.md#build_grammar-command). Vegye figyelembe, hogy ha a nyelvtan importálja a séma, a következő sémafájl kell a nyelvtan XML az adott elérési úton található. Ebben a példában a bináris nyelvtan fájl build *Academic.grammar* a bemeneti XML-nyelvtan fájlból *Academic.xml*. Használja az alábbi parancsot:

`kes.exe build_grammar Academic.xml Academic.grammar`

<a name="hosting-index"></a>
## <a name="host-the-grammar-and-index-in-a-web-service"></a>A nyelvtan és egy webszolgáltatás-bővítmény indexe
A gyors prototípusának tárolhatja a nyelvtan és egy webszolgáltatás-bővítmény a helyi gépen index használatával [ `kes.exe host_service` ](CommandLine.md#host_service-command). A szolgáltatás keresztül érheti el [webes API-khoz](WebAPI.md) az adatok helyességét és a nyelvtan terv ellenőrzése. Ebben a példában a nyelvtanfájl gazdagép *Academic.grammar* és indexfájlja *Academic.index* : http://localhost:8000/. Használja az alábbi parancsot:

`kes.exe host_service Academic.grammar Academic.index --port 8000`

Ez elindít egy helyi példányát a webes szolgáltatás. Látogasson el a szolgáltatást interaktív módon tesztelheti `http::localhost:<port>` böngészővel. További információkért lásd: [szolgáltatás tesztelés](#testing-service).

Közvetlenül is hívása különböző [webes API-khoz](WebAPI.md) természetes nyelv értelmezése, a lekérdezés végrehajtási, a strukturált lekérdezés kiértékelése és a számítást, hisztogram teszteléséhez. A szolgáltatás leállításához írja be a következőt "lépjen ki a" azokat a `kes.exe host_service` parancssort, vagy a Ctrl + C. Néhány példa:

* [http://localhost:8000/interpret?query=papers susan t dumais által](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20dumais)
* [http://localhost:8000/interpret?query=papers susan t d & teljes = 1](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20d&complete=1)
* [http://localhost:8000/evaluate?expr=Composite(Author.Name=="susan t dumais") & attributes=Title,Year,Author.Name,Author.Id & count = 2](http://localhost:8000/evaluate?expr=Composite%28Author.Name==%27susan%20t%20dumais%27%29&attributes=Title,Year,Author.Name,Author.Id&count=2)
* [http://localhost:8000/calchistogram?expr=And(Composite(Author.Name=="susan t dumais"), év > = 2013) & attribútumok = év, kulcsszó & count = 4](http://localhost:8000/calchistogram?expr=And%28Composite%28Author.Name=='susan%20t%20dumais'%29,Year>=2013%29&attributes=Year,Keyword&count=4)

Azure-ban kívül [ `kes.exe host_service` ](CommandLine.md#host_service-command) indexek legfeljebb 10 000 objektumok korlátozódik. Más korlátok közé tartozik egy 10 kérelmek / másodperc API arányát és 1000 kérelmek összesen előtt automatikusan megszakítja a folyamatot. Ezek a korlátozások megkerülése, futtassa a parancsot belül egy [Windows Azure-ban](../../../articles/virtual-machines/windows/quick-create-portal.md), vagy az Azure-felhőszolgáltatás segítségével telepítheti a [ `kes.exe deploy_service` ](CommandLine.md#deploy_service-command) parancsot. További információkért lásd: [szolgáltatás telepítése](#deploying-service).

<a name="scaling-up"></a>
## <a name="scale-up-to-host-larger-indices"></a>A gazdagép nagyobb indexek növelheti
Amikor fut `kes.exe` kívül Azure, az index korlátozódik, 10 000 objektumra. Hozza létre, és nagyobb indexeket üzemeltetni az Azure használatával. Regisztráljon egy [ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/). Másik lehetőségként, ha a Visual Studio vagy MSDN előfizetett, is [aktiválhatja előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Minden hónap ezek kínál néhány Azure-krediteket.

Hogy `kes.exe` az Azure-fiók, hozzáféréssel [töltse le az Azure közzétételi beállítási fájlját](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) Azure-portálról. Ha a rendszer kéri, jelentkezzen be arra a kívánt Azure-fiókot. Mentse a fájlt *AzurePublishSettings.xml* a munkakönyvtárat, amelyről a `kes.exe` futtatja.

Két módon lehet létrehozni, és nagy indexek üzemeltetésére. Az egyik készítse elő a a Windows Azure-ban a séma- és fájlokat. Ezután futtassa [ `kes.exe build_index` ](#building-index) hozhat létre az index helyileg a virtuális Gépre, méret korlátozások nélkül. Az indexet az alábbiakon tárolható helyileg a virtuális gép használatával [ `kes.exe host_service` ](#hosting-service) a gyors prototípusának újra korlátozások nélkül. Részletes útmutató: a [Azure virtuális gép oktatóanyag](../../../articles/virtual-machines/windows/quick-create-portal.md).

Második módszer: használatával történő végrehajtásához a távoli Azure build [ `kes.exe build_index` ](CommandLine.md#build_index-command) rendelkező a `--remote` paraméter. Azt határozza meg az Azure virtuális gép méretét. Ha a `--remote` paraméter meg van adva, a parancs létrehoz egy ideiglenes Azure virtuális Gépet, a megadott méretű. Majd épít fel az index a virtuális Gépre, feltölti az indexet a cél blob-tároló, és törli a virtuális gép létrehozása után. Az Azure-előfizetés fel van töltve a költség, a virtuális gép közben az index részét.

A távoli Azure-létrehozási funkció lehetővé teszi, hogy [ `kes.exe build_index` ](CommandLine.md#build_index-command) minden környezetben futtatható. Távoli build hajtja végre, amikor a bemeneti séma és az adatok lehetnek helyi elérési utat vagy [Azure blob storage](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) URL-címeket. A kimeneti indexargumentum egy blob storage URL-címet kell lennie. Egy Azure storage-fiók létrehozásához lásd: [tudnivalók az Azure storage-fiókok](../../storage/common/storage-create-storage-account.md). A hatékonyan másolja a fájlokat, és a blob storage használata a [AzCopy](../../storage/common/storage-use-azcopy.md) segédprogramot.

Ebben a példában feltételezzük, hogy a következő blob-tároló már létezik: http://&lt;*fiók*&gt;.blob.core.windows.net/&lt;*tároló* &gt;/. A séma tartalmaz *Academic.schema*, a hivatkozott szinonimát fájl *Keywords.syn*, és a teljes körű adatfájl *Academic.full.data*. A teljes index távolról hozhat létre a következő paranccsal:

`kes.exe build_index http://<account>.blob.core.windows.net/<container>/Academic.schema http://<account>.blob.core.windows.net/<container>/Academic.full.data http://<account>.blob.core.windows.net/<container>/Academic.full.index --remote <vm_size>`

Vegye figyelembe, hogy egy virtuális Gépet az index létrehozása temporay kiépítéséhez 5 – 10 percig is eltarthat. A gyors prototípusának a következő műveletek végezhetők el:
- Kidolgozhat egy kisebb adatkészlet bármely gépen helyileg.
- Manuálisan [hozzon létre egy Azure virtuális gép](../../../articles/virtual-machines/windows/quick-create-portal.md), [kapcsolódni hozzá](../../../articles/virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) távoli asztalon keresztül telepítse a [Tudásbázis feltárása szolgáltatás SDK](https://www.microsoft.com/en-us/download/details.aspx?id=51488), és futtassa [ `kes.exe` ](CommandLine.md) át a virtuális Gépen belül.

Lapozófájl lelassítja az összeállítási folyamat. Lapozófájl elkerülése érdekében használja a virtuális gépek háromszor a RAM mennyisége a a bemeneti adatok méretét index építése. Egy virtuális Gépet használjon több RAM üzemeltetéséhez index mint 1 GB. Elérhető Virtuálisgép-méretek listáját lásd: [virtuális gépek méretei](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

<a name="deploying-service"></a>
## <a name="deploy-the-service"></a>A szolgáltatás üzembe helyezése
Miután egy nyelvtan és egy indexet, készen áll a szolgáltatás telepítése egy Azure felhőszolgáltatást. Hozzon létre egy új Azure felhőszolgáltatást, tekintse meg [létrehozásáról és központi telepítése egy felhőalapú szolgáltatás](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md). Ne adjon meg ezen a ponton a központi telepítési csomagot.  

A felhőalapú szolgáltatás létrehozása után használhat [ `kes.exe deploy_service` ](CommandLine.md#deploy_service-command) telepíti a szolgáltatást. Azure-felhőszolgáltatás van két üzembe helyezési: üzemi és átmeneti. Élő felhasználói forgalmat fogadó szolgáltatáshoz először telepítse az átmeneti tárolási helyre. Várjon, amíg a szolgáltatás elindításához és inicializálása sikertelen. Ezután ellenőrizheti a telepítését, és győződjön meg arról, hogy megfelel-e egyszerű tesztek néhány kérelmeket küldhet.

[A felcserélendő](../../../articles/cloud-services/cloud-services-nodejs-stage-application.md) az átmeneti tartalmát úgy, hogy az élő forgalom most irányul, az újonnan telepített szolgáltatás tárolóhely az éles webalkalmazásra. Ez az eljárás megismétlésével az új adatokat a szolgáltatás frissített verziójának telepítésekor. Hasonlóan az összes többi Azure felhőszolgáltatások, opcionálisan az Azure-portál konfigurálása [automatikus skálázás](../../../articles/cloud-services/cloud-services-how-to-scale-portal.md).

Ebben a példában a központilag telepíteni a *Academic* az átmeneti helyet egy meglévő felhőalapú szolgáltatást index *< vm_size >* virtuális gépeket. Használja az alábbi parancsot:

`kes.exe deploy_service http://<account>.blob.core.windows.net/<container>/Academic.grammar http://<account>.blob.core.windows.net/<container>/Academic.index <serviceName> <vm_size> --slot Staging`

Elérhető Virtuálisgép-méretek listáját lásd: [virtuális gépek méretei](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

A szolgáltatás telepítése után hívása a különböző [webes API-khoz](WebAPI.md) természetes nyelv értelmezése, a lekérdezés végrehajtási, a strukturált lekérdezés kiértékelése és a számítást, hisztogram teszteléséhez.  

<a name="testing-service"></a>
## <a name="test-the-service"></a>A szolgáltatás tesztelése
Hibakeresési élő szolgáltatásként, keresse meg a gazdagép gépnek egy webböngészőből. A helyi keresztül telepített [host_service](#hosting-service), látogasson el `http://localhost:<port>/`.  Az Azure cloud service keresztül telepített [deploy_service](#deploying-service), látogasson el `http://<serviceName>.cloudapp.net/`.

Ezen a lapon API hívása alapszintű statisztikákat, valamint a nyelvtan és index: Ez a szolgáltatás üzemeltetett kapcsolatos információkra mutató hivatkozást tartalmaz. Ezen a lapon is tartalmaz, amely bemutatja, hogy a webes API-k interaktív keresési illesztőfelület. Adja meg a lekérdezés eredményeinek megtekintéséhez a keresőmezőbe a [értelmezhetők](interpretMethod.md), [kiértékelése](evaluateMethod.md), és [calchistogram](calchistogramMethod.md) API-hívásokat. Példa bemutatja, hogyan integrálható a webes API-knak az alkalmazásba, gazdag, interaktív keresési környezetet is szolgál az alapul szolgáló HTML-forrásának ezen a lapon.


