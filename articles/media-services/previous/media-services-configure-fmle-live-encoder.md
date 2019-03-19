---
title: Egy egyféle sávszélességű élő adatfolyamot küldeni a FMLE kódoló konfigurálása |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan konfigurálhatja a Flash Media Live Encoder (FMLE) kódoló egyféle sávszélességű adatfolyamot küldeni a valós idejű kódolásra képes csatornák AMS.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 3113f333-517a-47a1-a1b3-57e200c6b2a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: 01bb628a6520488dcebf49a1e868213b955abc31
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57895616"
---
# <a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>Használja az egyféle sávszélességű élő adatfolyamot küldeni FMLE kódoló 
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

Ez a cikk bemutatja, hogyan konfigurálhatja a [Flash Media Live Encoder](https://www.adobe.com/products/flash-media-encoder.html) (FMLE) kódoló egy egyfajta sávszélességű adatfolyamot AMS-csatorna is küldhet az élő kódolás engedélyezve vannak. További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

Ez az oktatóanyag bemutatja, hogyan kezelheti az Azure Media Services (AMS) az Azure Media Services Explorer (AMSE) eszközzel. Ez az eszköz csak akkor Windows-számítógépen fog futni. Ha Mac vagy Linux rendszeren, az Azure portal használatával hozzon létre [csatornák](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) és [programok](media-services-portal-creating-live-encoder-enabled-channel.md).

Ebben az oktatóanyagban az AAC használatát ismerteti. Ugyanakkor az FMLE nem támogatja az AAC alapértelmezés szerint. Meg kell vásárolni egy beépülő modult kiadottat AAC kódolással, például: [Az AAC-beépülő modul](https://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

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

    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. Adjon meg egy csatorna nevét, a Leírás mező kitöltése nem kötelező. Csatorna beállítások területén válassza ki a **Standard** a Live Encoding funkcióval beállítást is választja, a bemeneti protokoll beállítása **RTMP**. Hagyhatja, hogy a többi beállítás-jébe.

    Győződjön meg arról, hogy a **most indítsa el az új csatorna** van kiválasztva.

3. Kattintson a **csatorna létrehozása**.

   ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

> [!NOTE]
> A csatorna mindaddig elindításához 20 percet is igénybe vehet.
>
>

A csatorna indítása folyamatban van, amíg is [a kódoló](media-services-configure-fmle-live-encoder.md).

> [!IMPORTANT]
> Vegye figyelembe, hogy a számlázási elindul, amint a csatorna üzemkész állapotba kerül. További információkért lásd: [csatorna állapotok](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_fmle_rtmp></a>A FMLE kódoló
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
1. Keresse meg a gép, használja a kapcsolat a Flash Media Live Encoder a (FMLE).

    A felület egy fő lapján, beállítások esetén. Jegyezze fel a következő ajánlott beállítások streamelési funkciójával FMLE használatának megkezdéséhez.

   * Formátum: Képkockasebesség H.264: 30.00
   * Bemeneti méret: 1280 x 720
   * Átviteli sebesség: 5000 KB/s (módosítható hálózati korlátai alapján)  

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

     Ha források segítségével váltakozó, kérjük, pipa az "összefűzés lehetőség" beállítás
2. Válassza ki a formátumot melletti csavarkulcs ikont, a következő további beállításokat kell:

   * Profil: Elsődleges
   * Szint: 4.0
   * Kulcsképkocka gyakorisága: 2 másodperc

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)
3. Állítsa be a következő fontos hang beállítást:

   * Formátum: AAC
   * Mintavételi gyakoriság: 44100 Hz
   * Átviteli sebesség: 192 Kb/s

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)
4. A csatorna Get bemeneti URL-címet annak érdekében, hogy rendelje hozzá a FMLE **RTMP végpont**.

    Lépjen vissza az AMSE eszköz, és a csatorna befejezési állapotának ellenőrzéséhez. Miután állapota megváltozott **indítása** való **futó**, a bemeneti URL-címet kap.

    Ha a csatorna fut, kattintson a jobb gombbal a csatorna nevét, vigye fölé navigáljon **példány bemeneti URL-CÍMÉT a vágólapra** majd **elsődleges bemeneti URL-cím**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)
5. Illessze be ezt az információt a **FMS URL-cím** mezőjét, a kimeneti szakaszban, és a egy replikációsstream-név hozzárendelése.

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    A további redundancia biztosítása érdekében ismételje meg ezeket a lépéseket a másodlagos bemeneti URL-CÍMÉT.
6. Kattintson a **Csatlakozás** gombra.

> [!IMPORTANT]
> Gombra való kattintás előtt **Connect**, hogy **kell** győződjön meg arról, hogy készen áll-e a csatornát.
> Bizonyosodjon meg róla, hogy ne maradjon a csatorna egy kész állapotú > 15 percnél hosszabb ideig hírcsatorna egy bemeneti hozzájárulás nélkül.
>
>

## <a name="test-playback"></a>Teszt lejátszás

Keresse meg az AMSE eszköz, és kattintson a jobb gombbal a csatorna tesztelését. A menüben mutasson **lejátszási az előzetes verzió** válassza **az Azure Media Player**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

Ha a lejátszó az adatfolyam jelenik meg, majd a kódoló konfigurációja megfelelő AMS csatlakozni.

Ha hibaüzenet érkezik, a csatornát kell állítani, és kódoló beállítások úgy vannak megadva. Tekintse meg a [hibaelhárítási](media-services-troubleshooting-live-streaming.md) a cikk útmutatást.  

## <a name="create-a-program"></a>Egy olyan program létrehozásához
1. Csatorna lejátszási ellenőrzése után hozzon létre egy programot. Alatt a **élő** az AMSE eszköz lapra, kattintson a jobb gombbal a program területen belül, és válassza **új Program létrehozása**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)
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
