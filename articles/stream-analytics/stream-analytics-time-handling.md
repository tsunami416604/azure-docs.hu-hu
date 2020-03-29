---
title: Az azure Stream Analytics időkezelésének megismerése
description: Ismerje meg, hogyan működik az időkezelés az Azure Stream Analytics szolgáltatásban, például hogyan választhatja ki a legjobb kezdési időpontot, hogyan kezelje a késői és a korai eseményeket, valamint az időkezelési mutatókat.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 367b7c2e1ce1c8b3c0dbc02003218b76096b409d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354652"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Az azure Stream Analytics időkezelésének megismerése

Ebben a cikkben bemutatjuk, hogyan hozhat tervezési döntéseket az Azure Stream Analytics szolgáltatás gyakorlati időkezelési problémáinak megoldásához. Az időkezelési tervezési döntések szorosan kapcsolódnak az eseményrendezési tényezőkhöz.

## <a name="background-time-concepts"></a>Háttéridő-fogalmak

A vitafórum jobb keretezéséhez definiáljunk néhány háttérfogalmat:

- **Esemény időpontja**: Az az időpont, amikor az eredeti esemény történt. Például, ha egy mozgó autó az autópályán közeledik egy fizetős fülkében.

- **Feldolgozási idő**: Az az időpont, amikor az esemény eléri a feldolgozó rendszert, és megfigyelhető. Ha például egy fizetős fülke-érzékelő látja az autót, és a számítógépes rendszer néhány percet vesz igénybe az adatok feldolgozása.

- **Vízjel**: Eseményidő-jelölő, amely azt jelzi, hogy milyen pontig kerültek be a streamelési processzorba az események. A vízjelek lehetővé teszik, hogy a rendszer egyértelmű előrehaladást jelezaz események betöltése során. Az adatfolyamok jellege miatt a bejövő eseményadatok soha nem állnak le, így a vízjelek jelzik az adatfolyam egy bizonyos pontjára irányuló előrehaladást.

   A vízjel koncepciófontos. A vízjelek lehetővé teszik a Stream Analytics számára annak meghatározását, hogy a rendszer mikor képes olyan teljes, helyes és megismételhető eredményeket produkálni, amelyeket nem kell visszavonni. A feldolgozás kiszámítható és megismételhető, garantáltan elvégezhető. Ha például egy hibakezelési feltétel esetén újraszámlálást kell végezni, a vízjelek biztonságos kezdő- és végpontok.

További források ebben a témában, lásd Tyler Akidau blogbejegyzések [Streaming 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) és [Streaming 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102).

## <a name="choosing-the-best-starting-time"></a>A legjobb kezdési időpont kiválasztása

A Stream Analytics két lehetőséget kínál a felhasználóknak az eseményidő kiválasztására:

1. **Érkezési idő**  

   Az érkezési idő a bemeneti forráshoz van rendelve, amikor az esemény eléri a forrást. Az érkezési időt az Event Hubs bemenetek **EventEnqueuedUtcTime** tulajdonságával, az **IoT Hub IoTHub.EnqueuedTime** tulajdonsággal, valamint a **BlobProperties.LastModified** tulajdonsággal érheti el a blob bemeneti.

   Az érkezési idő használata az alapértelmezett viselkedés, és a legjobban használt adatarchiválási forgatókönyvek, ahol nincs időbeli logika szükséges.

2. **Alkalmazás időpontja** (más néven Esemény idő)

   Az alkalmazás idő az esemény létrehozásakor van hozzárendelve, és az esemény hasznos adatának része. Az események alkalmazásidő szerint történő feldolgozásához használja a **timestamp by záradékot** a választó lekérdezésben. Ha a **Timestamp by** clause hiányzik, az események feldolgozása az érkezési időpontszerint történik.

   Fontos, hogy időbélyeget használjon a hasznos adat, ha időbeli logika van szó. Így a forrásrendszerben vagy a hálózatban beokozott késések et lehet elszámolni.

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Az idő előrehaladtával az Azure Stream Analytics

Az alkalmazás idő használatakor az idő előrehaladása a bejövő eseményeken alapul. A streamfeldolgozó rendszer nehezen tudja, hogy nincsenek-e események, vagy ha az események késnek. Ezért az Azure Stream Analytics heurisztikus vízjeleket hoz létre az egyes bemeneti partíciókhoz:

1. Amikor bármilyen bejövő esemény érkezik, a vízjel a legnagyobb eseményidő, amelyet eddig láttunk, mínusz a rendelésen kívüli tűrési ablak mérete.

2. Ha nincs bejövő esemény, a vízjel az aktuális becsült érkezési idő (a színfalak mögött eltelt idő virtuális gép, amely a bemeneti esemény legutóbbi, valamint a bemeneti esemény érkezési idejét) dolgozza fel a késői érkezési tolerancia ablakot.

   Az érkezési idő csak megbecsülhető, mert a valós érkezési idő jön létre a bemeneti esemény közvetítő, például az Event Hubs, és nem az Azure Stream Analytics virtuális gép az események feldolgozása.

A kialakítás két további célt szolgál a vízjelek generálása mellett:

1. A rendszer időben hozza létre az eredményeket bejövő eseményekkel vagy azok nélkül.

   Ön szabályozhatja, hogy mennyi ideig szeretné látni a kimeneti eredményeket. Az Azure Portalon a Stream Analytics-feladat **Eseményrendelési** lapján konfigurálhatja a **rendelésen kívüli események** beállítást. A beállítás konfigurálásakor vegye figyelembe az idővonalak és az eseménystream nem sorrendben lévő eseményeinek tűrésével kapcsolatos kompromisszumot.

   A késői érkezés tűréshatára fontos a vízjelek létrehozásához, még bejövő események hiányában is. Időnként előfordulhat, hogy egy olyan időszakban, amikor nem bejövő események jönnek be, például amikor egy esemény bemeneti adatfolyam ritka. Ezt a problémát súlyosbítja a bemeneti eseményközvetítőben több partíció használata.

   A késői érkezési tűréshatár-értékkel nem rendelkező adatfolyam-feldolgozási rendszerek késleltetett kimeneteket okozhatnak, ha a bemenetek ritkák, és több partíciót használnak.

2. A rendszer viselkedésének megismételhetőnek kell lennie. Az ismételhetőség a streamelési adatfeldolgozó rendszer fontos tulajdonsága.

   A vízjel az érkezési időből és a kijuttatási időből származik. Mindkettő megmarad az esemény bróker, és így megismételhető. Abban az esetben, ha az érkezési időt események hiányában kell megbecsülni, az Azure Stream Analytics naplóba írja a becsült érkezési időt a visszajátszás során a hiba helyreállítása érdekében.

Figyelje meg, hogy ha úgy dönt, hogy **az érkezési időt** használja eseményidőként, nincs szükség a sorrenden kívüli tűrésésés és a késői érkezési tolerancia konfigurálására. Mivel az **érkezési idő** garantáltan monoton módon növekszik a bemeneti eseményközvetítőben, az Azure Stream Analytics egyszerűen figyelmen kívül hagyja a konfigurációkat.

## <a name="late-arriving-events"></a>Későn érkező események

A késői érkezési tolerancia ablak meghatározása szerint az Azure Stream Analytics minden egyes bejövő esemény esetében összehasonlítja az **esemény idejét** az **érkezési idővel;** ha az esemény ideje kívül esik a tűrési ablakon, beállíthatja a rendszert úgy, hogy vagy eldobja az eseményt, vagy állítsa be az esemény idejét, hogy a tűréshatáron belül legyen.

Vegye figyelembe, hogy a vízjelek létrehozása után a szolgáltatás potenciálisan fogadhat eseményeket, amelyek eseményideje alacsonyabb, mint a vízjel. Beállíthatja, hogy a szolgáltatás vagy **eldobja** ezeket az eseményeket, vagy **állítsa be** az esemény idejét a vízjel értékéhez.

A korrekció részeként az esemény **System.Timestamp** értéke az új érték, de maga az **esemény időmezője** nem változik. Ez a korrekció az egyetlen olyan helyzet, amikor egy esemény **System.Timestamp** eltérhet az esemény időmezőjében szereplő értéktől, és nem várt eredményeket eredményezhet.

## <a name="handling-time-variation-with-substreams"></a>Kezelési idővariáció részfolyamokkal

Az itt leírt heurisztikus vízjel-generálási mechanizmus a legtöbb olyan esetben jól működik, amikor az idő többnyire szinkronizálódik a különböző eseményfeladók között. Azonban a valós életben, különösen sok IoT-forgatókönyvek, a rendszer kevés ellenőrzést az óra az esemény feladói. Az esemény feladói lehetmindenféle eszközök a területen, talán a különböző változatai hardver és szoftver.

Ahelyett, hogy egy globális vízjelet használna egy bemeneti partíció összes eseményéhez, a Stream Analytics egy másik mechanizmust, az úgynevezett részfolyamokat is használ, amelyek segítenek. A feladatrészfolyamokat a [**TIMESTAMP BY**](/stream-analytics-query/timestamp-by-azure-stream-analytics) záradékot és az **OVER**kulcsszót használó feladatlekérdezés írásával használhatja. Az aladatfolyam kijelöléséhez adjon meg **OVER** egy kulcsoszlop-nevet `deviceid`az OVER kulcsszó után, például egy , hogy a rendszer az adott oszlopra alkalmazza az időházirendeket. Minden részfolyam saját független vízjelet kap. Ez a mechanizmus akkor hasznos, ha lehetővé teszi az időben történő kimenetlétrehozását, ha nagy óradöntésekkel vagy hálózati késésekkel foglalkozik az eseményfeladók között.

Az aladatfolyamok az Azure Stream Analytics által biztosított egyedi megoldások, és más streamelési adatfeldolgozó rendszerek nem kínálnak. A Stream Analytics a késői érkezést űrtűrt űrtűrt alkalmazza a bejövő eseményekre, amikor részfolyamokat használnak. Az alapértelmezett beállítás (5 másodperc) valószínűleg túl kicsi az eltérő időbélyeggel rendelkező eszközök számára. Javasoljuk, hogy 5 percalatt kezdje, és az eszköz óradöntési mintájának megfelelően hajtsa elő a beállításokat.

## <a name="early-arriving-events"></a>Korai érkezési események

Lehet, hogy észrevette egy másik koncepció takarás, hogy néz ki, mint az ellenkezője a késői érkezés tolerancia ablak. Ez az ablak 5 perc alatt van rögzítve, és más célt szolgál, mint a késői érkezés.

Mivel az Azure Stream Analytics garantálja, hogy mindig teljes eredményeket hoz létre, csak a **feladat kezdési idejét** adhatja meg a feladat első kimeneti idejeként, nem pedig a bemeneti időként. A feladat kezdési ideje szükséges, hogy a teljes ablak feldolgozása, ne csak az ablak közepéről.

A Stream Analytics ezután a lekérdezésspecifikációból származtatja a kezdési időt. Mivel azonban a bemeneti eseményközvetítő csak az érkezési idő szerint indexelt, a rendszernek le kell fordítania a kezdési esemény idejét az érkezési időre. A rendszer megkezdheti az események feldolgozását abemeneti eseményközvetítő ezen pontjáról. A korai érkezési ablak korlát, a fordítás egyszerű. A kezdési idő mínusz az 5 perces korai érkezési ablak. Ez a számítás azt is jelenti, hogy a rendszer elejt minden olyan eseményt, amelynek eseményideje 5 perccel nagyobb az érkezési időnél, mint az érkezési idő.

Ez a koncepció annak biztosítására szolgál, hogy a feldolgozás megismételhető legyen, függetlenül attól, hogy honnan kezdi a kimenetet. Egy ilyen mechanizmus nélkül nem lenne lehetséges az ismételhetőség garantálása, ahogy azt sok más streaming rendszer állítja.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Az eseményrendelési idő tűrések mellékhatásai

A Stream Analytics-feladatok számos **eseményrendezési** lehetőséggel rendelkeznek. Kettő konfigurálható az Azure Portalon: a **rendelésen kívüli események** beállítása (sorrenden kívüli tolerancia) és az **események, amelyek későn érkeznek** beállítás (késői érkezés tolerancia). A **korai érkezési** tűréshatár rögzített, és nem módosítható. Ezeket az időszabályzatokat a Stream Analytics erős garanciák nyújtására használja. Ezeknek a beállításoknak azonban néha váratlan következményei vannak:

1. Véletlenül túl korai eseményeket küld.

   A korai eseményeket nem szabad normálisan kitenni. Lehetséges, hogy a korai események et a kimenetre küldi a rendszer, ha a feladó órája túl gyorsan fut. Minden korai érkező esemény elesik, így egyiket sem fogja látni a kimenetből.

2. Régi események küldése az Azure Stream Analytics által feldolgozandó Eseményközpontokba.

   Míg a régi események tűnhet ártalmatlan az első, mert az alkalmazás a késői érkezés tolerancia, a régi események is elkell dobni. Ha az események túl régiek, a **System.Timestamp** értéke módosul az esemény betöltése során. Ennek a viselkedésnek köszönhetően jelenleg az Azure Stream Analytics alkalmasabb a közel valós idejű eseményfeldolgozási forgatókönyvekhez, nem pedig az előzményfeldolgozási forgatókönyvekhez. Beállíthatja, hogy a **késői időpontban érkező események** a lehető legnagyobb értékre (20 nap) kerüljenek létre, hogy bizonyos esetekben megkerüljék ezt a viselkedést.

3. Úgy tűnik, a kimenetek késnek.

   Az első vízjel a számított időpontban jön létre: a rendszer által eddig megfigyelt **maximális eseményidő** mínusz a nem sorrendben lévő tűrési ablak mérete. Alapértelmezés szerint a sorrenden kívüli tűrés nulla (00 perc és 00 másodperc) van beállítva. Ha magasabb, nem nulla időértékre állítja be, a streamelési feladat első kimenete az első kiszámított vízjel-idő miatt késlelteti az idő (vagy nagyobb) értékét.

4. A bemenetek ritkák.

   Ha egy adott partícióban nincs bemenet, a vízjel-idő kiszámítása az **érkezési idő** mínusz a késői érkezési tolerancia ablak. Ennek eredményeképpen, ha a bemeneti események ritkák és ritka, a kimenet késleltethető ennyi idővel. A késői értéket **meghozó események** alapértelmezett értéke 5 másodperc. A bemeneti események egyenkénti küldésekor például némi késésvárható. A késések rosszabbodhatnak, ha a **késői ablakban érkező eseményeket** nagy értékre állítja be.

5. **A System.Timestamp** értéke eltér az **esemény időmezőjében** szereplő időtől.

   Ahogy azt korábban leírtuk, a rendszer az eseményidőt a sorrenden kívüli tűréshatár vagy a késői érkezési tolerancia ablakok szerint állítja be. A **System.Timestamp** értéke az esemény módosul, de nem az **esemény időmező.**

## <a name="metrics-to-observe"></a>Megfigyelésre betartandó mérőszámok

Az eseményrendelési időtűrési hatások at a [Stream Analytics feladatmérőszámokkal](stream-analytics-monitoring.md)is megfigyelheti. A következő mutatók relevánsak:

|Metrika  | Leírás  |
|---------|---------|
| **Nem sorrendben lévő események** | A sorrenden kívül érkező események számát jelzi, amelyeket eldobtak vagy módosított időbélyeggel kaptak. Ezt a metrikát közvetlenül befolyásolja a **rendelésen kívüli események** beállítása az Azure Portalon a feladat **eseményrendelési** lapján. |
| **Késői bemeneti események** | A forrásból későn érkező események számát jelzi. Ez a metrika tartalmazza az okat eldobott vagy időbélyegüket módosító eseményeket. Ezt a metrikát közvetlenül befolyásolja az **Események, amelyek késői** beállítás érkeznek az Azure Portalon a feladat **eseményrendezési** lapján. |
| **Korai bemeneti események** | A forrásból korán érkező események számát jelzi, amelyek et elvetették, vagy az időbélyegüket módosították, ha 5 perccel korábban haladtak. |
| **Vízjel késleltetése** | A streamelési adatfeldolgozási feladat késését jelzi. További információt a következő szakaszban talál.|

## <a name="watermark-delay-details"></a>Vízjel késleltetésének részletei

A **vízjel késleltetési** metrikája a feldolgozási csomópont faliórájának mínusz az eddig látott legnagyobb vízjeléből számít. További információt a [vízjel késleltetése blogbejegyzésben talál.](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

Ennek több oka is lehet, hogy ez a metrikaérték normál működés mellett 0-nál nagyobb:

1. A streamelési folyamat eredendő feldolgozási késleltetése. Általában ez a késedelem névleges.

2. A nem sorrendben ellátható tűréshatár-keret késleltetést vezetett be, mivel a vízjel a tűréshatár-ablak méretével csökken.

3. A késői érkezési ablak késleltetést vezetett be, mivel a vízjel a tűréshatár-keret méretével csökken.

4. A metrikát létrehozó feldolgozó csomópont óradöntése.

Számos más erőforrás-megkötések, amelyek hatására a streamelési folyamat lelassulhat. A vízjel késleltetési mutatója a következők miatt emelkedhet:

1. Nincs elég feldolgozási erőforrások a Stream Analytics-ben a bemeneti események mennyiségének kezeléséhez. Az erőforrások méretezéséhez [olvassa el A streamelési egységek megértése és módosítása](stream-analytics-streaming-unit-consumption.md).

2. Nincs elég átviteli a bemeneti esemény brókerek, így azok szabályozása. A lehetséges megoldásokról az [Azure Event Hubs átviteli egységeinek automatikus skálázása](../event-hubs/event-hubs-auto-inflate.md).for possible solutions, see Automatically scale up up Azure Event Hubs throughput units.

3. Kimeneti fogadók nincsenek kiépítve elegendő kapacitással, így azok szabályozása. A lehetséges megoldások nagymértékben eltérnek az alkalmazott kimeneti szolgáltatás ízétól függően.

## <a name="output-event-frequency"></a>Kimeneti esemény gyakorisága

Az Azure Stream Analytics a vízjel-előrehaladást használja az egyetlen eseményindítóként a kimeneti események létrehozásához. Mivel a vízjel bemeneti adatokból származik, a hiba-helyreállítás és a felhasználó által kezdeményezett újrafeldolgozás során is megismételhető.

[Ablakos aggregátumok](stream-analytics-window-functions.md)használataesetén a szolgáltatás csak az ablakok végén hoz létre kimeneteket. Bizonyos esetekben előfordulhat, hogy a felhasználók az ablakokból létrehozott részleges összesítéseket szeretnék látni. Részleges összesítések jelenleg nem támogatott az Azure Stream Analytics.

Más streamelési megoldásokban a kimeneti események a külső körülményektől függően különböző eseményindító pontokon valósulhatnak meg. Egyes megoldásokban lehetséges, hogy egy adott időablak kimeneti eseményei többször is létrehozhatók. A bemeneti értékek finomításával az összesített eredmények pontosabbá válnak. Az eseményeket eleinte el lehet spekulálni, és idővel felül lehetett vizsgálni. Ha például egy adott eszköz offline állapotban van a hálózatról, a rendszer felhasználhat egy becsült értéket. Később ugyanaz az eszköz online állapotba kerül a hálózatra. Ezután a tényleges eseményadatok is szerepelhetnek a bemeneti adatfolyamban. Az időablak feldolgozásából származó kimeneti eredmények pontosabb kimenetet eredményeznek.

## <a name="illustrated-example-of-watermarks"></a>Példa a vízjelekre

Az alábbi képek bemutatják, hogyan haladnak a vízjelek különböző körülmények között.

Ez a táblázat az alábbi példaadatokat mutatja be. Figyelje meg, hogy az esemény ideje és az érkezési idő változik, néha egyező, és néha nem.

| Esemény időpontja | Érkezési idő | DeviceId |
| --- | --- | --- |
| 12:07 | 12:07 | device1
| 12:08 | 12:08 | device2
| 12:17 | 12:11 | device1
| 12:08 | 12:13 | eszköz3
| 12:19 | 12:16 | device1
| 12:12 | 12:17 | eszköz3
| 12:17 | 12:18 | device2
| 12:20 | 12:19 | device2
| 12:16 | 12:21 | eszköz3
| 12:23 | 12:22 | device2
| 12:22 | 12:24 | device2
| 12:21 | 12:27 | eszköz3

Ezen az ábrán a következő tűréshatárokat használjuk:

- Korai érkezési ablakok 5 perc
- A kései érkezési ablak 5 perc
- Az átrendezési ablak 2 perc

1. A vízjel ezen eseményeken keresztüli előrehaladásának illusztrációja:

   ![Az Azure Stream Analytics vízjelének illusztrációja](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Az előző ábrán bemutatott figyelemre méltó folyamatok:

   1. Az első esemény (device1) és a második esemény (device2) időhöz igazodik, és korrekció nélkül dolgozza fel. A vízjel minden eseménynél előrehalad.

   2. A harmadik esemény (device1) feldolgozásakor az érkezési idő (12:11) megelőzi az esemény időpontját (12:17). Az esemény 6 perccel korábban érkezett, így az esemény az 5 perces korai érkezési tolerancia miatt elmaradt.

      A vízjel nem halad előre ebben az esetben a korai esemény.

   3. A negyedik esemény (device3) és az ötödik esemény (device1) időhöz igazodik, és kiigazítás nélkül dolgozza fel. A vízjel minden eseménynél előrehalad.

   4. A hatodik esemény (device3) feldolgozásakor az érkezési idő (12:17) és az esemény időpontja (12:12) a vízjel szintje alatt van. Az esemény ideje a vízjel szintjéhez van igazítva (12:17).

   5. A tizenkettedik esemény (device3) feldolgozásakor az érkezési idő (12:27) 6 perccel az esemény időpontja előtt van (12:21). A késői érkezésre vonatkozó szabályzat érvényes. Az esemény időpontja módosul (12:22), amely a vízjel felett van (12:21), így nincs további beállítás.

2. A vízjel korai érkezési szabályzat nélküli előrehaladásának második példája:

   ![Az Azure Stream Analytics nincs korai házirend-vízjel illusztráció](media/stream-analytics-time-handling/watermark-graph-2.png)

   Ebben a példában nincs idő előtt érkezési szabályzat alkalmazva. A korai érkezéskiugró események jelentősen emelik a vízjelet. Figyelje meg, hogy a harmadik esemény (deviceId1 időpontban 12:11) nem esett ebben a forgatókönyvben, és a vízjel 12:15-re emelkedik. A negyedik esemény idő van beállítva előre 7 perc (12:08-12:15) eredményeként.

3. A végső ábrán részfolyamokat használnak (a DeviceId felett). Több vízjelet követnek nyomon, streamenként egyet. Kevesebb esemény van, és az időjük ennek eredményeképpen módosult.

   ![Az Azure Stream Analytics részfolyamként imitomára](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>További lépések

- [Az Azure Stream Analytics eseményrendelési szempontjai](stream-analytics-out-of-order-and-late-events.md)
- [Stream Analytics-feladat mérőszámai](stream-analytics-monitoring.md)
