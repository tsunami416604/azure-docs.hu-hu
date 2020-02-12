---
title: A NewTek TriCaster Encoder beállítása egyetlen sávszélességű élő stream küldésére | Microsoft Docs
description: Ebből a témakörből megtudhatja, hogyan konfigurálhatja a Tricaster élő kódolót úgy, hogy egyetlen sávszélességű adatfolyamot küldjön az élő kódoláshoz engedélyezett AMS-csatornákra.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 8973181a-3059-471a-a6bb-ccda7d3ff297
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 7909fbb958a66d00616d4ed1b844d02bb47d997e
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152499"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Egyetlen sávszélességű élő stream küldése a NewTek TriCaster Encoder használatával  
> [!div class="op_single_selector"]
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Elemi élő](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

Ez a cikk bemutatja, hogyan konfigurálhatja a [NewTek TriCaster](https://newtek.com/products/tricaster-40.html) élő kódolót úgy, hogy egyetlen sávszélességű adatfolyamot küldjön az élő kódolásra engedélyezett AMS-csatornáknak. További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

Ez az oktatóanyag bemutatja, hogyan kezelheti az Azure Media Services (AMS) az Azure Media Services Explorer (AMSE) eszközzel. Ez az eszköz csak akkor Windows-számítógépen fog futni. Ha Mac vagy Linux operációs rendszert használ, [csatornák](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) és [programok](media-services-portal-creating-live-encoder-enabled-channel.md)létrehozásához használja a Azure Portal.

Ha a Tricaster-t használja az élő kódolásra engedélyezett AMS-csatornákhoz való adatküldéshez, akkor az élő eseményen video-és hanghibák jelentkezhetnek, ha a Tricaster bizonyos funkcióit használja, például a hírcsatornák gyors kivágását, vagy váltás a betöltésre vagy a listára. Az AMS csapat dolgozik ezen problémák elhárításán, addig nem ajánlott ezeket a funkciókat használni.

> [!NOTE]
>  Érdemes lehet áthelyezni a TLS 1,2-re, a prefferred TLS-verzióra.

## <a name="prerequisites"></a>Előfeltételek

* [Azure Media Services fiók létrehozása](media-services-portal-create-account.md)
* Győződjön meg arról, van egy folyamatos átviteli végponton fut-e. További információ: [streaming-végpontok kezelése Media Services-fiókban](media-services-portal-manage-streaming-endpoints.md)
* Telepítse a [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) eszköz legújabb verzióját.
* Indítsa el az eszközt, és csatlakozzon az AMS-fiók.

## <a name="tips"></a>Tippek

* Amikor csak lehetséges, hardveresen rögzített beállítású internet kapcsolat használatára.
* Jó tapasztalatok forrássávszélesség követelményeinek meghatározásakor, hogy a streamelési bitsebességre való átkódolása duplán. Bár ez nem kötelezők, segít a hálózati torlódás hatásainak mérséklése érdekében.
* Szoftveralapú kódolók használatáról, amikor el minden felesleges programot zárja be.

## <a name="create-a-channel"></a>Csatorna létrehozása

1. A AMSE eszközben navigáljon az **élő** lapra, és kattintson a jobb gombbal a csatorna területére. Válassza a **csatorna létrehozása... lehetőséget.** a menüből.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Adjon meg egy csatorna nevét, a Leírás mező kitöltése nem kötelező. A Channel Settings (csatorna beállításai) területen válassza a **standard** lehetőséget a Live Encoding beállításnál, a bemeneti protokollt pedig az **RTMP**értékre állítva. Hagyhatja, hogy a többi beállítás-jébe.

    Győződjön meg arról, hogy az **új csatorna elindítása most** lehetőség van kiválasztva.

3. Kattintson a **csatorna létrehozása**gombra.

   ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> A csatorna mindaddig elindításához 20 percet is igénybe vehet.
>
>

A csatorna elindítása után beállíthatja [a kódolót](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> A számlázás elindul, amint a csatorna üzemkész állapotba kerül. További információ: [csatorna állapotai](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfigure_tricaster_rtmpconfigure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>a NewTek TriCaster-kódoló konfigurálása

Ebben az oktatóanyagban a következő kimeneti beállításokat használják. Ez a szakasz a többi konfigurációs lépések részletesebben ismerteti.

**Videó**:

* Kodek: H.264
* Profil: High (4.0-s szint)
* Átviteli sebesség: 5000 KB/s
* Kulcsképkocka: 2 másodperc (60 másodperc)
* Keret arány: 30

**Hang**:

* Kodekkel: Az AAC (LC)
* Átviteli sebesség: 192 Kb/s
* Mintavételi gyakoriság: 44,1 kHz

### <a name="configuration-steps"></a>Konfigurációs lépések

1. Hozzon létre egy új **NewTek-TriCaster** -projektet attól függően, hogy milyen video input-forrást használ.
2. A projekten belül keresse meg a **stream** gombot, és kattintson a mellette lévő fogaskerék ikonra az adatfolyam-konfiguráció menüjének eléréséhez.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. A menü megnyitása után kattintson az **új** elemre a kapcsolatok fejléce alatt. Ha a rendszer kéri a kapcsolattípus megadását, válassza az **Adobe Flash**elemet.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Kattintson az **OK** gombra.
5. A FMLE-profil most már importálható a **streaming profil** alatt található legördülő nyílra, és a **Tallózás**gombra kattintva navigálhat.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Navigáljon arra a helyre, ahová a konfigurált FMLE-profilt mentette.
7. Jelölje ki, majd kattintson **az OK gombra**.

    A profil feltöltése után folytassa a következő lépéssel.
8. Szerezze be a csatorna bemeneti URL-címét a Tricaster **RTMP-végponthoz**való hozzárendeléshez.

    Lépjen vissza az AMSE eszköz, és a csatorna befejezési állapotának ellenőrzéséhez. Ha az állapot már **megkezdődött** a **futtatástól**kezdve, beolvashatja a bemeneti URL-címet.

    Ha a csatorna fut, kattintson a jobb gombbal a csatorna nevére, navigáljon lefelé a **beviteli URL-cím másolása a vágólapra** , majd válassza az **elsődleges bemeneti URL-cím**elemet.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Illessze be ezt az információt a **hely** mezőbe a Tricaster projekt **Flash-kiszolgáló** területén. Az adatfolyam- **azonosító** mezőben is rendeljen egy stream-nevet.

    Ha a stream-adatok a FMLE-profilhoz lettek adva, a **Beállítások importálása**lehetőségre kattintva, a mentett FMLE-profilra navigálva, majd **az OK gombra**kattintva is importálhatja azt. A megfelelő Flash Server-mezőket a FMLE információi alapján kell feltölteni.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. Ha elkészült, kattintson az **OK** gombra a képernyő alján. Ha a Tricaster lévő videó-és hangbemenetek készen állnak, az **adatfolyam** gombra kattintva megkezdheti a folyamatos átvitelt az AMS-be.

     ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Mielőtt a **stream**elemre kattint, meg **kell** győződnie arról, hogy a csatorna készen áll.
> Bizonyosodjon meg róla, hogy ne maradjon a csatorna egy kész állapotú > 15 percnél hosszabb ideig hírcsatorna egy bemeneti hozzájárulás nélkül.
>
>

## <a name="test-playback"></a>Teszt lejátszás

Keresse meg az AMSE eszköz, és kattintson a jobb gombbal a csatorna tesztelését. A menüben vigye az egérmutatót **az előnézet lejátszásához** , és válassza a **Azure Media Player**lehetőséget.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Ha a lejátszó az adatfolyam jelenik meg, majd a kódoló konfigurációja megfelelő AMS csatlakozni.

Ha hiba érkezik, a csatornát alaphelyzetbe kell állítani, és a kódoló beállításait is módosítani kell. Útmutatásért tekintse meg a [hibaelhárítással](media-services-troubleshooting-live-streaming.md) foglalkozó cikket.  

## <a name="create-a-program"></a>Egy olyan program létrehozásához

1. Csatorna lejátszási ellenőrzése után hozzon létre egy programot. A AMSE eszköz **élő** lapján kattintson a jobb gombbal a program területére, és válassza az **új program létrehozása**lehetőséget.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
2. Adja meg a program nevét, és ha szükséges, állítsa be az **archiválási időszak hosszát** (amely alapértelmezés szerint négy óra). Adjon meg egy tárolási helyet is, vagy hagyja meg az alapértelmezett.  
3. Jelölje be a **program indítása** jelölőnégyzetet.
4. Kattintson a **program létrehozása**lehetőségre.  

    >[!NOTE]
    >Program létrehozása csatornák létrehozásának-nál kevesebb időt vesz igénybe.
        
5. A program futása után erősítse meg a lejátszást. ehhez kattintson a jobb gombbal a programra, és navigáljon **a program (ok) lejátszásához** , majd válassza **a Azure Media Player**lehetőséget.  
6. A megerősítést követően kattintson a jobb gombbal a programra, majd válassza a **kimeneti URL-cím másolása a vágólapra** lehetőséget (vagy olvassa be ezt az információt a **program információi és beállítások** menüpontból a menüből).

A stream beágyazott lejátszóval, vagy egy adott célközönségnek élő megtekintésre elosztott készen áll.  

## <a name="troubleshooting"></a>Hibakeresés

Útmutatásért tekintse meg a [hibaelhárítással](media-services-troubleshooting-live-streaming.md) foglalkozó cikket.

## <a name="next-step"></a>Következő lépés

Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
