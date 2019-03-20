---
title: A Haivision KB kódoló egyféle sávszélességű élő adatfolyamot küldeni az Azure-bA |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan konfigurálhatja a Haivision KB élő kódoló egy egyféle sávszélességű adatfolyamot küldeni a valós idejű kódolásra képes csatornák AMS.
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
ms.author: juliako;dbgeorge
ms.openlocfilehash: 058a1f964eb14d89628c92cbadd80511b7a27bae
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58014951"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Az élő Haivision KB kódoló használatával egy egyféle sávszélességű élő adatfolyamot küldeni  
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

Ez a témakör bemutatja, hogyan konfigurálhatja a [Havision KB élőadás-kódoló](https://www.haivision.com/products/kb-series/) kódoló egy egyfajta sávszélességű adatfolyamot AMS-csatorna is küldhet az élő kódolás engedélyezve vannak. További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

Ez az oktatóanyag bemutatja, hogyan kezelheti az Azure Media Services (AMS) az Azure Media Services Explorer (AMSE) eszközzel. Ez az eszköz csak akkor Windows-számítógépen fog futni. Ha Mac vagy Linux rendszeren, az Azure portal használatával hozzon létre [csatornák](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) és [programok](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Előfeltételek
*   A hozzáférést egy Haivision KB kódoló, Szoftveres v5.01 fut, vagy nagyobb.
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
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Adjon meg egy csatorna nevét, a Leírás mező kitöltése nem kötelező. Csatorna beállítások területén válassza ki a **Standard** a Live Encoding funkcióval beállítást is választja, a bemeneti protokoll beállítása **RTMP**. Hagyhatja, hogy a többi beállítás-jébe. Győződjön meg arról, hogy a **most indítsa el az új csatorna** van kiválasztva.
3. Kattintson a **csatorna létrehozása**.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> A csatorna mindaddig elindításához 20 percet is igénybe vehet.

## <a name="configure-the-haivision-kb-encoder"></a>A Haivision KB kódoló konfigurálása
Ebben az oktatóanyagban a következő kimeneti beállításokat használják. Ez a szakasz a többi konfigurációs lépések részletesebben ismerteti.

Videó:
-   Kodek: H.264
-   Profil: Nagy (4.0-s szint)
-   Átviteli sebesség: 5000 KB/s
-   Kulcsképkocka: 2 másodperc (60 keretek)
-   Képkockasebesség: 30

Hang:
-   Kodek: AAC (LC)
-   Átviteli sebesség: 192 Kb/s
-   Mintavételi gyakoriság: 44,1 kHz

## <a name="configuration-steps"></a>Konfigurációs lépések
1.  Jelentkezzen be a Haivision KB-os felhasználói felület.
2.  Kattintson a a **menügombra** a csatorna vezérlőközpont, és válasszon **Add-csatorna**  
    ![Képernyőkép – 2017-08-14 9.15.09 kor:](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Írja be a **csatorna neve** nevét a mezőbe, és kattintson a Tovább gombra.  
    ![Képernyőkép – 2017-08-14 9.19.07 kor:](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Válassza ki a **csatorna bemeneti forrás** származó a **bemeneti forrás** listából, és kattintson a Tovább gombra.
    ![Képernyőkép – 2017-08-14 9.20.44 kor:](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Az a **kódoló sablon** listából válasszon **H264-720-AAC-192** , és kattintson a Tovább gombra.
    ![Képernyőkép – 2017-08-14 9.23.15 kor:](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Az a **válassza ki az új kimeneti** válassza a legördülő **RTMP** , és kattintson a Tovább gombra.  
    ![Képernyőkép – 2017-08-14 9.27.51 kor:](./media/media-services-configure-kb-live-encoder/step6.png)
7.  Az a **csatorna kimeneti** ablakban töltse ki az Azure stream információkat. Illessze be a **RTMP** a kezdeti csatorna beállításai hivatkozásait a **kiszolgáló** területen. Az a **Kimenetnév** területen írja be a csatorna nevét. A Stream sablon területen a sablon RTMPStreamName_ video_bitrate % használatával nevezze el a streamet.
    ![Képernyőkép – 2017-08-14 9.33.17 kor:](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Kattintson a Tovább gombra, majd kattintson a Kész gombra.
9.  Kattintson a **lejátszás gombra** a kódoló channel indítására.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Teszt lejátszás
Keresse meg az AMSE eszköz, és kattintson a jobb gombbal a csatorna tesztelését. A menüben mutasson a lejátszás az előzetes verzióra, és válassza ki az Azure Media Player.

Ha a lejátszó az adatfolyam jelenik meg, majd a kódoló konfigurációja megfelelő AMS csatlakozni.

Ha hibaüzenet érkezik, a csatornát kell állítani, és kódoló beállítások úgy vannak megadva. Tekintse meg a hibaelhárításról szóló cikk útmutatást.

## <a name="create-a-program"></a>Egy olyan program létrehozásához
1.  Csatorna lejátszási ellenőrzése után hozzon létre egy programot. Az élő lapot az AMSE eszköz a program területén kattintson a jobb gombbal, és kattintson az új Program létrehozása.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Nevezze el a program, és ha szükséges, módosítsa az archiválási időszak hossza (amely alapértelmezés szerint 4 óra). Adjon meg egy tárolási helyet is, vagy hagyja meg az alapértelmezett.
2.  Jelölje be a Program most az elindítása.
3.  Kattintson a létrehozás Program.
4.  Ha a program fut, erősítse meg a lejátszás kattintson a jobb gombbal a program, és játssza le a program(ok), és válassza az Azure Media Player.
5.  Miután megerősítette, kattintson ismét a jobb gombbal a program, és válassza ki a példányt vágólapra a kimenetet URL-cím (vagy ezen információk lekérése, a Program információkat és a beállítások lehetőséget a menüből).

A stream beágyazott lejátszóval, vagy egy adott célközönségnek élő megtekintésre elosztott készen áll.

> [!NOTE]
> Program létrehozása csatornák létrehozásának-nál kevesebb időt vesz igénybe.
