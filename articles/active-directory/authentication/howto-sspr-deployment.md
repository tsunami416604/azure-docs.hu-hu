---
title: Az önkiszolgáló jelszó-visszaállítási központi telepítési csomag – Azure Active Directory
description: Stratégiát sikeres megvalósítását az Azure AD önkiszolgáló jelszó alaphelyzetbe állítása
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b566bfc3f6c49f6cb9fe31f166356f6ae351e38
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440942"
---
# <a name="deploy-azure-ad-self-service-password-reset"></a>Üzembe helyezni az Azure AD önkiszolgáló jelszó-visszaállítás

Az önkiszolgáló jelszó-visszaállítás (SSPR) az Azure Active Directory szolgáltatása, amely lehetővé teszi, hogy új jelszót kérjenek, anélkül, hogy lépjen kapcsolatba az informatikai részleg alkalmazottai. Az alkalmazottak regisztrálnia kell, vagy az önkiszolgáló jelszó-visszaállítást a szolgáltatás használata előtt regisztrálnia. Regisztráció során az alkalmazott úgy dönt, a szervezet által engedélyezett egy vagy több hitelesítési módszereket.

SSPR lehetővé teszi, hogy az alkalmazottak gyorsan le nem tiltott lekérése, és folytathatja a munkát, függetlenül attól, hogy hol vannak, vagy a napi időpontot. Azáltal, hogy a felhasználók maguk feloldásának, a szervezet csökkentheti a nem hatékony idő és a jelszóval kapcsolatos problémák leggyakoribb magas támogatási költségek.

Segítség a felhasználóknak az SSPR együtt egy másik alkalmazást vagy szolgáltatást a szervezetében üzembe helyezésével gyorsan regisztráláshoz. Ez a művelet bejelentkezések nagy mennyiségű hoz létre, és a hatékony felhasználhatóságot regisztráció.

Az SSPR üzembe helyezése előtt a szervezeteknek érdemes a határozza meg, hogy hány új jelszó kérésére vonatkozó kapcsolódó segélyszolgálatra beérkező hívások történik idő és a egy hívás átlagos költsége. Ezen adatok üzembe helyezés után használhatják az SSPR nemében szervezet értéket jeleníti meg.  

## <a name="how-sspr-works"></a>Az SSPR működése

1. Amikor egy felhasználó megpróbál jelszó alaphelyzetbe állítása, azok igazolnia kell a korábban regisztrált hitelesítési módszert vagy módszereket kell igazolnia az identitását.
1. Ezután a felhasználó beírja az új jelszót.
   1. Az új jelszó csak felhőbeli felhasználók, az Azure Active Directoryban van tárolva. További információkért tekintse meg a cikket [hogyan SSPR működik](concept-sspr-howitworks.md#how-does-the-password-reset-portal-work).
   1. A hibrid felhasználók számára a jelszó rendszer visszaírja a helyszíni Active Directoryval, az Azure AD Connect szolgáltatáson keresztül. További információkért tekintse meg a cikket [jelszóvisszaíró](concept-sspr-writeback.md#how-password-writeback-works).

## <a name="licensing-considerations"></a>Licencelési szempontok

Az Azure Active Directory-funkciók azok használatához szükséges licenccel kell rendelkeznie minden felhasználó rendelkezik licenccel felhasználónkénti jelentése.

- Az önkiszolgáló jelszó-visszaállítás csak felhőbeli felhasználók az Azure AD alapszintű vagy annál újabb érhető el.
- Az önkiszolgáló jelszó-visszaállítást a helyszíni visszaírási hibrid környezetekben szükséges Azure AD Premium P1 vagy újabb.

Licenceléssel kapcsolatos további információk találhatók a [Azure Active Directory díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="enable-combined-registration-for-sspr-and-mfa"></a>Egyesített regisztrációs SSPR és az MFA engedélyezése

A Microsoft azt javasolja, hogy a szervezetek a kombinált regisztrációs felület SSPR és a multi-factor authentication szolgáltatás engedélyezéséhez. Ha engedélyezi az egyesített regisztrációs felületet, felhasználók csak ki kell választania a regisztrációs adatok egyszer mindkét funkció engedélyezéséhez.

![Egyesített biztonsági információ regisztrálása](./media/howto-sspr-deployment/combined-security-info.png)

A kombinált regisztrációs élmény nem igényel a szervezetek számára, hogy engedélyezze az SSPR és a Azure multi-factor Authentication használatára. A kombinált regisztrációs élményt biztosít a szervezetek a hagyományos egyes összetevők képest jobb felhasználói élményt. További információ a kombinált regisztrációs és engedélyezése, a cikkben található [kombinált biztonsági információk regisztrációs (előzetes verzió)](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-configuration"></a>A konfiguráció tervezése

SSPR engedélyezése és ajánlott értékek a következő beállítások szükségesek.

| Terület | Beállítás | Érték |
| --- | --- | --- |
| **SSPR-tulajdonságok** | Önkiszolgáló jelszóátállítás engedélyezve | **Kiválasztott** próbaüzem csoport / **összes** éles környezetben |
| **Hitelesítési módszerek** | Hitelesítési módszer regisztrálásához szükséges | Mindig 1 több, mint a visszaállításhoz szükséges |
|   | Új jelszó kéréséhez szükséges hitelesítési módszerek | Egy vagy két |
| **Regisztráció** | Szükséges a felhasználóknak regisztrálniuk a bejelentkezéskor? | Igen |
|   | Hány nap elteltével a rendszer kéri a felhasználóktól a hitelesítési adataik ismételt megerősítését | 90 – 180 nap |
| **Értesítések** | Értesítse a felhasználókat új jelszó kérésekor? | Igen |
|   | Minden rendszergazda kapjon értesítést, ha más rendszergazdák új jelszót kérnek? | Igen |
| **Testreszabás** | Ügyfélszolgálati hivatkozás testreszabása | Igen |
|   | Egyéni ügyfélszolgálati e-mail cím vagy URL-címe | Támogatási webhely vagy e-mail-cím |
| **Helyszíni integráció** | Visszaírja a jelszavakat a helyszíni AD | Igen |
|   | Fiók zárolásának feloldása új jelszó kérése nélküli felhasználók | Igen |

### <a name="sspr-properties-recommendations"></a>SSPR tulajdonságok javaslatok

Amikor engedélyezi az önkiszolgáló jelszó-visszaállítás, válassza ki egy biztonsági csoportot a próbaüzem során használt.

Ha azt tervezi, indítsa el a szolgáltatás szélesebb körben, SSPR kényszeríteni a szervezet minden tagja számára az összes beállítás használatát javasoljuk. Az összes, válassza ki azt a megfelelő Azure AD biztonsági csoportot vagy AD-csoport szinkronizált Azure ad-ben, állítson be.

### <a name="authentication-methods"></a>Hitelesítési módszerek

Állítsa be legalább egy további, mint az új jelszó kéréséhez szükséges regisztrálásához szükséges hitelesítési módszereket. Lehetővé teszi több rugalmasságot biztosít felhasználók visszaállítása szükség.

Állítsa be **új jelszó kéréséhez szükséges módszerek száma** a szervezet számára megfelelő szintre. Egy szükséges a legalacsonyabb fennakadások nélkül használható, míg két növelhető meg biztonsági állapotát.

Lásd: [hitelesítési módszerei](concept-authentication-methods.md) részletes információk a hitelesítési módszerek érhetők el az SSPR, előre definiált biztonsági kérdések, és hogyan hozhat létre egyéni biztonsági kérdések.

### <a name="registration-settings"></a>Regisztrációs beállítások

Állítsa be **szükséges a felhasználóknak regisztrálniuk a bejelentkezéskor** való **Igen**. Ez a beállítás azt jelenti, hogy a felhasználók kényszerítve regisztrálja a szolgáltatásba, biztosítva, hogy minden felhasználó védettek.

Állítsa be **hány nap elteltével a rendszer kéri a felhasználóktól a hitelesítési adataik ismételt megerősítését** való között **90** és **180** nap, kivéve, ha a szervezete üzleti kell rövidebb idő keret.

### <a name="notifications-settings"></a>Értesítésbeállítások

Mindkét a **értesítse a felhasználókat új jelszó kérésekor** és a **minden rendszergazda kapjon értesítést, ha más rendszergazdák új jelszót kérnek** való **Igen**. Kiválasztásával **Igen** is növeli a biztonságot úgy, hogy, hogy felhasználók tisztában legyenek-e a jelszó vissza lett állítva, és a jelszót módosít, hogy az összes rendszergazda ismeri, amikor egy rendszergazda. Ha a felhasználók vagy rendszergazdák ilyen értesítést kapni, és azok nem kezdeményezett a módosítást, azok azonnal jelentheti a potenciális biztonsági incidensek.

### <a name="customization"></a>Testreszabás

Fontos, hogy testre szabhatja a **segélyszolgálat e-mail vagy URL-cím** annak biztosítása érdekében a felhasználók, akik a problémák gyorsan segítséget. Állítsa be ezt a lehetőséget egy közös ügyfélszolgálat e-mail címe vagy weblap, amelyen a felhasználók ismeri.

### <a name="on-premises-integration"></a>Helyszíni integráció

Ha hibrid környezettel rendelkezik, győződjön meg arról, hogy **visszaírja a jelszavakat a helyszíni AD** értékre van állítva **Igen**. A fiók feloldása nélkül Igen, a jelszó alaphelyzetbe állítása, mivel ez nagyobb rugalmasságot biztosít számukra engedélyezése a felhasználók is beállíthat.

### <a name="changingresetting-passwords-of-administrators"></a>A rendszergazdák jelszavak módosítása/alaphelyzetbe állítása

Rendszergazdai fiókok olyan speciális emelt szintű engedélyekkel rendelkező fiókok. A védelmük, a következő korlátozások vonatkoznak a rendszergazdák jelszó módosítása:

- A helyszíni vállalati rendszergazdák vagy tartományi rendszergazdák nem állítható alaphelyzetbe a jelszavát, SSPR keresztül. Csak a helyszíni környezetben jelszó módosításához. Ezért ajánlott nem szinkronizálja a helyszíni AD-rendszergazdai fiókok az Azure ad-hez.
- A rendszergazda nem titkos kérdések és válaszok módszerként használhatják a jelszó alaphelyzetbe állítása.

### <a name="environments-with-multiple-identity-management-systems"></a>Több identitáskezelő-rendszer rendelkező környezetek

Ha több identitás felügyeleti rendszerekkel, mint például az Oracle AM a helyszíni identitás-kezelők a környezeten belül, SiteMinder, vagy más rendszerek, és a jelszavakat az Active Directory írt előfordulhat, hogy módosítania kell a más rendszerekhez hasonló eszköz használatával történő szinkronizálását a jelszóváltozás-értesítési szolgáltatás (jelszóváltoztatás-értesítési szolgáltatás) a Microsoft Identity Manager (MIM). Ez a forgatókönyv összetettebb információkat talál a cikk [központi telepítése a MIM jelszóváltozás-értesítési szolgáltatás egy tartományvezérlőn](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-deployment-and-support-for-sspr"></a>Üzembe helyezés és a támogatás az SSPR megtervezése

### <a name="engage-the-right-stakeholders"></a>A jobb oldali érdekelt felek léphet.

Technológiai projektek sikertelen lesz, amikor általában ehhez a hatás, kimeneteket és felelősségek eltérő elvárások miatt. Ezek alkalmazásmegoldásokra elkerülése érdekében győződjön meg arról, vonzó vannak a megfelelő érintettek, hogy a projekt érdekelt szerepkörök jól értelmezni tud az érdekelt felek és a projekt bemenet és felelősségre vonhatóság dokumentálásáért.

### <a name="communications-plan"></a>Kommunikációs terv

Minden olyan új szolgáltatás sikeres kommunikáció. Proaktív módon tájékoztassa a felhasználókat a szolgáltatás használata, és mit tehet kaphat segítséget, ha valami nem működik az elvárásoknak megfelelően. Tekintse át a [önkiszolgáló jelszó-visszaállítási bevezetési anyagok a Microsoft letöltőközpontban](https://www.microsoft.com/download/details.aspx?id=56768) ötleteket találhat a végfelhasználói kommunikáció stratégiájának tervezése a.

### <a name="testing-plan"></a>Tesztelési terv

Győződjön meg arról, hogy a központi telepítés a várt módon működik-e, meg kell terveznie a megvalósítás ellenőrzése használandó esetek készletét ki. A következő táblázat néhány hasznos Tesztelési forgatókönyvek segítségével a szervezetek várt eredményei alapján a házirendek dokumentum tartalmaz.

| Vállalkozását | Várt eredmény |
| --- | --- |
| SSPR-portálon érhető el a vállalati hálózaton belül | A szervezet által meghatározott |
| SSPR-portálon érhető el a vállalati hálózaton kívül | A szervezet által meghatározott |
| A böngésző felhasználói jelszó alaphelyzetbe állítása, amikor a felhasználó nincs engedélyezve a jelszó-visszaállításhoz | Felhasználói nem érhetik el a jelszóvisszaállítási folyamatot |
| A böngésző felhasználói jelszó alaphelyzetbe állítása, amikor a felhasználó nincs regisztrálva a jelszó-visszaállításhoz | Felhasználói nem érhetik el a jelszóvisszaállítási folyamatot |
| Amikor jelszó-átállítási regisztrációk felhasználó bejelentkezik kényszerítése | Felhasználó kéri, hogy regisztrálja biztonsági információit |
| Felhasználó jelentkezik be, hogy amikor jelszó alaphelyzetbe állításának regisztrációja befejeződött. | Nem kéri a felhasználót, hogy regisztrálja biztonsági információit |
| Ha a felhasználó nem rendelkezik licenccel érhető el az SSPR-portál | Érhető el |
| A felhasználó jelszava a Windows 10-es AADJ vagy H + AADJ eszköz zárolási képernyőjén alaphelyzetbe, miután a felhasználó rendelkezik-e regisztrálva | Felhasználói jelszó alaphelyzetbe állítása |
| SSPR regisztrációs és használati adatok érhetők el a rendszergazdák közel valós időben | Auditnaplók keresztül érhető el |

### <a name="support-plan"></a>Támogatási csomag

Bár az SSPR általában nem létrehozni a felhasználói problémák, fontos támogató személyzet előkészített foglalkozik az esetleg felmerülő problémákat.

Amíg a rendszergazda is módosíthatja, vagy alaphelyzetbe állíthatja a jelszót a végfelhasználók számára az Azure AD portálon keresztül, akkor célszerűbb egy önkiszolgáló támogatási folyamaton keresztül a probléma megoldásához.

Ez a dokumentum, a üzemeltetési útmutató területen hozzon létre egy támogatási esetek és azok lehetséges oka a, és hozzon létre egy útmutatót a feloldásához.

### <a name="auditing-and-reporting"></a>Naplózás és jelentéskészítés

Az üzembe helyezést követően számos szervezet szeretné tudni, hogy hogyan valóban használja, vagy ha az önkiszolgáló jelszó-visszaállítási (SSPR). A jelentéskészítési funkció az Azure Active Directory (Azure AD) biztosít segít előre elkészített jelentéseket a kérdésre.

Auditnaplók regisztrálása és a jelszó-visszaállítás 30 napig érhetők el. Ezért, ha hosszabb adatmegőrzés megadásához biztonsági naplózás egy vállalat belül van szüksége, a naplók kell exportálhatók és a egy SIEM-eszközével, például a felhasznált [Azure Sentinel-](../../sentinel/connect-azure-active-directory.md), Splunk vagy ArcSight.

Egy tábla hasonló az alábbi a dokumentum a biztonsági mentési ütemezést, a rendszer és a felelős felek. Előfordulhat, hogy nem kell külön, naplózás és jelentéskészítés a biztonsági mentések, de rendelkeznie kell egy külön biztonsági mentés, amelyből helyre lehet állítani egy problémát.

|   | Letöltési gyakorisága | Célrendszer | Felelős fél |
| --- | --- | --- | --- |
| Naplózási biztonsági mentés |   |   |   |
| Jelentéskészítés a biztonsági mentés |   |   |   |
| Vész-helyreállítási biztonsági mentés |   |   |   |

## <a name="implementation"></a>Megvalósítás

Megvalósítás három lépésben hajtaná történik:

- A felhasználók és licencek konfigurálása
- Az Azure AD SSPR regisztrációs és önkiszolgáló konfigurálása
- Az Azure AD Connect a jelszóvisszaírás konfigurálása

### <a name="communicate-the-change"></a>A módosítás kommunikációhoz

A kommunikációs terv, amely a tervezési fázisban fejlesztett végrehajtásának kezdete.

### <a name="ensure-groups-are-created-and-populated"></a>Győződjön meg, hogy a csoportok létrehozása és feltöltése

A tervezés jelszó hitelesítési módszerek szakasz hivatkoznak, és győződjön meg arról, próba vagy az éles végrehajtásához (ok) ban elérhető, és megfelelő hozzá lesznek adva a csoportokhoz.

### <a name="apply-licenses"></a>-Licenceket

A csoportok, implementálási rendelkeznie kell az Azure AD prémium licenccel. Rendelhet licenceket közvetlenül a csoporthoz, vagy használhatja a PowerShell vagy a Csoportalapú licencelés keretében többek között a meglévő licenc házirendek.

A cikkben található információ a licencek hozzárendelése a felhasználói csoportok [-licencek hozzárendelése a felhasználók által az Azure Active Directory biztonságicsoport-tagság](../users-groups-roles/licensing-groups-assign.md).

### <a name="configure-sspr"></a>SSPR konfigurálása

#### <a name="enable-groups-for-sspr"></a>Csoportok az SSPR engedélyezése

1. Egy rendszergazdai fiókkal az Azure portal eléréséhez.
1. Válassza ki a szolgáltatásokat, és a Szűrő mezőbe írja be az Azure Active Directoryban, és válassza ki az Azure Active Directory.
1. Az Active Directory panelen válassza ki a jelszó-visszaállítás.
1. A Tulajdonságok panelen válassza ki a kijelölt. Ha azt szeretné, hogy minden felhasználó engedélyezve van, az összes kijelölése.
1. Az alapértelmezett jelszó alaphelyzetbe állítása a szabályzat panelen, írja be az első csoport nevét, jelölje ki azt, majd válassza a képernyő alján kattintson és a képernyő tetején válassza a mentés.
1. Ismételje meg ezt a folyamatot az egyes csoportokhoz.

#### <a name="configure-the-authentication-methods"></a>A hitelesítési módszerek konfigurálása

Hivatkozhat a tervezési Ez a dokumentum tervezési jelszó hitelesítési módszerek szakaszából.

1. Válassza ki a regisztrációs, a szükséges felhasználót bejelentkezéskor regisztrálni, válassza az Igen lehetőséget, majd adja meg, hány nappal a lejárat előtt, és válassza a mentés.
1. Értesítést, válassza ki, és a terv szerint konfigurálja, és válassza a mentés.
1. Válassza ki a testreszabási, és a terv szerint konfigurálja, és válassza a mentés.
1. Válassza ki a helyszíni integráció, és konfigurálja a terv szerint, és válassza a mentés.

### <a name="enable-sspr-in-windows"></a>A Windows SSPR engedélyezése

Windows 10-es eszközök 1803 vagy újabb verzióját futtató vagy Azure AD-hez vagy a hibrid Azure AD-hez alaphelyzetbe állíthatja a jelszavukat a Windows bejelentkezési képernyőn. A cikkben található információkat, és ez a funkció konfigurálásának lépéseit [az Azure AD-jelszó visszaállítása a bejelentkezési képernyőről](tutorial-sspr-windows.md)

### <a name="configure-password-writeback"></a>Jelszóvisszaíró konfigurálása

A jelszóvisszaíró konfigurálásáról a cikkben találja a szervezet [Útmutató: Jelszóvisszaíró konfigurálása](howto-sspr-writeback.md).

## <a name="manage-sspr"></a>SSPR kezelése

Szükséges szerepkörök felügyeli az új jelszó önkiszolgáló kérésének társított.

| Üzleti szerepkör/személy | Az Azure AD-szerepkörhöz (ha szükséges) |
| :---: | :---: |
| Segélyszolgálat 1. szint | Jelszókezelő |
| 2\. szintű ügyfélszolgálat | Felhasználói adminisztrátor |
| SSPR-rendszergazda | Globális rendszergazda |

### <a name="support-scenarios"></a>Forgatókönyvek támogatása

Ahhoz, hogy a támogatási csapat siker, létrehozhat egy Gyakori kérdések származó, a felhasználók alapján. Az alábbi táblázat tartalmazza a gyakori támogatási példák.

| Forgatókönyvek | Leírás |
| --- | --- |
| Felhasználó nem rendelkezik elérhető minden regisztrált hitelesítési módszerek | Egy felhasználó megpróbál új jelszót kérnek, de nem rendelkezik a hitelesítési módszerek bármelyikét, hogy azok regisztrálva érhető el (Példa: a mobiltelefon otthoni maradt, és a nem lehet hozzáférni az e-mailekhez) |
| Felhasználó nem kap egy szöveget vagy az office, vagy a mobiltelefon hívása | A felhasználó szöveges vagy telefonhíváson alapuló keresztül identitásukat próbál, de nem kap egy szöveges/hívás. |
| Felhasználó nem férhet hozzá a jelszó-visszaállítási portál | A felhasználó új jelszót kérnek szeretne, de nincs engedélyezve a jelszó-visszaállításhoz, és így nem férhet hozzá a jelszavak cseréje a lap. |
| Felhasználó nem állíthatja be az új jelszó | A felhasználó a jelszóvisszaállítási folyamatot ellenőrzés befejeződött, de nem állítható be új jelszó. |
| Felhasználó nem látja a jelszó visszaállítása hivatkozás a Windows 10-es eszközön | Egy felhasználó kísérli meg új jelszót a Windows 10-es zárolási képernyőről, de az eszköz vagy nincs csatlakoztatva az Azure ad-hez, vagy nincs engedélyezve az Intune eszközszabályzat |

Érdemes azt is, például a következő további hibaelhárítási információk.

- SSPR mely csoportok engedélyezettek.
- Milyen hitelesítési módszerek vannak konfigurálva.
- A be- és a vállalati hálózat kapcsolatos hozzáférési szabályzatokat.
- Hibaelhárítási lépések a gyakran használt esetekben.

Az önkiszolgáló jelszó-visszaállítás, SSPR leggyakoribb forgatókönyvekben általános hibaelhárítási lépések megértéséhez hibaelhárítási online dokumentáció is vonatkoznak.

## <a name="next-steps"></a>További lépések

- [Az Azure AD jelszóvédelem vegye fontolóra](concept-password-ban-bad.md)

- [Az Azure AD intelligens zárolás vegye fontolóra](howto-password-smart-lockout.md)
