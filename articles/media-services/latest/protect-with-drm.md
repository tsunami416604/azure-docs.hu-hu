---
title: DRM a dinamikus titkosítás és a licenc kézbesítési szolgáltatás használata az Azure Media Services |} A Microsoft Docs
description: Az Azure Media Services segítségével a streamjeit Microsoft PlayReady-, Google Widevine- vagy Apple FairPlay-licencekkel titkosíthatja.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ce3b7a29f6f57b2bc309c719dbbab6c4574f0a46
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306489"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Oktatóanyag: A DRM dinamikus titkosítási és licenctovábbítási szolgáltatás használata

Az Azure Media Services segítségével a streamjeit Microsoft PlayReady-, Google Widevine- vagy Apple FairPlay-licencekkel titkosíthatja. Részletes ismertetése: [Content protection szolgáltatása a dinamikus titkosítás segítségével](content-protection-overview.md).

Ezenkívül a Media Services része egy szolgáltatás, amelynek segítségével PlayReady, Widevine vagy FairPlay DRM-licenceket továbbíthat. Amikor a felhasználók DRM-védelemmel rendelkező tartalmat kérnek, a lejátszóalkalmazás licencet kér a Media Services licencelési szolgáltatástól. Ha a lejátszóalkalmazás hitelesítve van, a Media Services licencelési szolgáltatás kiadja a licencet. A licencek tartalmazzák a feloldási kulcsot, amelynek segítségével az ügyféllejátszó képes feloldani a titkosítást, majd streamelni a kért tartalmakat.

Ez a cikk a [DRM használatával való titkosítás](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) mintáján alapul. 

A cikkben leírt minta a következőt eredményezi:

![AMS DRM-védelemmel a videó](./media/protect-with-drm/ams_player.png)

Ez az oktatóanyag a következőket mutatja be:    

> [!div class="checklist"]
> * Hozzon létre egy kódolási átalakító
> * A token ellenőrzése használt aláíró kulcs beállítása
> * A tartalom fő házirend követelményeinek beállítása
> * Hozzon létre egy StreamingLocator a megadott adatfolyam-továbbítási szabályzat
> * Hozzon létre egy URL-címet használt lejátszási a fájl

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei.

* Tekintse meg a [Content Protection áttekintését](content-protection-overview.md) ismertető cikket.
* Tekintse át a [a hozzáférés-vezérléssel DRM-mel a content protection rendszer tervezése](design-multi-drm-system-with-access-control.md)
* A Visual Studio Code vagy a Visual Studio telepítése
* Hozzon létre egy új Azure Media Services-fiókot [az ebben a gyors útmutatóban](create-account-cli-quickstart.md) leírt módon.
* Szerezze be a Media Services API-k használatához szükséges hitelesítő adatokat az [adatelérési API-kat](access-api-cli-how-to.md) bemutató szakasz leírását követve.
* Állítsa be a megfelelő értékeket az alkalmazás konfigurációs fájljában (appsettings.json).

## <a name="download-code"></a>Kód letöltése

Klónozza a gépre az ebben a cikkben ismertetett teljes .NET-mintát tartalmazó GitHub-adattárat a következő paranccsal:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Az „Encrypt with DRM” minta az [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) mappában található.

> [!NOTE]
> A minta egyedi erőforrásokat hoz létre az alkalmazás minden futtatásakor. Általában újból felhasználja a meglévő erőforrásokat, például az átalakításokat és a szabályzatokat (ha a meglévő erőforrás rendelkezik a szükséges konfigurációkkal). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>A Media Services API-k használatának megkezdése a .NET SDK-val

Ha szeretné megkezdeni a Media Services API-k használatát a .NET-tel, létre kell hoznia egy **AzureMediaServicesClient** objektumot. Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. A cikk elején klónozott kódban a **GetCredentialsAsync** függvény létrehozza a ServiceClientCredentials objektumot a helyi konfigurációs fájlban szereplő hitelesítési adatok alapján. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Kimeneti objektum létrehozása  

A kimeneti [objektum](assets-concept.md) tárolja a kódolási feladat eredményeit.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Kódoló átalakítás beszerzése vagy létrehozása

Egy új [átalakításpéldány](transforms-jobs-concept.md) létrehozásakor meg kell adnia, milyen kimenetet szeretne létrehozni. A szükséges paraméter egy `transformOutput` objektumot, az alábbi kódban látható módon. Minden egyes TransformOutput tartalmaz egy **Előbeállítást**. Előre definiált videó és/vagy hang feldolgozási műveletek, amelyeket szeretne létrehozni a kívánt TransformOutput lépésenként ismerteti. Az ebben a cikkben leírt minta az **AdaptiveStreaming** nevű beépített előzetes beállítást használja. Az előzetes beállítás a bemeneti videót egy automatikusan létrehozott sávszélességi skálává (sávszélesség–felbontás párokká) kódolja a bemeneti felbontás és sávszélesség alapján, majd ISO MP4-fájlokat hoz létre H.264 kódolású video- és AAC kódolású audiosávokkal, amelyek megfelelnek a sávszélesség–felbontás pároknak. 

Új **átalakítások** létrehozása előtt ellenőrizze a **Get** metódussal, hogy létezik-e már átalakítás, ahogyan az az alábbi kódban látható.  A Media Services 3-as verziója esetében a **Get** metódusok **null** értéket adnak vissza, ha az entitás nem létezik (a kis- és nagybetűket meg nem különböztető névellenőrzés történik).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Feladat elküldése

Ahogy korábban említettük, az **átalakítási** objektum a recept, a [feladat](transforms-jobs-concept.md) pedig maga a kérés a Media Services számára, hogy alkalmazza az adott **átalakítást** egy meghatározott bemeneti video- vagy audiotartalomra. A **feladat** meghatároz bizonyos adatokat, például a bemeneti videó és a kimenet helyét.

Ebben az oktatóanyagban egy olyan fájl alapján hozzuk létre a feladat bemenetét, amely közvetlenül egy [HTTPs forrás URL-jéből](job-input-from-http-how-to.md) van betöltve.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Várakozás a feladat befejeződésére

A feladat végrehajtása némi időt vesz igénybe, és fontos, hogy értesüljön arról, ha ez megtörtént. Az alábbi kódminta bemutatja, hogyan kérdezheti le a **feladat** állapotát a szolgáltatásból. Éles alkalmazások esetében nem javasolt a lekérdezés használata a lehetséges késés miatt. Túlzott használat esetén a lekérdezés kapacitása korlátozott lehet egy adott fiókban. Fejlesztőknek inkább az Event Grid használata javasolt. További információkért tekintse meg az [események egyéni webes végponthoz való átirányítását](job-state-events-cli-how-to.md) ismertető cikket.

A **feladat** általában halad végig a következő állapotok: **Ütemezett**, **várólistán**, **feldolgozása**, **befejezett** (a végleges állapot). Ha a feladat hibát észlelt, a **Hiba** állapot jelenik meg. Ha a feladat megszakítás alatt áll, a **Megszakítás**, a megszakítás befejeződése után pedig a **Megszakítva** állapot jelenik meg.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Tartalom-házirend létrehozása

A tartalomkulcsok biztonságos hozzáférést nyújtanak az objektumokhoz. Szeretne létrehozni egy [tartalom kulcs házirend](content-key-policy-concept.md) amikor egy DRM tartalom titkosítása. A szabályzatot úgy konfigurálja a tartalomkulcsot a rendszer hogyan továbbítja az ügyfelek közötti. A tartalomkulcs nem tartozik egy Streamelési lokátor. A Media Services azt a kulcstovábbító szolgáltatást is biztosítja, amely titkosítási kulcsokat és licenceket továbbít a jogosult felhasználóknak. 

Be kell állítani a követelményeknek (korlátozások) az a **tartalom kulcs házirend** , amely teljesülniük kell, hogy a megadott konfiguráció kulcsok. Ebben a példában a következő konfigurációkat és követelményeket állítjuk be:

* Konfiguráció 

    A [PlayReady](playready-license-template-overview.md)- és a [Widevine](widevine-license-template-overview.md)-licencek úgy vannak konfigurálva, hogy a Media Services licenctovábbítási szolgáltatása továbbítani tudja azokat. Bár ez a mintaalkalmazás nem konfigurálja a [FairPlay](fairplay-license-overview.md) licencet, tartalmazza a FairPlay konfigurálására használható metódust. Másik lehetőségként hozzáadhatja a FairPlay konfigurációját is.

* Korlátozás

    Az alkalmazás egy JWT-jogkivonat típusú korlátozást állít be a szabályzaton.

Amikor egy lejátszó streamet kér, a Media Services a megadott kulccsal titkosítja dinamikusan a tartalmat. A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. A szolgáltatás kiértékeli a kulcshoz megadott tartalomkulcs-szabályzatot annak meghatározásához, hogy a felhasználó jogosult-e a kulcs lekérésére.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>A Streamelési Lokátorok létrehozásához

A kódolás befejezése és a tartalomkulcs-szabályzat beállítása után a következő lépés a kimeneti objektumban található videó elérhetővé tétele az ügyfelek számára lejátszásra. Ezt két lépésben végezheti el: 

1. Hozzon létre egy [lokátor](streaming-locators-concept.md)
2. Hozza létre az ügyfelek által használt streamelési URL-címeket. 

Létrehozásának folyamatán a **Streamelési lokátor** közzététel nevezzük. Alapértelmezés szerint a **Streamelési lokátor** érvényes az API-hívások végrehajtása után azonnal, és tart, amíg nem törli, ha nem konfigurál a választható kezdő és befejező időpontok. 

Amikor létrehozza a **Streamelési lokátor**, meg kell adnia a kívánt `StreamingPolicyName`. Ebben az oktatóanyagban egy előre meghatározott Streamelési házirendnek, amely arra kéri a tartalom streameléshez közzététele az Azure Media Services használjuk. Ebben a példában a StreamingLocator.StreamingPolicyName elemet állítjuk be a „Predefined_MultiDrmCencStreaming” szabályzathoz. A PlayReady és Widevine titkosítások használatára vonatkoznak, a rendszer továbbítja a kulcsot a lejátszás ügyfél a beállított DRM-licencek alapján. Ha CBCS (FairPlay) licenccel is titkosítani szeretné a streamet, használja a következőt: „Predefined_MultiDrmStreaming”. 

> [!IMPORTANT]
> Egyéni használatakor [Streamelési házirend](streaming-policy-concept.md), korlátozott számú házirendeket tervezzen a Media Services-fiókhoz, és újra alkalmazza őket a StreamingLocators, amikor ugyanazt a titkosítási beállítások és protokollok van szükség. A Media Service-fiókban korlátozva van a StreamingPolicy-bejegyzések száma. Nem érdemes új streamelési szabályzatot létrehozni minden egyes StreamingLocatorhöz.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Tesztjogkivonat lekérése
        
Ebben az oktatóanyagban meghatározzuk, hogy a tartalomkulcs-szabályzat jogkivonat-korlátozással rendelkezzen. A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. A Media Services a [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) formátumú jogkivonatokat támogatja, ezért ezt konfiguráljuk a mintában.

A ContentKeyIdentifierClaim szerepel a ContentKeyPolicy szabályzatban, ami azt jelenti, hogy a kulcstovábbító szolgáltatásnak bemutatott jogkivonatban szerepelnie kell a ContentKey azonosítójának. A mintában nem megadunk egy tartalomkulcsot, a Streamelési lokátor létrehozása során, a rendszer létrehoz egy véletlenszerű számunkra. A tesztjogkivonat létrehozásához le kell kérniük a ContentKeyId azonosítót, amelyet a ContentKeyIdentifierClaim jogcímbe helyezünk.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>A streamelési URL-cím létrehozása

A [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) létrehozása után lekérheti a streamelési URL-címeket. Egy URL-cím összeállítását, fűzze össze kell a [Streamvégpontok](https://docs.microsoft.com/rest/api/media/streamingendpoints) állomás neve és a **Streamelési lokátor** elérési útja. Ebben a példában a *alapértelmezett* **folyamatos átviteli végponton** szolgál. Amikor először hozzon létre egy Media Services-fiókját, ezt *alapértelmezett* **folyamatos átviteli végponton** egy leállított állapotba kerül, meg kell hívnia **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

Az alkalmazás futtatásakor a következő jelenik meg:

![DRM védelme](./media/protect-with-drm/playready_encrypted_url.png)

Megnyithat egy böngészőt, és beillesztheti az eredményül kapott URL-t, amellyel elindíthatja az Azure Media Player bemutató oldalát az előre kitöltött URL-lel és jogkivonattal. 
 
## <a name="clean-up-resources-in-your-media-services-account"></a>A Media Service-fiók erőforrásainak eltávolítása

Általában érdemes eltávolítani mindent azon objektumok kivételével, amelyeket később is szeretne használni (átalakítások, StreamingLocator objektumok stb.). Ha ki szeretné üríteni fiókját a kísérletezés után, töröljön minden erőforrást, amelyet nem szeretne ismét használni.  A következő kóddal például törölheti a feladatokat.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot. 

Hajtsa végre a következő CLI-parancsot:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Tegyen fel kérdéseket, küldje el visszajelzését, frissítések beszerzése

Tekintse meg a [Azure Media Services-Közösség](media-services-community.md) kérdések, küldje el visszajelzését, és tudnivalók a Media Services-frissítések különböző módon olvashatja.

## <a name="next-steps"></a>További lépések

Ismerkedjen meg

> [!div class="nextstepaction"]
> [AES-128 védelme](protect-with-aes128.md)

