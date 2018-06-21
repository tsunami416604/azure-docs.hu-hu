---
title: Az Azure AD jelszó protection előzetes kiadásának
description: Gyenge jelszavakat bA az Azure AD jelszó protection előzetes kiadásának használatával a helyszíni Active Directoryban
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 0819a947229e633331253923654de56638a6c76a
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296112"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Előzetes verzió: Azure AD jelszavas védelmet a Windows Server Active Directory kényszerítése

|     |
| --- |
| Az Azure AD-jelszó protection és a tiltott egyénijelszó listájának az Azure Active Directory nyilvános előzetes verziójú funkciók. Az előzetes verziójú funkciók kapcsolatos további információkért lásd: [kiegészítő használati feltételek Microsoft Azure előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Az Azure AD-jelszóvédelem Azure Active Directory (Azure AD) javítása érdekében a szervezeten belüli jelszóházirendek technológiával nyilvános előzetes verziójában az új szolgáltatása. A helyszíni telepítéshez az Azure AD a jelszavas védelem is a globális és egyéni tiltani az Azure ad-ben tárolt jelszavak listája, és az azonos ellenőrzi a helyszíni Azure AD felhőalapú módosítás hajt végre.

Az Azure AD jelszóvédelem alkotó három szoftver-összetevő van:

* Az Azure AD-jelszó védelmi proxy szolgáltatás az aktuális Active Directory-erdő bármely tartományhoz csatlakoztatott számítógépen fut. Tartományvezérlők kérelmeket továbbítja az Azure AD, és visszaadja a választ az Azure AD vissza a tartományvezérlőre.
* Az Azure AD-jelszó DC védelmi ügynök szolgáltatás jelszó érvényesítése kérést fogad a DC-ügynök jelszó-szűrő dll, dolgozza fel őket a jelenlegi helyileg elérhető jelszóházirend használatával, és visszaadja az eredményt (pass\fail). Ez a szolgáltatás felelős a rendszeres időközönként (óránként) hívja az Azure AD jelszó védelmi proxy szolgáltatás új verziói a jelszó-házirend beolvasása. Az Azure AD-jelszó védelmi proxy szolgáltatás érkező vagy oda irányuló hívások kommunikációs TCP protokollon keresztül (távoli eljáráshívási) RPC-n keresztül kell kezelni. Lekérés, akkor új házirendek tárolódnak a sysvol mappát, ahol azok képes replikálni a többi tartományvezérlőn. A DC-ügynökszolgáltatás is figyeli a változásokat a sysvol mappa, abban az esetben, ha más tartományvezérlők írt új jelszóházirendek hiba, ha megfelelően legutóbbi házirend már elérhető a az Azure AD-jelszó védelmi proxy szolgáltatás-ellenőrzés kimarad.
* A tartományvezérlő ügynök jelszó-szűrő dll jelszó érvényesítése kérést fogad az operációs rendszer, majd továbbítja azokat az Azure AD jelszó DC védelmi ügynök szolgáltatás helyben fut a tartományvezérlő.

![Hogyan működnek együtt az Azure AD-jelszó védelmi összetevők](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

## <a name="requirements"></a>Követelmények

* Az összes olyan gép, ahol telepítve vannak az Azure AD jelszó védelmi összetevői beleértve tartományvezérlők futnia kell a Windows Server 2012 vagy újabb.
* Minden tartományban legalább egy tartományvezérlő és az Azure AD-jelszó védelmi proxy szolgáltatás tartalmazó legalább egy kiszolgáló között léteznie kell a hálózati kapcsolatot.
* Minden Active Directory-tartományban fut a tartományvezérlő szolgáltatás ügynökszoftver kell használnia elosztott fájlrendszer replikációs szolgáltatása a sysvol replikáció.
* Az Azure AD-jelszó védelmi proxy szolgáltatás regisztrálása az Azure AD egy globális rendszergazdai fiókját.
* Az erdő gyökértartományában Active Directory tartomány-rendszergazdai jogosultságokkal rendelkező fiók.

### <a name="license-requirements"></a>Licenckövetelmények

A globális tiltott jelszó lista előnyeit az Azure Active Directory (Azure AD) minden felhasználóra vonatkoznak.

Az egyéni tiltott jelszó lista Azure AD alapvető licencek igényel.

Az Azure AD a Windows Server Active Directory jelszavas védelem Azure AD Premium licenc szükséges. 

További licencelési információt, beleértve a költségek, található meg a [Azure Active Directory-hely árképzési](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="download"></a>Letöltés

Nincsenek a két szükséges telepítők, amely letölthető az Azure AD jelszavas védelmet a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=57071)

## <a name="answers-to-common-questions"></a>Gyakori kérdésekre adott válaszok

* Nincs internetkapcsolat szükséges a tartományvezérlőről. A gép (ek) az Azure AD-jelszó védelmi proxy szolgáltatás fut a csak internetes kapcsolatot igénylő gépek.
* Nincs hálózati portokat a tartományvezérlőkön.
* Nincs Active Directory-séma módosításokra szükség.
   * A szoftver a meglévő Active Directory-tárolót és serviceConnectionPoint sémaobjektumok használja.
* Nincs olyan minimális Active Directory-tartomány vagy erdő működési szintjét (DFL\FFL) követelmény.
* A szoftver hozzon létre vagy nem szükséges azokat a fiókokat az Active Directory-tartományok megvédi a.
* Növekményes telepítési azzal a kompromisszummal jár, hogy jelszót szabályzat csak érvényes ahol telepítve van-e a tartomány a tartományvezérlő ügynök esetén támogatott.
* Az Azure AD-jelszóvédelem nincs valós idejű alkalmazás házirendmotor. Házirend konfigurációs jelszómódosítás és az idő elér, és a rendszer kényszeríti az összes tartományvezérlő között eltelő idő késés előfordulhat.

## <a name="next-steps"></a>További lépések

[Azure AD jelszóvédelem üzembe helyezése](howto-password-ban-bad-on-premises.md)