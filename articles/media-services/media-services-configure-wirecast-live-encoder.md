---
title: "A Telestream Wirecast kódoló egyféle sávszélességű élő adatfolyamot küldeni |} Microsoft Docs"
description: "Ez a témakör bemutatja, hogyan konfigurálhatja a Wirecast élő kódoló egy egyféle sávszélességű adatfolyamot küldeni AMS élő kódolásra képes csatornák. "
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: 0e4fb0b7c915969da1760eaccc77aa399030752e
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Használja a Wirecast kódoló egyféle sávszélességű élő adatfolyamot küldeni
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [Élő elemi](media-services-configure-elemental-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Ez a cikk ismerteti, hogyan konfigurálható a [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) élő kódoló egy egyfajta sávszélességű adatfolyamot AMS-csatorna is küldhet a valós idejű kódolásra engedélyezve vannak.  További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

Ez az oktatóanyag bemutatja, hogyan kezelheti az Azure Media Services (AMS) Azure Media Services Explorer (AMSE) eszközzel. Ez az eszköz csak a Windows rendszerű Számítógépeken fut. Ha Mac vagy Linux, létrehozásához használja az Azure-portálon [csatornák](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) és [programok](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Előfeltételek
* [Azure Media Services-fiók létrehozása](media-services-portal-create-account.md)
* Gondoskodjon arról, hogy fut egy adatfolyam-végpontot. További információkért lásd: [adatfolyam-továbbítási végpontok kezelése egy Media Services-fiók](media-services-portal-manage-streaming-endpoints.md)
* Telepítse a legújabb verzióját a [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) eszköz.
* Indítsa el az eszközt, és csatlakozzon az AMS-fiók.

## <a name="tips"></a>Tippek
* Amikor csak lehetséges, hardveresen rögzített beállítású internetkapcsolat használatának.
* Jó tapasztalatok sávszélesség-követelményekkel meghatározásakor, hogy az adatfolyam-továbbítási bitrates duplán. Ez nem kötelezők, segít csökkenteni a hálózati torlódás hatását.
* Szoftveres kódolók használatakor lezárható a felesleges programokat.

## <a name="create-a-channel"></a>Csatorna létrehozása
1. Az AMSE eszköz navigáljon a **Live** lapot, és kattintson a jobb gombbal a csatorna területen belül. Válassza ki **csatorna létrehozása...** a menüből.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Adjon meg egy csatorna nevét, a Leírás mezőt nem kötelező megadni. A csatorna beállítások területen válassza a **szabványos** az élő kódolás lehetőségnél a bemeneti protokoll beállítása **RTMP**. A többi beállítás, hagyhatja.

    Győződjön meg arról, hogy a **most indítsa el az új csatorna** van kiválasztva.

3. Kattintson a **csatornát létrehozni**.

   ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> A csatorna mindaddig elindításához 20 percet is igénybe vehet.
>
>

A csatorna indul, míg is [a kódoló](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> A számlázás indul, amint csatorna kész állapotba kerül. További információkért lásd: [csatorna állapotok](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_wirecast_rtmp></a>A Telestream Wirecast kódoló
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
1. Indítsa el a Telestream Wirecast alkalmazást azon a gépen használt, és állítsa be az RTMP adatfolyamként történő.
2. Konfigurálja a kimeneti navigáljon a **kimeneti** fülre, és kattintson **kimeneti beállításai...** .

    Győződjön meg arról, hogy a **kimeneti cél** értéke **RTMP Server**.
3. Kattintson az **OK** gombra.
4. A beállítások lapon állítsa be a **cél** mezőre, amely **Azure Media Services**.

    A kódolás profil csak az előre kiválasztott **Azure H.264 720 p 16:9 (1280 x 720)**. Ezek a beállítások testreszabásához fogaskerék ikonra kattintva jobb oldalán a legördülő listán válassza ki, és válassza **új készletet**.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Kódoló készletek beállítása.

    Nevezze el a készletet, és ellenőrizze a következőket ajánlott beállítások:

    **Videó**

   * Kódoló: MainConcept H.264
   * A képkockák másodpercenkénti: 30
   * Átlagos átviteli sebessége: 5000 kbit/s (módosítani lehet a hálózati korlátozás)
   * Profil: fő
   * Kulcskocka minden: 60 keretek

    **Hang**

   * Cél: 192 Kbit/s
   * Mintavételi gyakoriság: 44.100 kHz

     ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Nyomja le az **mentése**.

    A Encoding mező most már az újonnan létrehozott profil kijelölésnél érhetők el.

    Győződjön meg arról, hogy az új profil van kiválasztva.
7. A csatorna Get bemeneti URL-cím ahhoz, hogy rendelje hozzá a Wirecast **RTMP végpont**.

    Lépjen vissza az AMSE eszköz, és a csatorna befejezési állapotát. Miután állapota megváltozott, a **indítása** való **futtató**, kaphat a bemeneti URL-CÍMÉT.

    Ha a csatorna fut, kattintson a jobb gombbal a csatorna neve, felett az egérmutatót navigáljon **vágólapra másolás bemeneti URL-cím** , és válassza **elsődleges bemeneti URL-cím**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. A Wirecast a **kimeneti beállításainak** ablakban illessze be ezt az információt a **cím** output szakasz, és rendelje hozzá egy adatfolyam neve mezőben.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Kattintson az **OK** gombra.
2. A fő **Wirecast** képernyőjén ellenőrizze a bemeneti forrás a videó és hang készen áll, majd nyomja le **adatfolyam** bal felső sarokban.

   ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Kattintás előtt **adatfolyam**, akkor **kell** győződjön meg arról, hogy készen áll-e a csatornát.
> Győződjön meg arról, hogy nem a csatorna üzemkész állapotban hagyja meg az adatcsatorna-> 15 percnél hosszabb ideig bemeneti hozzájárulás nélkül.
>
>

## <a name="test-playback"></a>Teszt lejátszás

Keresse meg az AMSE eszköz, és kattintson a jobb gombbal a csatornát kell tesztelni. A menüben rámutat **lejátszás az előzetes** válassza **Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Ha az adatfolyam a Windows Media player jelenik meg, majd a kódoló megfelelően van konfigurálva AMS való kapcsolódáshoz.

Ha hibaüzenetet kap, a csatorna kell állítani, és kódoló beállításai módosul. Tekintse meg a [hibaelhárítási](media-services-troubleshooting-live-streaming.md) a cikk útmutatást.  

## <a name="create-a-program"></a>Hozzon létre egy programot
1. Miután csatorna lejátszás megerősítjük, hozzon létre egy programot. Az a **Live** az AMSE eszköz a lapot, kattintson a jobb gombbal a program területen belül, és válasszon **hozzon létre új Program**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. A program neve, és szükség esetén módosítsa a **az archiválási időtartam** (amely alapértelmezés szerint 4 óra). Adja meg a tárolási helyet is, vagy hagyja meg az alapértelmezett.  
3. Ellenőrizze a **indítsa el a Program most** mezőbe.
4. Kattintson a **hozzon létre programot**.  

   >[!NOTE]
   >Program létrehozása gyorsabb a csatorna létrehozása.
       
5. Ha a program fut, erősítse meg a lejátszás kattintson a jobb gombbal a program, és lépjen az **lejátszás a programokról** és jelölje be **Azure Media Player**.  
6. Ha megerősítette, kattintson ismét a jobb gombbal a program, és válassza ki **a kimeneti URL-Címének másolása a vágólapra** (vagy az adatok lekérésére a **Program információk és beállítások** lehetőséget a menüből).

Az adatfolyam egy Player beágyazott, vagy olyan célközönségnek juttathatja el élő megtekintésre elosztott készen áll.  

## <a name="troubleshooting"></a>Hibaelhárítás
Tekintse meg a [hibaelhárítási](media-services-troubleshooting-live-streaming.md) a cikk útmutatást.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
