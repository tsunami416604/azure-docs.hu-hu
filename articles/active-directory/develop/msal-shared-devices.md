---
title: Megosztott eszköz mód – áttekintés
titleSuffix: Microsoft identity platform | Azure
description: Ismerje meg a megosztott eszköz módot az eszközmegosztás engedélyezéséhez az első vonalban dolgozók számára.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 514782d62f117af5bfff4a5d2b3354c4e263eece
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550239"
---
# <a name="overview-of-shared-device-mode"></a>A megosztott eszköz mód áttekintése

A megosztott eszköz mód az Azure Active Directory egyik szolgáltatása, amely lehetővé teszi olyan alkalmazások készítését, amelyek támogatják az első vonalbeli dolgozókat, és engedélyezik a megosztott eszközmódot a rájuk telepített eszközökön.

> [!NOTE]
> Ez a funkció nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="what-are-firstline-workers"></a>Mik azok a Firstline Dolgozók?

Első vonali dolgozók kiskereskedelmi alkalmazottak, karbantartási és helyszíni ügynökök, egészségügyi személyzet, és más felhasználók, akik nem ülnek a számítógép előtt, vagy használja a vállalati e-mail együttműködés. A következő szakaszok bemutatják a First line Workers támogatásának szempontjait és kihívásait, majd bemutatják a Microsoft által nyújtott funkciókat, amelyek lehetővé teszik az alkalmazás használatát a szervezet első vonalbeli dolgozói számára.

### <a name="challenges-of-supporting-firstline-workers"></a>Az első vonalban dolgozók támogatásának kihívásai

A Firstline Worker-munkafolyamatok engedélyezése olyan kihívásokat tartalmaz, amelyeket általában nem a tipikus információs dolgozók jelentenek. Az ilyen kihívások közé tartozhat a magas fluktuáció és a szervezet alapvető termelékenységi eszközeinek kevésbé ismerete. Annak érdekében, hogy felhatalmazza a Firstline Munkavállalók, szervezetek elfogadása különböző stratégiákat. Vannak, akik egy bring-your-own-device (BYOD) stratégiát alkalmaznak, amelyben alkalmazottaik üzleti alkalmazásokat használnak a személyes telefonjukon, míg mások megosztott eszközöket, például iPadeket vagy Android-táblagépeket biztosítanak alkalmazottaiknak.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>Több felhasználó támogatása egy felhasználószámára tervezett eszközökön

Mivel az iOS vagy Android rendszert futtató mobileszközöket egyetlen felhasználó számára tervezték, a legtöbb alkalmazás optimalizálja a felhasználói élményt egyetlen felhasználó számára. Az optimalizált élmény része, hogy engedélyezd az alkalmazások közötti egyszeri bejelentkezést, és hogy a felhasználók bejelentkezve maradnak az eszközükön. Amikor egy felhasználó eltávolítja a fiókját egy alkalmazásból, az alkalmazás általában nem tekinti biztonsági vonatkozású eseménynek. Számos alkalmazás még a felhasználó hitelesítő adatait is megtartja a gyors bejelentkezéshez. Ezt még akkor is tapasztalhatta, amikor törölt egy alkalmazást a mobileszközéről, majd újratelepítette, csak azért, hogy felfedezze, hogy még be van jelentkezve.

### <a name="global-sign-in-and-sign-out-sso"></a>Globális bejelentkezés és kijelentkezés (SSO)

Annak érdekében, hogy a szervezet alkalmazottai használhassák az alkalmazásait az alkalmazottak által megosztott eszközök körében, a fejlesztőknek az ellenkező élményt kell engedélyeznie. Az alkalmazottaknak képesnek kell lenniük arra, hogy kiválasszanak egy eszközt a készletből, és egyetlen gesztust hajtsanak végre, hogy "az övék legyen" a műszakjuk idejére. A műszak végén egy másik gesztust kell tenniük, hogy globálisan kijelentkezzenek az eszközön, és minden személyes és vállalati adatukat eltávolítják, hogy visszaküldhessék az eszközkészletbe. Továbbá, ha egy alkalmazott elfelejt kijelentkezni, az eszköz automatikusan ki jelentkezik a műszak végén és/vagy egy inaktív időszak után.

Az Azure Active Directory engedélyezi ezeket a forgatókönyveket egy **megosztott eszköz mód**nevű szolgáltatással.

## <a name="introducing-shared-device-mode"></a>A megosztott eszköz mód bemutatása

Mint említettük, a megosztott eszköz mód az Azure Active Directory egyik szolgáltatása, amely lehetővé teszi a következőket:

* Első vonali dolgozókat támogató alkalmazások létrehozása
* Eszközök telepítése az első vonalban dolgozókra és a megosztott eszközmód bekapcsolása

### <a name="build-applications-that-support-firstline-workers"></a>Első vonali dolgozókat támogató alkalmazások létrehozása

A Microsoft Authentication Library (MSAL) és a [Microsoft Authenticator alkalmazás](../user-help/user-help-auth-app-overview.md) segítségével támogathatja az első vonali dolgozókat az alkalmazásokban a *megosztott eszközmódnak*nevezett eszközállapot engedélyezéséhez. Ha egy eszköz megosztott eszköz módban van, a Microsoft olyan adatokkal biztosítja az alkalmazást, amelyek lehetővé teszik az alkalmazás viselkedésének módosítását az eszközön lévő felhasználó állapota alapján, a felhasználói adatok védelme érdekében.

A támogatott funkciók a következők:

* **Jelentkezzen be egy felhasználói eszközre bármely** támogatott alkalmazáson keresztül.
* **Jelentkezzen ki egy felhasználói eszközt az egész felhasználóra** bármely támogatott alkalmazáson keresztül.
* **Az eszköz állapotának lekérdezése** annak megállapításához, hogy az alkalmazás megosztott eszköz módban lévő eszközön van-e.
* **Az eszköz eszközállapotának lekérdezése** az eszközön annak megállapításához, hogy az alkalmazás legutóbbi használata óta változott-e valami.

A megosztott eszközmód támogatása biztonsági fejlesztésnek és funkciófrissítésnek tekintendő az alkalmazáshoz, és segíthet növelni annak bevezetését olyan erősen szabályozott környezetekben, mint az egészségügy és a pénzügyek.

A felhasználók attól függenek, hogy az adataik nem szivárogtak-e ki egy másik felhasználónak. A Share Device Mode hasznos jeleket ad annak jelzésére, hogy az alkalmazás nak olyan változás történt, amelyet kezelnie kell. Az alkalmazás felelős a felhasználó állapotának ellenőrzéséért az eszközön minden alkalommal, amikor az alkalmazást használják, törölje az előző felhasználó adatait. Ez magában foglalja, ha újra betölti a háttérben a többfeladatos. A felhasználói módosítás esetén gondoskodnia kell arról, hogy az előző felhasználó adatai törlődjenek, és hogy az alkalmazásban megjelenő gyorsítótárazott adatok törlődjenek. Azt javasoljuk, hogy mindig végezzen alapos biztonsági ellenőrzési folyamatot, miután megosztott eszközmód-képességet adott az alkalmazáshoz.

A megosztott eszközmód támogatásához az alkalmazások módosításáról a cikk végén található [Következő lépések](#next-steps) című szakaszban olvashat részletesen.

### <a name="deploy-devices-to-firstline-workers-and-turn-on-shared-device-mode"></a>Eszközök telepítése az első vonalban dolgozókra és a megosztott eszközmód bekapcsolása

Miután az alkalmazások támogatják a megosztott eszköz módot, és tartalmazzák a szükséges adatokat és biztonsági módosításokat, hirdetheti őket, mint a Firstline Workers által használható.

A szervezet eszközrendszergazdái mobileszköz-felügyeleti (MDM) megoldással, például a Microsoft Intune-nal telepíthetik eszközeiket és alkalmazásaikat az üzleteikbe és munkahelyeikre. A létesítési folyamat része az eszköz megosztott eszközként való *megjelölése.* A rendszergazdák a [Microsoft Authenticator alkalmazás](../user-help/user-help-auth-app-overview.md) telepítésével és a megosztott eszközmód beállításával konfigurálják a megosztott eszköz módot a konfigurációs paramétereken keresztül. A lépések végrehajtása után a megosztott eszközmódot támogató összes alkalmazás a Microsoft Authenticator alkalmazást fogja használni a felhasználói állapot kezeléséhez, valamint az eszköz és a szervezet biztonsági szolgáltatásainak biztosításához.

## <a name="next-steps"></a>További lépések

Támogatjuk az iOS és Android platformokat a megosztott eszköz módban. Tekintse át az alábbi dokumentációt a platformhoz, hogy megkezdhesd az első vonaldolgozóinak támogatását az alkalmazásokban.

* [Megosztott eszköz mód támogatása iOS-hez](msal-ios-shared-devices.md)
* [Megosztott eszköz mód támogatása Androidon](msal-android-shared-devices.md)
