---
title: Konfigurálja a NewTek TriCaster kódolót úgy, hogy egyetlen bitráta-élő közvetítést küldjön | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan konfigurálhatja a Tricaster élő kódolót úgy, hogy egyetlen bitráta-adatfolyamot küldjön az élő kódolásra engedélyezett AMS-csatornáknak.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 8973181a-3059-471a-a6bb-ccda7d3ff297
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 7909fbb958a66d00616d4ed1b844d02bb47d997e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152499"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>A NewTek TriCaster kódoló segítségével egyetlen bitráta élő közvetítést küldhet  
> [!div class="op_single_selector"]
> * [Tricaster között](media-services-configure-tricaster-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

Ez a cikk bemutatja, hogyan konfigurálhatja a [NewTek TriCaster](https://newtek.com/products/tricaster-40.html) élő kódolót, hogy egyetlen bitráta-adatfolyamot küldjön az élő kódolásra engedélyezett AMS-csatornáknak. További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

Ez az oktatóanyag bemutatja, hogyan kezelheti az Azure Media Services (AMS) szolgáltatást az Azure Media Services Explorer (AMSE) eszközzel. Ez az eszköz csak Windows rendszerű számítógépen fut. Mac vagy Linux használata esetén az Azure Portalon [csatornákat](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) és [programokat](media-services-portal-creating-live-encoder-enabled-channel.md)hozhat létre.

Ha a Tricastert használja az élő kódolásra engedélyezett AMS-csatornákba való hozzájárulási hírcsatorna küldésére, akkor video- és hanghibák léphetnek fel az élő eseményben, ha a Tricaster bizonyos funkcióit használja, például a hírcsatornák közötti gyors vágást, vagy a palákra való váltást. Az AMS csapata dolgozik a problémák megoldásán, addig nem ajánlott használni ezeket a funkciókat.

> [!NOTE]
>  Fontolja meg a TLS 1.2,az előre kibékített TLS verzióra való áttérést.

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

    ![tricaster között](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Adja meg a csatorna nevét, a megnevezés mező nem kötelező. A Csatornabeállítások csoportban válassza a **Normál** az Élő kódoláshoz lehetőséget, ha a bemeneti protokoll **rtmp**lesz. Az összes többi beállítást úgy hagyhatja meg, ahogy van.

    Győződjön meg arról, hogy **az Új csatorna indítása most** ki van jelölve.

3. Kattintson **a Csatorna létrehozása gombra.**

   ![tricaster között](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> A csatorna indítása akár 20 percet is igénybe vehet.
>
>

A csatorna indítása közben [konfigurálhatja a kódolót.](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp)

> [!IMPORTANT]
> A számlázás akkor kezdődik, amikor a Csatorna készen áll. További információ: [Channel's states](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="configure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>A NewTek TriCaster kódoló konfigurálása

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

1. Hozzon létre egy új **NewTek TriCaster** projekt attól függően, hogy milyen video bemeneti forrást használnak.
2. Miután a projekten belül, keresse meg a **Stream** gombot, és kattintson a fogaskerék ikonra mellette az adatfolyam konfigurációs menüjének eléréséhez.

    ![tricaster között](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. A menü megnyitása után kattintson az **Új** gombra a Kapcsolat fejléc alatt. Amikor a rendszer kéri a kapcsolat típusát, válassza az **Adobe Flash**lehetőséget.

    ![tricaster között](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Kattintson az **OK** gombra.
5. Az FMLE-profilok most már importálhatók, ha a **Streamelési profil** alatti legördülő nyílra kattintanak, és navigálnak a **Tallózás elemre.**

    ![tricaster között](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Lépjen a beállított FMLE-profil mentésére.
7. Jelölje ki, és nyomja **le az OK gombot.**

    A profil feltöltése után folytassa a következő lépéssel.
8. A csatorna bemeneti URL-címének beírása a Tricaster **RTMP végponthoz**való hozzárendeléséhez.

    Lépjen vissza az AMSE eszközhöz, és ellenőrizze a csatorna készültségi állapotát. Miután az állapot **átvált a kezdő indításról** **a futtatásra,** beszerezheti a bemeneti URL-címet.

    Amikor a csatorna fut, kattintson a jobb gombbal a csatorna nevére, lefelé navigálva mutasson a **Szövegbeviteli URL másolása a vágólapra,** majd válassza az **Elsődleges bemeneti URL lehetőséget**.  

    ![tricaster között](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Illessze be ezt az információt a **Hely** mezőbe a Tricaster projekt **Flash Server területén.** Rendeljen hozzá egy adatfolyam-nevet is az **Adatfolyam-azonosító** mezőben.

    Ha adatfolyam-adatokat adott hozzá az FMLE-profilhoz, akkor az **importálási beállítások**, a mentett FMLE-profilra való navigálás és az **OK**gombra kattintva is importálható ebbe a szakaszba. A megfelelő Flash Server mezőknek fel kell nagyulniuk az FMLE-ből származó információkkal.

    ![tricaster között](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. Ha végzett, kattintson az **OK** gombra a képernyő alján. Amikor a Tricasterbe bevitt video- és hangbemenetek készen állnak, a **Stream** gombra kattintva kezdje el az AMS-re való streamelést.

     ![tricaster között](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Mielőtt a **Stream**gombra kattintana, meg **kell** győződnie arról, hogy a csatorna készen áll.
> Ügyeljen arra is, hogy ne hagyja el a csatornát készenléti állapotban, > 15 percnél hosszabb ideig ne hagyjon bemeneti hozzájárulási hírcsatornát.
>
>

## <a name="test-playback"></a>Visszajátszás tesztelése

Nyissa meg az AMSE eszközt, és kattintson a jobb gombbal a tesztelendő csatornára. A menüben mutasson **az Előnézet lejátszása** elemre, és válassza **az Azure Media Player alkalmazással**lehetőséget.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Ha az adatfolyam megjelenik a lejátszóban, akkor a kódoló megfelelően van konfigurálva az AMS-hez való csatlakozáshoz.

Ha hiba érkezik, a csatornát alaphelyzetbe kell állítani, és módosítani kell a kódoló beállításait. Útmutatást a [hibaelhárítási](media-services-troubleshooting-live-streaming.md) cikkben talál.  

## <a name="create-a-program"></a>Program létrehozása

1. A csatorna lejátszásának megerősítést követően hozzon létre egy programot. Az AMSE eszköz **Élő** lapján kattintson a jobb gombbal a programterületen belül, és válassza az **Új program létrehozása parancsot.**  

    ![tricaster között](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
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

## <a name="next-step"></a>Következő lépés

Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
