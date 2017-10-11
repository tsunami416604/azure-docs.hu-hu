---
title: "Alkalmazás-regisztrációs portál súgótémakörök |} Microsoft Docs"
description: "A Microsoft app-regisztrálási portál különféle funkcióinak leírása."
services: active-directory
documentationcenter: 
author: lnalepa
manager: mbaldwin
editor: 
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: lenalepa
ms.custom: aaddev
ms.openlocfilehash: c60499c425a7fd800f7ca9a5bac1fed5af73b801
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="app-registration-reference"></a>Alkalmazás regisztrálása referencia
Ez a dokumentum nyújt a környezet és a Microsoft App regisztrációs portálon található különböző szolgáltatások leírása [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).

## <a name="my-applications"></a>Saját alkalmazások
Ez a lista azokat az alkalmazásokat az Azure AD v2.0-végponttal való használatra regisztrálva.  Ezek az alkalmazások képesek legyenek jelentkezzen be Microsoft-fióknak és a munkahelyi vagy iskolai fiókok az Azure Active Directory mindkét személyes fiókkal rendelkező felhasználók.  Az Azure AD v2.0-végpontra vonatkozó további információkért tekintse meg a [v2.0 áttekintése](active-directory-appmodel-v2-overview.md).  Ezeket az alkalmazásokat is használható a Microsoft fiók hitelesítési végpont integrálása `https://login.live.com`.

## <a name="live-sdk-applications"></a>Élő SDK-alkalmazásokra
Ez a lista azokat az alkalmazásokat, kizárólag a Microsoft-fiókkal történő használathoz regisztrált.  Nincsenek engedélyezve az Azure Active Directoryval legyen használható.  Ez az olyan alkalmazásokat, amelyek volt korábban regisztrálva van a msa-t a fejlesztői portálhoz a talál `https://account.live.com/developers/applications`.  A korábban elvégzett összes függvények `https://account.live.com/developers/applications` most már az új portálon hajtható végre `https://apps.dev.microsoft.com`.  Ha a Microsoft-fiók alkalmazásokkal kapcsolatos további kérdése van, lépjen kapcsolatba velünk a következő címen.

## <a name="application-secrets"></a>Alkalmazás titkos kulcsok
Alkalmazás titkokat hitelesítő adatokat, amelyek lehetővé teszik az alkalmazás végrehajtásához megbízható [ügyfél-hitelesítés](http://tools.ietf.org/html/rfc6749#section-2.3) az Azure ad-val.  Az OAuth és az OpenID Connect, egy alkalmazás titkos kulcsok gyakran nevezik egy `client_secret`.  A bármilyen alkalmazás, amely megkapja a biztonsági jogkivonatot egy webes megcímezhető helyen a v2.0 protokoll (használatával egy `https` séma) kell használnia egy alkalmazás titkos kulcs az Azure AD-visszaváltás a biztonsági jogkivonat alapján azonosítja magát.  Továbbá minden natív ügyfél adott recieves jogkivonatok az eszköz lesz tiltott, ügyfél-hitelesítést végezni egy alkalmazás titkos kulcs segítségével nem biztonságos környezetben titkos kulcsok tárolására akadályozása érdekében.

Minden alkalmazás tartalmazhat két érvényes alkalmazás titkok álljon időben.  Két titkos kulcsok fölött, hogy a ablilty rendszeres kulcsváltás végrehajtani az alkalmazás teljes környezet között.  Miután áttelepítette a az alkalmazás számára az új titkos kulcsot a teljes, törölje a régi ügyfélkulcsot, és egy új kiépítéséhez.

Jelenleg csak kétféle típusú alkalmazás titkos kulcsok engedélyezettek az app-regisztrációs portálon.  Kiválasztása **új jelszó létrehozása** fogja létrehozni, és egy közös titkos kulcs tárolása a megfelelő tárolót, amellyel az alkalmazás.  Kiválasztása **létre új kulcspár** hoz létre egy új nyilvános és titkos kulcsból álló kulcspárt, amely letölthető, és az Azure AD-ügyfél-hitelesítéshez használt.

## <a name="profile"></a>Profil
A profil szakaszban az alkalmazás-regisztrálási portál segítségével testre szabhatja a bejelentkezési oldal az alkalmazáshoz.  Jelenleg a bejelentkezési lap alkalmazás embléma, a szolgáltatás URL-CÍMÉT, és az adatvédelmi nyilatkozatot feltételeket módosíthatja.  Az emblémának áttetsző hátterű, 48x48 vagy 50x50 képpontos, legfeljebb 15 KB-os, GIF, PNG vagy JPEG formátumú képnek kell lennie.  Próbálja értékeinek módosítása, és tekintse meg az eredményül kapott bejelentkezési oldal!

## <a name="live-sdk-support"></a>Élő SDK-támogatás
Ha engedélyezi a "Live SDK támogatása", bármely alkalmazás titkos kulcsok létrehozása lesznek üzembe helyezve a mindkét az Azure AD- és Microsoft Account adattárolókhoz.  Ez lehetővé teszi az alkalmazás integrálja közvetlenül a Microsoft Account szolgáltatással (login.live.com).  Ha szeretne hozza létre az alkalmazását, használja a Microsoft Account közvetlenül (figyelésekor az Azure AD v2.0-végponttól használatával), meg kell győződnie arról, hogy engedélyezve van-e az élő támogatása az SDK-ban.

Élő SDK-támogatás letiltása biztosítja, hogy az alkalmazás titkos kulcs csak kerül be az Azure AD-adatok tárolására.  Az Azure AD adatokat tároló tartalmazza, amelyek lehetővé teszik, hogy megfelelnek bizonyos szabványok, például a FISMA megfelelőségi vállalati szintű szabályzat.  Ha élő SDK támogatását engedélyezi, az alkalmazás nem érhetik el az egyes ezeknek a szabványoknak való megfelelés.

Ha csak tervezi használni az Azure AD v2.0-végpontra, Live SDK támogatási biztonságosan letilthatja.

