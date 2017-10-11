---
title: "A FMLE kódoló egyféle sávszélességű élő adatfolyamot küldeni |} Microsoft Docs"
description: "Ez a témakör bemutatja, hogyan konfigurálhatja a Flash Media élő kódoló (FMLE) kódoló egyféle sávszélességű adatfolyamot küldeni AMS élő kódolásra képes csatornák."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 3113f333-517a-47a1-a1b3-57e200c6b2a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: e831048f34ecf6e89595adc4bfd58b5977e04bdb
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>Használja a FMLE kódoló egyféle sávszélességű élő adatfolyamot küldeni
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Élő elemi](media-services-configure-elemental-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

Ez a témakör ismerteti, hogyan konfigurálható a [Flash Live Media Encoder](http://www.adobe.com/products/flash-media-encoder.html) (FMLE) kódoló egy egyfajta sávszélességű adatfolyamot AMS-csatorna is küldhet a valós idejű kódolásra engedélyezve vannak. További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

Ez az oktatóanyag bemutatja, hogyan kezelheti az Azure Media Services (AMS) Azure Media Services Explorer (AMSE) eszközzel. Ez az eszköz csak a Windows rendszerű Számítógépeken fut. Ha Mac vagy Linux, létrehozásához használja az Azure-portálon [csatornák](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) és [programok](media-services-portal-creating-live-encoder-enabled-channel.md).

Vegye figyelembe, hogy ez az oktatóanyag leírja AAC használatával. Azonban az FMLE nem támogatja az AAC alapértelmezés szerint. Vásároljon egy beépülő modul a AAC kódolás például MainConcept kellene: [AAC beépülő modul](http://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

## <a name="prerequisites"></a>Előfeltételek
* [Azure Media Services-fiók létrehozása](media-services-portal-create-account.md)
* Gondoskodjon arról, hogy fut egy adatfolyam-végpontot. További információkért lásd: [adatfolyam-továbbítási végpontok kezelése egy Media Services-fiók](media-services-portal-manage-streaming-endpoints.md)
* Telepítse a legújabb verzióját a [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) eszköz.
* Indítsa el az eszközt, és csatlakozzon az AMS-fiók.

## <a name="tips"></a>Tippek
* Amikor csak lehetséges, hardveresen rögzített beállítású internetkapcsolat használatának.
* Jó tapasztalatok sávszélesség-követelményekkel meghatározásakor, hogy az adatfolyam-továbbítási bitrates duplán. Ez nem kötelezők, ez segít csökkenteni a hálózati torlódás hatását.
* Ha szoftverrel kódolók, zárja be az el a felesleges programokat.

## <a name="create-a-channel"></a>Csatorna létrehozása
1. Az AMSE eszköz navigáljon a **Live** lapot, és a csatorna területen kattintson a jobb gombbal. Válassza ki **csatorna létrehozása...** a menüből.

    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. Adjon meg egy csatorna nevét, a Leírás mezőt nem kötelező megadni. A csatorna beállítások területen válassza a **szabványos** az élő kódolás lehetőségnél a bemeneti protokoll beállítása **RTMP**. A többi beállítás, hagyhatja.

    Győződjön meg arról, hogy a **most indítsa el az új csatorna** van kiválasztva.

3. Kattintson a **csatornát létrehozni**.

   ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

> [!NOTE]
> A csatorna mindaddig elindításához 20 percet is igénybe vehet.
>
>

A csatorna indítása közben is [a kódoló](media-services-configure-fmle-live-encoder.md#configure_fmle_rtmp).

> [!IMPORTANT]
> Vegye figyelembe, hogy a számlázás indul, amint csatorna kész állapotba kerül. További információkért lásd: [csatorna állapotok](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_fmle_rtmp></a>A FMLE kódoló
Ebben az oktatóanyagban a következő kimeneti beállításokat használják. Ez a szakasz a további konfigurációs lépések részletesebben ismerteti.

**Videó**:

* Kodek: H.264
* Profil: High (szint 4.0-s)
* Átviteli sebesség: 5000 KB/s
* Kulcskocka: 2 másodperc (60 másodperc)
* Keret aránya: 30

**Hang**:

* A kodek: AAC (LC)
* Átviteli sebesség: 192 Kbit/s
* Mintavételi gyakoriság: 44,1 kHz

### <a name="configuration-steps"></a>Konfigurációs lépések
1. Keresse meg a Flash Media élő kódoló (FMLE) a gép, használja a kapcsolat.

    Az objektumfelület beállítások egy fő lapján. Kérjük, szánjon jegyezze fel a következő beállítások használatába streaming FMLE használata ajánlott.

   * Formátum: H.264 képkockasebessége: 30,00
   * Bemeneti mérete: 1280 x 720
   * Átviteli sebesség: 5000 KB/s (módosítani lehet a hálózati korlátozás)  

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

     Amikor források segítségével váltakozó, kérjük, pipa "összefűzés lehetőség" beállítás
2. Válassza ki a csavarkulcsot ábrázoló ikon formátum, a további beállítások:

   * Profil: fő
   * Szint: 4.0
   * Keyframe gyakorisága: 2 másodpercben

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)
3. Állítsa be a következő fontos hang beállítást:

   * Formátum: AAC
   * Mintavételi gyakoriság: 44100 Hz
   * Átviteli sebesség: 192 Kbit/s

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)
4. A csatorna Get bemeneti URL-cím ahhoz, hogy rendelje hozzá a FMLE **RTMP végpont**.

    Lépjen vissza az AMSE eszköz, és a csatorna befejezési állapotát. Miután állapota megváltozott, a **indítása** való **futtató**, kaphat a bemeneti URL-CÍMÉT.

    Ha a csatorna fut, kattintson a jobb gombbal a csatorna neve, felett az egérmutatót navigáljon **vágólapra másolás bemeneti URL-cím** , és válassza **elsődleges bemeneti URL-cím**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)
5. Illessze be ezt az információt a **FMS URL-cím** output szakasz, és rendelje hozzá egy adatfolyam neve mezőben.

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    A további redundáns működéshez ismételje meg ezeket a lépéseket, a másodlagos bemeneti URL-címet.
6. Kattintson a **Csatlakozás** gombra.

> [!IMPORTANT]
> Kattintás előtt **Connect**, akkor **kell** győződjön meg arról, hogy készen áll-e a csatornát.
> Győződjön meg arról, hogy nem a csatorna üzemkész állapotban hagyja meg az adatcsatorna-> 15 percnél hosszabb ideig bemeneti hozzájárulás nélkül.
>
>

## <a name="test-playback"></a>Teszt lejátszás

Keresse meg az AMSE eszköz, és kattintson a jobb gombbal a csatornát kell tesztelni. A menüben rámutat **lejátszás az előzetes** válassza **Azure Media Player**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

Ha az adatfolyam a Windows Media player jelenik meg, majd a kódoló megfelelően van konfigurálva AMS való kapcsolódáshoz.

Ha hibaüzenetet kap, a csatorna le kell állítani, és kódoló beállításai módosul. Tekintse meg a [hibaelhárítási](media-services-troubleshooting-live-streaming.md) témakör útmutatást.  

## <a name="create-a-program"></a>Hozzon létre egy programot
1. Miután csatorna lejátszás megerősítjük, hozzon létre egy programot. Az a **Live** az AMSE eszköz lapján, a program területen kattintson a jobb gombbal, és válassza ki **hozzon létre új Program**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)
2. A program neve, és szükség esetén módosítsa a **az archiválási időtartam** (amely alapértelmezés szerint 4 óra). Adja meg a tárolási helyet is, vagy hagyja meg az alapértelmezett.  
3. Ellenőrizze a **indítsa el a Program most** mezőbe.
4. Kattintson a **hozzon létre programot**.  

    >[!NOTE]
    >Program létrehozása gyorsabb a csatorna létrehozása.
        
5. Ha a program fut, erősítse meg a lejátszás jobb gombbal kattint rá a program, és lépjen az **lejátszás a programokról** és jelölje be **Azure Media Player**.  
6. Ha megerősítette, kattintson a jobb gombbal a program újra, és válassza ki **a kimeneti URL-Címének másolása a vágólapra** (vagy az adatok lekérésére a **Program információk és beállítások** lehetőséget a menüből).

Az adatfolyam egy Player beágyazott, vagy olyan célközönségnek juttathatja el élő megtekintésre elosztott készen áll.  

## <a name="troubleshooting"></a>Hibaelhárítás
Tekintse meg a [hibaelhárítási](media-services-troubleshooting-live-streaming.md) témakör útmutatást.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
