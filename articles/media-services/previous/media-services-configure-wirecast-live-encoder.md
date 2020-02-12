---
title: Egy egyféle sávszélességű élő adatfolyamot küldeni a Telestream Wirecast kódoló konfigurálása |} A Microsoft Docs
description: 'Ez a témakör bemutatja, hogyan konfigurálhatja a Wirecast egyféle sávszélességű adatfolyamot küldeni a valós idejű kódolásra képes csatornák AMS élő kódoló. '
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: 1d9d63aa6b3da1b8d8389722bd5af0eeed585d03
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134978"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Használja a Wirecast kódoló egyféle sávszélességű élő adatfolyamot küldeni 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
>
>

Ez a cikk bemutatja, hogyan konfigurálhatja a [Wirecast](https://www.telestream.net/wirecast/overview.htm) élő kódolót úgy, hogy egyetlen sávszélességű adatfolyamot küldjön az élő kódolásra engedélyezett AMS-csatornáknak. További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

Ez az oktatóanyag bemutatja, hogyan kezelheti az Azure Media Services (AMS) az Azure Media Services Explorer (AMSE) eszközzel. Ez az eszköz csak akkor Windows-számítógépen fog futni. Ha Mac vagy Linux operációs rendszert használ, [csatornák](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) és [programok](media-services-portal-creating-live-encoder-enabled-channel.md)létrehozásához használja a Azure Portal.

> [!NOTE]
> A kódolók számára a TLS 1,2-et a RTMP protokoll használatakor kell támogatni. A TLS 1,2-követelmény miatt használja a Wirecast 13.0.2 vagy újabb verzióját.

## <a name="prerequisites"></a>Előfeltételek
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

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Adjon meg egy csatorna nevét, a Leírás mező kitöltése nem kötelező. A Channel Settings (csatorna beállításai) területen válassza a **standard** lehetőséget a Live Encoding beállításnál, a bemeneti protokollt pedig az **RTMP**értékre állítva. Hagyhatja, hogy a többi beállítás-jébe.

    Győződjön meg arról, hogy az **új csatorna elindítása most** lehetőség van kiválasztva.

3. Kattintson a **csatorna létrehozása**gombra.

   ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> A csatorna mindaddig elindításához 20 percet is igénybe vehet.
>
>

A csatorna elindítása után beállíthatja [a kódolót](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> A számlázás elindul, amint a csatorna üzemkész állapotba kerül. További információ: [csatorna állapotai](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfigure_wirecast_rtmp-configure-the-telestream-wirecast-encoder"></a>a Wirecast-kódoló <a id="configure_wirecast_rtmp" />konfigurálása
Ebben az oktatóanyagban a következő kimeneti beállításokat használják. Ez a szakasz a többi konfigurációs lépések részletesebben ismerteti.

**Videó**:

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
2. Konfigurálja a kimenetet a **kimenet** lapra való navigálással, és válassza a **kimeneti beállítások...** lehetőséget.

    Győződjön meg arról, hogy a **kimeneti célhely** az **RTMP-kiszolgáló**értékre van beállítva.
3. Kattintson az **OK** gombra.
4. A beállítások lapon adja meg, hogy a **cél** mező **Azure Media Services**legyen.

    A kódolási profil előre ki van választva az **Azure H. 264 720p 16:9 (1280x720)** szolgáltatásban. A beállítások testreszabásához válassza a legördülő menü jobb oldalán lévő fogaskerék ikont, majd válassza az **új beállításkészlet**lehetőséget.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Kódoló készletek beállítása.

    Nevezze el a készletet, és ellenőrizze a következőket ajánlott beállítások:

    **Videóinak**

   * Kódoló: MainConcept H.264
   * A képkockák másodpercenkénti: 30
   * Átlagos átviteli sebesség: 5000 kbit/s (módosítható hálózati korlátai alapján)
   * Profil: Main
   * Kulcs keret minden: 60 keretek

     **Hang**

   * Cél átviteli sebesség: 192 Kbit/s
   * Mintavételi gyakoriság: 44.100 kHz

     ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Kattintson a **Mentés** gombra.

    Az Encoding mező most már rendelkezik az újonnan létrehozott profil kiválasztható.

    Győződjön meg arról, hogy az új profil van kiválasztva.
7. Szerezze be a csatorna bemeneti URL-címét a Wirecast **RTMP-végponthoz**való hozzárendeléshez.

    Lépjen vissza az AMSE eszköz, és a csatorna befejezési állapotának ellenőrzéséhez. Ha az állapot már **megkezdődött** a **futtatástól**kezdve, beolvashatja a bemeneti URL-címet.

    Amikor a csatorna fut, kattintson a jobb gombbal a csatorna nevére, navigáljon lefelé a **beviteli URL másolása a vágólapra** , majd válassza az **elsődleges bemeneti URL-cím**elemet.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. A Wirecast **kimeneti beállítások** ablakban illessze be ezeket az információkat a kimenet szakasz **címe** mezőjébe, és rendeljen hozzá egy stream-nevet.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Kattintson az **OK** gombra.
2. A fő **Wirecast** képernyőn ellenőrizze, hogy a videó és hang bemeneti forrásai készen állnak-e, majd kattintson a bal felső sarokban található **stream** elemre.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Mielőtt a **stream**elemre kattint, meg **kell** győződnie arról, hogy a csatorna készen áll.
> Bizonyosodjon meg róla, hogy ne maradjon a csatorna egy kész állapotú > 15 percnél hosszabb ideig hírcsatorna egy bemeneti hozzájárulás nélkül.
>
>

## <a name="test-playback"></a>Teszt lejátszás

Keresse meg az AMSE eszköz, és kattintson a jobb gombbal a csatorna tesztelését. A menüben vigye az egérmutatót **az előnézet lejátszásához** , és válassza a **Azure Media Player**lehetőséget.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Ha a lejátszó az adatfolyam jelenik meg, majd a kódoló konfigurációja megfelelő AMS csatlakozni.

Ha hibaüzenet érkezik, a csatornát kell állítani, és kódoló beállítások úgy vannak megadva. Útmutatásért tekintse meg a [hibaelhárítással](media-services-troubleshooting-live-streaming.md) foglalkozó cikket.  

## <a name="create-a-program"></a>Egy olyan program létrehozásához
1. Csatorna lejátszási ellenőrzése után hozzon létre egy programot. A AMSE eszköz **élő** lapján kattintson a jobb gombbal a program területére, és válassza az **új program létrehozása**lehetőséget.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Adja meg a program nevét, és ha szükséges, állítsa be az **archiválási időszak hosszát** (amely alapértelmezés szerint négy óra). Adjon meg egy tárolási helyet is, vagy hagyja meg az alapértelmezett.  
3. Jelölje be a **program indítása** jelölőnégyzetet.
4. Kattintson a **program létrehozása**lehetőségre.  

   >[!NOTE]
   >Program létrehozása csatornák létrehozásának-nál kevesebb időt vesz igénybe.
       
5. A program futása után erősítse meg a lejátszást. ehhez kattintson a jobb gombbal a programra, és navigáljon **a program (ok) lejátszásához** , majd válassza **a Azure Media Player**lehetőséget.  
6. A megerősítést követően kattintson a jobb gombbal a programra, majd válassza a **kimeneti URL-cím másolása a vágólapra** lehetőséget (vagy olvassa be ezt az információt a **program információi és beállítások** menüpontból a menüből).

A stream beágyazott lejátszóval, vagy egy adott célközönségnek élő megtekintésre elosztott készen áll.  

## <a name="troubleshooting"></a>Hibakeresés
Útmutatásért tekintse meg a [hibaelhárítással](media-services-troubleshooting-live-streaming.md) foglalkozó cikket.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
