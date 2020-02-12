---
title: Konfigurálja a Haivision KB-kódolót úgy, hogy egyetlen sávszélességű élő streamet küldjön az Azure-nak | Microsoft Docs
description: Ebből a témakörből megtudhatja, hogyan konfigurálhatja az Haivision KB élő kódolót úgy, hogy egyetlen sávszélességű adatfolyamot küldjön az élő kódoláshoz engedélyezett AMS-csatornákra.
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
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: afc0fcb6751a08b41010fa569c67a9827e0abec0
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131925"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Egyetlen bitráta élő stream küldése a Haivision KB Live Encoder használatával  
> [!div class="op_single_selector"]
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

Ebből a témakörből megtudhatja, hogyan konfigurálhatja a [HAVISION kb élő kódoló](https://www.haivision.com/products/kb-series/) kódolóját úgy, hogy egyetlen sávszélességű adatfolyamot küldjön az élő kódoláshoz engedélyezett AMS-csatornákra. További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

Ez az oktatóanyag bemutatja, hogyan kezelheti az Azure Media Services (AMS) az Azure Media Services Explorer (AMSE) eszközzel. Ez az eszköz csak akkor Windows-számítógépen fog futni. Ha Mac vagy Linux operációs rendszert használ, [csatornák](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) és [programok](media-services-portal-creating-live-encoder-enabled-channel.md)létrehozásához használja a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek
*   Hozzáférés egy Haivision KB-kódolóhoz, amely az SW v 5.01-es vagy újabb verzióját futtatja.
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
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Adjon meg egy csatorna nevét, a Leírás mező kitöltése nem kötelező. A Channel Settings (csatorna beállításai) területen válassza a **standard** lehetőséget a Live Encoding beállításnál, a bemeneti protokollt pedig az **RTMP**értékre állítva. Hagyhatja, hogy a többi beállítás-jébe. Győződjön meg arról, hogy az **új csatorna elindítása most** lehetőség van kiválasztva.
3. Kattintson a **csatorna létrehozása**gombra.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> A csatorna mindaddig elindításához 20 percet is igénybe vehet.

## <a name="configure-the-haivision-kb-encoder"></a>A Haivision KB-kódoló konfigurálása
Ebben az oktatóanyagban a következő kimeneti beállításokat használják. Ez a szakasz a többi konfigurációs lépések részletesebben ismerteti.

Videó:
-   Kodek: H.264
-   Profil: High (4.0-s szint)
-   Átviteli sebesség: 5000 KB/s
-   Kulcsképek: 2 másodperc (60-es keret)
-   Keret arány: 30

Hang
-   Kodekkel: Az AAC (LC)
-   Átviteli sebesség: 192 Kb/s
-   Mintavételi gyakoriság: 44,1 kHz

## <a name="configuration-steps"></a>Konfigurációs lépések
1.  Jelentkezzen be a Haivision KB felhasználói felületére.
2.  Kattintson a Channel Control Center **menü gombjára** , majd válassza a **csatorna hozzáadása** elemet.  
    ![képernyőkép 2017-08-14 a következő helyen: 9.15.09 AM](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Írja be a **csatorna nevét** a név mezőbe, és kattintson a Tovább gombra.  
    ![képernyőkép 2017-08-14 a következő helyen: 9.19.07 AM](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Válassza ki a **csatorna bemeneti forrását** a **bemeneti forrás** legördülő menüből, és kattintson a Tovább gombra.
    ![képernyőkép 2017-08-14 a következő helyen: 9.20.44 AM](./media/media-services-configure-kb-live-encoder/step4.png)
5.  A **kódoló sablon** legördülő menüből válassza a **H264-720-AAC-192** elemet, majd kattintson a Tovább gombra.
    ![képernyőkép 2017-08-14 a következő helyen: 9.23.15 AM](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Az **új kimenet kiválasztása** legördülő menüből válassza az **RTMP** elemet, majd kattintson a Tovább gombra.  
    ![képernyőkép 2017-08-14 a következő helyen: 9.27.51 AM](./media/media-services-configure-kb-live-encoder/step6.png)
7.  A **csatorna kimenete** ablakban töltse ki az Azure stream-információkat. Illessze be a **RTMP** hivatkozást a **kiszolgáló** területének kezdeti csatornájának beállításához. A **kimeneti név** területen adja meg a csatorna nevét. Az adatfolyam neve sablon területen használja a RTMPStreamName_% video_bitrate% sablont a stream elnevezéséhez.
    ![képernyőkép 2017-08-14 a következő helyen: 9.33.17 AM](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Kattintson a Tovább gombra, majd kattintson a kész gombra.
9.  A kódoló csatorna elindításához kattintson a **Lejátszás gombra** .  
    ![Haivision KB. png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Teszt lejátszás
Keresse meg az AMSE eszköz, és kattintson a jobb gombbal a csatorna tesztelését. A menüben vigye az egérmutatót az előnézet lejátszásához, és válassza a Azure Media Player lehetőséget.

Ha a lejátszó az adatfolyam jelenik meg, majd a kódoló konfigurációja megfelelő AMS csatlakozni.

Ha hibaüzenet érkezik, a csatornát kell állítani, és kódoló beállítások úgy vannak megadva. Útmutatásért tekintse meg a hibaelhárítással foglalkozó cikket.

## <a name="create-a-program"></a>Egy olyan program létrehozásához
1.  Csatorna lejátszási ellenőrzése után hozzon létre egy programot. A AMSE eszköz élő lapján kattintson a jobb gombbal a program területére, és válassza az új program létrehozása lehetőséget.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Adja meg a program nevét, és ha szükséges, állítsa be az archiválási időszak hosszát (amely alapértelmezés szerint négy óra). Adjon meg egy tárolási helyet is, vagy hagyja meg az alapértelmezett.
2.  Jelölje be a program indítása jelölőnégyzetet.
3.  Kattintson a program létrehozása lehetőségre.
4.  A program futása után erősítse meg a lejátszást. ehhez kattintson a jobb gombbal a programra, és navigáljon a program (ok) lejátszásához, majd válassza a Azure Media Player lehetőséget.
5.  A megerősítést követően kattintson a jobb gombbal a programra, majd válassza a kimeneti URL-cím másolása a vágólapra lehetőséget (vagy olvassa be ezt az információt a program információi és beállítások menüpontból a menüből).

A stream beágyazott lejátszóval, vagy egy adott célközönségnek élő megtekintésre elosztott készen áll.

> [!NOTE]
> Program létrehozása csatornák létrehozásának-nál kevesebb időt vesz igénybe.
