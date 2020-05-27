---
title: A Word konvertálása vektorra
titleSuffix: Azure Machine Learning
description: Ebből a témakörből megtudhatja, hogyan hozhatja ki a három megadott Word2Vec-modellt a szókincs és a hozzá tartozó Word-beágyazások szövegből való kinyeréséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: e0e796b75690bcacc6be8ef29b8b490c7faa40af
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853770"
---
# <a name="convert-word-to-vector"></a>A Word konvertálása vektorra

Ez a cikk azt ismerteti, hogyan használható a **Word konvertálása vektoros** modul Azure Machine learning Designerben (előzetes verzió), hogy különböző különböző Word2Vec-modelleket (Word2Vec, FastText, kesztyűt előre betanított modellt) alkalmazzon a bemenetként megadott szövegre, és létrehozza a szavakat beágyazásokat tartalmazó szókincset.

Ez a modul a Gensim könyvtárat használja. A Gensim kapcsolatos további információkért tekintse meg az oktatóanyagokat és az algoritmusok magyarázatát tartalmazó [hivatalos webhelyét](https://radimrehurek.com/gensim/apiref.html) .

### <a name="more-about-convert-word-to-vector"></a>További információ a Word konvertálása vektorra

Általánosságban elmondható, hogy a Word a vektorra vagy a Word vektorizációt-re való konvertálása természetes nyelvi feldolgozási folyamat, amely nyelvi modelleket vagy technikákat használ a szavak vektoros területre való leképezésére, azaz az egyes szavak valós számok vektorával való ábrázolására, és eközben lehetővé teszi, hogy a hasonló jelentésekkel rendelkező szavak hasonló ábrázolással rendelkezzenek.

A Word-beágyazások kezdeti bemenetként használhatók az NLP alsóbb rétegbeli feladataihoz, mint például a szöveg besorolása, a hangulat elemzése stb.

Ebben a modulban három széles körben használt módszert ismertetünk, többek között két online képzési modellt, Word2Vec és FastText, valamint egy előre betanított modellt, amely a kesztyű-wiki-gigaword-100. Az online képzési modelleket betanítjuk a bemeneti adataira, míg az előre betanított modellek egy nagyobb szöveges Corpus (például a Wikipedia, a Google Hírek) esetében általában körülbelül 100 000 000 000 szót tartalmaznak, a Word-beágyazás során pedig állandó marad a Word vektorizációt. Az előre betanított Word-modellek olyan előnyöket biztosítanak, mint például a csökkentett képzési idő, a jobb Word-vektorok kódolása és a teljes teljesítmény.

+ A Word2Vec az egyik legnépszerűbb módszer a sekély neurális hálózatot használó Word-beágyazások megismerésére, az elméletet a PDF-letöltésként elérhetővé teszi a következő dokumentumban: a [vektoros térben, a Mikolov, a Tomas és az et al](https://arxiv.org/pdf/1301.3781.pdf). A modul implementációja a [Word2Vec gensim könyvtárán](https://radimrehurek.com/gensim/models/word2vec.html)alapul.

+ A FastText elméletét a PDF-letöltésként elérhető dokumentum ismerteti: a [Word-vektorok gazdagítása a Word Information, a Bojanowski, a Piotr és az mtsai](https://arxiv.org/pdf/1607.04606.pdf). A modul implementációja a [FastText gensim könyvtárán](https://radimrehurek.com/gensim/models/fasttext.html)alapul.

+ Kesztyűt előre betanított modell: a kesztyű-wiki-gigaword-100, az előre betanított vektorok gyűjteménye, amely a wikipedia Text corpuson alapul, amely 5.6 B jogkivonatokat és 400K nem megfelelő szókincset tartalmaz, a PDF elérhető: [kesztyű: globális vektorok a Word ábrázolásához](https://nlp.stanford.edu/pubs/glove.pdf).

## <a name="how-to-configure-convert-word-to-vector"></a>A Word átalakítása vektorra történő konfigurálásának módja

Ehhez a modulhoz olyan adatkészlet szükséges, amely egy szöveges oszlopot tartalmaz, az előfeldolgozott szöveg jobb.

1. Adja hozzá a **Word konvertálása vektoros** modult a folyamathoz.

2. A modul bemenete adjon meg egy vagy több szöveges oszlopot tartalmazó adatkészletet.

3. A **cél oszlop**esetében csak egy olyan oszlopot válasszon, amely a feldolgozandó szöveget tartalmazza.

    Általánosságban elmondható, hogy ez a modul szövegből származó szókincset hoz létre, a különböző oszlopok tartalma eltér egymástól, így a modul csak egyetlen cél oszlopot fogad el.

4. A **Word2Vec stratégia**esetében válassza a következőt:, `GloVe pretrained English Model` `Gensim Word2Vec` és `Gensim FastText` .

5. Ha a **Word2Vec stratégia** `Gensim Word2Vec` vagy `Gensim FastText` :

    + **Word2Vec-betanítási algoritmus**. Válassza a `Skip_gram` és a lehetőséget `CBOW` . A különbség az eredeti [dokumentumban](https://arxiv.org/pdf/1301.3781.pdf)van bevezetve.

        Az alapértelmezett metódus: `Skip_gram` .

    + **A Word beágyazásának hossza**. A Word-vektorok dimenzióját meghatározása. Megfelel a `size` paraméternek a gensim.

        Az alapértelmezett embedding_size a 100.

    + **Környezet ablakának mérete** Az előre jelzett szó és az aktuális szó közötti maximális távolságot határozza meg. Megfelel a `window` paraméternek a gensim.

        Az ablak alapértelmezett mérete 5.

    + **A korszakok száma**. Adja meg az időpontok számát (iteráció) a corpuson. Megfelel a `iter` paraméternek a gensim.

        Az alapértelmezett időkorszakok száma 5.

6. A **szókincs maximális méretéhez**a generált szókincsben szereplő szavak maximális számát határozza meg.

    Ha a fentieknél több egyedi szó van, akkor a nem gyakoriak.

    Az alapértelmezett szókincs mérete 10000.

7. A szavak **minimális száma**beállításnál adjon meg egy minimális számú szót, ami lehetővé teszi, hogy a modul figyelmen kívül hagyja az összes szót, amelynek a gyakorisága ennél az értéknél kisebb.

    Az alapértelmezett érték 5.

8. A folyamat elküldése.

## <a name="examples"></a>Példák

A modul egy kimenettel rendelkezik:

+ **Beágyazást tartalmazó szókincs**: tartalmazza a generált szókincset, valamint az egyes szavak beágyazását, az egyik dimenzió egy oszlopot foglal el.

### <a name="result-examples"></a>Példák az eredményekre

Az alábbi példa azt szemlélteti, hogyan működik a **Word konvertálása vektoros** modulba, a következő példa a modult alkalmazza alapértelmezett beállításokkal a Azure Machine learning (előzetes verzió) szolgáltatásban megadott előfeldolgozott Wikipedia SP 500-adatkészletre.

#### <a name="source-dataset"></a>Forrásadatkészlet

Az adatkészlet tartalmaz egy category oszlopot, valamint a wikipedia-ból beolvasott teljes szöveget is. Ez a táblázat csak néhány reprezentatív példát mutat be.

|szöveg|
|----------|
|NASDAQ 100 Component s p 500 Component Foundation alapító Location City Apple Campus 1 végtelen Loop Street infinite loop Cupertino California Cupertino Kalifornia helyszín ország Egyesült Államok...|
|br NASDAQ 100 NASDAQ 100 összetevő br s p 500 s p 500 Component Industry számítógép Software Foundation br alapítója, Charles geschke br John Warnock hely Adobe Systems...|
|s p 500 s p 500 összetevő-iparág autóipari elődje, General Motors Corporation 1908 2009 utód...|
|s p 500 s p 500 összetevő iparági konglomerátum társasági konglomerátum Foundation alapító Location City Fairfield Connecticut Fairfield Connecticut Location Country USA területén...|
|br s p 500 s p 500 Component Foundation 1903 alapító William s Harley br Arthur Davidson Harley Davidson alapító Arthur Davidson br Walter Davidson br William a Davidson hely...|

#### <a name="output-vocabulary-with-embeddings"></a>Kimeneti Szószedet beágyazásokkal

A következő táblázat tartalmazza a modul kimenetét a wikipedia SP 500 adatkészlet bemenetként való elkészítésekor. A bal szélső oszlop jelzi a szókincset, a beágyazási vektort az ugyanabban a sorban található fennmaradó oszlopok értékei jelölik.

|Szókincs|A Dim 0 beágyazása|A Dim 1 beágyazása|A Dim 2 beágyazása|A Dim 3 beágyazása|A 4. Dim beágyazás|A Dim 5 beágyazása|...|A Dim 99 beágyazása|
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|NASDAQ|– 0,375865|0,609234|0,812797|– 0,002236|0,319071|– 0,591986|...|0,364276
|-összetevő|0,081302|0,40001|0,121803|0,108181|0,043651|– 0,091452|...|0,636587
|s|– 0,34355|– 0,037092|– 0,012167|0,151542|0,601019|0,084501|...|0,149419
|P|– 0,133407|0,073244|0,170396|0,326706|0,213463|– 0,700355|...|0,530901
Alapítvány|– 0,166819|0,10883|– 0,07933|– 0,073753|0,262137|0,045725|...|0,27487
alapítója|– 0,297408|0,493067|0,316709|– 0,031651|0,455416|– 0,284208|...|0,22798
location|– 0,375213|0,461229|0,310698|0,213465|0,200092|0,314288|...|0,14228
city|– 0,460828|0,505516|– 0,074294|– 0,00639|0,116545|0,494368|...|– 0,2403
Apple|0,05779|0,672657|0,597267|– 0,898889|0,099901|0,11833|...|0,4636
Campus|– 0,281835|0,29312|0,106966|– 0,031385|0,100777|– 0,061452|...|0,05978
végtelen|– 0,263074|0,245753|0,07058|– 0,164666|0,162857|– 0,027345|...|– 0,0525
hurok|– 0,391421|0,52366|0,141503|– 0,105423|0,084503|– 0,018424|...|– 0,0521

Ebben a példában az alapértelmezett értéket használtuk `Gensim Word2Vec` a Word2Vec- **stratégia**, a **betanítási algoritmus** , a `Skip-gram` **word beágyazásának hossza** 100, ezért 100 beágyazási oszlopokkal rendelkezünk.

## <a name="technical-notes"></a>Technikai megjegyzések

Ez a szakasz tippeket és válaszokat tartalmaz a gyakori kérdésekre.

+ Az online és az előre betanított modell közötti különbség

    A **Word konvertálása vektoros modulba**három különböző stratégiát, két online képzési modellt és egy előre betanított modellt biztosítunk. Az online képzési modell a bemeneti adatkészletet használja a betanítási adatokhoz, szókincset és Word-vektorokat hoz létre a betanítás során, míg az előre betanított modellt már a sokkal nagyobb szöveg-Corpus, például a wikipedia vagy a Twitter szövege is betanítja, így az előre betanított modell tulajdonképpen egy gyűjtemény (Word, beágyazási) pár  

    Ha a kesztyű előre betanított modelljét Word vektorizációt-stratégiaként választják meg, akkor az összefoglalja a bemeneti adatkészletből származó szókincset, és az előre betanított modell minden szavához létrehoz egy beágyazási vektort, online képzés nélkül, az előre betanított modell használata időt takaríthat meg, és jobb teljesítményt nyújt, különösen akkor, ha a bemeneti adatkészlet mérete viszonylag kicsi

+ Beágyazási méret

    Általánosságban elmondható, hogy a Word beágyazásának hossza néhány száz (például 100, 200, 300), hogy jó teljesítményt érjen el, mivel a kis beágyazási méret kis vektoros területet jelent, ami a Word beágyazási ütközéseket okozhat.  

    Az előre ellátott modellek esetében a Word-beágyazások hossza javítva lett, ebben a megvalósításban a kesztyűk beágyazásának mérete – wiki-gigaword-100, 100.


## <a name="next-steps"></a>További lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 

A tervezői (előzetes) modulokra vonatkozó hibák listáját a [Machine learning hibakódok](designer-error-codes.md)című részben tekintheti meg.
