---
title: "Widevine-licencek kézbesíthet Azure Media Services castLabs használatával |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan használható az Azure Media Services (AMS) által a PlayReady vagy a Widevine DRMs AMS dinamikusan titkosított adatfolyam továbbítására. A PlayReady-licenc Media Services PlayReady licenckiszolgáló származik, és Widevine-licenc castLabs licenckiszolgáló hozta."
services: media-services
documentationcenter: 
author: Mingfeiy
manager: cfowler
editor: 
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: Mingfeiy;willzhan;Juliako
ms.openlocfilehash: 5b69e804809f834e81221fb2787a997a52dbe286
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>A castLabs használata a Widevine-licencek közvetítéséhez az Azure Media Servicesbe
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti, hogyan használható az Azure Media Services (AMS) által a PlayReady vagy a Widevine DRMs AMS dinamikusan titkosított adatfolyam továbbítására. A PlayReady-licenc származik a licenckiszolgáló Media Services PlayReady és Widevine-licenc hozta **castLabs** licenckiszolgáló.

Adatfolyam-CENC (PlayReady és/vagy Widevine) által védett tartalmak lejátszásához, használhatja a [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Lásd: [AMP dokumentum](http://amp.azure.net/libs/amp/latest/docs/) részleteiről.

A következő ábra azt mutatja be, a magas szintű Azure Media Services és a castLabs integrációs architektúra.

![Integráció](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Tipikus rendszer beállítása
* A médiatartalom AMS tárolja.
* Kulcs azonosítók tartalom kulcsok castLabs, mind az AMS vannak tárolva.
* castLabs és AMS egyaránt rendelkezik beépített tokent használó hitelesítés. Az alábbi szakaszok ismertetik a hitelesítési tokenek. 
* Amikor egy ügyfél történő folyamatos igényel, a tartalom dinamikusan titkosított rendelkező **Common Encryption** (CENC) és a Smooth Streaming, valamint a kötőjel AMS által dinamikusan csomagolni. Azt is biztosítanak a HLS streamelési protokoll PlayReady M2TS elemi adatfolyam titkosítását.
* PlayReady-licenc AMS licenckiszolgáló kéri le a rendszer, és Widevine-licenc castLabs licenckiszolgáló kéri le a rendszer. 
* Media Player automatikusan eldönti, mely licenc beolvasni az ügyfél platform képességei alapján. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Hitelesítési jogkivonatok létrehozásához a licenc beolvasásakor
CastLabs, mind az AMS támogatja (JSON Web Token) JWT jogkivonat formátumának engedélyezése licenccel. 

### <a name="jwt-token-in-ams"></a>Az AMS JWT jogkivonat
A következő táblázat ismerteti az AMS JWT jogkivonat. 

| Kiállító | A választott kibocsátó karakterláncot Secure Token Service (STS) |
| --- | --- |
| Célközönség |A használt STS a célközönség karakterlánc |
| Jogcímek |Jogcímek egy készletét |
| NotBefore |A token érvényességének kezdő |
| Lejár |A token érvényességének vége |
| SigningCredentials |A kulcs, amelyet használ PlayReady licenckiszolgáló, castLabs licenckiszolgáló és STS, annak oka lehet szimmetrikus vagy aszimmetrikus kulcs. |

### <a name="jwt-token-in-castlabs"></a>A castLabs JWT jogkivonat
A következő táblázat ismerteti a castLabs JWT jogkivonat. 

| Név | Leírás |
| --- | --- |
| optData |Egy adatokat tartalmazó JSON-karakterlánc. |
| CRT |Az eszköz adatainak tartalmazó JSON karakterláncnak a licencelési adatokat, és a lejátszás jogok. |
| IAT |Az aktuális dátum és idő a epoch. |
| jti |Ez a token (összes jogkivonat csak egyszer használható a castLabs rendszerben) kapcsolatos egyedi azonosítója. |

## <a name="sample-solution-set-up"></a>A minta megoldás beállítása
A [megoldás minta](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) két projektet tartalmaz:

* Egy konzolalkalmazás használható egy már feldolgozott eszköz, mind a PlayReady, mind a Widevine DRM korlátozások beállítása.
* A webes alkalmazás, amely átadja a jogkivonatokat, ami az STS szolgáltatással nagyon egyszerűsített verziója sikerült tekinthető meg.

A Konzolalkalmazás használata:

1. Módosítsa az app.config AMS hitelesítő adatokat, a castLabs hitelesítő adatokat, a STS konfigurációs és a megosztott kulcs beállítása.
2. Töltse fel az eszköz az AMS.
3. Az UUID azonosító beszerzése a feltöltött eszköz, és módosítsa a sor 32 a Program.cs fájlban:
   
      var objIAsset = _context. Assets.Where (x = > x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf"). FirstOrDefault();
4. Használjon egy AssetId elnevezési az eszköz a castLabs rendszerben (sor 44 a Program.cs fájlban).
   
   Meg kell adni a AssetId **castLabs**; kell lennie egy egyedi alfanumerikus karakterlánc.
5. Futtassa a programot.

A webes alkalmazás (STS) használata:

1. Módosítsa a web.config telepítő castlabs kereskedelmi azonosítója, az STS-konfiguráció és a megosztott kulcsot.
2. Telepítse az Azure-webhelyekre.
3. Nyissa meg a webhelyet.

## <a name="playing-back-a-video"></a>Vissza a videó lejátszása
(PlayReady és/vagy Widevine) közös titkosítással titkosított videó lejátszása, használhatja a [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Ha fut a Konzolalkalmazás, annál a tartalom kulcs azonosítója és a jegyzékfájl URL-címet.

1. Nyisson meg egy új lapot, és indítsa el a STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Ugrás a [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
3. Illessze be a streamelési URL-cím.
4. Kattintson a **speciális beállítások** jelölőnégyzetet.
5. Az a **védelmi** legördülő menüben válassza a PlayReady és/vagy Widevine.
6. Illessze be a származik a jogkivonat szövegmezőjének az STS-tokent. 
   
   A castLab licenckiszolgáló nem kell a "tulajdonosi =" előtag előtt a jogkivonatot. Ezért távolítsa el, amely a token elküldése előtt.
7. A Windows Media player frissítése.
8. A videó lejátszása kell lehet.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

