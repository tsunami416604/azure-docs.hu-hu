---
title: Videó titkosítása AES-128-mal
titleSuffix: Azure Media Services
description: Ismerje meg, hogyan titkosíthatja a videókat az AES 128 bites titkosításával, és hogyan használhatja a kulcskézbesítési szolgáltatást az Azure Media Servicesben.
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
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 126700e6290650221a9cb9711b22472301409fca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974172"
---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>Bemutató: Titkosítsa a videót az AES-128-kal, és használja a kulcskézbesítési szolgáltatást

> [!NOTE]
> Annak ellenére, hogy az oktatóanyag a [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) példákat használja, az általános lépések megegyeznek a [REST API,](https://docs.microsoft.com/rest/api/media/liveevents) [cli](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest), vagy más támogatott [SDK-k](media-services-apis-overview.md#sdks)esetében.

A Media Services segítségével 128 bites titkosítási kulcsok használatával http-élő streamelést (HLS), MPEG-DASH-t és simított streamelést biztosíthat az AES-szel. A Media Services biztosítja azt a kulcskézbesítési szolgáltatást is, amely titkosítási kulcsokat biztosít a jogosult felhasználóknak. Ha azt szeretné, hogy a Media Services dinamikusan titkosítsa a videót, társítsa a titkosítási kulcsot egy streamelési lokátorral, és konfigurálja a tartalomkulcs-házirendet. Amikor egy lejátszó adatfolyamot kér, a Media Services a megadott kulccsal dinamikusan titkosítja a tartalmat az AES-128-as sal. A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. A szolgáltatás kiértékeli a kulcshoz megadott tartalomkulcs-szabályzatot annak meghatározásához, hogy a felhasználó jogosult-e a kulcs lekérésére.

Minden objektumot több titkosítási típussal titkosíthat (AES-128, PlayReady, Widevine, FairPlay). A [streamelési protokollokkal és a titkosítási típusokkal](content-protection-overview.md#streaming-protocols-and-encryption-types) kapcsolatos szakaszban megtekintheti, hogy mit mivel érdemes kombinálni. Lásd még: [Hogyan védheta kreténnel.](protect-with-drm.md)

A jelen cikkből származó kimenet tartalmaz egy URL-címet az Azure Media Player, jegyzékfájl URL-cím, és a tartalom lejátszásához szükséges AES-token kimenetét. A minta a JSON webtoken (JWT) token lejárati 1 órára állítja. Megnyithat egy böngészőt, és beillesztheti az eredményül kapott URL-t az Azure Media Player ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```bemutatóoldalának elindításához, amelyen az URL-cím és a token már a következő formátumban van kitöltve: .

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Töltse le a cikkben ismertetett [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) mintát.
> * Kezdje el használni a Media Services API-jait a .NET SDK-val.
> * Hozzon létre egy kimeneti eszközt.
> * Hozzon létre egy kódolási átalakítást.
> * Feladat beküldése.
> * Várja meg, amíg a feladat befejeződik.
> * Tartalomkulcs-házirend létrehozása
> * Konfigurálja úgy a házirendet, hogy JWT-jogkivonat-korlátozást használjon.
> * Hozzon létre egy streamelési lokátort.
> * Állítsa be a streamelési lokátort a videó AES (ClearKey) titkosításához.
> * Szerezzen be egy teszttokent.
> * Hozzon létre egy streamelési URL-címet.
> * Az erőforrások megtisztítása.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei.

* Tekintse meg a [Content Protection áttekintését](content-protection-overview.md) ismertető cikket.
* Telepítse a Visual Studio Code vagy a Visual Studio alkalmazást.
* [Hozzon létre egy Media Services-fiókot](create-account-cli-quickstart.md).
* A Media Services API-k használatához szükséges hitelesítő adatokbe az [Access API-k](access-api-cli-how-to.md)követésével szerezheti be a szükséges hitelesítő adatokat.

## <a name="download-code"></a>Kód letöltése

Klónozza a gépre az ebben a cikkben ismertetett teljes .NET-mintát tartalmazó GitHub-adattárat a következő paranccsal:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

A "Titkosítás AES-128-mal" minta a [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) mappában található.

> [!NOTE]
> A minta egyedi erőforrásokat hoz létre az alkalmazás minden futtatásakor. Általában újra fel kell használnia a meglévő erőforrásokat, például az átalakításokat és a házirendeket (ha a meglévő erőforrás rendelkezik a szükséges konfigurációkkal).

## <a name="start-using-media-services-apis-with-net-sdk"></a>A Media Services API-k használatának megkezdése a .NET SDK-val

A Media Services API-k .NET használatával való használatának megkezdéséhez hozzon létre egy **AzureMediaServicesClient-objektumot.** Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. A cikk elején klónozott kódban a **GetCredentialsAsync** függvény létrehozza a ServiceClientCredentials objektumot a helyi konfigurációs fájlban szereplő hitelesítési adatok alapján.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Kimeneti objektum létrehozása  

A kimeneti [objektum](https://docs.microsoft.com/rest/api/media/assets) tárolja a kódolási feladat eredményeit.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Kódoló átalakítás beszerzése vagy létrehozása

Egy új [átalakításpéldány](https://docs.microsoft.com/rest/api/media/transforms) létrehozásakor meg kell adnia, milyen kimenetet szeretne létrehozni. A kötelező paraméter egy **TransformOutput** objektum, ahogyan az az alábbi kódban látható. Minden **TransformOutput** objektum tartalmaz **előzetes beállításokat**. Az **előzetes beállítások** részletesen leírják azokat a video- és audiofeldolgozási műveleteket, amelyek a kívánt **TransformOutput** objektum előállításához szükségesek. Az ebben a cikkben leírt minta az **AdaptiveStreaming** nevű beépített előzetes beállítást használja. A készlet a bemeneti videót egy automatikusan generált bitráta létrába (bitráta-felbontású párokba) kódolja a bemeneti felbontás és a bitráta alapján, majd iSO MP4 fájlokat állít elő H.264 videóval és AAC hanggal, amely minden bitráta-felbontású párnak felel meg.

Az új [átalakítás](https://docs.microsoft.com/rest/api/media/transforms)létrehozása előtt először ellenőrizze, hogy létezik-e már ilyen a **Get** metódus használatával, ahogy az a következő kódban látható. A Media Services 3-as verziója esetében a **Get** metódusok **null** értéket adnak vissza, ha az entitás nem létezik (a kis- és nagybetűket meg nem különböztető névellenőrzés történik).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Feladat elküldése

Ahogy korábban említettük, az [átalakítási](https://docs.microsoft.com/rest/api/media/transforms) objektum a recept, a [feladat](https://docs.microsoft.com/rest/api/media/jobs) pedig maga a kérés a Media Services számára, hogy alkalmazza az adott **átalakítást** egy meghatározott bemeneti video- vagy audiotartalomra. A **feladat** olyan információkat ad meg, mint például a bemeneti videó helye és a kimenet helye.

Ebben az oktatóanyagban egy olyan fájl alapján hozzuk létre a feladat bemenetét, amely közvetlenül egy [HTTPs forrás URL-címből](job-input-from-http-how-to.md)kerül betöltésre.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Várakozás a feladat befejeződésére

A munka elvégzéséhez idő kell. Ha ez megtörténik, értesítést szeretne kapni. Az alábbi kódminta bemutatja, hogyan kérdezheti le a [feladat](https://docs.microsoft.com/rest/api/media/jobs) állapotát a szolgáltatásból. A lekérdezés nem ajánlott ajánlott eljárás éles alkalmazások potenciális késés miatt. Túlzott használat esetén a lekérdezés kapacitása korlátozott lehet egy adott fiókban. Fejlesztőknek inkább az Event Grid használata javasolt. További információ: [Események irányítása egyéni webes végpontra](job-state-events-cli-how-to.md)című témakörben talál.

A **feladat** a következő állapotokon halad végig: **Ütemezve**, **Várólistán**, **Feldolgozás alatt**, **Befejeződött** (a végső állapot). Ha a feladat hibát észlelt, a **Hiba** állapotot kapja. Ha a feladat megszakítása folyamatban van, akkor a **Megszakítás** és a **Canceled** is megkapja, amikor elkészült.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Tartalomkulcs-házirend létrehozása

A tartalomkulcsok biztonságos hozzáférést nyújtanak az objektumokhoz. Létre kell hoznia egy **tartalomkulcs-házirendet,** amely konfigurálja a tartalomkulcs kézbesítésének módját a végfelhasználóknak. A tartalomkulcs a **streamelési lokátorhoz van társítva.** A Media Services biztosítja azt a kulcskézbesítési szolgáltatást is, amely titkosítási kulcsokat biztosít a jogosult felhasználóknak.

Amikor egy lejátszó adatfolyamot kér, a Media Services a megadott kulccsal dinamikusan titkosítja a tartalmat (ebben az esetben AES titkosítással.) Az adatfolyam visszafejtéséhez a lejátszó kéri a kulcsot a kulcskézbesítési szolgáltatástól. A szolgáltatás kiértékeli a kulcshoz megadott tartalomkulcs-szabályzatot annak meghatározásához, hogy a felhasználó jogosult-e a kulcs lekérésére.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Streamelési lokátor létrehozása

A kódolás befejezése és a tartalomkulcs-szabályzat beállítása után a következő lépés a kimeneti objektumban található videó elérhetővé tétele az ügyfelek számára lejátszásra. A videót két lépésben teheti elérhetővé:

1. Hozzon létre egy [streamelési lokátort](https://docs.microsoft.com/rest/api/media/streaminglocators).
2. Hozza létre az ügyfelek által használt streamelési URL-címeket.

A **streamelési lokátor** létrehozásának folyamatát közzétételnek nevezzük. Alapértelmezés szerint a **streamelési lokátor** azonnal érvényes az API-hívások kezdeményezése után. Ez tart, amíg törli, kivéve, ha konfigurálja a választható kezdési és befejezési időpontok.

[A streamelési lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators)létrehozásakor meg kell adnia a kívánt **StreamingPolicyName nevet.** Ebben az oktatóanyagban az egyik PredefinedStreamingPolicies, amely megmondja az Azure Media Services, hogyan teheti közzé a tartalmat streamelésre. Ebben a példában az AES boríték titkosítása lesz alkalmazva (ezt a titkosítást ClearKey titkosításnak is nevezik, mivel a kulcs HTTPS-en keresztül, nem pedig DRM-licencen keresztül kerül a lejátszási ügyfélhez).

> [!IMPORTANT]
> Egyéni [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies)használata esetén meg kell terveznie egy korlátozott ilyen házirendeket a Media Service-fiókhoz, és újra fel kell használnia őket a streamelési lokátorokhoz, amikor ugyanazokra a titkosítási beállításokra és protokollokra van szükség. A Media Service-fiókban korlátozva van a StreamingPolicy-bejegyzések száma. Nem kellene új streamelési házirendet létrehoznia minden egyes streamelési lokátorhoz.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Tesztjogkivonat lekérése

Ebben az oktatóanyagban meghatározzuk, hogy a tartalomkulcs-szabályzat jogkivonat-korlátozással rendelkezzen. A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. A Media Services támogatja a [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) formátumú jogkivonatokat, és ezt konfiguráljuk a mintában.

A ContentKeyIdentifierClaim a **Tartalomkulcs-házirendben**használatos, ami azt jelenti, hogy a kulcskézbesítési szolgáltatásnak bemutatott jogkivonatnak rendelkeznie kell a tartalomkulcs azonosítójával. A mintában nem adtunk meg tartalomkulcsot a streamelési lokátor létrehozásakor, a rendszer létrehozott egy véletlenszerűt. A teszttoken létrehozásához be kell szereznünk a ContentKeyId-et a ContentKeyIdentifierClaim jogcímbe.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>DASH streamelési URL létrehozása

Most, hogy a [streamelési lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators) létrejött, lejuthat a streamelési URL-címek. URL-cím létrehozásához össze kell fűznie a [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) állomásnevét és a **streamelési lokátor** elérési útját. Ebben a példában az *alapértelmezett* **streamelési végpontot** használja a rendszer. Amikor először hoz létre egy Media Service-fiókot, ez az *alapértelmezett* **streamelési végpont** leállított állapotban lesz, ezért meg kell hívnia a Start **programot.**

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>A Media Service-fiók erőforrásainak eltávolítása

Általában mindent meg kell tisztítania, kivéve azokat az objektumokat, amelyeket újra fel kíván használni (általában újra fel kell használnia az átalakításokat, a streamelési lokátorokat és így tovább). Ha azt szeretné, hogy a fiók a kísérletezés után tiszta legyen, törölje azokat az erőforrásokat, amelyeket nem kíván újra felhasználni. A következő kód például törli a Feladatok:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot.

Hajtsa végre a következő CLI parancsot:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdéseket tehet fel, visszajelzést adhat, frissítéseket kaphat

Tekintse meg az [Azure Media Services közösségi](media-services-community.md) cikket, ahol különböző módokon tehet fel kérdéseket, küldhet visszajelzést, és kaphat frissítéseket a Media Services szolgáltatásról.

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Védelem drm-mel](protect-with-drm.md)
