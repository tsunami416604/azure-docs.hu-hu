---
title: Cortana előre elkészített app hivatkozhat |} Microsoft Docs
description: A Cortana személyes Segéd, egy előre elkészített alkalmazást a nyelvi ismertetése intelligens szolgáltatások (LUIS) hivatkozás.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: cd808c30a6781fc0252992c13ba247486e35ad44
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347234"
---
# <a name="cortana-prebuilt-app-reference"></a>A Cortana előre elkészített app hivatkozik
Ez a hivatkozás a leképezések és a Cortana előre elkészített alkalmazás felismeri entitások sorolja fel.

## <a name="cortana-prebuilt-intents"></a>A Cortana előre elkészített leképezések
Az előre elkészített személyes Segéd alkalmazás a következő leképezések képes azonosítani:

| Kísérlet történt | Példa utterances |
|--------| ------------------|
| Builtin.Intent.Alarm.alarm_other|a 7:30 frissíteni kell nyolc óra riasztás <br/>Módosítsa a riasztás 8 am értékről a reggel 9 |
| Builtin.Intent.Alarm.delete_alarm|egy riasztás törlése <br/>Törölje a riasztás "felébreszteni"|
| Builtin.Intent.Alarm.find_alarm|milyen a ébresztési riasztás időtartamot számára? <br/> az ébresztési riasztás van? |
| Builtin.Intent.Alarm.set_alarm|az ébresztési riasztás bekapcsolása<br/>állíthatja be egy riasztás nevű 12 tartani, antibiotikumok?|
| Builtin.Intent.Alarm.snooze|Emlékeztető riasztás 5 perc<br/>Emlékeztető riasztás|
| Builtin.Intent.Alarm.time_remaining| milyen mértékű már van "ébresztési" amíg? <br/> mennyi idő, amíg a következő riasztás?|
| Builtin.Intent.Alarm.turn_off_alarm | Kapcsolja ki a reggel riasztás <br/> Kapcsolja ki a ébresztési riasztás |
| Builtin.Intent.Calendar.change_calendar_entry| egy időpontot egyeztessen módosítása<br/>értekezlet a mai naptól áthelyezése holnap|
|Builtin.Intent.Calendar.check_availability|az tim foglalt péntekén?<br/>szabad szombaton brian van|
|Builtin.Intent.Calendar.connect_to_meeting|értekezlet kapcsolódni<br/>Csatlakozás a hálózatra|
|Builtin.Intent.Calendar.create_calendar_entry|Kell egy egyeztessen a tony a péntek<br/>Ellenőrizze egy időpontot egyeztessen az lisa 2 du. vasárnap|
|Builtin.Intent.Calendar.delete_calendar_entry|a időpontot egyeztessen törlése<br/>Távolítsa el az értekezlet du. 3: Holnap naptár|
|Builtin.Intent.Calendar.find_calendar_entry|a naptár megjelenítése<br/>a heti naptár megjelenítése|
|Builtin.Intent.Calendar.find_calendar_when|Ha a következő megfelel-e a jon?<br/>milyen ideje a időpontot egyeztessen az Endre hétfőn?|
|Builtin.Intent.Calendar.find_calendar_where|jelenjen meg a 6 óra értekezlet helye<br/>hol található az adott értekezlet jon?|
|Builtin.Intent.Calendar.find_calendar_who|ki van, az értekezlet?<br/>ki másnak felkérik?|
|Builtin.Intent.Calendar.find_calendar_why|Mik a 11 óra értekezlet részleteit?<br/>Mi az, hogy a jon kapcsolatos értekezlet?|
|Builtin.Intent.Calendar.find_duration|milyen hosszú legyen a következő értekezlet<br/>hány perc hosszú az értekezlet a ez délután|
|Builtin.Intent.Calendar.time_remaining|mennyi idő, amíg a következő időpontot egyeztessen?<br/>milyen mértékű több időre van az értekezlet előtt?|
|Builtin.Intent.Communication.add_contact|Mentse ezt a számot, és helyezze a neve, ahogyan jose<br/>jason be a Névjegyek listára|
|Builtin.Intent.Communication.answer_phone|válasz<br/>a hívás|
|Builtin.Intent.Communication.assign_nickname|a nickolas becenév szerkesztése<br/>john doe becenév hozzáadása|
|Builtin.Intent.Communication.call_voice_mail|Hangposta<br/>hívás hangposta|
|Builtin.Intent.Communication.find_contact|Saját kapcsolatok megjelenítése<br/>ügyfelek keresése|
|Builtin.Intent.Communication.forwarding_off|a hívások továbbítani<br/>Kapcsolja ki az átirányítás|
|Builtin.Intent.Communication.forwarding_on|otthoni telefon hívásainak küldendő átirányítás beállítása<br/>előre hívások otthoni telefon|
|Builtin.Intent.Communication.ignore_incoming|a hívás nem válaszol<br/>most már nem vagyok foglalt|
|Builtin.Intent.Communication.ignore_with_message|a hívás nem válaszol, de ehelyett elküldeni az üzenetet<br/>elküldheti vissza egy szöveges|
|Builtin.Intent.Communication.make_call|bob és john meghívása<br/>mom és dad meghívása|
|Builtin.Intent.Communication.press_key|telefonos kapcsolat csillag<br/>nyomja meg az 1-2 3|
|Builtin.Intent.Communication.read_aloud|olvassa el a szöveg<br/>Mi volt ő fel az üzenet|
|Builtin.Intent.Communication.redial|az utolsó hívás Újratárcsázás<br/>Újratárcsázás|
|Builtin.Intent.Communication.send_email|e-mailek mike vizek nagy, hogy splendid volt-e múlt héten vacsora<br/>Belinszky e-mail küldése|
|Builtin.Intent.Communication.send_text|bob és john szöveg küldése<br/>message|
|Builtin.Intent.Communication.speakerphone_off|Ugrás a hangalapú kikapcsolása<br/>Kapcsolja ki a kihangosító|
|Builtin.Intent.Communication.speakerphone_on|kihangosító mód<br/>kihangosító elhelyezése|
|Builtin.Intent.MyStuff.find_attachment|a dokumentum john e-mailben kérek tegnap keresése <br/>john talál, a dokumentumot|
|Builtin.Intent.MyStuff.find_my_stuff|A vásárlási listáját a következőtől: tegnap módosítani<br/>a kémia megjegyzéseket talál,. szeptember
|Builtin.Intent.MyStuff.search_messages|Nyissa meg az üzenet <br/>üzenet|
|Builtin.Intent.MyStuff.transform_my_stuff|a vásárlási lista megosztását a férj<br/>a vásárlási lista törlése|
|Builtin.Intent.ondevice.open_setting|Nyissa meg a cortana beállításait <br/>Ugrás az értesítések|
|Builtin.Intent.ondevice.pause|zene kikapcsolása<br/>zene kikapcsolása|
|Builtin.Intent.ondevice.play_music|Szeretném hall magányos szív tulajdonosa<br/>Néhány gospel zene lejátszása|
|Builtin.Intent.ondevice.recognize_song|arról, hogy mi az a szám<br/>elemezze és dal címét beolvasása|
|Builtin.Intent.ondevice.REPEAT|Ismételje meg a nyomon követése<br/>Ez lejátszása|
|Builtin.Intent.ondevice.Resume|Indítsa újra a zene<br/>Indítsa el újból a zene|
|Builtin.Intent.ondevice.skip_back|Készítsen biztonsági másolatot a nyomon követése<br/>előző|
|Builtin.Intent.ondevice.skip_forward|következő dal<br/>hagyja ki azokat, amelyek egy nyomon követése|
|Builtin.Intent.ondevice.turn_off_setting|Wi-Fi kikapcsolása<br/>repülőgép üzemmód letiltása|
|Builtin.Intent.ondevice.turn_on_setting|a Wi-Fi<br/>bluetooth bekapcsolása|
|Builtin.Intent.Places.add_favorite_place|Ez a cím felvétele a Kedvencek<br/>Ezen a helyen mentése a Kedvencek közé|
|Builtin.Intent.Places.book_public_transportation|a vonat jegy megvásárlása<br/>könyv egy villamoson fázis|
|Builtin.Intent.Places.book_taxi|tudhatja meg egy, az óra menethelyzetben?<br/>egy taxi keresése|
|Builtin.Intent.Places.check_area_traffic|Mi az az adatforgalmat például 520<br/>hogyan van a forgalom i-5|
|Builtin.Intent.Places.check_into_place|Ellenőrizze a joya<br/>Jelölje be|
|Builtin.Intent.Places.check_route_traffic|jelenítse meg a kirkland módja lévő a forgalom<br/>hogyan van a forgalom Budapest?|
|Builtin.Intent.Places.find_place|Ha közvetít élő <br/>Adja meg az első 3 japán éttermekben|
|Builtin.Intent.Places.get_address|jelenítse meg a címe guu lévő robson utca<br/>Mi az a legközelebbi starbucks címét?|
|Builtin.Intent.Places.get_coupon|a területen található illeszkedő kereskedelmi ajánlatokat televízió<br/>kedvezményeket hegyi nézetben|
|Builtin.Intent.Places.get_distance|milyen távolságra van szünnap inn?<br/>Mi az a távolság tahoe|
|Builtin.Intent.Places.get_hours|Mik azok a sáv del corso óra hétfőn?<br/>Ha a szalagtár nyitva van?|
|Builtin.Intent.Places.get_menu|applebee tartozó menü megjelenítése<br/>Mi az a menü sizzler:|
|Builtin.Intent.Places.get_phone_number|meg kell adni a szám otthoni raktár számára<br/>Mi az a legközelebbi starbucks telefonszámát?|
|Builtin.Intent.Places.get_price_range|Mennyibe piros homár költségekkel vacsora<br/>Hogyan költséges lila bal van|
|Builtin.Intent.Places.get_reviews|helyi hardver tárolóinak értékelést megjelenítése<br/>Ellenőrzi, hogy éttermi megtekintése|
|Builtin.Intent.Places.get_route|Adja meg az utasításokat|lehetséges, a mértékű kiszolgálóhasználat pizzaszósz hut eléréséhez|
|Builtin.Intent.Places.get_star_rating|hány csillag rendelkezik a francia mosnivaló?<br/>a monterrey akvárium jó van?|
|Builtin.Intent.Places.get_transportation_schedule|mikor nem a san francisco komphajó hagyja?<br/>Ha az a legújabb vonat Budapest?|
|Builtin.Intent.Places.get_travel_time|Mi ú való jelenti az vezetői idő<br/>mennyi ideig tart a innen san francisco eléréséhez|
|Builtin.Intent.Places.make_call|a Dr. smith bellevue hívás<br/>1. utca hívható meg az otthoni raktár számára|
|Builtin.Intent.Places.rate_place|Adjon egy ez éttermi besorolását<br/>értékelje il fornaio 5 csillagos yelp|
|Builtin.Intent.Places.show_map|Mi az az aktuális hely <br/>Mi az a hely|
|Builtin.Intent.Places.takes_reservations|van lehetőség: a olajbogyó kerti lefoglalni úgy<br/>fogadja el foglalások a galéria|
|Builtin.Intent.Reminder.change_reminder|Emlékeztető módosítása<br/>a kép nap emlékeztető feljebb 30 perc|
|Builtin.Intent.Reminder.create_single_reminder|Emlékeztessen 7 órakor felébresztéséhez<br/>Emlékeztessen go trükk vagy du. 4:40: luca való kezelésére|
|Builtin.Intent.Reminder.delete_reminder|Ezt az emlékeztetőt törlése<br/>a kép emlékeztető törlése|
|Builtin.Intent.Reminder.find_reminder|van bármilyen beállított emlékeztetők még ma<br/>luca tartozó fél beállítása emlékeztető van|
|Builtin.Intent.Reminder.read_aloud|Emlékeztető Hangos olvasás<br/>olvassa el, hogy emlékeztető|
|Builtin.Intent.Reminder.snooze|adott emlékeztető emlékeztet holnap-ig<br/>Ezt az emlékeztetőt emlékeztet|
|Builtin.Intent.Reminder.turn_off_reminder|Emlékeztető kikapcsolása<br/>hagyja figyelmen kívül repülőtéri felvételi emlékeztető|
|Builtin.Intent.weather.change_temperature_unit|Módosítsa a fahrenheit kelvin<br/>Módosítsa a fahrenheit celsius|
|Builtin.Intent.weather.check_weather|jelenjen meg a jövőbeli út előrejelzését Budapest<br/>hogyan fogja a időjárási a hétvégi|
|Builtin.Intent.weather.check_weather_facts|Mi az a időjárási, például a hawaii december?<br/>ki volt a hőmérséklet ezúttal az elmúlt évben?|
|Builtin.Intent.weather.compare_weather|Adja meg a magas hőmérséklet és dallas és houston, tx lows összehasonlítása<br/>hogyan hasonlítsa össze a a időjárási slc és nyc|
|Builtin.Intent.weather.get_frequent_locations|Adja meg a leggyakoribb helye<br/>leggyakrabban leállítja megjelenítése|
|Builtin.Intent.weather.get_weather_advisory|időjárás-figyelmeztetések<br/>időjárás Sacramentói tanácsadó megjelenítése|
|Builtin.Intent.weather.get_weather_maps|időjárás-térképet<br/>időjárás maps Afrika megjelenítése|
|Builtin.Intent.weather.question_weather|lesz foggy holnap reggel?<br/>Módosítanom kell shovel hó a hétvégi?|
|Builtin.Intent.weather.show_weather_progression|helyi időjárási sugárdiagram megjelenítése<br/>Sugárdiagram megkezdése|
|Builtin.Intent.None|mennyi idős vagy<br/>Nyissa meg a kamera|

## <a name="prebuilt-entities"></a>Előre elkészített entitások 

Íme néhány példa a entitások az előre elkészített személyes Segéd alkalmazás képes azonosítani:

|Entitás |Entitás utterance – példa |
|-------|------------------|
|Builtin.Alarm.alarm_state | Kapcsolja be `off` a ébresztési riasztás    <br/> az ébresztési riasztás van `on`  | 
|Builtin.Alarm.DURATION |a emlékeztető `10 minutes`    <br/> Emlékeztető riasztás `5 minutes`  |
|Builtin.Alarm.start_date | Állítsa be a riasztót olyan esetben `monday` éjfélre (7)   <br/> Állítsa be a riasztót olyan esetben `tomorrow` órakor   |
|Builtin.Alarm.start_time | egy riasztás létrehozása `30 minutes`    <br/> Lépjen a riasztás beállítása `in 20 minutes`   |
|Builtin.Alarm.Title | van a `wake up` a riasztás <br/> negyedév és 12 hétfőtől péntekig nevű is beállíthatja egy riasztás `take antibiotics` |
|Builtin.Calendar.absolute_location | Hozzon létre egy időpontot egyeztessen az holnap: `123 main street`   <br/> az értekezlet akkor kerül sor `cincinnati` meg az 5. a június    |
|Builtin.Calendar.contact_name|a naptár marketing értekezlet helyezze, és ügyeljen arra, hogy `joe` van-e <br/>Állítson be egy ebédet il fornaio, és meghívása `paul` |   
|Builtin.Calendar.destination_calendar|Adja hozzá ezt a saját `work` ütemezése   <br/>másképp fogalmazva saját `personal` naptár |
|Builtin.Calendar.DURATION| Állítson be egy időpontot egyeztessen az `an hour` esti 6 <br/>  könyv egy `2 hour` joe értekezlet |  
|Builtin.Calendar.end_date | létrehoz egy naptári bejegyzést, amíg holnap szabadsága hívása `next monday` <br/>    az idő, amíg nem foglalt letiltása `monday, october 5th` | 
|Builtin.Calendar.end_time | Értekezlet vége `5:30 PM` <br/> az ütemezés a 11 `noon`  |   
|Builtin.Calendar.implicit_location | Szakítsa meg a időpontot egyeztessen a dmv: <br/> miles' születési való helyének módosítása `poppy restaurant` |    
|Builtin.Calendar.move_earlier_time | leküldéses továbbítás értekezlet `an hour` <br/> a dentist időpontot egyeztessen mozgatása felfelé `30 minutes`       |
|Builtin.Calendar.move_later_time | Helyezze át a dentist időpontot egyeztessen `30 minutes`    <br/> az értekezlet leküldéses `an hour` |  
|Builtin.Calendar.original_start_date | ütemezze újra a időpontot egyeztessen a barber a "Kedd" szerdáig: <br/> Helyezze át a ken az értekezlet a `monday` való kedd |
|Builtin.Calendar.original_start_time | ütemezze újra a értekezletet `2:00` 3  <br/> Módosítsa a dentist időpontot egyeztessen az `3:30` 4 |  
|Builtin.Calendar.start_date | mikor indul a fél a `flag day` <br/> ebéd ütemezni a `friday after next` órakor 
|Builtin.Calendar.start_time | Az ütemezése `this morning` <br/> Az ütemezés a kívánt a `morning` |  
|Builtin.Calendar.Title | `vet appointment`  <br/> `dentist` Kedd |
|Builtin.Communication.audio_device_type | a hívás használata érdekében `bluetooth`  <br/> hívás használatával saját `headset` | 
|Builtin.Communication.contact_name | szöveg `bob jones`  <br/> | hívás `sarah`|
|Builtin.Communication.destination_platform | dave hívás `london` <br/> a hívás `work line` |    
|Builtin.Communication.from_relationship_name | a hívások megjelenítése a `daughter` <br/> az e-mail olvasása `mom`   |   
|Builtin.Communication.key | telefonos kapcsolat `star` <br/>  nyomja meg a `hash` kulcs    |
|Builtin.Communication.Message | e-mailek carly, azaz `i'm running late` <br/> szöveg angus smith `good luck on your exam` |    
|Builtin.Communication.message_category | új e-mail megjelölve `follow up`  <br/> új e-mail megjelölve `high priority` |    
|Builtin.Communication.message_type | küldjön egy `email`   <br/> olvasási én `text` hangosan üzenetek |
|Builtin.Communication.phone_number | Tárcsázza a kívánt `1-800-328-9459` <br/>     hívás `555-555-5555` |   
|Builtin.Communication.relationship_name | szöveg a `husband` <br/>  e-mailek `family`| 
|Builtin.Communication.slot_attribute | Módosítsa a `recipient` <br/>    Módosítsa a `text` | 
|Builtin.comminication.source_platform | hívja az őt `skype` <br/> hívja az őt a `personal line` |
|Builtin.MyStuff.Attachment| a dokumentumok `attached` <br/> az e-mailben található `attachment` küldött bob |
|Builtin.MyStuff.contact_name| a táblázat lisa küldött keresése `me` <br/> Ha a dokumentum i kap `susan` utolsó éjszakai |
|Builtin.MyStuff.data_source | `c:\dev\` <br/> a `desktop` <br/> |`"resolution": { "resolution_type": "metadataItems", "metadataType": CanonicalEntity", "value": "desktop" }`|
|Builtin.MyStuff.DATA_TYPE | Keresse meg a `document` i utolsó éjszakai dolgozott. <br/> |`"resolution": {"resolution_type": "metadataItems", "metadataType": "CanonicalEntity", "value":Document" }` <br/> nagy meg elindítani `visio diagram`  |
|Builtin.MyStuff.end_date| a dokumentumok i dolgozott megjelenítése `between yesterday and today`   <br/> milyen doc i dolgozott keresése `before thursday the 31st` |
|Builtin.MyStuff.end_time| található fájlok i mentése `before noon` <br/> milyen doc i dolgozott keresése `before 4pm`|      
|Builtin.MyStuff.file_action| Nyissa meg a táblázat i `saved` tegnap <br/> a táblázatban található kevin `created`| 
|Builtin.MyStuff.from_contact_name | a javaslat található `jason` küldött nekem <br/> Nyissa meg a `isaac` tartozó utolsó e-mail |
|Builtin.MyStuff.Keyword | jelenjen meg a `french conjugation` fájlok <br/> Keresés a `marketing plan` i elkészíteni tegnap |
|Builtin.MyStuff.Location | a dokumentum i szerkeszti `work` <br/> fényképek i került `paris` |
|Builtin.MyStuff.message_category | Keresse meg a `new` e-mailek <br/> Keresse meg a `high priority` e-mail |
|Builtin.MyStuff.message_type | Ellenőrizze a `email` <br/>  jelenjen meg a `text` üzenetek  |
|Builtin.MyStuff.source_platform | Keresés a `hotmail` az e-mailek john az e-mailek    <br/> Keresés a dokumentum i küldött `work` |
|Builtin.MyStuff.start_date | a megjegyzések keresése `january` <br/> az e-mailt i küldése rob keresése `after january 1st` |
|Builtin.MyStuff.start_time | Keresse meg, hogy az e-mail i küld egy kis ideig előtt 2 pm rob, de `after noon`? <br/> Keresés a munkalap kristin küldött nekem szerkesztése `last night` | 
|Builtin.MyStuff.Title | `c:\dev\mystuff.txt` <br/> `*.txt` |
|Builtin.MyStuff.transform_action | `download` a fájl john küldött nekem <br/> `open` a jegyzet irányelvek doc |
|Builtin.note.note_text | Hozzon létre egy élelmiszerboltban lista többek között `pork chops, applesauce and milk` <br/> Ügyeljen arra, hogy `buy milk` |
|Builtin.note.Title | Jegyezze fel neve `grocery list` <br/> Jegyezze fel neve `people to call` |
|Builtin.ondevice.music_artist_name | minden által lejátszása `rufus wainwright` <br/> lejátszási `garth brooks` zene |   
|Builtin.ondevice.music_genre | megjelenítése `classic rock` zeneszámok <br/> Play saját `classical` zene baroque időszak |
|Builtin.ondevice.music_playlist | az összes britney spears sorrendű `workout` lista <br/> lejátszási `breakup` lista
|Builtin.ondevice.music_song_name | Play `summertime` <br/> Play `me and bobby mcgee` | 
|Builtin.ondevice.setting_type | `quiet hours` <br/> `airplane mode` |
|Builtin.Places.absolute_location | Ugrás a metszetét `5th and pike` <br/> nem szeretném utasításokat `1 microsoft way redmond wa 98052` |
|Builtin.Places.Atmosphere | Keressen `interesting` helyek nyissa a    <br/> hol található a `casual` éttermi |  
|Builtin.Places.audio_device_type |a postai hívható meg `hands free` <br/> hívás papa john a `speakerphone` |    
|Builtin.Places.avoid_route | Kerülje a `toll road` <br/> me eléréséhez san francisco elkerülve a `construction on 101` |  
|Builtin.Places.cuisine | `halal` szendvicsbár közelében hegyi megtekintése   <br/> `kosher` azokat a félszigeten étkezési lehetőségek |
|Builtin.Places.Date | a foglalás létrehozása `next friday the 12th` <br/>  mashiko meg van nyitva a `mondays` ? |
|Builtin.Places.discount_type | Keresés a `coupon` macy meg az <br/> megtaláljanak egy `coupon` |
|Builtin.Places.Distance | egy jó diner van `within 5 miles` az itt <br/> Keresés állók közül. `within 15 miles` |   
|Builtin.Places.from_absolute_location | irányban `45 elm street` a kezdőlapra <br/> első me irányban `san francisco` palo alto számára  |
|Builtin.Places.from_place_name | a vezetés a `post office` 56 center utca számára <br/> első me irányban `home depot` lowes számára |
|Builtin.Places.from_place_type | a New York utasításokat `work` <br/>  első me irányban a `drug store` a kezdőlapra |
|Builtin.Places.meal_type | Ha a közeli helyeken `dinner` <br/> a vállalati remek keresése `lunch` | 
|Builtin.Places.Nearby | bizonyos ritkán használt adatok üzletekben megjelenítése `near` nekem  <br/> vannak-e minden helyes Libanoni éttermekben `around` Itt? |
|Builtin.Places.open_status | Ha van a kis `closed` <br/> me beolvasása a `opening` a tároló üzemideje (óra) | 
|Builtin.Places.place_name | Ugrás a számára `central park` <br/> Keresse meg a `eiffel tower` |   
|Builtin.Places.place_type | `atms` <br/> `post office` |
|Builtin.Places.prefer_route | által irányban megjelenítése a `shortest` útvonal <br/> igénybe vehet a `fastest` útvonal | 
|Builtin.Places.price_range| Adja meg azok a helyek `moderately affordable` <br/>  Szeretném egy `expensive` egy   |
|Builtin.Places.Product | Hol kaphatok `fresh fish` körül Itt  <br/> Ha itt által forgalmazott körül `bare minerals` |
|Builtin.Places.public_transportation_route | BUS ütemezését a `m2` busz <br/> BUS útvonal `3x` |  
|Builtin.Places.rating | megjelenítése `3 star` éttermekben <br/> eredmények, amelyek megjelenítése `3 stars or higher`  |
|Builtin.Places.reservation_number | a táblát, le `seven` személyek <br/>  Ellenőrizze a foglalást `two` il fornaio: |
|Builtin.Places.results_number | jelenjen meg a `10` itt legközelebbi kávézókban <br/> jelenjen meg felső `3` akváriumokban  
|Builtin.Places.service_provided | ahol lehet ugrani `whale watch` által bus? <br/> Egy szerelő van szükségem `fix my brakes` |    
|Builtin.Places.Time | Szeretném a helyeken, nyissa meg a következő szombat `8 am`| Nyissa meg az mashiko `now` |
|Builtin.Places.transportation_company | az ütemezések képzése `new jersey transit` <br/> kaphatok van `bart` | 
|Builtin.Places.transportation_type | hol található a zeneáruház i el tudja érni `on foot`? <br/>  Adja meg `biking` mashiko utasításokat|
|Builtin.Places.travel_time | Szeretnék tudni meghajtó `less than 15 minutes` <br/>   Szeretném valahol I el tudja érni a `under 15 minutes` |   
|Builtin.Reminder.absolute_location | a dad hívására i megnyílik a Emlékeztessen `chicago` <br/> Amikor lehet visszaállítani a `seattle` Emlékeztessen gáz eléréséhez |
|Builtin.Reminder.contact_name | Amikor `bob` hívások, emlékeztessen mondja el neki a vicc <br/> Hozzon létre egy emlékeztető az iskolai bus felhívja, ha szeretnék konzultáljon `arthur` |
|Builtin.Reminder.leaving_absolute_location | Emlékeztető átvételéhez craig elhagyásakor `1200 main` |
|Builtin.Reminder.leaving_implicit_location | Emlékeztessen gáz lekérdezni, amikor elhagyása `work`  |
|Builtin.Reminder.original_start_date | Módosítsa a gyepvágó vonatkozó felszólítás `saturday` vasárnapig <br/> Helyezze át a emlékeztető az iskolai kapcsolatos `monday` való kedd   |
|Builtin.Reminder.relationship_name | Ha a `husband` hívások, emlékeztessen adja neki a ESP értekezlet <br/> Figyelmeztessen amikor `mom` hívások|
|Builtin.Reminder.reminder_text | lehet, emlékeztessen `bring up my small spot of patchy skin` amikor vész-helyreállítási smith'calls  <br/> Emlékeztető `pick up dry cleaning` 4:40:   |
|Builtin.Reminder.start_date | Emlékeztessen a `thursday after next` 8 du.:  <br/> Emlékeztessen `next thursday the 18th` 8 du.:    |
|Builtin.Reminder.start_time | Hozzon létre egy emlékeztető `in 30 minutes` <br/> a tőzsdei árfolyamjelző víznek emlékeztető létrehozása `this evening at 7` |  
|Builtin.weather.absolute_location | fogja azt a eső `boston` <br/> Mi az az előrejelzés `seattle`?  |
|Builtin.weather.date_range | időjárás a következőt: `this weekend` <br/>   Keresse meg a `five day` hollywood előrejelzés hogy |
|Builtin.weather.suitable_for | is lépjen `hiking` a sort a hétvégi?   <br/> lesz elég töltött ahhoz, hogy `walk` a játékba ma? | 
|Builtin.weather.temperature_unit | Mi az a hőmérséklet ma `kelvin`   <br/> a temps megjelenítése `celsius` |  
|Builtin.weather.time_range | nem, keresse meg például azt fogja hó `tonight`? <br/> Ennyi az windy jobb `now`?  |
|Builtin.weather.weather_condition | megjelenítése `precipitation` <br/> Hogyan vastag van a `snow` : lake tahoe most?  |

