---
title: A castLabs használata Widevine-licencek Azure Media Services szolgáltatásához | Microsoft dokumentumok
description: Ez a cikk azt ismerteti, hogyan használhatja az Azure Media Services (AMS) egy adatfolyam, amely dinamikusan titkosított AMS mind PlayReady és Widevine DRM-ek.
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
ms.date: 03/14/2019
ms.author: Juliako
ms.reviewer: willzhan
ms.openlocfilehash: 29a344c739d8d99da2e5c81d41a11c601e48022e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74969140"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>A castLabs használata a Widevine-licencek közvetítéséhez az Azure Media Servicesbe 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Áttekintés

Ez a cikk azt ismerteti, hogyan használhatja az Azure Media Services (AMS) egy adatfolyam, amely dinamikusan titkosított AMS mind PlayReady és Widevine DRM-ek. A PlayReady licenc a Media Services PlayReady licenckiszolgálóról származik, a Widevine licencet pedig a **castLabs** licenckiszolgáló.

A CENC (PlayReady és/vagy Widevine) által védett adatfolyam-tartalom lejátszásához használhatja az [Azure Media Player t.](https://aka.ms/azuremediaplayer) A részleteket lásd az [AMP-dokumentumban.](https://amp.azure.net/libs/amp/latest/docs/)

Az alábbi ábra egy magas szintű Azure Media Services és castLabs integrációs architektúrát mutat be.

![Integráció](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Tipikus rendszer beállítása

* A médiatartalom az AMS-ben tárolódik.
* A tartalomkulcsok kulcsazonosítói a castLabs és az AMS-ben is tárolódnak.
* CastLabs és AMS egyaránt token hitelesítés beépített. A következő szakaszok a hitelesítési jogkivonatokat tárgyalják. 
* Amikor egy ügyfél a videó streamelésére kér, a tartalom dinamikusan titkosítva lesz **a Common Encryption** (CENC) titkosítással, és az AMS dinamikusan csomagolva sima streamelésre és DASH-re. PlayReady M2TS elemi adatfolyam-titkosítást is biztosítunk a HLS streamprotokollhoz.
* A PlayReady-licenc az AMS licenckiszolgálóról, a Widevine-licenc pedig a castLabs licenckiszolgálóról származik. 
* A Media Player automatikusan eldönti, hogy melyik licencet szeretné lekérni az ügyfélplatform képességei alapján. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Hitelesítési jogkivonat létrehozása licenc megszerzéséhez

Mind a castLabs, mind az AMS támogatja a JWT (JSON Web Token) token formátumot, amely et licenc licenc engedélyezésére használják. 

### <a name="jwt-token-in-ams"></a>JWT token az AMS-ben

Az alábbi táblázat az AMS JWT-jogkivonatát ismerteti. 

| Kiállító | Kiállítói karakterlánc a kiválasztott Secure Token Service (STS) szolgáltatásból |
| --- | --- |
| Célközönség |Közönségkarakterlánc a használt STS-ből |
| Jogcímek |Egy sor követelések |
| Nem előtte |A jogkivonat érvényességének kezdete |
| Lejárat |A token érvényességének vége |
| Aláíró hitelesítő adatok |A PlayReady License Server, a castLabs License Server és az STS között megosztott kulcs lehet szimmetrikus vagy aszimmetrikus kulcs. |

### <a name="jwt-token-in-castlabs"></a>JWT token a castLabs-ben

Az alábbi táblázat a JWT-tokent ismerteti a castLabs ben. 

| Név | Leírás |
| --- | --- |
| optData |Az Önről információkat tartalmazó JSON-karakterlánc. |
| Crt |JSON-karakterlánc, amely az eszközről, annak licencadatairól és lejátszási jogairól tartalmaz információkat. |
| iat |Az aktuális dátum idő korszakban. |
| Jti |A token egyedi azonosítója (minden token csak egyszer használható a castLabs rendszerben). |

## <a name="sample-solution-setup"></a>Mintamegoldás beállítása

A [mintaoldat](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) két projektből áll:

* Konzolalkalmazás, amely segítségével drm-korlátozásokat állíthat be egy már bevitt eszköz, mind a PlayReady, mind a Widevine számára.
* A webalkalmazás, hogy a kioszt tokenek, ami lehet tekinteni, mint egy nagyon egyszerűsített változata STS.

A konzolalkalmazás használata:

1. Módosítsa az app.config fájlt az AMS-hitelesítő adatok, a castLabs hitelesítő adatok, az STS-konfiguráció és a megosztott kulcs beállításához.
2. Töltsön fel egy eszközt az AMS-be.
3. Az UUID beszerzése a feltöltött eszközből, és a Program.cs 32.
   
      var objIAsset = _context. Eszközök.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf"). FirstOrDefault();
4. Használjon egy AssetId elnevezésére az eszköz a castLabs rendszer (Line 44 a Program.cs fájl).
   
   Be kell állítani a CastId azonosítót a **castLabs**számára ; egyedi alfanumerikus karakterláncnak kell lennie.
5. Futtassa a programot.

A webalkalmazás (STS) használata:

1. Módosítsa a web.config-ot a castlabs kereskedőazonosító, az STS-konfiguráció és a megosztott kulcs beállítására.
2. Üzembe helyezése az Azure-webhelyeken.
3. Keresse meg a webhelyet.

## <a name="playing-back-a-video"></a>Videó lejátszása

A közös titkosítással (PlayReady és/vagy Widevine) titkosított videó lejátszásához használhatja az [Azure Media Player t.](https://aka.ms/azuremediaplayer) A konzolalkalmazás futtatásakor a tartalomkulcs-azonosító és a jegyzékfájl URL-címe visszhangzik.

1. Nyisson meg egy új lapot, és indítsa el az STS-t: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Nyissa meg az [Azure Media Player](https://aka.ms/azuremediaplayer)t.
3. Illessze be a streamelési URL-címbe.
4. Kattintson a **Speciális beállítások** jelölőnégyzetre.
5. A **Védelem** legördülő menüben válassza a PlayReady és/vagy a Widevine lehetőséget.
6. Illessze be az STS-ből kapott jogkivonatot a Token szövegmezőbe. 
   
   A castLab licenckiszolgálónak nincs szüksége a "Bearer=" előtagra a jogkivonat előtt. Ezért kérjük, távolítsa el, hogy mielőtt a token.
7. Frissítse a lejátszót.
8. A videónak le kell játszania.

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

