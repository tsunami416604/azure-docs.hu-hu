---
title: Az Azure Media Player ismert problémái
description: Az aktuális kiadás a következő ismert problémákkal rendelkezik.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: ff8dc58b9122e5173a9a6065e2efdbc5697be0d7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727215"
---
# <a name="known-issues"></a>Ismert problémák #

Az aktuális kiadás a következő ismert problémákkal rendelkezik:

## <a name="azure-media-player"></a>Azure Media Player ##

- A helytelenül konfigurált kódolók problémákat okozhatnak a lejátszással
- A 2^53-nál nagyobb időbélyeggel rendelkező adatfolyamok lejátszási problémákkal fordulhatnak elő.
  - Kockázatcsökkentés: 90 kHz-es videó és 44,1 kHz-es hangidőskálák használata
- Nincs automatikus lejátszás a mobileszközökön felhasználói beavatkozás nélkül. A peron blokkolja.
- A majdnem megszakítások keresése lejátszási hibát okozhat.
- A nagy méretű bemutatók letöltése a felhasználói felület zárolását okozhatja.
- A bemutató befejezése után nem lehet automatikusan újra lejátszani ugyanazt a forrást.
  - A forrás újbóli lejátszásához a forrás befejezése után újra be kell állítani.
- Az üres jegyzékek problémákat okozhatnak a lejátszóval.
  - Ez a probléma akkor fordulhat elő, amikor egy élő közvetítés, és nem elég adattömbök találhatók a jegyzékben.
- Lejátszási pozíció talán a felhasználói felületen kívül.
- Az eseményrendezés nem minden technikusban egységes.
- A pufferelt tulajdonság nem egységes a technikusok között.
- A nativeControlsForTouch értékének hamisnak (alapértelmezettnek) kell lennie az "Objektum nem támogatja a tulajdonságot vagy a "setControls" metódust.
- A posztereknek most abszolút URL-eknek kell lenniük
- Kisebb esztétikai problémák merülhetnek fel a felhasználói felületen, ha az eszköz nagy kontrasztú üzemmódját használja
- A teljesen dekódolt karakterláncban "%" vagy "+" azonosítót tartalmazó URL-címek nem rendelkezhetnek a forrás beállításával

## <a name="ad-insertion"></a>Hirdetés beillesztése ##

- Előfordulhat, hogy a hirdetések behelyezése (igény szerint vagy élőben) merülfel, amikor hirdetésblokkoló van telepítve a böngészőben
- Előfordulhat, hogy a mobileszközöknél problémák merülnek fel a hirdetések lejátszásakor.

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- Az Élő tartalom DVR ablakában, amikor a tartalom befejeződik, az idővonal tovább fog nőni, amíg el nem éri a területet, vagy el nem éri a bemutató végét.
- Élő bemutatók a Firefox MSE engedélyezve van néhány kérdés
- A csak hang- vagy videoeszközök nem játszódnak le az AzureHtml5JS-technológián keresztül.
  - Ha hang vagy videó nélkül szeretné lejátszani az eszközöket, ezt az [Azure Media Services Explorer eszközével](https://aka.ms/amse) üres hang vagy videó beszúrásával teheti meg.
    - A néma hang beillesztéséhez [here](https://azure.microsoft.com/documentation/articles/media-services-advanced-encoding-with-mes/#silent_audio) itt található utasítások

## <a name="flash"></a>Flash ##

- Az AES-tartalom nem játszható le a Flash 30.0.0.134-es verziójában az Adobe gyorsítótárazási logikájának hibája miatt. Az Adobe kijavította a problémát, és kiadta a 30.0.0.154
- Technikai és http-hibák (például 404 hálózati időtúllépések), a játékos nak hosszabb időt kell igénybe vennie a helyreállításhoz, mint más technikusok.
- Kattintson a videó terület flashSS tech nem fogja lejátszani / szünet elanak a játékos.
- Ha a felhasználó telepítette a Flash-t, de nem ad engedélyt a betöltésre az oldalon, végtelen fonás fordulhat elő. Ez azért van, mert a játékos úgy gondolja, a plugin telepítve van, és elérhető, és úgy gondolja, a plugin fut a tartalom. JavaScript kódot küldtek, de a böngésző beállításai blokkolták a plugin végrehajtó, amíg a felhasználó elfogadja a kérdést, hogy a plugin. Ez minden böngészőben előfordulhat.  

## <a name="silverlight"></a>Silverlight ##

- Hiányzó funkciók
- Technikai és http-hibák (például 404 hálózati időtúllépések), a játékos nak hosszabb időt kell igénybe vennie a helyreállításhoz, mint más technikusok.
- A Safari és a Firefox Mac-en a `"http://` `https://` Silverlight-szal való lejátszáshoz kifejezetten meg kell határozni vagy a forrást kell meghatározni.
- Ha egy API hiányzik ehhez a technológiához, akkor általában null értéket ad vissza.
- Ha a felhasználó telepítette a Flash-t, de nem ad engedélyt a betöltésre az oldalon, végtelen fonás fordulhat elő. Ez azért van, mert a játékos úgy gondolja, a plugin telepítve van, és elérhető, és úgy gondolja, a plugin fut a tartalom. JavaScript kódot küldtek, de a böngésző beállításai blokkolták a plugin végrehajtó, amíg a felhasználó elfogadja a kérdést, hogy a plugin. Ez minden böngészőben előfordulhat.  

## <a name="native-html5"></a>Natív HTML5 ##

- A Html5 tech csak az első beállított forráshoz küld ionkeresztüli eseményt.
- Ez a technológia csak azt támogatja, amit a böngésző megvalósított.  Előfordulhat, hogy egyes funkciók hiányoznak ebben a technológiában.  
- Ha egy API hiányzik ehhez a technológiához, akkor általában null értéket ad vissza.
- Vannak problémák feliratok és feliratok ezen a tech. Lehet, hogy nem állnak rendelkezésre, vagy nem látható kontrások.
- Miután korlátozott sávszélesség HLS / Html5 tech forgatókönyv eredményeaudio lejátszás nélkül video.

### <a name="microsoft"></a>Microsoft ###

- Az IE8 lejátszás jelenleg nem működik az ECMAScript 5-tel való inkompatibilitás miatt
- Az IE és az Edge egyes verzióiban a teljes képernyős nem adható meg a gomb beadásával és kiválasztásával, vagy az F/f gyorsbillentyű használatával.

## <a name="google"></a>Google ##

- Az azonos jegyzékfájlban lévő több kódolási profilnak van néhány lejátszási problémája a Chrome-ban, és nem ajánlott.
- A Chrome nem tudja lejátszani a HE-AAC-t az AzureHtml5JS segítségével. Kövesse a részleteket a [hibakövetőn](https://bugs.chromium.org/p/chromium/issues/detail?id=534301).
- A Chrome v58-as részénél a widevine tartalmat a https:// protokollon keresztül kell betölteni/lejátszani, különben a lejátszás sikertelen lesz.

## <a name="mozilla"></a>Mozilla ##

- Az audioadatfolyam-váltáshoz hangadatfolyamok kell, hogy az AzureHtml5JS használatakor ugyanazok a kodek személyes adatok legyenek. A Firefox platform megköveteli ezt.

## <a name="apple"></a>Apple ##

- A Safari on Mac alapértelmezés szerint lehetővé teszi az energiatakarékos üzemmódot a "Plug-inek leállítása az energiatakarékosság érdekében" beállítással, amely blokkolja az olyan bővítményeket, mint a Flash és a Silverlight, ha úgy vélik, hogy nem támogatja a felhasználót. Ez a blokk nem blokkolja a plugin létezik, csak képességek. Az alapértelmezett techOrder miatt ez problémákat okozhat, amikor
  - 1. kockázatcsökkentés: Ha a videólejátszó "elöl és középen" van (a dokumentum bal felső sarkától számított 3000 x 3000 képponthatáron belül), akkor is le kell játszania.
  - 2. kockázatcsökkentés: Módosítsa a safari techOrder-ét ["azureHtml5JS", "html5") Ez a mérséklés azzal jár, hogy nem kapja meg a FlashSS-technológiában elérhető összes funkciót.
- A Silverlighton keresztüli PlayReady-tartalmak lejátszása problémákat okozhat a Safariban való lejátszással kapcsolatban.
- Az AES és a korlátozott tokentartalom nem játszható le iOS és régebbi Android-eszközökhasználatával.
  - A forgatókönyv eléréséhez egy proxyt kell hozzáadni a szolgáltatáshoz.
- Az iOS-telefon alapértelmezett felszíne megjelenik.
- az iPhone lejátszása mindig a natív lejátszó teljes képernyőjén történik.
  - Előfordulhat, hogy a funkciók, beleértve a feliratokat is, nem maradnak meg ebben a nem inline lejátszásban.
- Amikor az élő bemutató véget ér, az iOS-eszközök nem fejezik be megfelelően a bemutatót.
- Az iOS nem teszi lehetővé a DVR képességeit.
- Az iOS audiostream kapcsoló csak az iOS natív lejátszó felhasználói felületén végezhető el, és az audio streameknek ugyanazokkal a kodek személyes adatokkal kell rendelkezniük
- A Safari régebbi verziói nak problémái lehetnek az élő közvetítések lejátszásával.

## <a name="older-android"></a>Régebbi Android ##

- Az AES és a korlátozott tokentartalom nem játszható le iOS és régebbi Android-eszközökhasználatával.
  - A forgatókönyv eléréséhez egy proxyt kell hozzáadni a szolgáltatáshoz.

## <a name="next-steps"></a>További lépések ##

- [Az Azure Media Player rövid útmutatója](azure-media-player-quickstart.md)