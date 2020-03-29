---
title: Alkalmazásregisztrációs portál hivatkozása | Azure
titleSuffix: Microsoft identity platform
description: A Microsoft alkalmazásregisztrációs portál szolgáltatásainak leírása.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: ryanwi
ms.reviewer: lenalepa
ms.custom: aaddev
ms.openlocfilehash: 26ebee446523c138569b9d5379c9a5e1b9e93e7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76698441"
---
# <a name="app-registration-reference"></a>Alkalmazásregisztráció-referencia

Ez a dokumentum az Azure Portalon található [alkalmazásregisztrációs](https://aka.ms/appregistrations) élményben található különböző funkciók környezetét és leírását tartalmazza.

## <a name="my-applications-or-converged-applications"></a>Saját alkalmazások vagy konvergens alkalmazások

Ez a lista tartalmazza a Microsoft identity platform (2.0) végpontjával való használatra regisztrált összes alkalmazást. Ezek az alkalmazások személyes Microsoft-fiókkal és munkahelyi/iskolai fiókokkal is bejelentkeznek az Azure Active Directoryból. Az identitásplatform végpontjáról a [2.0-s virtuális gép áttekintése](active-directory-appmodel-v2-overview.md)című témakörben olvashat bővebben. Ezek az alkalmazások a Microsoft-fiók hitelesítési végpontjával való integrációra is használhatók. `https://login.live.com`

## <a name="azure-ad-only-applications"></a>Csak Azure AD-alkalmazások

Ez a lista tartalmazza az Azure AD 1.0-s végponthoz való használatra regisztrált összes alkalmazást. Ezek az alkalmazások csak az Azure Active Directoryból munkahelyi/iskolai fiókkal rendelkező felhasználókat tudnak bejelentkezni. Ez a lista tartalmazza azokat az alkalmazásokat, amelyek az [Azure Portalon](https://portal.azure.com)az **alkalmazásregisztrációk használatával regisztráltak.**

## <a name="live-sdk-applications"></a>Élő SDK-alkalmazások

Ez a lista tartalmazza az összes olyan alkalmazást, amely kizárólag microsoftos fiókkal való használatra van regisztrálva. Nincsenek engedélyezve az Azure Active Directory használatához. Itt találja azokat az alkalmazásokat, amelyeket korábban `https://account.live.com/developers/applications`regisztráltak az MSA fejlesztői portálján a (. Az összes olyan funkció, amelykorábban korábban `https://account.live.com/developers/applications` elvégezhető, most már elvégezhető az [alkalmazásregisztrációkban.](https://aka.ms/appregistrations)

## <a name="application-secrets"></a>Alkalmazás titkai

Az alkalmazástitok olyan hitelesítő adatok, amelyek lehetővé teszik az alkalmazás számára, hogy megbízható [ügyfél-hitelesítést](https://tools.ietf.org/html/rfc6749#section-2.3) hajtson végre az Azure AD-vel. Az OAuth & openid connectben az alkalmazástitkos `client_secret`kulcsot gyakran nevezik . A v2.0 protokollban minden olyan alkalmazás, amely egy webcímezhető `https` helyen (séma használatával) biztonsági jogkivonatot kap, egy alkalmazástitkos kulcsot kell használnia, hogy azonosítsa magát az Azure AD-vel az adott biztonsági jogkivonat beváltásakor. Továbbá minden olyan natív ügyfél, amely jogkivonatokat fogad egy eszközön, tilos egy alkalmazástitkos kulcs használata az ügyfélhitelesítés végrehajtásához. Ez elriasztja a titkos kulcsok nem biztonságos környezetben való tárolását.

Minden alkalmazás tartalmazhat két érvényes alkalmazás titokban egy adott időpontban. Két titkos kulcs karbantartásával, akkor képes az alkalmazás teljes környezetében rendszeres kulcsváltás. Miután áttelepítette az alkalmazás teljes egészét egy új titkos titokba, törölheti a régi titkos kulcsot, és újat hozhat ki.

Jelenleg csak kétféle alkalmazástitok engedélyezett az alkalmazás regisztrációs portálján. Az **Új jelszó létrehozása** beállításával egy megosztott titkos kulcsot hoz létre és tárol a megfelelő adattárban, amelyet az alkalmazásban használhat. Az **Új kulcspár létrehozása** beállítás új nyilvános/titkos kulcspárt hoz létre, amely letölthető és használható az Azure AD ügyfélhitelesítéséhez. A **Nyilvános kulcs feltöltése** lehetőséget választva saját nyilvános/titkos kulcspárt használhat.
Nyilvános kulcsot tartalmazó tanúsítványfeltöltésre van szükség.

## <a name="profile"></a>Profil

Az alkalmazásregisztrációs portál profilszakasza az alkalmazás bejelentkezési lapjának testreszabására használható. Ekkor módosíthatja a bejelentkezési oldal alkalmazáslogóját, a szolgáltatási feltételek URL-címét és az adatvédelmi nyilatkozat URL-címét. Az emblémának átlátszó, 48 x 48 vagy 50 x 50 képpontos képnek kell lennie GIF-, PNG- vagy JPEG-fájlban, amely 15 KB vagy kisebb. Próbálja meg módosítani az értékeket, és tekintse meg az eredményül kapott bejelentkezési oldalt!

## <a name="live-sdk-support"></a>Live SDK-támogatás

Ha engedélyezi a "Live SDK-támogatás", minden alkalmazás titkos kulcsokat hoz létre az Azure AD és a Microsoft-fiók adattárak. Ez lehetővé teszi, hogy az alkalmazás közvetlenül integrálható a Microsoft Account szolgáltatással (login.live.com). Ha közvetlenül a Microsoft-fiók használatával szeretne alkalmazást készíteni (nem a v2.0-végpont használatával), győződjön meg arról, hogy a Live SDK-támogatás engedélyezve van.

A Live SDK-támogatás letiltása biztosítja, hogy az alkalmazás titkos kulcsot csak az Azure AD-adattárba íródik. Az Azure AD-adattár nagyvállalati szintű szabályozásokat tartalmaz, amelyek lehetővé teszik, hogy megfeleljen bizonyos szabványoknak, például a FISMA-megfelelőségnek. Ha engedélyezi a Live SDK-támogatást, előfordulhat, hogy az alkalmazás nem felel meg néhány szabványnak.

Ha csak a v2.0-végpontot tervezi használni, biztonságosan letilthatja a Live SDK-támogatást.
