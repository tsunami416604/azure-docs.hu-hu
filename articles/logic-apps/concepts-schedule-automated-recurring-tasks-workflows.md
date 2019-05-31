---
title: Ismétlődő feladatok ütemezése és munkafolyamatokat az Azure Logic Appsben
description: Ismétlődő, automatizált feladatokat, folyamatokat és munkafolyamatokat az Azure Logic Apps ütemezéséről további áttekintése
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 7f15dc5b28a44dc8405e2f0524913e6012ebe380
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66356051"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Ütemezése és ismétlődő, automatizált feladatokat, folyamatok és munkafolyamatok futtatása az Azure Logic Apps

A Logic Apps segítségével hozzon létre és automatikus ismétlődő feladatok és folyamatok futtatása ütemezés szerint. Hozzon létre egy logikai alkalmazás munkafolyamatának, amelyek egy eseményindítóval indul beépített ismétlődési vagy késleltetett ablakban, amelyek Ütemezés-típusú triggerek, feladatok azonnal, később, vagy egy ismétlődő időtartamot is futtathatja. Szolgáltatások belül és kívül az Azure-ban, például a HTTP vagy HTTPS-végpontok hívása, az Azure-szolgáltatások például az Azure Storage és Azure Service Bus üzeneteket tehet közzé, vagy a fájlok feltöltése egy fájlmegosztásba lekérése. Az ismétlődési trigger is beállíthat komplex ütemezések és a speciális ismétlődések feladatok futtatásához. További információ a beépített ütemezési eseményindítók és műveletek: [ütemezés és Futtatás ismétlődő, automatizált, feladatok és az Azure Logic Apps munkafolyamat](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Íme néhány példa azt mutatják be, bármilyen típusú feladatokat, amelyek futtathatja:

* Belső adatok, például a Futtatás SQL tárolt eljárás minden nap.

* Külső adatok, például lekéréses időjárás-előrejelzéseket kaphat a NOAA 15 percenként beolvasása.

* Adatokat, például az e-mailek nagyobb, mint az elmúlt héten határozott megrendeléseket összegzése küldhessen.

* Adatok feldolgozásához, például a mai compress minden csúcsidőn weekday rendelkezik feltöltött képek.

* Adatok, például a törlés három hónapnál régebbi összes tweetek törlése.

* Archiválási adatok, például egy biztonsági mentési szolgáltatás 1:00 órakor a következő kilenc hónap minden napján leküldéses számlákat.

A munkafolyamat felfüggesztése, például a következő művelet futtatása előtt is használhatja a beépített műveletek ütemezése:

* Várjon, amíg az állapot frissítéséhez küldése e-mailben egy hét napja.

* Késleltetés a munkafolyamat, amíg egy HTTP-hívás befejezését, mielőtt folytatása, és az eredmény beolvasása idő tartozik.

Ez a cikk ismerteti a funkciókat az ütemezés beépített triggereket és műveleteket.

## <a name="schedule-triggers"></a>Ütemezési eseményindító

Megkezdheti a logikai alkalmazás munkafolyamata az ismétlődési trigger vagy a késleltetett ablakos eseményindító, amelyek nem kapcsolódnak bármely adott szolgáltatás vagy a rendszer, például az Office 365 Outlook vagy az SQL Server. Ezek az eseményindítók indítsa el, és futtassa a munkafolyamat a megadott ismétlődési alapján, itt választhatja ki időközét és gyakoriságát, például másodperc, perc és egyaránt eseményindítók órák számát, vagy a nap, hét és az ismétlődési eseményindító hónapok száma. A kezdő dátum és idő, valamint az időzóna is beállíthatja. Minden alkalommal, amikor egy eseményindítót, a Logic Apps hoz létre, és a logikai alkalmazás egy új munkafolyamat-példány futtatja.

Ezek az eseményindítók között különbségek vannak itt:

* **Ismétlődés**: Futtatja a munkafolyamatot a megadott ütemezés alapján rendszeres időközönként. Ismétlődések elmaradt az ismétlődési eseményindító nem dolgozza fel a kihagyott ismétlődések, de a következő ütemezett időszakban az ismétlődések újraindul. Megadhat egy kezdési dátumot és időt, valamint az időzóna. Ha a "Day" lehetőséget választja, megadhatja a nap, óra és perc óra, ha például minden nap, 2:30. Ha "Week", a hét azon napjai, szerdán és szombat például is kiválaszthatja. További információkért lásd: [létrehozása, ütemezése, és ismétlődő feladatokat futtató és az ismétlődési eseményindító munkafolyamatok](../connectors/connectors-native-recurrence.md).

* **Csúszóablakon**: Futtatja a munkafolyamatot, amelyek kezelik az adatokat folyamatos tömbökben rendszeres időközönként. Ismétlődések elmaradt a késleltetett eseményindító visszatér, és feldolgozza a kihagyott ismétlődések. Kezdő dátum és idő időzóna és egy időtartamot késlelteti az egyes, a munkafolyamat ismétlődő is megadhat. Ez az eseményindító nem rendelkezik nap, hét, és a hónap, a nap, óra, perc, óra, és a hét azon napjai, adja meg a beállításokat. További információkért lásd: [létrehozása, ütemezése, és az ismétlődő feladatokat futtató és a késleltetett eseményindító munkafolyamatok](../connectors/connectors-native-sliding-window.md).

## <a name="schedule-actions"></a>Műveletek ütemezése

A logikai alkalmazás munkafolyamatának a műveleteket, miután a késleltetés és a késleltetés amíg műveletek segítségével a munkafolyamat, várjon a következő művelet futtatása előtt.

* **késleltetés**: Várjon, amíg a következő művelet futtatása a megadott számú másodperc, perc, óra, nap, hét vagy hónapok például a időegységek azon mennyisége. További információkért lásd: [munkafolyamatokban a következő művelet késleltetése](../connectors/connectors-native-delay.md).

* **Késleltetés eddig**: Várjon, amíg a következő művelet a megadott dátumig és időpontig. További információkért lásd: [munkafolyamatokban a következő művelet késleltetése](../connectors/connectors-native-delay.md).

## <a name="patterns-for-start-date-and-time"></a>Minták a kezdő dátum és idő

<a name="start-time"></a>

Íme néhány a minták azt mutatják be, hogy miként szabályozható a kezdő dátum és idő ismétlődési, és hogyan a Logic Apps szolgáltatás fut-e ezek ismétlődések:

| Kezdési idő | Ismétlődés ütemezés nélkül | Ismétlődés ütemezéssel (csak az ismétlődési trigger esetén) |
|------------|-----------------------------|----------------------------------------------------|
| {nincs} | Az első számítási feladat azonnal futtatja. <p>A legutóbbi futtatás ideje alapján jövőbeli számítási feladatot futtat. | Az első számítási feladat azonnal futtatja. <p>Jövőbeli számítási feladatok a megadott ütemezés alapján fut. |
| Múltbeli kezdési időpontja | **Ismétlődési** eseményindító: Futtatási időt a megadott kezdési időpontban és elvetések elmúlt futtassa többször alapján számítja ki. Az első számítási feladat fut, a következő jövő futási idő. <p>A legutóbbi futtatás ideje kiszámítása alapján jövőbeli számítási feladatot futtat. <p><p>**Csúszóablakon** eseményindító: Futtatási időt a megadott kezdési időpontban, és figyelembe veszi elmúlt futtassa többször alapján számítja ki. <p>A megadott kezdési időpontban kiszámítása alapján jövőbeli számítási feladatot futtat. <p><p>További magyarázat tekintse meg a táblázat példa. | Az első számítási feladatot futtat *nem hamarabb* a kezdési időpontnál, a kezdési időpontból kiszámított ütemezésen alapul. <p>Jövőbeli számítási feladatok a megadott ütemezés alapján fut. <p>**Megjegyzés:** Ha adjon meg egy ismétlődési ütemezés, de nem adja meg az óra vagy perc alatt az ütemezés, majd jövőbeli futtatási idő kiszámítása az óra vagy perc, az első futtatásának időpontját, használatával. |
| Jelenlegi vagy jövőbeni kezdő időpontja | Az első számítási feladatok a megadott kezdési időpontban fut. <p>A legutóbbi futtatás ideje kiszámítása alapján jövőbeli számítási feladatot futtat. | Az első számítási feladatot futtat *nem hamarabb* a kezdési időpontnál, a kezdési időpontból kiszámított ütemezésen alapul. <p>Jövőbeli számítási feladatok a megadott ütemezés alapján fut. <p>**Megjegyzés:** Ha adjon meg egy ismétlődési ütemezés, de nem adja meg az óra vagy perc alatt az ütemezés, majd jövőbeli futtatási idő kiszámítása az óra vagy perc, az első futtatásának időpontját, használatával. |
||||

*Példa a múltbeli kezdési időpont és ismétlődési, de ütemezés nélkül*

Tegyük fel, hogy az aktuális dátum és idő 2017. szeptember 8. jelenleg 1:00 Órakor. Azt adja meg a kezdő dátum és idő 2017. szeptember 7., 2:00 Órakor, a múltbeli és a egy ismétlődési, amely 2 naponta fut, amely.

| Kezdési idő | Aktuális idő | Ismétlődés | Ütemezés |
|------------|--------------|------------|----------|
| 2017-09-**07**T14:00:00Z <br>(2017-09 -**07** , 2:00-kor) | 2017-09-**08**T13:00:00Z <br>(2017-09 -**08** du. 1:00) | Minden 2 nap | {nincs} |
|||||

Az ismétlődési eseményindító futtatási idejének alapján a kezdési időpont alapján kiszámítja a Logic Apps elveti a korábbi futtatási időt, használja a következő jövő kezdési idő az első futtassa, és kiszámítja a jövőben alapján fut, a legutóbbi futtatás ideje.

Itt látható, hogyan néz ki az Ismétlődés:

| Kezdési idő | Első futtatás ideje | A jövőben futtatási idejének |
|------------|----------------|------------------|
| 2017-09 -**07** , 2:00-kor | 2017-09 -**09** , 2:00-kor | 2017-09 -**11** , 2:00-kor </br>2017-09 -**13** , 2:00-kor </br>2017-09 -**15** , 2:00-kor </br>és így tovább... |
||||

Igen, függetlenül attól milyen múltbeli például adja meg a kezdő időpont 2017-09 -**05** 2:00-kor, vagy 2017-09 -**01** 2:00 Órakor, az első futtassa a következő mindig használja a következő jövő kezdési időpontja.

A késleltetett ablak eseményindító, a futtatási idejének alapján a kezdési időpont alapján kiszámítja a Logic Apps figyelembe veszi az elmúlt futtatási idejének, a kezdési idő az első futtatásakor használ, és későbbi futtatások a kezdési időpont alapján számítja ki.

Itt látható, hogyan néz ki az Ismétlődés:

| Kezdési idő | Első futtatás ideje | A jövőben futtatási idejének |
|------------|----------------|------------------|
| 2017-09 -**07** , 2:00-kor | 2017-09 -**07** , 2:00-kor | 2017-09 -**09** , 2:00-kor </br>2017-09 -**11** , 2:00-kor </br>2017-09 -**13** , 2:00-kor </br>2017-09 -**15** , 2:00-kor </br>és így tovább... |
||||

Igen, függetlenül attól milyen múltbeli például adja meg a kezdő időpont 2017-09 -**05** 2:00-kor, vagy 2017-09 -**01** 2:00 Órakor, az első futtassa a következő mindig használja a megadott kezdési időpontja.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Példa ismétlődések

Az alábbiakban különböző példa ismétlődések, beállíthatja az eseményindítók, amelyek támogatják a beállítások:

| Eseményindító | Ismétlődés | Interval | Gyakoriság | Kezdési idő | Ezeken a napokon | Ezekben az órákban | Ezekben a percekben | Megjegyzés |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Ismétlődés <br>Csúszóablak | 15 percenként fut le (nincs kezdő dátum és idő) | 15 | Perc | {nincs} | {nem} érhető el | {nincs} | {nincs} | Ez az ütemezés azonnal elindítja, majd a jövőbeli ismétlődések a legutóbbi futtatás ideje alapján számítja ki. |
| Ismétlődés <br>Csúszóablak | 15 percenként fut le (a kezdő dátum és idő) | 15 | Perc | *startDate*T*startTime*Z | {nem} érhető el | {nincs} | {nincs} | Ez az ütemezés nem indul el *minden korábban* , mint a megadott kezdő dátum és idő, jövőbeli ismétlődések a legutóbbi futtatás ideje alapján számítja ki. |
| Ismétlődés <br>Csúszóablak | Futtassa a minden óra kezdetén (a kezdő dátum és idő) | 1 | Óra | *startDate*Thh:00:00Z | {nem} érhető el | {nincs} | {nincs} | Ez az ütemezés nem indul el *minden korábban* a megadott kezdési dátuma és időpontja. Jövőbeli ismétlődések "00" perces jel, akkor a kezdési időpontból kiszámított minden órában futtatva. <p>Ha a gyakoriság "Week" vagy "Month", az ütemezés rendre fut csak egy naponta, hetente vagy havonta egy nap. |
| Ismétlődés <br>Csúszóablak | Óránként fut le, minden nap (nincs kezdő dátum és idő) | 1 | Óra | {nincs} | {nem} érhető el | {nincs} | {nincs} | Ez az ütemezés azonnal elindul, és a jövőbeli ismétlődések a legutóbbi futtatás ideje alapján számítja ki. <p>Ha a gyakoriság "Week" vagy "Month", az ütemezés rendre fut csak egy naponta, hetente vagy havonta egy nap. |
| Ismétlődés <br>Csúszóablak | Óránként fut le, minden nap (kezdő dátum és idő) | 1 | Óra | *startDate*T*startTime*Z | {nem} érhető el | {nincs} | {nincs} | Ez az ütemezés nem indul el *minden korábban* , mint a megadott kezdő dátum és idő, jövőbeli ismétlődések a legutóbbi futtatás ideje alapján számítja ki. <p>Ha a gyakoriság "Week" vagy "Month", az ütemezés rendre fut csak egy naponta, hetente vagy havonta egy nap. |
| Ismétlődés <br>Csúszóablak | Futtassa az elmúlt óra 15 percenként, óránként (a kezdő dátum és idő) | 1 | Óra | *startDate*T00:15:00Z | {nem} érhető el | {nincs} | {nincs} | Ez az ütemezés nem indul el *minden korábban* a megadott kezdési dátuma és időpontja. Futtassa a "15" minute befejeződnie, amelynek kiszámítása a kezdetektől jövőbeli ismétlődések idő, így: 00:15-kor, 1:15-kor, 2:15-kor, és így tovább. |
| Ismétlődés | 15 percenként fut le egy óra, minden órában (nincs kezdő dátum és idő) | 1 | Nap | {nincs} | {nem} érhető el | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Ez az ütemezés lefuttat 00:15-kor, 1:15-kor, 2:15-kor, és így tovább. Ez az ütemezés is egyenértékűnek gyakoriságot, "Hour" és a egy kezdési időpontot "15" perc. |
| Ismétlődés | 15 percenként fut le, a megadott perces jelzések (kezdő dátum és idő nem). | 1 | Nap | {nincs} | {nem} érhető el | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ez az ütemezés nem indul el, mindaddig, amíg a következő megadott 15 perces be van jelölve. |
| Ismétlődés | Futtatás 8:00 órakor (kezdő dátum és idő nem) naponta | 1 | Nap | {nincs} | {nem} érhető el | 8 | {nincs} | Ez az ütemezés 8:00 órakor minden nap, fut, a megadott ütemezés alapján. |
| Ismétlődés | Futtatás 8:00 órakor minden nap (kezdő dátum és idő) | 1 | Nap | *startDate*T08:00:00Z | {nem} érhető el | {nincs} | {nincs} | Ez az ütemezés 8:00-kor minden nap, fut, a megadott kezdési időpont alapján. | 
| Ismétlődés | Futtatás reggel 8:30-kor minden nap (nincs kezdő dátum és idő) | 1 | Nap | {nincs} | {nem} érhető el | 8 | 30 | Ez az ütemezés futtatja, reggel 8:30-kor minden nap, a megadott ütemezés alapján. |
| Ismétlődés | Futtatás reggel 8:30-kor minden nap (kezdő dátum és idő) | 1 | Nap | *startDate*T08:30:00Z | {nem} érhető el | {nincs} | {nincs} | Ez az ütemezés a megadott kezdési időpontban reggel 8:30-kor kezdődik. |
| Ismétlődés | 8:30-kor és du. 4:30 naponta futtatása | 1 | Nap | {nincs} | {nem} érhető el | 8, 16 | 30 | |
| Ismétlődés | Futtatás reggel 8:30-kor, 8:45-kor, 4:30 = 1997031213 és 4:45-kor minden nap | 1 | Nap | {nincs} | {nem} érhető el | 8, 16 | 30, 45 | |
| Ismétlődés | Futtat minden szombat hajnali 17: 00 (nincs kezdő dátum és idő) | 1 | Hét | {nincs} | "Szombat" | 17 | 00 | Az ütemezés futtat minden szombat délután 5:00-kor. |
| Ismétlődés | Futtat minden szombat hajnali 17: 00 (a kezdő dátum és idő) | 1 | Hét | *startDate*T17:00:00Z | "Szombat" | {nincs} | {nincs} | Ez az ütemezés nem indul el *minden korábban* a megadott kezdési dátum és idő, ebben az esetben 2017. szeptember 9., 17:00-kor. Jövőbeli ismétlődések futtat minden szombat hajnali 17:00-kor. |
| Ismétlődés | Minden kedden, csütörtök, 17: 00 | 1 | Hét | {nincs} | "Kedd", "Thursday" | 17 | {nincs} | Ez az ütemezés futtatása minden kedden és csütörtökön délután 5:00-kor. |
| Ismétlődés | Óránként fut le munkaidőben | 1 | Hét | {nincs} | Válassza ki a hétvégén kivételével minden nap. | Adja meg a kívánt nap. | Válassza ki bármelyik kívánt órák perc. | Például ha a munkaórák 8:00-kor, 5:00 Órakor, majd válassza ki "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" szerint az órák, a nap. <p>Ha a munkaórák, 5:30 = 1997031213 8:30-kor, válassza ki az előző órában a napot plusz "30" óra perc. |
| Ismétlődés | Naponta egyszer futtatni hétvégén | 1 | Hét | {nincs} | "Szombat", "Sunday értékkel" | Adja meg a kívánt nap. | Válassza ki bármelyik óra perc szükség szerint. | Ez az ütemezés a megadott ütemezés szerint minden szombat és vasárnap futtatja. |
| Ismétlődés | 15 percenként fut le Kétheti hétfőn csak | 2 | Hét | {nincs} | "Hétfő" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ez az ütemezés lefuttat minden más hétfő minden 15 perces be van jelölve. |
| Ismétlődés | Futtatás havonta | 1 | Hónap | *startDate*T*startTime*Z | {nem} érhető el | {nem} érhető el | {nem} érhető el | Ez az ütemezés nem indul el *minden korábban* , mint a megadott kezdő dátum és idő és a jövőbeli ismétlődések kiszámítja a kezdő dátum és idő. Ha nem adja meg a kezdő dátum és idő, az ütemezés használja a létrehozásának dátuma és időpontja. |
| Ismétlődés | Óránként fut le egy nap / hó | 1 | Hónap | {Lásd a megjegyzést} | {nem} érhető el | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {Lásd a megjegyzést} | Ha nem adja meg a kezdő dátum és idő, az ütemezés használja a létrehozásának dátuma és időpontja. A perc az ismétlődési ütemezés beállításához adja meg az órák, a kezdési időt percben, vagy azt az időpontot használja. Például ha a kezdési és a létrehozás ideje 8:25-kor, ez az ütemezés fut reggel 8:25-kor, 9:25-kor, 10:25-kor, és így tovább. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Csak egyszer futtatni

Ha azt szeretné, a logikai alkalmazás futtatásához csak egy időben a jövőben is használhatja a **Scheduler: Egyszeri futtatás feladatok** sablont. Új logikai alkalmazás létrehozása után, de a Logic Apps Designer, a megnyitása előtt a **sablonok** szakaszban, az a **kategória** listáról válassza ki **ütemezés**, majd válassza ki Ez a sablon:

![Válassza "a Scheduler: Feladatok futtatása után"sablon](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Vagy, ha a logikai alkalmazás megkezdése a **amikor egy HTTP-kérés érkezik - kérelem** aktiválhat, és adja át a kezdési időpontot az eseményindító paraméterként. Az első művelet használja a **késleltetés eddig – ütemezése** műveletet, és adja meg az idő, amikor elindul a következő művelet.

## <a name="next-steps"></a>További lépések

* [Létrehozhatja, ütemezheti és ismétlődő feladatok és munkafolyamatok futtatása a ismétlődési trigger](../connectors/connectors-native-recurrence.md)
* [Létrehozhatja, ütemezheti és ismétlődő feladatok és a munkafolyamatok futtatásához a késleltetett ablakos eseményindító és](../connectors/connectors-native-sliding-window.md)
* [Munkafolyamatok felfüggesztése késleltetés műveletek](../connectors/connectors-native-delay.md)