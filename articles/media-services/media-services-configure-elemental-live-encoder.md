---
title: "A elemi élő kódoló egy egyféle sávszélességű élő adatfolyamot küldeni |} Microsoft Docs"
description: "Ez a témakör bemutatja, hogyan AMS élő kódolásra képes csatornák egyféle sávszélességű adatfolyamot küldeni a elemi élő kódoló."
services: media-services
documentationcenter: 
author: cenkdin
manager: cfowler
editor: 
ms.assetid: 9c6bf6a9-6273-4fdd-9477-f0e565280b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: cenkd;anilmur;juliako
ms.openlocfilehash: 668a3ab46a70c0ee25fa87031d27c0f4333ec89c
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="use-the-elemental-live-encoder-to-send-a-single-bitrate-live-stream"></a>Használja a elemi élő kódoló egy egyféle sávszélességű élő adatfolyamot küldeni
> [!div class="op_single_selector"]
> * [Élő elemi](media-services-configure-elemental-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Ez a témakör ismerteti, hogyan konfigurálható a [elemi élő](http://www.elementaltechnologies.com/products/elemental-live) kódoló egy egyfajta sávszélességű adatfolyamot AMS-csatorna is küldhet a valós idejű kódolásra engedélyezve vannak.  További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

Ez az oktatóanyag bemutatja, hogyan kezelheti az Azure Media Services (AMS) Azure Media Services Explorer (AMSE) eszközzel. Ez az eszköz csak a Windows rendszerű Számítógépeken fut. Ha Mac vagy Linux, létrehozásához használja az Azure-portálon [csatornák](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) és [programok](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Előfeltételek
* Élő események létrehozása elemi élő webes felülete segítségével ismeretét kell rendelkeznie.
* [Azure Media Services-fiók létrehozása](media-services-portal-create-account.md)
* Gondoskodjon arról, hogy fut egy adatfolyam-végpontot. További információkért lásd: [adatfolyam-továbbítási végpontok kezelése egy Media Services-fiók](media-services-portal-manage-streaming-endpoints.md).
* Telepítse a legújabb verzióját a [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) eszköz.
* Indítsa el az eszközt, és csatlakozzon az AMS-fiók.

## <a name="tips"></a>Tippek
* Amikor csak lehetséges, hardveresen rögzített beállítású internetkapcsolat használatának.
* Jó tapasztalatok sávszélesség-követelményekkel meghatározásakor, hogy az adatfolyam-továbbítási bitrates duplán. Ez nem kötelezők, ez segít csökkenteni a hálózati torlódás hatását.
* Ha szoftverrel kódolók, zárja be az el a felesleges programokat.

## <a name="elemental-live-with-rtp-ingest"></a>A RTP elemi Live betöltési
Ez a szakasz bemutatja, hogyan a elemi élő kódoló egy egyféle sávszélességű élő adatfolyamot küldő RTP keresztül.  További információkért lásd: [MPEG-TS stream RTP keresztül](media-services-manage-live-encoder-enabled-channels.md#channel).

### <a name="create-a-channel"></a>Csatorna létrehozása

1. Az AMSE eszköz navigáljon a **Live** lapot, és a csatorna területen kattintson a jobb gombbal. Válassza ki **csatorna létrehozása...** a menüből.

    ![Elemi](./media/media-services-elemental-live-encoder/media-services-elemental1.png)

2. Adjon meg egy csatorna nevét, a Leírás mezőt nem kötelező megadni. A csatorna beállítások területen válassza a **szabványos** az élő kódolás lehetőségnél a bemeneti protokoll beállítása **RTP (MPEG-TS)**. A többi beállítás, hagyhatja.

    Győződjön meg arról, hogy a **most indítsa el az új csatorna** van kiválasztva.

3. Kattintson a **csatornát létrehozni**.

   ![Elemi](./media/media-services-elemental-live-encoder/media-services-elemental12.png)

> [!NOTE]
> A csatorna mindaddig elindításához 20 percet is igénybe vehet.
>
>

A csatorna indítása közben is [a kódoló](media-services-configure-elemental-live-encoder.md#configure_elemental_rtp).

> [!IMPORTANT]
> Vegye figyelembe, hogy a számlázás indul, amint csatorna kész állapotba kerül. További információkért lásd: [csatorna állapotok](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

### <a id=configure_elemental_rtp></a>A elemi élő kódoló
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

#### <a name="configuration-steps"></a>Konfigurációs lépések
1. Keresse meg a **elemi élő** webes felület, és állítsa be a kódoló **UDP/TS** streaming.
2. Új esemény létrehozása után görgessen le a kimeneti csoportokat, és adja hozzá a **UDP/TS** kimeneti csoport.
3. Hozzon létre egy új kimeneti kiválasztásával **új adatfolyam** , majd **hozzáadása kimeneti**.  

    ![Elemi](./media/media-services-elemental-live-encoder/media-services-elemental13.png)

   > [!NOTE]
   > Javasoljuk, hogy rendelkezik-e a "Rendszeróra" értékre a kódoló adatfolyam hiba esetén újra segítségével időkód elemi esemény.
   >
   >
4. Most, hogy a kimenet létrehozása után kattintson **adatfolyam adható hozzá**. A kimeneti beállításait mostantól konfigurálható.
5. Görgessen le a "Stream 1", amely éppen most hozták létre, kattintson a **videó** a bal oldali lapon, és bontsa ki a **speciális** beállítások szakaszban.

    ![Elemi](./media/media-services-elemental-live-encoder/media-services-elemental4.png)

    Míg elemi élő elérhető testreszabása számos, a következő beállítások ajánlott első lépések az AMS streaming.

   * Megoldás: 1280 x 720
   * Képkockasebesség: 30
   * GOP mérete: 60 keretek
   * Váltott mód: fokozatos
   * Átviteli sebesség: 5000000 bit/mp (ezt úgy kell beállítani a hálózati korlátozás)

    ![Elemi](./media/media-services-elemental-live-encoder/media-services-elemental5.png)

1. A csatorna bemeneti URL-cím beszerzése.

    Lépjen vissza az AMSE eszköz, és a csatorna befejezési állapotát. Miután állapota megváltozott, a **indítása** való **futtató**, kaphat a bemeneti URL-CÍMÉT.

    Ha a csatorna fut, kattintson a jobb gombbal a csatorna neve, felett az egérmutatót navigáljon **vágólapra másolás bemeneti URL-cím** , és válassza **elsődleges bemeneti URL-cím**.  

    ![Elemi](./media/media-services-elemental-live-encoder/media-services-elemental6.png)
2. Illessze be ezt az információt a **elsődleges cél** elemi mezőjében. A többi beállítás az alapértelmezett maradjanak.

    ![Elemi](./media/media-services-elemental-live-encoder/media-services-elemental14.png)

    További redundancia ismételje meg ezeket a lépéseket, a másodlagos bemeneti URL-címet a UDP/TS Streaming hozzon létre egy külön "Kimeneti" lapon.
3. Kattintson a **létrehozása** (Ha új esemény létrehozása) vagy **frissítés** (Ha egy már meglévő esemény szerkesztése) és folytathatja a kódoló elindításához.

> [!IMPORTANT]
> Végül kattintson **Start** a elemi élő webes felülete, **kell** győződjön meg arról, hogy készen áll-e a csatorna.
> Bizonyosodjon meg róla, hogy ne maradjon a csatorna egy esemény > 15 percnél hosszabb ideig nem üzemkész állapotban.
>
>

Miután az adatfolyam 30 másodpercig fut, lépjen vissza az AMSE eszköz és a vizsgálati lejátszás.  

### <a name="test-playback"></a>Teszt lejátszás

Keresse meg az AMSE eszköz, és kattintson a jobb gombbal a csatornát kell tesztelni. A menüben rámutat **lejátszás az előzetes** válassza **Azure Media Player**.  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental8.png)

Ha az adatfolyam a Windows Media player jelenik meg, majd a kódoló megfelelően van konfigurálva AMS való kapcsolódáshoz.

Ha hibaüzenetet kap, a csatorna le kell állítani, és kódoló beállításai módosul. Tekintse meg a [hibaelhárítási](media-services-troubleshooting-live-streaming.md) témakör útmutatást.   

### <a name="create-a-program"></a>Hozzon létre egy programot
1. Miután csatorna lejátszás megerősítjük, hozzon létre egy programot. Az a **Live** az AMSE eszköz lapján, a program területen kattintson a jobb gombbal, és válassza ki **hozzon létre új Program**.  

    ![Elemi](./media/media-services-elemental-live-encoder/media-services-elemental9.png)
2. A program neve, és szükség esetén módosítsa a **az archiválási időtartam** (amely alapértelmezés szerint 4 óra). Adja meg a tárolási helyet is, vagy hagyja meg az alapértelmezett.  
3. Ellenőrizze a **indítsa el a Program most** mezőbe.
4. Kattintson a **hozzon létre programot**.  

    >[!NOTE]
    > Program létrehozása gyorsabb a csatorna létrehozása.   
      
5. Ha a program fut, erősítse meg a lejátszás jobb gombbal kattint rá a program, és lépjen az **lejátszás a programokról** és jelölje be **Azure Media Player**.  
6. Ha megerősítette, kattintson a jobb gombbal a program újra, és válassza ki **a kimeneti URL-Címének másolása a vágólapra** (vagy az adatok lekérésére a **Program információk és beállítások** lehetőséget a menüből).

Az adatfolyam egy Player beágyazott, vagy olyan célközönségnek juttathatja el élő megtekintésre elosztott készen áll.  

## <a name="troubleshooting"></a>Hibaelhárítás
Tekintse meg a [hibaelhárítási](media-services-troubleshooting-live-streaming.md) témakör útmutatást.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
