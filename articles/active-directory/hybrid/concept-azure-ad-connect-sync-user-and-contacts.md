---
title: 'Azure AD Connect szinkronizálás: A felhasználók, csoportok és kapcsolattartók ismertetése | Microsoft dokumentumok'
description: Az Azure AD Connect szinkronizálásának felhasználói, csoportjai és névjegyei.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 661747754369c17ca98ae69d477e04124b6a2942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60245495"
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Azure AD Connect szinkronizálás: A felhasználók, csoportok és kapcsolattartók ismertetése
Számos különböző oka lehet annak, hogy több Active Directory-erdővel rendelkezik, és több különböző telepítési topológiája van. A gyakori modellek közé tartozik a fiók-erőforrás üzembe helyezése és a GAL sync'ed erdők egyesülés után & felvásárlás. De még akkor is, ha vannak tiszta modellek, hibrid modellek gyakoriak is. Az Azure AD Connect szinkronizálásalapértelmezett konfigurációja nem feltételez egy adott modellt, de attól függően, hogy a telepítési útmutatóban hogyan lett kiválasztva a felhasználó egyezése, különböző viselkedések figyelhetők meg.

Ebben a témakörben végigmegyünk, hogyan viselkedik az alapértelmezett konfiguráció bizonyos topológiákban. Végigmegyünk a konfiguráción, és a szinkronizálási szabályok szerkesztője használható a konfiguráció vizsgálatához.

Van néhány általános szabály a konfiguráció feltételezi:
* Függetlenül attól, hogy melyik sorrendben importáljuk a forrásból Aktív könyvtárak, a végeredmény mindig ugyanaz.
* Az aktív fiók mindig hozzájárul a bejelentkezési adatokhoz, például **a userPrincipalName** és **a sourceAnchor**.
* A letiltott fiók hozzájárul userPrincipalName és sourceAnchor, kivéve, ha egy csatolt postaláda, ha nincs aktív fiók található.
* A csatolt postaládával rendelkező fiókot soha nem fogja használni a userPrincipalName és sourceAnchor. Feltételezzük, hogy később egy aktív fiók található.
* Előfordulhat, hogy egy kapcsolattartó-objektum kapcsolattartóként vagy felhasználóként ki van építve az Azure AD-be. Nem igazán tudja, amíg az összes forrás Active Directory-erdő feldolgozása meg nem történt.

## <a name="groups"></a>Csoportok
Fontos tudnivalók a csoportok Active Directory és Azure AD szolgáltatásból történő szinkronizálásakor:

* Az Azure AD Connect kizárja a beépített biztonsági csoportokat a címtár-szinkronizálásból.

* Az Azure AD Connect nem támogatja [az elsődleges csoporttagságok](https://technet.microsoft.com/library/cc771489(v=ws.11).aspx) azure AD-vel való szinkronizálását.

* Az Azure AD Connect nem támogatja a dinamikus terjesztési csoport tagságok azure AD-vel való [szinkronizálását.](https://technet.microsoft.com/library/bb123722(v=exchg.160).aspx)

* Active Directory-csoport szinkronizálása az Azure AD-vel levelezést támogató csoportként:

    * Ha a csoport *proxyAddress* attribútuma üres, a *levelezési* attribútumnak értékkel kell rendelkeznie

    * Ha a csoport *proxyAddress* attribútuma nem üres, legalább egy SMTP-proxycímértéket kell tartalmaznia. Néhány példa:
    
      * Az Active Directory-csoport, amelynek proxyAddress attribútumértéke *{"X500:/0=contoso.com/ou=users/cn=testgroup"}* nem lesz mail-kompatibilis az Azure AD-ben. Nem rendelkezik SMTP-címmel.
      
      * Az Active Directory-csoport, amelynek proxyAddress *attribútumértékei\@{"X500:/0=contoso.com/ou=users/cn=testgroup","SMTP:johndoe contoso.com"}* lesz mail-kompatibilis az Azure AD-ben.
      
      * Az Active Directory-csoport, amelynek proxyAddress *attribútumértékei {"X500:/0=contoso.com/ou=users/cn=testgroup",\@"smtp:johndoe contoso.com"}* is mail-kompatibilis az Azure AD-ben.

## <a name="contacts"></a>Kapcsolatok
Az egyesülési & felvásárlás t követően gyakori, hogy egy másik erdőben lévő felhasználót képviselő kapcsolattartók vannak, ahol a GALSync-megoldás két vagy több Exchange-erdőt áthidal. Az összekötő területről mindig csatlakozik az összekötő objektum a metaverzumhoz a mail attribútum használatával. Ha már van egy névjegyobjektum vagy felhasználói objektum ugyanazzal a levelezési címmel, az objektumok összekapcsolódnak. Ez az **AD - Partnercsatlakozás**szabályban van beállítva. Van is egy szabály neve **In az AD - Contact Közös** egy attribútum folyama a metaverzum attribútum **sourceObjectType** az állandó **Kapcsolat**. Ez a szabály nagyon alacsony prioritású, így ha bármely felhasználói objektum ugyanahhoz a metaverzum objektumhoz csatlakozik, akkor az **AD – User Common** szabály hozzájárul a Felhasználó értékhez ehhez az attribútumhoz. Ezzel a szabállyal ez az attribútum a Kapcsolattartó értéket fogja elkönyvelve, ha egyetlen felhasználó sem csatlakozott, és a Felhasználó értéket, ha legalább egy felhasználómegtalálható.

Az Azure AD-nek való kiépítéshez a kimenő szabály **Out to AAD – Contact Join** létrehoz egy kapcsolattartó-objektumot, ha a metaverzum attribútum **sourceObjectType** beállítása **Kapcsolat**. Ha ez az attribútum **felhasználó,** akkor a Ki a **AAD - Felhasználói csatlakozás** szabály létrehoz egy felhasználói objektumot.
Lehetséges, hogy egy objektum előléptetése kapcsolattartóról felhasználóra, ha több forrás aktív könyvtárat importál és szinkronizál.

Például egy GALSync topológiában az első erdő importálásakor a második erdő minden tagjához találunk kapcsolattartó objektumokat. Ez új érintkezőobjektumokat hoz létre az AAD-összekötőben. Amikor később importáljuk és szinkronizáljuk a második erdőt, megtaláljuk a valódi felhasználókat, és csatlakozunk hozzájuk a meglévő metaverzum objektumokhoz. Ezután töröljük a névjegyobjektumot az AAD-ben, és helyette új felhasználói objektumot hozunk létre.

Ha olyan topológiával rendelkezik, amelyben a felhasználók kapcsolattartóként jelennek meg, győződjön meg arról, hogy a telepítési útmutatóban a levelezési attribútumfelhasználóival egyeznek. Ha másik lehetőséget választ, akkor rendelésfüggő konfigurációval fog rendelkezni. Az érintkező objektumok mindig csatlakoznak a mail attribútumhoz, de a felhasználói objektumok csak akkor csatlakoznak a mail attribútumhoz, ha ez a beállítás a telepítési útmutatóban van kiválasztva. Ekkor a metaverzumban két különböző objektum ot hozhat létre ugyanazzal a mail attribútummal, ha az ügyfélobjektumot a felhasználói objektum előtt importálta. Az Azure AD-be való exportálás során egy hiba jelenik meg. Ez a viselkedés szándékosan, és azt jelzi, rossz adatokat, vagy hogy a topológia nem megfelelően azonosították a telepítés során.

## <a name="disabled-accounts"></a>Letiltott fiókok
A letiltott fiókok szinkronizálása az Azure AD-vel is történik. A letiltott fiókok gyakoriak az Exchange erőforrásainak, például a konferenciatermek számára. Kivételt képeznek a csatolt postaládával rendelkező felhasználók; mint korábban említettük, ezek soha nem egy fiókot az Azure AD.As previously mentioned, these will never provision a account to Azure AD.

A feltételezés az, hogy ha egy letiltott felhasználói fiókot talál, majd később nem találunk egy másik aktív fiókot, és az objektum ki van építve az Azure AD-be a userPrincipalName és sourceAnchor található. Abban az esetben, ha egy másik aktív fiók csatlakozik ugyanahhoz a metaverzum objektumhoz, akkor a userPrincipalName és sourceAnchor lesz használva.

## <a name="changing-sourceanchor"></a>Forrásszerkesztő módosítása
Ha egy objektum exportálása az Azure AD-be, akkor már nem módosíthatja a sourceAnchor többé. Az objektum exportálása után a metaverzum attribútum **cloudSourceAnchor** van beállítva az Azure AD által elfogadott **sourceAnchor** érték. Ha **sourceAnchor** változik, és nem egyezik **cloudSourceAnchor**, a szabály **Out to AAD - User Join** fogja dobni a hiba **sourceAnchor attribútum megváltozott**. Ebben az esetben a konfigurációt vagy az adatokat ki kell javítani, hogy ugyanazt a sourceAnchor ismét jelen legyen a metaverzumban, mielőtt az objektum újra szinkronizálható lenne.

## <a name="additional-resources"></a>További források
* [Azure AD Connect Sync: Szinkronizálási beállítások testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)

