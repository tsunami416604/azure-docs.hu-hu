---
title: Konfigurálja úgy a Telestream Wirecast kódolót, hogy egyetlen bitráta-élő közvetítést küldjön | Microsoft dokumentumok
description: 'Ez a témakör bemutatja, hogyan konfigurálható a Wirecast élő kódoló, hogy egyetlen bitráta-adatfolyamot küldjön az élő kódolásra engedélyezett AMS-csatornáknak. '
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
ms.openlocfilehash: 8e3705aaecb0760513f0605aece89b7ffc0044a8
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641649"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Egyetlen bitráta élő közvetítés küldéséhez a Wirecast kódoló val 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>

Ez a cikk bemutatja, hogyan konfigurálható a [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) élő kódoló, hogy küldjön egy bitráta stream AMS csatornák, amelyek engedélyezve vannak az élő kódolás. További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

Ez az oktatóanyag bemutatja, hogyan kezelheti az Azure Media Services (AMS) szolgáltatást az Azure Media Services Explorer (AMSE) eszközzel. Ez az eszköz csak Windows rendszerű számítógépen fut. Mac vagy Linux használata esetén az Azure Portalon [csatornákat](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) és [programokat](media-services-portal-creating-live-encoder-enabled-channel.md)hozhat létre.

> [!NOTE]
> A kódolóknak támogatniuk kell a TLS 1.2 protokollt RTMPS protokollok használata esetén. Használja a Wirecast 13.0.2-es vagy újabb verzióját a TLS 1.2 követelmény miatt.

## <a name="prerequisites"></a>Előfeltételek
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

    ![vezetékes](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Adja meg a csatorna nevét, a megnevezés mező nem kötelező. A Csatornabeállítások csoportban válassza a **Normál** az Élő kódoláshoz lehetőséget, ha a bemeneti protokoll **rtmp**lesz. Az összes többi beállítást úgy hagyhatja meg, ahogy van.

    Győződjön meg arról, hogy **az Új csatorna indítása most** ki van jelölve.

3. Kattintson **a Csatorna létrehozása gombra.**

   ![vezetékes](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> A csatorna indítása akár 20 percet is igénybe vehet.
>
>

A csatorna indítása közben [konfigurálhatja a kódolót.](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp)

> [!IMPORTANT]
> A számlázás akkor kezdődik, amikor a Csatorna készen áll. További információ: [Channel's states](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />A Telestream Wirecast kódoló konfigurálása
Ebben az oktatóanyagban a következő kimeneti beállításokat használja a program. A szakasz további részei részletesebben ismertetik a konfigurációs lépéseket.

**Videó**:

* Kodek: H.264
* Profil: Jó minőség (4.0-ás szint)
* Bitráta: 5000 kbps
* Kulcskép: 2 másodperc (60 másodperc)
* Képkockasebesség: 30

**Hang**:

* Kodek: AAC (LC)
* Átviteli sebesség: 192 kbps
* Mintavételi sebesség: 44,1 kHz

### <a name="configuration-steps"></a>Konfigurációs lépések
1. Nyissa meg a Telestream Wirecast alkalmazást a használt gépen, és állítsa be az RTMP streameléshez.
2. A kimenet konfigurálása a **Kimenet** lapra való navigálással és a **Kimeneti beállítások...** parancsra kattintva.

    Ellenőrizze, hogy a **kimeneti cél** **az RTMP-kiszolgálóra van-e**állítva.
3. Kattintson az **OK** gombra.
4. A beállítások lapon állítsa be a **Cél** mezőt **Azure Media Services szolgáltatásra.**

    A kódolási profil előre be van jelölve az **Azure H.264 720p 16:9 (1280x720)** számára. A beállítások testreszabásához válassza a legördülő menü jobb oldalán található fogaskerék ikont, majd az **Új készlet lehetőséget.**

    ![vezetékes](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Kódolókészletek konfigurálása.

    Nevezze el a készletet, és ellenőrizze a következő ajánlott beállításokat:

    **Videóinak**

   * Kódoló: MainConcept H.264
   * Képkocka/másodperc: 30
   * Átlagos átviteli sebesség: 5000 kbit/mp (a hálózati korlátozások alapján módosítható)
   * Profil: Fő
   * Kulcskeret minden: 60 képkocka

     **Hang**

   * Cél átviteli sebesség: 192 kbit/mp
   * Mintavételi sebesség: 44.100 kHz

     ![vezetékes](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Kattintson a **Mentés** gombra.

    A Kódolás mezőben most már választható az újonnan létrehozott profil.

    Ellenőrizze, hogy az új profil ki van-e jelölve.
7. A csatorna bemeneti URL-címének beírása a Wirecast **RTMP-végponthoz**való hozzárendeléshez.

    Lépjen vissza az AMSE eszközhöz, és ellenőrizze a csatorna készültségi állapotát. Miután az állapot **átvált a kezdő indításról** **a futtatásra,** beszerezheti a bemeneti URL-címet.

    Amikor a csatorna fut, kattintson a jobb gombbal a csatorna nevére, lefelé navigálva mutasson a **Szövegbeviteli URL másolási URL-címre a vágólapra,** majd válassza **az Elsődleges bemeneti URL parancsot**.  

    ![vezetékes](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. A Wirecast **kimeneti beállítások** ablakban illessze be ezt az információt a kimeneti szakasz **Cím** mezőjébe, és rendeljen hozzá egy adatfolyam-nevet.

    ![vezetékes](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Válassza **az OK gombot.**
2. A fő **Wirecast** képernyőn ellenőrizze, hogy a video- és hangbemeneti források készen állnak-e, majd nyomja meg a **Stream** a bal felső sarokban.

    ![vezetékes](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Mielőtt a **Stream**gombra kattintana, meg **kell** győződnie arról, hogy a csatorna készen áll.
> Ügyeljen arra is, hogy ne hagyja el a csatornát készenléti állapotban, > 15 percnél hosszabb ideig ne hagyjon bemeneti hozzájárulási hírcsatornát.
>
>

## <a name="test-playback"></a>Visszajátszás tesztelése

Nyissa meg az AMSE eszközt, és kattintson a jobb gombbal a tesztelendő csatornára. A menüben mutasson **az Előnézet lejátszása** elemre, és válassza **az Azure Media Player alkalmazással**lehetőséget.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Ha az adatfolyam megjelenik a lejátszóban, akkor a kódoló megfelelően van konfigurálva az AMS-hez való csatlakozáshoz.

Ha hiba érkezik, a csatornát alaphelyzetbe kell állítani, és módosítani kell a kódoló beállításait. Útmutatást a [hibaelhárítási](media-services-troubleshooting-live-streaming.md) cikkben talál.  

## <a name="create-a-program"></a>Program létrehozása
1. A csatorna lejátszásának megerősítést követően hozzon létre egy programot. Az AMSE eszköz **Élő** lapján kattintson a jobb gombbal a programterületen belül, és válassza az **Új program létrehozása parancsot.**  

    ![vezetékes](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Nevezze el a programot, és szükség esetén állítsa be az **Archív ablak hosszát** (amely alapértelmezés szerint négy óra). Megadhat egy tárolási helyet is, vagy alapértelmezettként hagyhatja.  
3. Jelölje be **a Program indítása most** jelölőnégyzetet.
4. Kattintson **a Program létrehozása gombra.**  

   >[!NOTE]
   >A program létrehozása kevesebb időt vesz igénybe, mint a csatorna létrehozása.
       
5. A program futtatása után erősítse meg a lejátszást úgy, hogy a jobb gombbal a programra kattint, és **a program(ok) lejátszására** navigál, majd az **Azure Media Player programmal**választ.  
6. A megerősítést követően kattintson ismét a jobb gombbal a programra, és válassza **a Kimeneti URL másolása a vágólapra** (vagy olvassa be ezt az információt a **menü Program adatai és beállításai** beállításából).

Az adatfolyam most már készen áll arra, hogy beágyazódjon egy lejátszóba, vagy élő megtekintésre a közönség nek terjesszék.  

## <a name="troubleshooting"></a>Hibaelhárítás
Útmutatást a [hibaelhárítási](media-services-troubleshooting-live-streaming.md) cikkben talál.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
