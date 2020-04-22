---
title: Az Azure Media Player beépülő modul galériája
description: Ez a cikk az Azure Media Player számára elérhető bővítmények listáját tartalmazza.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 206200f3ba0757cd738439b58c8b94874cf5a938
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726479"
---
# <a name="azure-media-player-plugin-gallery"></a>Az Azure Media Player beépülő modul galériája #

## <a name="plugins"></a>Beépülő modulok ##

| Bővítmény neve                         | Bemutató URL-címe                    | Forráskód                | Leírás    |
|-------------------------------------|-----------------------------|----------------------------|----------------|
| További funkciók                 | | | |
| **Új!** AMP360Videó                | [Bemutató](http://www.babylonjs.com/demos/amp360video/)                        | [GitHub](https://github.com/BabylonJS/Extensions/tree/master/Amp360Video)                     | A plugin lehetővé teszi, hogy láthatóvá 360 videó Amp akár az asztalon, vagy a VR kompatibilis eszközök. A teljes dokumentáció [itt](https://doc\.babylonjs\.com/extensions/amp360video)érhető el: |
|  Sprite tipp                         | [Bemutató](http://www.skymedia.tv/asset/sprite)                        | [GitHub](https://github.com/RickShahid/SpriteTip)                    | Azure Media Player (AMP) beépülő modul az Azure Media Services (AMS) Media Encoder Standard (MES) által létrehozott videó miniatűr képsprite idővonal-megjelenítéséhez. |
| Diagnosztikai átfedés                 | [Bemutató](https://openidconnectweb.azurewebsites.net/Diagnoverlay.html)                        | [GitHub](https://github.com/willzhan/diagnoverlay)                     | Ez a plugin megjeleníti: Minden kulcsfontosságú paraméterek, statisztika a videó, az összes esemény a videó lejátszási életciklus, és a DRM védelmi információk, mint például a kulcs azonosító, licenc megszerzése URL-eket, ha védett.                                                                                                                                                                      |
| Képkockasebesség és Időkód kalkulátor | [Bemutató](http://mconvertitest001.blob.core.windows.net/public/example.html)                        | [GitHub](https://github.com/mconverti/media-services-javascript-azure-media-player-framerate-timecode-calculator-plugin)                     | Ez a plugin kiszámítja a képkockasebesség videó alapján az `tfhd` / `trun` MP4 doboz az első MPEG-DASH videó töredék, elemzi az időskála értékét az MPEG-DASH ügyfél nyilvánvaló, és azt is lehetővé teszi, hogy létrehoz az időkód egy adott abszolút idő a játékos (valamint biztosítja a játékos abszolút idő adott időkód) |
| <strike>Lejátszási sebesség</strike>                      | [Bemutató](https://azure-samples.github.io/media-services-javascript-Azure-Media-Player-playback-rate-plugin/)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Ez a plugin lehetővé teszi a nézők számára, hogy ellenőrizzék, milyen sebességgel a videó. *Ne feledje, hogy ez a funkció automatikusan elérhető az AMP verzió 2.0.0+ verziójában, de alapértelmezés szerint le van tiltva.* Ha meg szeretné tudni, hogyan engedélyezheti, tekintse meg a [mintákat itt](https://github.com/Azure-Samples/azure-media-player-samples) |
| Rámutatási idő tippje                      | [Bemutató](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Időtippet jelenít meg az egér rámutatása kormutatón lévő folyamatjelző n az időpontos kereséshez. *Megjegyzés: Ez a plugin már integrálva van az AMP-be,* de ha érdekel, hogyan van programozva, nyugodtan vessen egy pillantást.                                                                                                                       |
| Cím fedvény                       | [Bemutató](https://azure-samples.github.io/media-services-javascript-azure-media-player-title-overlay-plugin/")                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-title-overlay-plugin)                     | Átfedések konfigurálható videó cím a képernyőn |
| Ütemterv jelölői                    | [Bemutató](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timelinemarkers/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-timeline-markers-plugin)                     | Ez a plugin vesz egy sor alkalommal, és overlays apró markerek át a haladás büfé azokban az időkben. |
| Elemzés                           | | | |
| Application Insights                | [Blogbejegyzést](https://azure.microsoft.com/blog/player-analytics-azure-media-player-plugin/)                   | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-application-insights-plugin)                     | Beépülő modul, amely nyomon követi a lejátszó mutatóit, és bekapcsolja a Power BI-hoz, hogy intuitív grafikus annektálta a megtekintők játékosélményét. |
| Google Analytics                    | N/A                         | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-google-analytics-plugin)                     | Google Analytics beépülő modul az Azure Media Player hez |
| Diagnosztika                         | | | |
| Diagnosztikai kimenet                  | [Bemutató](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/diagnosticslogger/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-diagnostic-logger-plugin)                     | Ez a plugin kimenetek egy sor diagnosztika a játékos, hogy ez a cselekvés megy a demo linket, és nyissa meg a JavaScript konzolt. |
| Könnyű hozzáférés                      | | | |
| Nagyítás                             | [Bemutató](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/zoom/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-zoom-plugin)                     | Ez a plugin megjeleníti a drag-able zoom-in skála a játékosok képernyőn, így a nézők nagyítani a tartalom |
| Élő feliratok                       | [Azure blogbejegyzés](https://azure.microsoft.com/blog/live-real-time-captions-with-azure-media-services-and-player/),[subply post](http://www.subply.com/en/Products/AzureLiveCaptions.htm) | N/A | *Lásd post további információkért.* End to End munkafolyamat, amelyet az Azure Media Player élő feliratozási beépített bővítményéhez terveztek, kattintson a bal szélső linre, és látogasson el az Alply webhelyére, és tudjon meg többet a megoldásról |
| Gyorsbillentyűk                            | <strike>[Bemutató](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)</strike>                        | <strike>[GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-hot-keys-plugin)</strike>                     | A gyorsbillentyűk plugin lehetővé teszi a nézők számára, hogy ellenőrizzék a különböző aspektusait a játékos általános plugin kombinációk, mint az F teljes képernyős, M némítás és nyilakkal a haladás sáv ellenőrzés. *Megjegyzés: Ez a plugin már integrálva amp, de nyugodtan használja, mint egy erőforrás* |
| Közösségi tartalom                              | | | |
| Megosztás                               | [Bemutató](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/share/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-social-share-plugin)                     | Ez a plugin hozzáad egy megosztás gombot a játékos vezérlősávjára, hogy a nézők megoszthassák a videót, amit néznek barátaikkal a Facebookon, a Twitteren vagy a Linkedinen keresztül. |

## <a name="next-steps"></a>További lépések ##

- [Az Azure Media Player rövid útmutatója](azure-media-player-quickstart.md)