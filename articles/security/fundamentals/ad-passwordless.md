---
title: A jelszavak nélküli világ megismerése Azure Active Directoryokkal | Microsoft Docs
description: Ez az útmutató segítséget nyújt az ügyvezetők, a informatikai igazgatók, a CISOs, az identitás-építészek, a vállalati építészek, valamint a biztonsági és informatikai döntéshozók számára, akik a jelszóval nem rendelkező hitelesítési módszer kiválasztásához felelősek a Azure Active Directory megvalósításához.
keywords: jelszóval nem rendelkező, azuread
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: 38c850f167287fe5e1a444c8670e359c4d2b6b90
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934836"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>Jelszavak nélküli globális Azure Active Directory

Itt az ideje, hogy megszakítsa a kapcsolatait a jelszavakkal. A jelszavak a múltban jó számunkra, de a mai digitális munkahelyen a hackerek viszonylag egyszerű támadási vektora lett. A hackerek szeretik a jelszavakat, és nem nehéz megtekinteni, miért, ha úgy gondolja, hogy a leggyakrabban visszautasított jelszavak Azure Active Directory (Azure AD) olyan kifejezéseket tartalmaznak, mint az év, a hónap, a szezon vagy egy helyi sport csapat. Emellett a [kutatások azt mutatták](https://aka.ms/passwordguidance) , hogy a jelszó-kezelésre vonatkozó hagyományos javaslatok, például a hosszúsági követelmények, a bonyolultsági követelmények és a változási gyakoriságok az emberi természettel kapcsolatos különféle okok miatt kontraproduktív.

A felhasználói fiókok elszivárgására leggyakrabban használt három típusú támadás jelszavas spray, adathalászat és szerződésszegés. Az Azure AD-funkciók, például az [intelligens zárolás](../../active-directory/authentication/howto-password-smart-lockout.md), a [tiltott jelszavak](../../active-directory/authentication/concept-password-ban-bad-on-premises.md)és a [jelszavas védelem](../../active-directory/authentication/concept-password-ban-bad-on-premises.md) segíthetnek az ilyen típusú támadások elleni védelemben. Hasonlóképpen, a [többtényezős hitelesítés](../../active-directory/authentication/concept-mfa-howitworks.md) (MFA) és a kétlépéses ellenőrzés megvalósítása is lehetővé teszi a további biztonságot a hitelesítés második formáját megkövetelve. Hosszú távon azonban a legbiztonságosabb hitelesítési módszer biztosításához a legjobb megoldás a jelszó nélkül.

Ez a cikk a Microsoft jelszavas megoldásainak megismeréséhez és megvalósításához nyújt segítséget, és segít az alábbi lehetőségek közül:

* **Vállalati Windows Hello**. A Windows 10 rendszerben a Windows Hello for Business a jelszavakat erős, Kéttényezős hitelesítéssel helyettesíti a számítógépeken és mobileszközökön. Ez a hitelesítés olyan új típusú felhasználói hitelesítő adatokból áll, amely egy eszközhöz van kötve, és biometrikus vagy PIN-kódot használ.

* **A jelszó nélküli bejelentkezés Microsoft Authenticator**. A Microsoft Authenticator alkalmazás jelszó használata nélkül is használható az Azure AD-fiókba való bejelentkezéshez. A vállalati Windows Hello technológiához hasonlóan a Microsoft Authenticator a kulcs-alapú hitelesítés használatával engedélyezi az eszközhöz kötött felhasználói hitelesítő adatokat, és biometrikus vagy PIN-kódot használ.

* **FIDO2 biztonsági kulcsok**. A FIDO2 olyan titkosítási bejelentkezési hitelesítő adatokat biztosít, amelyek minden webhelyen egyediek, és helyi eszközön, például a Windows Hello vagy külső biztonsági kulcsokban vannak tárolva. Ezek a biztonsági kulcsok az adathalászat, a jelszó-lopás és a visszajátszás elleni támadások kockázatával szemben ellenállóak. A felhasználók hitelesítése a biometria vagy a PIN-kód használatával együtt a megoldás két tényező ellenőrzése a modern biztonsági igényeknek megfelelően.

## <a name="the-future-of-passwordless-authentication"></a>A jelszóval nem rendelkező hitelesítés jövője

Ezekben a napokban, bankokban, hitelkártya-vállalatokban és más szervezeteknél és online szolgáltatások gyakran védik a fiókját azáltal, hogy az Ön személyazonosságát kétszer kell ellenőriznie: egyszer a jelszóval, majd telefonon, szöveggel vagy alkalmazással. A többtényezős hitelesítés a megosztott, ellopott vagy kitalált jelszavak biztonsági hibáját is felhasználja, ezért nem oldja meg a megjegyezhető kellemetlenség tényezőt. A mai Felhőbeli korszakban lévő felhasználók és szervezetek a jelszóval nem rendelkező hitelesítési módszerek, amelyek nagyon biztonságosak *és* kényelmesek.

![Kényelem és biztonság](./media/ad-passwordless/azure-ad-pwdless-image1.png)

A vállalati Windows Hello, a jelszó nélküli bejelentkezés Microsoft Authenticator és a FIDO2 biztonsági kulcsainak mindegyike egy egyszerű, közös architektúrával rendelkezik, amely a felhasználók számára egy olyan hitelesítési módszert biztosít, amely igen biztonságos és kényelmes használatot tesz lehetővé. Mindhárom a nyilvános és a titkos kulcsokra épülő technológián alapul, szükség van egy helyi kézmozdulatra, például biometrikus vagy PIN-kódra, valamint az egyetlen eszközhöz kötött titkos kulcsokra, és biztonságosan tárolja és soha nem osztja meg őket.

## <a name="windows-hello-for-business"></a>Vállalati Windows Hello

A Windows 10 rendszerben a Windows Hello for Business a jelszavakat erős, Kéttényezős hitelesítéssel helyettesíti a számítógépeken és eszközökön. A hitelesítés olyan új típusú felhasználói hitelesítő adatokból áll, amely egy eszközhöz van kötve, és biometrikus kézmozdulatot vagy PIN-kódot használ, amely lehetővé teszi a felhasználóknak az Azure AD és a helyszíni Active Directory hitelesítését. Egyszerűen be kell jelentkeznie egy eszközre a vállalati Windows Hello használatával. Használjon PIN-kódot vagy biometrikus kézmozdulatot, például ujjlenyomatot vagy Arcfelismerés.

### <a name="windows-hello-for-business-scenarios"></a>Vállalati Windows Hello-forgatókönyvek

A vállalati Windows Hello ideális olyan információkkal dolgozó munkatársak számára, akik saját kijelölt Windows-számítógéppel rendelkeznek. A biometrikus és a hitelesítő adatok közvetlenül a felhasználó SZÁMÍTÓGÉPéhez vannak kötve, ami megakadályozza a hozzáférést a tulajdonostól eltérő személyek számára. A PKI-integrációval és az egyszeri bejelentkezés (SSO) beépített támogatásával a Windows Hello for Business egyszerű és kényelmes módszert kínál a vállalati erőforrások zökkenőmentes elérésére a helyszínen és a felhőben.

### <a name="windows-hello-for-business-deployment-considerations"></a>A vállalati Windows Hello üzembe helyezési szempontjai

A vállalati Windows Hello egy elosztott rendszer, amely számos összetevőt használ az eszközök regisztrálásához, üzembe helyezéséhez és hitelesítéséhez. Ezért az üzembe helyezéshez megfelelő tervezés szükséges a szervezeten belüli több csapat számára. A vállalati Windows Hello [tervezési útmutató](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) segítségével döntéseket hozhat a vállalati Windows Hello üzembe helyezésének típusáról és a szükséges lehetőségekről.

A vállalati Windows Hello telepítésekor számos lehetőség közül választhat. Több lehetőség is biztosít szinte minden szervezet számára a vállalati Windows Hello üzembe helyezését. Vegye figyelembe a következő támogatott típusú központi telepítéseket:

* [Hibrid Azure AD-hez csatlakoztatott kulcsfontosságú megbízható üzembe helyezés](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [Hibrid Azure AD-hez csatlakoztatott tanúsítvány-megbízhatóság üzembe helyezése](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Az Azure AD JOIN egyszeri bejelentkezési telepítési útmutatói](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [Helyszíni kulcs megbízhatóságának üzembe helyezése](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [Helyszíni tanúsítványok megbízhatóságának telepítése](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

Számos lehetőség megadása esetén a központi telepítés összetett módon jelenik meg. Azonban a legtöbb szervezet valószínűleg megállapítja, hogy már implementálták a legtöbb olyan infrastruktúrát, amelyre a vállalati Windows Hello-telepítés függ. Függetlenül attól, hogy a vállalati Windows Hello egy elosztott rendszer, és a megfelelő tervezés ajánlott.

Javasoljuk, hogy olvassa el a [vállalati Windows Hello üzembe helyezésének](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) megtervezése című cikk segítségét, amely segítséget nyújt az adott szervezet számára legmegfelelőbb üzembe helyezési modell eldöntésében. Ezt követően a tervezéstől függően tekintse meg a [vállalati Windows Hello telepítési útmutatóját](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide) , amely a Windows Hello for Business sikeres üzembe helyezését biztosítja a meglévő környezetben.

### <a name="how-windows-hello-for-business-works"></a>A vállalati Windows Hello működése

#### <a name="user-sets-up-windows-hello-for-business"></a>A felhasználó beállítja a vállalati Windows Hello-t

A felhasználó a regisztráció során végzett kezdeti kétlépéses ellenőrzése után a Windows Hello beállítása a felhasználó eszközén, a Windows pedig arra kéri a felhasználót, hogy állítson be egy kézmozdulatot, amely lehet biometrikus, például ujjlenyomat vagy Arcfelismerés vagy PIN-kód. A beállítás után a felhasználó a kézmozdulattal ellenőrizheti az identitását. A Windows ezután a Windows Hello használatával hitelesíti a felhasználókat.

A Windows 10-es eszköz képességei alapján a beépített biztonságos enklávé, a hardveres platformmegbízhatósági modul (TPM), vagy egy szoftveres TPM is rendelkezésre áll. A TPM tárolja a titkos kulcsot, amelyhez az arc, az ujjlenyomat vagy a PIN-kód szükséges a zárolás feloldásához. A biometrikus adatok nem barangolnak, és a rendszer soha nem küldi el azokat külső eszközökre vagy kiszolgálókra. Egyetlen gyűjtési pont sem veszélyeztetheti a biometrikus adatok ellopását, mivel a Windows Hello csak biometrikus azonosító adatokat tárol az eszközön.

> [!TIP]
> A felületen a PIN-kód úgy érzi magát, mint egy jelszót, de valójában biztonságosabb. A jelszó és a PIN-kód közötti fontos különbség, hogy a PIN-kód ahhoz a konkrét eszközhöz kötődik, amelyen be lett állítva. Ha valaki ellopja a jelszavát, bárhonnan bejelentkezhet a fiókjába. Ha azonban ellopják a PIN-kódját, a fizikai eszközt is ellopni kell. Továbbá, mivel a PIN-kód helyi az eszközön, nem kerül sehova, így nem lehet elolvasni az átvitel során, vagy a kiszolgáló ellopja őket.

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>A Windows Hello for businesst használó felhasználó a bejelentkezéshez

A vállalati Windows Hello biometria és PIN-kódok aszimmetrikus (nyilvános/titkos kulcs) titkosítást használnak a hitelesítéshez. A hitelesítés során a titkosítás a TLS-munkamenet kulcsaként van társítva, amely biztosítja a hitelesítési folyamatot, hogy egy MiTM támadás ne lopja el az eredményül kapott biztonsági jogkivonatot vagy összetevőt, és más helyekről is visszajátszható legyen.

A vállalati Windows Hello kényelmes bejelentkezési élményt nyújt, amely hitelesíti a felhasználót az Azure AD-ben, és Active Directory erőforrásokat. Az Azure AD-hez csatlakoztatott eszközök a bejelentkezéskor hitelesítik magukat az Azure-ban, és igény szerint hitelesítik Active Directory. A hibrid Azure Active Directory csatlakoztatott eszközök hitelesítése Active Directory a bejelentkezés során, és a háttérben való hitelesítés Azure Active Directory.

![Tekintse meg a forrás képét](./media/ad-passwordless/azure-ad-pwdless-image2.jpeg)

A következő lépések bemutatják a bejelentkezési hitelesítés Azure Active Directory.

![Windows 10 zárolási képernyő](./media/ad-passwordless/azure-ad-pwdless-image3.png)

1. A felhasználó biometrikus vagy PIN-kód típusú kézmozdulattal jelentkezik be a Windowsba. A kézmozdulat feloldja a vállalati Windows Hello-kulcs zárolását, és elküldjük a Cloud Authentication biztonsági támogatási szolgáltatónak, amelyet a Felhőbeli AP-szolgáltatónak nevezünk.

2. A Felhőbeli AP-szolgáltató egy alkalomra kér Azure Active Directoryból.

3. Az Azure AD egy egyszeres, 5 perces időként érvényes értéket ad vissza.

4. A Felhőbeli AP-szolgáltató aláírja az időpontot a felhasználó titkos kulcsa alapján, és visszaadja az aláírt alkalmi lehetőséget a Azure Active Directory.

5. Azure Active Directory a felhasználó biztonságosan regisztrált nyilvános kulcsával érvényesíti az aláírt egyszeres bejelentkezést az egyszeres aláírással. Az aláírás ellenőrzése után az Azure AD ezt követően érvényesíti a visszaadott, aláírt időpontot. Az egyszeres ellenőrzés után az Azure AD létrehoz egy PRT-t, amely az eszköz átviteli kulcsára van titkosítva, és visszaadja a Felhőbeli AP-szolgáltatónak.

6. A Felhőbeli AP-szolgáltató fogadja a titkosított PRT-t a munkamenet-kulccsal. Az eszköz privát átviteli kulcsának használatával a Cloud AP Provider visszafejti a munkamenetkulcsot, és megvédi a munkamenetkulcsot az eszköz TPM-je használatával.

7. A Felhőbeli AP-szolgáltató sikeres hitelesítési választ ad vissza a Windows rendszernek, amely után a felhasználó a Windows, valamint a Felhőbeli és a helyszíni alkalmazások számára is elérhetővé válik anélkül, hogy újra kellene hitelesítenie (SSO).

A vállalati Windows Hello szolgáltatással kapcsolatos egyéb helyzetekben a hitelesítési folyamat mélyebb megjelenítését lásd: [a vállalati Windows Hello és a hitelesítés](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#azure-ad-join-authentication-to-active-directory-using-a-key).

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>A felhasználó kezeli a vállalati Windows Hello-beli hitelesítő adatait

A [Microsoft PIN-kód-visszaállítási szolgáltatása](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset) az Azure ad egyik funkciója, amely lehetővé teszi a felhasználók számára, hogy szükség esetén visszaállítsák a PIN-kódját. A csoportházirend, Microsoft Intune vagy egy kompatibilis MDM használatával a rendszergazdák úgy konfigurálhatják a Windows 10-es eszközöket, hogy biztonságosan használják a Microsoft PIN-kód-átállítási szolgáltatását, amely lehetővé teszi, hogy a felhasználók az elfelejtett PIN-kódot a beállítások vagy a zárolási képernyő felett, anélkül, hogy újbóli regisztráció.

Időnként a felhasználóknak vissza kell térniük a jelszavak használatára. Önkiszolgáló [jelszó-visszaállítás](../../active-directory/authentication/howto-sspr-deployment.md) A (SSPR) egy másik Azure AD-szolgáltatás, amely lehetővé teszi a felhasználók számára a jelszavuk visszaállítását anélkül, hogy kapcsolatba kell lépnie az informatikai részleggel. A felhasználónak regisztrálnia kell az önkiszolgáló jelszó-visszaállításhoz, vagy regisztrálnia kell a szolgáltatást a szolgáltatás használata előtt. A regisztráció során a felhasználó egy vagy több, a szervezete által engedélyezett hitelesítési módszert választ. A SSPR lehetővé teszi, hogy a felhasználók gyorsan feloldják a blokkolást, és a munka helyétől függetlenül is működjenek. Azáltal, hogy a felhasználók letiltják magukat, a szervezet csökkentheti a nem produktív időt és a magas támogatási költségeket a leggyakoribb jelszóval kapcsolatos problémákhoz.

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>Jelszó nélküli bejelentkezés Microsoft Authenticator

A jelszó nélküli bejelentkezés Microsoft Authenticator egy másik jelszó nélküli megoldás, amellyel bejelentkezhet az Azure AD-fiókba telefonos bejelentkezéssel. Az identitást továbbra is igazolnia kell, ha megadta a kívánt adatokat, és a telefonos bejelentkezés lehetővé teszi, hogy kihagyja a jelszavát, és elvégezze az összes személyazonosság-ellenőrzést a mobileszközön az ujjlenyomat, az arc vagy a PIN-kód használatával.

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Microsoft Authenticator jelszóval nem rendelkező forgatókönyvek

A Microsoft Authenticator alkalmazás lehetővé teszi, hogy a felhasználók igazolják személyazonosságát, és hitelesítsék a munkájukat vagy személyes fiókjaikat. A jelszót egyszeri jelszóval vagy leküldéses értesítéssel is kiegészítheti, vagy a jelszó szükségességét teljes egészében le is lehet cserélni. A jelszó használata helyett a felhasználók az ujjlenyomat-vizsgálat, az arc-és az írisz-felismerés, vagy a PIN-kód használatával igazolják személyazonosságát a mobiltelefonnal. A Windows Hello által használt biztonságos technológiához hasonlóan ez az eszköz egy egyszerű alkalmazásba van csomagolva, amely a felhasználók számára kényelmes lehetőséget biztosít. Az Microsoft Authenticator alkalmazás Android és iOS rendszerhez érhető el.

### <a name="microsoft-authenticator-deployment-considerations"></a>Microsoft Authenticator üzembe helyezési megfontolások

A Microsoft Authenticator alkalmazás az Azure AD-be való jelszó nélküli bejelentkezéshez való használatának előfeltételei közé tartozik a következők:

* A végfelhasználók engedélyezve vannak az Azure multi-Factor Authentication szolgáltatáshoz

* Javasoljuk, hogy a felhasználók Microsoft Intune vagy egy külső mobileszköz-kezelési (MDM) megoldás használatával regisztrálják eszközeiket az alkalmazás üzembe helyezésének megkönnyítéséhez

Feltételezve, hogy ezek a követelmények teljesülnek, a rendszergazdák a [Windows PowerShell](../../active-directory/authentication/howto-authentication-passwordless-phone.md) használatával engedélyezhetik a jelszó nélküli telefonos bejelentkezést a bérlőn. Ha a telefonos bejelentkezés engedélyezve van a bérlőben, a végfelhasználók a saját telefonjára is bejelentkezhetnek, ha kiválasztja a munkahelyi vagy iskolai fiókját az alkalmazás **fiókok** képernyőjén, majd kiválasztja a **telefonos bejelentkezés engedélyezése**lehetőséget.

Ha egy rendszergazda engedélyezte a jelszó nélküli bejelentkezést, a végfelhasználóknak meg kell felelniük az alábbi követelményeknek:

* Regisztrálva az Azure multi-Factor Authentication szolgáltatásban

* Az iOS 8,0-es vagy újabb verzióját futtató eszközökön telepített Microsoft Authenticator legújabb verziója, vagy Android 6,0 vagy újabb verzió

* Munkahelyi vagy iskolai fiók, amely leküldéses értesítésekkel bővült az alkalmazáshoz

A fiók hitelesítő adatainak a felhőbe történő [biztonsági mentéséhez](../../active-directory/user-help/user-help-auth-app-backup-recovery.md) a Microsoft Authenticator segítségével elkerülheti a fiókból való kizárni kívánt adatokat, vagy a fiókokat újra létre kell hozni egy új eszközön. A biztonsági mentést követően az alkalmazással helyreállíthatja az adatokat egy új eszközön, így elkerülhető a zárolás vagy a fiókok ismételt létrehozása.

Mivel a legtöbb felhasználó megszokta, hogy csak a jelszavakat használja a hitelesítéshez, fontos, hogy a szervezet a folyamattal kapcsolatban tájékoztassa a felhasználókat. A tájékoztató csökkenti annak a valószínűségét, hogy a felhasználók a Microsoft Authenticator [](../../active-directory/authentication/howto-authentication-passwordless-phone.md) alkalmazással való bejelentkezéshez kapcsolódó esetleges problémákra meghívja az ügyfélszolgálatot.

> [!NOTE]
> A megoldás lehetséges meghibásodási pontja, amikor egy barangoló felhasználó olyan helyen található, ahol nincs internetkapcsolat. A FIDO2 biztonsági kulcsainak és a vállalati Windows Hello szolgáltatásnak nem ugyanaz a korlátozása.

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>A jelszó nélküli bejelentkezés Microsoft Authenticator működése

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>A felhasználó jelszó nélküli bejelentkezést állít be Microsoft Authenticator

Mielőtt a Microsoft Authenticator alkalmazás használható legyen jelszó nélküli megoldásként az Azure AD-fiókba való bejelentkezéshez, a lépéseket a rendszergazdának és a végfelhasználóknak is el kell végrehajtaniuk.

Először is a rendszergazdának engedélyeznie kell [az alkalmazás használatát hitelesítő adatként](../../active-directory/authentication/howto-authentication-passwordless-phone.md) a bérlőn a Windows PowerShell használatával. A rendszergazdának engedélyeznie kell a végfelhasználók számára az Azure multi-Factor Authentication (Azure MFA) használatát, és konfigurálnia kell az Microsoft Authenticator alkalmazást az egyik [ellenőrzési módszerként](../../active-directory/authentication/howto-mfa-mfasettings.md#verification-methods).

A végfelhasználóknak le kell [tölteniük és telepíteniük](../../active-directory/user-help/user-help-auth-app-download-install.md) kell a Microsoft Authenticator alkalmazást, és [be kell állítania](../../active-directory/user-help/security-info-setup-auth-app.md) a fiókját, hogy a Microsoft Authenticator alkalmazást használják az ellenőrzési módszerek egyikének.

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>Jelszó nélküli bejelentkezéshez Microsoft Authenticator használó felhasználó

A Microsoft Authenticator alkalmazás jelszó használata nélkül is bejelentkezhet bármely Azure AD-fiókba. Amíg a Windows 10-es zárolási képernyője nem tartalmazza a Microsoft Authenticator alkalmazást bejelentkezési lehetőségként, a felhasználók továbbra is megadhatják a felhasználónevet, majd leküldéses értesítést kaphatnak a mobileszközön a jelenlét ellenőrzéséhez. A felhasználók megerősítik a jelenlétüket a bejelentkezési képernyőn megadott számnak megfelelően, majd egy Arcfelismerés, ujjlenyomat vagy PIN-kód megadásával a titkos kulcs zárolásának feloldásához és a hitelesítés befejezéséhez. Ez a multi-Factor ellenőrzési módszer biztonságosabb, mint a jelszó, és kényelmesebb, mint a jelszó és a kód beírása.

![Hitelesítő bejelentkezés](./media/ad-passwordless/azure-ad-pwdless-image4.png)

Microsoft Authenticator a jelszóval nem rendelkező hitelesítés a vállalati Windows Hello szolgáltatással azonos alapszintű, de valamivel bonyolultabb, mivel a felhasználó azonosítására van szükség, hogy az Azure AD megtalálja a Microsoft Authenticator alkalmazás verziószámát használt.

![Hitelesítő folyamat](./media/ad-passwordless/azure-ad-pwdless-image5.png)

1. A felhasználó beírja a felhasználónevét.

2. Az Azure AD észleli, hogy a felhasználó erős hitelesítő adatokkal rendelkezik, és elindítja a hitelesítő adatok erős folyamatát.

3. Az értesítést Apple Push Notification Service (APNS) iOS-eszközökön, vagy az Android-eszközökön a Firebase Cloud Messaging (FCM) szolgáltatáson keresztül küldi el a rendszer.

4. A felhasználó megkapja a leküldéses értesítést, és megnyitja az alkalmazást.

5. Az alkalmazás meghívja az Azure AD-t, és befogadja a jelenléti igazolást.

6. A felhasználó a titkos kulcs feloldásához a biometrikus vagy PIN-kód megadásával befejezi a kihívást.

7. A rendszer aláírja a titkos kulcsot, és visszaküldi az Azure AD-nek.

8. Az Azure AD nyilvános/titkos kulcs érvényesítését hajtja végre, és visszaadja a tokent.

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>A felhasználó Microsoft Authenticator hitelesítő adataival kezelheti a jelszó nélküli bejelentkezést

A [kombinált regisztrációval](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md)a felhasználók regisztrálhatják és igénybe vehetik az Azure multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítás előnyeit. A felhasználók regisztrálják és kezelhetik ezeket a beállításokat a [saját profil lapon](https://aka.ms/mysecurityinfo)való navigálással. A SSPR engedélyezése mellett a kombinált regisztráció több hitelesítési módszert és műveletet is támogat.

## <a name="fido2-security-keys"></a>FIDO2 biztonsági kulcsok

A FIDO2 a következő két összetevőből áll: a W3c web Authentication (WebAuthN) standard és a hozzá tartozó, az ügyfél és a hitelesítő kapcsolat protokollja (CTAP2). A FIDO2-szabványok lehetővé teszik a felhasználók számára, hogy hardveres, mobil-és biometriai hitelesítő adatokat használjanak a mobil-és asztali környezetekben található számos alkalmazáshoz és webhelyhez való egyszerű hitelesítéshez.

A Microsoft és az iparág partnerei együttműködtek a Windows Hello FIDO2 biztonsági eszközeivel, így könnyen és biztonságosan lehet a megosztott eszközökön engedélyezni a hitelesítést. A FIDO2 biztonsági kulcsai lehetővé teszik a hitelesítő adatok biztonságos elvégzését, és a szervezete részét képező [Azure ad](https://aka.ms/azuread418)-hez csatlakoztatott Windows 10-es eszközök biztonságos hitelesítését.

A WebAuthN olyan API-t határoz meg, amely lehetővé teszi a webalkalmazások és szolgáltatások által nyújtott erős, jelszó nélküli hitelesítés fejlesztését és megvalósítását. A CTAP protokoll lehetővé teszi, hogy a külső eszközök, például a WebAuthN és a hitelesítő adataikkal működjenek együtt. A webes hitelesítéssel a felhasználók bejelentkezhetnek a online szolgáltatásokba az arc-, ujjlenyomat-, PIN-vagy hordozható FIDO2, és a jelszavak helyett erős nyilvános kulcsú hitelesítő adatokat is kihasználhatnak. Jelenleg a WebAuthN támogatott a Microsoft Edge-ben, és a Chrome és a Firefox támogatása fejlesztés alatt áll.

A FIDO2, WebAuthN és CTAP protokolloknak megfelelő eszközök és tokenek lehetővé teszi az erős hitelesítés többplatformos megoldását jelszavak használata nélkül. A Microsoft-partnerek különböző biztonsági kulcsokra vonatkozó tényezőket, például USB-biztonsági kulcsokat és NFC-kompatibilis intelligens kártyákat dolgoznak fel.

### <a name="fido2-security-keys-scenarios"></a>FIDO2 biztonsági kulcsok forgatókönyvei

A FIDO2 biztonsági kulcsainak használatával bejelentkezhet az Azure AD-be, ha a biztonsági kulcsot a Windows 10 zárolási képernyőjén a hitelesítő adatok szolgáltatója választja. Nincs szükség felhasználónévre vagy jelszóra, ami ideális megoldást kínál az első olyan munkavégzők számára, akik több felhasználó között osztják meg a számítógépeket. Emellett kiváló hitelesítési lehetőség is, ha a vállalati házirendek azt írják elő, hogy a felhasználó hitelesítő adatai fizikailag el legyenek különítve az eszköztől. A felhasználók a Windows 10 1809-es vagy újabb verziójában is dönthetnek úgy is, hogy a Microsoft Edge böngészőben a FIDO2 biztonsági kulcson belül bejelentkeznek a webhelyekre.

### <a name="fido2-security-keys-deployment-considerations"></a>FIDO2 biztonsági kulcsok telepítési szempontjai

A rendszergazdák engedélyezhetik a FIDO2 támogatását az Azure AD-ben, és hozzárendelheti a képességet a felhasználókhoz vagy a csoportokhoz. A kulcsok kiépítés és a korlátozások konfigurálása, például a hardveres biztonsági kulcsok adott készletének engedélyezése vagy blokkolása céljából is létrehozhatók házirendek. A kulcsokat fizikailag el kell osztani a végfelhasználók számára.

**A jelszó nélküli bejelentkezés az Azure AD-be és a webhelyekre FIDO2 biztonsági kulcsokkal való engedélyezésének követelményei a következők:**

* Azure AD

* Azure Multi-Factor Authentication

* A kombinált regisztráció előzetes verziója

* A FIDO2 biztonsági kulcsának előzetes verziója kompatibilis FIDO2 biztonsági kulcsot igényel

* A webes hitelesítéshez (WebAuthN) a Microsoft Edge használata szükséges a Windows 10 1809-es vagy újabb verziójához

* A FIDO2-alapú Windows-bejelentkezéshez az Azure AD-hez csatlakoztatott Windows 10 1809-es vagy újabb verziója szükséges (a legjobb megoldás a Windows 10 1903-es vagy újabb verziója)

A FIDO2-kompatibilis űrlap-tényezők közé tartoznak az USB-, NFC-és Bluetooth-eszközök. Azt javasoljuk, hogy válassza ki az adott igényeknek megfelelő FIDO2, mivel egyes platformok és böngészők még nem megfelelőek.

Azt is javasoljuk, hogy minden szervezet hozzon létre egy protokollt a felhasználók és a rendszergazdák számára, hogy elveszítsék vagy ellopják a biztonsági kulcsot. A felhasználóknak jelenteniük kell az elveszett vagy ellopott kulcsot, hogy a rendszergazdák vagy a felhasználók törölhetik a biztonsági kulcsaikat a felhasználó profiljából, és üzembe helyezhetnek egy újat.

### <a name="how-fido2-security-keys-works"></a>Hogyan működik a FIDO2 biztonsági kulcsai?

#### <a name="user-sets-up-fido2-security-key"></a>A felhasználó beállítja a FIDO2 biztonsági kulcsát

Habár a rendszergazdák [manuálisan](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable) is kiépíthetik a kulcsokat, és eloszthatják azokat a végfelhasználók számára, az [Intune](https://docs.microsoft.com/intune/windows-enrollment-methods)-ban az FIDO2 hitelesítő adatok szolgáltatóját a Windows 10 zárolási képernyőjén fogja támogatni. A rendszergazdáknak a [Azure Portal](https://portal.azure.com/) használatával is engedélyeznie kell a hardveres jogkivonat-eszközöket a jelszóval nem rendelkező hitelesítési módszerként.

A FIDO2 biztonsági kulcsainak telepítése azt is megköveteli, hogy a felhasználók a kulcsokat a [kombinált regisztráció](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md)használatával regisztrálják. A kombinált regisztrációval a felhasználók egyszer regisztrálhatnak, és az Azure multi-Factor Authentication és az egyszeri bejelentkezési jelszó alaphelyzetbe állítása (SSPR) előnyeit élvezheti.

A hardveres token alapértelmezett multi-Factor Authentication módszerként való kiválasztásán kívül ajánlott egy további ellenőrzési lehetőség is.

* Microsoft Authenticator – értesítés

* Hitelesítő alkalmazás vagy hardver jogkivonata – kód

* Telefonhívás

* Szöveges üzenet

#### <a name="user-using-fido2-security-key-for-sign-in"></a>FIDO2 biztonsági kulcsot használó felhasználó a bejelentkezéshez

A FIDO2 egy absztrakciós réteget biztosít a hitelesítő és a nyilvános/titkos kulcsú titkosítási módszer között, hogy lehetővé tegye a beépített platform-hitelesítő, például a Windows Hello és a biztonsági kulcsok feloldását egy titkos kulcshoz, és nyilvános kulcsot kézbesítenek. , amely azonosítóként használható a külső erőforrások eléréséhez. A FIDO2 biztonsági kulcsai saját beépített biztonságos enklávéval rendelkeznek, amely a titkos kulcsot tárolja, és a biometrikus vagy PIN-kód megadását igényli. A hitelesítő adatok nem használhatók fel újra, nem játszhatók le vagy nem oszthatók meg a szolgáltatások között, és nem tartoznak az adathalászat és a MiTM támadásokra, illetve a kiszolgálói szabálysértésekre.

![FIDO2 bejelentkezés](./media/ad-passwordless/azure-ad-pwdless-image6.png)

A FIDO2 biztonsági kulcsai biztonságos hitelesítést biztosítanak, amely az űrlap faktortól független. A biztonsági kulcs tárolja a hitelesítő adatokat, és egy további második tényezővel, például egy ujjlenyomattal (a biztonsági kulcsba integrálva) vagy a Windows-bejelentkezéshez megadott PIN-kóddal kell védeni. A Microsoft partnerei számos biztonsági kulcsra vonatkozó tényezőt használnak. Ilyenek például az USB biztonsági kulcsok és az NFC-kompatibilis intelligens kártyák.

> [!NOTE]
> A biztonsági kulcsnak bizonyos szolgáltatásokat és bővítményeket kell megvalósítani a FIDO2 CTAP-protokollból, hogy a [Microsoft-kompatibilis](https://aka.ms/fido2securitykeys)legyen. A Microsoft tesztelte ezeket a megoldásokat a Windows 10 és a Azure Active Directory kompatibilitásához.

![FIDO2 bejelentkezési folyamata](./media/ad-passwordless/azure-ad-pwdless-image9.png)

1. A felhasználó csatlakoztatja a FIDO2 eszközt a számítógéphez.

2. A Windows észleli a FIDO2 biztonsági kulcsot.

3. A Windows hitelesítési kérelmet küld.

4. Az Azure AD egy alkalomból álló üzenetet küld vissza.

5. A felhasználó befejezi a kézmozdulatot a FIDO2 biztonsági kulcs biztonságos enklávéban tárolt titkos kulcs feloldásához.

6. A FIDO2 biztonsági kulcs aláírja az egyszeres kulcsot a titkos kulccsal.

7. A PRT-jogkivonat az aláírt egyszeres kérelmét az Azure AD-nek küldi a rendszer.

8. Az Azure AD a FIDO2 nyilvános kulcsával ellenőrzi a bejelentkezett alkalmi használatot.

9. Az Azure AD a PRT-t a helyszíni erőforrásokhoz való hozzáférés engedélyezéséhez adja vissza.

#### <a name="user-manages-their-fido2-security-key-credentials"></a>A felhasználó felügyeli a FIDO2 biztonsági kulcsának hitelesítő adatait

A Microsoft Authenticator alkalmazáshoz hasonlóan a FIDO2 biztonsági kulcsaihoz tartozó hitelesítő adatok kezelése a végfelhasználók együttes regisztrációs felületén alapul.

## <a name="deciding-a-passwordless-method"></a>Jelszóval nem rendelkező metódus eldöntése

A három jelszóval nem rendelkező lehetőség közül választva a vállalat biztonsági, platform-és alkalmazási követelményeitől függ.

Íme néhány tényező, amelyet érdemes figyelembe vennie a Microsoft jelszó nélküli technológiájának kiválasztásakor:

||**Vállalati Windows Hello**|**Jelszó nélküli bejelentkezés a Microsoft Authenticator alkalmazással**|**FIDO2 biztonsági kulcsok**|
|:-|:-|:-|:-|
|**Előfeltételek**| Windows 10, 1809-es vagy újabb verzió<br>Azure Active Directory| Microsoft Authenticator alkalmazás<br>Telefon (Android 6,0 vagy újabb rendszert futtató iOS-és Android-eszközök)|Windows 10, 1809-es vagy újabb verzió<br>Azure Active Directory|
|**Mód**|Platform|Szoftver|Hardver|
|**Rendszerek és eszközök**|Beépített platformmegbízhatósági modul (TPM) rendelkező számítógép<br>PIN-kód és biometriai felismerés |PIN-kód és biometriai felismerés telefonon|Microsoft-kompatibilis FIDO2 biztonsági eszközök|
|**Felhasználói élmény**|Bejelentkezés PIN-kód vagy biometrikus felismerés (arc, írisz vagy ujjlenyomat) használatával Windows-eszközökkel.<br>A Windows Hello-hitelesítés az eszközhöz van kötve; a felhasználónak az eszközre és egy bejelentkezési összetevőre is szüksége van, például PIN-kódot vagy biometrikus tényezőt a vállalati erőforrások eléréséhez.|Jelentkezzen be egy olyan mobiltelefonnal, amely ujjlenyomat-vizsgálatot, arc-vagy írisz-felismerést vagy PIN-kódot használ.<br>A felhasználók a SZÁMÍTÓGÉPRŐL vagy a mobiltelefonjára bejelentkezhetnek a munkahelyi vagy személyes fiókba.|Bejelentkezés a FIDO2 biztonsági eszközzel (biometria, PIN-kód és NFC)<br>A felhasználó a szervezeti vezérlők és a PIN-kód alapján végzett hitelesítés alapján képes hozzáférni az eszközhöz, például az USB biztonsági kulcsok és az NFC-kompatibilis intelligens kártyák, kulcsok vagy hordható eszközök használatával.|
|**Engedélyezett forgatókönyvek**| Jelszó nélküli felhasználói élmény a Windows-eszközön.<br>A dedikált munkahelyi SZÁMÍTÓGÉPekre alkalmazható, amely képes az egyszeri bejelentkezésre az eszközre és az alkalmazásokra.|Jelszó nélküli, bárhol elérhető megoldás mobiltelefon használatával.<br>A munkahelyi vagy személyes alkalmazások webes elérésére bármely eszközről.|Jelszó nélküli felhasználói élmény a biometria, a PIN-kód és az NFC használatát végző feldolgozók számára.<br>A megosztott számítógépekre alkalmazható, és ha a mobiltelefon nem életképes megoldás (például ügyfélszolgálati munkatárs, nyilvános kioszk vagy kórházi csapat)|

A következő táblázat segítségével kiválaszthatja, hogy melyik módszer fogja támogatni a követelményeket és a felhasználókat.

|Persona|Forgatókönyv|Környezet|Jelszóval nem rendelkező technológia|
|:-|:-|:-|:-|
|**Felügyeleti**|Biztonságos hozzáférés egy eszközhöz felügyeleti feladatokhoz|Hozzárendelt Windows 10-es eszköz|Vállalati Windows Hello és/vagy FIDO2 biztonsági kulcs|
|**Felügyeleti**|Felügyeleti feladatok nem Windows rendszerű eszközökön| Mobil-vagy nem Windows-eszköz|Jelszó nélküli bejelentkezés a Microsoft Authenticator alkalmazással|
|**Információkkal dolgozó feldolgozó**|Termelékenységi munka|Hozzárendelt Windows 10-es eszköz|Vállalati Windows Hello és/vagy FIDO2 biztonsági kulcs|
|**Információkkal dolgozó feldolgozó**|Termelékenységi munka| Mobil-vagy nem Windows-eszköz|Jelszó nélküli bejelentkezés a Microsoft Authenticator alkalmazással|
|**Frontline Worker**|Kioszkok gyárban, üzemben, kiskereskedelemben vagy adatbevitelben|Megosztott Windows 10-es eszközök|FIDO2 biztonsági kulcsok|

## <a name="getting-started"></a>Első lépések

A jelszóval nem rendelkező hitelesítés a jövő hulláma, és egy biztonságosabb környezet elérési útja. Javasoljuk, hogy a szervezetek kezdjenek megtervezni ezt a változást, és csökkentsék a jelszavuk függőségeit. Első lépésként vegye figyelembe a következő célokat:

* Engedélyezheti a felhasználók számára az MFA + Microsoft Authenticator alkalmazást és a feltételes hozzáférést.

* Azure AD jelszavas védelem bevezetése és frissítési szabályzatok.

* Lehetővé teszi a felhasználók számára a kombinált regisztrációval rendelkező SSPR.

* Microsoft Authenticator alkalmazás üzembe helyezése mobilitáshoz.

* A Windows Hello for Business üzembe helyezése (1903: aktuális marad).

* Telepítsen FIDO2-eszközöket olyan felhasználók számára, akik nem tudnak telefont használni.

* Ha lehetséges, tiltsa le a jelszó-alapú hitelesítést.

A célok eléréséhez a következő módszert javasoljuk:

1. A Azure Active Directory lehetővé teszi az Azure MFA és a feltételes hozzáférés funkcióinak teljes körű kihasználását.

2. A többtényezős hitelesítés engedélyezése további védelem biztosításához.

3. Az önkiszolgáló jelszó-visszaállítás engedélyezése arra az esetre, ha a felhasználóknak jelszóval kell visszaesniük.

4. Microsoft Authenticator telefonos bejelentkezés üzembe helyezése a további mobilitás érdekében.

5. A Windows Hello for Business üzembe helyezése az összes Windows 10-es eszközön.

6. Felkészülés a FIDO2 biztonsági kulcsaira.

> [!NOTE]
> A jelszóval nem [](https://azure.microsoft.com/pricing/details/active-directory/) rendelkező metódusok licencelési követelményeivel kapcsolatos részletekért tekintse meg a Azure Active Directory licencelési lapját.

## <a name="conclusion"></a>Összegzés

Az elmúlt néhány évben a Microsoft továbbra is vállalta elkötelezettségét, hogy jelszó nélkül engedélyezze a világot. A Windows 10 rendszerben a Microsoft bemutatta a vállalati Windows Hello szolgáltatást, amely egy erős, hardveres védelemmel ellátott Kéttényezős hitelesítő adat, amely lehetővé teszi az egyszeri bejelentkezést Azure Active Directory és Active Directory. A vállalati Windows Hello technológiához hasonlóan a Microsoft Authenticator alkalmazás a kulcs alapú hitelesítés használatával lehetővé teszi a mobileszközök számára kötött és biometrikus vagy PIN-kódot használó felhasználói hitelesítő adatok használatát. A FIDO2 biztonsági kulcsai lehetővé teszik a hitelesítő adatok megadását és az Azure AD-be való bejelentkezést, ha a biztonsági kulcsot a Windows 10 zárolási képernyőjén hitelesítő szolgáltatóként választja. Mindhárom jelszó nélküli megoldás csökkenti az adathalászat, a jelszó-és Visszajátszási támadások kockázatát, és lehetővé teszi a felhasználók számára, hogy a bejelentkezéshez és az adatok bárhonnan történő eléréséhez nagyon biztonságos és kényelmes módon hozzáférjenek.

A modern, többtényezős hitelesítési technológiák, például a biometria és a nyilvános kulcsú titkosítás széles körben elérhető eszközökön való bevezetése az egyik leginkább befolyásoló lépés, amely értelmesen csökkentheti a vállalat személyazonossági kockázatát. A jelszó nélküli a biztonságos hitelesítés hosszú távú megközelítése, amely továbbra is fejlődik. Az új igényeknek megfelelően a szervezetek előkészíthetik magukat úgy, hogy megkezdik a jelszóval nem rendelkező technológiákba való áttérést.

## <a name="next-steps"></a>További lépések

* A [jelszó](../../active-directory/authentication/concept-authentication-passwordless.md) nélküli tartalom áttekintése
* [Jelszó engedélyezése az Azure AD-ben](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable)
