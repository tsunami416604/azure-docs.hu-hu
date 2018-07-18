---
title: AES az Azure Media Services dinamikus titkosítás használata |} A Microsoft Docs
description: Tartalomkézbesítés 128 bites AES titkosítási kulcsokkal titkosítja a Microsoft Azure Media Services használatával. A Media Services emellett a legfontosabb licenctovábbítási szolgáltatása, amely a titkosítási kulcsokat biztosít a jogosult felhasználókra. Ez a témakör bemutatja, hogyan dinamikusan titkosítani az AES-128, és a kulcstovábbítást használata.
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
ms.date: 07/12/2018
ms.author: juliako
ms.openlocfilehash: 3e5de521570a587b049702dabd3e3692c4227796
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114793"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>AES-128, a dinamikus titkosítás és a kulcstovábbítást használata

A Media Services segítségével HTTP Live Streaming (HLS), MPEG-DASH és Smooth Streaming az AES-128 bites titkosítási kulcsok használatával titkosítja. A Media Services emellett a legfontosabb licenctovábbítási szolgáltatása, amely a titkosítási kulcsokat biztosít a jogosult felhasználókra. Ha azt szeretné, a Media Services az objektum titkosítására, rendelje hozzá a titkosítási kulcs StreamingLocator, és is konfigurálhatja a tartalom fő házirendet. Adatfolyam-lejátszó kér, amikor a Media Services a megadott kulcs használatával dinamikusan titkosítani az AES-titkosítás segítségével. A stream visszafejteni, az a Windows Media player a kulcs kér a kulcstovábbítást. Annak megállapításához, hogy a felhasználó jogosult kulcs lekérése, a szolgáltatás kiértékeli a tartalom a kulcshoz megadott kulcs házirend.

A cikk alapján a [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) minta. A minta azt ismerteti, hogyan hozhat létre egy kódolási átalakító adaptív sávszélességű kódolási előbeállítást beépített használó, és feltölti a fájlt közvetlenül egy [HTTPs URL-cím forrás](job-input-from-http-how-to.md). A kimeneti adategység ezután közzé lesz téve, az AES (ClearKey) titkosítással. A minta kimenete az Azure Media Player, beleértve a DASH-jegyzék és az AES-jogkivonat a tartalmak lejátszásához szükséges URL-címe. A minta a JWT jogkivonat lejáratának 1 órás értékre állítja. Nyisson meg egy böngészőt, és illessze be a megjelenő URL-cím, indítsa el az Azure Media Player demó oldal URL-cím és jogkivonat automatikusan kitölti már a következő formátumban: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.

> [!NOTE]
> Minden eszközhöz több titkosítási típusok (AES-128, a PlayReady, Widevine, FairPlay) használatával titkosítsa. Lásd: [Streamelési protokollok és a titkosítási típusok](content-protection-overview.md#streaming-protocols-and-encryption-types), mi értelme úgy, hogy megtekintéséhez.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei.

* Tekintse át a [Content protection áttekintése](content-protection-overview.md) cikk.
* A Visual Studio Code vagy a Visual Studio telepítése
* Hozzon létre egy új Azure Media Services-fiók leírtak [ebben a rövid útmutatóban](create-account-cli-quickstart.md).
* A Media Services API-k használatához a következő szükséges hitelesítő adatainak lekérése [hozzáférés API-k](access-api-cli-how-to.md)

## <a name="download-code"></a>Kód letöltése

A teljes .NET-mintát tartalmazó GitHub-adattár klónozása a a gépre a következő paranccsal cikkben leírtak szerint:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
A "titkosítani az AES-128" mintában található a [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) mappát.

> [!NOTE]
> A minta egyedi erőforrásokat hozza létre, minden alkalommal, amikor futtatja az alkalmazást. Általában felhasználja a meglévő erőforrások, például átalakítások és házirendeket (Ha meglévő erőforrás a szükséges konfigurációk). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>A Media Services API-k használatának megkezdése a .NET SDK-val

Ha szeretné megkezdeni a Media Services API-k használatát a .NET-tel, létre kell hoznia egy **AzureMediaServicesClient** objektumot. Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. A kódban, a cikk elején klónozta a **GetCredentialsAsync** függvény létrehozza az ServiceClientCredentials objektumot a helyi konfigurációs fájlban megadott hitelesítő adatok alapján. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Kimeneti objektum létrehozása  

A kimeneti [objektum](https://docs.microsoft.com/rest/api/media/assets) tárolja a kódolási feladat eredményeit.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>GET, vagy hozzon létre egy kódolási átalakító

Egy új [átalakításpéldány](https://docs.microsoft.com/rest/api/media/transforms) létrehozásakor meg kell adnia, milyen kimenetet szeretne létrehozni. A kötelező paraméter egy **TransformOutput** objektum, ahogyan az az alábbi kódban látható. Minden **TransformOutput** objektum tartalmaz **előzetes beállításokat**. Az **előzetes beállítások** részletesen leírják azokat a video- és audiofeldolgozási műveleteket, amelyek a kívánt **TransformOutput** objektum előállításához szükségesek. Az ebben a cikkben leírt minta az **AdaptiveStreaming** nevű beépített előzetes beállítást használja. Az előzetes beállítás a bemeneti videót egy automatikusan létrehozott sávszélességi skálává (sávszélesség–felbontás párokká) kódolja a bemeneti felbontás és sávszélesség alapján, majd ISO MP4-fájlokat hoz létre H.264 kódolású video- és AAC kódolású audiosávokkal, amelyek megfelelnek a sávszélesség–felbontás pároknak. 

Előtt hozzon létre egy új [átalakítása](https://docs.microsoft.com/rest/api/media/transforms), először nézze meg, ha már létezik egy használatával a **első** metódus, a következő kódban látható módon.  A Media Services 3-as verziója esetében a **Get** metódusok **null** értéket adnak vissza, ha az entitás nem létezik (a kis- és nagybetűket meg nem különböztető névellenőrzés történik).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Feladat elküldése

Ahogy korábban említettük, az [átalakítási](https://docs.microsoft.com/rest/api/media/transforms) objektum a recept, a [feladat](https://docs.microsoft.com/rest/api/media/jobs) pedig maga a kérés a Media Services számára, hogy alkalmazza az adott **átalakítást** egy meghatározott bemeneti video- vagy audiotartalomra. A **feladat** meghatároz bizonyos adatokat, például a bemeneti videó és a kimenet helyét.

Ebben az oktatóanyagban létrehozunk egy fájlt, amely közvetlenül a betöltött alapján a feladat bemenetének egy [HTTPs URL-cím forrás](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Várakozás a feladat befejeződésére

A feladat végrehajtása némi időt vesz igénybe, és fontos, hogy értesüljön arról, ha ez megtörtént. Az alábbi kódminta bemutatja, hogyan kérdezheti le a [feladat](https://docs.microsoft.com/rest/api/media/jobs) állapotát a szolgáltatásból. Éles alkalmazások esetében nem javasolt a lekérdezés használata a lehetséges késés miatt. Túlzott használat esetén a lekérdezés kapacitása korlátozott lehet egy adott fiókban. Fejlesztőknek inkább az Event Grid használata javasolt. További információkért tekintse meg az [események egyéni webes végponthoz való átirányítását](job-state-events-cli-how-to.md) ismertető cikket.

A **feladat** a következő állapotokon halad végig: **Ütemezve**, **Várólistán**, **Feldolgozás alatt**, **Befejeződött** (a végső állapot). Ha a feladat hibát észlelt, a **Hiba** állapot jelenik meg. Ha a feladat megszakítás alatt áll, a **Megszakítás**, a megszakítás befejeződése után pedig a **Megszakítva** állapot jelenik meg.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkeypolicy"></a>Hozzon létre egy ContentKeyPolicy

Tartalomkulcs biztonságos hozzáférést biztosít az eszközök. Szeretne létrehozni egy **ContentKeyPolicy** , amely beállítja a tartalomkulcsot a rendszer hogyan továbbítja az ügyfelek közötti. A tartalomkulcs társítva van **StreamingLocator**. A Media Services emellett a legfontosabb licenctovábbítási szolgáltatása, amely a titkosítási kulcsokat biztosít a jogosult felhasználókra. 

Amikor egy adatfolyam-lejátszó kér, Media Services segítségével a megadott kulcs dinamikusan titkosítja a tartalom (ebben az esetben AES titkosítás segítségével.) A stream visszafejteni, az a Windows Media player a kulcs kér a kulcstovábbítást. Annak megállapításához, hogy a felhasználó jogosult kulcs lekérése, a szolgáltatás kiértékeli a tartalom a kulcshoz megadott kulcs házirend.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaminglocator"></a>StreamingLocator létrehozása

Miután befejeződött a kódolást, és a tartalom kulcs szabályzat van beállítva, az a következő lépés, hogy a kimenetben, hogy a videó lejátszás céljából az ügyfelek számára elérhető eszköz. Ehhez két lépésben: 

1. Hozzon létre egy [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)
2. Hozhat létre, amellyel az ügyfelek streamelési URL-címeket. 

Létrehozásának folyamatán a **StreamingLocator** közzététel nevezzük. Alapértelmezés szerint a **StreamingLocator** azonnal érvényessé válik az API-hívás után, és a törléséig aktív marad. Ehelyett be lehet állítani indítási és befejeződési időpontokat is. 

A [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) létrehozása során meg kell adnia a kívánt **StreamingPolicyName** elemet. Ebben az oktatóanyagban a PredefinedStreamingPolicies, amely arra kéri a tartalom streameléshez közzététele az Azure Media Services egyik használjuk. Ebben a példában az AES-boríték a rendszer titkosítást alkalmaz (más néven ClearKey titkosítás, mert a rendszer továbbítja a kulcsot a lejátszás ügyfélnek HTTPS és a nem DRM-licenc használatával).

> [!IMPORTANT]
> Egyéni [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) használata esetén érdemes korlátozott számú szabályzatot létrehoznia a Media Service-fiókhoz, és újra felhasználni őket a StreamingLocator használatakor, amikor ugyanolyan titkosítási beállításokra és protokollokra van szükség. A Media Service-fiókban korlátozva van a StreamingPolicy-bejegyzések száma. Nem érdemes új streamelési szabályzatot létrehozni minden egyes StreamingLocatorhöz.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Tesztjogkivonat lekérése
        
Ebben az oktatóanyagban adjuk meg a tartalom-szabályzat szeretné, hogy a jogkivonat korlátozás. A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. A Media Services jogkivonatokat támogatja a [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) formátumú, és hogy rendszer milyen konfigurálnánk a mintában.

A ContentKeyPolicy, ami azt jelenti, hogy a jogkivonat jelenik meg a kulcs-továbbítási szolgáltatást kell rendelkeznie a ContentKey azonosítóját, a ContentKeyIdentifierClaim használatban van. A mintában nem megadunk egy tartalomkulcsot, a StreamingLocator létrehozásakor, a rendszer létrehoz egy véletlenszerű számunkra. Annak érdekében, hogy a teszt létrehozása token, azt be kell szereznie a ContentKeyId a ContentKeyIdentifierClaim jogcím a helyezi.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>A DASH-streamelési URL-cím létrehozása

Most, hogy a [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) lett létrehozva, beszerezheti a streamelési URL-címek. URL-cím létrehozásához össze kell fűznie a [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) gazdanevét és a **StreamingLocator** elérési útját. Ebben a mintában az *alapértelmezett* **StreamingEndpoint** elemet használjuk. A Media Service-fiók létrehozásakor ez az *alapértelmezett* **StreamingEndpoint** leállított állapotú lesz, ezért meg kell hívnia a **Start** parancsot.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>A Media Service-fiók erőforrásainak eltávolítása

Általában érdemes eltávolítani mindent azon objektumok kivételével, amelyeket később is szeretne használni (átalakítások, StreamingLocator objektumok stb.). Ha ki szeretné üríteni fiókját a kísérletezés után, töröljön minden erőforrást, amelyet nem szeretne ismét használni.  A következő kóddal például törölheti a feladatokat.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="next-steps"></a>További lépések

Tekintse meg, hogy miként [DRM védelme](protect-with-drm.md)
