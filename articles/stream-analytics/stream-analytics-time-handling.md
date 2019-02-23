---
title: Idő kezelése az Azure Stream Analytics ismertetése
description: Ismerje meg időt kezelése az Azure Stream Analytics szolgáltatásban
author: jasonwhowell
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/05/2018
ms.openlocfilehash: 91193cc328c02f5ba4f5eb18e0bf853f9d91313a
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732262"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Idő kezelése az Azure Stream Analytics ismertetése

Ebben a cikkben bemutatjuk, hogyan teheti tervezési döntések gyakorlati idő kezelése az Azure Stream Analytics szolgáltatásban problémák megoldásához. Kezelési tervezési döntések szorosan kapcsolódó tényezők eseményrendezési időt. További információkért tekintse meg a kapcsolódó cikk: [Az Azure Stream Analytics esemény rendelés szempontok](stream-analytics-out-of-order-and-late-events.md).

## <a name="background-time-concepts"></a>Háttérben futó idő fogalmak

A hozzászólás kerethez jobb határozzon meg néhány olyan fogalmat, háttér:

- **Esemény időpontja**: Az az időpont, amikor az eredeti esemény történt. Például egy áthelyezése esetén a highway az autó közelíti egy nem ingyenes érzékelőadatainak.

- **Feldolgozási idő**: Az az időpont, amikor az esemény eléri a feldolgozási rendszer, és figyelhető meg. Például amikor egy nem ingyenes érzékelőadatainak érzékelő látja, a car és a számítógép rendszer veszi fel az adatokat egy kis ideig.

- **Vízjel**: Egy esemény, amely azt jelzi, hogy milyen pont akár az összes esemény idő jelölő belépő az adatfolyam-feldolgozó lett. A vízjelek, hogy a rendszer jelzi a fürtjét az események törlése folyamatban. Streamek jellege a beérkező eseményadatokon soha nem leállítja, így a vízjelek jelzik, hogy egy bizonyos ponton a Stream.

   A vízjel azért lényeges. A vízjelek lehetővé teszik a Stream Analytics használatával határozható meg, hogy a rendszer hozhatnak, teljes körű, helyes-e, és megismételhető eredményeket, amelyeket nem kell kell visszavonni. A feldolgozás garantált úgy, hogy a kiszámítható és megismételhető hajtható végre. Például, ha egy újraszámolás kell tenni a hibakezelési feltétel, vízjelek lesznek biztonságos kezdő és záró pontot.

Tyler Akidau blogbejegyzés megtekintése, a témával kapcsolatos további források [Streamelési 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) és [Streamelési 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102).

## <a name="choosing-the-best-starting-time"></a>A legjobb kezdési idő kiválasztása

Stream Analytics biztosít a felhasználóknak a két választási lehetőség a kiadási esemény időpontja:

1. **Érkezés időpontja**  

   Érkezési ideje van hozzárendelve a bemeneti forrásnál, amikor az esemény eléri a forrás. Érkezési ideje használatával érheti a **EventEnqueuedUtcTime** tulajdonság az Event Hubs-bemenet, **IoTHub.EnqueuedTime** tulajdonság az IoT Hub és a használatával a **BlobProperties.LastModified**  blob bemeneti tulajdonság.

   Érkezési ideje használja az alapértelmezett viselkedés, és használhatók a legjobban, az adatarchiválás a forgatókönyvek, ahol nincs historikus logikai van szükség.

2. **Alkalmazás ideje** (más néven esemény időpontja)

   Kérelem ideje van hozzárendelve, amikor az esemény akkor jön létre, és ez az esemény hasznos része. Alkalmazás ideje eseményeinek feldolgozására, használja a **időbélyeg szerint** záradékot a select-lekérdezésben. Ha a **időbélyeg szerint** záradék hiányzik, az események feldolgozása érkezési ideje szerint.

   Fontos használja az adattartalomban szereplő időbélyeget, ha a historikus logikai van szó. Ezzel a módszerrel a forrásrendszerben, vagy a hálózati késések számítani lehet.

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Hogyan idő menet közben, az Azure Stream Analytics szolgáltatásban

Alkalmazás ideje használatakor az idő metódushívásainak a bejövő eseményeket alapul. A streamfeldolgozó rendszer tudni, hogy ha nem tartoznak események, vagy ha az események késnek számára nehézséget jelent. Emiatt az Azure Stream Analytics állít elő, heurisztikus vízjelek minden egyes bemeneti partíció a következő módon:

1. Minden bejövő esemény történik, a vízjel a legnagyobb úgy találtuk, amennyiben a soron kívüli out tolerancia ablakméret mínusz esemény időpontja.

2. Nem található olyan bejövő esemény, amikor a vízjel az aktuális becsült érkezési ideje (az eltelt idő a virtuális gép a legutóbbi alkalommal bemeneti esemény látható, valamint az adott bemeneti esemény érkezési ideje az események feldolgozása a háttérben) csökkentve a késedelmes érkezési tolerancia ablak.

   A érkezési ideje csak becsülhető, mivel a valódi érkezési ideje jön létre a bemeneti eseményközvetítőből, például az Event Hubsba, a, és nem az Azure Stream Analytics virtuális Géphez az események feldolgozását.

A Tervező vízjelek generálása mellett két további célokra szolgál:

1. A rendszer eredményeket időben vagy anélkül bejövő eseményeket hoz létre.

   Hogyan időben szeretnének a kimeneti eredmények megtekintéséhez felett van. Az Azure Portalon az a **események rendezése** lapon konfigurálhatja a Stream Analytics-feladat a **üzemen kívüli események** beállítás. Ez a beállítás konfigurálásakor érdemes kompromisszumot kötni a teljesítési idejét, az eseménystream out soron kívüli események tűréssel.

   A késedelmes érkezési tolerancia ablak fontos, hogy tartsa generálása vízjelét, még akkor is, a bejövő események hiányának. Időnként előfordulhatnak egy ideig nem bejövő események fontossá, amikor egy esemény bemeneti stream nem ritka. A bemeneti eseményt átvitelszervező több partíció használata problémát van fokozottabb.

   Streamelési adatok feldolgozási rendszerek egy késői érkezési tolerancia ablak nélkül gyengülhet a késleltetett kimenetek amikor bemenetek ritka és több partíciót.

2. A rendszer viselkedését nem lehet ismételhető. Ismételhetőség a streamelési adatok feldolgozása rendszer fontos tulajdonságot.

   A vízjel érkezési ideje és kérelem ideje van származtatva. Mindkettő az esemény-átvitelszervező megőrzött, és így ismételhető. Abban az esetben a érkezési ideje van becsülni az események, az Azure Stream Analytics naplók a becsült érkezési ideje a hiba utáni helyreállítás céljából ismétlés során ismételhetőség hiányában.

Figyelje meg, hogy ha használni kívánja **érkezési ideje** az esemény időpontját, nincs szükség a soron kívüli out hibatűrést és késői érkezési tolerancia konfigurálása. Mivel **érkezési ideje** garantáltan kell monoton növekvő a bemeneti eseményt átvitelszervező, az Azure Stream Analytics egyszerűen nem veszik figyelembe a konfigurációkat.

## <a name="late-arriving-events"></a>Beérkező későn érkező események

Definíció végéig érkezési tolerancia ablak, minden bejövő-esemény esetén az Azure Stream Analytics hasonlítja össze a **esemény időpontja** együtt a **érkezési ideje**; Ha az esemény időpontját a tolerancia időszakon kívül, a rendszer, vagy dobja el az eseményt, vagy állítsa be az esemény időpontját a tűréshatáron belül kell konfigurálhatja.

Vegye figyelembe, hogy után vízjelek jönnek létre, a szolgáltatás is potenciálisan események fogadása az alacsonyabb, mint a vízjel esemény időpontja. Beállíthatja, hogy a szolgáltatás vagy **drop** események, vagy **beállítása** az esemény időpontját, a küszöbértékek.

A beállítást, az eseményt a részeként **System.Timestamp** az új értékre van állítva, de a **esemény időpontja** magát a mező nem módosul. Ez a beállítás csak akkor, ha a egy esemény **System.Timestamp** kell megegyeznie az eseményt idő mező értékét, és a létrehozandó váratlan eredményekhez vezethet.

## <a name="handling-time-variation-with-substreams"></a>A részadatfolyamok idő variation kezelése

A heurisztikus vízjel generációs mechanizmus az itt leírtak szerint ahol idő többnyire szinkronizálva legyenek a különböző esemény feladók az esetek többségében a jól működik. Azonban a valós életben különösen sok IoT-forgatókönyveket, a rendszer van kis szabályozhatja az esemény feladói az óra. Az esemény feladók például lehet a mezőben eszközök számos hardver- és a különböző verzióin.

Egy bemeneti partíció globális eseményekhez vízjel helyett Stream Analytics rendelkezik egy másik mechanizmus segítségével részadatfolyamok nevű. Használhat részadatfolyamok a feladat, ha egy feladat lekérdezést, amely a [ **TIMESTAMP BY** ](/stream-analytics-query/timestamp-by-azure-stream-analytics) záradék és a kulcsszó **keresztül**. A részstreamek kijelölni, adjon meg egy kulcsoszlop neve után a **keresztül** kulcsszó, mint például egy `deviceid`, így a rendszer akkor alkalmazza a szabályzatokat idő, adott oszlop szerint. Minden egyes részstreamek lekérdezi a saját független vízjelet. Ez a mechanizmus hasznos időben kimeneti generálása, ha nagy a foglalkozó rendszerórája dönt engedélyezése és a hálózati késések esemény feladók között.

Részadatfolyamok olyan egyedi megoldást az Azure Stream Analytics által biztosított, és más streamelési adatokat feldolgozó rendszerben nem érhető el. Stream Analytics bejövő eseményeket részadatfolyamok használatakor a késedelmes érkezési tolerancia ablak vonatkozik. Az alapértelmezett beállítás (5 másodperces), valószínűleg eltérő időbélyeggel rendelkező eszközök túl kicsi. Azt javasoljuk, hogy kezdje 5 perc, és hajtsa végre az esetlegesen az eszköz óra torzulása minta alapján.

## <a name="early-arriving-events"></a>Korai beérkező események

Talán már észrevette késői érkezési tolerancia ablak ellentéte hasonló öröklési korai érkezési ablakban nevű másik fogalom. Ebben az ablakban 5 perc van rögzítve, és a egy késői megérkezése más célt szolgál.

Az Azure Stream Analytics biztosítja, hogy mindig teljes eredmények állít elő, mert csak meg **feladat kezdési ideje** kimeneti először a feladat nem a bemeneti időpont szerint. A feladat kezdési időpontját kötelező megadni, úgy, hogy az ablakban közepéről származó nem csak a teljes ablak feldolgozását.

Stream Analytics a lekérdezés specifikációjában majd származik a kezdési időpontot. Viszont bemeneti eseményközvetítőből csak indexelt érkezési idő szerint, mert a rendszer rendelkezik lefordítani a kezdési időt esemény érkezési ideje. A rendszer ettől a ponttól a bemeneti eseményközvetítőből megkezdheti a feldolgozási események. A korai beérkező ablak korlátot, az a fordítás rendkívül egyszerű. A indítása folyamatban van az 5 perces korai beérkező ablak mínusz esemény időpontja. Ehhez a számításhoz, azt is jelenti, hogy a rendszer elveti láthatók kellene esemény időpontja nagyobb, mint érkezési ideje 5 perc összes eseményt.

A fogalom annak biztosítása érdekében, a feldolgozási megismételhető, függetlenül attól, hogy az először a kimenetre szolgál. Olyan mechanizmus nélkül nem lenne garantálja az ismételhetőség, lehetséges, számos más streamelési rendszer jogcím tesznek.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Az idő az adott eseményrendezési hatásai

Több Stream Analytics-feladatok rendelkezik **események rendezése** beállítások. Két konfigurálható az Azure Portalon: a **üzemen kívüli események** beállítás (out soron kívüli tűrés), és a **későn érkező események** (késői érkezési tűrés) beállítást. A **korai érkezési** tolerancia rögzített, és nem módosítható. Stream Analytics az ezen idő szabályzatok segítségével erős garanciákat biztosít. Ezek a beállítások azonban vannak bizonyos időnként nem várt vonatkozásai:

1. Túl korai események véletlenül küldésekor.

   Korai események kell nem lehet használt kimeneti adattípus általában. Korai események küldött a kimenetbe küldő óra fut-e túl gyorsan, azonban ha lehetőség. Korai beérkező összes eseményt a rendszer elveti, ezért nem jelenik meg valamelyiket, a kimenetből.

2. Régi események küldése az Event Hubs az Azure Stream Analytics általi feldolgozásának.

   Régi események úgy tűnhet, ártalmatlan először az alkalmazás a késedelmes érkezési tűrés miatt, amíg a régi események nem vethetőek el. Ha az események túl régi az **System.Timestamp** érték eseményfogadás során módosul. Ez a viselkedés miatt az Azure Stream Analytics jelenleg alkalmasabb közel valós idejű Eseményfeldolgozási forgatókönyvekre, korábbi eseményfeldolgozás forgatókönyvek helyett. Beállíthatja a **későn érkező események** időt a legnagyobb lehetséges értéket (20 nap) ezt a viselkedést bizonyos esetekben megkerüléséhez.

3. Kimenetek úgy tűnik, hogy késni.

   A számított időben jön létre az első küszöbérték: a **események maximális idő** a rendszer az eddigi megállapítása szerint a soron kívüli out tolerancia ablakméret mínusz. Alapértelmezés szerint a soron kívüli out tolerancia 0 (00 perc és 00 másodperc) van konfigurálva. Ha magasabb, nem nulla értékű idő értékre állítva, a folyamatos átviteli feladat első kimeneti-e a késleltetett ezt az értéket idő szerint (vagy újabb) vízjel először kiszámított miatt.

4. Bemenetek olyan ritka.

   Nincs bemenet nem egy adott partíció található, amikor a vízjel idő kiszámítása a **érkezési ideje** csökkentve a késedelmes érkezési tolerancia ablakot. Ennek eredményeképpen ha a bemeneti események alkalmi és a ritka, a kimenet az úgy, hogy mennyi ideig késleltethető. Az alapértelmezett **későn érkező események** értéke 5 másodperc. Némi késéssel láthatja, ha a bemeneti események egy küld egyszerre, például számíthat. Az késleltetések rosszabb, beszerezheti a beállításakor **későn érkező események** ablak nagy értéket.

5. **System.Timestamp** értéke eltér az idő a **esemény időpontja** mező.

   Korábban leírt, a rendszer a soron kívüli out tolerancia vagy késői érkezési tolerancia windows esemény időpontja állítja be. A **System.Timestamp** értéket az esemény módosul, de nem a **esemény időpontja** mező.

## <a name="metrics-to-observe"></a>Figyelje meg a metrikák

Figyelheti, hogy az idő tolerancia hatások keresztül Eseményrendezési számos [Stream Analytics-feladat metrikák](stream-analytics-monitoring.md). A következő metrikák kapcsolódnak:

|Metrika  | Leírás  |
|---------|---------|
| **Kimenő soron kívüli események** | Azt jelzi, hogy a kapott lettek eldobva, vagy egy módosított időbélyeg megadott, üzemen kívüli események száma. Ez a metrika közvetlen hatással van a konfigurációja a **üzemen kívüli események** beállítást a **események rendezése** lap a feladathoz az Azure Portalon. |
| **Későn érkező bemeneti események** | Azt jelzi, hogy a forrás-későn érkező események száma. Ez a metrika tartalmaz eseményeket, amelyek el lett dobva vagy időbélyegzőik voltak lett igazítva. Ez a metrika közvetlen hatással van a konfigurációja a **későn érkező események** beállítása a **események rendezése** lap a feladathoz az Azure Portalon. |
| **Korai bemeneti események** | Azt jelzi, hogy a forrás-, vagy el lett dobva, vagy, ha azok túl korán 5 percet időbélyegzőik van igazítva korai érkező események száma. |
| **Vízjel késleltetés** | A streamelési adatok feldolgozási feladat a késleltetést jelzi. További információ a következő szakaszban.|

## <a name="watermark-delay-details"></a>Vízjel késleltetés részletei

A **késleltetés küszöbérték** metrika számítja ki, hogy a valós idő mínusz a legnagyobb vízjel eddig észlelte a feldolgozási csomópont. További információkért lásd: a [késleltetés blogbejegyzés küszöbérték](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/).

Metrika értéke nagyobb, mint 0, a normál működés során számos oka lehet:

1. A streamelési folyamat rejlő feldolgozás késéssel. Ez a késleltetés általában névleges.

2. A soron kívüli out tolerancia ablak késleltetés, vezetett be, mert a vízjel csökkenti a tolerancia ablak méretének.

3. A késedelmes érkezési ablak késedelem vezetett be, mert vízjel mérete csökken a tolerancia ablakot.

4. Mért döntés-e a feldolgozási csomópont a metrika létrehozásához.

Nincsenek korlátozott más erőforrások, amelyek miatt a streamelési folyamat lelassítja a száma. A vízjel késleltetés metrika miatt is növekménye:

1. Nincs elég a Stream Analytics a bemeneti események mennyiségének kezeléséhez szükséges erőforrások. Erőforrások vertikális felskálázásához lásd [megismerés és módosítsa a folyamatos átviteli egységek](stream-analytics-streaming-unit-consumption.md).

2. Nincs elegendő átviteli sebesség a bemeneti eseményszervezőként, így azok szabályozott belül. Lehetséges megoldásokat talál [automatikus vertikális felskálázás az Azure Event Hubs átviteli egységek](../event-hubs/event-hubs-auto-inflate.md).

3. Kimeneti fogadóinak nincs kiépítve elegendő kapacitással, így azok szabályozott. A lehetséges megoldások széles körben megoldástól a íz kimeneti szolgáltatás használt.

## <a name="output-event-frequency"></a>Kimeneti esemény gyakorisága

Az Azure Stream Analytics vízjel folyamat az egyetlen eseményindító kimeneti eseményeket létrehozásához használja. Mivel a vízjel a bemeneti adatok származtatott, megismételhető hiba utáni helyreállítás során és a felhasználó által kezdeményezett újrafeldolgozása.

Használata esetén [ablakos összesítéseket](stream-analytics-window-functions.md), a szolgáltatás csak a kimeneteket a windows végén hoz létre. Bizonyos esetekben felhasználók előfordulhat, hogy szeretné látni a Windows generált részleges összesítések. Részleges összesítések nem támogatottak jelenleg az Azure Stream Analytics szolgáltatásban.

Más streamelési megoldások, a kimeneti eseményekben a sikerült materializált külső körülmények között függően különböző eseményindító időpontokban. Az egyes megoldások, hogy a kimeneti eseményekben a megadott időtartomány sikerült jöjjön létre többször is lehetőség. A bemeneti értékek finomított, mert az összesített eredmények pontos válnak. Események időbeli sikerült speculated első és a módosított. Például ha egy bizonyos eszköz offline állapotban van, a hálózatról, becsült értéket az rendszerek által felhasználható. Később az ugyanazon az eszközön online állapotba kerül a hálózathoz. Majd a tényleges esemény adatokat kell szerepel a bemeneti streamet. Az adott időtartományban feldolgozását a kimeneti eredmények pontos kimenetet eredményez.

## <a name="illustrated-example-of-watermarks"></a>A vízjelek ábrázolt példa

Az alábbi lemezképek bemutatják, hogyan halad a vízjelek különböző körülmények között.

Ez a táblázat alatti ábrázolt példa adatok. Figyelje meg, hogy az esemény időpontját és a érkezési idő változó, egyes esetekben megfelelő, és néha nem.

| Esemény időpontja | Érkezés időpontja | Eszközazonosító |
| --- | --- | --- |
| 12:07 | 12:07 | device1
| 12:08 | 12:08 | device2
| 12:17 | 12:11 | device1
| 12:08 | 12:13 | device3
| 12:19 | 12:16 | device1
| 12:12 | 12:17 | device3
| 12:17 | 12:18 | device2
| 12:20 | 12:19 | device2
| 12:16 | 12:21 | device3
| 12:23 | 12:22 | device2
| 12:22 | 12:24 | device2
| 12:21 | 12:27 | device3

Ezen az ábrán a következő eltérések használhatók:

- Korai érkezési windows érték 5 perc
- Késedelmes beérkező ablak érték 5 perc
- Átrendezési ablak 2 perce

1. Ezek az események keresztül halad vízjel ábrája:

   ![Az Azure Stream Analytics vízjel ábra](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   A fenti ábrán szemléltetett jelentős folyamatok:

   1. Az első esemény (device1), és a második esemény (device2) többször van igazítva, és módosítás nélkül dolgozzák fel. A vízjel különböző fázisokon halad az egyes eseményeket.

   2. A harmadik esemény (device1) feldolgozásakor a rendszer a érkezési ideje (12:11) szerepel az esemény időpontját (12:17). Az esemény 6 perccel, megérkezett, így az esemény miatt az 5 perces korai érkezési tolerancia eseményértesítése eldobva.

      A vízjel egy korai események ebben az esetben nem halad.

   3. A negyedik események (device3), és az ötödik esemény (device1) többször van igazítva, és a beállítás nélkül dolgozzák fel. A vízjel különböző fázisokon halad az egyes eseményeket.

   4. A hatodik (device3) esemény feldolgozásakor a rendszer a érkezési ideje (12:17) és az esemény időpontját (12:12) nem éri el a vízjel szintjét. Az esemény időpontját igazodik a vízjelek mark szint (12:17).

   5. A kilencedik (device3) esemény feldolgozásakor a rendszer a érkezési ideje (12:27) 6 perccel az esemény kelljen (12:21). A késedelmes érkezési szabályzat érvényes. Az esemény időpontját a módosított (12:22), amely a vízjel (12:21) felett tehát nincsenek további szabályozás van alkalmazva.

2. Vízjel halad előre, egy korai érkezési szabályzat nélkül második ábrája:

   ![Az Azure Stream Analytics előzetes házirend vízjel ábrát nem](media/stream-analytics-time-handling/watermark-graph-2.png)

   Ebben a példában nincs korai érkezési szabályzat érvényes. Korai érkezik a kiugró eseményeket hoz létre a vízjel jelentősen. Figyelje meg, hogy a harmadik esemény (deviceId1 idő 12:11) között nem szakad meg ebben a forgatókönyvben, és a vízjel jelenik meg, 12:15-re. A negyedik esemény idő következtében az átalakított előre 7 perc (12:08-12:15).

3. Az utolsó ábrán részadatfolyamok (keresztül az eszköz azonosítójával) használt. Több vízjelek nyomon követi, egy stream egy. Nincsenek kevesebb események eredményeként igazított időtől.

   ![Az Azure Stream Analytics substreams vízjel ábra](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>További lépések

- [Az Azure Stream Analytics esemény rendelés kapcsolatos szempontok](stream-analytics-out-of-order-and-late-events.md)
- [Stream Analytics-feladat metrikáit](stream-analytics-monitoring.md)
