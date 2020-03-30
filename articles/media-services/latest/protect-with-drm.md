---
title: Az Azure Media Services DRM titkosítási és licenckézbesítési szolgáltatása
titleSuffix: Azure Media Services
description: Ismerje meg, hogyan használhatja a DRM dinamikus titkosítást és licenckézbesítési szolgáltatást a Microsoft PlayReady, a Google Widevine vagy az Apple FairPlay licencekkel titkosított adatfolyamok továbbítására.
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
ms.openlocfilehash: 14ba5f270138db22a76fd697b264046e22577427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086734"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Oktatóanyag: A DRM dinamikus titkosítási és licenckézbesítési szolgáltatásának használata

> [!NOTE]
> Annak ellenére, hogy ez az oktatóanyag a [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) példákat használja, az általános lépések megegyeznek a [REST API,](https://docs.microsoft.com/rest/api/media/liveevents) [cli](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest), vagy más támogatott [SDK-k](media-services-apis-overview.md#sdks)esetében.

Az Azure Media Services segítségével a streamjeit Microsoft PlayReady-, Google Widevine- vagy Apple FairPlay-licencekkel titkosíthatja. Részletes magyarázat: [Tartalomvédelem dinamikus titkosítással.](content-protection-overview.md)

A Media Services a PlayReady, a Widevine és a FairPlay DRM licencek kézbesítésével is szolgál. Amikor egy felhasználó DRM-védelemmel ellátott tartalmat kér, a lejátszóalkalmazás licencet kér a Media Services licencszolgáltatásától. Ha a lejátszó alkalmazás engedélyezett, a Media Services licencszolgáltatása licencet ad ki a lejátszónak. A licencek tartalmazzák a feloldási kulcsot, amelynek segítségével az ügyféllejátszó képes feloldani a titkosítást, majd streamelni a kért tartalmakat.

Ez a cikk a [DRM használatával való titkosítás](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) mintáján alapul.

A cikkben leírt minta a következőt eredményezi:

![AMS DRM-védelemmel ellátott videóval az Azure Media Playerben](./media/protect-with-drm/ams_player.png)

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Hozzon létre egy kódolási átalakítást.
> * A jogkivonat ellenőrzéséhez használt aláírókulcs beállítása.
> * Állítsa be a tartalomkulcs-házirend követelményeit.
> * Hozzon létre egy StreamingLocator a megadott streamelési házirenddel.
> * Hozzon létre egy URL-címet a fájl lejátszásához.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének feltételei a következők:

* Tekintse meg a [Content Protection áttekintését](content-protection-overview.md) ismertető cikket.
* Tekintse át a [Több DRM-tartalomvédelmi rendszer tervezését hozzáférés-vezérléssel.](design-multi-drm-system-with-access-control.md)
* Telepítse a Visual Studio Code vagy a Visual Studio alkalmazást.
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
> A minta egyedi erőforrásokat hoz létre az alkalmazás minden futtatásakor. Általában újra fel kell használnia a meglévő erőforrásokat, például az átalakításokat és a házirendeket (ha a meglévő erőforrás rendelkezik a szükséges konfigurációkkal).

## <a name="start-using-media-services-apis-with-net-sdk"></a>A Media Services API-k használatának megkezdése a .NET SDK-val

A Media Services API-k .NET használatával való használatának megkezdéséhez hozzon létre egy **AzureMediaServicesClient-objektumot.** Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. A cikk elején klónozott kódban a **GetCredentialsAsync** függvény létrehozza a ServiceClientCredentials objektumot a helyi konfigurációs fájlban szereplő hitelesítési adatok alapján.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Kimeneti objektum létrehozása  

A kimeneti [objektum](assets-concept.md) tárolja a kódolási feladat eredményeit.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Kódoló átalakítás beszerzése vagy létrehozása

Egy új [átalakításpéldány](transforms-jobs-concept.md) létrehozásakor meg kell adnia, milyen kimenetet szeretne létrehozni. A szükséges paraméter `transformOutput` egy objektum, amint az az alábbi kódban látható. Minden TransformOutput objektum tartalmaz **előzetes beállításokat**. Az előzetes beállítások részletesen leírják azokat a video- és audiofeldolgozási műveleteket, amelyek a kívánt TransformOutput objektum előállításához szükségesek. Az ebben a cikkben leírt minta az **AdaptiveStreaming** nevű beépített előzetes beállítást használja. A készlet a bemeneti videót egy automatikusan generált bitráta létrába (bitráta-felbontású párokba) kódolja a bemeneti felbontás és a bitráta alapján, és ISO MP4 fájlokat állít elő H.264 videóval és AAC hanggal, amely minden bitráta-felbontású párnak felel meg. 

Új **átalakítások** létrehozása előtt ellenőrizze a **Get** metódussal, hogy létezik-e már átalakítás, ahogyan az az alábbi kódban látható.  A Media Services 3-as verziója esetében a **Get** metódusok **null** értéket adnak vissza, ha az entitás nem létezik (a kis- és nagybetűket meg nem különböztető névellenőrzés történik).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Feladat elküldése

Ahogy korábban említettük, az **átalakítási** objektum a recept, a [feladat](transforms-jobs-concept.md) pedig maga a kérés a Media Services számára, hogy alkalmazza az adott **átalakítást** egy meghatározott bemeneti video- vagy audiotartalomra. A **feladat** olyan információkat ad meg, mint például a bemeneti videó helye és a kimenet helye.

Ebben az oktatóanyagban egy olyan fájl alapján hozzuk létre a feladat bemenetét, amely közvetlenül egy [HTTPs forrás URL-címből](job-input-from-http-how-to.md)kerül betöltésre.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Várakozás a feladat befejeződésére

A munka elvégzéséhez idő kell. Ha ez megtörténik, értesítést szeretne kapni. Az alábbi kódminta bemutatja, hogyan kérdezheti le a **feladat** állapotát a szolgáltatásból. A lekérdezés nem ajánlott ajánlott eljárás éles alkalmazások potenciális késés miatt. Túlzott használat esetén a lekérdezés kapacitása korlátozott lehet egy adott fiókban. Fejlesztőknek inkább az Event Grid használata javasolt. További információkért tekintse meg az [események egyéni webes végponthoz való átirányítását](job-state-events-cli-how-to.md) ismertető cikket.

A **feladat** a következő állapotokon halad végig: **Ütemezve**, **Várólistán**, **Feldolgozás alatt**, **Befejeződött** (a végső állapot). Ha a feladat hibát észlelt, a **Hiba** állapotot kapja. Ha a feladat megszakítása folyamatban van, akkor a **Megszakítás** és a **Canceled** is megkapja, amikor elkészült.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Tartalomkulcs-házirend létrehozása

A tartalomkulcsok biztonságos hozzáférést nyújtanak az objektumokhoz. [Tartalomkulcs-házirendet kell létrehoznia,](content-key-policy-concept.md) amikor a tartalmat DRM-mel titkosítja. A házirend konfigurálja, hogy a tartalomkulcs hogyan kerüla végfelhasználókhoz. A tartalomkulcs egy streamelési lokátorhoz van társítva. A Media Services azt a kulcstovábbító szolgáltatást is biztosítja, amely titkosítási kulcsokat és licenceket továbbít a jogosult felhasználóknak.

Be kell állítania a **tartalomkulcs-házirend** követelményeit (korlátozásait), amelyeknek meg kell felelniük a megadott konfigurációval rendelkező kulcsok kézbesítéséhez. Ebben a példában a következő konfigurációkat és követelményeket állítjuk be:

* Konfiguráció

    A [PlayReady](playready-license-template-overview.md)- és a [Widevine](widevine-license-template-overview.md)-licencek úgy vannak konfigurálva, hogy a Media Services licenctovábbítási szolgáltatása továbbítani tudja azokat. Annak ellenére, hogy ez a mintaalkalmazás nem konfigurálja a [FairPlay](fairplay-license-overview.md) licencet, tartalmaz egy módszert, amelyet a FairPlay konfigurálásához használhat. A FairPlay konfigurációt is hozzáadhatja egy másik lehetőségként.

* Korlátozás

    Az alkalmazás beállítja a JSON webtoken (JWT) token típusú korlátozása a szabályzat.

Amikor egy lejátszó streamet kér, a Media Services a megadott kulccsal titkosítja dinamikusan a tartalmat. A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. A szolgáltatás kiértékeli a kulcshoz megadott tartalomkulcs-szabályzatot annak meghatározásához, hogy a felhasználó jogosult-e a kulcs lekérésére.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Streamelési lokátor létrehozása

A kódolás befejezése és a tartalomkulcs-szabályzat beállítása után a következő lépés a kimeneti objektumban található videó elérhetővé tétele az ügyfelek számára lejátszásra. A videót két lépésben teheti elérhetővé:

1. Hozzon létre egy [streamelési lokátort](streaming-locators-concept.md).
2. Hozza létre az ügyfelek által használt streamelési URL-címeket.

A **streamelési lokátor** létrehozásának folyamatát közzétételnek nevezzük. Alapértelmezés szerint a **streamelési lokátor** azonnal érvényes az API-hívások kezdeményezése után. Ez tart, amíg törli, kivéve, ha konfigurálja a választható kezdési és befejezési időpontok.

**A streamelési lokátor**létrehozásakor `StreamingPolicyName`meg kell adnia a kívánt értéket. Ebben az oktatóanyagban az előre definiált streamelési szabályzatok egyikét használjuk, amely megmondja az Azure Media Servicesnek, hogyan teheti közzé a tartalmat streamelésre. Ebben a példában a StreamingLocator.StreamingPolicyName elemet állítjuk be a „Predefined_MultiDrmCencStreaming” szabályzathoz. A PlayReady és widevine titkosítások alkalmazása megtörtént, és a kulcs a konfigurált DRM-licencek alapján kerül a lejátszási ügyfélbe. Ha CBCS (FairPlay) licenccel is titkosítani szeretné a streamet, használja a következőt: „Predefined_MultiDrmStreaming”.

> [!IMPORTANT]
> Egyéni [streamelési szabályzat](streaming-policy-concept.md)használataesetén a Media Service-fiókhoz korlátozott számú ilyen házirendet kell megterveznie, és újra fel kell használnia őket a StreamingLocatorok számára, ha ugyanazokra a titkosítási beállításokra és protokollokra van szükség. A Media Service-fiókban korlátozva van a StreamingPolicy-bejegyzések száma. Nem kell létrehoznia egy új StreamingPolicy minden StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Tesztjogkivonat lekérése

Ebben az oktatóanyagban meghatározzuk, hogy a tartalomkulcs-szabályzat jogkivonat-korlátozással rendelkezzen. A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. A Media Services támogatja a [JWT-formátumok](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) tokenjeit, és ezt konfiguráljuk a mintában.

A ContentKeyIdentifierClaim szerepel a ContentKeyPolicy szabályzatban, ami azt jelenti, hogy a kulcstovábbító szolgáltatásnak bemutatott jogkivonatban szerepelnie kell a ContentKey azonosítójának. A mintában nem ad meg tartalomkulcsot a streamelési lokátor létrehozásakor, a rendszer véletlenszerűt hoz létre számunkra. A teszttoken létrehozásához be kell szereznünk a ContentKeyId-et a ContentKeyIdentifierClaim jogcímbe.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Streamelési URL-cím létrehozása

A [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) létrehozása után lekérheti a streamelési URL-címeket. URL-cím létrehozásához össze kell fűznie a [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) állomásnevét és a **streamelési lokátor** elérési útját. Ebben a példában az *alapértelmezett* **streamelési végpontot** használja a rendszer. Amikor először hoz létre egy Media Service-fiókot, ez az *alapértelmezett* **streamelési végpont** leállított állapotban lesz, ezért meg kell hívnia a Start **programot.**

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

Az alkalmazás futtatásakor a következő képernyő jelenik meg:

![Védelem drm-mel](./media/protect-with-drm/playready_encrypted_url.png)

Megnyithat egy böngészőt, és beillesztheti az eredményül kapott URL-t, amellyel elindíthatja az Azure Media Player bemutató oldalát az előre kitöltött URL-lel és jogkivonattal.

## <a name="clean-up-resources-in-your-media-services-account"></a>A Media Service-fiók erőforrásainak eltávolítása

Általában mindent meg kell tisztítania, kivéve azokat az objektumokat, amelyeket újra fel kíván használni (általában újra fel kell használnia az Átalakításokat, a StreamingLocators-t és így tovább). Ha azt szeretné, hogy a fiók a kísérletezés után tiszta legyen, törölje azokat az erőforrásokat, amelyeket nem kíván újra felhasználni. A következő kód például törli a Feladatok:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot.

Hajtsa végre a következő CLI parancsot:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdéseket tehet fel, visszajelzést adhat, frissítéseket kaphat

Tekintse meg az [Azure Media Services közösségi](media-services-community.md) cikket, ahol különböző módokon tehet fel kérdéseket, küldhet visszajelzést, és kaphat frissítéseket a Media Services szolgáltatásról.

## <a name="next-steps"></a>További lépések

Kivétel

> [!div class="nextstepaction"]
> [Védje az AES-128-as](protect-with-aes128.md)
