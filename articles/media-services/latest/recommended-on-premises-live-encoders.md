---
title: Ismerje meg az élő adatfolyam-továbbítási helyszíni kódolókkal történő továbbítását a Media Services – Azure által ajánlott |} A Microsoft Docs
description: További tudnivalók a Media Services által ajánlott élő streamelési helyszíni kódolók
services: media-services
keywords: kódolás; kódolók; adathordozó
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 01/17/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: c3e42ba9fe84ded8c60fc71f19de785945852116
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55656668"
---
# <a name="recommended-live-streaming-encoders"></a>Ajánlott élő streamelési kódolók

A Media Services szolgáltatásban egy [élő esemény](https://docs.microsoft.com/rest/api/media/liveevents) (csatorna) egy olyan folyamatot jelent feldolgozása live-streaming-tartalmat. Az élő esemény élő bemeneti streamekhez kap a két módszer egyikével:

* Egy helyszíni élő kódoló küld az élő eseményt nincs engedélyezve a valós idejű kódolás a Media Services adatfolyam egy többszörös sávszélességű RTMP vagy Smooth Streaming (darabolt MP4). Élő események továbbítja a feldolgozott adatfolyamok további feldolgozás nélkül. Ezt a metódust meghívják **átmenő**. Az élő kódolók olyan egy egyféle sávszélességű adatfolyamot küldeni a csatorna továbbítása, de ez a konfiguráció nem ajánlott, mert nem teszi lehetővé az adaptív sávszélességű streamelés az ügyfélnek.

  > [!NOTE]
  > Valamely áteresztő módszer használata a leggazdaságosabb élő közvetítést végezni.

* A helyszíni élő kódoló egy egyféle sávszélességű adatfolyamot küld az élő esemény, amely engedélyezve van a valós idejű kódolás a Media Services a következő formátumok egyikében: RTMP vagy Smooth Streaming (darabolt MP4). Az élő esemény végez a bejövő egyszeres átviteli sebességű streamet, és többféle sávszélességű (adaptív) video-adatfolyamot élő kódolás.

Valós idejű kódolás a Media Services használatával kapcsolatos részletes információkért lásd: [élő adások online közvetítése a Media Services v3](live-streaming-overview.md).

## <a name="live-encoders-that-output-rtmp"></a>Élő kódolók képesek RTMP-kimenetre

A Media Services RTMP kimenetként rendelkező következő élő kódolók egyikének használatát javasolja. A támogatott URL-sémák a `rtmp://` vagy `rtmps://`.

> [!NOTE]
 > Streamelési RTMP-n keresztül, amikor ellenőrizze a tűzfallal és/vagy a proxy beállításait, győződjön meg arról, hogy 1935 és 1936 kimenő TCP-portok nyitva-e.<br/>
 Streamelési RTMPS keresztül, amikor ellenőrizze a tűzfallal és/vagy a proxy beállításait, győződjön meg arról, hogy 2935 és 2936 kimenő TCP-portok nyitva-e.

- Az Adobe Flash Media Live Encoder 3.2-es verzióját
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast 8.1 +
- Telestream Wirecast S
- Teradek szelet 756
- A 8000-es TriCaster
- Tricaster Mini HD-4
- VMIX
- xStream

## <a name="live-encoders-that-output-fragmented-mp4"></a>Élő kódolók képesek kimeneti töredékes MP4

A Media Services a következő élő kódolók többféle sávszélességű Smooth Streaming (töredékes-MP4) kimenetként rendelkező egyikének használatát javasolja. A támogatott URL-sémák a `http://` vagy `https://`.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 csomag általános III.
- Az Imagine Communications Selenio MCP3
- Media Excel élő Hero és a Hero 4 KB-os (UHD/HEVC)

## <a name="configuring-on-premises-live-encoder-settings"></a>Konfigurálása a helyszíni élő kódoló beállításai

Milyen beállítások érvényesek az élő esemény típus kapcsolatos információkért lásd: [élő esemény-típusok összehasonlítása](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Lejátszási követelmények

Mind az audio- és stream lejátszás tartalom sorrendben elérhetőnek kell lennie. A csak a videó Stream lejátszás nem támogatott.

### <a name="configuration-tips"></a>Konfigurációs tippek

- Amikor csak lehetséges, hardveresen rögzített beállítású internet kapcsolat használatára.
- Jó tapasztalatok forrássávszélesség követelményeinek meghatározásakor, hogy a streamelési bitsebességre való átkódolása duplán. Bár ez nem kötelezők, segít a hálózati torlódás hatásainak mérséklése érdekében.
- Ha-alapú kódolók szoftvert használ, zárja be a felesleges programokat ki.
- A kódoló beállításai nem változnak, miután elindult, hogyan lehet továbbítani rá. Ez az esemény negatív hatással van, és okozhat az eseményt, ezért instabil lehet. 
- Győződjön meg arról, állíthatja be az esemény bőséges időt biztosíthat magának. Nagy méretű események, ajánlott egy újabb órájába az esemény előtt a telepítő elindításához.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Hogyan válhat egy helyszíni kódolót partner

Egy Azure Media Services a helyszíni kódolót partnerként a Media Services a termék elősegíti a vállalati ügyfelek számára a kódoló javaslatot. Egy helyszíni kódolót partner szeretne lenni, ellenőriznie kell a helyszíni kódolót kompatibilitását a Media Services használatával. Ehhez hajtsa végre a következő ellenőrzések:

### <a name="pass-through-live-event-verification"></a>Az átmenő Live Event ellenőrzése

1. A Media Services-fiók, ellenőrizze, hogy a **folyamatos átviteli végponton** fut-e. 
2. Hozzon létre, és indítsa el a **átmenő** élő esemény. <br/> További információkért lásd: [élő esemény állapotok és számlázási](live-event-states-billing.md).
3. A betöltés URL-címe és a konfigurálása a helyszíni kódolót, az URL-cím segítségével szeretne küldeni a Media Services élő stream többszörös átviteli sebességű.
4. Előnézeti URL-címére, és a segítségével ellenőrizheti, hogy a kódoló a bemeneti ténylegesen fogadja.
5. Hozzon létre egy új **eszköz** objektum.
6. Hozzon létre egy **élő kimeneti** , és használja az Ön által létrehozott objektum nevét.
7. Hozzon létre egy **Streamelési lokátor** a beépített **Streamelési házirend** típusokat.
8. Az útvonalak listájában a **Streamelési lokátor** visszatéréshez használandó URL-címeket.
9. A gazdanevének beszerzése a **folyamatos átviteli végponton** érkező adatfolyam szeretné.
10. Az URL-címet a 8. lépés kombinálva a teljes URL-Címének lekéréséhez 9. lépésben az állomásnevet.
11. Futtassa az élő kódoló körülbelül 10 percre.
12. Leállítja az élő eseményt. 
13. Például egy lejátszóval [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) tekintse meg az adott lejátszási ne legyen az összes minőségi szinthez nem látható hipervizorgazdákat archivált objektumot (, vagy másik lehetőségként tekintse meg és az élő munkamenet során az előnézeti URL-CÍMEN keresztül ellenőrzése).
14. Jegyezze fel az eszköz azonosítója, közzétett streamelési URL-cím, az élő archívumot, és a beállítások és az élő kódoló a használt verzió.
15. Élő esemény állapot alaphelyzetbe állítása az összes minta létrehozása után.
16. Ismételje meg a 5-15 minden konfiguráció a kódoló (rendelkező és anélküli ad jelzés/akadálymentes feliratok/különböző sebességek kódolás) által támogatott.

### <a name="live-encoding-live-event-verification"></a>Élő kódolás Live Event ellenőrzése

1. A Media Services-fiók, ellenőrizze, hogy a **folyamatos átviteli végponton** fut-e. 
2. Hozzon létre, és indítsa el a **valós idejű kódolás** élő esemény. <br/> További információkért lásd: [élő esemény állapotok és számlázási](live-event-states-billing.md).
3. A betöltés URL-címe és a konfigurálása a Media Services egy egyféle sávszélességű élő streamet leküldése a kódolót.
4. Előnézeti URL-címére, és a segítségével ellenőrizheti, hogy a kódoló a bemeneti ténylegesen fogadja.
5. Hozzon létre egy új **eszköz** objektum.
6. Hozzon létre egy **élő kimeneti** , és használja az Ön által létrehozott objektum nevét.
7. Hozzon létre egy **Streamelési lokátor** a beépített **Streamelési házirend** típusokat.
8. Az útvonalak listájában a **Streamelési lokátor** visszatéréshez használandó URL-címeket.
9. A gazdanevének beszerzése a **folyamatos átviteli végponton** érkező adatfolyam szeretné.
10. Az URL-címet a 8. lépés kombinálva a teljes URL-Címének lekéréséhez 9. lépésben az állomásnevet.
11. Futtassa az élő kódoló körülbelül 10 percre.
12. Leállítja az élő eseményt.
13. Például egy lejátszóval [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) tekintse meg az adott lejátszási ne legyen az összes minőségi szinthez nem látható hipervizorgazdákat archivált objektumot (, vagy másik lehetőségként tekintse meg és az élő munkamenet során az előnézeti URL-CÍMEN keresztül ellenőrzése).
14. Jegyezze fel az eszköz azonosítója, közzétett streamelési URL-cím, az élő archívumot, és a beállítások és az élő kódoló a használt verzió.
15. Élő esemény állapot alaphelyzetbe állítása az összes minta létrehozása után.
16. Ismételje meg a 5-15 minden konfiguráció a kódoló (rendelkező és anélküli ad jelzés/akadálymentes feliratok/különböző sebességek kódolás) által támogatott.

### <a name="longevity-verification"></a>Az érvényességi ideje ellenőrzése

Ugyanaz, mint a lépések [átmenő élő esemény ellenőrzési](#pass-through-live-event-verification) kivételével a 11. lépés. <br/>10 perc helyett futtassa az élő kódoló egy hét vagy hosszabb ideig. Például egy lejátszóval [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) tekintse meg az élő streamelés időpontból lejátszás ne legyen semmilyen látható hipervizorgazdákat idő (vagy archivált objektumot).

### <a name="email-your-recorded-settings"></a>Az e-mail a rögzített beállítások

Végül, az e-mail a rögzített beállítások és archív paramétereket, az Azure Media Services élő amsstreaming@microsoft.com , egy értesítés, hogy az összes önkiszolgáló ellenőrzési ellenőrzések. Is bármely kövesse UPS a kapcsolattartási adatokat. Ez a folyamat kapcsolatos kérdéseivel Azure Media Services csapat fordulnia.

## <a name="next-steps"></a>További lépések

[Élő adások online közvetítése a Media Services v3](live-streaming-overview.md)
