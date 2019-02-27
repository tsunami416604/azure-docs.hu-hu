---
title: Az Azure Media Services használatával titkosítja a videót, az AES-128 |} A Microsoft Docs
description: Tartalomkézbesítés 128 bites AES titkosítási kulcsokkal titkosítja a Microsoft Azure Media Services használatával. A Media Services emellett a legfontosabb licenctovábbítási szolgáltatása, amely a titkosítási kulcsokat biztosít a jogosult felhasználókra. Ez a témakör bemutatja, hogyan dinamikusan titkosítani az AES-128, és a kulcstovábbítást használata.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: juliako
ms.openlocfilehash: 2216deb7a59dda2a7c3b99c55956ef8541925425
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56877282"
---
# <a name="tutorial-use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Oktatóanyag: AES-128, a dinamikus titkosítás és a kulcstovábbítást használata

A Media Services segítségével HTTP Live Streaming (HLS), MPEG-DASH és Smooth Streaming az AES-128 bites titkosítási kulcsok használatával titkosítja. A Media Services emellett a legfontosabb licenctovábbítási szolgáltatása, amely a titkosítási kulcsokat biztosít a jogosult felhasználókra. Ha azt szeretné, a Media Services dinamikus titkosítást a videót, a titkosítási kulcs társítása Streamelési lokátor, és is konfigurálhatja a tartalom fő házirendet. Ha egy stream-lejátszó kér, a Media Services a megadott kulcs dinamikus a tartalmait az AES-128 titkosítást használ. A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. A szolgáltatás kiértékeli a kulcshoz megadott tartalomkulcs-szabályzatot annak meghatározásához, hogy a felhasználó jogosult-e a kulcs lekérésére.

Minden objektumot több titkosítási típussal titkosíthat (AES-128, PlayReady, Widevine, FairPlay). A [streamelési protokollokkal és a titkosítási típusokkal](content-protection-overview.md#streaming-protocols-and-encryption-types) kapcsolatos szakaszban megtekintheti, hogy mit mivel érdemes kombinálni. Lásd még [DRM-védelme](protect-with-drm.md).

A minta kimenete Ez a cikk tartalmazza az Azure Media Player URL-címe, jegyzékfájl URL-CÍMÉT és az AES jogkivonat szükséges a tartalom lejátszását. A minta a JWT jogkivonat lejáratának 1 órás értékre állítja. Nyisson meg egy böngészőt, és illessze be a megjelenő URL-cím, indítsa el az Azure Media Player demó oldal URL-cím és jogkivonat automatikusan kitölti már a következő formátumban: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.

Ez az oktatóanyag a következőket mutatja be:    

> [!div class="checklist"]
> * Töltse le a [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) a cikkben leírt mintát
> * A Media Services API-k használatának megkezdése a .NET SDK-val
> * Kimeneti objektum létrehozása
> * Hozzon létre egy kódolási átalakító
> * Feladat elküldése
> * Várakozás a feladat befejeződésére
> * Tartalom-házirend létrehozása
> * A JWT jogkivonat korlátozás használni kívánt házirendet konfigurálása 
> * A Streamelési Lokátorok létrehozásához
> * A Streamelési lokátor titkosíthatja a videó AES (ClearKey) konfigurálása
> * Tesztjogkivonat lekérése
> * A streamelési URL-cím létrehozása
> * Az erőforrások eltávolítása

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei.

* Tekintse át a [Content protection áttekintése](content-protection-overview.md) cikk
* A Visual Studio Code vagy a Visual Studio telepítése
* [A Media Services-fiók létrehozása](create-account-cli-quickstart.md)
* Szerezze be a Media Services API-k használatához szükséges hitelesítő adatokat az [adatelérési API-kat](access-api-cli-how-to.md) bemutató szakasz leírását követve.

## <a name="download-code"></a>Kód letöltése

Klónozza a gépre az ebben a cikkben ismertetett teljes .NET-mintát tartalmazó GitHub-adattárat a következő paranccsal:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
A "titkosítani az AES-128" mintában található a [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) mappát.

> [!NOTE]
> A minta egyedi erőforrásokat hoz létre az alkalmazás minden futtatásakor. Általában újból felhasználja a meglévő erőforrásokat, például az átalakításokat és a szabályzatokat (ha a meglévő erőforrás rendelkezik a szükséges konfigurációkkal). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>A Media Services API-k használatának megkezdése a .NET SDK-val

Ha szeretné megkezdeni a Media Services API-k használatát a .NET-tel, létre kell hoznia egy **AzureMediaServicesClient** objektumot. Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. A cikk elején klónozott kódban a **GetCredentialsAsync** függvény létrehozza a ServiceClientCredentials objektumot a helyi konfigurációs fájlban szereplő hitelesítési adatok alapján. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Kimeneti objektum létrehozása  

A kimeneti [objektum](https://docs.microsoft.com/rest/api/media/assets) tárolja a kódolási feladat eredményeit.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Kódoló átalakítás beszerzése vagy létrehozása

Egy új [átalakításpéldány](https://docs.microsoft.com/rest/api/media/transforms) létrehozásakor meg kell adnia, milyen kimenetet szeretne létrehozni. A kötelező paraméter egy **TransformOutput** objektum, ahogyan az az alábbi kódban látható. Minden **TransformOutput** objektum tartalmaz **előzetes beállításokat**. Az **előzetes beállítások** részletesen leírják azokat a video- és audiofeldolgozási műveleteket, amelyek a kívánt **TransformOutput** objektum előállításához szükségesek. Az ebben a cikkben leírt minta az **AdaptiveStreaming** nevű beépített előzetes beállítást használja. Az előzetes beállítás a bemeneti videót egy automatikusan létrehozott sávszélességi skálává (sávszélesség–felbontás párokká) kódolja a bemeneti felbontás és sávszélesség alapján, majd ISO MP4-fájlokat hoz létre H.264 kódolású video- és AAC kódolású audiosávokkal, amelyek megfelelnek a sávszélesség–felbontás pároknak. 

Új [átalakítások](https://docs.microsoft.com/rest/api/media/transforms) létrehozása előtt ellenőrizze a **Get** metódussal, hogy létezik-e már átalakítás, ahogyan az az alábbi kódban látható.  A Media Services 3-as verziója esetében a **Get** metódusok **null** értéket adnak vissza, ha az entitás nem létezik (a kis- és nagybetűket meg nem különböztető névellenőrzés történik).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Feladat elküldése

Ahogy korábban említettük, az [átalakítási](https://docs.microsoft.com/rest/api/media/transforms) objektum a recept, a [feladat](https://docs.microsoft.com/rest/api/media/jobs) pedig maga a kérés a Media Services számára, hogy alkalmazza az adott **átalakítást** egy meghatározott bemeneti video- vagy audiotartalomra. A **feladat** meghatároz bizonyos adatokat, például a bemeneti videó és a kimenet helyét.

Ebben az oktatóanyagban egy olyan fájl alapján hozzuk létre a feladat bemenetét, amely közvetlenül egy [HTTPs forrás URL-jéből](job-input-from-http-how-to.md) van betöltve.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Várakozás a feladat befejeződésére

A feladat végrehajtása némi időt vesz igénybe, és fontos, hogy értesüljön arról, ha ez megtörtént. Az alábbi kódminta bemutatja, hogyan kérdezheti le a [feladat](https://docs.microsoft.com/rest/api/media/jobs) állapotát a szolgáltatásból. Éles alkalmazások esetében nem javasolt a lekérdezés használata a lehetséges késés miatt. Túlzott használat esetén a lekérdezés kapacitása korlátozott lehet egy adott fiókban. Fejlesztőknek inkább az Event Grid használata javasolt. További információkért tekintse meg az [események egyéni webes végponthoz való átirányítását](job-state-events-cli-how-to.md) ismertető cikket.

A **feladat** általában halad végig a következő állapotok: **Ütemezett**, **várólistán**, **feldolgozása**, **befejezett** (a végleges állapot). Ha a feladat hibát észlelt, a **Hiba** állapot jelenik meg. Ha a feladat megszakítás alatt áll, a **Megszakítás**, a megszakítás befejeződése után pedig a **Megszakítva** állapot jelenik meg.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Tartalom-házirend létrehozása

A tartalomkulcsok biztonságos hozzáférést nyújtanak az objektumokhoz. Szeretne létrehozni egy **tartalom kulcs házirend** , amely beállítja a tartalomkulcsot a rendszer hogyan továbbítja az ügyfelek közötti. A tartalomkulcs társítva van **Streamelési lokátor**. A Media Services emellett a legfontosabb licenctovábbítási szolgáltatása, amely a titkosítási kulcsokat biztosít a jogosult felhasználókra. 

Amikor egy adatfolyam-lejátszó kér, Media Services segítségével a megadott kulcs dinamikusan titkosítja a tartalom (ebben az esetben AES titkosítás segítségével.) A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. A szolgáltatás kiértékeli a kulcshoz megadott tartalomkulcs-szabályzatot annak meghatározásához, hogy a felhasználó jogosult-e a kulcs lekérésére.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>A Streamelési Lokátorok létrehozásához

A kódolás befejezése és a tartalomkulcs-szabályzat beállítása után a következő lépés a kimeneti objektumban található videó elérhetővé tétele az ügyfelek számára lejátszásra. Ezt két lépésben végezheti el: 

1. Hozzon létre egy [lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators)
2. Hozza létre az ügyfelek által használt streamelési URL-címeket. 

Létrehozásának folyamatán a **Streamelési lokátor** közzététel nevezzük. Alapértelmezés szerint a **Streamelési lokátor** érvényes az API-hívások végrehajtása után azonnal, és tart, amíg nem törli, ha nem konfigurál a választható kezdő és befejező időpontok. 

Amikor létrehozza a [Streamelési lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators), meg kell adnia a kívánt **StreamingPolicyName**. Ebben az oktatóanyagban az egyik PredefinedStreamingPolicies szabályzatot használjuk, amely közli az Azure Media Services szolgáltatással, hogy hogyan tegye közzé a tartalmat a streameléshez. Ebben a példában az AES-boríték a rendszer titkosítást alkalmaz (más néven ClearKey titkosítás, mert a rendszer továbbítja a kulcsot a lejátszás ügyfélnek HTTPS és a nem DRM-licenc használatával).

> [!IMPORTANT]
> Egyéni használatakor [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies), korlátozott számú házirendeket tervezzen a Media Services-fiók, és újra alkalmazza őket a Streamelési Lokátorok, amikor az ugyanazon titkosítási lehetőségeket és a protokollok van szükség. A Media Service-fiókban korlátozva van a StreamingPolicy-bejegyzések száma. Meg kell nem hoz létre egy új StreamingPolicy az egyes Streamelési lokátor.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Tesztjogkivonat lekérése
        
Ebben az oktatóanyagban meghatározzuk, hogy a tartalomkulcs-szabályzat jogkivonat-korlátozással rendelkezzen. A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. A Media Services a [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) formátumú jogkivonatokat támogatja, ezért ezt konfiguráljuk a mintában.

A ContentKeyIdentifierClaim használatban van a **tartalom kulcs házirend**, ami azt jelenti, hogy a jogkivonat jelenik meg a kulcs-továbbítási szolgáltatást kell rendelkeznie a tartalomkulcs azonosítóját. A minta azt adta meg a tartalom kulcs, a Streamelési lokátor létrehozásakor a rendszer létrehozott egy véletlenszerű számunkra. A tesztjogkivonat létrehozásához le kell kérniük a ContentKeyId azonosítót, amelyet a ContentKeyIdentifierClaim jogcímbe helyezünk.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>DASH streamelési URL létrehozása

Most, hogy a [Streamelési lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators) lett létrehozva, beszerezheti a streamelési URL-címek. Egy URL-cím összeállítását, fűzze össze kell a [Streamvégpontok](https://docs.microsoft.com/rest/api/media/streamingendpoints) állomás neve és a **Streamelési lokátor** elérési útja. Ebben a példában a *alapértelmezett* **folyamatos átviteli végponton** szolgál. Amikor először hozzon létre egy Media Services-fiókját, ezt *alapértelmezett* **folyamatos átviteli végponton** egy leállított állapotba kerül, meg kell hívnia **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>A Media Service-fiók erőforrásainak eltávolítása

Általában érdemes megtisztítani tervez újra felhasználhatja objektumok kivételével mindent (általában felhasználja a átalakítások és akkor áll fenn a Streamelési Lokátorok stb.). Ha ki szeretné üríteni fiókját a kísérletezés után, töröljön minden erőforrást, amelyet nem szeretne ismét használni.  A következő kóddal például törölheti a feladatokat.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot. 

Hajtsa végre a következő CLI-parancsot:

```azurecli
az group delete --name amsResourceGroup
```
## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [DRM védelme](protect-with-drm.md)
