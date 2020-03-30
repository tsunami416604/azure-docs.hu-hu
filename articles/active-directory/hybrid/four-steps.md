---
title: Négy lépés az erős identitásalaphoz – Azure AD
description: Ez a témakör négy lépésben ismerteti a hibrid identitás ügyfelek hozhat létre egy erős identitás alapja.
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3eb98f543e17981be0d5b9ab08fa4e146659b47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74206779"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Négy lépés az erős identitásalaphoz az Azure Active Directoryval

Az alkalmazásokhoz és adatokhoz való hozzáférés kezelése már nem támaszkodhat a hagyományos hálózati biztonsági határstratégiákra, például a peremhálózatokra és a tűzfalakra az alkalmazások gyors felhőbe való mozgása miatt. Mostantól a szervezeteknek meg kell bízniuk az identitáskezelési megoldásukban annak szabályozására, hogy ki és mi férhet hozzá a szervezet alkalmazásaihoz és adataihoz. Több szervezet is lehetővé teszi az alkalmazottak számára, hogy saját eszközeiket munkába hozzák, és bárhonnan használhassák eszközeiket, ahonnan csatlakozhatnak az internethez. Az eszközök megfelelőségének és biztonságosságának biztosítása fontos szemponttá vált a szervezet által megvalósítandó identitáskezelési megoldásban. A mai digitális munkahelyen az identitás a felhőbe költöző szervezetek [elsődleges vezérlősíkja.](https://www.microsoft.com/security/technology/identity-access-management?rtc=1)

Az Azure Active Directory (Azure AD) hibrid identitáskezelési megoldás ának alkalmazása során a szervezetek olyan prémium szintű funkciókhoz férhetnek hozzá, amelyek automatizálással, delegálással, önkiszolgáló és egyszeri bejelentkezési képességekkel szabadítják fel a hatékonyságot. Lehetővé teszi a dolgozók számára, hogy a vállalati erőforrásokhoz bárhol hozzáférjenek, ahol dolgozniuk kell, miközben lehetővé teszi az informatikai csapat számára, hogy szabályozza ezt a hozzáférést azáltal, hogy biztosítja, hogy a megfelelő emberek megfelelő hozzáféréssel rendelkezzenek a megfelelő erőforrásokhoz a biztonságos termelékenység létrehozásához.

A tanulásunk alapján ez az ajánlott eljárások ellenőrzőlistája segít az ajánlott műveletek gyors üzembe helyezésében, hogy *erős* identitásalapot hozzon létre a szervezetben:

* Egyszerű csatlakozás alkalmazásokhoz
* Minden felhasználó automatikusan létrehoz egy identitást
* A felhasználók biztonságos felvértezése
* Az elemzések működőképessé tétele

## <a name="step-1---connect-to-apps-easily"></a>1. lépés - Egyszerűen csatlakozhat alkalmazásokhoz

Az alkalmazások Azure AD-vel való összekapcsolásával javíthatja a végfelhasználók termelékenységét és biztonságát az egyszeri bejelentkezés (SSO) engedélyezésével és a felhasználói kiépítéssel. Egyetlen helyen kezelheti alkalmazásait, az Azure AD-t, minimalizálhatja a felügyeleti terhelést, és egyetlen vezérlőpontot érhet el a biztonsági és megfelelőségi szabályzatok számára.

Ez a szakasz ismerteti az alkalmazások felhasználói hozzáférésének kezelésére, a belső alkalmazások biztonságos távoli elérésének engedélyezésére, valamint az alkalmazások Azure AD-be való áttelepítésének előnyeit.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Alkalmazások zökkenőmentes elérhetővé szolgáltatása a felhasználók számára

Az Azure AD lehetővé teszi a rendszergazdák számára, hogy [alkalmazásokat adjanak hozzá](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) az [Azure Portal](https://portal.azure.com/)Vállalati alkalmazásgyűjteményéhez. Alkalmazások hozzáadása a nagyvállalati alkalmazások gyűjteményének megkönnyíti az alkalmazások konfigurálása az Azure AD identitásszolgáltatóként. Emellett lehetővé teszi az alkalmazáshoz való felhasználói hozzáférés feltételes hozzáférési házirendekkel történő kezelését, valamint az alkalmazások egyszeri bejelentkezésének (SSO) konfigurálását, hogy a felhasználóknak ne kelljen ismételten megadniuk a jelszavukat, és automatikusan be legyenek jelentkezve mind a helyszíni, mind a felhőalapú alkalmazások.

Miután az alkalmazásokat hozzáadta az Azure AD-gyűjteményhez, a felhasználók láthatják a hozzájuk rendelt alkalmazásokat, és szükség szerint kereshetnek és kérhetnek más alkalmazásokat. Az Azure AD [számos módszert](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences) kínál a felhasználók számára az alkalmazásaik eléréséhez:

* Hozzáférési panel/Saját alkalmazások
* Az Office 365 appindítója
* Közvetlen bejelentkezés az összevont alkalmazásokba
* Közvetlen bejelentkezési hivatkozások

Ha többet szeretne megtudni az alkalmazásokhoz való felhasználói hozzáférésről, olvassa el a cikk **3.**

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Alkalmazások áttelepítése az Active Directory összevonási szolgáltatásokból az Azure AD-be

Az egyszeri bejelentkezési konfiguráció áttelepítése az Active Directory összevonási szolgáltatásokból (ADFS) az Azure AD-be további biztonsági képességeket, egységesebb kezelhetőséget és együttműködést tesz lehetővé. Az optimális eredmény érdekében azt javasoljuk, hogy telepítse át az alkalmazásokat az AD FS-ből az Azure AD-be. Az alkalmazás hitelesítésének és engedélyezésének az Azure AD-be való bejuttatása a következő előnyöket biztosítja:

* Költség kezelése
* A kockázatok kezelése
* A termelékenység növelése
* A megfelelés és az irányítás kezelése

További információ: Az alkalmazások áttelepítése az [Azure Active Directoryba](https://aka.ms/migrateapps/whitepaper) tanulmányban.

### <a name="enable-secure-remote-access-to-apps"></a>Biztonságos távelérés engedélyezése az alkalmazásokhoz

[Az Azure AD alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) egy egyszerű megoldást biztosít a szervezetek számára, hogy a felhőben közzétegyék a helyszíni alkalmazásokat a távoli felhasználók számára, akiknek biztonságos hozzáférésre van szükségük a belső alkalmazásokhoz. Az Azure AD-be való egyszeri bejelentkezés után a felhasználók külső URL-címeken vagy egy belső alkalmazásportálon keresztül is elérhetik a felhőalapú és a helyszíni alkalmazásokat.

Az Azure AD alkalmazásproxy a következő előnyöket kínálja:

* Az Azure AD kiterjesztése a helyszíni erőforrásokra
  * Felhőszintű biztonság és védelem
  * Olyan funkciók, mint a feltételes hozzáférés és a többtényezős hitelesítés, amelyek könnyen engedélyezhetők
* Nincsenek összetevők a peremhálózaton, például VPN és hagyományos fordított proxymegoldások
* Nincs szükség bejövő kapcsolatokra
* Egyszeri bejelentkezés (SSO) a felhőben és a helyszíni eszközökön, erőforrásokon és alkalmazásokon keresztül
* Lehetővé teszi a végfelhasználók számára, hogy bárhol és bármikor termelékenyek legyenek

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Fedezze fel az árnyékinformatikai szolgáltatást a Microsoft Cloud App Security segítségével

A modern vállalatokban az informatikai részlegek gyakran nincsenek tisztában az összes olyan felhőalkalmazással, amelyet a felhasználók a munkájukhoz használnak. Amikor a rendszer megkérdezi az informatikai rendszergazdákat, hogy szerintük hány felhőalapú alkalmazást használnak, átlagosan 30 vagy 40-et mondanak. A valóságban az átlag több mint 1000 különálló alkalmazást használ a szervezet alkalmazottai által. Az alkalmazottak 80%-a olyan nem szankcionált alkalmazásokat használ, amelyeket senki sem tekintett át, és nem biztos, hogy megfelel az Ön biztonsági és megfelelőségi irányelveinek.

[A Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) segítségével azonosíthatja azokat a hasznos alkalmazásokat, amelyek népszerűek a felhasználók körében, amelyeket az informatikai felhasználók szankcionálhatnak, és hozzáadnak a Vállalati alkalmazások gyűjteményéhez, hogy a felhasználók kihasználhassák az olyan képességekelőnyeit, mint az SSO és a Conditional Access.

<em>"**A Cloud App Security** segít annak biztosításában, hogy munkatársaink megfelelően használják a felhő- és SaaS-alkalmazásainkat oly módon, hogy támogassák az Accenture védelmét szolgáló alapvető biztonsági házirendeket."</em> --- [John Blasi, ügyvezető igazgató, információbiztonság, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Az árnyékinformatikai adatok észlelése mellett az MCAS az alkalmazások kockázati szintjét is meghatározhatja, megakadályozhatja a vállalati adatokhoz való jogosulatlan hozzáférést, az esetleges adatszivárgást és az alkalmazásokban rejlő egyéb biztonsági kockázatokat.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>2. lépés - Minden felhasználó számára automatikusan hozzon létre egy identitást

A helyszíni és felhőalapú könyvtárak egy Azure AD hibrid identitáskezelési megoldásban való egyesítése lehetővé teszi a meglévő helyszíni Active Directory-befektetés újbóli felhasználását a meglévő identitások felhőben való kiépítésével. A megoldás szinkronizálja a helyszíni identitások az Azure AD, míg az informatikai kezeli a helyszíni Active Directory fut a meglévő cégirányítási megoldások, mint az identitások elsődleges forrása az igazság. A Microsoft Azure AD hibrid identitáskezelési megoldása a helyszíni és a felhőalapú képességekre is kiterjed, így közös felhasználói identitást hoz létre a hitelesítéshez és az összes erőforrás engedélyezéséhez, függetlenül azok helyétől.

A helyszíni könyvtárak Azure AD-vel való integrálása hatékonyabbá teszi a felhasználókat, és megakadályozza, hogy a felhasználók több fiókot használjanak az alkalmazások és szolgáltatások között azáltal, hogy közös identitást biztosítanak a felhőbeli és a helyszíni erőforrások eléréséhez. Több fiók használata fájdalompont a végfelhasználók és az it számára egyaránt. A végfelhasználó szempontjából a több fiók azt jelenti, hogy több jelszót kell megjegyezni. Ennek elkerülése érdekében sok felhasználó újra ugyanazt a jelszót használja minden fiókhoz, ami biztonsági szempontból rossz. Informatikai szempontból az újrafelhasználás gyakran több jelszó-visszaállításhoz és ügyfélszolgálati költségekhez vezet a végfelhasználói panaszok mellett.

Az Azure AD Connect az az eszköz, amellyel szinkronizálhatja a helyszíni identitások az Azure AD, amely ezután felhőalapú alkalmazások eléréséhez használható. Miután az identitások az Azure AD-ben vannak, saas-alkalmazásokhoz, például a Salesforce-hoz vagy a Concur-hoz építhetnek ki.

Ebben a szakaszban a magas rendelkezésre állás, a felhő modern hitelesítése és a helyszíni helyigény csökkentése érdekében javaslatokat sorolunk fel.

> [!NOTE]
> Ha többet szeretne megtudni az Azure AD Connectről, olvassa el [a Mi az Azure AD Connect Sync?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Átmeneti kiszolgáló beállítása az Azure AD Connecthez és naprakészen tartása

Az Azure AD Connect kulcsfontosságú szerepet játszik a kiépítési folyamatban. Ha a szinkronizálási kiszolgáló bármilyen okból offline állapotba kerül, a helyszíni módosítások nem frissülnek a felhőben, és hozzáférési problémákat okoznak a felhasználóknak. Fontos, hogy olyan feladatátvételi stratégiát határozzon meg, amely lehetővé teszi a rendszergazdák számára a szinkronizálás gyors folytatását a szinkronizálási kiszolgáló offline állapotba kerülése után.

Ahhoz, hogy magas rendelkezésre állást biztosítson abban az esetben, ha az elsődleges Azure AD Connect-kiszolgáló offline állapotba kerül, javasoljuk, hogy helyezzen üzembe egy külön [átmeneti kiszolgálót](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server) az Azure AD Connect hez. A kiszolgáló telepítése lehetővé teszi a rendszergazda számára, hogy egy egyszerű konfigurációs kapcsolóval "elősegítse" az átmeneti kiszolgálót éles környezetbe. Miután a készenléti kiszolgáló konfigurálva átmeneti módban is lehetővé teszi, hogy teszteljék és üzembe helyezése az új konfigurációs változásokat, és vezessenek be egy új kiszolgálót, ha a régi leszerelése.

> [!TIP]
> Az Azure AD Connect rendszeresen frissül. Ezért erősen ajánlott, hogy tartsa az átmeneti kiszolgáló aktuális annak érdekében, hogy kihasználják a teljesítmény beli fejlesztések, hibajavítások és az új képességek, amelyek minden új verzió biztosítja.

### <a name="enable-cloud-authentication"></a>Felhőalapú hitelesítés engedélyezése

A helyszíni Active Directoryval rendelkező szervezeteknek ki kell terjeszteniük címtárukat az Azure AD-re az Azure AD Connect használatával, és konfigurálniuk kell a megfelelő hitelesítési módszert. A megfelelő [hitelesítési módszer kiválasztása a](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) szervezet számára az első lépés az alkalmazások felhőbe való áthelyezésének útján. Ez egy kritikus összetevő, mivel szabályozza a hozzáférést az összes felhőbeli adatok és erőforrások.

A legegyszerűbb és ajánlott módszer a felhőbeli hitelesítés engedélyezéséhez a helyszíni címtárobjektumok az Azure AD-ben, hogy a [jelszókivonat-szinkronizálás](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS) engedélyezése. Azt is megteheti, hogy egyes szervezetek engedélyezik [az átmenő hitelesítést](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA).

Akár phs-t, akár [PTA-t](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) választ, ne felejtse el engedélyezni a zökkenőmentes egyszeri bejelentkezést, hogy a felhasználók anélkül férhessenek hozzá a felhőalapú alkalmazásokhoz, hogy folyamatosan meg kellene adniuk felhasználónevüket és jelszavukat az alkalmazásban, amikor Windows 7 és 8 eszközöket használnak a vállalati hálózaton. Egyszeri bejelentkezés nélkül a felhasználóknak emlékezniük kell az alkalmazásspecifikus jelszavakra, és be kell jelentkezniük az egyes alkalmazásokba. Hasonlóképpen az informatikai személyzetnek is létre kell hoznia és frissítenie kell a felhasználói fiókokat minden alkalmazáshoz, például az Office 365-höz, a Boxhoz és a Salesforce-hoz. A felhasználóknak meg kell emlékezniük a jelszavukra, és időt kell fordítaniuk arra, hogy bejelentkezhessenek az egyes alkalmazásokba. A legjobb felhasználói élmény, a kockázatcsökkentés, a jelentési képesség és a cégirányítási képesség érdekében elengedhetetlen, hogy szabványos egyszeri bejelentkezési mechanizmust biztosítson a teljes vállalat számára.

Az AD FS-t vagy más helyszíni hitelesítésszolgáltatót már használó szervezetek esetében az Azure AD-re, mint identitásszolgáltatóra való áttérés csökkentheti az összetettséget és javíthatja a rendelkezésre állást. Ha nincsenek konkrét használati esetek az összevonáshoz, javasoljuk, hogy az összevont hitelesítésről a PHS-re, a seamless sso-ra vagy pta-ra és a seamless sso-ra való áttérést a csökkentett helyszíni lábnyom és a felhő által nyújtott rugalmasság előnyeinek kihasználásához jobb felhasználói élményt. További információ: [Áttelepítés összevonásról jelszóra az Azure Active Directory hoz.](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync)

### <a name="enable-automatic-deprovisioning-of-accounts"></a>A fiókok automatikus megszüntetésének engedélyezése

Az automatikus kiépítés és az alkalmazások kiirtásának engedélyezése a legjobb stratégia az identitások életciklusának több rendszeren keresztül történő szabályozására. Az Azure AD támogatja a felhasználói fiókok [automatizált, házirendalapú kiépítését és a](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) felhasználói fiókok deprovisioning szolgáltatását számos népszerű SaaS-alkalmazáshoz, például a ServiceNow-hoz és a Salesforce-hoz, valamint az [SCIM 2.0 protokollt](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)megvalósító más alkalmazásokhoz. A hagyományos kiépítési megoldásokkal ellentétben, amelyek egyéni kódot vagy a CSV-fájlok manuális feltöltését igénylik, a kiépítési szolgáltatás a felhőben található, és az Azure Portalon keresztül beállítható és kezelhető, előre integrált összekötőket tartalmaz. Az automatikus megszüntetés egyik fő előnye, hogy segít a szervezet biztonságának biztosításában azáltal, hogy azonnal eltávolítja a felhasználók identitását a kulcsfontosságú SaaS-alkalmazásokból, amikor elhagyják a szervezetet.

Ha többet szeretne tudni az automatikus felhasználói fiókok kiépítéséről és működéséről, olvassa el a [Felhasználói kiépítés és a SaaS-alkalmazások ba való megszüntetésének automatizálása az Azure Active Directoryval.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

## <a name="step-3---empower-your-users-securely"></a>3. lépés - A felhasználók biztonságos felvértezése

A mai digitális munkahelyen fontos, hogy egyensúlyt teremtsünk a biztonság és a termelékenység között. A végfelhasználók azonban gyakran visszaszorítják azokat a biztonsági intézkedéseket, amelyek lassítják a hatékonyságot és a felhőalapú alkalmazásokhoz való hozzáférést. Ennek érdekében az Azure AD önkiszolgáló képességeket biztosít, amelyek lehetővé teszik a felhasználók számára, hogy továbbra is hatékonyan, miközben minimálisra csökkenti a felügyeleti többletterhelést.

Ez a szakasz javaslatokat sorol fel a súrlódások szervezetből való eltávolítására azáltal, hogy a felhasználók éberséget biztosít, miközben éberen marad.

### <a name="enable-self-service-password-reset-for-all-users"></a>Önkiszolgáló jelszó-visszaállítás engedélyezése minden felhasználó számára

Az Azure [önkiszolgáló jelszó-visszaállítás](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR) egyszerű eszközt kínál a rendszergazdák számára, hogy a felhasználók rendszergazdai beavatkozás nélkül alaphelyzetbe állíthassák és feloldhassák jelszavukat vagy fiókjukat. A rendszer részletes, követhető jelentést tartalmaz a felhasználók rendszerhozzáféréséről, továbbá értesítőkkel figyelmeztet az illetéktelen használatra vagy visszaélésre.

Alapértelmezés szerint az Azure AD feloldja a fiókok, amikor jelszó-visszaállítás t hajt végre. Ha azonban engedélyezi az Azure AD [Connect-integrációt a helyszínen,](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration)akkor is lehetősége van elválasztani ezt a két műveletet, amelyek lehetővé teszik a felhasználók számára, hogy a jelszó alaphelyzetbe állítása nélkül oldják fel a fiókzárolást.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Annak biztosítása, hogy minden felhasználó regisztrálva van az MFA-ra és az SSPR-re

Az Azure jelentéseket biztosít, amelyek segítségével Ön és szervezete biztosítja, hogy a felhasználók regisztrálva vannak az MFA és az SSPR számára. Előfordulhat, hogy a regisztrálatlan felhasználóknak meg kell tanulniuk a folyamatot.

Az [MFA-bejelentkezések jelentés](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting) információkat tartalmaz az MFA-használatról, és betekintést nyújt az MFA működésébe a szervezetben. Az Azure AD bejelentkezési tevékenységhez (és auditokhoz és kockázatészleléshez) való hozzáférés elengedhetetlen a hibaelhárításhoz, a használati elemzésekhez és a kriminalisztikai vizsgálatokhoz.

Hasonlóképpen az [önkiszolgáló jelszókezelés jelentés](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) segítségével megállapítható, hogy ki regisztrált (vagy nem) regisztrált az SSPR-hez.

### <a name="self-service-app-management"></a>Önkiszolgáló alkalmazások kezelése

Ahhoz, hogy a felhasználók önfelderíthessék az alkalmazásokat a hozzáférési paneljükről, engedélyeznie kell az [önkiszolgáló alkalmazások hozzáférését](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access) minden olyan alkalmazáshoz, amelyet lehetővé kíván tenni a felhasználók számára, hogy önfelderítsék és hozzáférést kérjenek. Az önkiszolgáló alkalmazások hozzáférése nagyszerű módja annak, hogy a felhasználók önfelderítsék az alkalmazásokat, és adott esetben lehetővé tegyék az üzleti csoport számára az alkalmazásokhoz való hozzáférés jóváhagyását. Engedélyezheti, hogy az üzleti csoport közvetlenül a hozzáférési paneljeikről kezelje a [jelszóegyszeri bejelentkezésalkalmazások](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app) hoz a felhasználókhoz rendelt hitelesítő adatokat.

### <a name="self-service-group-management"></a>Önkiszolgáló csoportkezelés

A felhasználók alkalmazásokhoz való hozzárendelése a csoportok használatakor a legjobb, mivel nagy rugalmasságot és nagyfokú kezelhetőséget tesz lehetővé:

* Attribútumalapú dinamikus csoporttagsággal
* Delegálás az alkalmazástulajdonosoknak

Az Azure AD lehetővé teszi az erőforrásokhoz való hozzáférés kezelését biztonsági csoportok és Office 365-csoportok használatával. Ezeket a csoportokat olyan csoporttulajdonos is kezelheti, aki jóváhagyhatja vagy megtagadhatja a tagsági kérelmeket, és delegálhatja a csoporttagság ellenőrzését. Az [önkiszolgáló csoportkezelés](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)néven ismert szolgáltatás időt takarít meg azáltal, hogy lehetővé teszi a nem rendszergazdai szerepkörrel rendelkező csoporttulajdonosok számára, hogy csoportokat hozzanak létre és kezeljenek anélkül, hogy a rendszergazdákra kellene támaszkodniuk a kéréseik kezeléséhez.

## <a name="step-4---operationalize-your-insights"></a>4. lépés - Működőképessé az elemzéseket

A biztonsággal kapcsolatos események és a kapcsolódó riasztások naplózása a hatékony stratégia alapvető összetevői annak biztosítása érdekében, hogy a felhasználók hatékonyak maradjanak, és a szervezet biztonságos legyen. A biztonsági naplók és jelentések segíthetnek a kérdések megválaszolásában, például:

* Azt használja, amiért fizet?
* Van valami gyanús vagy rosszindulatú történik a bérlőmben?
* Ki rejlett egy biztonsági incidens során?

A biztonsági naplók és jelentések elektronikus nyilvántartást nyújtanak a gyanús tevékenységekről, és segítenek észlelni azokat a mintákat, amelyek a hálózat külső behatolására vagy sikeres behatolására utalhatnak, valamint a belső támadásokat. A naplózás segítségével figyelheti a felhasználói tevékenységet, dokumentálhatja a jogszabályok nak való megfelelést, törvényszéki elemzést és egyebeket. A riasztások biztonsági eseményekről adnak értesítést.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Legkevésbé kiemelt rendszergazdai szerepkörök hozzárendelése műveletekhez

Ahogy gondolkodik a megközelítés műveletek, van egy pár szintű adminisztráció, hogy fontolja meg. Az első szint a globális rendszergazda(k)ra hárítja az adminisztráció terhét. Mindig a globális rendszergazdai szerepkörhasználata megfelelő lehet a kisebb vállalatok számára. De a nagyobb szervezetek ügyfélszolgálati személyzet és a rendszergazdák felelős konkrét feladatokat, hozzárendelve a globális rendszergazda lehet a biztonsági kockázatot, mivel ez biztosítja az egyének képesek kezelni feladatokat, amelyek felett és azon túl hogy mire kellene képeseknek lenniük.

Ebben az esetben meg kell fontolnia a következő szintű adminisztráció. Az Azure AD használatával kijelölheti a végfelhasználókat "korlátozott rendszergazdákként", akik kevésbé kiemelt jogosultságú szerepkörökben kezelhetik a feladatokat. Például kijelölheti az ügyfélszolgálati személyzetet a [biztonsági olvasói](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader) szerepkörrel, hogy lehetővé tegye számukra a biztonsággal kapcsolatos szolgáltatások írásvédett hozzáféréssel való kezelését. Vagy talán célszerű a [hitelesítési rendszergazdai](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator) szerepkört egyénekhez rendelni, hogy lehetővé tegye számukra a nem jelszó hitelesítő adatok alaphelyzetbe állítását, illetve az Azure Service Health olvasását és konfigurálását.

További információ: [Rendszergazdai szerepkör-engedélyek az Azure Active Directoryban.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Hibrid összetevők figyelése (Azure AD Connect-szinkronizálás, AD FS) az Azure AD Connect Health használatával

Az Azure AD Connect és az AD FS olyan kritikus összetevők, amelyek potenciálisan megszakíthatják az életciklus-kezelést és -hitelesítést, és végső soron kimaradásokhoz vezethetnek. Ezért telepítenie kell az Azure AD Connect Health ezen összetevők figyeléséhez és jelentéséhez.

További információkért olvassa el [az AD FS figyelője az Azure AD Connect Health használatával](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs).

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Adatnaplók gyűjtése elemzéshez az Azure Monitor használatával

[Az Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) egy egységes figyelési portál az összes Azure AD-naplóhoz, amely mély elemzéseket, fejlett elemzéseket és intelligens gépi tanulást biztosít. Az Azure Monitor segítségével metrikákat és naplókat használhat fel a portálon belül és API-kon keresztül, hogy jobban átnézhesse az erőforrások állapotát és teljesítményét. Lehetővé teszi, hogy egyetlen üvegtábla élményt a portálon belül, miközben lehetővé teszi a széles körű termékintegrációk api-k és adatexportálási lehetőségek, amelyek támogatják a hagyományos külső SIEM rendszerek. Az Azure Monitor lehetővé teszi, hogy riasztási szabályokat konfiguráljon az értesítéshez, vagy automatikus műveleteket végrehajtson az erőforrásokat érintő problémákesetén.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Egyéni irányítópultok létrehozása a vezetéshez és a napi

A SIEM-megoldással nem rendelkező szervezetek letölthetik az Azure [AD-hez való Power BI-tartalomcsomagot.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack) A Power BI-tartalomcsomag előre elkészített jelentéseket tartalmaz, amelyek segítségével megismerheti, hogy a felhasználók hogyan alkalmazzák és használják az Azure AD-funkciókat, így betekintést nyerhet a címtárban lévő összes tevékenységbe. Saját [egyéni irányítópultot](https://docs.microsoft.com/power-bi/service-dashboards) is létrehozhat, és megoszthatja a vezetői csapattal, hogy beszámoljon a napi tevékenységekről. Az irányítópultok nagyszerű módja annak, hogy figyelemmel kísérje vállalkozását, és egy pillantással áttekinthesse a legfontosabb mutatókat. Az irányítópulton található vizualizációk egy vagy több adatkészletből vagy jelentésből is származhatnak. Az irányítópult a helyi és a felhőben keletkezett adatokat ötvözi, és egyesített nézetet biztosít függetlenül attól, hogy az adatok hol találhatók.

![Egyéni Power BI-irányítópult](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>A támogatási hívásillesztőprogramok megismerése

Ha egy hibrid identitáskezelési megoldás, ebben a cikkben vázolt, végső soron észre kell venni a támogatási hívások csökkentése. Az olyan gyakori problémákat, mint az elfelejtett jelszavak és a fiókzárolások, az Azure önkiszolgáló jelszó-alaphelyzetbe állításának megvalósításával mérsékeljük, miközben az önkiszolgáló alkalmazás-hozzáférés lehetővé teszi a felhasználók számára, hogy önfelderítsék és hozzáférést kérjenek az alkalmazásokhoz anélkül, hogy informatikai személyzetén.

Ha nem tartja be a támogatási hívások csökkenését, javasoljuk, hogy elemezze a támogatási hívásillesztőprogramokat annak érdekében, hogy ellenőrizze, az SSPR vagy az önkiszolgáló alkalmazás-hozzáférés megfelelően van-e konfigurálva, vagy ha vannak más olyan új problémák, amelyek szisztematikusan alkalmazhatók. Foglalkozni.

*"Digitális átalakulási utunk során megbízható identitás- és hozzáférés-kezelő szolgáltatóra volt szükségünk, hogy elősegítsük a zökkenőmentes, mégis biztonságos integrációt közöttünk, partnereink és felhőszolgáltatók között a hatékony ökoszisztéma érdekében; Az Azure AD volt a legjobb megoldás, amely a szükséges képességeket és láthatóságot kínált a kockázatok észlelésére és az azokra való reagálásra."* --- [Yazan Almasri, globális információbiztonsági igazgató, Aramex](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Az alkalmazások használatának figyelése az elemzések ösztönzése érdekében

Az árnyékinformatikai szolgáltatás felderítése mellett az alkalmazások használatának figyelése a [szervezeten](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) belül a Microsoft Cloud App Security használatával segíthet a szervezetnek, hogy teljes mértékben kihasználja a felhőalkalmazások ígéretét. A tevékenység jobb láthatóságának növelésével és a kritikus fontosságú adatok felhőalapú alkalmazások közötti védelmének növelésével segít az eszközök feletti ellenőrzés ben. Az alkalmazások használatának figyelése a szervezetben az MCAS használatával segíthet a következő kérdések megválaszolásában:

* Milyen nem engedélyezett alkalmazásokat használnak az alkalmazottak az adatok tárolására?
* Hol és mikor tárolják a bizalmas adatokat a felhőben?
* Ki fér hozzá a bizalmas adatokhoz a felhőben?

*"A Cloud App Security segítségével gyorsan észlelhetjük az anomáliákat, és intézkedhetünk."* --- [Eric LePenske, vezető menedzser, információbiztonság, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Összefoglalás

A hibrid identitáskezelési megoldás megvalósításának számos szempontból van, de ez a négylépéses ellenőrzőlista segít gyorsan megvalósítani egy identitás-infrastruktúra, amely lehetővé teszi a felhasználók számára, hogy hatékonyabb és biztonságosabb.

* Egyszerű csatlakozás alkalmazásokhoz
* Minden felhasználó automatikusan létrehoz egy identitást
* A felhasználók biztonságos felvértezése
* Az elemzések működőképessé tétele

Reméljük, hogy ez a dokumentum hasznos ütemterv et hoz létre egy erős identitás alapot a szervezet számára.

## <a name="identity-checklist"></a>Identitás-ellenőrzőlista

Azt javasoljuk, hogy nyomtassa ki az alábbi ellenőrzőlistát referenciaként, amikor megkezdi az utazást egy szilárdabb identitásalapba a szervezetben.

### <a name="today"></a>Today

|Bejelentkezett?|Elem|
|:-|:-|
||Kísérleti önkiszolgáló jelszó-visszaállítás (SSPR) egy csoportszámára|
||Hibrid összetevők figyelése az Azure AD Connect Health használatával|
||Legkevésbé kiemelt rendszergazdai szerepkörök hozzárendelése a művelethez|
||Fedezze fel az árnyékinformatikai szolgáltatást a Microsoft Cloud App Security segítségével|
||Adatnaplók gyűjtése elemzéshez az Azure Monitor használatával|

### <a name="next-two-weeks"></a>Következő két hét

|Bejelentkezett?|Elem|
|:-|:-|
||Alkalmazás elérhetővé téválása a felhasználók számára|
||Az Azure AD-kiépítés a választáshoz tervezett SaaS-alkalmazásokhoz|
||Átmeneti kiszolgáló beállítása az Azure AD Connect hez, és naprakészen tartása|
||Alkalmazások áttelepítésének megkezdése az ADFS-ből az Azure AD-be|
||Egyéni irányítópultok létrehozása a vezetéshez és a napi|

### <a name="next-month"></a>Következő hónapban

|Bejelentkezett?|Elem|
|:-|:-|
||Az alkalmazások használatának figyelése az elemzések ösztönzése érdekében|
||Az alkalmazások biztonságos távelérésének próbaalkalmazása|
||Annak biztosítása, hogy minden felhasználó regisztrálva van az MFA-ra és az SSPR-re|
||Felhőalapú hitelesítés engedélyezése|

### <a name="next-three-months"></a>Következő három hónap

|Bejelentkezett?|Elem|
|:-|:-|
||Önkiszolgáló alkalmazáskezelés engedélyezése|
||Önkiszolgáló csoportkezelés engedélyezése|
||Az alkalmazások használatának figyelése az elemzések ösztönzése érdekében|
||A támogatási hívásillesztőprogramok megismerése|

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan növelheti a biztonságos testtartást az Azure Active Directory és az ötlépéses ellenőrzőlista használatával – [Öt lépés az identitásinfrastruktúra védelméhez.](https://aka.ms/securitysteps)

Ismerje meg, hogy az Azure AD identitáskezelési funkciói hogyan segíthetnek felgyorsítani a felhőalapú felügyelt kezelésre való áttérést azáltal, hogy olyan megoldásokat és képességeket biztosítanak, amelyek lehetővé teszik a szervezetek számára, hogy gyorsan elfogadják és áthelyezzék identitáskezelésüket a hagyományos helyszíni rendszerekről az Azure AD-re – [hogyan biztosítja az Azure AD a felhőalapú felügyelt helyszíni számítási feladatokhoz.](https://aka.ms/cloudgoverned)
