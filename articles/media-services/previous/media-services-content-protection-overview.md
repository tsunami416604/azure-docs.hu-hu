---
title: A tartalom védelme az Azure Media Services szolgáltatással | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt az Azure Media Services 2-es számú használatával történő tartalomvédelemről.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 88e0e1c18722fd86e79fc1fa7722b59b3cb8966a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460959"
---
# <a name="content-protection-overview"></a>Tartalomvédelem – áttekintés 

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

Az Azure Media Services segítségével a számítógép tárolása, feldolgozása és kézbesítése révén biztosíthatja az adathordozókat. A Media Services segítségével az Advanced Encryption Standard (AES-128) vagy a három fő digitális jogkezelési (DRM) rendszer bármelyikével dinamikusan titkosított élő és igény szerinti tartalmakat is kézbesíthet: Microsoft PlayReady, Google Widevine és Apple FairPlay. A Media Services szolgáltatást is biztosít az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licencek hivatalos ügyfelek nek történő kézbesítéséhez. 

Az alábbi képen a Media Services tartalomvédelmi munkafolyamata látható: 

![Védelem biztosítása a PlayReadyvel](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Ez a cikk ismerteti a Media Services tartalomvédelem megértéséhez szükséges fogalmakat és terminológiát. A cikk a tartalom védelméről szóló cikkekre mutató hivatkozásokat is tartalmaz. 

## <a name="dynamic-encryption"></a>Dinamikus titkosítás

A Media Services segítségével dinamikusan, AES-titkosítással vagy DRM-titkosítással titkosított tartalmakat vezérelhet a PlayReady, a Widevine vagy a FairPlay használatával. Ha a tartalom titkosítva van egy AES egyértelmű kulccsal, és HTTPS-en keresztül küldi el, akkor nem egyértelmű, amíg el nem éri az ügyfelet. 

Minden titkosítási módszer a következő streamelési protokollokat támogatja:
 
- AES: MPEG-DASH, Smooth Streaming és HLS
- PlayReady: MPEG-DASH, Smooth Streaming és HLS
- Széles: MPEG-DASH
- FairPlay: HLS

A progresszív letöltések titkosítása nem támogatott. 

Egy eszköz titkosításához hozzá kell rendelnie egy titkosítási tartalomkulcsot az eszközhöz, és konfigurálnia kell egy engedélyezési házirendet a kulcshoz. A tartalomkulcsokat a Media Services adhatja meg vagy hozza létre automatikusan.

Az eszköz kézbesítési szabályzatát is konfigurálnia kell. Ha egy tárolóval titkosított eszközt szeretne streamelni, az eszközkézbesítési szabályzat konfigurálásával adja meg, hogyan szeretné kézbesíteni.

Amikor egy lejátszó adatfolyamot kér, a Media Services a megadott kulccsal dinamikusan titkosítja a tartalmat AES titkosítása iaes törlési kulccsal vagy DRM-titkosítással. Az adatfolyam visszafejtéséhez a lejátszó kéri a kulcsot a Media Services kulcskézbesítési szolgáltatásától. Annak eldöntéséhez, hogy a felhasználó jogosult-e a kulcs bekésésére, a szolgáltatás kiértékeli a kulcshoz megadott engedélyezési házirendeket.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 tiszta kulcs vs DRM
Az ügyfelek gyakran csodálkoznak, hogy AES titkosítást vagy DRM-rendszert kell-e használniuk. Az elsődleges különbség a két rendszer között az, hogy az AES titkosítással a tartalomkulcs titkosítatlan formátumban kerül továbbításra az ügyfélnek ("a tiszta"). Ennek eredményeképpen a tartalom titkosításához használt kulcs egyszerű szövegként megtekinthető az ügyfél hálózati nyomkövetésében. Az AES-128 egyértelmű kulcstitkosítás olyan esetekben használható, amikor a megtekintő megbízható fél (például a vállalaton belül terjesztett vállalati videók titkosítása, amelyeket az alkalmazottak megtekintenek).

A PlayReady, a Widevine és a FairPlay mind magasabb szintű titkosítást biztosítanak az AES-128 titkosításhoz képest. A tartalomkulcs átvitele titkosított formátumban történik. Emellett a visszafejtést biztonságos környezetben kezelik az operációs rendszer szintjén, ahol a rosszindulatú felhasználók nehezebben támadnak. A DRM olyan esetekhez ajánlott, ahol a megjelenítő esetleg nem megbízható fél, és a legmagasabb szintű biztonságra van szükség.

## <a name="storage-encryption"></a>Storage-titkosítás
A storage-titkosítás segítségével helyileg titkosíthatja a tiszta tartalmat az AES 256 bites titkosítással. Ezután feltöltheti az Azure Storage-ba, ahol az infóban titkosított. A tárolótitkosítással védett eszközök automatikusan titkosítatlanok lesznek, és a kódolás előtt titkosított fájlrendszerben kerülnek elhelyezésre. Az eszközök opcionálisan újra titkosítva feltöltés előtt vissza, mint egy új kimeneti eszköz. A tárolótitkosítás elsődleges használati esete az, ha a kiváló minőségű bemeneti médiafájlokat erős titkosítással szeretné biztosítani a lemezen.

A tárolóval titkosított eszköz biztosításához konfigurálnia kell az eszköz kézbesítési szabályzatát, hogy a Media Services tudja, hogyan szeretné kézbesíteni a tartalmat. Az eszköz streamelése előtt a streamelési kiszolgáló visszafejti és streameli a tartalmat a megadott kézbesítési szabályzat (például AES, közös titkosítás vagy titkosítás nélkül).

## <a name="types-of-encryption"></a>A titkosítás típusai
A PlayReady és a Widevine közös titkosítást (AES CTR mód) használ. A FairPlay AES CBC-módú titkosítást használ. Az AES-128 titkosítatlan titkosítása borítéktitkosítást használ.

## <a name="licenses-and-keys-delivery-service"></a>Licencek és kulcsok kézbesítési szolgáltatása
A Media Services kulcsfontosságú kézbesítési szolgáltatást nyújt a DRM (PlayReady, Widevine, FairPlay) licencek és AES-kulcsok jogosult ügyfelek nek történő kézbesítéséhez. Az [Azure Portal](media-services-portal-protect-content.md), a REST API vagy a Media Services SDK .NET használatával konfigurálhatja a licencek és kulcsok engedélyezési és hitelesítési szabályzatait.

## <a name="control-content-access"></a>Tartalom-hozzáférés szabályozása
A tartalomkulcs-engedélyezési házirend beállításával szabályozhatja, hogy ki férhet hozzá a tartalomhoz. A tartalomkulcs-engedélyezési házirend támogatja a nyílt vagy jogkivonat-korlátozást.

### <a name="open-authorization"></a>Nyílt engedélyezés
A nyílt engedélyezési házirend esetén a tartalomkulcs bármely ügyfélnek elküldésre kerül (nincs korlátozás).

### <a name="token-authorization"></a>Token engedélyezése
A jogkivonat-korlátozott engedélyezési házirend, a tartalomkulcs csak egy olyan ügyfélnek küldi, amely érvényes JSON webtoken (JWT) vagy egyszerű webes jogkivonatot (SWT) jelenít meg a kulcs/licenc kérelemben. Ezt a jogkivonatot egy biztonsági jogkivonat-szolgáltatásnak (STS) kell kiadnia. Használhatja az Azure Active Directoryt STS-ként, vagy egyéni STS-t telepíthet. Az STS-t úgy kell konfigurálni, hogy hozzon létre egy jogkivonatot a megadott kulccsal, és kiadja a jogkivonat-korlátozási konfigurációban megadott jogcímeket. A Media Services kulcskézbesítési szolgáltatás visszaadja a kért kulcsot/licencet az ügyfélnek, ha a jogkivonat érvényes, és a jogkivonatban lévő jogcímek megegyeznek a kulcshoz/licenchez konfigurált jogcímekhez.

A korlátozott jogkivonat-házirend konfigurálásakor meg kell adnia az elsődleges ellenőrzési kulcsot, a kibocsátót és a közönség paramétereit. Az elsődleges ellenőrzési kulcs tartalmazza azt a kulcsot, amelyhez a jogkivonat ot aláírták. A kibocsátó a biztonságos jogkivonat-szolgáltatás, amely kiadja a jogkivonatot. A célközönség, más néven hatókör, leírja a jogkivonat szándékát, vagy az erőforrás, amelyhez a jogkivonat engedélyezi a hozzáférést. A Media Services-kulcs kézbesítési szolgáltatás ellenőrzi, hogy ezek az értékek a jogkivonatban megegyeznek-e a sablonban lévő értékekkel.

### <a name="token-replay-prevention"></a>Token visszajátszásának megakadályozása

A *Token Replay Prevention* funkció lehetővé teszi a Media Services-ügyfelek számára, hogy korlátozzák, hogy hányszor ugyanazt a jogkivonatot lehet használni egy kulcs vagy licenc igényléséhez. Az ügyfél hozzáadhat egy `urn:microsoft:azure:mediaservices:maxuses` típustípusú jogcímet a jogkivonatban, ahol az érték a jogkivonat licenc vagy kulcs megszerzéséhez használható számának száma. Minden további kérelmek azonos jogkivonatot kulcskézbesítés i jogosulatlan választ ad vissza. Tekintse meg, hogyan adhat hozzá a jogcímet a [DRM-mintába.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601)
 
#### <a name="considerations"></a>Megfontolandó szempontok

* Az ügyfeleknek szabályozniuk kell a tokenek létrehozását. A jogcím kell helyezni a jogkivonatot is.
* A funkció használata kor, kérelmek tokenek, amelyek lejárati ideje több mint egy óra távolságra a kérelem fogadásának időpontjától elutasításra kerül egy jogosulatlan válasz.
* A tokeneket egyedileg azonosítja az aláírásuk. A hasznos adat bármilyen módosítása (például a lejárati idő frissítése vagy a jogcím) módosítja a jogkivonat aláírását, és olyan új jogkivonatnak számít, amelyet a kulcskézbesítés még nem ért el.
* A lejátszás sikertelen, ha a `maxuses` token túllépte az ügyfél által beállított értéket.
* Ez a funkció az összes meglévő védett tartalomhoz használható (csak a kibocsátott jogkivonatot kell módosítani).
* Ez a funkció a JWT-vel és az SWT-vel is működik.

## <a name="streaming-urls"></a>Streamelési URL-ek
Ha az eszköz egynél több DRM-mel volt titkosítva, használjon titkosítási címkét a streamelési URL-címben: (format='m3u8-aapl', encryption='xxx').

A következő szempontokat kell figyelembe venni:

* Egynél több titkosítási típus adható meg.
* A titkosítási típust nem kell megadni az URL-címben, ha csak egy titkosítást alkalmaztak az eszközre.
* A titkosítás típusa nem identik a kis- és nagybetűket.
* A következő titkosítási típusok adhatók meg:

  * **cenc**: PlayReady vagy Widevine esetén (gyakori titkosítás)
  * **cbcs-aapl**: FairPlay (AES CBC titkosítás)
  * **CBC**: AES boríték titkosításhoz

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="next-steps"></a>További lépések
Az alábbi cikkek a következő lépéseket ismertetik a tartalomvédelem megkezdéséhez:

* [Védettterület-titkosítással](media-services-rest-storage-encryption.md)
* [Védelem AES titkosítással](media-services-protect-with-aes128.md)
* [Védelem PlayReady és/vagy Widevine segítségével](media-services-protect-with-playready-widevine.md)
* [Védelem a FairPlay segítségével](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Kapcsolódó hivatkozások

* [JWT token hitelesítése](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integrálja az Azure Media Services OWMedia MVC-alapú alkalmazást az Azure Active Directoryval, és korlátozza a tartalomkulcs kézbesítését a JWT-jogcímek alapján](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
