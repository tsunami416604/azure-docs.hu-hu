---
title: A castLabs használata a Widevine licencek kézbesítéséhez az Azure Media Services |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan használhatja az Azure Media Services (AMS), hogy olyan adatfolyamra, amely a PlayReady vagy a Widevine DRMs AMS dinamikusan titkosítja. A Media Services PlayReady licenckiszolgáló származik, a PlayReady-licenc, és a Widevine-licenc castLabs licenckiszolgáló hozta.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: Mingfeiy;willzhan;Juliako
ms.openlocfilehash: 0b3d8759f13f48e5fa95ff709fa283ed41e0ea25
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003210"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>A castLabs használata a Widevine-licencek közvetítéséhez az Azure Media Servicesbe 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Áttekintés

Ez a cikk bemutatja, hogyan használhatja az Azure Media Services (AMS), hogy olyan adatfolyamra, amely a PlayReady vagy a Widevine DRMs AMS dinamikusan titkosítja. A Media Services PlayReady licenckiszolgáló származik, a PlayReady-licenc, és a Widevine-licenc hozta **castLabs** licenckiszolgáló.

Vissza a CENC (PlayReady és/vagy Widevine) által védett tartalmak online lejátszásához használható [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Lásd: [AMP dokumentum](http://amp.azure.net/libs/amp/latest/docs/) részleteiről.

Az alábbi ábrán egy magas szintű Azure Media Services és a castLabs integrációs architektúra bemutatja.

![Integráció](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Tipikus rendszer beállítása

* A médiatartalom AMS tárolja.
* Tartalomkulcs kulcs azonosítóját castLabs és az AMS vannak tárolva.
* a castLabs és a AMS egyaránt rendelkeznek az eszközjogkivonattal történő hitelesítés a beépített. A következő részekben bemutatjuk a hitelesítési tokenek. 
* Amikor egy ügyfél a videó továbbításához, dinamikusan a tartalom titkosított a **Common Encryption** (CENC) és a Smooth Streaming és DASH AMS által dinamikusan csomagolni. Is tudunk PlayReady M2TS elemi stream titkosítási HLS streamelési protokollhoz.
* PlayReady-licenc az AMS-licenckiszolgáló nem gyűjt, és a Widevine-licenc castLabs licenckiszolgáló nem gyűjt. 
* Media Player automatikusan úgy dönt, hogy melyik licenc beolvasni az ügyfél-platform képességei alapján. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Hitelesítési jogkivonatok létrehozásához az első licenc

A castLabs és AMS is támogatja a JWT-(JSON Web Token) token formátuma engedélyezése licenccel. 

### <a name="jwt-token-in-ams"></a>Az AMS JWT-jogkivonat

A következő táblázat ismerteti az AMS JWT jogkivonat. 

| Kiállító | A kiválasztott kiállítók karakterláncra Secure Token Service (STS) |
| --- | --- |
| Célközönség |A használt STS célközönség karakterláncra |
| Igénylések |A jogcímek készlete |
| NotBefore |Indítsa el a jogkivonat érvényessége |
| Elévül |A token érvényességének vége |
| SigningCredentials |A kulcs, amelyet használ PlayReady licenckiszolgáló, castLabs licenckiszolgáló telepítése és az STS, annak oka az lehet szimmetrikus vagy aszimmetrikus kulccsal. |

### <a name="jwt-token-in-castlabs"></a>A castLabs JWT-jogkivonat

A következő táblázat ismerteti a castLabs JWT jogkivonat. 

| Name (Név) | Leírás |
| --- | --- |
| optData |Önnel kapcsolatos információkat tartalmazó JSON-karakterláncot. |
| CRT |Az eszköz adatait tartalmazó JSON-karakterláncot a licencelési adatokat és a lejátszás jogok. |
| IAT |Az aktuális dátum és epoch. |
| jti |Ez a token (jogkivonat minden csak egyszer használhatók fel a castLabs rendszerben) kapcsolatos egyedi azonosítója. |

## <a name="sample-solution-setup"></a>Mintául szolgáló megoldás – telepítés

A [minta megoldás](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) két projektből áll:

* Egy konzolalkalmazást, amely egy már betöltött eszköz, a PlayReady és Widevine DRM korlátozások beállítása is használható.
* Egy webalkalmazást, amely átadja ki jogkivonatokat, ami az STS szolgáltatással rendkívül egyszerűsített változata látható volt.

A Konzolalkalmazás használata:

1. Módosítsa az app.config állíthatja be az AMS hitelesítő adatok, a castLabs hitelesítő adatokat, a STS-konfiguráció és a megosztott kulcsot.
2. Az AMS-eszköz feltöltése.
3. A feltöltött adategység UUID kérhet, és módosítsa a sor 32 a Program.cs fájlban:
   
      var objIAsset = _context. Assets.Where (x = > x.Id == "nb:cid:UUID:dac53a5d – 1500-80bd-b864-f1e4b62594cf"). FirstOrDefault();
4. Használjon egy AssetId elnevezési az eszköz a castLabs rendszerben (sor: 44 a Program.cs fájlban).
   
   Be kell állítani a AssetId **castLabs**; egyedi alfanumerikus karakterláncnak lennie kell.
5. Futtassa a programot.

A Web Application (STS) használata:

1. Módosítsa a Web.config fájlt, a telepítő castlabs kereskedelmi Azonosítót, az STS-konfiguráció és a megosztott kulcsot.
2. Helyezze üzembe az Azure-webhelyek.
3. Keresse meg a webhelyet.

## <a name="playing-back-a-video"></a>Videó lejátszásának

Szeretne lejátszani egy videót, az általános titkosítás (a PlayReady és/vagy Widevine) titkosított, használhatja a [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). A Konzolalkalmazás futtatásakor meg a tartalom kulcs Azonosítóját és a jegyzékfájl URL-CÍMÉT.

1. Nyisson meg egy új lapot, majd indítsa el az STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Lépjen a [az Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
3. Illessze be a streamelési URL-CÍMÉT.
4. Kattintson a **speciális beállítások** jelölőnégyzetet.
5. Az a **védelmi** legördülő menüben válassza a PlayReady és/vagy Widevine.
6. Illessze be a jogkivonatot, portáltól kapott az STS a jogkivonat szövegmezőben. 
   
   A castLab licenckiszolgáló nem kell a "tulajdonosi =" előtag a token elé. Ezért távolítsa el, hogy a jogkivonat elküldése előtt.
7. Frissítés a Windows Media player.
8. A videó lejátszás alatt kell lennie.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

