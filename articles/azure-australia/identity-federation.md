---
title: Identitás-összevonás az Azure Ausztráliában
description: Útmutató az identitás-összevonás konfigurálásához az ausztráliai régiókban az ausztráliai kormányzati szabályzatok, rendeletek és jogszabályok követelményeinek kielégítése érdekében.
author: galey801
ms.service: azure-australia
ms.topic: quickstart
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: e161e36ee7b403381b65f52a6f416be09025d0a2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570858"
---
# <a name="identity-federation-in-azure-australia"></a>Identitás-összevonás az Azure Ausztráliában

Az Identitáskezelés és a nyilvános felhőalapú ajánlatokkal való összevonás az egyik legfontosabb első lépés a felhő használatához. A Microsoft Azure Active Directory Service tárolja a felhasználói adatokat a Cloud Services elérésének engedélyezéséhez, és az egyéb Azure-szolgáltatások használatának előfeltétele.

Ez a cikk a Azure Active Directory megvalósításához, a felhasználók Active Directory tartományi szolgáltatások tartományból való szinkronizálásához és a biztonságos hitelesítés megvalósításához szükséges legfontosabb tervezési pontokat ismerteti. Konkrét hangsúly kerül az ausztrál Cyber Security Center Information Security Manual (ISM) és az Azure minősítési jelentéseinek javaslataira.

A Azure Active Directoryon belül tárolt információk besorolása a megtervezésével kapcsolatos döntéseket tájékoztatja. A következő részlet a [ASCs-Microsoft Azure](https://aka.ms/au-irap):

>**ASCs-tanúsítási jelentés – Microsoft Azure** A Azure Active Directory (Azure AD) Active Directory összevonási szolgáltatásokkal kell konfigurálni, ha a Commonwealth-entitások védett módon osztályozzák a Active Directory felhasználását és tartalmát. Míg Active Directory a nem besorolt terjesztési korlátozási jelölések (UDLM) besorolásban lévő adatok nem igénylik az összevonást, a nemzetközösségi entitások továbbra is végrehajthatják az összevonást a szolgáltatáson kívülről elérhetővé tett kockázatok enyhítéséhez Ausztrália.

Ennek megfelelően az itt ismertetett két fontos szempont az információk szinkronizálása, valamint a felhasználók hitelesítésének mechanizmusa.

## <a name="key-design-considerations"></a>A legfontosabb tervezési szempontok

### <a name="user-synchronisation"></a>Felhasználói szinkronizálás

Azure AD Connect telepítésekor több döntést kell meghoznia a szinkronizálni kívánt információkról. A Azure AD Connect Microsoft Identity Manageron alapul, és robusztus szolgáltatáskészlet-készletet biztosít [](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-best-practices-changing-default-configuration) az adatkönyvtárak közötti átalakításhoz.

A Microsoft Consulting Services szolgáltatással a meglévő Windows Server-Active Directory ADRAP kiértékelését végezheti el. A ADRAP segítséget nyújt a Azure Active Directoryval való szinkronizálás előtt kijavítani kívánt problémák meghatározásában. A Microsoft Premier szintű támogatás-szerződések általában tartalmazzák ezt a szolgáltatást.

A [IDFix eszköz](https://docs.microsoft.com/office365/enterprise/install-and-run-idfix) az Azure ad-vel való szinkronizálás előtt megkeresi a helyszíni Active Directory tartományt. A IDFix az első lépés a Azure AD Connect megvalósítása előtt. Bár a IDFix-vizsgálat nagy mennyiségű problémát képes azonosítani, sok ilyen probléma gyorsan feloldható parancsfájlokkal vagy a Azure AD Connect adatátalakítások használatával.

Az Azure AD használatához a felhasználóknak külsőleg irányítható legfelső szintű tartománnyal kell rendelkezniük a hitelesítés engedélyezéséhez. Ha a tartománynak nincs külsőleg irányítható UPN-utótagja, akkor az Active Directory-beli [alternatív bejelentkezési azonosítót](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname) kell beállítania a felhasználó levelezési attribútumához. A felhasználók ezután bejelentkezhetnek az Azure-szolgáltatásba az e-mail-címükkel, és nem a tartományi bejelentkezésük.

A felhasználói fiókok UPN-utótagja a PowerShell használatával is módosítható. nem várható következményekkel járhat más csatlakoztatott rendszerek esetében is, és a továbbiakban nem tekinthető az ajánlott eljárásnak.

Annak eldöntéséhez, hogy mely attribútumok legyenek szinkronizálva Azure Active Directoryre, legbiztonságosabb azt feltételezni, hogy minden attribútumra szükség van. Ritkán fordul elő, hogy egy könyvtár ténylegesen védett adatokat tartalmazzon, azonban ajánlott a naplózás végrehajtása. Ha a címtárban védett adatmennyiség található, mérje fel az attribútum kihagyásának vagy átalakításának következményeit. Hasznos útmutatóként felsoroljuk azokat az attribútumokat, amelyekhez Microsoft Cloud szolgáltatások [szükségesek](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized).

### <a name="authentication"></a>Authentication

Fontos megérteni a rendelkezésre álló lehetőségeket, valamint azt, hogy miként használhatók a végfelhasználók számára a biztonság fenntartása érdekében.
A Microsoft [három natív megoldást](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) kínál a felhasználók Azure Active Directory való hitelesítésére:

* Jelszó kivonatának szinkronizálása – a Active Directory tartományi szolgáltatások kivonatos jelszavait az Azure Active Directory Azure AD Connect szinkronizálja.
* [Átmenő hitelesítés – a](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) jelszavak Active Directory tartományi szolgáltatások belül maradnak. A felhasználók hitelesítése Active Directory tartományi szolgáltatások ügynökön keresztül történik. Az Azure AD-n belül nem találhatók jelszavak.
* [Összevont egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-whatis) – a Azure Active Directory összevonta Active Directory összevonási szolgáltatások (AD FS) használatával a bejelentkezés során az Azure irányítja a felhasználókat, hogy Active Directory összevonási szolgáltatások (AD FS) a hitelesítéshez. Az Azure AD-n belül nem találhatók jelszavak.

A jelszó-kivonat szinkronizálása olyan forgatókönyvekben használható, ahol a hivatalos: a bizalmas és az alábbi adatok tárolása a címtárban történik. A védett adatok tárolására szolgáló forgatókönyvek esetében a két fennmaradó lehetőség egyikét kell megkövetelni.

Mind a három lehetőség támogatja a [jelszavak](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)visszaírását, amelyet a [ASCs felhasználói útmutatója](https://aka.ms/au-irap) a letiltáshoz javasol. Azonban a szervezeteknek ki kell értékelniük a jelszó letiltásának kockázatát a visszaírási és az önkiszolgáló jelszó-visszaállítások használatának kevesebb támogatási erőfeszítést illetően.

#### <a name="pass-through-authentication-pta"></a>Átmenő hitelesítés (PTA)

Az átmenő hitelesítés a IRAP értékelésének befejezése után lett kiadva, ezért; egyénileg kell kiértékelni, hogy meghatározza, hogyan illeszkedik a megoldás a szervezet kockázati profiljához. Az átmenő hitelesítés előnyben részesített a Microsoft által a jobb biztonsági helyzet miatti összevonással szemben.

![Átmenő hitelesítés](media/pta1.png)

Az átmenő hitelesítés számos tervezési tényezőt jelent:

* Az átmenő hitelesítési ügynöknek képesnek kell lennie kimenő kapcsolatok létrehozására Microsoft Cloud szolgáltatásokhoz.
* Több ügynök telepítése annak biztosítására, hogy a szolgáltatás nagyon elérhető legyen. Ajánlott legalább három ügynököt üzembe helyezni, legfeljebb 12 ügynökkel.
* Az ajánlott eljárás az, hogy ne telepítse az ügynököt közvetlenül egy Active Directory-tartomány vezérlőre. Alapértelmezés szerint a Azure AD Connect áteresztő hitelesítéssel történő telepítésekor a rendszer telepíti az ügynököt az AD csatlakozási kiszolgálóra.
* Az átmenő hitelesítés alacsonyabb karbantartási lehetőség, mint Active Directory összevonási szolgáltatások (AD FS), mert nincs szükség dedikált kiszolgálói infrastruktúrára, tanúsítványkezelőre vagy bejövő tűzfalszabályok kezelésére.

#### <a name="active-directory-federation-services-adfs"></a>Active Directory összevonási szolgáltatások (AD FS) (ADFS)

Active Directory összevonási szolgáltatások (AD FS) szerepelt a IRAP-felmérésben, és jóváhagyva a védett környezetekben való használatra.

![Összevonás](media/federated-identity.png)

Active Directory összevonási szolgáltatások (AD FS) számos tervezési tényezőt figyelembe vesz:

* Az összevonási szolgáltatások megkövetelik, hogy az internetről vagy a minimális Microsoft szolgáltatási végpontokról érkező HTTPS-forgalomhoz hálózati behatolás szükséges.
* Az összevonási szolgáltatások PKI-t és tanúsítványokat használ, amelyek folyamatos felügyeletet és megújítást igényelnek.
* Az összevonási szolgáltatásokat dedikált kiszolgálókon kell üzembe helyezni, és a megfelelő hálózati infrastruktúrára van szükség ahhoz, hogy biztonságosan elérhető legyen külsőleg.

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

A többtényezős hitelesítéshez tartozó ISM szakasz a kockázati profil alapján a következő helyzetekben javasolja a megvalósítását:

* Általános jogú felhasználók hitelesítése
* Rendszerjogosultságú fiókok hitelesítése
* A felhasználók távoli hozzáférésének hitelesítése
* Kiemelt műveleteket végző felhasználók

A Azure Active Directory többtényezős hitelesítést biztosít, amely az összes vagy a felhasználók egy részhalmaza számára engedélyezhető (például csak a rendszerjogosultságú fiókok esetében). A Microsoft egy feltételes hozzáférés nevű megoldást is biztosít, amely részletesebben szabályozza a többtényezős hitelesítés használatát (például csak akkor, ha a felhasználók távoli IP-címtartományokből jelentkeznek be).

Az Azure multi-Factor Authentication a következő ISM elfogadható ellenőrzési formákat támogatja:

* Telefonhívás
* SMS-üzenet
* Microsoft Authenticator alkalmazás
* Támogatott hardveres tokenek

A Privileged Identity Management a Azure Active Directory egy összetevője használható a többtényezős hitelesítés használatára, ha a felhasználók a negyedik javaslat teljesítéséhez megemelik az engedélyeiket.

## <a name="next-steps"></a>További lépések

Tekintse át a következő cikket: [szerepköralapú hozzáférés-vezérlés és Privileged Identity Management](role-privileged.md).
