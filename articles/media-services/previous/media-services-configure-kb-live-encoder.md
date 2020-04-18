---
title: A Haivision KB kódoló konfigurálása egyetlen átviteli sebességű élő közvetítés küldésére az Azure-ba | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan konfigurálható a Haivision KB élő kódoló, hogy egyetlen bitráta-adatfolyamot küldjön az élő kódolásra engedélyezett AMS-csatornáknak.
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
ms.openlocfilehash: babb5a10818c8108bae34402962fd2503dbecbdc
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641683"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Egyetlen átviteli sebességű élő közvetítés küldéséhez használja a Haivision KB élő kódolót  
> [!div class="op_single_selector"]
> * [Haivision között](media-services-configure-kb-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

Ez a témakör bemutatja, hogyan konfigurálható a [Havision KB élő kódoló](https://www.haivision.com/products/kb-series/) kódoló, hogy egyetlen bitráta-adatfolyamot küldjön az élő kódolásra engedélyezett AMS-csatornáknak. További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

Ez az oktatóanyag bemutatja, hogyan kezelheti az Azure Media Services (AMS) szolgáltatást az Azure Media Services Explorer (AMSE) eszközzel. Ez az eszköz csak Windows rendszerű számítógépen fut. Mac vagy Linux használata esetén az Azure Portalon [csatornákat](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) és [programokat](media-services-portal-creating-live-encoder-enabled-channel.md)hozhat létre.

## <a name="prerequisites"></a>Előfeltételek
*   Hozzáférés a Haivision KB kódolóhoz, amely sW v5.01 vagy nagyobb.
* [Azure Media Services-fiók létrehozása](media-services-portal-create-account.md)
* Győződjön meg arról, hogy fut egy streamelési végpont. További információ: [Streamelési végpontok kezelése médiaszolgáltatások-fiókban](media-services-portal-manage-streaming-endpoints.md)
* Telepítse az [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) eszköz legújabb verzióját.
* Indítsa el az eszközt, és csatlakozzon Az AMS-fiókhoz.

## <a name="tips"></a>Tippek
* Lehetőség szerint használjon vezetékes internetkapcsolatot.
* A sávszélesség-követelmények meghatározásának jól használható módszere a streamelés átviteli sebességének megduplázása. Bár ez nem kötelező követelmény, segít enyhíteni a hálózati torlódások hatását.
* Szoftveralapú kódolók használata esetén zárja be a szükségtelen programokat.

## <a name="create-a-channel"></a>Csatorna létrehozása
1. Az AMSE eszközben keresse meg az **Élő** lapot, és kattintson a jobb gombbal a csatornaterületen belül. Válassza **a Csatorna létrehozása lehetőséget...** a menüből.
[Haivision között](./media/media-services-configure-kb-live-encoder/channel.png)
2. Adja meg a csatorna nevét, a megnevezés mező nem kötelező. A Csatornabeállítások csoportban válassza a **Normál** az Élő kódoláshoz lehetőséget, ha a bemeneti protokoll **rtmp**lesz. Az összes többi beállítást úgy hagyhatja meg, ahogy van. Győződjön meg arról, hogy **az Új csatorna indítása most** ki van jelölve.
3. Kattintson **a Csatorna létrehozása gombra.**
[Haivision között](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> A csatorna indítása akár 20 percet is igénybe vehet.

## <a name="configure-the-haivision-kb-encoder"></a>A Haivision KB kódoló konfigurálása
Ebben az oktatóanyagban a következő kimeneti beállításokat használja a program. A szakasz további részei részletesebben ismertetik a konfigurációs lépéseket.

Videó:
-   Kodek: H.264
-   Profil: Jó minőség (4.0-ás szint)
-   Bitráta: 5000 kbps
-   Kulcskép: 2 másodperc (60 képkocka)
-   Képkockasebesség: 30

Audio:
-   Kodek: AAC (LC)
-   Átviteli sebesség: 192 kbps
-   Mintavételi sebesség: 44,1 kHz

## <a name="configuration-steps"></a>Konfigurációs lépések
1.  Jelentkezzen be a Haivision KB felhasználói felületére.
2.  Kattintson a csatornavezérlő központ **Menü gombjára,** és válassza a **Csatorna hozzáadása lehetőséget.**  
    ![Képernyőkép 2017-08-14 at 9.15.09](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Írja be a **Csatorna nevét** a Név mezőbe, és kattintson a tovább gombra.  
    ![Képernyőkép 2017-08-14 at 9.19.07 AM](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Válassza ki a **csatornabemeneti forrást** a **bemeneti forrás** legördülő menüből, majd kattintson a tovább gombra.
    ![Képernyőkép 2017-08-14 at 9.20.44](./media/media-services-configure-kb-live-encoder/step4.png)
5.  A **Kódolósablon legördülő** menüből válassza a **H264-720-AAC-192 lehetőséget,** és kattintson a tovább gombra.
    ![Képernyőkép 2017-08-14 at 9.23.15](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Az **Új kimenet kiválasztása** legördülő menüben válassza az **RTMP** elemet, és kattintson a tovább gombra.  
    ![Képernyőkép 2017-08-14 at 9.27.51](./media/media-services-configure-kb-live-encoder/step6.png)
7.  A **Csatorna kimenetablakban** feltöltheti az Azure stream adatait. Illessze be az **RTMP-hivatkozást** a **kiszolgálóterület** kezdeti csatornabeállításából. A Csatorna nevének **Kimeneti név** csoportjában írja be a típust. Az Adatfolyam névsablon területén a sablon takarása RTMPStreamName_%video_bitrate% az adatfolyam elnevezéséhez.
    ![Képernyőkép 2017-08-14 at 9.33.17](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Kattintson a tovább gombra, majd a Kész gombra.
9.  A kódolócsatorna elindításához kattintson a **Lejátszás gombra.**  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Visszajátszás tesztelése
Nyissa meg az AMSE eszközt, és kattintson a jobb gombbal a tesztelendő csatornára. A menüben mutasson az Előnézet lejátszása elemre, és válassza ki az Azure Media Player rel lehetőséget.

Ha az adatfolyam megjelenik a lejátszóban, akkor a kódoló megfelelően van konfigurálva az AMS-hez való csatlakozáshoz.

Ha hiba érkezik, a csatornát alaphelyzetbe kell állítani, és módosítani kell a kódoló beállításait. Útmutatást a hibaelhárítási cikkben talál.

## <a name="create-a-program"></a>Program létrehozása
1.  A csatorna lejátszásának megerősítést követően hozzon létre egy programot. Az AMSE eszköz Élő lapján kattintson a jobb gombbal a programterületen belül, és válassza az Új program létrehozása parancsot.
[Haivision között](./media/media-services-configure-kb-live-encoder/program.png)
1.  Nevezze el a programot, és szükség esetén állítsa be az Archív ablak hosszát (amely alapértelmezés szerint négy óra). Megadhat egy tárolási helyet is, vagy alapértelmezettként hagyhatja.
2.  Jelölje be a Program indítása most jelölőnégyzetet.
3.  Kattintson a Program létrehozása gombra.
4.  A program futtatása után erősítse meg a lejátszást úgy, hogy a jobb gombbal a programra kattint, és a program(ok) lejátszásához navigál, majd kiválasztja az Azure Media Player programmal.
5.  A megerősítést követően kattintson ismét a jobb gombbal a programra, és válassza a Kimeneti URL másolása a vágólapra (vagy olvassa be ezt az információt a menü Program adatai és beállításai beállításából).

Az adatfolyam most már készen áll arra, hogy beágyazódjon egy lejátszóba, vagy élő megtekintésre a közönség nek terjesszék.

> [!NOTE]
> A program létrehozása kevesebb időt vesz igénybe, mint a csatorna létrehozása.
