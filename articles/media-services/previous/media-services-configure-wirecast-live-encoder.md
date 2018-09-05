---
title: Egy egyféle sávszélességű élő adatfolyamot küldeni a Telestream Wirecast kódoló konfigurálása |} A Microsoft Docs
description: 'Ez a témakör bemutatja, hogyan konfigurálhatja a Wirecast egyféle sávszélességű adatfolyamot küldeni a valós idejű kódolásra képes csatornák AMS élő kódoló. '
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: 6cc4c0b01511309766e48c3d671ee897e5d6f326
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669113"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Használja a Wirecast kódoló egyféle sávszélességű élő adatfolyamot küldeni
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Ez a cikk bemutatja, hogyan konfigurálhatja a [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) élőadás-kódoló egy egyfajta sávszélességű adatfolyamot AMS-csatorna is küldhet az élő kódolás engedélyezve vannak.  További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

Ez az oktatóanyag bemutatja, hogyan kezelheti az Azure Media Services (AMS) az Azure Media Services Explorer (AMSE) eszközzel. Ez az eszköz csak akkor Windows-számítógépen fog futni. Ha Mac vagy Linux rendszeren, az Azure portal használatával hozzon létre [csatornák](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) és [programok](media-services-portal-creating-live-encoder-enabled-channel.md).

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

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Adjon meg egy csatorna nevét, a Leírás mező kitöltése nem kötelező. Csatorna beállítások területén válassza ki a **Standard** a Live Encoding funkcióval beállítást is választja, a bemeneti protokoll beállítása **RTMP**. Hagyhatja, hogy a többi beállítás-jébe.

    Győződjön meg arról, hogy a **most indítsa el az új csatorna** van kiválasztva.

3. Kattintson a **csatorna létrehozása**.

   ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> A csatorna mindaddig elindításához 20 percet is igénybe vehet.
>
>

A csatorna indítása folyamatban van, amíg is [a kódoló](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> A számlázás elindul, amint a csatorna üzemkész állapotba kerül. További információkért lásd: [csatorna állapotok](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfigurewirecastrtmp-aconfigure-the-telestream-wirecast-encoder"></a>< a id = "configure_wirecast_rtmp" /a > a Telestream Wirecast kódoló konfigurálása
Ebben az oktatóanyagban a következő kimeneti beállításokat használják. Ez a szakasz a többi konfigurációs lépések részletesebben ismerteti.

**A videó**:

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
1. Nyissa meg a Telestream Wirecast alkalmazást, az a gép, használja, és állítsa be az RTMP-streameléshez.
2. A kimenet konfigurálása az a **kimeneti** fülre, és kattintson **kimeneti beállításai...** .

    Győződjön meg arról, hogy a **kimeneti cél** értékre van állítva **RTMP kiszolgáló**.
3. Kattintson az **OK** gombra.
4. A beállítások lapon állítsa be a **cél** mezőre, amely **Azure Media Services**.

    A kódolás profil az előre kiválasztott **Azure H.264 720 p 16:9 (1280 x 720)**. Ezek a beállítások testreszabásához kattintson a fogaskerék ikonra a jobb oldalán a listából, és válassza **új készletet**.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Kódoló készletek beállítása.

    Nevezze el a készletet, és ellenőrizze a következőket ajánlott beállítások:

    **Video**

   * Kódoló: MainConcept H.264
   * A képkockák másodpercenkénti: 30
   * Átlagos átviteli sebesség: 5000 kbit/s (módosítható hálózati korlátai alapján)
   * Profil: Main
   * Kulcs keret minden: 60 keretek

    **Audio**

   * Cél átviteli sebesség: 192 Kbit/s
   * Mintavételi gyakoriság: 44.100 kHz

     ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Kattintson a **Mentés** gombra.

    Az Encoding mező most már rendelkezik az újonnan létrehozott profil kiválasztható.

    Győződjön meg arról, hogy az új profil van kiválasztva.
7. A csatorna Get bemeneti URL-címet annak érdekében, hogy rendelje hozzá a Wirecast **RTMP végpont**.

    Lépjen vissza az AMSE eszköz, és a csatorna befejezési állapotának ellenőrzéséhez. Miután állapota megváltozott **indítása** való **futó**, a bemeneti URL-címet kap.

    Ha a csatorna fut, kattintson a jobb gombbal a csatorna nevét, vigye fölé navigáljon **példány bemeneti URL-CÍMÉT a vágólapra** majd **elsődleges bemeneti URL-cím**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. Az a Wirecast **kimeneti beállításainak** ablakban illessze be ezt az információt a **cím** mezőjét, a kimeneti szakaszban, és a egy replikációsstream-név hozzárendelése.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Kattintson az **OK** gombra.
2. A fő **Wirecast** képernyőjén ellenőrizze a bemeneti forrás a videó és hang készen áll, majd nyomja le az **Stream** bal felső sarokban.

   ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Gombra való kattintás előtt **Stream**, hogy **kell** győződjön meg arról, hogy készen áll-e a csatornát.
> Bizonyosodjon meg róla, hogy ne maradjon a csatorna egy kész állapotú > 15 percnél hosszabb ideig hírcsatorna egy bemeneti hozzájárulás nélkül.
>
>

## <a name="test-playback"></a>Teszt lejátszás

Keresse meg az AMSE eszköz, és kattintson a jobb gombbal a csatorna tesztelését. A menüben mutasson **lejátszási az előzetes verzió** válassza **az Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Ha a lejátszó az adatfolyam jelenik meg, majd a kódoló konfigurációja megfelelő AMS csatlakozni.

Ha hibaüzenet érkezik, a csatornát kell állítani, és kódoló beállítások úgy vannak megadva. Tekintse meg a [hibaelhárítási](media-services-troubleshooting-live-streaming.md) a cikk útmutatást.  

## <a name="create-a-program"></a>Egy olyan program létrehozásához
1. Csatorna lejátszási ellenőrzése után hozzon létre egy programot. Alatt a **élő** az AMSE eszköz lapra, kattintson a jobb gombbal a program területen belül, és válassza **új Program létrehozása**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
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

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
