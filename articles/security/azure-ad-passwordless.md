---
title: Az Azure Active Directoryval jelszó nélküli világában megértése |} A Microsoft Docs
description: Az útmutató segítségével CEOs, informatikai vezetők, CISOs, vezető identitás Architects, nagyvállalati Rendszertervezőknek és biztonsági és az Azure Active Directory végrehajtás beállításának hitelesítési módszer választása felelős az informatikai döntéshozókat.
services: active-directory
keywords: beállításának, Azure ad
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 00f66b6010bead3de131095a47ba1e419d2511c0
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723440"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>Egy olyan világban, az Azure Active Directoryval jelszó nélkül

Eljött az idő érdekében a jelszavakat a kapcsolattal. Jelszavak eljött velünk a kapcsolatot a múltban, de a mai digitális munkahelyi azok váltak egy viszonylag egyszerű támadási felület a támadóknak. A támadók kedvelt jelszavakat, és nem nehéz, hogy miért meghatározásakor, amelyek leggyakrabban az elutasított jelszavak az Azure Active Directory (Azure AD) olyan feltételek, például év, hónap, az évszak, vagy egy helyi csoport például. Ezenkívül [kutatási segített](https://aka.ms/passwordguidance) , hogy jelszókezelést, például a hosszúsági követelményeknek, összetettségi követelményeknek és módosítás gyakoriságok hagyományos javaslatok gyűltek számos okból a kapcsolódó emberi jellegét.

Felhasználói fiókok biztonsága a gyakran használt támadások három típusú adathalászati és illetéktelen behatolás visszajátszani jelszó megfelelő,. Az Azure AD szolgáltatások, például a [intelligens zárolás](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout), [letiltott jelszavak](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises), és [jelszavas védelem](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) segít az ilyen típusú támadások ellen. Hasonlóképpen, végrehajtási [a multi-factor authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) (MFA), vagy a kétlépéses ellenőrzést, azzal, hogy egy második hitelesítési mód további biztonságot nyújt. Azonban hosszú távon, beállításának megoldás a legjobb megoldás a hitelesítés a legbiztonságosabb módszer annak biztosítására.

Ez a cikk a következő könnyebben értelmezhető és megvalósítható a Microsoft beállításának megoldásokat és súgó, az alábbi lehetőségek közül választhat foglalkozás kezdete:

* **A vállalati Windows Hello**. A Windows 10 Windows Hello for Business lecseréli jelszavak erős, kétfaktoros hitelesítés a számítógépek és mobileszközök felügyeletét. Ez a hitelesítési módszer új típusú a felhasználói hitelesítő adatok, amelyek egy eszközhöz kötődik, és a egy biometrikus adatokat vagy PIN-kódot használ.

* **Bejelentkezés beállításának a Microsoft Authenticatort használó**. A Microsoft Authenticator alkalmazás segítségével jelentkezzen be az Azure AD-fiókot jelszó használata nélkül. Hasonlóan a Windows hello for Business technológia, a Microsoft Authenticator-alapú hitelesítést használ ahhoz, hogy a felhasználói hitelesítő adatok, amelyek egy eszközhöz kötődik, és a egy biometrikus adatokat vagy PIN-kódot használ.

* **Biztonsági kulcsok FIDO2**. FIDO2 nyújt titkosítási bejelentkezési hitelesítő adatokat, amelyek egyediek legyenek minden webhely és a egy helyi eszközön, például a Windows Hello vagy külső biztonsági kulcsok vannak tárolva. Ezek a biztonsági kulcsok ellenálló adathalász, ellopják jelszavát, a kockázatát és visszajátszásos támadások. Felhasználói hitelesítés keresztül a biometria vagy PIN-kód kombinálva, a megoldás, két tényező ellenőrzési értekezlet modern biztonsági igényeinek.

## <a name="the-future-of-passwordless-authentication"></a>A jövőben beállításának hitelesítés

Ezek a napok, bankokhoz, hitelkártya cég, és más szervezetek és online szolgáltatások gyakran a fiók védelme érdekében úgy, hogy kétszer igazolhatja személyazonosságát: a jelszavát, majd ismét a telefonon, szöveges vagy egy alkalmazást egyszer. Amíg a multi-factor authentication szolgáltatás biztonsági tárgyalja a jelszavak megosztását, azt ellopták vagy kitalálni, nem tárgyalja a kényelmetlenségért tényezője próbál megjegyezze őket. Felhasználók és a szervezetek fókuszálhat a napjaink felhő korszakának beállításának hitelesítési módszereket, amelyek rendkívül biztonságosak *és* kényelmes.

![Kényelmi és biztonság](./media/azure-ad/azure-ad-pwdless-image1.png)

Windows Hello for üzleti, beállításának jelentkezzen be a Microsoft Authenticator és FIDO2 biztonsági kulcsok osztoznak egy egyszerű, gyakori architektúrát, amely biztosíthatja a felhasználók számára, amely rendkívül biztonságos és kényelmes használandó hitelesítési módszert. Mindhárom technológia nyilvános/titkos kulcsot kell egy helyi kézmozdulatot, mint például a biometrikus vagy a PIN-kód és a titkos kulcsok kötve egy eszközre, és biztonságosan tárolja, és soha nem osztották alapulnak.

## <a name="windows-hello-for-business"></a>Vállalati Windows Hello

A Windows 10 Windows Hello for Business lecseréli jelszavak erős, kétfaktoros hitelesítés a számítógépeken és eszközökön. A hitelesítés áll egy új alkalmazástípus felhasználói hitelesítő adatok, amelyek egy eszközhöz kötődik, és biometrikus kézmozdulatok vagy PIN-kód, amely lehetővé teszi a felhasználók számára az Azure AD-hitelesítést használ, valamint a helyszíni Active Directory. Egyszerűen aláíró eszköz használatával Windows Hello for Business sem ördöngösség. Vagy használhat egy PIN-kódot vagy biometrikus hitelesítési módok, például egy ujjlenyomattal vagy a videofelismerést, arc felismerése.

### <a name="windows-hello-for-business-scenarios"></a>Windows Hello for Business-forgatókönyvek

Windows Hello for Business ideális információkkal dolgozó szakemberek, akik rendelkeznek a saját kijelölt Windows PC. A biometrikai és hitelesítő adatokat közvetlenül a felhasználó Számítógépének, amely megakadályozza a hozzáférést a tulajdonos nem bárkitől vannak társítva. PKI-integrációval és beépített támogatást az egyszeri bejelentkezés (SSO), Windows Hello for Business egy egyszerű és kényelmes módszert biztosít zökkenőmentes eléréséhez a vállalati erőforrásokhoz a helyszínen és a felhőben.

### <a name="windows-hello-for-business-deployment-considerations"></a>Windows Hello for Business telepítésével kapcsolatos megfontolások

Windows Hello for Business egy olyan elosztott rendszer, amely több összetevőket használ, az eszköz regisztrálása, a kiépítés és a hitelesítés elvégzéséhez. Ezért telepítéséhez van szükség a megfelelő tervezés több csapatok a szervezeten belül. A Windows Hello for Business [kapacitástervezési útmutató](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) segítséget nyújt az üzleti üzembe helyezéséhez és a lehetőségeket kell figyelembe venni Windows hello típushoz döntéseket is használható.

Hibrid vagy a helyszíni üzemelő példánya esetében valószínű, hogy:

* Egy megfelelően csatlakoztatott, működő hálózati

* Internetelérés

* Vállalati üzembe helyezés során Windows Hello támogatja az MFA, multi-factor Authentication-kiszolgáló

* Megfelelő névfeloldást, külső és belső neve

* Az Active Directory és a egy megfelelő számú tartományvezérlő webhelyenként-hitelesítés támogatásához

* Active Directory tanúsítványszolgáltatás 2012 vagy újabb

* Windows 10 1903 verziójával futó egy vagy több munkaállomás típusú számítógépek

Minden típusú Windows Hello óta az üzleti központi telepítések vállalati által kiállított tanúsítványokat használnak a tartományvezérlők egy legfelső szintű megbízhatóság, célszerű a lejárt tanúsítványok automatikus megújítására [kiszolgáló és a felhasználó automatikus igénylés konfigurálása tanúsítványok](https://docs.microsoft.com/windows-server/networking/core-network-guide/cncg/server-certs/configure-server-certificate-autoenrollment). A helyszíni üzemelő példánya esetében az identitásszolgáltató a helyszíni kiszolgáló, a Windows Server Active Directory összevonási szolgáltatások (AD FS) szerepkört futtatja. Összevont rendszerek általában szükség van a kiszolgálók, a farm néven egy elosztott terhelésű tömbje. Ezen a farmon konfigurálva van egy belső hálózatot és a peremhálózati topológia hitelesítési kéréseket a magas rendelkezésre állásának biztosításához.

Amikor beállítja a csoport megkövetelő szabályzata Windows Hello for Business a munkahelyi, érdemes készítse elő a szervezet felhasználói elmagyarázza a Windows Hello használata. Például, amikor valaki új eszköz beállította, bekapcsolják választhat **szervezethez tartozik az eszköz** vagy **Ez a saját személyes eszköz**. Vállalati eszközök esetén az előbbi kell választaniuk. Felhasználók is megtudja, melyik kapcsolat lehetőséget kell választaniuk kell: **Az Azure AD JOIN** vagy **beállítása helyi fiókkal (később a tartományhoz való csatlakozás)** .

Szokás a felhasználók számára, akik hozzászoktak biometrikus Kézmozdulatok használatával történő hitelesítéshez nappal után nappal végül elfelejtette a PIN-kódjukban. A segélyszolgálat hívások elkerülése érdekében javasoljuk, hogy lehetővé teszi az új felhasználók biztosítson elfelejtett [jelszavak](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment) és [PIN-kódok](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset).

Számos módon, amelyről választhat a Windows Hello for Business telepítése során. Melyben számos lehetőséget biztosítja, hogy szinte minden szervezetnek telepíthet Windows Hello for Business. Vegye figyelembe a következő típusú központi telepítések támogatottak:

* [Hibrid Azure AD tartományhoz kulcs megbízhatósági üzembe helyezés](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [Hibrid Azure AD-csatlakoztatott megbízhatósági központi](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Az Azure AD Join egyszeri bejelentkezés üzembe helyezési útmutatók](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [A helyi kulcs megbízhatósági központi](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [A helyi tanúsítvány-megbízhatósági központi](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

Számos lehetőséget biztosít az üzembe helyezés jelenik meg az összetett lehetővé teszi. Azonban a legtöbb szervezet valószínűleg határozza meg, hogy azok már megvalósított, amelyen a Windows Hello for Business telepítési függ az infrastruktúra legnagyobb. Függetlenül attól fontos tisztában lenni azzal, hogy a Windows Hello for Business egy olyan elosztott rendszer és a megfelelő tervezést ajánlott.

Azt javasoljuk, hogy olvasási [a Windows Hello üzleti központi telepítésének tervezése](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) segítségével meghatározhatja az ajánlott üzemi megfelel bizonyos szervezete igényeinek. Ezt követően a tervezési, győződjön meg arról, alapján, tekintse meg a [Windows Hello for Business üzembe helyezési útmutató](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide) a meglévő környezetben a Windows Hello for Business sikeres telepítés érdekében.

### <a name="how-windows-hello-for-business-works"></a>Windows Hello for Business működése

#### <a name="user-sets-up-windows-hello-for-business"></a>Felhasználó állítja be Windows Hello for Business

Az első kétlépéses ellenőrzés után a felhasználó regisztráció során Windows Hello be van állítva a felhasználó eszközén, és a Windows kéri a felhasználót, hogy egy kézmozdulatot, amely lehet egy biometrikai, például egy ujjlenyomattal vagy a videofelismerést, arc felismerése, vagy egy PIN-kód beállítása. Beállítása után a felhasználó megadja a hitelesítési módok identitásukat. Windows használja fel Windows Hello hitelesítheti a felhasználókat.

A Windows 10-eszköz funkcióinak alapján, módosítania kell egy beépített biztonságos enklávé hardver platformmegbízhatósági modul (TPM), vagy a TPM-eszköz szoftveres néven is ismert. A TPM-eszköz tárolja a titkos kulcsot, mert az arcfelismerés, ujjlenyomat vagy zárolásának feloldásához PIN-kód szükséges. A biometrikai adataival nem hordozhatók a, és a külső eszközökkel vagy kiszolgálókkal soha nem érkezik. Nincs a támadó kedvezőtlenül befolyásolhatja a ellopására biometrika használatát, mert a Windows Hello csak biometrikus azonosító adatokat tárol az eszközön egyetlen pont.

> [!TIP]
> A Surface PIN-kódot úgy érzi, egy jelszót, de ténylegesen fokozott biztonság érdekében. Egy jelszó és a egy PIN-kódot egy fontos különbség, hogy a PIN-kód vannak kötve, az adott eszköz, amelyen be lett állítva. Személy, aki ellopja a jelszót a fiók bárhonnan be tud jelentkezni. Azonban ha azokat ellopják, a PIN-kód, a fizikai eszköz túl ellopására rendelkezik lenne! Továbbá mivel a PIN-kódot az eszköz helyi, azt nem továbbít bárhol nem lehet a továbbítás elfogásuk vagy ellopták egy kiszolgálóról.

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>Felhasználói használatával Windows Hello for Business bejelentkezések

Windows Hello for Business biometrika és PIN-kódok használata (nyilvános és titkos kulcs) aszimmetrikus titkosítási a hitelesítéshez. A hitelesítés során a titkosítást a hitelesítési folyamat biztosítja, hogy a man-in-the-middle (MiTM) támadások nem ellophatják az eredményül kapott biztonsági jogkivonat vagy összetevő és, más helyeiről visszajátszani TLS munkamenetkulcs van kötve.

Windows Hello for Business kényelmes bejelentkezési élményt nyújt, amely hitelesíti a felhasználót az Azure AD és az Active Directory-erőforrásokat. Az Azure AD-hez csatlakoztatott eszközök Azure-beli hitelesítésre bejelentkezés során, és igény szerint hitelesíthető legyen az Active Directory. Hibrid Azure Active Directoryhoz csatlakoztatott eszközökön az Active Directory-bejelentkezés során hitelesítsék magukat, és a háttérben az Azure Active Directory való hitelesítéshez.

![Tekintse meg a forrás lemezkép](./media/azure-ad/azure-ad-pwdless-image2.jpeg)

A következő lépések bemutatják az Azure Active Directory bejelentkezési hitelesítési.

![Windows 10-es zárolási képernyőn](./media/azure-ad/azure-ad-pwdless-image3.png)

1. Felhasználói bejelentkeznek használatával Windows biometrikai vagy PIN-kód kézmozdulat. A hitelesítési módok feloldja a Windows Hello for Business titkos kulcsot, és a felhőalapú hitelesítés biztonsági támogatási szolgáltatóhoz, a felhő-hozzáférési pont szolgáltató néven küldi el.

2. A felhő AP-szolgáltató egy egyszeri kéri az Azure Active Directoryból.

3. Az Azure AD adja vissza egy egyszeri, 5 percig érvényes.

4. A felhő-hozzáférési pont szolgáltató aláírja a egyszeri, a felhasználó titkos kulccsal, és visszaadja az aláírt egyszeri az Azure Active Directory.

5. Az Azure Active Directory érvényesíti az aláírt egyszeri, a felhasználó biztonságosan regisztrált nyilvános kulcs nonce aláírási használatával. Miután a visszaadott aláírt egyszeri ellenőrzi az aláírást, majd az Azure AD ellenőrzi. Miután ellenőrizte a egyszeri, Azure ad-ben, amely az eszköz átvitel kulcsával van titkosítva, és visszaadja a felhő-hozzáférési pont szolgáltató a munkamenetkulcs egy PRT hoz létre.

6. A felhő-hozzáférési pont szolgáltató a titkosított PRT kap a munkamenetkulcs. Az eszköz átviteli titkos kulccsal, a felhő-hozzáférési pont szolgáltató visszafejti a munkamenetkulcs, és a munkamenetkulcs, az eszköz TPM-eszköz használatával védi.

7. A felhő-hozzáférési pont szolgáltató választ küld a sikeres hitelesítés Windows ezt követően a felhasználó férhet hozzá a Windows, valamint a felhőalapú és helyszíni alkalmazások hitelesítése újra kellene (SSO).

A hitelesítési folyamat a Windows Hello for Business érintő egyéb forgatókönyvek alaposabban, lásd: [Windows Hello for Business és hitelesítési](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#Azure-AD-join-authentication-to-Active-Directory-using-a-Key).

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>Felhasználó által felügyelt a Windows Hello for Business hitelesítő adatok

A [Microsoft PIN reset szolgáltatások](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset) funkciója az Azure ad-ben, amely lehetővé teszi a felhasználók szükség esetén alaphelyzetbe állítani a PIN-kódjukban. A Microsoft Intune vagy egy kompatibilis MDM, Csoportházirend használata a rendszergazdák konfigurálhatják a Microsoft PIN reset szolgáltatás, amely lehetővé teszi, hogy a felhasználók visszaállíthassák az elfelejtett PIN-beállításokon keresztül vagy a zárolási képernyőn anélkül, hogy biztonságosan használandó Windows 10-eszközök újból elvégezni a regisztrálást.

Egyes esetekben a felhasználók rendelkeznek térhet vissza a jelszavak használatával. [Az önkiszolgáló jelszó-visszaállítási](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment) (SSPR) egy másik Azure AD-funkció, amely lehetővé teszi a felhasználók visszaállíthassák a jelszavukat anélkül, hogy lépjen kapcsolatba az informatikai részleg számára. A felhasználók regisztrálnia kell, vagy az önkiszolgáló jelszó-visszaállítást a szolgáltatás használata előtt regisztrálnia. Regisztráció során a felhasználó úgy dönt, a szervezet által engedélyezett egy vagy több hitelesítési módszereket. SSPR lehetővé teszi, hogy a felhasználók gyorsan első feloldva, és folytassa a munkát, függetlenül attól, hogy hol vannak, vagy a napi időpontot. Azáltal, hogy a felhasználók maguk feloldásának, a szervezet csökkentheti a nem hatékony idő és a jelszóval kapcsolatos problémák leggyakoribb magas támogatási költségek.

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>Bejelentkezés beállításának a Microsoft Authenticatort használó

Bejelentkezés beállításának a Microsoft Authenticatort használó egy másik beállításának megoldás, amely segítségével jelentkezzen be a telefonos bejelentkezés használata az Azure AD-fiókokat. Azáltal, hogy felhasználó által ismert információ és valami már rendelkezik, de a telefonos bejelentkezés lehetővé teszi a hagyja ki a jelszó beírása és az identitás-ellenőrzést hajt végre a mobileszköz az ujjlenyomatot, arc vagy PIN-kód használatával továbbra is kell igazolnia személyazonosságát.

### <a name="microsoft-authenticator-passwordless-scenarios"></a>A Microsoft Authenticator beállításának forgatókönyvek

A Microsoft Authenticator alkalmazás lehetővé teszi a felhasználóknak az identitásukat, és a munkahelyi vagy személyes fiókkal történő hitelesítést. Is használható bővítésével egy jelszót az egyszer használatos jelszót vagy leküldéses értesítés hozhassanak cserélje le a jelszót kell. Jelszó helyett felhasználónak meg kell erősítenie-azonosítót fog kérni a mobiltelefon ujjlenyomat vizsgálat, videofelismerést, arc vagy iris felismerése vagy PIN-kód használatával. Biztonságos-technológiára épülő hasonlóan mi Windows Hello használ, ez az eszköz kerül csomagokként egy egyszerű alkalmazást egy mobileszközön, ami egy kényelmes megoldás felhasználók számára. A Microsoft Authenticator alkalmazást Android és IOS rendszereken érhető el.

### <a name="microsoft-authenticator-deployment-considerations"></a>A Microsoft Authenticator telepítésével kapcsolatos megfontolások

TEENDŐ beállításának bejelentkezés az Azure ad a Microsoft Authenticator alkalmazás használatára vonatkozó Előfeltételek a következők:

* Az Azure multi-factor Authentication engedélyezve vannak a végfelhasználók számára

* Lehetővé teszi a felhasználók regisztrálhatják az eszközeiket a Microsoft Intune vagy harmadik fél mobileszköz-felügyelet (MDM) megoldás használatával

Ha ezek a követelmények teljesülnek-e, a rendszergazdák engedélyezése beállításának telefonnal bejelentkezni a bérlőn belüli használatával [Windows PowerShell.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) Miután a telefonos bejelentkezés engedélyezve van a bérlőn, a végfelhasználók választhatnak, hogy jelentkezzen be munkahelyi vagy iskolai fiókjával válassza a telefon használatával a **fiókok** az alkalmazást, majd válassza a képernyő **telefonos bejelentkezés engedélyezése** .

Ha egy rendszergazda engedélyezve van bejelentkezés beállításának, a végfelhasználók kell az alábbi követelményeknek:

* Az Azure multi-factor Authentication regisztrált

* Legújabb verzióját a Microsoft Authenticator telepített iOS 8.0-s vagy újabb rendszert futtató eszközök vagy az Android 6.0-s vagy újabb

* Munkahelyi vagy iskolai fiók hozzáadása az alkalmazáshoz a leküldéses értesítésekkel

Az első zárolva lehetőségeinek elkerülése érdekében a fiók, vagy hozza létre újra a fiókok, egy új eszköz kellene, javasoljuk, hogy a Microsoft Authenticator segítségével [biztonsági másolatot készíteni a fiók hitelesítő adataival](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-backup-recovery) a felhőbe. Biztonsági mentés, után használhatja az alkalmazás használatával állítsa helyre a információkat egy új eszközön, potenciálisan elkerülve az első zárolva ki vagy hozza létre újra a fiókok kellene.

Mivel a legtöbb felhasználó vannak bemutatásával csak jelszavak használatával hitelesíteni, fontos, hogy a szervezet szerezhetnek felhasználókkal ezt a folyamatot. Tájékoztatási csökkentheti annak a valószínűségét, hogy felhasználók hívja a segélyszolgálatot bármely [problémák](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#known-issues) való bejelentkezéssel a Microsoft Authenticator alkalmazással kapcsolatos.

> [!NOTE]
> Ez a megoldás egy lehetséges pontja akkor, ha a központi felhasználói helyen van, ahol nincs internetkapcsolat. FIDO2 biztonsági kulcsok és a Windows Hello for Business nem vonatkozik a korlátozások.

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>Hogyan beállításának jelentkezzen be a Microsoft Authenticator működése

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>Felhasználó beállította beállításának jelentkezzen be a Microsoft Authenticator

A Microsoft Authenticator alkalmazás segítségével beállításának megoldásként jelentkezzen be az Azure AD-fiókot, mielőtt a rendszergazda és a végfelhasználók számára lépéseket kell elvégezni.

Egy rendszergazda kell először [hitelesítő adatokat, az alkalmazás használatának engedélyezése](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) Windows PowerShell-lel a bérlőben. A rendszergazda is kell engedélyezni a végfelhasználók számára az Azure multi-factor Authentication (az Azure MFA) és konfigurálása a Microsoft Authenticator alkalmazás egyik a [ellenőrzési módszerek sorát](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings#verification-methods).

A végfelhasználók kell [töltse le és telepítse](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) a Microsoft Authenticator alkalmazás és [állítsa be a fiókjuk](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app) használata a Microsoft Authenticator alkalmazás egy, az ellenőrzési módszerek sorát.

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>Felhasználói bejelentkezés beállításának a Microsoft Authenticator használatával

A Microsoft Authenticator alkalmazás segítségével jelentkezzen be minden olyan Azure AD-fiókot jelszó használata nélkül. A Windows 10-es zárolási képernyőn nem tartalmazza a bejelentkezési lehetőségként a Microsoft Authenticator alkalmazást, miközben a felhasználók továbbra is megadja a felhasználónevét és majd leküldéses értesítés küldése a mobil eszközén jelenlétének ellenőrzése. Felhasználók ezek jelenléte erősítse meg a bejelentkezési képernyőn számnak megfelelő, majd a face vizsgálat, ujjlenyomat vagy PIN-kód feloldja a privát kulcsot, és a hitelesítés végrehajtásához. A többtényezős ellenőrzési metódus biztonságosabb, mint egy jelszó és kényelmesebb, mint egy jelszó és a egy kódot megadása.

![Authenticator-bejelentkezés](./media/azure-ad/azure-ad-pwdless-image4.png)

A Microsoft Authenticator használatával beállításának hitelesítési ugyanazt a alapszintű mintát követi, mivel Windows Hello for Business azonban egy kicsit bonyolultabb, mivel a felhasználónak szüksége van így, hogy az Azure AD a Microsoft Authenticator alkalmazás verziója alatt kell azonosítani használja.

![Authenticator-folyamat](./media/azure-ad/azure-ad-pwdless-image5.png)

1. A felhasználó megadja a felhasználóneve.

2. Az Azure AD észleli, hogy a felhasználó erős hitelesítő adatokat, és az erős hitelesítő adatokat a folyamat elindul.

3. Értesítést küld az alkalmazásnak via Apple Push Notification Service (APNS) az iOS-eszközökön, illetve keresztül Firebase Cloud Messaging (FCM) az Android-eszközökön.

4. A felhasználó a leküldéses értesítést kap, és megnyitja az alkalmazást.

5. Az alkalmazás meghívja az Azure ad-ben, és megkapja a koncepció jelenléti challenge és az egyszeri.

6. A felhasználó elvégzi a kihívás a biometrikus vagy a rögzítés feloldásához a titkos kulcs megadásával.

7. Az egyszeri titkos kulcsával aláírt és küld vissza az Azure ad-ben.

8. Azure ad-ben nyilvános és titkos kulcs ellenőrzése végzi, és a egy tokent ad vissza.

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>Felhasználó által felügyelt, a bejelentkezés beállításának a Microsoft Authenticator hitelesítő adatokkal

A [kombinált regisztrációs](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined), a felhasználók regisztrálása és az Azure multi-factor Authentication és az új jelszó önkiszolgáló kérésének előnyeinek kihasználása. Felhasználók regisztrálása és ezek a beállítások kezeléséhez az azok [saját profiloldalát](https://aka.ms/mysecurityinfo). SSPR egyrészt kombinált regisztrációs által támogatott hitelesítési módszerek és a műveletek.

## <a name="fido2-security-keys"></a>FIDO2 biztonsági kulcsok

FIDO2 FIDO Alliance standard legújabb verzióját, és két részből áll – a W3C webes hitelesítés (WebAuthN) standard és a megfelelő FIDO Alliance ügyfél-hitelesítő protokoll (CTAP2). A FIDO2 szabványok hardver-, mobil-és biometrika-alapú hitelesítők számos alkalmazásokat és webhelyeket, mobil- és asztali környezetekben az egyszerű hitelesítést, hogy a felhasználók engedélyezése.

A Microsoft és az iparági partnerekkel közösen dolgozik FIDO2 biztonsági eszközök a Windows Hello engedélyezése a megosztott eszközökön egyszerű és biztonságos hitelesítési. FIDO2 biztonsági kulcsok lehetővé teszik a hitelesítő adat, és biztonságosan hitelesíthetik egy [Azure ad-ben](https://aka.ms/azuread418)-csatlakoztatott Windows 10 rendszerű eszköz, amely a szervezet része.

WebAuthN határozza meg, amely lehetővé teszi a fejlesztési és az erős megvalósítását, a webes alkalmazások és szolgáltatások által beállításának hitelesítés egy API-t. A CTAP protokoll lehetővé teszi, hogy a külső eszközökkel, például WebAuthN dolgozhat és hitelesítők FIDO-kompatibilis biztonsági kulcsok. A webes hitelesítéssel felhasználók jelentkezhetnek be az arcfelismerés, ujjlenyomat, PIN-kód vagy hordozható FIDO2 biztonsági kulcsait, az online szolgáltatások kihasználva helyett a jelszavak erős nyilvánoskulcs-hitelesítő adatokat. Jelenleg WebAuthN támogatott a Microsoft Edge és a Chrome támogatása, és a Firefox szolgáltatás fejlesztés alatt áll.

Eszközök és a jogkivonatok, amelyek igazodnak FIDO2 WebAuthN és CTAP protokollok használata erős hitelesítési platformok közötti átjárhatóságról megoldásokkal kapcsolatos jelszavak használata nélkül. Microsoft-partnerek különböző biztonsági fő űrlap tényezők, például USB biztonsági kulcsok és NFC engedélyezve van az intelligens kártyák dolgozik.

### <a name="fido2-security-keys-scenarios"></a>FIDO2 biztonsági kulcsok forgatókönyvek

FIDO2 biztonsági kulcsok segítségével jelentkezzen be az Azure AD a biztonsági kulcs kiválasztása, hogy a Windows 10-es zárolási képernyőjén a hitelesítőadat-szolgáltatója. Egy felhasználónév és jelszó megadása nem kötelező, így ideális számítógépek megosztható több felhasználóval, aki az első sor adminisztratív munkatársak számára. Szerepelnek egy kiváló hitelesítési lehetőséget, ha a vállalati házirendek szabályozzák, hogy egy felhasználó hitelesítő adatainak eszközük fizikailag elkülönül kell lennie. Felhasználók saját FIDO2 biztonsági kulcs belül a Microsoft Edge böngésző használatával 1809 vagy újabb verziójú Windows 10 webhelyekhez való bejelentkezéshez is beállíthatja.

### <a name="fido2-security-keys-deployment-considerations"></a>FIDO2 biztonsági kulcsok üzembe helyezési szempontok

Rendszergazdák Azure AD-ben FIDO2 támogatásának engedélyezése és a funkció hozzárendelése a felhasználókhoz vagy csoportokhoz. Házirendek hogyan kulcsok felhasznált és korlátozásai – például engedélyezése vagy blokkolása egy meghatározott készletének hardveres biztonsági kulcsok konfigurálása is létrehozhatók. A végfelhasználók számára kulcsok fizikailag elérhetőnek kell lennie.

**Az Azure AD-bejelentkezés beállításának engedélyezéséhez szükséges követelményeket, és webhelyek FIDO2 biztonsági kulcsok használatával többek között a következők:**

* Azure AD

* Azure Multi-Factor Authentication

* Kombinált regisztrációs előzetes verzió

* Biztonsági kulcs előzetes FIDO2 kompatibilis FIDO2 biztonsági kulcs szükséges.

* Webes hitelesítés (WebAuthN) igényel a Microsoft Edge a Windows 10-es 1809 vagy újabb verzió

* Alapján Windows jelentkezzen be az Azure AD szükséges FIDO2 csatlakoztatott Windows 10-es 1809 vagy újabb verzióját (a legoptimálisabb 1903 vagy újabb verziójú Windows 10 esetén)

FIDO2 megfelelő méretformátumú USB, NFC és a Bluetooth-eszközök közé tartozik. Javasoljuk, hogy úgy dönt, hogy a saját igényeinek megfelelő óta egyes platformok méretformátumhoz és böngészők egyelőre nem FIDO2-kompatibilis.

Azt is javasoljuk, hogy minden egyes szervezet hozzon létre egy protokoll, a felhasználók számára, és hogy a rendszergazdák kell a biztonsági kulcsot kell elvesztése vagy ellopása esetén. Felhasználók jelentse az elveszett vagy ellopott kulcs, hogy a rendszergazdák vagy a felhasználó is törölje a biztonsági kulcsok a felhasználó profilját, és egy új beállítására.

### <a name="how-fido2-security-keys-works"></a>Hogyan működik a FIDO2 biztonsági kulcsok

#### <a name="user-sets-up-fido2-security-key"></a>Felhasználó beállította FIDO2 biztonsági kulcs

Bár a rendszergazdák [manuálisan kiépítése kulcsok](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable) és ossza ki őket a végfelhasználók számára, az üzembe helyezést, és engedélyezi a Windows 10-es zárolási képernyőn FIDO2 hitelesítőadat-szolgáltató támogatott keresztül [Intune](https://docs.microsoft.com/intune/windows-enrollment-methods). A rendszergazdák is kell használnia a [az Azure portal](https://portal.azure.com/) token hardvereszközök engedélyezéséhez beállításának hitelesítési módszerként.

Biztonsági kulcsok FIDO2 telepítése is szükséges, hogy a felhasználóknak regisztrálniuk a kulcsok használatával [kombinált regisztrációs](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined). A kombinált regisztrációs a felhasználók egyszer regisztrálni, és Azure multi-factor Authentication és az egyszeri bejelentkezés jelszó-visszaállítás (SSPR) előnyeit.

A hardvertoken jelölje alapértelmezett többtényezős hitelesítési módszerként, ajánlott egy további ellenőrzési lehetőséget válassza.

* A Microsoft Authenticator – értesítés

* Hitelesítő alkalmazás vagy a hardveres token – kód

* Telefonhívás

* Szöveges üzenet

#### <a name="user-using-fido2-security-key-for-sign-in"></a>Felhasználói bejelentkezések FIDO2 biztonsági kulcs használatával

FIDO2 között a beépített platform hitelesítők például a Windows Hello és biztonsági kulcsok feloldani a titkos kulcsot, és a egy nyilvános kulcs más engedélyezéséhez használja a hitelesítő és a nyilvános/titkos kulcsú meghatározó tényező absztrakciós réteget biztosít. amely használható azonosítójaként külső erőforrások hozzáféréséhez. FIDO2 biztonsági kulcsok vannak ellátva a saját beépített biztonságos enklávé, amely tárolja a titkos kulcsot, és megköveteli a biometrikus adatokat vagy PIN-kód zárolásának feloldásához. Hitelesítő adatok nem használható fel újra, játssza vissza, vagy a szolgáltatások között megosztott, és nem vonatkozik adathalász és a közbeékelődéses támadások kiküszöbölésében vagy a kiszolgáló kapcsolatos problémák esetén.

![FIDO2 bejelentkezési](./media/azure-ad/azure-ad-pwdless-image6.png)

FIDO2 biztonsági kulcsok használatával nyújt biztonságos hitelesítést, az űrlap tényező független. A biztonsági kulcs a hitelesítő adatokat tárolja, és a egy további második tényező, például egy ujjlenyomat-alapú (a biztonsági kulcs integrálva) vagy egy PIN-kódot kell megadni: a Windows bejelentkezési védeni kell. Microsoft-partnerek biztonsági kulcs méretformátumú különböző dolgozik. Néhány példa: USB biztonsági kulcsait, és NFC engedélyezve van az intelligens kártyák.

> [!NOTE]
> A biztonsági kulcsot meg kell valósítani, bizonyos funkciók és bővítmények megadott FIDO2 CTAP protokollal kell [Microsoft-kompatibilis](https://aka.ms/fido2securitykeys). Ezek a megoldások a Windows 10 és Azure Active Directoryval való kompatibilitás érdekében a Microsoft tesztelte.

![FIDO2 bejelentkezési folyamat](./media/azure-ad/azure-ad-pwdless-image9.png)

1. A felhasználó a FIDO2 eszköz rendkívüli a számítógépen.

2. Windows észleli a FIDO2 biztonsági kulcs.

3. Windows-hitelesítési kérelmet küld.

4. Az Azure AD egy egyszeri küldi vissza.

5. A felhasználó elvégzi a saját hitelesítési módok feloldásához a FIDO2 biztonsági kulcs biztonságos enklávé tárolja a titkos kulcsot.

6. A FIDO2 biztonsági kulcs az egyszeri aláírja a titkos kulccsal.

7. Az Azure AD az aláírt egyszeri PRT jogkivonatkérelem érkezik.

8. Az Azure AD az aláírt egyszeri FIDO2 nyilvános kulcsával ellenőrzi.

9. Az Azure AD a helyszíni erőforrásokhoz való hozzáférés engedélyezéséhez PRT adja vissza.

#### <a name="user-manages-their-fido2-security-key-credentials"></a>Felhasználó által felügyelt azok FIDO2 biztonsági kulcs hitelesítő adatai

A Microsoft Authenticator alkalmazás hasonlóan, a hitelesítőadat-kezelés FIDO2 biztonsági kulcsok kombinált regisztrációs végfelhasználói élmény támaszkodik.

## <a name="deciding-a-passwordless-method"></a>Annak eldöntése beállításának metódus

Válassza a három beállításának beállítások között attól függ, a vállalat biztonsági, platform és alkalmazásokra vonatkozó követelmények.

Íme néhány tényező, hogy a Microsoft jelszó nélküli technológia kiválasztásakor vegye figyelembe:

||**Vállalati Windows Hello**|**Bejelentkezés beállításának a Microsoft Authenticator alkalmazással**|**FIDO2 biztonsági kulcsok**|
|:-|:-|:-|:-|
|**Előfeltételeként**| A Windows 10-es, 1809 vagy újabb verzió<br>Azure Active Directory| A Microsoft Authenticator alkalmazás<br>Telefon (iOS és Android-eszközökön futó Android 6.0-s vagy újabb verzió.)|A Windows 10-es, 1809 vagy újabb verzió<br>Azure Active Directory|
|**mód**|Platform|Szoftver|Hardver|
|**Rendszerek és eszközök**|PC-egy beépített platformmegbízhatósági modul (TPM)<br>PIN-kódot vagy biometrikus adatokat felismerése |PIN-kódot vagy biometrikus adatokat felismerés, telefonon|FIDO2 biztonsági eszközök, amelyek kompatibilisek a Microsoft|
|**Felhasználói élmény**|Jelentkezzen be a Windows-eszközök PIN-kódot vagy biometrikus felismerése (videofelismerést, arc, iris vagy ujjlenyomat) használatával.<br>Windows Hello hitelesítési vannak kötve, az eszköz; a felhasználónak van szüksége, az eszköz és a egy bejelentkezési összetevő, például a vállalati erőforrások eléréséhez PIN-kódot vagy biometrikus tényező.|Jelentkezzen be a mobiltelefon az ujjlenyomat-vizsgálat, videofelismerést, arc vagy iris-felismerés, vagy PIN-kód.<br>Felhasználók bejelentkeznek a munkahelyi vagy személyes fiókkal a PC-s vagy mobiltelefonon.|Jelentkezzen be a FIDO2 biztonsági eszközzel (biometrikus, PIN-kód és NFC)<br>Az eszköz a szervezet vezérlők alapján eléréséhez és alapján PIN-kód, biometrikus eszközök, például USB biztonsági kulcsok és intelligens kártyák NFC engedélyezve van, kulcsok vagy wearables használatával hitelesíti a felhasználónak.|
|**Engedélyezett forgatókönyvek**| Windows-eszközök jelszó nélküli felhasználói élményt.<br>Az érvényes dedikált munkahelyi Számítógépéhez arra, hogy az egyszeri bejelentkezés az eszközök és alkalmazások számára.|Jelszó nélküli bárhol megoldás mobiltelefon használata.<br>A munkahelyi vagy személyes alkalmazások a weben elérése bármilyen eszközről alkalmazható.|Jelszó nélküli élmény biometrika, PIN-kód és az NFC-feldolgozókhoz.<br>A megosztott számítógépek, és ahol a mobiltelefon nem egy működőképes beállítás (például súgó ügyfélszolgálati munkatársak, a nyilvános teljes képernyős vagy a kórházi csapat meghajtóbetűjeleket) vonatkozik|

A következő táblázat segítségével válassza ki, melyik módszert támogatni fogja az igényei és a felhasználók.

|Személy|Forgatókönyv|Környezet|Beállításának technológia|
|:-|:-|:-|:-|
|**rendszergazda**|Biztonságos hozzáférés az eszköz felügyeleti feladatok|Hozzárendelt Windows 10 rendszerű eszköz|Windows Hello for Business és/vagy FIDO2 biztonsági kulcs|
|**rendszergazda**|Felügyeleti feladatok nem Windows eszközökön| Mobil- vagy a windows eszköz|Bejelentkezés beállításának a Microsoft Authenticator alkalmazással|
|**Infomunkás**|Irodai munka|Hozzárendelt Windows 10 rendszerű eszköz|Windows Hello for Business és/vagy FIDO2 biztonsági kulcs|
|**Infomunkás**|Irodai munka| Mobil- vagy a windows eszköz|Bejelentkezés beállításának a Microsoft Authenticator alkalmazással|
|**Tulajdonosa: Frontline feldolgozó**|A gyári, üzemek, kereskedelmi vagy adatok bejegyzés zárolandó|Megosztott Windows 10-eszközök|FIDO2 biztonsági kulcsok|

## <a name="getting-started"></a>Első lépések

Beállításának hitelesítés az a szakasz a jövőben és a egy biztonságosabb környezet elérési útját. Javasoljuk, hogy a szervezetek megkezdi a tervezés a változás, és csökkenti a jelszavak függősége. Első lépésként vegye figyelembe az alábbi célok:

* Lehetővé teheti a felhasználók az MFA + a Microsoft Authenticator alkalmazás + a feltételes hozzáférés.

* Bevezetés az Azure AD-jelszó védelmi + frissítési szabályzatok.

* Lehetővé teheti a felhasználók az SSPR-kombinált regisztrációs.

* Mobilitás központi telepítése a Microsoft Authenticator alkalmazást.

* Üzembe helyezése Windows Hello for Business (1903: naprakész).

* Felhasználók számára, akik nem használható a telefonok FIDO2 eszközök üzembe helyezésére.

* Ha lehetséges, tiltsa le a jelszóalapú hitelesítést.

Az ezen célok elérése érdekében javasoljuk, hogy a következő módon:

1. Engedélyezze az Azure Active Directoryval, teljes mértékben kihasználhatja az Azure MFA és a feltételes hozzáférési funkciókat.

2. Multi-factor authentication szolgáltatás engedélyezése további védelmet biztosíthat.

3. Engedélyezze a önkiszolgáló jelszó-visszaállítási, abban az esetben, ha a felhasználóknak kell térhet vissza a jelszó használatával.

4. Telepítse a Microsoft Authenticator telefonos bejelentkezés hozzáadott mobilitás.

5. Üzembe helyezése Windows Hello for Business az összes Windows 10-eszközre.

6. Készítse elő a FIDO2 biztonsági kulcsok.

> [!NOTE]
> Tekintse meg az Azure Active Directory [licencelési oldal](https://azure.microsoft.com/pricing/details/active-directory/) beállításának módszerek a licencelési követelményeket részleteiért.

## <a name="conclusion"></a>Összegzés

Az elmúlt néhány évben a Microsoft továbbra is elkötelezettségüket engedélyezése jelszó nélkül világában. A Windows 10-ben Microsoft bevezetett Windows Hello for Business, egy erős, kéttényezős hitelesítő adat, amely lehetővé teszi az egyszeri bejelentkezés az Azure Active Directory és az Active Directory védettek. Hasonlóan a Windows hello for Business technológia, a Microsoft Authenticator alkalmazás-alapú hitelesítést használ ahhoz, hogy a felhasználói hitelesítő adatok vannak kötve, a mobileszköz és a egy biometrikus adatokat vagy PIN-kódot használja. Most már FIDO2 biztonsági kulcsok lehetővé teszik a hitelesítő adat, és jelentkezzen be az Azure AD a biztonsági kulcs kiválasztása, hogy a Windows 10-es zárolási képernyőjén a hitelesítőadat-szolgáltatója. Ezek a megoldások beállításának mindhárom adathalász, jelszó megfelelő kockázatának csökkentése érdekében és visszajátszásos támadások, és biztosíthatja a felhasználók számára olyan rendkívül biztonságos, és kényelmes módon jelentkezik be, és bárhonnan elérheti az adatokat.

A modern a multi-factor authentication technológiák, például a biometria és eszközök széles körben elérhető-e a nyilvános kulcstitkosításhoz elfogadását közérthetően csökkentheti a vállalati identitás kockázati tevékenységét leginkább befolyásoló lépések egyike. A hosszú távú megközelítés a biztonságos hitelesítési beállításának is, és még kialakulófélben van. Adja meg új követelmények, szervezetek is felkészülhessenek azáltal, hogy a csomag beállításának technológiákkal való elindításához.

## <a name="next-steps"></a>További lépések

* Áttekintést [beállításának mi?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)
* [Az Azure ad-ben beállításának engedélyezése](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable)
