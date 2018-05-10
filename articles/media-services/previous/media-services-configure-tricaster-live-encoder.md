---
title: A NewTek TriCaster kódoló egyféle sávszélességű élő adatfolyamot küldeni |} Microsoft Docs
description: Ez a témakör bemutatja, hogyan konfigurálhatja a Tricaster élő kódoló egy egyféle sávszélességű adatfolyamot küldeni AMS élő kódolásra képes csatornák.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: ''
ms.assetid: 8973181a-3059-471a-a6bb-ccda7d3ff297
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako;cenkd;anilmur
ms.openlocfilehash: 8084f32ac8cc2184d93796468ad66fb73398e876
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Használja a NewTek TriCaster kódoló egyféle sávszélességű élő adatfolyamot küldeni
> [!div class="op_single_selector"]
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Élő elemi](media-services-configure-elemental-live-encoder.md)
> * [wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

Ez a cikk ismerteti, hogyan konfigurálható a [NewTek TriCaster](http://newtek.com/products/tricaster-40.html) élő kódoló egy egyfajta sávszélességű adatfolyamot AMS-csatorna is küldhet a valós idejű kódolásra engedélyezve vannak. További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

Ez az oktatóanyag bemutatja, hogyan kezelheti az Azure Media Services (AMS) Azure Media Services Explorer (AMSE) eszközzel. Ez az eszköz csak a Windows rendszerű Számítógépeken fut. Ha Mac vagy Linux, létrehozásához használja az Azure-portálon [csatornák](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) és [programok](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> Az AMS élő kódolásra képes csatornák hírcsatornát hozzájárulás küldési Tricaster használatakor lehet videó/hang hibáktól az élő esemény Tricaster, például a gyors közötti hírcsatornák kivágja, vagy táblagépükkel/való váltás az egyes szolgáltatások használatakor. Ezek a problémák kijavítása addig az AMS-csapat dolgozik, nem ajánlott használni ezeket a szolgáltatásokat.
>
>

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

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Adjon meg egy csatorna nevét, a Leírás mezőt nem kötelező megadni. A csatorna beállítások területen válassza a **szabványos** az élő kódolás lehetőségnél a bemeneti protokoll beállítása **RTMP**. A többi beállítás, hagyhatja.

    Győződjön meg arról, hogy a **most indítsa el az új csatorna** van kiválasztva.

3. Kattintson a **csatornát létrehozni**.

   ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> A csatorna mindaddig elindításához 20 percet is igénybe vehet.
>
>

A csatorna indul, míg is [a kódoló](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> A számlázás indul, amint csatorna kész állapotba kerül. További információkért lásd: [csatorna állapotok](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_tricaster_rtmp></a>A NewTek TriCaster kódoló
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
1. Hozzon létre egy új **NewTek TriCaster** projekt attól függően, hogy milyen bemeneti videoforrást használatban van.
2. Egyszer, hogy a projektben található a **adatfolyam** gombra, és hozzáférjen a konfigurációs adatfolyam menü mellett fogaskerék ikonra.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Miután megnyílt a menüben, kattintson a **új** kapcsolat fejléc alatt. Ha a rendszer kéri a kapcsolat típusát, válassza ki a **Adobe Flash**.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Kattintson az **OK** gombra.
5. A legördülő lista alatt nyílra kattintva most importálni egy FMLE profil **Streaming profil** és lépjen az **Tallózás**.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Nyissa meg a konfigurált FMLE profilt menteni.
7. Jelölje ki, majd nyomja le az ENTER **OK**.

    A profil a feltöltést követően folytassa a következő lépéssel.
8. A csatorna Get bemeneti URL-cím ahhoz, hogy rendelje hozzá a Tricaster **RTMP végpont**.

    Lépjen vissza az AMSE eszköz, és a csatorna befejezési állapotát. Miután állapota megváltozott, a **indítása** való **futtató**, kaphat a bemeneti URL-CÍMÉT.

    Ha a csatorna fut, kattintson a jobb gombbal a csatorna neve, felett az egérmutatót navigáljon **vágólapra másolás bemeneti URL-cím** , és válassza **elsődleges bemeneti URL-cím**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Illessze be ezt az információt a **hely** eleménél **Flash Server** a Tricaster projektben. Is hozzárendelhet a adatfolyam nevet adja meg a **adatfolyam-azonosító** mező.

    Ha az adatfolyam információt adott FMLE profilt, azt is importálhatók ebben a szakaszban kattintva **beállítások importálása**, navigáljon a mentett FMLE profilt, és kattintson a **OK**. A kiszolgáló Flash mezőket fel kell töltenie FMLE származó információkkal.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. Ha befejezte, kattintson a **OK** a képernyő alján. Készen áll a Tricaster video- és ráfordítások, kezdje kattintva AMS streamelés a **adatfolyam** gombra.

     ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Kattintás előtt **adatfolyam**, akkor **kell** győződjön meg arról, hogy készen áll-e a csatornát.
> Győződjön meg arról, hogy nem a csatorna üzemkész állapotban hagyja meg az adatcsatorna-> 15 percnél hosszabb ideig bemeneti hozzájárulás nélkül.
>
>

## <a name="test-playback"></a>Teszt lejátszás
Keresse meg az AMSE eszköz, és kattintson a jobb gombbal a csatornát kell tesztelni. A menüben rámutat **lejátszás az előzetes** válassza **Azure Media Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Ha az adatfolyam a Windows Media player jelenik meg, majd a kódoló megfelelően van konfigurálva AMS való kapcsolódáshoz.

Ha hibaüzenetet kap, a csatorna le kell állítani, és kódoló beállításai módosul. Tekintse meg a [hibaelhárítási](media-services-troubleshooting-live-streaming.md) a cikk útmutatást.  

## <a name="create-a-program"></a>Hozzon létre egy programot
1. Miután csatorna lejátszás megerősítjük, hozzon létre egy programot. Az a **Live** az AMSE eszköz a lapot, kattintson a jobb gombbal a program területen belül, és válasszon **hozzon létre új Program**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
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

## <a name="next-step"></a>Következő lépés
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
