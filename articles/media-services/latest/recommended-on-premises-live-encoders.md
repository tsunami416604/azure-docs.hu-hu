---
title: Élő adatfolyam-Media Services – Azure által javasolt kódolók |} A Microsoft Docs
description: További tudnivalók a Media Services által ajánlott élő streamelési helyszíni kódolók
services: media-services
keywords: kódolás; kódolók; adathordozó
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 01/17/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: a165fac2de89d5510b21b9185d4bc61e730b09ff
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55960122"
---
# <a name="recommended-live-streaming-encoders"></a>Ajánlott élő streamelési kódolók

Az Azure Media Services egy [élő esemény](https://docs.microsoft.com/rest/api/media/liveevents) (csatorna) egy olyan folyamatot jelent feldolgozása live-streaming-tartalmat. Az élő esemény élő bemeneti streamekhez kap a két módszer egyikével.

* Egy helyszíni élő kódoló küld az élő eseményt nincs engedélyezve a valós idejű kódolás a Media Services adatfolyam egy többszörös sávszélességű RTMP vagy Smooth Streaming (darabolt MP4). Élő események továbbítja a feldolgozott adatfolyamok további feldolgozás nélkül. Ezt a metódust meghívják **átmenő**. Az élő kódolók olyan küldhet egy egyszeres átviteli sebességű streamet átmenő csatornát. Ez a konfiguráció nem ajánlott, mert nem teszi lehetővé az adaptív sávszélességű streamelés az ügyfélnek.

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

## <a name="live-encoders-that-output-fragmented-mp4"></a>Élő kódolók képesek: darabolt MP4 kimeneti

A Media Services a következő élő kódolók többféle sávszélességű Smooth Streaming (darabolt MP4) kimenetként rendelkező egyikének használatát javasolja. A támogatott URL-sémák a `http://` vagy `https://`.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 csomag általános III.
- Az Imagine Communications Selenio MCP3
- Media Excel élő Hero és a Hero 4 KB-os (UHD/HEVC)

## <a name="configuring-on-premises-live-encoder-settings"></a>Konfigurálása a helyszíni élő kódoló beállításai

Milyen beállítások érvényesek az élő esemény típus kapcsolatos információkért lásd: [élő esemény-típusok összehasonlítása](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Lejátszási követelmények

Vissza a tartalom lejátszása, mind az audio- és stream elérhetőnek kell lennie. A csak a videó Stream lejátszás nem támogatott.

### <a name="configuration-tips"></a>Konfigurációs tippek

- Amikor csak lehetséges, hardveresen rögzített beállítású internet kapcsolat használatára.
- Amikor sávszélességre van szükség, double a streamelési bitsebességre való átkódolása meghatározása. Bár nem kötelező, az egyszerű szabály segít mérsékelni a hálózati torlódás hatását.
- Szoftveralapú kódolók használatáról, amikor el minden felesleges programot zárja be.
- Az esemény negatív hatással a kódoló konfiguráció módosítása, miután elkezdődött annak elküldése rendelkezik. Konfigurációs módosítások okozhat az esemény instabillá válhat. 
- Győződjön meg arról, hogy engedélyezi a saját maga állíthatja be az esemény bőséges időt. Nagy méretű események javasoljuk, hogy a telepítő egy újabb órájába az esemény előtt.

## <a name="becoming-an-on-premises-encoder-partner"></a>Egy helyszíni kódolót partner lenni

Egy Azure Media Services a helyszíni kódolót partnerként a Media Services a termék elősegíti a vállalati ügyfelek számára a kódoló javaslatot. Egy helyszíni kódolót partner szeretne lenni, ellenőriznie kell a helyszíni kódolót kompatibilitását a Media Services használatával. Ehhez a következő ellenőrzések elvégzéséhez.

### <a name="pass-through-live-event-verification"></a>Az átmenő Live Event ellenőrzése

1. A Media Services-fiók, győződjön meg arról, hogy a **folyamatos átviteli végponton** fut-e. 
2. Hozzon létre, és indítsa el a **átmenő** élő esemény. <br/> További információkért lásd: [élő esemény állapotok és számlázási](live-event-states-billing.md).
3. A betöltés URL-címek lekérése, és állítsa be az URL-cím segítségével egy többszörös sávszélességű élő streamet küldeni a Media Services a helyszíni kódolót.
4. Előnézeti URL-címére, és a segítségével ellenőrizheti, hogy a kódoló a bemeneti ténylegesen fogadja.
5. Hozzon létre egy új **eszköz** objektum.
6. Hozzon létre egy **élő kimeneti** , és használja az Ön által létrehozott objektum nevét.
7. Hozzon létre egy **Streamelési lokátor** a beépített **Streamelési házirend** típusokat.
8. Az útvonalak listájában a **Streamelési lokátor** visszatéréshez használandó URL-címeket.
9. A gazdagép nevét a **folyamatos átviteli végponton** , amelyet ki szeretne.
10. Az URL-címet a 8. lépés kombinálva a számítógép neve a teljes URL-címére a 9. lépés.
11. Futtassa az élő kódoló körülbelül 10 percre.
12. Leállítja az élő eseményt. 
13. Például egy lejátszóval [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) tekintse meg az adott lejátszási nem látható hipervizorgazdákat ne legyen minden minőségi szinten archivált objektumot. Vagy nézze meg, és ellenőrizze az előnézeti URL-CÍMEN keresztül az élő munkamenet során.
14. Jegyezze fel az eszköz azonosítója, közzétett streamelési URL-CÍMÉT az élő archívumot, és a beállítások és az élő kódoló a használt verzió.
15. Élő esemény állapot alaphelyzetbe állítása az összes minta létrehozása után.
16. A kódoló (rendelkező és anélküli ad jelzés, feliratokat vagy különböző kódolási sebességű) által támogatott konfigurációk ismételje meg az 5 – 15.

### <a name="live-encoding-live-event-verification"></a>Élő kódolás Live Event ellenőrzése

1. A Media Services-fiók, győződjön meg arról, hogy a **folyamatos átviteli végponton** fut-e. 
2. Hozzon létre, és indítsa el a **valós idejű kódolás** élő esemény. <br/> További információkért lásd: [élő esemény állapotok és számlázási](live-event-states-billing.md).
3. A betöltés URL-címek lekérése, és állítsa be a Media Services egy egyféle sávszélességű élő streamet leküldése a kódolót.
4. Előnézeti URL-címére, és a segítségével ellenőrizheti, hogy a kódoló a bemeneti ténylegesen fogadja.
5. Hozzon létre egy új **eszköz** objektum.
6. Hozzon létre egy **élő kimeneti** , és használja az Ön által létrehozott objektum nevét.
7. Hozzon létre egy **Streamelési lokátor** a beépített **Streamelési házirend** típusokat.
8. Az útvonalak listájában a **Streamelési lokátor** visszatéréshez használandó URL-címeket.
9. A gazdagép nevét a **folyamatos átviteli végponton** , amelyet ki szeretne.
10. Az URL-címet a 8. lépés kombinálva a számítógép neve a teljes URL-címére a 9. lépés.
11. Futtassa az élő kódoló körülbelül 10 percre.
12. Leállítja az élő eseményt.
13. Például egy lejátszóval [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) tekintse meg az adott lejátszási ne legyen az összes minőségi szinthez nem látható hipervizorgazdákat archivált objektumot. Vagy nézze meg, és ellenőrizze az előnézeti URL-CÍMEN keresztül az élő munkamenet során.
14. Jegyezze fel az eszköz azonosítója, közzétett streamelési URL-CÍMÉT az élő archívumot, és a beállítások és az élő kódoló a használt verzió.
15. Élő esemény állapot alaphelyzetbe állítása az összes minta létrehozása után.
16. A kódoló (rendelkező és anélküli ad jelzés, feliratokat vagy különböző kódolási sebességű) által támogatott konfigurációk ismételje meg az 5 – 15.

### <a name="longevity-verification"></a>Az érvényességi ideje ellenőrzése

Kövesse a lépéseket, mint a [átmenő élő esemény ellenőrzési](#pass-through-live-event-verification) kivételével 11. lépés. <br/>10 perc helyett futtassa az élő kódoló egy hét vagy hosszabb ideig. Például egy lejátszóval [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) tekintse meg az élő streamelés időpontból lejátszás ne legyen semmilyen látható hipervizorgazdákat idő (vagy egy archivált objektumot).

### <a name="email-your-recorded-settings"></a>Az e-mail a rögzített beállítások

Végül az e-mail a rögzített beállítások és archív paramétereket, az Azure Media Services élő amsstreaming@microsoft.com , egy értesítés, hogy az összes önkiszolgáló ellenőrzési ellenőrzések. Is bármely követések kapcsolattartási adatait. Az Azure Media Services team, ha bármilyen kérdése van, erről a folyamatról fordulnia.

## <a name="next-steps"></a>További lépések

[Élő adások online közvetítése a Media Services v3](live-streaming-overview.md)
