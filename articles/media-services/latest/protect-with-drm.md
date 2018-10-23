---
title: A DRM dinamikus titkosítási és licenctovábbítási szolgáltatás használata az Azure Media Serviceszel | Microsoft Docs
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
ms.topic: get-started-article
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 2a8a00ab034016e7121e4601b3ff5a16d8c721ac
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49395079"
---
# <a name="use-drm-dynamic-encryption-and-license-delivery-service"></a>A DRM dinamikus titkosítási és licenctovábbítási szolgáltatás használata

Az Azure Media Services segítségével MPEG-DASH, Smooth Streaming vagy HTTP Live Streaming (HLS) típusú streamjeit [PlayReady DRM- (digitális jogkezelési)](https://www.microsoft.com/playready/overview/) védelemmel láthatja el. Ezenfelül a Media Services használatával **Google Widevine** DRM-licencekkel ellátott DASH-streameket is továbbíthat. Mind a PlayReady, mind a Widevine titkosítása a Common Encryption (ISO/IEC 23001-7 CENC) szabvány specifikációi szerint történik. A Media Services segítségével az **Apple FairPlay** (AES-128 CBC) használatával is titkosíthatja a HLS-tartalmakat. 

Ezenkívül a Media Services része egy szolgáltatás, amelynek segítségével PlayReady, Widevine vagy FairPlay DRM-licenceket továbbíthat. Amikor a felhasználók DRM-védelemmel rendelkező tartalmat kérnek, a lejátszóalkalmazás licencet kér a Media Services licencelési szolgáltatástól. Ha a lejátszóalkalmazás hitelesítve van, a Media Services licencelési szolgáltatás kiadja a licencet. A licencek tartalmazzák a feloldási kulcsot, amelynek segítségével az ügyféllejátszó képes feloldani a titkosítást, majd streamelni a kért tartalmakat.

Ez a cikk a [DRM használatával való titkosítás](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) mintáján alapul. A minta többek között a következőket mutatja be:

* Kódoló átalakítás létrehozása, amely beépített előzetes beállítást használ az adaptív sávszélességű kódoláshoz, és közvetlenül egy [HTTPs forrás URL-jéből](job-input-from-http-how-to.md) fogad fájlokat.
* A jogkivonat ellenőrzéséhez használt aláírókulcs beállítása.
* Azon követelmények (korlátozások) beállítása a tartalomkulcs szabályzatához, amelyeknek meg kell felelni a megadott konfigurációjú kulcsok kézbesítése érdekében. 

    * Konfiguráció 
    
        Ebben a példában a [PlayReady](playready-license-template-overview.md)- és a [Widevine](widevine-license-template-overview.md)-licencek úgy vannak konfigurálva, hogy a Media Services licenctovábbítási szolgáltatása továbbítani tudja azokat. Bár ez a mintaalkalmazás nem konfigurálja a [FairPlay](fairplay-license-overview.md) licencet, tartalmazza a FairPlay konfigurálására használható metódust. Igény szerint másik lehetőségként hozzáadhatja a FairPlay konfigurációját is.

    * Korlátozás

        Az alkalmazás egy JWT-jogkivonat típusú korlátozást állít be a szabályzaton.

* Hozzon létre egy StreamingLocatort a megadott objektumhoz a megadott streamelési szabályzat nevével. Ebben az esetben az előre meghatározott szabályzatot használjuk. Ez két tartalomkulcsot állít be a StreamingLocatoron: AES-128 (envelope) és CENC (PlayReady és Widevine).  
    
    A StreamingLocator létrehozása után a rendszer közzéteszi a kimeneti objektumot, amelyek elérhetővé válnak az ügyfelek számára lejátszásra.

    > [!NOTE]
    > Győződjön meg arról, hogy a streameléshez használni kívánt StreamingEndpoint Fut állapotban legyen.

* Hozzon létre egy URL-címet az Azure Media Playerhez, amely tartalmazza a DASH-jegyzékfájlt és a PlayReady által titkosított tartalom lejátszásához szükséges PlayReady-jogkivonatot. A minta 1 órára állítja a jogkivonat lejáratát. 

    Megnyithat egy böngészőt, és beillesztheti az eredményül kapott URL-t, amellyel elindíthatja az Azure Media Player bemutató oldalát az előre kitöltött URL-lel és jogkivonattal.  

    ![védelem drm használatával](./media/protect-with-drm/playready_encrypted_url.png)

> [!NOTE]
> Minden objektumot több titkosítási típussal titkosíthat (AES-128, PlayReady, Widevine, FairPlay). A [streamelési protokollokkal és a titkosítási típusokkal](content-protection-overview.md#streaming-protocols-and-encryption-types) kapcsolatos szakaszban megtekintheti, hogy mit mivel érdemes kombinálni.

A cikkben leírt minta a következőt eredményezi:

![védelem drm használatával](./media/protect-with-drm/ams_player.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei.

* Tekintse meg a [Content Protection áttekintését](content-protection-overview.md) ismertető cikket.
* Tekintse meg a [hozzáférés-vezérléssel ellátott Multi-DRM rendszerek tervezését](design-multi-drm-system-with-access-control.md) ismertető cikket.
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

A kimeneti [objektum](https://docs.microsoft.com/rest/api/media/assets) tárolja a kódolási feladat eredményeit.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Kódoló átalakítás beszerzése vagy létrehozása

Egy új [átalakításpéldány](https://docs.microsoft.com/rest/api/media/transforms) létrehozásakor meg kell adnia, milyen kimenetet szeretne létrehozni. A kötelező paraméter egy **TransformOutput** objektum, ahogyan az az alábbi kódban látható. Minden **TransformOutput** objektum tartalmaz **előzetes beállításokat**. Az **előzetes beállítások** részletesen leírják azokat a video- és audiofeldolgozási műveleteket, amelyek a kívánt **TransformOutput** objektum előállításához szükségesek. Az ebben a cikkben leírt minta az **AdaptiveStreaming** nevű beépített előzetes beállítást használja. Az előzetes beállítás a bemeneti videót egy automatikusan létrehozott sávszélességi skálává (sávszélesség–felbontás párokká) kódolja a bemeneti felbontás és sávszélesség alapján, majd ISO MP4-fájlokat hoz létre H.264 kódolású video- és AAC kódolású audiosávokkal, amelyek megfelelnek a sávszélesség–felbontás pároknak. 

Új [átalakítások](https://docs.microsoft.com/rest/api/media/transforms) létrehozása előtt ellenőrizze a **Get** metódussal, hogy létezik-e már átalakítás, ahogyan az az alábbi kódban látható.  A Media Services 3-as verziója esetében a **Get** metódusok **null** értéket adnak vissza, ha az entitás nem létezik (a kis- és nagybetűket meg nem különböztető névellenőrzés történik).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Feladat elküldése

Ahogy korábban említettük, az [átalakítási](https://docs.microsoft.com/rest/api/media/transforms) objektum a recept, a [feladat](https://docs.microsoft.com/rest/api/media/jobs) pedig maga a kérés a Media Services számára, hogy alkalmazza az adott **átalakítást** egy meghatározott bemeneti video- vagy audiotartalomra. A **feladat** meghatároz bizonyos adatokat, például a bemeneti videó és a kimenet helyét.

Ebben az oktatóanyagban egy olyan fájl alapján hozzuk létre a feladat bemenetét, amely közvetlenül egy [HTTPs forrás URL-jéből](job-input-from-http-how-to.md) van betöltve.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Várakozás a feladat befejeződésére

A feladat végrehajtása némi időt vesz igénybe, és fontos, hogy értesüljön arról, ha ez megtörtént. Az alábbi kódminta bemutatja, hogyan kérdezheti le a [feladat](https://docs.microsoft.com/rest/api/media/jobs) állapotát a szolgáltatásból. Éles alkalmazások esetében nem javasolt a lekérdezés használata a lehetséges késés miatt. Túlzott használat esetén a lekérdezés kapacitása korlátozott lehet egy adott fiókban. Fejlesztőknek inkább az Event Grid használata javasolt. További információkért tekintse meg az [események egyéni webes végponthoz való átirányítását](job-state-events-cli-how-to.md) ismertető cikket.

A **feladat** a következő állapotokon halad végig: **Ütemezve**, **Várólistán**, **Feldolgozás alatt**, **Befejeződött** (a végső állapot). Ha a feladat hibát észlelt, a **Hiba** állapot jelenik meg. Ha a feladat megszakítás alatt áll, a **Megszakítás**, a megszakítás befejeződése után pedig a **Megszakítva** állapot jelenik meg.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkeypolicy"></a>ContentKeyPolicy létrehozása

A tartalomkulcsok biztonságos hozzáférést nyújtanak az objektumokhoz. Létre kell hoznia egy tartalomkulcs-szabályzatot, amely azt konfigurálja, hogy a tartalomkulcs hogyan kerüljön a végfelhasználókhoz. A tartalomkulcs a StreamingLocatorral van társítva. A Media Services azt a kulcstovábbító szolgáltatást is biztosítja, amely titkosítási kulcsokat és licenceket továbbít a jogosult felhasználóknak. 

Azon követelményeket (korlátozásokat) is be kell állítania a tartalomkulcs szabályzatához, amelyeknek meg kell felelni a megadott konfigurációjú kulcsok kézbesítése érdekében. Ebben a példában a következő konfigurációkat és követelményeket állítjuk be:

* Konfiguráció 

    A [PlayReady](playready-license-template-overview.md)- és a [Widevine](widevine-license-template-overview.md)-licencek úgy vannak konfigurálva, hogy a Media Services licenctovábbítási szolgáltatása továbbítani tudja azokat. Bár ez a mintaalkalmazás nem konfigurálja a [FairPlay](fairplay-license-overview.md) licencet, tartalmazza a FairPlay konfigurálására használható metódust. Másik lehetőségként hozzáadhatja a FairPlay konfigurációját is.

* Korlátozás

    Az alkalmazás egy JWT-jogkivonat típusú korlátozást állít be a szabályzaton.

Amikor egy lejátszó streamet kér, a Media Services a megadott kulccsal titkosítja dinamikusan a tartalmat. A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. A szolgáltatás kiértékeli a kulcshoz megadott tartalomkulcs-szabályzatot annak meghatározásához, hogy a felhasználó jogosult-e a kulcs lekérésére.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaminglocator"></a>StreamingLocator létrehozása

A kódolás befejezése és a tartalomkulcs-szabályzat beállítása után a következő lépés a kimeneti objektumban található videó elérhetővé tétele az ügyfelek számára lejátszásra. Ezt két lépésben végezheti el: 

1. [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) létrehozása
2. Hozza létre az ügyfelek által használt streamelési URL-címeket. 

A **StreamingLocator** létrehozásának folyamatát közzétételnek nevezzük. Alapértelmezés szerint a **StreamingLocator** azonnal érvényessé válik az API-hívás után, és a törléséig aktív marad. Ehelyett be lehet állítani indítási és befejeződési időpontokat is. 

A [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) létrehozása során meg kell adnia a kívánt **StreamingPolicyName** elemet. Ebben az oktatóanyagban az egyik előre definiált StreamingPolicies szabályzatot használjuk, amely közli az Azure Media Services szolgáltatással, hogy hogyan tegye közzé a tartalmat a streameléshez. Ebben a példában a StreamingLocator.StreamingPolicyName elemet állítjuk be a „Predefined_MultiDrmCencStreaming” szabályzathoz. Ez a szabályzat jelzi, hogy két tartalomkulcsot (envelope és CENC) szeretne létrehozni és beállítani a lokátoron. Így az envelope, a PlayReady és a Widevine titkosítások lesznek alkalmazva (a kulcsot a konfigurált DRM-licencek alapján továbbítja a rendszer a lejátszást végző ügyfelének). Ha CBCS (FairPlay) licenccel is titkosítani szeretné a streamet, használja a következőt: „Predefined_MultiDrmStreaming”. 

> [!IMPORTANT]
> Egyéni [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) használata esetén érdemes korlátozott számú szabályzatot létrehoznia a Media Service-fiókhoz, és újra felhasználni őket a StreamingLocator használatakor, amikor ugyanolyan titkosítási beállításokra és protokollokra van szükség. A Media Service-fiókban korlátozva van a StreamingPolicy-bejegyzések száma. Nem érdemes új streamelési szabályzatot létrehozni minden egyes StreamingLocatorhöz.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Tesztjogkivonat lekérése
        
Ebben az oktatóanyagban meghatározzuk, hogy a tartalomkulcs-szabályzat jogkivonat-korlátozással rendelkezzen. A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. A Media Services a [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) formátumú jogkivonatokat támogatja, ezért ezt konfiguráljuk a mintában.

A ContentKeyIdentifierClaim szerepel a ContentKeyPolicy szabályzatban, ami azt jelenti, hogy a kulcstovábbító szolgáltatásnak bemutatott jogkivonatban szerepelnie kell a ContentKey azonosítójának. A mintában nem adunk meg tartalomkulcsot a StreamingLocator létrehozásakor, mert a rendszer véletlenszerűen létrehoz nekünk egyet. A tesztjogkivonat létrehozásához le kell kérniük a ContentKeyId azonosítót, amelyet a ContentKeyIdentifierClaim jogcímbe helyezünk.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>DASH streamelési URL létrehozása

A [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) létrehozása után lekérheti a streamelési URL-címeket. URL-cím létrehozásához össze kell fűznie a [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) gazdanevét és a **StreamingLocator** elérési útját. Ebben a mintában az *alapértelmezett* **StreamingEndpoint** elemet használjuk. A Media Service-fiók létrehozásakor ez az *alapértelmezett* **StreamingEndpoint** leállított állapotú lesz, ezért meg kell hívnia a **Start** parancsot.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>A Media Service-fiók erőforrásainak eltávolítása

Általában érdemes eltávolítani mindent azon objektumok kivételével, amelyeket később is szeretne használni (átalakítások, StreamingLocator objektumok stb.). Ha ki szeretné üríteni fiókját a kísérletezés után, töröljön minden erőforrást, amelyet nem szeretne ismét használni.  A következő kóddal például törölheti a feladatokat.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="next-steps"></a>További lépések

Az [AES-128 használatával történő védelem](protect-with-aes128.md) ismertetése
