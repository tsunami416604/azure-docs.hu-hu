---
title: Védve a tartalmat az Azure Media Services |} Microsoft Docs
description: Ez a cikk áttekintést a Media Services tartalomvédelem.
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
ms.date: 06/25/2018
ms.author: juliako
ms.openlocfilehash: 28c10d7c478ea7a9b1d1bfa91da79452eba3a4b6
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132916"
---
# <a name="content-protection-overview"></a>A védett tartalom áttekintése

Azure Media Services segítségével biztonságos az adathordozó a tárhely, feldolgozás és kézbesítési keresztül elhagyja óta. A Media Services, az élő és igény tartalom Advanced Encryption Standard (AES-128) vagy a három fő digitális tartalomvédelmi (DRM) felügyeleti rendszerek dinamikusan titkosított biztosíthat: Microsoft PlayReady, Google Widevine és Apple FairPlay. A Media Services is biztosít egy szolgáltatás, amelynek segítségével a AES-kulcsok és DRM hitelesített ügyfelek (PlayReady, Widevine és FairPlay) licenceket. 

Az alábbi ábrán a Media Services tartalomvédelem-munkafolyamat: 

![Tartalom védelme](./media/content-protection/content-protection.png)

&#42;*dinamikus titkosítást támogat, az AES-128 "tiszta kulcsot" CBCS és CENC. További információ: a támogatási mátrix [Itt](#streaming-protocols-and-encryption-types).*

Ez a cikk azt ismerteti, fogalmak és terminológiát ismertetése a védett tartalom Media Services kapcsolódik. A cikk ismertetik a tartalmak védelméhez hivatkozásokat is tartalmaz. 

## <a name="main-components-of-the-content-protection-system"></a>A tartalomvédelem rendszer fő összetevői

Fejezze be a "content protection" rendszer vagy alkalmazás tervét, szüksége teljes megértéséhez részéről az erőfeszítés körét. Az alábbi lista áttekintést három részből kellene kell megvalósítani. 

1. Az Azure Media Services-kód
  
  * PlayReady-, Widevine-és/vagy FairPlay licenc sablonok. A sablonok segítségével úgy konfigurálhatja a jogok és engedélyek a használt DRMs mindegyikének
  * Licenc kézbesítési engedélyezési, a JWT jogcímei alapján jogosultsági ellenőrzés logikájának megadása
  * Tartalomkulcs, az adatfolyam-továbbítási protokollok és a megfelelő DRMs alkalmazza, definiáló DRM-titkosítás

  > [!NOTE]
  > Minden eszköz a több titkosítás (az AES-128, a PlayReady, Widevine, FairPlay) titkosíthatók. Lásd: [adatfolyam-protokollok és a titkosítási típusok](#streaming-protocols-and-encryption-types), érdemes lehet kombinálni hasznossá megjelenítéséhez.
  
  Következő lépések alapján titkosítják AES tartalom megjelenítése: [védelem az AES titkosítási](protect-with-aes128.md)
 
2. Player AES vagy DRM-ügyféllel. A videólejátszó alkalmazások egy player SDK (natív vagy webböngésző-alapú) alapján az alábbi követelményeknek kell megfelelnie:
  * A Windows Media player SDK támogatja a szükséges DRM-ügyfelek
  * A Windows Media player SDK támogatja a szükséges adatfolyam-továbbítási protokollok: Smooth, DASH vagy HLS
  * A Windows Media player SDK képesnek kell lennie a JWT jogkivonat licenc beszerzési kérés benyújtása kezeléséhez
  
    Egy player segítségével létrehozható a [Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/). Használja a [Azure Media Player ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/) adhatja meg, melyik DRM technológiát DRM különböző platformokon használatára.

    A tesztelési AES vagy CENC (Widevine + PlayReady) titkosított tartalom, használhat [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html). Győződjön meg arról, kattintson a "Speciális beállításai" és AES ellenőrizze, és adja meg a jogkivonatot.

    FairPlay titkosított tartalmat ellenőrzéséhez programmal, [a teszt player](http://aka.ms/amtest). A Windows Media player Widevine, PlayReady, támogatja, és FairPlay DRMs, valamint az AES-128 törölje a titkosítási kulcs. Meg kell adnia a megfelelő böngésző különböző DRMs tesztelése: Chrome/Opera/Firefox Widevine, MS peremhálózati/IE11 a PlayReady, a FairPlay maOS a Safari.

3. Secure Token Service (STS), amely kiadja a JSON webes jogkivonat (JWT) mint háttér erőforrás-hozzáférési jogkivonat. Az AMS licenctovábbítási szolgáltatások használhatja a háttér-erőforrásként. Az STS szolgáltatással rendelkezik az alábbiak megadásához:

  * Kibocsátó és a célközönség (vagy hatókör)
  * Jogcímek, az üzleti követelmények a tartalomvédelem függő
  * Szimmetrikus vagy aszimmetrikus ellenőrzési aláírás-ellenőrzés
  * Kulcsváltás támogatási (ha szükséges)

    Használhat [az STS eszköz](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) tesztelési STS, amely támogatja a hitelesítési kulcs 3 típusú: szimmetrikus, aszimmetrikus, vagy az aad-ben a kulcsváltás. 

> [!NOTE]
> Lehetőleg összpontosítson, és a teljes vizsgálat, minden egyes (lásd fent), a következő rész áthelyezése előtt. A "content protection" rendszer teszteléséhez használja a fenti listában megadott eszközök.  

## <a name="streaming-protocols-and-encryption-types"></a>Adatfolyam-továbbítási protokollok és a titkosítási típusok

A Media Services segítségével a tartalom titkosított dinamikusan AES-kulcs törlése vagy DRM-titkosítást a PlayReady, Widevine vagy FairPlay. Jelenleg a HTTP-Live Streaming (HLS), MPEG DASH vagy Smooth Streaming formátumban titkosíthatók. Minden protokoll a következő titkosítási módszereket támogatja:

|Protokoll|A tárolóformátummal|Titkosítási sémája|
|---|---|---|---|
|MPEG-DASH|Összes|AES|
||CSF(fmp4) |CENC (Widevine + PlayReady) |
||CMAF(fmp4)|CENC (Widevine + PlayReady)|
|HLS|Összes|AES|
||MPG2 – TS |CBCS (Fairplay) |
||MPG2 – TS |CENC (PlayReady) |
||CMAF(fmp4) |CENC (PlayReady) |
|Smooth Streaming|fMP4|AES|
||fMP4 | CENC (PlayReady) |

## <a name="dynamic-encryption"></a>Dinamikus titkosítás

A Media Services v3 egy tartalomkulcsot StreamingLocator társítva (lásd: [ebben a példában](protect-with-aes128.md)). A Media Services kulcs kézbesítési szolgáltatás használata esetén meg kell automatikus generálása a tartalomkulcsot. Meg kell hoznak létre a tartalomkulcs saját kezűleg saját kulcs kézbesítési szolgáltatás használata akkor, vagy ha a tartalom ugyanazzal a kulccsal van két adatközpont kell egy magas rendelkezésre állású forgatókönyv kezelni kell.

Adatfolyam egy player kér, amikor Media Services a megadott kulcs segítségével dinamikusan titkosítani az AES tiszta kulcsot vagy DRM-titkosítás segítségével. Az adatfolyam visszafejtése, a Windows Media player kér a kulcsot a Media Services kulcs kézbesítési szolgáltatás vagy a megadott kulcs kézbesítési szolgáltatás. Döntse el, hogy a felhasználó jogosult-e a kulcs eléréséhez, hogy a szolgáltatás értékeli az engedélyezési házirendeket, amelyek a kulcshoz megadott.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 egyértelműen kulcs vs. DRM

Az ügyfelek gyakran wonder, hogy azok használja-e az AES titkosítási vagy DRM rendszert. Az elsődleges különbség a két rendszer között, hogy az AES titkosítási a tartalomkulcs átkerülnek az ügyfél egy nem titkosított formátumban ("a clear"). Ennek eredményeképpen a tartalom titkosításához használt kulcs tekintheti meg a hálózati nyomkövetés egyszerű szöveges az ügyfélen. AES-128 egyértelműen kulcstitkosítási alkalmas használata esetekben, amikor a jelentésmegjelenítő egy megbízható entitás (például titkosított vállalati videók legyen az alkalmazottak által megtekinthető a vállalaton belül terjesztett).

PlayReady Widevine és FairPlay AES-128 titkosítást képest magasabb szintű összes biztosítása törölje a titkosítás. A tartalomkulcs átkerülnek a titkosított formátumban. Emellett visszafejtési az operációs rendszer szintjén, ott, ahol egy rosszindulatú felhasználó támadható meg a biztonságos környezetben kell kezelni. DRM használati esetek, ahol az használatával nem lehet egy megbízható entitás, és szüksége van a legmagasabb szintű biztonság ajánlott.

## <a name="storage-side-encryption"></a>Tárolási kiszolgálóoldali titkosítás

Az inaktív eszközök védelme érdekében az eszközöket a storage ügyféloldali titkosítása titkosítani kell. Az alábbi táblázat bemutatja, hogyan működik a storage ügyféloldali titkosítás a Media Services v3:

|A titkosítási beállítás|Leírás|Media Services v3|
|---|---|---|---|
|Media Services-tárolás titkosítása| AES-256 titkosítás, kulcs Media Services által felügyelt|Nem támogatott<sup>(1)</sup>|
|[Storage szolgáltatás titkosítási az inaktív adatok](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Kiszolgálóoldali titkosítás Azure Storage által kínált kulcsát kezeli az Azure-ban vagy az ügyfél által|Támogatott|
|[Storage ügyféloldali titkosítása](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure-tárolót, a Key Vault ügyfél által felügyelt kulcs által kínált ügyféloldali titkosítás|Nem támogatott|

<sup>1</sup> v3 a Media Services, storage encryption (az AES-256 titkosítás) van csak a visszamenőleges kompatibilitás létrehozásakor támogatott az eszközök volt a Media Services v2. Tehát a meglévő tárhely v3 együttműködik eszközök titkosítottak, de nem teszi lehetővé a újakat.

## <a name="licenses-and-keys-delivery-service"></a>Licencek és a kulcsok kézbesítési szolgáltatás

A Media Services (PlayReady, Widevine, FairPlay) DRM-licencek és AES-kulcsok kézbesítéséhez hitelesített ügyfelek kulcs kézbesítési szolgáltatást nyújt. A REST API vagy egy Media Services ügyféloldali kódtár segítségével konfigurálhatja a hitelesítési és engedélyezési házirendeket a licencekkel és a kulcsok.

## <a name="control-content-access"></a>Tartalom elérés

Azt is szabályozhatja, hogy ki férhet hozzá a tartalom a tartalom kulcs házirend konfigurálásával. A Media Services szolgáltatásban több különböző módot is beállíthat, amelynek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. A tartalom kulcs házirendet kell konfigurálnia. Az ügyfél (a lejátszó) meg kell felelnie a házirendet, a kulcs el lehet juttatni az ügyfél előtt. A tartalom kulcs házirend rendelkezhet **nyissa meg a** vagy **token** korlátozás. 

Jogkivonat-korlátozott tartalom kulcs házirendnek a tartalomkulcs érkezik csak egy ügyfél, amely a kulcs/licenc kérelem jeleníti meg egy érvényes JSON webes jogkivonat (JWT) vagy egyszerű webes jogkivonat (SWT). Ez a token biztonságijogkivonat-szolgáltatás (STS) kell kiállítania. Az Azure Active Directoryt az STS-ként, vagy egy egyéni STS telepítése. Az STS be kell állítani a megadott kulcs és a probléma JOGCÍMEKKEL, amely a token korlátozás konfigurációjában megadott aláírt jogkivonat létrehozásához. A Media Services kulcs kézbesítési szolgáltatás a kért kulcs/licencfeltételeket az ügyfélnek ad vissza, ha a jogkivonat érvényes, és a jogkivonatában lévő jogcímeket pontosan megegyeznek a kulcs/licencfeltételeket konfigurálva.

A token korlátozott házirendjének konfigurálásakor adjon meg, hogy az elsődleges hitelesítési kulcs, a kibocsátó és a célközönség paramétereket. Az elsődleges hitelesítési kulcs, amely a token aláírt kulcsot tartalmazza. A nem a biztonságos biztonságijogkivonat-szolgáltatás, amely kibocsátja a jogkivonatot. A célközönséget, más néven a hatókör, ismerteti a jogkivonat a leképezést, vagy az erőforrás a token engedélyezi a hozzáférést. A Media Services kulcs kézbesítési szolgáltatás ellenőrzi, hogy ezek az értékek a token egyeznek-e a sablonban szereplő értékeket.

## <a name="streaming-urls"></a>Streamelési URL-ek

Az objektum egynél több DRM lett titkosítva, használjon egy titkosítási címke a streamelési URL-cím: (formátum = "m3u8-aapl" titkosítási = "xxx").

A következők érvényesek:

* Titkosítási típus nem kell az URL-cím adható meg, ha csak egy titkosítási alkalmazta az eszközhöz.
* Titkosítási típus-és nagybetűket.
* A következő titkosítási típusok adhatók meg:
  * **cenc**: A PlayReady vagy Widevine (common encryption)
  * **cbcs-aapl**: A FairPlay (AES CBC titkosítást)
  * **CBC**: az AES envelope titkosítás

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

Használja a következő hibaelhárítási információk megvalósítási problémái segítségért.

* A kibocsátó URL-cím végén szerepelnie kell "/". A célközönség kell lennie a player alkalmazás ügyfél-azonosítót. Továbbá adja hozzá "/" a kibocsátó URL-cím végén.

  ```
  <add key="ida:audience" value="[Application Client ID GUID]" />
  <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
  ```

* Az engedélyeket az alkalmazásnak az Azure AD hozzá a **konfigurálása** az alkalmazás lapján. Engedélyek is szükségesek az alkalmazások a helyi és a telepített verzió.
* Használja a megfelelő kibocsátó dinamikus CENC védelem beállítása során.

  ```
  <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
  ```

  A következő nem működik:

  ```
  <add key="ida:issuer" value="https://username.onmicrosoft.com/" />
  ```

  A GUID az az Azure AD bérlő azonosítója. A GUID-azonosító található a **végpontok** az Azure portálon előugró menüjét.

* Támogatás csoporttagság jogosultságokat jogcímek. Győződjön meg arról, hogy a következő Azure AD alkalmazás-jegyzékfájl: 

    "groupMembershipClaims": "All" (alapértelmezett értéke null)

## <a name="next-steps"></a>További lépések

[Az AES titkosítási védelme](protect-with-aes128.md)
