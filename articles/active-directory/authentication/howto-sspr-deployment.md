---
title: Üzembe helyezési megfontolások Azure Active Directory önkiszolgáló jelszó-visszaállításhoz
description: Ismerje meg az Azure AD önkiszolgáló jelszó-visszaállítás sikeres megvalósításához szükséges telepítési szempontokat és stratégiát
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a56f7248d5782b63befc55c4215360e0f5cb52b2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84338566"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>Azure Active Directory önkiszolgáló jelszó-visszaállítás központi telepítésének megtervezése

> [!IMPORTANT]
> Ez a telepítési terv útmutatást és ajánlott eljárásokat nyújt az Azure AD önkiszolgáló jelszó-visszaállítás (SSPR) üzembe helyezéséhez.
>
> **Ha Ön végfelhasználó, és vissza kell kérnie a fiókját, lépjen [https://aka.ms/sspr](https://aka.ms/sspr) a **következőre:.

Az [önkiszolgáló jelszó-visszaállítás (SSPR)](https://www.youtube.com/watch?v=tnb2Qf4hTP8) egy Azure Active Directory (ad) szolgáltatás, amely lehetővé teszi a felhasználók számára a jelszavuk visszaállítását anélkül, hogy segítségre lenne felvenni a kapcsolatot az informatikai személyzettel. A felhasználók gyorsan feloldják magukat, és a munka helyétől függetlenül is dolgozhatnak. Azáltal, hogy az alkalmazottak feloldják magukat, a szervezet csökkentheti a nem produktív időt és a magas támogatási költségeket a leggyakoribb jelszóval kapcsolatos problémákhoz.

A SSPR a következő főbb képességekkel rendelkezik:

* Az önkiszolgáló szolgáltatás lehetővé teszi a végfelhasználók számára, hogy a lejárt vagy nem lejárt jelszavakat anélkül állítsanak vissza, hogy a rendszergazdával vagy a támogatási szolgálattal kapcsolatba lépjenek.
* A [jelszó visszaírási](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) lehetővé teszi a helyszíni jelszavak kezelését és a fiókzárolás feloldását, bár a felhőben.
* A jelszóházirend-jelentések segítségével a rendszergazdák betekintést nyerhetnek a szervezetében bekövetkezett jelszó-visszaállítási és regisztrációs tevékenységekbe.

Ez a telepítési útmutató bemutatja, hogyan tervezheti meg és tesztelheti a SSPR.

A SSPR gyors megjelenítéséhez, majd térjen vissza a további üzembe helyezési megfontolások megismeréséhez:

> [!div class="nextstepaction"]
> [Önkiszolgáló jelszó-visszaállítás engedélyezése (SSPR)](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>Tudnivalók a SSPR

További információ a SSPR. Ismerje [meg, hogyan működik: Azure ad](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)önkiszolgáló jelszó-visszaállítás.

### <a name="key-benefits"></a>Főbb előnyök

A SSPR engedélyezésének fő előnyei a következők:

* A **Cost kezelése**. A SSPR csökkenti az informatikai támogatási költségeket azáltal, hogy lehetővé teszi a felhasználók számára, hogy a jelszavakat saját magukra állítsanak A jelszó és a zárolások elvesztése miatt is csökkenti az elvesztett időt. 

* **Intuitív felhasználói élmény**. Intuitív egyszeri felhasználói regisztrációs folyamatot biztosít, amely lehetővé teszi a felhasználók számára a jelszavak alaphelyzetbe állítását és az igény szerinti fiókok blokkolását bármely eszközről vagy helyről. A SSPR lehetővé teszi a felhasználók számára, hogy gyorsabban és hatékonyabban működjenek.

* **Rugalmasság és biztonság**. A SSPR lehetővé teszi a vállalatok számára a felhőalapú platform által biztosított biztonság és rugalmasság elérését. A rendszergazdák módosíthatják a beállításokat úgy, hogy az új biztonsági követelményeknek megfelelőek legyenek, és ezeket a módosításokat a felhasználóknak a bejelentkezésük megszakítása nélkül is elvégezzék.

* **Robusztus naplózás és használat nyomon követése**. Egy szervezet gondoskodhat arról, hogy az üzleti rendszerek biztonságban maradjanak, miközben a felhasználók visszaállítják a saját jelszavukat. A robusztus naplók a jelszó-visszaállítási folyamat egyes lépéseinek információit tartalmazzák. Ezek a naplók egy API-ból érhetők el, és lehetővé teszik a felhasználó számára, hogy az adatok importálását egy biztonsági incidensek és esemény-figyelési (SIEM) rendszerbe.

### <a name="licensing"></a>Licencek

A Azure Active Directory felhasználónkénti licenccel rendelkezik, azaz minden felhasználónak megfelelő licencre van szüksége az általuk használt funkciókhoz. A SSPR csoportos licencelését javasoljuk. 

A kiadások és szolgáltatások összehasonlításához, valamint a csoport-vagy felhasználói licencek engedélyezéséhez tekintse meg [Az Azure ad önkiszolgáló jelszó-visszaállítás licencelési követelményeit](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-licensing)ismertető témakört.

További információ a díjszabásról: [Azure Active Directory díjszabása](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites"></a>Előfeltételek

* Egy működő Azure AD-bérlő, legalább próbaverziós licenccel. Ha szükséges, [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Egy globális rendszergazdai jogosultsággal rendelkező fiók.


### <a name="training-resources"></a>Erőforrások betanítása

| Erőforrások| Hivatkozás és leírás |
| - | - |
| Videók| [Hatékonyabb informatikai skálázhatóság a felhasználók számára](https://youtu.be/g9RpRnylxS8) 
| |[Mit jelent az önkiszolgáló jelszóátállítás?](https://youtu.be/hc97Yx5PJiM)|
| |[Önkiszolgáló jelszó-visszaállítás üzembe helyezése](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Az önkiszolgáló jelszó-visszaállítás konfigurálása a felhasználók számára az Azure AD-ben](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[Útmutató [a felhasználók előkészítéséhez] regisztráció [a] biztonsági információi Azure Active Directory](https://youtu.be/gXuh0XS18wA) |
| Online tanfolyamok|[Identitások kezelése a Microsoft Azure Active Directoryban](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) A SSPR használatával modern és védett felhasználói élményt biztosíthat a felhasználóknak. Lásd különösen a "[Azure Active Directory felhasználók és csoportok kezelése](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)" modult. |
|Pluralsight fizetős tanfolyamok |[Az identitás-és hozzáférés-kezelés problémái](https://www.pluralsight.com/courses/identity-access-management-issues) Ismerje meg, hogy milyen biztonsági problémákat kell figyelembe vennie a szervezetében. Lásd különösen a "más hitelesítési módszerek" modult.|
| |[Első lépések a Microsoft nagyvállalati mobilitási csomaggal](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) Ismerje meg az ajánlott eljárásokat a helyszíni eszközök felhőbe való kiterjesztéséhez olyan módon, amely lehetővé teszi a hitelesítést, az engedélyezést, a titkosítást és a biztonságos mobil élményt. Tekintse meg különösen a "Prémium szintű Microsoft Azure Active Directory speciális funkcióinak konfigurálása" modult.
|Oktatóanyagok |[Azure AD önkiszolgáló jelszó-visszaállítási próba befejezése](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) |
| |[Jelszó visszaírási engedélyezése](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback) |
| |[Azure AD-jelszó alaphelyzetbe állítása a Windows 10 bejelentkezési képernyőjéről](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows) |
| GYIK|[Jelszavas kezelés – gyakori kérdések](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) |


### <a name="solution-architecture"></a>Megoldásarchitektúra

Az alábbi példa az általános hibrid környezetek jelszó-visszaállítási megoldási architektúráját ismerteti.

![megoldási architektúra ábrája](./media/howto-sspr-deployment//solutions-architecture.png)

A munkafolyamat leírása

A jelszó alaphelyzetbe állításához a felhasználók a [jelszó-visszaállítási portálra](https://aka.ms/sspr)léphetnek. Ellenőriznie kell a korábban regisztrált hitelesítési módszert vagy metódusokat a személyazonosságuk bizonyításához. Ha sikeresen alaphelyzetbe állítja a jelszót, elindul az alaphelyzetbe állítási folyamat.

* A csak felhőalapú felhasználók számára a SSPR az új jelszót az Azure AD-ben tárolja. 

* A hibrid felhasználók esetében a SSPR a Azure AD Connect szolgáltatáson keresztül írja vissza a jelszót a helyszíni Active Directoryba. 

Megjegyzés: a [jelszó-kivonat szinkronizálásával (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) rendelkező felhasználók számára a SSPR csak a helyszíni Active Directory tárolja a jelszavakat.

### <a name="best-practices"></a>Ajánlott eljárások

A felhasználók gyorsan regisztrálhatnak a SSPR a szervezet egy másik népszerű alkalmazásával vagy szolgáltatásával való üzembe helyezésével. Ez a művelet nagy mennyiségű bejelentkezést eredményez, és a regisztrációt fogja vezetni.

A SSPR telepítése előtt dönthet úgy, hogy meghatározza a jelszó-visszaállítási hívások számát és átlagos árát. Az adatposta üzembe helyezésével megjelenítheti a szervezet számára a SSPR értéket.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>A SSPR és az MFA együttes regisztrációjának engedélyezése

A Microsoft azt javasolja, hogy a szervezetek lehetővé tegyék a SSPR és a többtényezős hitelesítés együttes regisztrációs funkciójának használatát. Ha engedélyezi ezt a kombinált regisztrációs élményt, a felhasználóknak csak egyszer kell kiválasztaniuk regisztrációs adataikat mindkét funkció engedélyezéséhez.

A kombinált regisztrációs élmény nem igényli, hogy a szervezetek a SSPR és az Azure Multi-Factor Authentication is engedélyezzék. A kombinált regisztráció jobb felhasználói élményt nyújt a szervezeteknek. További információ: [kombinált biztonsági információk regisztrálása](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-deployment-project"></a>Az üzembe helyezési projekt megtervezése

A környezetében érdemes figyelembe vennie a szervezeti igényeket is.

### <a name="engage-the-right-stakeholders"></a>A megfelelő résztvevők bevonása

A technológiai projektek meghibásodása esetén általában a hatás, az eredmények és a felelősségek eltérő elvárásai miatt válnak elérhetővé. A buktatók elkerülése érdekében [Győződjön meg arról, hogy a megfelelő érintett feleket folytatja](https://aka.ms/deploymentplans) , és hogy a projektben érintett szerepköröket jól megértette az érintett felek és a projekt bemeneti és elszámoltathatóság dokumentálása révén.

#### <a name="required-administrator-roles"></a>Szükséges rendszergazdai szerepkörök


| Üzleti szerepkör/persona| Azure AD-szerepkör (ha szükséges) |
| - | - |
| 1. szint segélyszolgálat| Jelszókezelő |
| 2. szint helpdesk| Felhasználói rendszergazda |
| SSPR-rendszergazda| Globális rendszergazda |


### <a name="plan-communications"></a>A kommunikáció tervezése

A kommunikáció minden új szolgáltatás sikeressége szempontjából kritikus fontosságú. Proaktív módon kell kommunikálni a felhasználókkal, hogy a felhasználói élmény hogyan módosuljon, mikor módosul, és hogyan szerezhet támogatást, ha problémákat tapasztal. Tekintse át az [önkiszolgáló jelszó-visszaállítás](https://www.microsoft.com/download/details.aspx?id=56768) bevezetésével kapcsolatos anyagokat a Microsoft letöltőközpontból, és ötleteket a végfelhasználói kommunikációs stratégia megtervezéséhez.

### <a name="plan-a-pilot"></a>Pilóta megtervezése

Javasoljuk, hogy a SSPR kezdeti konfigurációját tesztkörnyezetben tesztelje. Kezdje egy kísérleti csoporttal, ha engedélyezi a SSPR a szervezeten belüli felhasználók egy részhalmaza számára. Tekintse [meg a próbaüzem ajánlott eljárásait](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans)ismertető témakört.

Csoport létrehozásához tekintse meg [a csoport létrehozása és Tagok hozzáadása a Azure Active Directory-ban](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal)című témakört. 

## <a name="plan-configuration"></a>Séma konfigurálása

A következő beállítások szükségesek a SSPR engedélyezéséhez az ajánlott értékekkel együtt.

| Terület | Beállítás | Érték |
| --- | --- | --- |
| **SSPR tulajdonságai** | Önkiszolgáló jelszó-visszaállítás engedélyezve | **Kijelölt** csoport a próbaüzem/ **mind** az éles környezethez |
| **Hitelesítési módszerek** | A regisztráláshoz szükséges hitelesítési módszerek | Az alaphelyzetbe állításhoz mindig 1 nagyobb érték szükséges |
|   | Az alaphelyzetbe állításhoz szükséges hitelesítési módszerek | Egy vagy kettő |
| **Regisztráció** | Szükséges a felhasználóknak regisztrálniuk a bejelentkezéskor? | Yes |
|   | A napok száma, amely előtt a rendszer kéri a felhasználóktól a hitelesítési adataik ismételt megerősítését | 90 – 180 nap |
| **Értesítések** | Értesítse a felhasználókat új jelszó kérésekor? | Yes |
|   | Minden rendszergazda kapjon értesítést, ha más rendszergazdák új jelszót kérnek? | Yes |
| **Testreszabás** | Segélyszolgálat hivatkozásának testreszabása | Yes |
|   | Egyéni segélyszolgálat e-mail-címe vagy URL-címe | Támogatási webhely vagy e-mail-cím |
| **Helyszíni integráció** | Jelszavak visszaírása a helyszíni AD-be | Yes |
|   | Fiók feloldásának engedélyezése a felhasználók számára a jelszó alaphelyzetbe állítása nélkül | Yes |

### <a name="sspr-properties"></a>SSPR tulajdonságai

A SSPR engedélyezésekor válasszon ki egy megfelelő biztonsági csoportot a kísérleti környezetben.

* Ha mindenki számára szeretné kikényszeríteni a SSPR-regisztrációt, javasoljuk, hogy használja az **összes** lehetőséget.
* Ellenkező esetben válassza ki a megfelelő Azure AD-vagy AD-biztonsági csoportot.

### <a name="authentication-methods"></a>Hitelesítési módszerek

Ha a SSPR engedélyezve van, a felhasználók csak akkor állíthatják vissza a jelszavukat, ha a rendszergazda által engedélyezett hitelesítési módszerekben találhatók. A módszerek közé tartozik például a telefon, a hitelesítő alkalmazás értesítése, a biztonsági kérdések stb. További információ: [Mi a hitelesítési módszer?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods).

A következő hitelesítési módszer beállításait javasoljuk:

* Állítsa be a **regisztrálni kívánt hitelesítési módszereket** legalább egy, az alaphelyzetbe állításhoz szükséges értéknél. A többszörös hitelesítés engedélyezése lehetővé teszi a felhasználók számára, hogy alaphelyzetbe kell állítani őket.

* Állítsa be a szervezete számára megfelelő szintre való **visszaállításhoz szükséges metódusok számát** . Az egyik a legkevesebb súrlódást igényli, míg a kettő növelheti a biztonsági testhelyzetet. 

Megjegyzés: a felhasználónak rendelkeznie kell a [jelszóházirend és a korlátozások Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)konfigurált hitelesítési módszerekkel.

### <a name="registration-settings"></a>Regisztrációs beállítások

Állítsa be a **felhasználókat, hogy regisztráljanak** az **Igen**értékre való bejelentkezéskor. Ehhez a beállításhoz a felhasználóknak regisztrálniuk kell a bejelentkezéskor, hogy minden felhasználó védve legyen.

Adja meg a **napok számát, mielőtt a felhasználók megkérik a hitelesítési adataik újbóli megerősítését** **90** és **180** nap között, kivéve, ha a szervezetnek rövidebb időre van szüksége üzleti igényekre.

### <a name="notifications-settings"></a>Értesítések beállításai

Konfigurálja mind a **felhasználók értesítése jelszó** **alaphelyzetbe állítását, mind a rendszergazdák értesítése, ha más rendszergazdák Igen értékre állítják vissza a jelszavukat** . **Yes** Ha az **Igen** lehetőséget választja, mindkettő növeli a biztonságot, hogy a felhasználók tisztában legyenek a jelszó alaphelyzetbe állításakor. Azt is biztosítja, hogy az összes rendszergazda tisztában legyen azzal, hogy egy rendszergazda módosítja a jelszót. Ha a felhasználók vagy rendszergazdák értesítést kapnak, és nem kezdeményezték a változást, azonnal jelenthetik a potenciális biztonsági problémákat.

### <a name="customization-settings"></a>Testreszabási beállítások

Az ügyfélszolgálati e-mail vagy URL-cím személyre szabása kritikus fontosságú, hogy a problémákkal rendelkező felhasználók azonnal segítséget kapjanak. Ezt a beállítást olyan általános ügyfélszolgálati e-mail-címre vagy weblapra állíthatja be, amelyet a felhasználók ismernek. 

További információ: [Az Azure ad funkcióinak testreszabása az önkiszolgáló jelszó-visszaállításhoz](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization).

### <a name="password-writeback"></a>Jelszó visszaírási

A **jelszó visszaírási** engedélyezve van a [Azure ad Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity) , és valós időben írja vissza a jelszó-visszaállítást a felhőben egy meglévő helyszíni könyvtárba. További információ: [Mi a Password visszaírási?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

A következő beállításokat javasoljuk:

* Győződjön meg arról, hogy **a helyi ad-beli jelszavak visszaírása** **Igen**értékre van állítva. 
* Állítsa be a **fiók feloldásának engedélyezése a felhasználók számára anélkül, hogy a jelszó alaphelyzetbe állítása** **Igen**értékű legyen.

Alapértelmezés szerint az Azure AD feloldja a fiókokat, amikor a jelszó-visszaállítást végzi.

### <a name="administrator-password-setting"></a>Rendszergazdai jelszó beállítása

A rendszergazdai fiókok emelt szintű engedélyekkel rendelkeznek. A helyszíni vállalati vagy tartományi rendszergazdák nem állíthatják alaphelyzetbe a jelszavukat a SSPR használatával. A helyszíni rendszergazdai fiókok esetében a következő korlátozások vonatkoznak:

* a csak a saját helyszíni környezetben módosíthatják a jelszavukat.
* a titkos kérdéseket és válaszokat soha nem használhatja a jelszó alaphelyzetbe állítására szolgáló metódusként.

Javasoljuk, hogy ne szinkronizálja helyszíni Active Directory rendszergazdai fiókját az Azure AD-vel.

### <a name="environments-with-multiple-identity-management-systems"></a>Környezetek több Identitáskezelés-felügyeleti rendszerrel

Egyes környezetekben több Identity Management rendszer van. A helyszíni Identity managerek, például az Oracle AM és a SiteMinder, az AD-vel való szinkronizálást igényelnek a jelszavakhoz. Ezt olyan eszközzel teheti meg, mint például a jelszó-módosítási értesítési szolgáltatás (a (z) Microsoft Identity Manager (webalkalmazás). Ha további információt szeretne megtudni erről az összetettebb forgatókönyvről, tekintse meg a következő cikket: a rendszerszintű [jelszó-módosítási értesítési szolgáltatás központi telepítése tartományvezérlőn](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-testing-and-support"></a>Tesztelés és támogatás tervezése

Az üzembe helyezés minden egyes fázisában, a kezdeti kísérleti csoportoktól a szervezet egészére kiterjedve ellenőrizze, hogy az eredmények a várt módon működnek-e.

### <a name="plan-testing"></a>Tesztelési terv

Annak ellenőrzéséhez, hogy az üzemelő példány a várt módon működik-e, tervezze meg a tesztelési esetek készletét a megvalósítás ellenőrzése érdekében. A tesztelési esetek felméréséhez jelszóval rendelkező, nem rendszergazdai jogosultsággal rendelkező felhasználóra van szükség. Ha létre kell hoznia egy felhasználót, tekintse meg [az új felhasználók hozzáadása a Azure Active Directoryhoz](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)című témakört.

A következő táblázat olyan hasznos tesztelési forgatókönyveket tartalmaz, amelyekkel dokumentálhatja a szervezete által várt eredményeket a szabályzatok alapján.
<br>


| Üzleti eset| Várt eredmények |
| - | - |
| A SSPR portál elérhető a vállalati hálózaton belülről| A szervezet által meghatározott |
| A SSPR portál a vállalati hálózaton kívülről érhető el| A szervezet által meghatározott |
| Felhasználói jelszó alaphelyzetbe állítása a böngészőből, ha a felhasználó nincs engedélyezve a jelszó-visszaállításhoz| A felhasználó nem tud hozzáférni a jelszó-visszaállítási folyamathoz |
| Felhasználói jelszó alaphelyzetbe állítása a böngészőből, ha a felhasználó nincs regisztrálva a jelszó-visszaállításhoz| A felhasználó nem tud hozzáférni a jelszó-visszaállítási folyamathoz |
| A felhasználó bejelentkezik, ha kényszeríti a jelszó-visszaállítási regisztrációt| Megkéri a felhasználót, hogy regisztrálja a biztonsági adatokat |
| A felhasználó bejelentkezik, ha a jelszó-visszaállítási regisztráció befejeződött| Megkéri a felhasználót, hogy regisztrálja a biztonsági adatokat |
| A SSPR portál elérhető, ha a felhasználó nem rendelkezik licenccel| Elérhető |
| Felhasználói jelszó alaphelyzetbe állítása Windows 10 Azure AD-hez csatlakoztatott vagy hibrid Azure AD-hez csatlakoztatott eszköz zárolási képernyőjén| A felhasználó alaphelyzetbe állíthatja a jelszót |
| A SSPR regisztrációs és használati adatok közel valós időben érhetők el a rendszergazdák számára| Naplózási naplókon keresztül érhető el |

Az Azure AD önkiszolgáló [jelszó-visszaállítási próbaverziójának kitöltését](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot)is megtekintheti. Ebben az oktatóanyagban lehetővé teszi, hogy a SSPR a szervezetében, és egy nem rendszergazdai fiók használatával tesztelje.

### <a name="plan-support"></a>Támogatás tervezése

Habár a SSPR általában nem hoz létre felhasználói problémákat, fontos, hogy felkészítse a támogatási munkatársakat az esetlegesen felmerülő problémák kezelésére. Habár a rendszergazdák az Azure AD-portálon keresztül alaphelyzetbe állíthatják a végfelhasználók jelszavát, jobb megoldás, ha az önkiszolgáló támogatási folyamaton keresztül oldja meg a problémát.

A támogatási csapat sikerességének engedélyezéséhez a felhasználóktól kapott kérdések alapján hozhat létre GYIK-et. Íme, néhány példa:

| Forgatókönyvek| Description |
| - | - |
| A felhasználónak nincs elérhető regisztrált hitelesítési módszere.| A felhasználó megpróbálja alaphelyzetbe állítani a jelszavát, de nem rendelkezik az általuk regisztrált hitelesítési módszerekkel (például a mobil telefonját otthon hagyta, és nem fér hozzá az e-mailekhez) |
| A felhasználó nem kap szöveget vagy hívást az irodában vagy a mobiltelefonján| A felhasználó szöveges vagy hívási identitást próbál meg ellenőrizni, de nem kap szöveget vagy hívást. |
| A felhasználó nem férhet hozzá a jelszó-visszaállítási portálhoz| A felhasználó szeretné visszaállítani a jelszavát, de nincs engedélyezve a jelszó-visszaállításhoz, és nem fér hozzá a laphoz a jelszavak frissítéséhez. |
| A felhasználó nem állíthat be új jelszót| A felhasználó ellenőrzi az ellenőrzést a jelszó-visszaállítási folyamat során, de nem állíthat be új jelszót. |
| A felhasználó nem látja a jelszó alaphelyzetbe állítása hivatkozást egy Windows 10-es eszközön| A felhasználó megpróbálja alaphelyzetbe állítani a jelszót a Windows 10 zárolási képernyőjén, de az eszköz nincs csatlakoztatva az Azure AD-hez, vagy az Intune-eszköz házirendje nincs engedélyezve |

### <a name="plan-rollback"></a>Terv visszaállítása

Az üzemelő példány visszaállítása:

* egyetlen felhasználó esetén távolítsa el a felhasználót a biztonsági csoportból. 

* csoport esetén távolítsa el a csoportot a SSPR-konfigurációból.

* Mindenki számára, tiltsa le az Azure AD-bérlő SSPR

## <a name="deploy-sspr"></a>Az SSPR üzembe helyezése

A telepítése előtt győződjön meg arról, hogy végrehajtotta a következőket:

1. Létrehozott és megkezdődött a [kommunikációs terv](#plan-communications)végrehajtása.

1. Meghatározta a megfelelő [konfigurációs beállításokat](#plan-configuration).

1. A [kísérleti](#plan-a-pilot) és éles környezetekhez tartozó felhasználókat és csoportokat azonosította.

1. A regisztrációhoz és az önkiszolgáláshoz [meghatározott konfigurációs beállítások](#plan-configuration) .

1. [Konfigurált jelszó visszaírási](#password-writeback) , ha hibrid környezettel rendelkezik.


**Most már készen áll a SSPR üzembe helyezésére!**

A következő területek konfigurálásának lépésenkénti lépéseiért lásd: az [önkiszolgáló jelszó-visszaállítás engedélyezése](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot#enable-self-service-password-reset) .

1. [Hitelesítési módszerek](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

1. [Regisztrációs beállítások](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

1. [Értesítések beállításai](#notifications-settings)

1. [Testreszabási beállítások](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

1. [Helyszíni integráció](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback)

### <a name="enable-sspr-in-windows"></a>SSPR engedélyezése a Windowsban
A Windows 7, 8, 8,1 és 10 rendszerű gépek esetében [engedélyezheti a felhasználók számára, hogy a Windows bejelentkezési képernyőjén visszaállítsa a jelszavukat](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-windows)

## <a name="manage-sspr"></a>SSPR kezelése

Az Azure AD további információkat biztosít a SSPR teljesítményéről a naplózás és a jelentések használatával.

### <a name="password-management-activity-reports"></a>Jelszavas kezelési tevékenységgel kapcsolatos jelentések 

A SSPR teljesítményének méréséhez a Azure Portalon előre elkészített jelentéseket is használhat. Ha megfelelő licenccel rendelkezik, egyéni lekérdezéseket is létrehozhat. További információ: [jelentéskészítési beállítások az Azure ad jelszavas kezeléséhez](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)

> [!NOTE]
>  [Globális rendszergazdának](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)kell lennie, és be kell jelentkeznie a szervezete számára az adatok gyűjtéséhez. A bekapcsolásához legalább egyszer fel kell keresnie a jelentéskészítés lapot vagy a naplókat az Azure Portalon. Addig nem történik meg az adatok gyűjtése a szervezet számára.

A regisztrációs és a jelszó-visszaállítási naplók 30 napig érhetők el. Ha a vállalaton belüli biztonsági naplózás nagyobb adatmegőrzést igényel, a naplókat olyan SIEM-eszközbe kell exportálni és felvenni, mint például az [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-active-directory), a splunk vagy a ArcSight.

![SSPR jelentés képernyőképe](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>Hitelesítési módszerek – használat és bepillantást

A [használat és](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights) az adatok segítségével megismerheti, hogy az Azure MFA és a SSPR különböző funkciói milyen hitelesítési módszereket dolgoznak a szervezetben. Ez a jelentéskészítési funkció lehetővé teszi a szervezet számára, hogy megtudja, milyen módszerekkel regisztrálhat, és hogyan használhatja őket.

### <a name="troubleshoot"></a>Hibaelhárítás

* Lásd: önkiszolgáló [jelszó-visszaállítási hibák megoldása](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-troubleshoot) 

* A [jelszó-kezeléssel kapcsolatos gyakori kérdések](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) követése 

### <a name="helpful-documentation"></a>Hasznos dokumentáció

* [Mik a hitelesítési módszerek?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

* [Hogyan működik: az Azure AD önkiszolgáló jelszó-visszaállítási szolgáltatása?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)

* [Az Azure AD funkcióinak testreszabása az önkiszolgáló jelszó-visszaállításhoz](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

* [Jelszószabályzatok és -korlátozások az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)

* [Mi a jelszó visszaírási?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

## <a name="next-steps"></a>További lépések

* Az SSPR üzembe helyezésének megkezdéséhez lásd: az [Azure ad önkiszolgáló jelszó-visszaállításának engedélyezése](tutorial-enable-sspr.md)

* [Az Azure AD jelszavas védelem megvalósításának megfontolása](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)

* [Vegye fontolóra az Azure AD Smart zárolás megvalósítását](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)
