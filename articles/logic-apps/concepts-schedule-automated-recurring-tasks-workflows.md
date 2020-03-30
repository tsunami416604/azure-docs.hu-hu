---
title: Ismétlődő feladatok és munkafolyamatok ütemezése az Azure Logic Apps alkalmazásban
description: Az ismétlődő automatizált feladatok, folyamatok és munkafolyamatok Azure Logic Apps alkalmazásokkal történő ütemezésének áttekintése
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 0f6ec158cf6ab855191e6796be3abec7d37439a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270562"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Ismétlődő automatizált feladatok, folyamatok és munkafolyamatok ütemezése és futtatása az Azure Logic Apps használatával

A Logic Apps segítségével ütemezés szerint hozhat létre és futtathat automatikus ismétlődő feladatokat és folyamatokat. Ha olyan logikai alkalmazás-munkafolyamatot hoz létre, amely egy beépített Ismétlődés eseményindítóval vagy csúszó ablak eseményindítóval kezdődik, amely ütemezés típusú eseményindítók, azonnal, egy későbbi időpontban vagy ismétlődő időközön futtathatja a feladatokat. Az Azure-on belüli és kívüli szolgáltatásokat, például a HTTP- vagy HTTPS-végpontokat, üzeneteket küldhet az Azure-szolgáltatásokba, például az Azure Storage-ba és az Azure Service Busba, vagy fájlokat tölthet be egy fájlmegosztásra. Az Ismétlődés eseményindítóval összetett ütemezéseket és speciális ismétlődéseket is beállíthat a feladatok futtatásához. Ha többet szeretne tudni a beépített ütemezési eseményindítókról és műveletekről, olvassa el az [Eseményindítók](#schedule-triggers) és [A műveletek ütemezése című témakört.](#schedule-actions) 

> [!TIP]
> Az ismétlődő munkaterhelések ütemezése és futtatása anélkül is ütemezhető, hogy minden egyes ütemezett feladathoz külön logikai alkalmazást kellene létrehoznia, és [régiónként és előfizetésenként a munkafolyamatokra vonatkozó korlátba kellene futnia.](../logic-apps/logic-apps-limits-and-config.md#definition-limits) Ehelyett használhatja az Azure QuickStart sablon által létrehozott logikai [alkalmazásmintát: Logic Apps feladatütemező.](https://github.com/Azure/azure-quickstart-templates/tree/master/301-logicapps-jobscheduler/)
>
> A Logic Apps feladatütemező sablon létrehoz egy CreateTimerJob logikai alkalmazást, amely meghívja a TimerJob logikai alkalmazást. Ezután meghívhatja a CreateTimerJob logikai alkalmazást API-ként egy HTTP-kérelem, és a kérelem bemeneti ütemezése átadása. A CreateTimerJob logikai alkalmazás minden egyes hívása meghívja a TimerJob logikai alkalmazást is, amely létrehoz egy új TimerJob-példányt, amely folyamatosan fut a megadott ütemezés alapján, vagy amíg meg nem felel egy megadott korlátnak. Így annyi TimerJob-példányt futtathat, amennyit csak szeretne, anélkül, hogy aggódnia kellene a munkafolyamat-korlátok miatt, mivel a példányok nem különálló logikai alkalmazás-munkafolyamat-definíciók vagy erőforrások.

Ez a lista néhány példafeladatot tartalmaz, amelyeket a Beépített ütemezés eseményindítókkal futtathat:

* Belső adatok beszerezni, például futtatni egy SQL tárolt eljárás minden nap.

* Külső adatokat, például időjárás-jelentéseklekérése a NOAA-tól 15 percenként.

* Jelentésadatok küldése, például e-mailben az összes rendelésre vonatkozó anam-mailben, amely nagyobb, mint egy adott összeg az elmúlt héten.

* Dolgozza fel az adatokat, például tömörítse a mai feltöltött képeket minden hétköznap csúcsidőn kívül.

* Törölje az adatokat, például törölje az összes három hónapnál régebbi tweetet.

* Archiválja az adatokat, például leküldéses számlák egy biztonsági mentési szolgáltatás 1:00 minden nap a következő kilenc hónapban.

A Beépített műveletek ütemezése segítségével is szüneteltetheti a munkafolyamatot a következő művelet futtatása előtt, például:

* Várjon egy hétköznapig, hogy e-mailben küldjön állapotfrissítést.

* Az eredmény folytatása és beolvasása előtt a HTTP-hívás befejezésére van idő, amíg a HTTP-hívásnak be kell fejeződnie.

Ez a cikk ismerteti a beépített eseményindítók és műveletek ütemezése képességeit.

<a name="schedule-triggers"></a>

## <a name="schedule-triggers"></a>Eseményindítók ütemezése

A logikai alkalmazás munkafolyamatát az Ismétlődés eseményindító vagy a Csúszó ablak eseményindítóval indíthatja el, amely nincs társítva egyetlen adott szolgáltatáshoz vagy rendszerhez, például az Office 365 Outlook vagy az SQL Server alkalmazáshoz. Ezek az eseményindítók a megadott ismétlődés alapján indítják el és futtatják a munkafolyamatot, ahol kiválaszthatja az időközt és gyakoriságot, például a másodpercek, percek és órák számát mindkét eseményindítóesetén, vagy az Ismétlődés eseményindító napjainak, heteinek vagy hónapjainak számát. Beállíthatja a kezdési dátumot és időt, valamint az időzónát is. Minden alkalommal, amikor egy eseményindító aktiválódik, a Logic Apps új munkafolyamat-példányt hoz létre és futtat a logikai alkalmazáshoz.

Az alábbi eseményindítók között az alábbi különbségek vannak:

* **Ismétlődés**: A munkafolyamatot a megadott ütemezés alapján rendszeres időközönként futtatja. Ha az ismétlődések kimaradnak, az Ismétlődés eseményindító nem dolgozza fel a kihagyott ismétlődéseket, de a következő ütemezett időközzel újraindítja az ismétlődéseket. Megadhatja a kezdő dátumot és az időt, valamint az időzónát. Ha a "Nap" lehetőséget választja, megadhatja a nap óráit és az óra perceit, például minden nap 2:30-kor. Ha a "Hét" lehetőséget választja, a hét napjait is kiválaszthatja, például szerdán és szombaton. További információt az [Ismétlődő feladatok és munkafolyamatok létrehozása, ütemezése és futtatása az Ismétlődés eseményindítóval című témakörben](../connectors/connectors-native-recurrence.md)talál.

* **Csúszó ablak:** A munkafolyamatot rendszeres időközönként futtatja, amelyek folyamatos adattömbökben kezelik az adatokat. Ha az ismétlődések kimaradnak, a Csúszó ablak eseményindító visszamegy, és feldolgozza a kihagyott ismétlődéseket. Megadhatja a kezdő dátumot és az időt, az időzónát és az időtartamot, hogy késleltesse a munkafolyamat minden ismétlődését. Ez az eseményindító nem rendelkezik a napok, hetek és hónapok, a nap óráinak, az óra perceinek és a hét napjainak megadására. További információt az [Ismétlődő feladatok és munkafolyamatok létrehozása, ütemezése és futtatása a Csúszóablak-eseményindítóval című témakörben talál.](../connectors/connectors-native-sliding-window.md)

<a name="schedule-actions"></a>

## <a name="schedule-actions"></a>Műveletek ütemezése

A logikai alkalmazás munkafolyamatában végrehajtott bármely művelet után használhatja a Késleltetés és késleltetés a míg a műveleteket, hogy a munkafolyamat várjon a következő művelet futtatása előtt.

* **Késleltetés**: Várja meg a következő művelet futtatását a megadott számú időegységhez, például másodperchez, perchez, órához, naphoz, héthez vagy hónaphoz. További információ: [A következő művelet késleltetése a munkafolyamatokban című témakörben.](../connectors/connectors-native-delay.md)

* **Késleltetés a míg**: Várjon a következő művelet futtatásával a megadott dátumig és időpontig. További információ: [A következő művelet késleltetése a munkafolyamatokban című témakörben.](../connectors/connectors-native-delay.md)

## <a name="patterns-for-start-date-and-time"></a>A kezdő dátum és az idő mintái

<a name="start-time"></a>

Íme néhány példa, amely bemutatja, hogyan szabályozhatja az ismétlődést a kezdő dátummal és idővel, és hogyan futtatja a Logic Apps szolgáltatás ezeket az ismétlődéseket:

| Kezdési idő | Ismétlődés ütemezés nélkül | Ismétlődés ütemezéssel (csak ismétlődési eseményindító) |
|------------|-----------------------------|----------------------------------------------------|
| {nincs} | Az első számítási feladatok azonnal fut. <p>A jövőbeli számítási feladatok futtatása az utolsó futtatási idő alapján. | Az első számítási feladatok azonnal fut. <p>Jövőbeli számítási feladatok futtatása a megadott ütemezés alapján. |
| Kezdési időpont a múltban | **Ismétlődési** eseményindító: Kiszámítja a futási időket a megadott kezdési idő alapján, és elveti a korábbi futtatási időket. Az első számítási feladatok futtatása a következő jövőbeli futtatási időpontban. <p>A jövőbeli számítási feladatokfuttatása az utolsó futtatási idő számításai alapján. <p><p>**Csúszó ablak** eseményindító: Kiszámítja a futási időket a megadott kezdési idő alapján, és kitüntetések múltfutási idők. <p>Jövőbeli számítási feladatok futtatása a megadott kezdési időpontszámításai alapján. <p><p>További magyarázat: a táblázat ot követő példa. | Az első számítási feladatot a kezdési *időpontnál legkorábban* futtatja a kezdési időponttól számított ütemezés alapján. <p>Jövőbeli számítási feladatok futtatása a megadott ütemezés alapján. <p>**Megjegyzés:** Ha ütemezéssel rendelkező ismétlődést ad meg, de nem ad meg órákat vagy perceket az ütemezéshez, akkor a jövőbeli futási idők et az első futási időórái vagy percei alapján számítja ki a rendszer. |
| Kezdési időpont jelenleg vagy a jövőben | Az első számítási feladatot a megadott kezdési időpontban futtatja. <p>A jövőbeli számítási feladatokfuttatása az utolsó futtatási idő számításai alapján. | Az első számítási feladatot a kezdési *időpontnál legkorábban* futtatja a kezdési időponttól számított ütemezés alapján. <p>Jövőbeli számítási feladatok futtatása a megadott ütemezés alapján. <p>**Megjegyzés:** Ha ütemezéssel rendelkező ismétlődést ad meg, de nem ad meg órákat vagy perceket az ütemezéshez, akkor a jövőbeli futási idők et az első futási időórái vagy percei alapján számítja ki a rendszer. |
||||

> [!IMPORTANT]
> Ha az ismétlődések nem adnak meg speciális ütemezési beállításokat, a jövőbeli ismétlődések az utolsó futtatási időalapján történnek.
> Ezek az ismétlődések kezdési időpontjai elsodródhatnak olyan tényezők miatt, mint például a tárolási hívások során a késés. Annak érdekében, hogy a logikai alkalmazás ne maradjon le az ismétlődésről, különösen akkor, ha a gyakoriság napokban vagy hosszabb ideig tart, használja az alábbi lehetőségek egyikét:
> 
> * Adja meg az ismétlődés kezdési idejét.
> 
> * Adja meg az órákat és perceket, hogy mikor kell futtatni az ismétlődést az Ezekben az **órákban** és ezekben a **percekben** tulajdonságok használatával.
> 
> * Az Ismétlődés eseményindító helyett használja a [Csúszó ablak eseményindítót.](../connectors/connectors-native-sliding-window.md)

*Példa a múltbeli kezdési időpontra és ismétlődésre, de nincs ütemezés*

Tegyük fel, hogy az aktuális dátum és időpont 2017. A kezdési dátumot és az időpontot 2017.

| Kezdési idő | Aktuális idő | Ismétlődés | Ütemezés |
|------------|--------------|------------|----------|
| 2017-09-**07**T14:00:00Z <br>(2017-09-**07** at 14:00) | 2017-09-**08**T13:00:00Z <br>(2017-09-**08** at 13:00) | Kétnaponta | {nincs} |
|||||

Az ismétlődési eseményindító esetében a Logic Apps motor kiszámítja a futási időket a kezdési idő alapján, elveti a korábbi futtatási időket, a következő jövőbeli kezdési időpontot használja az első futtatáshoz, és kiszámítja a jövőbeli futtatásokat az utolsó futtatási idő alapján.

Az ismétlődés a következőképpen néz ki:

| Kezdési idő | Első futtatási idő | A jövő futási ideje |
|------------|----------------|------------------|
| 2017-09-**07** at 14:00 | 2017-09-**09** at 14:00 | 2017-09-**11** at 14:00 </br>2017-09-**13** at 14:00 </br>2017-09-**15** at 14:00 </br>és így tovább ... |
||||

Tehát nem számít, milyen messze van a múltban, adja meg a kezdési időpontot, például 2017-09-**05** 14:00-kor vagy 2017-09-01-kor 14:00-kor, az első futtatás mindig a következő jövőbeli kezdési időpontot használja.**01**

A Csúszó ablak eseményindító esetében a Logic Apps motor kiszámítja a futási időket a kezdési idő alapján, kifizeti a korábbi futási időket, az első futtatás kezdési idejét használja, és a jövőbeli futtatásokat a kezdési idő alapján számítja ki.

Az ismétlődés a következőképpen néz ki:

| Kezdési idő | Első futtatási idő | A jövő futási ideje |
|------------|----------------|------------------|
| 2017-09-**07** at 14:00 | 2017-09-**07** at 14:00 | 2017-09-**09** at 14:00 </br>2017-09-**11** at 14:00 </br>2017-09-**13** at 14:00 </br>2017-09-**15** at 14:00 </br>és így tovább ... |
||||

Tehát nem számít, hogy a múltban milyen messze adja meg a kezdési időpontot, például 2017-09-**05** 14:00-kor vagy 2017-09-01-kor 14:00-kor, az első futtatás mindig a megadott kezdési időpontot használja.**01**

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Példa ismétlődésre

Íme néhány példa ismétlődések, amelyek beállíthatja az eseményindítók, amelyek támogatják a lehetőségeket:

| Eseményindító | Ismétlődés | Intervallum | Frequency | Kezdési idő | Ezeken a napokon | Ezekben az órákban | Ezekben a percekben | Megjegyzés |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Ismétlődés <br>Csúszóablak | Futtatás 15 percenként (nincs kezdési dátum és idő) | 15 | Perc | {nincs} | {nem érhető el} | {nincs} | {nincs} | Ez az ütemezés azonnal elindul, majd kiszámítja a jövőbeli ismétlődéseket az utolsó futtatási idő alapján. |
| Ismétlődés <br>Csúszóablak | Futtatás 15 percenként (kezdési dátummal és idővel) | 15 | Perc | *kezdődátum* T*startTime*Z | {nem érhető el} | {nincs} | {nincs} | Ez az ütemezés nem indul el a megadott kezdési dátumnál és időpontnál *korábban,* majd kiszámítja a jövőbeli ismétlődéseket az utolsó futtatási idő alapján. |
| Ismétlődés <br>Csúszóablak | Futtatás óránként, az óra (a kezdő dátum és időpont) | 1 | Óra | *kezdődátum* Thh:00:00Z | {nem érhető el} | {nincs} | {nincs} | Ez az ütemezés nem indul el a megadott kezdési dátumnál és időpontnál *korábban.* A jövőbeli ismétlődések óránként futnak a "00" perces jellel, amely et a kezdési időponttól számítja ki a program. <p>Ha a gyakoriság "Hét" vagy "Hónap", akkor ez az ütemezés csak heti vagy egy napot futtat havonta. |
| Ismétlődés <br>Csúszóablak | Futtatás óránként, minden nap (nincs kezdési dátum és idő) | 1 | Óra | {nincs} | {nem érhető el} | {nincs} | {nincs} | Ez az ütemezés azonnal elindul, és az utolsó futtatási idő alapján kiszámítja a jövőbeli ismétlődéseket. <p>Ha a gyakoriság "Hét" vagy "Hónap", akkor ez az ütemezés csak heti vagy egy napot futtat havonta. |
| Ismétlődés <br>Csúszóablak | Futtatás óránként, minden nap (kezdési dátummal és idővel) | 1 | Óra | *kezdődátum* T*startTime*Z | {nem érhető el} | {nincs} | {nincs} | Ez az ütemezés nem indul el a megadott kezdési dátumnál és időpontnál *korábban,* majd kiszámítja a jövőbeli ismétlődéseket az utolsó futtatási idő alapján. <p>Ha a gyakoriság "Hét" vagy "Hónap", akkor ez az ütemezés csak heti vagy egy napot futtat havonta. |
| Ismétlődés <br>Csúszóablak | Futtatás 15 percenként az óra után, óránként (kezdési dátummal és idővel) | 1 | Óra | *kezdődátum* T00:15:00Z | {nem érhető el} | {nincs} | {nincs} | Ez az ütemezés nem indul el a megadott kezdési dátumnál és időpontnál *korábban.* A jövőbeli ismétlődések a "15" perces jellel futnak, amelyet a kezdési időponttól számítanak ki, így 00:15,15, 2:15 és így tovább. |
| Ismétlődés | Fuss 15 percenként az óra után, óránként (nincs kezdési dátum és időpont) | 1 | Day | {nincs} | {nem érhető el} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Ez az ütemezés 00:15-kor, 1:15-kor, 2:15-kor és így tovább tart. Továbbá, ez az ütemezés megegyezik a gyakorisága "Óra" és a kezdési idő "15" perc. |
| Ismétlődés | Fusson 15 percenként a megadott percjeleken (nincs kezdő dátum és időpont). | 1 | Day | {nincs} | {nem érhető el} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ez az ütemezés csak a következő megadott 15 perces jel kezdődik. |
| Ismétlődés | Naponta *8:00-kor, valamint* a logikai alkalmazás mentésekor a percjel | 1 | Day | {nincs} | {nem érhető el} | 8 | {nincs} | Kezdési dátum és idő nélkül ez az ütemezés a logikai alkalmazás (PUT művelet) mentésének időpontja alapján fut. |
| Ismétlődés | Fuss naponta 8:00-kor (kezdési dátummal és időpontdal) | 1 | Day | *kezdődátum* T08:00:00Z | {nem érhető el} | {nincs} | {nincs} | Ez az ütemezés nem indul el a megadott kezdési dátumnál és időpontnál *korábban.* A jövőbeli események naponta 8:00-kor futnak. | 
| Ismétlődés | Fuss naponta 8:30-kor (nincs kezdési dátum és időpont) | 1 | Day | {nincs} | {nem érhető el} | 8 | 30 | Ez az ütemezés minden nap 8:30-kor tart. |
| Ismétlődés | Naponta 8:30-kor és 16:30-kor futnak | 1 | Day | {nincs} | {nem érhető el} | 8, 16 | 30 | |
| Ismétlődés | Naponta 8:30-kor, 8:45-kor, 16:30-kor és 16:45-kor futtatják | 1 | Day | {nincs} | {nem érhető el} | 8, 16 | 30, 45 | |
| Ismétlődés | Fuss minden szombaton 17:00-kor (nincs kezdési dátum és időpont) | 1 | Hét | {nincs} | "Szombat" | 17 | 00 | Ez a menetrend minden szombaton 17:00 órakor tart. |
| Ismétlődés | Fuss on minden szombaton 17:00 (a kezdő dátum és időpont) | 1 | Hét | *kezdődátum* T17:00:00Z | "Szombat" | {nincs} | {nincs} | Ez az ütemezés nem indul *el hamarabb,* mint a megadott kezdési dátum és időpont, ebben az esetben, szeptember 9, 2017 at 5:00. A jövőbeli ismétlődések minden szombaton 17:00-kor futnak. |
| Ismétlődés | Fuss minden kedden, csütörtökön *17:00-kor, valamint* a logikai alkalmazás mentésekor érvényes percjellel| 1 | Hét | {nincs} | "Kedd", "Csütörtök" | 17 | {nincs} | |
| Ismétlődés | Munkaidőben óránként fut | 1 | Hét | {nincs} | Válassza ki az összes napot, kivéve a szombatot és a vasárnapot. | Válassza ki a kívánt napi órákat. | Válassza ki a kívánt órapercet. | Ha például a munkaidő 8:00 és 17:00 óra között van, akkor a nap óráiként válassza a "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" lehetőséget. <p>Ha a munkaidő 8:30 és 17:30 között van, válassza ki a nap előző óráit plusz a "30" percet az óra percében. |
| Ismétlődés | Fuss naponta egyszer hétvégén | 1 | Hét | {nincs} | "Szombat", "Vasárnap" | Válassza ki a kívánt napi órákat. | Válassza ki az óra bármely percét. | Ez az ütemezés minden szombaton és vasárnap a megadott ütemezés szerint tart. |
| Ismétlődés | Fuss 15 percenként kéthetente csak hétfőnként | 2 | Hét | {nincs} | "Hétfő" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ez az ütemezés minden második hétfőn 15 percenként tart. |
| Ismétlődés | Futtatás havonta | 1 | Month | *kezdődátum* T*startTime*Z | {nem érhető el} | {nem érhető el} | {nem érhető el} | Ez az ütemezés nem indul el a megadott kezdési dátumnál és időpontnál *korábban,* és kiszámítja a jövőbeli ismétlődéseket a kezdő dátumon és időpontban. Ha nem ad meg kezdési dátumot és időpontot, az ütemezés a létrehozás dátumát és időpontját használja. |
| Ismétlődés | Fuss minden órában egy nap havonta | 1 | Month | {lásd a megjegyzést} | {nem érhető el} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {lásd a megjegyzést} | Ha nem ad meg kezdési dátumot és időpontot, az ütemezés a létrehozás dátumát és időpontját használja. Az ismétlődési ütemezés perceinek szabályozásához adja meg az óra perceit, a kezdési időpontot, vagy használja a létrehozási időt. Ha például a kezdési időpont vagy a létrehozás időpontja 8:25, ez az ütemezés 8:25, 9:25, 10:25 és így tovább tart. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Csak egyszer fusson

Ha a logikai alkalmazást csak egy időben szeretné futtatni a jövőben, használhatja az **Ütemező: Egyszer futó feladat** sablont. Miután létrehozott egy új logikai alkalmazást, de a Logic Apps Designer megnyitása előtt, a **Sablonok** csoport **Kategória** listájában válassza az **Ütemezés**lehetőséget, majd válassza a sablont:

![Válassza az "Ütemező: Feladatok futtatása" sablont](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Vagy ha elindíthatja a logikai alkalmazást a **HTTP-kérelem beérkezésekor – Eseményindító kérése,** és adja át a kezdési időpontot az eseményindító paramétereként. Az első művelethez használja a **Késleltetés a - Ütemezés** műveletet, és adja meg a következő művelet futásának időpontját.

## <a name="next-steps"></a>További lépések

* [Ismétlődő feladatok és munkafolyamatok létrehozása, ütemezése és futtatása az Ismétlődés eseményindítóval](../connectors/connectors-native-recurrence.md)
* [Ismétlődő feladatok és munkafolyamatok létrehozása, ütemezése és futtatása a Csúszóablak-eseményindítóval](../connectors/connectors-native-sliding-window.md)
* [Munkafolyamatok szüneteltetése késleltetési műveletekkel](../connectors/connectors-native-delay.md)
