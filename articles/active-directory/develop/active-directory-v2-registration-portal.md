---
title: Alkalmazásregisztráció portál súgótémakörök |} A Microsoft Docs
description: A Microsoft app-regisztrálási portál különböző szolgáltatásaihoz leírása.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: celested
ms.reviewer: lenalepa
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b77b5185056329113ee1fd17fa3ed3f364380ca2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58095874"
---
# <a name="app-registration-reference"></a>Alkalmazásregisztráció-referencia
Ez a dokumentum kontextust biztosít a és a különféle funkcióinak leírása megtalálható a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/).

> [!NOTE]
> Már nem támogatjuk regisztrálása és a konvergens és az Azure AD-alkalmazások kezelése a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/) 2019. május indítása. Javasoljuk, hogy a meglévő alkalmazások kezeléséhez, és az új alkalmazások regisztrálását a [alkalmazásregisztrációk (előzetes verzió)](https://aka.ms/appregistrations) élmény az Azure Portalon.

## <a name="my-applications-or-converged-applications"></a>Saját alkalmazások vagy összevont alkalmazások
Ez a lista tartalmazza az összes alkalmazás regisztrálása az Azure AD v2.0-végpontra való használatra. Ezeket az alkalmazásokat a felhasználók személyes Microsoft-fiókok és az Azure Active Directoryból a munkahelyi vagy iskolai fiókokkal is tudnak. Az Azure AD v2.0-végpont kapcsolatos további információkért tekintse meg a [v2.0 áttekintése](active-directory-appmodel-v2-overview.md). Ezek az alkalmazások integrálása a Microsoft fiók hitelesítési végpontot is használható `https://login.live.com`.

## <a name="azure-ad-only-applications"></a>Csak Azure AD-t használó alkalmazások
Ez a lista tartalmazza az összes alkalmazás regisztrálása az Azure AD-1.0-s verziójú végpont való használatra. Ezeket az alkalmazásokat csak kell lehetővé teszi a felhasználók munkahelyi vagy iskolai fiókok az Azure Active Directoryból. Ez a lista tartalmazza az alkalmazásokat, amelyek használatával lett regisztrálva a **alkalmazásregisztrációk** felületét a [az Azure Portal](https://portal.azure.com).

## <a name="live-sdk-applications"></a>Live SDK-alkalmazások
Ez a lista tartalmazza az összes alkalmazás kizárólag a Microsoft-fiókkal való használatra regisztrálva. Nincsenek engedélyezve az Azure Active Directoryval történő használatra. Ez a érheti el olyan alkalmazásokat, amelyek az MSA fejlesztői portálja, a korábban már regisztrált `https://account.live.com/developers/applications`. Minden függvény, amely a korábban végrehajtott `https://account.live.com/developers/applications` most már az új portálon végrehajtható `https://apps.dev.microsoft.com`.

## <a name="application-secrets"></a>Alkalmazástitkok
Titkos alkalmazáskulcsok rendszer hitelesítő adatokat, amelyek lehetővé teszik az alkalmazásnak, hogy megbízható [ügyfél-hitelesítés](https://tools.ietf.org/html/rfc6749#section-2.3) az Azure ad-ben. Az OAuth és OpenID Connect, az alkalmazás titkos gyakran nevezik egy `client_secret`. A 2.0-s protokoll, minden olyan alkalmazás, amely fogad egy biztonsági jogkivonatot egy webes címezhető helyen (használatával egy `https` séma) segítségével azonosítja magát a biztonsági jogkivonat beváltása után az Azure AD-alkalmazás titkos kell használnia. Továbbá minden olyan natív ügyfél, amely megkapja a tokenek egy eszközön lesz tiltott egy alkalmazás titkos kulcs az ügyfél-hitelesítés végrehajtásához. Ez megnehezíti a nem biztonságos környezetben titkos kulcsok tárolására.

Minden alkalmazás két érvényes titkos alkalmazáskulcsok tartalmazhat egy adott időpontban. Két titkos kódok megőrzése révén az alkalmazás teljes környezetre rendszeres kulcsváltás képességével rendelkezik. Után az alkalmazás egy új titkos kulcsot a teljes migrálás, törölje a régi titkos kulcsot, és egy új beállítására.

Az alkalmazás regisztrációs portál jelenleg csak kétféle típusú titkos alkalmazáskulcsok használata engedélyezett. Választás **új jelszó készítése** állít elő, és a egy közös titkos kulcsot tárol a megfelelő adattár, amely az alkalmazásban is használhatja. Választás **hozzon létre új kulcspár** hoz létre az új nyilvános/titkos kulcspárt, amely letölthető, és az ügyfél-hitelesítéshez az Azure ad-hez használt. Választás **nyilvános kulcs feltöltése** lehetővé teszi, hogy a saját nyilvános/titkos kulcspár.
A nyilvános kulcsot tartalmazó tanúsítvány feltöltése szükségesek.

## <a name="profile"></a>Profil
A profil szakaszához az alkalmazás regisztrációs portál segítségével az alkalmazás bejelentkezési oldal testreszabható. Most módosíthatja a bejelentkezési oldal alkalmazás emblémája, használati szolgáltatás URL-CÍMÉT, és az adatvédelmi nyilatkozat URL-címe. Az emblémának áttetsző hátterű, 48x48 vagy 50x50 képpontos, legfeljebb 15 KB-os, GIF, PNG vagy JPEG formátumú képnek kell lennie. Próbálja ki az értékek módosítása, és az eredményül kapott bejelentkezési oldal megtekintése!

## <a name="live-sdk-support"></a>Live SDK-támogatás
Ha engedélyezi a "Live SDK-támogatás", bármely hoz létre a titkos alkalmazáskulcsok vehető át az Azure AD és a Microsoft Account adattárakban. Ez lehetővé teszi az alkalmazás közvetlen integráció a Microsoft Account szolgáltatás (login.live.com). Ha alkalmazás készítése Microsoft Account használatával közvetlenül (szemben az Azure AD v2.0-végpont használatával), győződjön meg arról, hogy engedélyezve van a Live SDK-támogatás.

Live SDK támogatja biztosítja, hogy az alkalmazás titkos csak írja be az Azure AD-adatok tárolására. Az Azure AD-adatokat tároló tartalmazza, amelyek lehetővé teszik, hogy bizonyos követelményeknek, például a FISMA-megfelelőségi szabályzat nagyvállalati szintű. Ha engedélyezi a Live SDK támogatását, az alkalmazás nem érhetik el néhány ezeknek a szabványoknak való megfelelést.

Ha csak egyre tervezi használni az Azure AD v2.0-végpont, biztonságosan letilthatja a Live SDK-támogatás.

