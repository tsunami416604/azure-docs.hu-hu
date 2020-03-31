---
title: Azure AD jelszavas védelem – Azure Active Directory
description: A gyenge jelszavak kitiltása a helyszíni Active Directoryban az Azure AD jelszavas védelmével
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b9d07099f8de996181948921330ef6744b302a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848646"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Jelszavas Azure AD-védelem kikényszerítése Windows Server Active Directoryhoz

Az Azure AD jelszavas védelem olyan funkció, amely javítja a jelszóházirendek egy szervezetben. A jelszavas védelem helyszíni telepítése az Azure AD-ben tárolt globális és egyéni tiltott jelszólistákat is használja. Ugyanazokat az ellenőrzéseket végzi a helyszínen, mint az Azure AD a felhőalapú módosításokesetében. Ezek az ellenőrzések a jelszómódosítások és a jelszó-visszaállítási forgatókönyvek során történnek.

## <a name="design-principles"></a>Tervezési alapelvek

Az Azure AD jelszavas védelme ta-

* A tartományvezérlőknek soha nem kell közvetlenül kommunikálniuk az internettel.
* A tartományvezérlőkön nem nyitnak meg új hálózati portokat.
* Nincs szükség Active Directory-sémamódosításra. A szoftver a meglévő Active **Directory-tárolót** és serviceConnectionPoint-sémaobjektumokat használja. **serviceConnectionPoint**
* Nincs szükség minimális Active Directory tartomány- vagy erdőműködési szintre (DFL/FFL).
* A szoftver nem hoz létre és nem igényel olyan fiókokat az Általa védett Active Directory-tartományokban, amelyet védelmez.
* A felhasználó egyértelmű szöveges jelszavai soha nem hagyják el a tartományvezérlőt, sem a jelszó-érvényesítési műveletek során, sem máskor.
* A szoftver nem függ az Azure AD egyéb szolgáltatásaitól; például az Azure AD jelszókivonat-szinkronizálás nem kapcsolódik, és nem szükséges az Azure AD jelszavas védelmének működéséhez.
* A növekményes telepítés támogatott, azonban a jelszóházirend csak akkor van érvényben, ha a tartományvezérlő-ügynök (DC-ügynök) telepítve van. További részleteket a következő témakörben talál.

## <a name="incremental-deployment"></a>Növekményes telepítés

Az Azure AD jelszavas védelme támogatja a növekményes üzembe helyezést az Active Directory-tartomány tartományvezérlői között, de fontos megérteni, hogy ez valójában mit jelent, és mik a kompromisszumok.

Az Azure AD jelszavas védelem tartományvezérlő-ügynök szoftver csak akkor tudja érvényesíteni a jelszavakat, ha telepítve van egy tartományvezérlőre, és csak az adott tartományvezérlőre küldött jelszómódosítások esetén. Nem lehet szabályozni, hogy a Windows ügyfélgépek mely tartományvezérlőket választják ki a felhasználói jelszó módosításának feldolgozásához. A konzisztens viselkedés és az univerzális jelszóvédelmi biztonsági kényszerítés biztosítása érdekében a tartományvezérlő-ügynök szoftverét a tartomány összes tartományvezérlőjére telepíteni kell.

Számos szervezet a teljes üzembe helyezés előtt gondosan tesztelni szeretné az Azure AD jelszavas védelmét a tartományvezérlők egy részhalmazán. Az Azure AD jelszavas védelem támogatja a részleges telepítést, azaz a tartományvezérlő-ügynök szoftver egy adott tartományvezérlőn aktívan érvényesíti a jelszavakat akkor is, ha a tartomány más tartományvezérlői nem rendelkeznek a tartományvezérlő-ügynök szoftver telepítve. Az ilyen típusú részleges telepítések NEM biztonságosak, és nem ajánlottak, kivéve tesztelési célokra.

## <a name="architectural-diagram"></a>Építészeti diagram

Fontos, hogy az alapul szolgáló tervezési és függvényfogalmakat, mielőtt az Azure AD jelszavas védelem központi telepítése egy helyszíni Active Directory környezetben. Az alábbi ábra bemutatja, hogyan működnek együtt a jelszavas védelem összetevői:

![Az Azure AD jelszavas védelmi összetevőinek együttműködése](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Az Azure AD password protection proxy szolgáltatás fut az aktuális Active Directory-erdő bármely tartományhoz csatlakozó gépen. Elsődleges célja a jelszóházirend letöltési kérelmek továbbítása a tartományvezérlőkről az Azure AD-be. Ezután visszaadja az Azure AD válaszait a tartományvezérlőnek.
* A tartományvezérlő-ügynök DLL jelszószűrője felhasználói jelszó-érvényesítési kérelmeket kap az operációs rendszertől. Továbbítja őket a tartományvezérlőn helyileg futó tartományvezérlő-ügynök szolgáltatásnak.
* A tartományvezérlő-ügynök jelszavas védelmi szolgáltatás a tartományvezérlő-ügynök jelszószűrőjének DLL-jétől kap jelszóérvényesítési kérelmeket. Feldolgozza őket az aktuális (helyileg elérhető) jelszóházirend használatával, és az eredményt adja vissza: *pass* or *fail*.

## <a name="how-password-protection-works"></a>A jelszóvédelem működése

Minden Azure AD Password Protection Proxy szolgáltatáspéldány hirdeti magát az erdő tartományvezérlői nek egy **serviceConnectionPoint-objektum** létrehozásával az Active Directoryban.

Minden tartományvezérlő-ügynök jelszavas védelemmel foglalkozó szolgáltatás létrehoz egy **serviceConnectionPoint-objektumot** is az Active Directoryban. Ez az objektum elsősorban jelentéskészítésre és diagnosztikára szolgál.

A DC-ügynök szolgáltatás felelős egy új jelszószabályzat letöltésének az Azure AD-ből történő letöltésének. Az első lépés egy Azure AD password protection proxy szolgáltatás megkeresése az erdő **proxyszolgáltatásConnectionPoint-objektumok** lekérdezésével. Ha egy elérhető proxyszolgáltatás található, a tartományvezérlő-ügynök jelszóházirend-letöltési kérelmet küld a proxyszolgáltatásnak. A proxyszolgáltatás viszont elküldi a kérelmet az Azure AD-nek. A proxyszolgáltatás ezután visszaadja a választ a tartományvezérlő-ügynök szolgáltatásnak.

Miután a TARTOMÁNYVEZÉRLŐ-ügynök szolgáltatás új jelszóházirendet kap az Azure AD-től, a szolgáltatás tárolja a szabályzatot egy dedikált mappában a tartomány *sysvol* mappamegosztás ának gyökerében. A tartományvezérlő-ügynök szolgáltatás is figyeli ezt a mappát abban az esetben, ha újabb házirendek replikálódnak a tartomány más tartományvezérlő-ügynök szolgáltatásaiból.

A DC-ügynök szolgáltatás mindig új házirendet kér a szolgáltatás indításakor. A tartományvezérlő-ügynök szolgáltatás indítása után óránként ellenőrzi az aktuális helyileg elérhető házirend korát. Ha a szabályzat egy óránál régebbi, a tartományvezérlő-ügynök új szabályzatot kér az Azure AD-től a proxyszolgáltatáson keresztül, ahogy azt korábban leírtuk. Ha az aktuális házirend nem régebbi egy óránál, a tartományvezérlő-ügynök továbbra is használja ezt a házirendet.

Amikor egy Azure AD jelszavas védelem jelszavas szabályzat letöltése, a szabályzat a bérlőre vonatkozik. Más szóval a jelszóházirendek mindig a Microsoft globális tiltott jelszó listájának és a bérlőnkénti egyéni tiltott jelszavak listájának kombinációja.

A tartományvezérlő-ügynök TCP-n keresztül iradt a proxyszolgáltatással. A proxyszolgáltatás a konfigurációtól függően dinamikus vagy statikus RPC-porton figyeli ezeket a hívásokat.

A tartományvezérlő-ügynök soha nem figyel a hálózaton elérhető porton.

A proxyszolgáltatás soha nem hívja meg a tartományvezérlő-ügynök szolgáltatást.

A proxyszolgáltatás állapot nélküli. Soha nem gyorsítótárazza a szabályzatokat vagy bármely más, az Azure-ból letöltött állapotot.

A tartományvezérlő-ügynök szolgáltatás mindig a legfrissebb, helyileg elérhető jelszóházirendet használja a felhasználó jelszavának kiértékeléséhez. Ha a helyi tartományvezérlőn nem érhető el jelszóházirend, a rendszer automatikusan elfogadja a jelszót. Ebben az esetben a rendszer egy eseményüzenetet naplóz, hogy figyelmeztesse a rendszergazdát.

Az Azure AD jelszavas védelem nem egy valós idejű szabályzatalkalmazás-motor. Késleltethető lehet a jelszóházirend-konfiguráció módosítása az Azure AD-ben, és a módosítás elérése és kényszerítése között az összes tartományvezérlőn.

Az Azure AD jelszavas védelem a meglévő Active Directory jelszóházirendek kiegészítéseként működik, nem pedig csereként. Ez magában foglalja a külső jelszószűrő minden olyan dll-jét, amely telepíthető. Az Active Directory használatához mindig szükség van arra, hogy a jelszó elfogadása előtt minden jelszó-érvényesítési összetevő egyetértsen.

## <a name="foresttenant-binding-for-password-protection"></a>Erdő/bérlő kötése jelszavas védelemhez

Az Azure AD jelszavas védelmének telepítése egy Active Directory-erdőben az erdő regisztrációját igényli az Azure AD-vel. Minden üzembe helyezett proxyszolgáltatásnak regisztrálnia kell az Azure AD-vel. Ezek az erdő- és proxyregisztrációk egy adott Azure AD-bérlőhöz vannak társítva, amelyet implicit módon a regisztráció során használt hitelesítő adatok azonosítanak.

Az Active Directory erdőt és az erdőn belüli összes telepített proxyszolgáltatást ugyanazzal a bérlővel kell regisztrálni. Nem támogatott, hogy egy Active Directory-erdő vagy bármely proxyszolgáltatás az erdőben regisztrált különböző Azure AD-bérlők. Az ilyen helytelenül konfigurált központi telepítés tünetei közé tartozik a jelszóházirendek letöltésének képtelensége.

## <a name="download"></a>Letöltés

Az Azure AD jelszavas védelméhez szükséges két ügynöktelepítő a [Microsoft letöltőközpontjából](https://www.microsoft.com/download/details.aspx?id=57071)érhető el.

## <a name="next-steps"></a>További lépések
[Azure AD jelszóvédelem üzembe helyezése](howto-password-ban-bad-on-premises-deploy.md)
