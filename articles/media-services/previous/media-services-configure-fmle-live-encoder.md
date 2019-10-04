---
title: A FMLE Encoder beállítása egyetlen bitráta élő stream küldésére | Microsoft Docs
description: Ez a témakör bemutatja, hogyan konfigurálhatja a Flash Media Live Encoder (FMLE) kódolót úgy, hogy egyetlen sávszélességű adatfolyamot küldjön az élő kódoláshoz engedélyezett AMS-csatornákra.
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
ms.author: juliako
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: 09d9bdffefe9204e9f58b8f07af5b21228269f6c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "69016755"
---
# <a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>Egyetlen bitráta élő stream küldése a FMLE Encoder használatával 
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

Ez a cikk bemutatja, hogyan konfigurálhatja a [Flash Media Live Encoder](https://www.adobe.com/products/flash-media-encoder.html) (FMLE) kódolót úgy, hogy egyetlen sávszélességű adatfolyamot küldjön az élő kódoláshoz engedélyezett AMS-csatornákra. További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

Ez az oktatóanyag bemutatja, hogyan kezelheti az Azure Media Services (AMS) az Azure Media Services Explorer (AMSE) eszközzel. Ez az eszköz csak akkor Windows-számítógépen fog futni. Ha Mac vagy Linux rendszeren, az Azure portal használatával hozzon létre [csatornák](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) és [programok](media-services-portal-creating-live-encoder-enabled-channel.md).

Ez az oktatóanyag az AAC használatát ismerteti. A FMLE azonban alapértelmezés szerint nem támogatja az AAC-t. Az AAC-kódoláshoz olyan beépülő modult kell vásárolnia, mint például a MainConcept: [AAC beépülő modul](https://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

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
> Vegye figyelembe, hogy a számlázás azonnal elindul, amint a csatorna üzemkész állapotba kerül. További információkért lásd: [csatorna állapotok](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_fmle_rtmp></a>A FMLE-kódoló konfigurálása
Ebben az oktatóanyagban a következő kimeneti beállításokat használják. Ez a szakasz a többi konfigurációs lépések részletesebben ismerteti.

**A videó**:

* Codec H.264
* Profil Magas (4,0-as szint)
* Sávszélességű 5000 kbps
* Keyframe 2 másodperc (60 másodperc)
* Keret sebessége: 30

**Hang**:

* Codec AAC (LC)
* Sávszélességű 192 kbps
* Mintavételezési arány: 44,1 kHz

### <a name="configuration-steps"></a>Konfigurációs lépések
1. Navigáljon a Flash Media Live Encoder (FMLE) felületére a használt gépen.

    Az illesztőfelület a beállítások egyik fő lapja. Jegyezze fel a következő ajánlott beállításokat a streaming használatának megkezdéséhez a FMLE használatával.

   * Formátum: H. 264 keret sebessége: 30,00
   * Bemeneti méret: 1280 x 720
   * Átviteli sebesség: 5000 kbps (a hálózati korlátozások alapján módosítható)  

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

     Összefűzött források használata esetén jelölje be a "deinterlace" beállítást.
2. A formátum mellett válassza a csavarkulcs ikont, a további beállítások a következők:

   * Profil Elsődleges
   * Szint: 4.0
   * Kulcsképek gyakorisága: 2 másodperc

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)
3. Állítsa be a következő fontos hangbeállítást:

   * Formátum: AAC
   * Mintavételezési arány: 44100 Hz
   * Sávszélességű 192 kbps

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)
4. Szerezze be a csatorna bemeneti URL-címét a FMLE **RTMP**-végponthoz való hozzárendeléséhez.

    Lépjen vissza az AMSE eszköz, és a csatorna befejezési állapotának ellenőrzéséhez. Miután állapota megváltozott **indítása** való **futó**, a bemeneti URL-címet kap.

    Ha a csatorna fut, kattintson a jobb gombbal a csatorna nevét, vigye fölé navigáljon **példány bemeneti URL-CÍMÉT a vágólapra** majd **elsődleges bemeneti URL-cím**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)
5. Illessze be ezt az információt a kimenet szakasz **FMS URL** mezőjébe, és rendeljen hozzá egy stream-nevet.

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    Az extra redundancia érdekében ismételje meg ezeket a lépéseket a másodlagos bemeneti URL-címmel.
6. Kattintson a **Csatlakozás** gombra.

> [!IMPORTANT]
> Mielőtt a **kapcsolat**elemre kattint, meg **kell** győződnie arról, hogy a csatorna készen áll.
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
2. Adja meg a program nevét, és ha szükséges, állítsa be az archiválási időszak hosszát (amely alapértelmezés szerint 4 óra). Adjon meg egy tárolási helyet is, vagy hagyja meg az alapértelmezett.  
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
