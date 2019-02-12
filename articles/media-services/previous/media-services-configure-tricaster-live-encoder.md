---
title: Egy egyféle sávszélességű élő adatfolyamot küldeni a NewTek TriCaster kódoló konfigurálása |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan konfigurálhatja a Tricaster egyféle sávszélességű adatfolyamot küldeni a valós idejű kódolásra képes csatornák AMS élő kódoló.
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
ms.date: 02/08/2019
ms.author: juliako;cenkd;anilmur
ms.openlocfilehash: 64c38d6e7400d49903323159f23b395a8c2c334f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998228"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Használja az egyféle sávszélességű élő adatfolyamot küldeni NewTek TriCaster kódoló  
> [!div class="op_single_selector"]
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Ez a cikk bemutatja, hogyan konfigurálhatja a [NewTek TriCaster](http://newtek.com/products/tricaster-40.html) élőadás-kódoló egy egyfajta sávszélességű adatfolyamot AMS-csatorna is küldhet az élő kódolás engedélyezve vannak. További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

Ez az oktatóanyag bemutatja, hogyan kezelheti az Azure Media Services (AMS) az Azure Media Services Explorer (AMSE) eszközzel. Ez az eszköz csak akkor Windows-számítógépen fog futni. Ha Mac vagy Linux rendszeren, az Azure portal használatával hozzon létre [csatornák](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) és [programok](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> A valós idejű kódolásra képes csatornák AMS-hírcsatorna hozzájárulás küldése Tricaster használatakor lehet videó vagy hang hipervizorgazdákat az élő esemény Tricaster, például gyors darabolás hírcsatornák között, vagy a befutók és a Váltás egyes funkcióinak használatakor. Ezek a problémák kijavítása addig az AMS-csapat dolgozik, nem ajánlott a funkciók használatához.
>
>

## <a name="prerequisites"></a>Előfeltételek

* [Az Azure Media Services-fiók létrehozása](media-services-portal-create-account.md)
* Győződjön meg arról, van egy folyamatos átviteli végponton fut-e. További információkért lásd: [adatfolyam-továbbítási végpontok kezelése egy Media Services-fiók](media-services-portal-manage-streaming-endpoints.md)
* Telepítse a legújabb verzióját a [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) eszközt.
* Indítsa el az eszközt, és csatlakozzon az AMS-fiók.

## <a name="tips"></a>Tippek

* Amikor csak lehetséges, hardveresen rögzített beállítású internet kapcsolat használatára.
* Jó tapasztalatok forrássávszélesség követelményeinek meghatározásakor, hogy a streamelési bitsebességre való átkódolása duplán. Bár ez nem kötelezők, segít a hálózati torlódás hatásainak mérséklése érdekében.
* Szoftveralapú kódolók használatáról, amikor el minden felesleges programot zárja be.

## <a name="create-a-channel"></a>Csatorna létrehozása

1. Az AMSE eszköz navigáljon a **élő** lapra, majd kattintson a jobb gombbal a csatorna területen belül. Válassza ki **csatorna létrehozása...** a menüből.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Adjon meg egy csatorna nevét, a Leírás mező kitöltése nem kötelező. Csatorna beállítások területén válassza ki a **Standard** a Live Encoding funkcióval beállítást is választja, a bemeneti protokoll beállítása **RTMP**. Hagyhatja, hogy a többi beállítás-jébe.

    Győződjön meg arról, hogy a **most indítsa el az új csatorna** van kiválasztva.

3. Kattintson a **csatorna létrehozása**.

   ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> A csatorna mindaddig elindításához 20 percet is igénybe vehet.
>
>

A csatorna indítása folyamatban van, amíg is [a kódoló](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> A számlázás elindul, amint a csatorna üzemkész állapotba kerül. További információkért lásd: [csatorna állapotok](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfiguretricasterrtmpconfigure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>Konfigurálja a NewTek TriCaster kódoló

Ebben az oktatóanyagban a következő kimeneti beállításokat használják. Ez a szakasz a többi konfigurációs lépések részletesebben ismerteti.

**A videó**:

* Kodek: H.264
* Profil: Nagy (4.0-s szint)
* Átviteli sebesség: 5000 KB/s
* Kulcsképkocka: 2 másodperc (60 másodperc)
* Képkockasebesség: 30

**Hang**:

* Kodek: AAC (LC)
* Átviteli sebesség: 192 Kb/s
* Mintavételi gyakoriság: 44,1 kHz

### <a name="configuration-steps"></a>Konfigurációs lépések

1. Hozzon létre egy új **NewTek TriCaster** projekt attól függően, hogy milyen videó bemeneti forrás használja.
2. Egyszer, hogy a projektben keresse meg a **Stream** gombra, majd kattintson a fogaskerék ikonra a stream konfigurációs menü mellett.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Kattintson a menü nyitotta meg, miután **új** kapcsolat fejléc alatt. Ha a rendszer kéri a kapcsolat típusát, válassza ki a **Adobe Flash**.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Kattintson az **OK** gombra.
5. Egy FMLE profilt most importálhatók, kattintson a legördülő nyílra alatt **Streamelési profil** ellenőrizheti, hogy **Tallózás**.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Keresse meg, ahol a konfigurált FMLE profil mentése megtörtént.
7. Válassza ki azt, majd nyomja le az ENTER **OK**.

    Miután feltöltötte a profil, folytassa a következő lépéssel.
8. A csatorna Get bemeneti URL-címet annak érdekében, hogy rendelje hozzá a Tricaster **RTMP végpont**.

    Lépjen vissza az AMSE eszköz, és a csatorna befejezési állapotának ellenőrzéséhez. Miután állapota megváltozott **indítása** való **futó**, a bemeneti URL-címet kap.

    Ha a csatorna fut, kattintson a jobb gombbal a csatorna neve, vigye fölé navigáljon **példány bemeneti URL-CÍMÉT a vágólapra** majd **elsődleges bemeneti URL-cím**.  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Illessze be ezt az információt a **hely** eleménél **Flash kiszolgáló** a Tricaster projektben. A stream nevét is hozzárendelhet a **Stream azonosítója** mező.

    Ha az adatfolyam adatok hozzáadásának FMLE profilt, azt is importálhatók ebben a szakaszban kattintva **beállítások importálása**, ellenőrizheti, hogy a mentett FMLE profilt, és kattintson a **OK**. A megfelelő kiszolgáló Flash mezőket kell adja meg a FMLE adatokat.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. Ha befejezte, kattintson a **OK** a képernyő alján. Amikor készen áll a Tricaster, video- és bemeneti, elkezdeni a streamelést az AMS-nek kattintva a **Stream** gombra.

     ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Gombra való kattintás előtt **Stream**, hogy **kell** győződjön meg arról, hogy készen áll-e a csatornát.
> Bizonyosodjon meg róla, hogy ne maradjon a csatorna egy kész állapotú > 15 percnél hosszabb ideig hírcsatorna egy bemeneti hozzájárulás nélkül.
>
>

## <a name="test-playback"></a>Teszt lejátszás

Keresse meg az AMSE eszköz, és kattintson a jobb gombbal a csatorna tesztelését. A menüben mutasson **lejátszási az előzetes verzió** válassza **az Azure Media Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Ha a lejátszó az adatfolyam jelenik meg, majd a kódoló konfigurációja megfelelő AMS csatlakozni.

Ha hibaüzenet érkezik, a csatornát kell vissza kell állítania, és kódoló beállítások úgy vannak megadva. Tekintse meg a [hibaelhárítási](media-services-troubleshooting-live-streaming.md) a cikk útmutatást.  

## <a name="create-a-program"></a>Egy olyan program létrehozásához

1. Csatorna lejátszási ellenőrzése után hozzon létre egy programot. Alatt a **élő** az AMSE eszköz lapra, kattintson a jobb gombbal a program területen belül, és válassza **új Program létrehozása**.  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
2. Nevezze el a programot, és ha szükséges, módosítsa a **archiválási időszak hossza** (amely alapértelmezés szerint 4 óra). Adjon meg egy tárolási helyet is, vagy hagyja meg az alapértelmezett.  
3. Ellenőrizze a **indítsa el a Program most** mezőbe.
4. Kattintson a **Program létrehozásához, amely**.  

    >[!NOTE]
    >Program létrehozása csatornák létrehozásának-nál kevesebb időt vesz igénybe.
        
5. Után a program fut, kattintson a jobb gombbal a program, és ellenőrizheti, hogy ellenőrizze a lejátszás **lejátszási a program(ok)** választásával **az Azure Media Player**.  
6. Miután megerősítette, kattintson ismét a jobb gombbal a program, és válassza ki **a kimeneti URL-cím másolása a vágólapra** (vagy az adatok lekérésére az **Program információk és beállítások** lehetőséget a menüből).

A stream beágyazott lejátszóval, vagy egy adott célközönségnek élő megtekintésre elosztott készen áll.  

## <a name="troubleshooting"></a>Hibaelhárítás

Tekintse meg a [hibaelhárítási](media-services-troubleshooting-live-streaming.md) a cikk útmutatást.

## <a name="next-step"></a>Következő lépés

Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
