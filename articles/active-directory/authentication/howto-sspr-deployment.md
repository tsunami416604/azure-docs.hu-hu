---
title: Önkiszolgáló jelszó-visszaállítási telepítés – Azure Active Directory
description: Stratégia az Azure AD önkiszolgáló jelszó-visszaállítás sikeres megvalósításához
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
ms.openlocfilehash: 785a8a031a10232a37b235711ba919fdc1df35d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061417"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset"></a>Azure Active Directory önkiszolgáló jelszó-visszaállítás megtervezése

> [!NOTE]
> Ez a telepítési terv tervezési útmutatást és gyakorlati tanácsokat kínál az Azure AD önkiszolgáló jelszó-visszaállítás (SSPR) üzembe helyezéséhez. <br>**Ha keres az SSPR eszköz, hogy újra a [https://aka.ms/sspr](https://aka.ms/sspr)fiókjába, megy **.

[Az önkiszolgáló jelszó-visszaállítás (SSPR)](https://www.youtube.com/watch?v=tnb2Qf4hTP8) az Azure Active Directory (AD) egyik szolgáltatása, amely lehetővé teszi a felhasználók számára, hogy a jelszavakat anélkül alaphelyzetbe állítsák, hogy az informatikai személyzettől segítségért kapcsolatba lépnek. A felhasználók gyorsan feloldhatják a blokkolást, és folytathatják a munkát, függetlenül attól, hogy hol vannak vagy milyen napszakban vannak. Azáltal, hogy az alkalmazottak feloldhatják a blokkolásukat, a szervezet csökkentheti a nem produktív időt és a magas támogatási költségeket a jelszavakkal kapcsolatos leggyakoribb problémák esetén. 

Az SSPR a következő kulcsfontosságú képességekkel rendelkezik:

* Az önkiszolgáló szolgáltatás lehetővé teszi a végfelhasználók számára, hogy visszaállítsák lejárt vagy nem lejárt jelszavukat anélkül, hogy a rendszergazdától vagy az ügyfélszolgálattól segítségért kapcsolatba lépnek.

* [A Password Writeback](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) lehetővé teszi a helyszíni jelszavak kezelését és a fiókzárolás feloldását a felhőben.

* A jelszókezelési tevékenységjelentések betekintést nyújtanak a rendszergazdáknak a szervezetükben előforduló jelszó-visszaállítási és regisztrációs tevékenységekbe.

## <a name="learn-about-sspr"></a>További információ az SSPR-ről

További információ az SSPR-ről. Lásd: [Hogyan működik: Az Azure AD önkiszolgáló jelszó-visszaállítás.](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)

### <a name="key-benefits"></a>Főbb előnyök

Az SSPR engedélyezésének legfontosabb előnyei a következők:

* **Költség kezelése**. Az SSPR csökkenti az informatikai támogatási költségeket azáltal, hogy lehetővé teszi a felhasználók számára a jelszavak saját visszaállítását. Emellett csökkenti az elveszett jelszavak és zárolások miatt elveszett idő költségét is. 

* **Intuitív felhasználói élmény**. Ez biztosítja az intuitív egyszeri felhasználói regisztrációs folyamat, amely lehetővé teszi a felhasználók számára, hogy állítsa vissza a jelszavakat, és feloldása számlák on-demand bármilyen eszközről vagy helyről. Az SSPR lehetővé teszi a felhasználók számára, hogy gyorsabban visszatérjenek a munkába, és hatékonyabbak legyenek.

* **Rugalmasság és biztonság**. Az SSPR lehetővé teszi a vállalatok számára, hogy hozzáférjenek a felhőplatform által biztosított biztonsághoz és rugalmassághoz. A rendszergazdák módosíthatják a beállításokat, hogy megfeleljenek az új biztonsági követelményeknek, és ezeket a módosításokat a bejelentkezés megzavarása nélkül hajtsák végre a felhasználók számára.

* **Robusztus naplózás és használatkövetés**. A szervezetek biztosíthatják, hogy az üzleti rendszerek biztonságban maradjanak, miközben a felhasználók alaphelyzetbe állítják saját jelszavukat. A megbízható naplónaplók a jelszó-visszaállítási folyamat minden egyes lépésére vonatkozó információkat tartalmaznak. Ezek a naplók api-ból érhetők el, és lehetővé teszik a felhasználó számára, hogy importálja az adatokat egy választott biztonsági incidens- és eseményfigyelési (SIEM) rendszerbe.

### <a name="licensing"></a>Licencek

Az Azure Active Directory felhasználónkénti licenccel rendelkezik, ami azt jelenti, hogy minden felhasználónak szüksége van egy megfelelő licencre az általuk használt funkciókhoz. Az SSPR csoportalapú licencelését javasoljuk. 

A kiadások és funkciók összehasonlításához, valamint a csoport- vagy felhasználóalapú licencelés engedélyezéséhez olvassa el [az Azure AD önkiszolgáló jelszó-visszaállításlicencelési követelményei című témakört.](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-licensing)

A díjszabásról az [Azure Active Directory díjszabása](https://azure.microsoft.com/pricing/details/active-directory/)című témakörben talál további információt.

### <a name="prerequisites"></a>Előfeltételek

* Egy működő Azure AD-bérlő, legalább próbaverziós licenccel. Szükség esetén [hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Egy globális rendszergazdai jogosultsággal rendelkező fiók.


### <a name="training-resources"></a>Képzési források

| Források| Hivatkozás és leírás |
| - | - |
| Videók| [Tegye hatékonyabbá a felhasználókat a jobb informatikai méretezhetőséggel](https://youtu.be/g9RpRnylxS8) 
| |[Mi az önkiszolgáló jelszó-visszaállítás?](https://youtu.be/hc97Yx5PJiM)|
| |[Önkiszolgáló jelszó-visszaállítás telepítése](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Hogyan konfigurálható az önkiszolgáló jelszó-visszaállítás a felhasználók számára az Azure AD-ben?](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[[Felkészülés a felhasználókra] az Azure Active Directory biztonsági adatainak regisztrálására?](https://youtu.be/gXuh0XS18wA) |
| Online tanfolyamok|[Identitások kezelése a Microsoft Azure Active Directoryban](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) Az SSPR segítségével modern, védett élményt nyújt a felhasználóknak. Lásd különösen a "[Kezelése Az Azure Active Directory felhasználói és csoportjai](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)" modul. |
|Pluralsight Fizetett tanfolyamok |[Az identitás- és hozzáférés-kezelés kérdései](https://www.pluralsight.com/courses/identity-access-management-issues) Ismerje meg az IAM-et és a szervezetben figyelembe veendő biztonsági problémákat. Lásd különösen az "Egyéb hitelesítési módszerek" modult.|
| |[A Microsoft Enterprise Mobility Suite – első lépések](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) Ismerje meg a helyszíni eszközök felhőre való kiterjesztésének ajánlott gyakorlatait olyan módon, amely lehetővé teszi a hitelesítést, az engedélyezést, a titkosítást és a biztonságos mobilélményt. Tekintse meg különösen a "Microsoft Azure Active Directory Premium speciális szolgáltatásainak konfigurálása" modult.
|Oktatóanyagok |[Azure AD önkiszolgáló jelszó-visszaállítási próbaüzem bevezetésének befejezése](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) |
| |[Jelszó-visszaírás engedélyezése](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback) |
| |[Az Azure AD jelszó visszaállítása a Windows 10 bejelentkezési képernyőjéről](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows) |
| GYIK|[A jelszókezelés gyakori kérdései](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) |


### <a name="solution-architecture"></a>Megoldásarchitektúra

A következő példa a jelszó-visszaállítási megoldás architektúráját ismerteti a közös hibrid környezetekben.

![megoldásarchitektúra diagramja](./media/howto-sspr-deployment//solutions-architecture.png)

A munkafolyamat leírása

A jelszó alaphelyzetbe állításához a felhasználók a [jelszó-visszaállítási portálra](https://aka.ms/sspr)lépnek. Személyazonosságuk igazolásához ellenőrizniük kell a korábban regisztrált hitelesítési módszert vagy módszereket. Ha sikeresen alaphelyzetbe állítják a jelszót, megkezdik az alaphelyzetbe állítási folyamatot.

* Csak felhőalapú felhasználók számára az SSPR tárolja az új jelszót az Azure AD-ben. 

* A hibrid felhasználók számára az SSPR visszaírja a jelszót az on-prem Active Directoryba az Azure AD Connect szolgáltatáson keresztül. 

Megjegyzés: Azoknak a felhasználóknak, akik [a jelszókivonat-szinkronizálás (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) le van tiltva, az SSPR csak az on-prem Active Directoryban tárolja a jelszavakat.

### <a name="best-practices"></a>Ajánlott eljárások

A felhasználók gyors regisztrációját segítheti az SSPR telepítésével a szervezet egy másik népszerű alkalmazása vagy szolgáltatása mellett. Ez a művelet nagy mennyiségű bejelentkezést hoz létre, és regisztrációt eredményez.

Az SSPR telepítése előtt dönthet úgy, hogy meghatározza az egyes jelszó-visszaállítási hívások számát és átlagos költségét. Használhatja ezt az adatokat telepítés után, hogy megmutassa az sspr által a szervezet számára.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>Kombinált regisztráció engedélyezése az SSPR és az MFA számára

A Microsoft azt javasolja, hogy a szervezetek engedélyezze az SSPR és a többtényezős hitelesítés kombinált regisztrációs élményét. Ha engedélyezi ezt a kombinált regisztrációs élményt, a felhasználóknak csak egyszer kell kiválasztaniuk a regisztrációs adataikat mindkét funkció engedélyezéséhez.

A kombinált regisztrációs élmény nem követeli meg a szervezetek számára, hogy mind az SSPR, mind az Azure többtényezős hitelesítést engedélyezze. A kombinált regisztráció jobb felhasználói élményt nyújt a szervezeteknek. További információ: [Kombinált biztonsági adatok regisztrálása (előzetes verzió)](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-deployment-project"></a>A telepítési projekt megtervezése

Vegye figyelembe a szervezeti igényeket, amíg meghatározza a környezetben a központi telepítés stratégiáját.

### <a name="engage-the-right-stakeholders"></a>Vonja be a megfelelő érdekelt feleket

Ha a technológiai projektek kudarcot vallanak, általában a hatásra, az eredményekre és a felelősségre vonatkozó, nem megfelelő elvárások miatt teszik ezt. E buktatók elkerülése érdekében [győződjön meg arról, hogy a megfelelő érdekelt feleket vonja be,](https://aka.ms/deploymentplans) és hogy az érdekelt felek szerepeit a projektben jól megértjük az érdekelt felek, valamint a projekt bemenetének és elszámoltathatóságának dokumentálása.

#### <a name="required-administrator-roles"></a>Szükséges rendszergazdai szerepkörök


| Üzleti szerepkör/személyiség| Azure AD-szerepkör (ha szükséges) |
| - | - |
| 1. szintű ügyfélszolgálat| Jelszókezelő |
| 2. szintű ügyfélszolgálat| Rendszergazda |
| SSPR-rendszergazda| Globális rendszergazda |


### <a name="plan-communications"></a>A kommunikáció tervezése

A kommunikáció minden új szolgáltatás sikeréhez elengedhetetlen. Proaktív módon kommunikáljon a felhasználókkal arról, hogy a felhasználói élményük hogyan változik, mikor fog változni, és hogyan szerezhet támogatást, ha problémákat tapasztalnak. Tekintse át az [önkiszolgáló jelszó-visszaállítási bevezetési anyagokat a Microsoft letöltőközpontjában,](https://www.microsoft.com/download/details.aspx?id=56768) és olvassa el a végfelhasználói kommunikációs stratégia megtervezésére vonatkozó ötleteket.

### <a name="plan-a-pilot"></a>Pilóta megtervezése

Azt javasoljuk, hogy az SSPR kezdeti konfigurációja tesztkörnyezetben legyen. Kezdje egy próbacsoporttal, ha engedélyezi az SSPR-t a szervezet felhasználóinak egy részhalmaza számára. Lásd: [Gyakorlati tanácsok a pilótához.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans)

Csoport létrehozásához tekintse meg, hogyan [hozhat létre csoportot, és hogyan vehet fel tagokat az Azure Active Directoryban.](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal) 

## <a name="plan-configuration"></a>Konfiguráció megtervezése

Az SSPR és az ajánlott értékek engedélyezéséhez a következő beállítások szükségesek.

| Terület | Beállítás | Érték |
| --- | --- | --- |
| **SSPR tulajdonságai** | Önkiszolgáló jelszó-visszaállítás engedélyezve | **Kiválasztott** csoport a pilothoz / **Minden** a termeléshez |
| **Hitelesítési módszerek** | A regisztrációhoz szükséges hitelesítési módszerek | Mindig 1-vel több, mint ami az alaphelyzetbe állításhoz szükséges |
|   | Az alaphelyzetbe állításhoz szükséges hitelesítési módszerek | Egy vagy kettő |
| **Regisztráció** | Szükséges a felhasználóknak regisztrálniuk a bejelentkezéskor? | Igen |
|   | A napok száma, amely előtt a rendszer kéri a felhasználóktól a hitelesítési adataik ismételt megerősítését | 90 – 180 nap |
| **Értesítések** | Értesítse a felhasználókat új jelszó kérésekor? | Igen |
|   | Minden rendszergazda kapjon értesítést, ha más rendszergazdák új jelszót kérnek? | Igen |
| **Testreszabás** | Az ügyfélszolgálat hivatkozásának testreszabása | Igen |
|   | Egyéni ügyfélszolgálati e-mail vagy URL | Támogatási webhely vagy e-mail cím |
| **Helyszíni integráció** | Jelszavak visszaírása a helyszíni AD-hez | Igen |
|   | A fiók zárolásának feloldása jelszó alaphelyzetbe állítása nélkül a felhasználók számára | Igen |

### <a name="sspr-properties"></a>SSPR tulajdonságai

Az SSPR engedélyezésekor válassza ki a megfelelő biztonsági csoportot a próbakörnyezetben.

* Az SSPR regisztráció mindenki számára történő érvényesítéséhez javasoljuk az **Összes** beállítást.
* Ellenkező esetben válassza ki a megfelelő Azure AD vagy AD biztonsági csoportot.

### <a name="authentication-methods"></a>Hitelesítési módszerek

Ha az SSPR engedélyezve van, a felhasználók csak akkor állíthatják alaphelyzetbe a jelszavukat, ha a rendszergazda által engedélyezett hitelesítési módszerekben adatok szerepelnek. Módszerek közé tartozik a telefon, Hitelesítő app értesítés, biztonsági kérdések, stb További információ: [Mik a hitelesítési módszerek?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

A hitelesítési módszerek alábbi beállításait javasoljuk:

* Állítsa be a **regisztrációhoz szükséges hitelesítési módszereket** legalább eggyel nagyobbra, mint az alaphelyzetbe állításhoz szükséges szám. A több hitelesítés engedélyezése rugalmasságot biztosít a felhasználóknak, amikor alaphelyzetbe kell állítaniőket.

* Állítsa be a szervezetnek megfelelő szintre **való visszaállításhoz szükséges metódusok számát.** Az egyik a legkisebb súrlódást igényli, míg kettő növelheti a biztonsági pozíciót. 

Megjegyzés: A felhasználónak rendelkeznie kell a hitelesítési módszerek konfigurálva a jelszó házirendek és korlátozások az [Azure Active Directoryban.](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)

### <a name="registration-settings"></a>Regisztrációs beállítások

Állítsa be **A felhasználók regisztrációjának megkövetelése az** **Igen**beállításra. Ehhez a beállításhoz a felhasználóknak regisztrálniuk kell a bejelentkezéskor, biztosítva, hogy minden felhasználó védett legyen.

Beállítás: A felhasználók hitelesítési adatainak **90** és **180** nap közötti **újbóli megerősítésére felkért napok száma,** kivéve, ha a szervezetnek rövidebb időkeretre van szüksége.

### <a name="notifications-settings"></a>Értesítések beállításai

Konfigurálja a **Felhasználók értesítése a jelszó-visszaállításkor** és az **Értesítés az összes rendszergazdának, ha más rendszergazdák visszaállítják a jelszavukat** **Igen**jelszóra. Ha mindkét esetben az **Igen** lehetőséget választja, azzal növeli a biztonságot, hogy a felhasználók tisztában lesznek a jelszó alaphelyzetbe állításával. Azt is biztosítja, hogy minden rendszergazda tisztában legyen, ha egy rendszergazda módosítja a jelszót. Ha a felhasználók vagy a rendszergazdák értesítést kapnak, és nem ők kezdeményezték a módosítást, azonnal jelenthetik a lehetséges biztonsági problémát.

### <a name="customization-settings"></a>Testreszabási beállítások

Fontos, hogy testre szabja az ügyfélszolgálate e-mail címét vagy URL-címét, hogy a problémákat tapasztaló felhasználók azonnal segítséget kapjanak. Állítsa ezt a beállítást egy olyan gyakori ügyfélszolgálati e-mail címre vagy weblapra, amelyet a felhasználók ismernek. 

További információ: [Az Azure AD funkció testreszabása az önkiszolgáló jelszó-visszaállításhoz.](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

### <a name="password-writeback"></a>Jelszó visszaírása

**A Password Writeback** engedélyezve van az [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity) szolgáltatással, és valós időben visszaírja a felhőben visszaállítja a jelszó-visszaállításokat egy meglévő helyszíni könyvtárba. További információ: [Mi a jelszó-visszaírás?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

A következő beállításokat javasoljuk:

* Győződjön meg arról, hogy **a Jelszavak írása a helyszíni AD-hez** **Igen**beállítással van beállítva. 
* Állítsa be a **Fiók zárolásának engedélyezése a felhasználók számára a jelszó alaphelyzetbe állítása nélkül** **az Igen**beállítást.

Alapértelmezés szerint az Azure AD feloldja a fiókok, amikor jelszó-visszaállítás t hajt végre.

### <a name="administrator-password-setting"></a>Rendszergazdai jelszó beállítása

A rendszergazdai fiókok emelt szintű engedélyekkel rendelkeznek. A helyszíni vállalati vagy tartományi rendszergazdák nem állíthatja alaphelyzetbe a jelsta- sspr-en keresztül. A helyszíni rendszergazdai fiókokra a következő korlátozások vonatkoznak:

* csak a saját környezetükben módosíthatják a jelszavukat.
* soha nem használhatja a titkos kérdéseket és válaszokat, mint a jelszó visszaállításának módszerét.

Azt javasoljuk, hogy ne szinkronizálja a helyszíni Active Directory-rendszergazdai fiókokat az Azure AD-vel.

### <a name="environments-with-multiple-identity-management-systems"></a>Több identitáskezelő rendszerrel rendelkező környezetek

Egyes környezetek több identitáskezelő rendszerrel rendelkeznek. On-premesis identitás kezelők, mint az Oracle AM és SiteMinder, szinkronizálást igényelnek az AD jelszavakat. Ezt a Microsoft Identity Manager (MIM) segítségével a Jelszómódosítási értesítési szolgáltatás (PCNS) segítségével teheti meg. Az összetettebb forgatókönyvvel kapcsolatos információkért olvassa el [a MIM jelszómódosítási értesítési szolgáltatás tartományvezérlőre való telepítése](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)című témakört.

## <a name="plan-testing-and-support"></a>Tervtesztelés és -támogatás

A kezdeti kísérleti csoportoktól a szervezeti szintű üzembe helyezés minden szakaszában győződjön meg arról, hogy az eredmények a várt módon teljesülnek.

### <a name="plan-testing"></a>Terv tesztelése

Annak érdekében, hogy a központi telepítés a várt módon működjön, tervezzen egy sor tesztesetek a megvalósítás érvényesítéséhez. A tesztesetek felméréséhez nem rendszergazdai tesztfelhasználóra van szüksége jelszóval. Ha felhasználót kell létrehoznia, olvassa el az Új felhasználók hozzáadása az Azure Active Directoryhoz című [témakört.](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)

Az alábbi táblázat hasznos tesztforgatókönyveket tartalmaz, amelyekkel dokumentálhatja a szervezetek várt eredményeit a szabályzatok alapján.
<br>


| Üzleti eset| Várt eredmények |
| - | - |
| Az SSPR portál a vállalati hálózaton belülről érhető el| A szervezet határozza meg |
| Az SSPR portál a vállalati hálózaton kívülről érhető el| A szervezet határozza meg |
| Felhasználói jelszó alaphelyzetbe állítása a böngészőből, ha a felhasználó nincs engedélyezve a jelszó-visszaállításhoz| A felhasználó nem tudja elérni a jelszó-visszaállítási folyamatot |
| Felhasználói jelszó alaphelyzetbe állítása a böngészőből, ha a felhasználó nem regisztrált jelszó-visszaállításra| A felhasználó nem tudja elérni a jelszó-visszaállítási folyamatot |
| A felhasználó bejelentkezik, amikor jelszó-visszaállítási regisztrációra kényszerítik| A felhasználó biztonsági adatok regisztrálásának kérése |
| A felhasználó bejelentkezik, ha a jelszó-visszaállítási regisztráció befejeződött| A felhasználó biztonsági adatok regisztrálásának kérése |
| Az SSPR-portál akkor érhető el, ha a felhasználó nem rendelkezik licenccel| Elérhető |
| Felhasználói jelszó alaphelyzetbe állítása a Windows 10 Azure AD-hez vagy hibrid Azure AD-hez csatlakozott eszközzárolási képernyőről| A felhasználó alaphelyzetbe állíthatja a jelszót |
| Az SSPR regisztrációs és használati adatai közel valós időben állnak a rendszergazdák rendelkezésére| Elérhető a naplónaplókon keresztül |

Az [Azure AD önkiszolgáló jelszó-visszaállítási próbarendszeri névjegyzék befejezése](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot)című dokumentumban is hivatkozhat. Ebben az oktatóanyagban engedélyezi az SSPR próbabevezetését a szervezetben, és nem rendszergazdai fiókkal teszteli a tesztet.

### <a name="plan-support"></a>Támogatás megtervezése

Bár az SSPR általában nem okoz felhasználói problémákat, fontos felkészíteni a támogató személyzetet az esetlegesen felmerülő problémák kezelésére. Bár a rendszergazda az Azure AD-portálon keresztül alaphelyzetbe állíthatja a végfelhasználók jelszavát, érdemes egy önkiszolgáló támogatási folyamaton keresztül megoldani a problémát.

A támogatási csapat sikerének érdekében létrehozhat egy gyIK-et a felhasználóktól kapott kérdések alapján. Íme, néhány példa:

| Forgatókönyvek| Leírás |
| - | - |
| A felhasználó nem rendelkezik regisztrált hitelesítési módszerekkel| A felhasználó megpróbálja visszaállítani a jelszavát, de nem rendelkezik az általa regisztrált hitelesítési módszerekkel (például: otthon hagyta a mobiltelefonját, és nem tud hozzáférni az e-mailekhez) |
| A felhasználó nem kap sms-t vagy hívást az irodájában vagy a mobiltelefonján| A felhasználó sms-ben vagy hívással próbálja ellenőrizni a személyazonosságát, de nem kap sms-t/hívást. |
| A felhasználó nem tud hozzáférni a jelszó-visszaállítási portálhoz| A felhasználó vissza szeretné állítani a jelszavát, de nincs engedélyezve a jelszó-visszaállítás, és nem tudja elérni a lapot a jelszavak frissítéséhez. |
| A felhasználó nem tud új jelszót beállítani| A felhasználó befejezi az ellenőrzést a jelszó-visszaállítási folyamat során, de nem tud új jelszót beállítani. |
| A felhasználó nem látja a Jelszó alaphelyzetbe állítása hivatkozást windows 10-es eszközön| Egy felhasználó megpróbálja alaphelyzetbe állítani a jelszót a Windows 10 zárolási képernyőjéről, de az eszköz vagy nem csatlakozik az Azure AD-hez, vagy az Intune-eszközházirend nincs engedélyezve |

### <a name="plan-roll-back"></a>Terv visszaállítása

A telepítés visszaállítása:

* egyetlen felhasználó esetén távolítsa el a felhasználót a biztonsági csoportból 

* csoport esetén távolítsa el a csoportot az SSPR-konfigurációból

* Mindenki számára tiltsa le az SSPR-t az Azure AD-bérlőhöz

## <a name="deploy-sspr"></a>Az SSPR üzembe helyezése

A telepítés előtt győződjön meg arról, hogy elvégezte az alábbiakat:

1. A [kommunikációs terv](#plan-communications)létrehozása és végrehajtása.

1. Meghatározta a megfelelő [konfigurációs beállításokat](#plan-configuration).

1. Azonosította a [felhasználókat](#plan-a-pilot) és csoportokat a kísérleti és az éles környezetben.

1. [Meghatározott konfigurációs beállítások](#plan-configuration) a regisztrációhoz és az önkiszolgálóhoz.

1. [Konfigurált jelszó-visszaírás,](#password-writeback) ha hibrid környezettel rendelkezik.


**Most már készen áll az SSPR telepítésére!**

Az [önkiszolgáló jelszó-visszaállítás engedélyezése](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot#enable-self-service-password-reset) című témakörben részletesen részletes útmutatást nyújt a következő területek konfigurálása.

1. [Hitelesítési módszerek](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

1. [Regisztrációs beállítások](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

1. [Értesítések beállításai](#notifications-settings)

1. [Testreszabási beállítások](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

1. [Helyszíni integráció](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback)

### <a name="enable-sspr-in-windows"></a>SSPR engedélyezése a Windows ban
Windows 7, 8, 8.1 és 10 rendszert futtató gépek esetén engedélyezheti a [felhasználóknak, hogy visszaállítsák jelszavukat a Windows bejelentkezési képernyőjén](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-windows)

## <a name="manage-sspr"></a>SSPR kezelése

Az Azure AD további információkat adhat az SSPR teljesítményéről a naplózások és jelentések révén.

### <a name="password-management-activity-reports"></a>Jelszókezelési tevékenységjelentések 

Az SSPR teljesítményének méréséhez használhatja az Azure Portalon előre elkészített jelentéseket. Ha megfelelő licenccel rendelkezik, egyéni lekérdezéseket is létrehozhat. További információ: [Reporting options for Azure AD password management](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)

> [!NOTE]
>  [Globális rendszergazdának](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)kell lennie, és engedélyeznie kell, hogy ezeket az adatokat összegyűjtsék a szervezet számára. A feliratkozáshoz legalább egyszer meg kell látogatnia a Jelentés lapot vagy az Azure Portalon a naplónaplókat. Addig az adatok nem gyűjtenek a szervezet számára.

A regisztrációés a jelszó visszaállítása naplói 30 napig állnak rendelkezésre. Ha a vállalaton belüli biztonsági naplózás hosszabb megőrzést igényel, a naplókat exportálni kell, és egy SIEM eszközbe, például [az Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-active-directory), Splunk vagy ArcSight-ba kell felhasználni.

![SSPR-jelentésképernyő](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>Hitelesítési módszerek - Használat és elemzések

[A használat és az elemzések](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights) segítségével megismerheti, hogyan működnek az Azure MFA és az SSPR funkciók hitelesítési módszerei a szervezetben. Ez a jelentéskészítési funkció biztosítja a szervezet számára az eszközöket, hogy megértse, milyen módszerek regisztrálják, és hogyan kell használni őket.

### <a name="troubleshoot"></a>Hibaelhárítás

* Lásd: [Önkiszolgáló jelszó-visszaállítás – problémamegoldás](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-troubleshoot) 

* Kövesse [a jelszókezelést gyakori kérdések](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) 

### <a name="helpful-documentation"></a>Hasznos dokumentáció

* [Mik a hitelesítési módszerek?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

* [Hogyan működik: Az Azure AD önkiszolgáló jelszó-visszaállítás?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)

* [Az Azure AD funkció testreszabása az önkiszolgáló jelszó-visszaállításhoz](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

* [Jelszószabályzatok és -korlátozások az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)

* [Mi az a jelszó-visszaírás?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

## <a name="next-steps"></a>További lépések

* Az SSPR üzembe helyezésének megkezdéséhez olvassa [el az Azure AD önkiszolgáló jelszó-visszaállítási próbabevezetésének befejezése című témakört.](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot)

* [Fontolja meg az Azure AD jelszavas védelem bevezetését](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)

* [Fontolja meg az Azure AD Intelligens zárolás ának megvalósítását](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)