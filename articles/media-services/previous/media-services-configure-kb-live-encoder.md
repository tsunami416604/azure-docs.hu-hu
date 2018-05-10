---
title: A Haivision KB kódoló egyféle sávszélességű élő adatfolyamot küldeni az Azure-bA |} Microsoft Docs
description: Ez a témakör bemutatja, hogyan konfigurálhatja a Haivision KB élő kódoló egy egyféle sávszélességű adatfolyamot küldeni AMS élő kódolásra képes csatornák.
services: media-services
documentationcenter: ''
author: dbgeorge
manager: vsood
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 02/02/2018
ms.author: juliako;dbgeorge
ms.openlocfilehash: 25077cd9338a2764c6dff9e755812033685f6641
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Használja a Haivision KB élő kódoló egy egyféle sávszélességű élő adatfolyamot küldeni
> [!div class="op_single_selector"]
> * [Élő elemi](media-services-configure-elemental-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [wirecast](media-services-configure-wirecast-live-encoder.md)

Ez a témakör ismerteti, hogyan konfigurálható a [Havision KB élő kódoló](https://www.haivision.com/products/kb-series/) kódoló egy egyfajta sávszélességű adatfolyamot AMS-csatorna is küldhet a valós idejű kódolásra engedélyezve vannak. További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

Ez az oktatóanyag bemutatja, hogyan kezelheti az Azure Media Services (AMS) Azure Media Services Explorer (AMSE) eszközzel. Ez az eszköz csak a Windows rendszerű Számítógépeken fut. Ha Mac vagy Linux, létrehozásához használja az Azure-portálon [csatornák](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) és [programok](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Előfeltételek
*   Egy Haivision KB kódoló Szoftveres v5.01 fut a hozzáférést vagy nagyobb.
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
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Adjon meg egy csatorna nevét, a Leírás mezőt nem kötelező megadni. A csatorna beállítások területen válassza a **szabványos** az élő kódolás lehetőségnél a bemeneti protokoll beállítása **RTMP**. A többi beállítás, hagyhatja. Győződjön meg arról, hogy a **most indítsa el az új csatorna** van kiválasztva.
3. Kattintson a **csatornát létrehozni**.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> A csatorna mindaddig elindításához 20 percet is igénybe vehet.

## <a name="configure-the-haivision-kb-encoder"></a>A Haivision KB kódoló
Ebben az oktatóanyagban a következő kimeneti beállításokat használják. Ez a szakasz a további konfigurációs lépések részletesebben ismerteti.

Videó:
-   Kodek: H.264
-   Profil: High (szint 4.0-s)
-   Átviteli sebesség: 5000 KB/s
-   Kulcskocka: 2 másodperc (60 másodperc)
-   Keret aránya: 30

Hang:
-   A kodek: AAC (LC)
-   Átviteli sebesség: 192 Kbit/s
-   Mintavételi gyakoriság: 44,1 kHz

## <a name="configuration-steps"></a>Konfigurációs lépések
1.  Jelentkezzen be a Haivision KB felhasználói felületét.
2.  Kattintson a **menügomb** a csatorna vezérlőközpont, és válasszon **Hozzáadás csatorna**  
    ![Képernyőfelvétel 2017-08-14: 9.15.09 AM.png](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Típus a **csatorna neve** a név mezőben, majd kattintson a Tovább gombra.  
    ![Képernyőfelvétel 2017-08-14: 9.19.07 AM.png](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Válassza ki a **csatorna a bemeneti forráshoz** a a **a bemeneti forráshoz** legördülő listán, és kattintson a Tovább gombra.
    ![Képernyőfelvétel 2017-08-14: 9.20.44 AM.png](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Az a **kódoló sablon** legördülő listán válasszon **H264-720-AAC-192** , és kattintson a Tovább gombra.
    ![Képernyőfelvétel 2017-08-14: 9.23.15 AM.png](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Az a **válassza ki az új kimeneti** legördülő listán válassza **RTMP** , és kattintson a Tovább gombra.  
    ![Képernyőfelvétel 2017-08-14: 9.27.51 AM.png](./media/media-services-configure-kb-live-encoder/step6.png)
7.  Az a **csatorna kimeneti** ablakban feltöltése az Azure stream információkat. Beillesztés a **RTMP** a hivatkozás leválasztása a kezdeti csatorna a telepítő a **Server** területen. Az a **kimeneti név** terület típus nevében a csatornát. Az adatfolyam neve sablon területen a sablon RTMPStreamName_ video_bitrate % segítségével az adatfolyam neve.
    ![Képernyőfelvétel 2017-08-14: 9.33.17 AM.png](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Kattintson a Tovább gombra, majd kattintson a Kész gombra.
9.  Kattintson a **lejátszás gombra** a kódoló csatorna elindításához.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Teszt lejátszás
Keresse meg az AMSE eszköz, és kattintson a jobb gombbal a csatornát kell tesztelni. A menüben az előzetes lejátszás rámutat, és válassza ki az Azure Media Player.

Ha az adatfolyam a Windows Media player jelenik meg, majd a kódoló megfelelően van konfigurálva AMS való kapcsolódáshoz.

Ha hibaüzenetet kap, a csatorna kell állítani, és kódoló beállításai módosul. A hibaelhárítási cikke útmutatót.

## <a name="create-a-program"></a>Hozzon létre egy programot
1.  Miután csatorna lejátszás megerősítjük, hozzon létre egy programot. A működés közbeni lap az AMSE eszköz a program területen kattintson a jobb gombbal, és válassza ki az új Program létrehozása.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  A program neve, és ha szükséges, módosítsa az archiválási időtartam (amely alapértelmezés szerint 4 óra). Adja meg a tárolási helyet is, vagy hagyja meg az alapértelmezett.
2.  A Start jelölje be a Program most jelölőnégyzetet.
3.  Kattintson a létrehozás Program.
4.  Után a program fut, a lejátszás megerősítéséhez kattintson a jobb gombbal a program, és navigáljon a programokról lejátszani, és jelölje be az Azure Media Player.
5.  Ha megerősítette, kattintson ismét a jobb gombbal a program és válassza a Másolás a kimeneti URL vágólapra (vagy az adatok lekérését a programmal kapcsolatos információk és beállítások lehetőséget a menüből).

Az adatfolyam egy Player beágyazott, vagy olyan célközönségnek juttathatja el élő megtekintésre elosztott készen áll.

> [!NOTE]
> Program létrehozása gyorsabb a csatorna létrehozása.