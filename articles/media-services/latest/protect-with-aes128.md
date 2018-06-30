---
title: AES Azure Media Services dinamikus titkosítás használatához |} Microsoft Docs
description: A 128 bites AES titkosítási kulcsokat a Microsoft Azure Media Services használatával titkosított tartalmat továbbít. Media Services is biztosít a kulcs kézbesítési szolgáltatás letölti a titkosítási kulcsok engedéllyel rendelkező felhasználók számára. Ez a témakör bemutatja, hogyan dinamikusan titkosítani az AES-128, és a kulcs kézbesítési szolgáltatás használata.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: juliako
ms.openlocfilehash: 0da5bbee6d0d6401a35c301a8b35dc0efa77da7d
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132934"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>AES-128, a dinamikus titkosítás és a kulcs kézbesítési szolgáltatás

A Media Services segítségével biztosítanak a HTTP-Live Streaming (HLS), MPEG-DASH vagy Smooth Streaming az AES 128 bites titkosítási kulcsok használatával titkosítja. Media Services is biztosít a kulcs kézbesítési szolgáltatás letölti a titkosítási kulcsok engedéllyel rendelkező felhasználók számára. Ha azt szeretné, a Media Services az objektum titkosítására, rendelje hozzá a titkosítási kulcs StreamingLocator, és is konfigurálhatja a tartalom kulcs házirend. Ha olyan adatfolyamot kell megadni a Windows Media Player van szükség, a Media Services megadott kulcsot használja az dinamikusan titkosítani az AES titkosítással. Az adatfolyam visszafejtése, a Windows Media player kér a kulcsot a fő kézbesítési szolgáltatás. Annak megállapításához, hogy a felhasználó jogosult-e a kulcs eléréséhez, a szolgáltatás értékeli az engedélyezési házirendeket, amelyek a kulcshoz megadott.

A cikk alapján a [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) minta. A minta bemutatja, hogyan hozzon létre egy kódolási átalakító, amely az adott néven beállítás beépített használ kódolása adaptív sávszélességű, és közvetlenül a fájl ingests egy [HTTPs forrás URL-címe](job-input-from-http-how-to.md). A kimeneti adategységen majd közzé van téve, az AES (ClearKey) titkosítással. A mintából eredménye az Azure Media Player, beleértve a kötőjel jegyzékfájl és az AES jogkivonat a tartalmat szükséges URL-CÍMÉT. A minta a JWT jogkivonat lejárata 1 órás értékre állítja be. Nyisson meg egy böngészőt, és az Azure Media Player bemutató oldal URL-cím és token tölti ki az Ön már elindíthatja az eredményül kapott URL-címet (a következő formátumban: ``` https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.)

> [!NOTE]
> Minden eszköz a több titkosítás (az AES-128, a PlayReady, Widevine, FairPlay) titkosíthatók. Lásd: [adatfolyam-protokollok és a titkosítási típusok](content-protection-overview.md#streaming-protocols-and-encryption-types), érdemes lehet kombinálni hasznossá megjelenítéséhez.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei.

* Tekintse át a [áttekintés tartalom](content-protection-overview.md) cikk.
* A Visual Studio Code vagy a Visual Studio telepítése
* Hozzon létre egy új Azure Media Services-fiók leírtak [a gyors üzembe helyezés](create-account-cli-quickstart.md).
* Media Services API következő használatához szükséges hitelesítő adatok elkérése [hozzáférés API-k](access-api-cli-how-to.md)

## <a name="download-code"></a>Kód letöltése

Klónozás egy GitHub-adattár, amely tartalmazza a teljes .NET minta tárgyalt ebben a témakörben a számítógépen a következő parancsot:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
A "titkosítani az AES-128" minta található a [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) mappa.

> [!NOTE]
> A minta hoz létre egyedi erőforrásokat, minden alkalommal, amikor az alkalmazás futtatásához. Általában felhasználja a meglévő erőforrásokhoz, mint a átalakítások és házirendeket (Ha a meglévő erőforrás szükséges konfigurációk). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>A Media Services API-k használatának megkezdése a .NET SDK-val

Ha szeretné megkezdeni a Media Services API-k használatát a .NET-tel, létre kell hoznia egy **AzureMediaServicesClient** objektumot. Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. A cikk elején klónozott kódban a **GetCredentialsAsync** függvény létrehozza a ServiceClientCredentials objektumot a helyi konfigurációs fájlban szereplő hitelesítési adatok alapján. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Kimeneti objektum létrehozása  

A kimeneti [objektum](https://docs.microsoft.com/rest/api/media/assets) tárolja a kódolási feladat eredményeit. A kódolás befejezését követően a kimeneti adategységen van közzétéve, az AES (ClearKey) titkosítással.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Vagy az egy kódolási átalakító létrehozása

Egy új [átalakításpéldány](https://docs.microsoft.com/rest/api/media/transforms) létrehozásakor meg kell adnia, milyen kimenetet szeretne létrehozni. A kötelező paraméter egy **TransformOutput** objektum, ahogyan az az alábbi kódban látható. Minden **TransformOutput** objektum tartalmaz **előzetes beállításokat**. Az **előzetes beállítások** részletesen leírják azokat a video- és audiofeldolgozási műveleteket, amelyek a kívánt **TransformOutput** objektum előállításához szükségesek. Az ebben a cikkben leírt minta az **AdaptiveStreaming** nevű beépített előzetes beállítást használja. Az előzetes beállítás a bemeneti videót egy automatikusan létrehozott sávszélességi skálává (sávszélesség–felbontás párokká) kódolja a bemeneti felbontás és sávszélesség alapján, majd ISO MP4-fájlokat hoz létre H.264 kódolású video- és AAC kódolású audiosávokkal, amelyek megfelelnek a sávszélesség–felbontás pároknak. 

Új létrehozása előtt [átalakítási](https://docs.microsoft.com/rest/api/media/transforms), ha már létezik egy használatával először ellenőrizze a **beolvasása** metódust, az alábbi kódban látható módon.  A Media Services 3-as verziója esetében a **Get** metódusok **null** értéket adnak vissza, ha az entitás nem létezik (a kis- és nagybetűket meg nem különböztető névellenőrzés történik).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Feladat elküldése

Ahogy korábban említettük, az [átalakítási](https://docs.microsoft.com/rest/api/media/transforms) objektum a recept, a [feladat](https://docs.microsoft.com/rest/api/media/jobs) pedig maga a kérés a Media Services számára, hogy alkalmazza az adott **átalakítást** egy meghatározott bemeneti video- vagy audiotartalomra. A **feladat** meghatároz bizonyos adatokat, például a bemeneti videó és a kimenet helyét.

Ebben az oktatóanyagban egy fájlt, amely keresztül a szervezetbe közvetlenül a feladat bevitel létrehozhatunk egy [HTTPs forrás URL-címe](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Várakozás a feladat befejeződésére

A feladat végrehajtása némi időt vesz igénybe, és fontos, hogy értesüljön arról, ha ez megtörtént. Az alábbi kódminta bemutatja, hogyan kérdezheti le a [feladat](https://docs.microsoft.com/rest/api/media/jobs) állapotát a szolgáltatásból. Éles alkalmazások esetében nem javasolt a lekérdezés használata a lehetséges késés miatt. Túlzott használat esetén a lekérdezés kapacitása korlátozott lehet egy adott fiókban. Fejlesztőknek inkább az Event Grid használata javasolt. További információkért tekintse meg az [események egyéni webes végponthoz való átirányítását](job-state-events-cli-how-to.md) ismertető cikket.

A **feladat** a következő állapotokon halad végig: **Ütemezve**, **Várólistán**, **Feldolgozás alatt**, **Befejeződött** (a végső állapot). Ha a feladat hibát észlelt, a **Hiba** állapot jelenik meg. Ha a feladat megszakítás alatt áll, a **Megszakítás**, a megszakítás befejeződése után pedig a **Megszakítva** állapot jelenik meg.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkey-policy"></a>ContentKey házirend létrehozása

Tartalomkulcs biztonságos hozzáférést biztosít az eszközök. A szabályzatban tartalom kulcs állítja be, hogyan a tartalomkulcsot a rendszer ügyfelek végén kell. A tartalomkulcs StreamingLocator tartozik. Media Services is biztosít a kulcs kézbesítési szolgáltatás letölti a titkosítási kulcsok engedéllyel rendelkező felhasználók számára. 

Ha olyan adatfolyamot kell megadni a Windows Media Player van szükség, a Media Services megadott kulcsot használja az dinamikusan titkosítására a tartalom (ebben az esetben az AES titkosítási.) Az adatfolyam visszafejtése, a Windows Media player kér a kulcsot a fő kézbesítési szolgáltatás. Annak megállapításához, hogy a felhasználó jogosult-e a kulcs eléréséhez, a szolgáltatás értékeli az engedélyezési házirendeket, amelyek a kulcshoz megadott.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="get-a-token"></a>A jogkivonat beolvasása
        
Az oktatóanyag azt adja meg a tartalom kulcs házirendhez kell rendelkeznie a token korlátozás. A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. A Media Services tokeneket támogatja a [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) formátumú, és hogy rendszer milyen azt a mintában.

A ContentKeyIdentifierClaim használatos a ContentKeyPolicy, ami azt jelenti, hogy a jogkivonat jelenik meg a kulcs kézbesítési szolgáltatás azt kell rendelkeznie a ContentKey azonosítóját. A mintában a Microsoft nem ad meg egy tartalomkulcsot a StreamingLocator létrehozásakor, a rendszer létrehoz egy véletlenszerű nekünk. Létrehozni a teszt token, azt kell szereznie a ContentKeyId kerüljenek bele a ContentKeyIdentifierClaim jogcímet.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="create-a-streaminglocator"></a>StreamingLocator létrehozása

A kódolás befejezése után a következő lépés a kimeneti objektumban található videó elérhetővé tétele az ügyfelek számára lejátszásra. Ezt két lépésben teheti meg: először hozzon létre egy [StreamingLocatort](https://docs.microsoft.com/rest/api/media/streaminglocators), majd a streamelési URL-címeket, amelyeket az ügyfelek használhatnak. 

A **StreamingLocator** létrehozásának folyamatát közzétételnek nevezzük. Alapértelmezés szerint a **StreamingLocator** azonnal érvényessé válik az API-hívás után, és a törléséig aktív marad. Ehelyett be lehet állítani indítási és befejeződési időpontokat is. 

A [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) létrehozása során meg kell adnia a kívánt **StreamingPolicyName** elemet. Ebben az oktatóanyagban a PredefinedStreamingPolicies, amely arra kéri a tartalmat az adatfolyamként történő közzététele az Azure Media Services egyik használunk. Ebben a példában az AES Envelope titkosítási alkalmazzák (más néven ClearKey titkosítási, mert a kulcsot a rendszer a lejátszás ügyfél HTTPS és a nem a DRM licenc keresztül).

> [!IMPORTANT]
> Egyéni [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) használata esetén érdemes korlátozott számú szabályzatot létrehoznia a Media Service-fiókhoz, és újra felhasználni őket a StreamingLocator használatakor, amikor ugyanolyan titkosítási beállításokra és protokollokra van szükség. A Media Service-fiókban korlátozva van a StreamingPolicy-bejegyzések száma. Nem érdemes új streamelési szabályzatot létrehozni minden egyes StreamingLocatorhöz.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="build-a-dash-streaming-url"></a>Build a streaming URL-cím KÖTŐJELLEL

Most, hogy a [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) lett létrehozva, beszerezheti a streamelési URL-címeket. URL-cím létrehozásához össze kell fűznie a [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) gazdanevét és a **StreamingLocator** elérési útját. Ebben a mintában az *alapértelmezett* **StreamingEndpoint** elemet használjuk. A Media Service-fiók létrehozásakor ez az *alapértelmezett* **StreamingEndpoint** leállított állapotú lesz, ezért meg kell hívnia a **Start** parancsot.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>A Media Service-fiók erőforrásainak eltávolítása

Általában érdemes eltávolítani mindent azon objektumok kivételével, amelyeket később is szeretne használni (átalakítások, StreamingLocator objektumok stb.). Ha ki szeretné üríteni fiókját a kísérletezés után, töröljön minden erőforrást, amelyet nem szeretne ismét használni.  A következő kóddal például törölheti a feladatokat.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="next-steps"></a>További lépések

[Áttekintés](content-protection-overview.md)