---
title: Önkiszolgáló jelszó-visszaállítás üzembe helyezése – Azure Active Directory
description: Az Azure AD önkiszolgáló jelszó-visszaállítás sikeres megvalósítására szolgáló stratégia
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc2c68c53a7c03d1de08e5cde528f27aa61b0096
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847269"
---
# <a name="deploy-azure-ad-self-service-password-reset"></a>Új jelszó önkiszolgáló kérésének üzembe helyezése az Azure AD-ben

> [!NOTE]
> Ez az útmutató ismerteti az önkiszolgáló jelszó-visszaállítást és az üzembe helyezésének módját. Ha az önkiszolgáló jelszó-visszaállítási eszközt keresi, hogy visszalépjen a fiókjába, lépjen a [https://aka.ms/sspr](https://aka.ms/sspr). 

Az önkiszolgáló jelszó-visszaállítás (SSPR) egy Azure Active Directory funkció, amely lehetővé teszi az alkalmazottak számára, hogy az IT-részleggel való kapcsolatfelvétel nélkül visszaállítsák a jelszavukat. Az alkalmazottaknak regisztrálniuk kell az önkiszolgáló jelszó-visszaállításra, vagy regisztrálniuk kell a szolgáltatás használata előtt. A regisztráció során az alkalmazott egy vagy több, a szervezet által engedélyezett hitelesítési módszert választ.

A SSPR lehetővé teszi, hogy az alkalmazottak gyorsan feloldják a blokkolást, és a munka helyétől függetlenül is működjenek. Azáltal, hogy a felhasználók letiltják magukat, a szervezet csökkentheti a nem produktív időt és a magas támogatási költségeket a leggyakoribb jelszóval kapcsolatos problémákhoz.

A felhasználók gyorsan regisztrálhatnak a SSPR a szervezet egy másik alkalmazásával vagy szolgáltatásával együtt. Ez a művelet nagy mennyiségű bejelentkezést eredményez, és a regisztrációt fogja vezetni.

A SSPR üzembe helyezése előtt a szervezetek dönthetnek arról, hogy hány jelszó-visszaállítási kapcsolódó ügyfélszolgálati hívás történik az idő múlásával, és az egyes hívások átlagos díjait. Ezeket az adatposta üzembe helyezésével megjelenítheti a szervezete számára SSPR értéket.  

## <a name="how-sspr-works"></a>Az SSPR működése

1. Amikor egy felhasználó megpróbálja alaphelyzetbe állítani a jelszót, ellenőriznie kell a korábban regisztrált hitelesítési módszereket vagy metódusokat, hogy igazolják személyazonosságát.
1. Ezután a felhasználó új jelszót ad meg.
   1. A csak felhőalapú felhasználók számára az új jelszót Azure Active Directory tárolja a rendszer. További információkért tekintse meg a [SSPR működését](concept-sspr-howitworks.md#how-does-the-password-reset-portal-work)ismertető cikket.
   1. A hibrid felhasználók esetében a jelszó a helyszíni Active Directoryra íródik a Azure AD Connect szolgáltatáson keresztül. További információkért lásd a [jelszó-visszaírási](concept-sspr-writeback.md#how-password-writeback-works)című cikket.

## <a name="licensing-considerations"></a>Licencelési megfontolások

A Azure Active Directory felhasználónkénti licenccel rendelkezik, és minden felhasználónak rendelkeznie kell megfelelő licenccel az általuk használt funkciókhoz.

A licenceléssel kapcsolatos további információkért tekintse meg a [Azure Active Directory díjszabási oldalát](https://azure.microsoft.com/pricing/details/active-directory/) .

## <a name="enable-combined-registration-for-sspr-and-mfa"></a>A SSPR és az MFA együttes regisztrációjának engedélyezése

A Microsoft azt javasolja, hogy a szervezetek lehetővé tegyék a SSPR és a többtényezős hitelesítés együttes regisztrációs funkciójának használatát. Ha engedélyezi ezt a kombinált regisztrációs élményt, a felhasználóknak csak egyszer kell kiválasztaniuk regisztrációs adataikat mindkét funkció engedélyezéséhez.

![Kombinált biztonsági információk regisztrálása](./media/howto-sspr-deployment/combined-security-info.png)

A kombinált regisztrációs élmény nem igényli, hogy a szervezetek mind a SSPR, mind az Azure Multi-Factor Authentication használatát engedélyezzék. A közös regisztrációs élmény lehetővé teszi a szervezetek számára, hogy a hagyományos egyedi összetevőkhöz képest jobb felhasználói élményt biztosítanak. A kombinált regisztrációval és az engedélyezéssel kapcsolatos további információkért tekintse meg a következő cikket: [kombinált biztonsági információk regisztrálása (előzetes verzió)](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-configuration"></a>A konfiguráció megtervezése

A következő beállítások szükségesek a SSPR engedélyezéséhez az ajánlott értékekkel együtt.

| Terület | Beállítás | Value (Díj) |
| --- | --- | --- |
| **SSPR tulajdonságai** | Önkiszolgáló jelszó-visszaállítás engedélyezve | **Kijelölt** csoport a próbaüzem/ **mind** az éles környezethez |
| **Hitelesítési módszerek** | A regisztráláshoz szükséges hitelesítési módszerek | Az alaphelyzetbe állításhoz mindig 1 nagyobb érték szükséges |
|   | Az alaphelyzetbe állításhoz szükséges hitelesítési módszerek | Egy vagy kettő |
| **Regisztráció** | Szükséges a felhasználóknak regisztrálniuk a bejelentkezéskor? | Igen |
|   | A napok száma, amely előtt a rendszer kéri a felhasználóktól a hitelesítési adataik ismételt megerősítését | 90 – 180 nap |
| **Értesítések** | Értesítse a felhasználókat új jelszó kérésekor? | Igen |
|   | Minden rendszergazda kapjon értesítést, ha más rendszergazdák új jelszót kérnek? | Igen |
| **Testreszabási** | Támogatási szolgálat hivatkozásának testreszabása | Igen |
|   | Egyéni támogatási szolgálat e-mail-címe vagy URL-címe | Támogatási webhely vagy e-mail-cím |
| **Helyszíni integráció** | Jelszavak visszaírása a helyszíni AD-be | Igen |
|   | Fiók feloldásának engedélyezése a felhasználók számára a jelszó alaphelyzetbe állítása nélkül | Igen |

### <a name="sspr-properties-recommendations"></a>SSPR-tulajdonságok – javaslatok

Az önkiszolgáló jelszó-visszaállítás engedélyezésekor válassza ki a próbaüzem során használni kívánt biztonsági csoportot.

Ha a szolgáltatás szélesebb körű elindítását tervezi, javasoljuk, hogy az összes beállítás használatával kényszerítse ki a SSPR mindenki számára a szervezeten belül. Ha nem állítható be az összes értékre, válassza ki az Azure ad-vel szinkronizált megfelelő Azure AD biztonsági csoportot vagy AD-csoportot.

### <a name="authentication-methods"></a>Hitelesítési módok

Adja meg az alaphelyzetbe állításhoz szükséges hitelesítési módszereket a legalább egy értékre való regisztráláshoz. Lehetővé teszi, hogy több felhasználó rugalmasságot biztosítson, amikor alaphelyzetbe kell állítani őket.

Állítsa be a szervezete számára megfelelő szintre való **visszaállításhoz szükséges metódusok számát** . Az egyik a legkevesebb súrlódást igényli, míg a kettő növelheti a biztonsági testhelyzetet.

Tekintse meg, mi az a [hitelesítési módszer](concept-authentication-methods.md) a SSPR, előre meghatározott biztonsági kérdésekre és testreszabott biztonsági kérdések létrehozására szolgáló hitelesítési módszerekkel kapcsolatos részletes információkért.

### <a name="registration-settings"></a>Regisztrációs beállítások

Állítsa be a **felhasználókat, hogy regisztráljanak** az **Igen**értékre való bejelentkezéskor. Ez a beállítás azt jelenti, hogy a felhasználók a bejelentkezéskor kénytelenek regisztrálni, hogy minden felhasználó védve legyen.

Adja meg a **napok számát, mielőtt a rendszer megkéri a felhasználóktól, hogy hitelesítő adataikat** a **90** és **180** nap között újra erősítse meg, kivéve, ha a szervezetnek rövidebb időre van szüksége üzleti igényekre.

### <a name="notifications-settings"></a>Értesítésbeállítások

Konfigurálja mind a **felhasználók értesítése jelszó** **alaphelyzetbe állítását, mind a rendszergazdák értesítése, ha más rendszergazdák Igen értékre állítják vissza a jelszavukat** . Ha az **Igen** lehetőséget választja, mindkettő növeli a biztonságot azáltal, hogy a felhasználók tisztában vannak a jelszavuk alaphelyzetbe állítását követően, és az összes rendszergazda tisztában van azzal, hogy a rendszergazda módosítja a jelszót. Ha a felhasználók vagy rendszergazdák ilyen értesítést kapnak, és nem kezdeményezték a változást, azonnal jelenthetik a potenciális biztonsági problémákat.

### <a name="customization"></a>Testreszabás

Fontos, hogy testreszabja az **ügyfélszolgálati e-mailt vagy URL-címet** annak biztosítására, hogy a problémákat tapasztaló felhasználók gyorsan segítséget kapjanak. Ezt a beállítást olyan általános ügyfélszolgálati e-mail-címre vagy weblapra állíthatja be, amelyet a felhasználók ismernek.

### <a name="on-premises-integration"></a>Helyszíni integráció

Ha hibrid környezettel rendelkezik, győződjön meg arról, hogy **a helyi ad-re való visszaírási jelszavak** értéke **Igen**. Azt is beállíthatja, hogy a felhasználók a fiók zárolásának feloldása nélkül is feloldják a jelszót az Igen értékre, mivel ez nagyobb rugalmasságot biztosít számukra.

### <a name="changingresetting-passwords-of-administrators"></a>Rendszergazdák jelszavának módosítása/alaphelyzetbe állítása

A rendszergazdai fiókok emelt szintű engedélyekkel rendelkező speciális fiókok. A biztonság érdekében a következő korlátozások vonatkoznak a rendszergazdák jelszavának módosítására:

- A helyszíni vállalati rendszergazdák vagy a tartományi rendszergazdák nem állíthatják alaphelyzetbe a jelszavukat a SSPR használatával. Csak a helyi környezetben módosíthatják a jelszavukat. Ezért javasoljuk, hogy a helyszíni AD-rendszergazdai fiókokat ne szinkronizálja az Azure AD-be.
- A rendszergazda nem használhat titkos kérdéseket & válaszként a jelszó alaphelyzetbe állítására szolgáló metódusként.

### <a name="environments-with-multiple-identity-management-systems"></a>Környezetek több Identitáskezelés-felügyeleti rendszerrel

Ha több Identity Management rendszer van egy olyan környezetben, mint például a helyszíni Identitáskezelő-kezelők, például az Oracle AM, a SiteMinder vagy más rendszerek, akkor előfordulhat, hogy az Active Directoryba írt jelszavakat a többi rendszerhez kell szinkronizálni egy olyan eszköz használatával, mint például: a jelszó-módosítási értesítési szolgáltatás (PCN) Microsoft Identity Manager (webszolgáltatással). Ha további információt szeretne megtudni erről az összetettebb forgatókönyvről, tekintse meg a következő cikket: a rendszerszintű [jelszó-módosítási értesítési szolgáltatás központi telepítése tartományvezérlőn](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-deployment-and-support-for-sspr"></a>A SSPR üzembe helyezésének és támogatásának megtervezése

### <a name="engage-the-right-stakeholders"></a>A megfelelő résztvevők bevonása

A technológiai projektek meghibásodása esetén általában a hatás, az eredmények és a felelősségek eltérő elvárásai miatt válnak elérhetővé. A buktatók elkerülése érdekében győződjön meg arról, hogy a megfelelő érintett feleket látja el, és hogy a projektben érintett szerepköröket jól megértette az érintett felek és a projekt bemenetének és elszámoltathatóságának dokumentálása révén.

### <a name="communications-plan"></a>Kommunikációs terv

A kommunikáció minden új szolgáltatás sikeressége szempontjából kritikus fontosságú. Proaktív módon kommunikálhat a felhasználókkal, hogyan használhatja a szolgáltatást, és mit tehet a segítségért, ha valami nem a várt módon működik. Tekintse át az [önkiszolgáló jelszó-visszaállítás](https://www.microsoft.com/download/details.aspx?id=56768) bevezetésével kapcsolatos anyagokat a Microsoft letöltőközpontból, és ötleteket a végfelhasználói kommunikációs stratégia megtervezéséhez.

### <a name="testing-plan"></a>Tesztelési terv

Annak ellenőrzéséhez, hogy az üzemelő példány a várt módon működik-e, meg kell terveznie azokat a tesztelési eseteket, amelyeket a megvalósítás ellenőrzéséhez fog használni. Az alábbi táblázat néhány hasznos tesztelési forgatókönyvet tartalmaz, amelyekkel dokumentálhatja a szervezete által várt eredményeket a szabályzatok alapján.

| Üzleti eset | Várt eredmény |
| --- | --- |
| A SSPR portál elérhető a vállalati hálózaton belülről | A szervezet által meghatározott |
| A SSPR portál a vállalati hálózaton kívülről érhető el | A szervezet által meghatározott |
| Felhasználói jelszó alaphelyzetbe állítása a böngészőből, ha a felhasználó nincs engedélyezve a jelszó-visszaállításhoz | A felhasználó nem tud hozzáférni a jelszó-visszaállítási folyamathoz |
| Felhasználói jelszó alaphelyzetbe állítása a böngészőből, ha a felhasználó nincs regisztrálva a jelszó-visszaállításhoz | A felhasználó nem tud hozzáférni a jelszó-visszaállítási folyamathoz |
| A felhasználó bejelentkezik, ha a jelszó-visszaállítási regisztráció kényszerítve van | A rendszer kéri a felhasználótól, hogy regisztrálja a biztonsági adatokat |
| A felhasználó bejelentkezik, ha a jelszó-visszaállítási regisztráció befejeződött | A rendszer nem kéri a felhasználót, hogy regisztrálja a biztonsági adatokat |
| A SSPR portál elérhető, ha a felhasználó nem rendelkezik licenccel | Elérhető |
| Felhasználói jelszó alaphelyzetbe állítása Windows 10 Azure AD-hez csatlakoztatott vagy hibrid Azure AD-hez csatlakoztatott eszköz zárolási képernyőjén a felhasználó regisztrálása után | A felhasználó alaphelyzetbe állíthatja a jelszót |
| A SSPR regisztrációs és használati adatok közel valós időben érhetők el a rendszergazdák számára | Naplózási naplókon keresztül érhető el |

### <a name="support-plan"></a>Támogatási csomag

Habár a SSPR általában nem hoz létre felhasználói problémákat, fontos, hogy az esetlegesen felmerülő problémák kezelésére szolgáló támogatási munkatársakat is felkészítse.

Habár a rendszergazdák az Azure AD-portálon keresztül módosíthatják vagy alaphelyzetbe állíthatják a végfelhasználók jelszavát, jobb megoldás az önkiszolgáló támogatási folyamaton keresztüli probléma megoldása.

A jelen dokumentum üzemeltetési útmutató szakaszában hozzon létre egy listát a támogatási esetekről és azok várható okairól, és hozzon létre egy útmutatót a megoldáshoz.

### <a name="auditing-and-reporting"></a>Naplózás és jelentéskészítés

Az üzembe helyezést követően számos szervezet szeretné tudni, hogy az önkiszolgáló jelszó-visszaállítás (SSPR) valóban használatban van-e. A Azure Active Directory (Azure AD) által biztosított jelentéskészítési funkció segítséget nyújt az előre elkészített jelentések használatával kapcsolatos kérdések megválaszolásához.

A regisztrációs és a jelszó-visszaállítási naplók 30 napig érhetők el. Ezért ha egy vállalaton belül a biztonsági naplózás nagyobb adatmegőrzést igényel, a naplókat olyan SIEM-eszközbe kell exportálni és felvenni, mint például az [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), a splunk vagy a ArcSight.

Egy táblában, az alábbihoz hasonlóan a biztonsági mentési ütemterv, a rendszer és a felelős felek dokumentálása. Előfordulhat, hogy nincs szükség külön naplózási és jelentéskészítési biztonsági mentésre, de egy különálló biztonsági mentést kell készítenie, amelyből a probléma megoldására lehet visszaállítani.

|   | Letöltés gyakorisága | Célrendszer | Felelős fél |
| --- | --- | --- | --- |
| Biztonsági mentés naplózása |   |   |   |
| Jelentések biztonsági mentése |   |   |   |
| Vész-helyreállítási biztonsági mentés |   |   |   |

## <a name="implementation"></a>Megvalósítás

A megvalósítás három lépésben történik:

- Felhasználók és licencek konfigurálása
- Azure AD-SSPR konfigurálása a regisztrációhoz és az önkiszolgáló szolgáltatásokhoz
- Azure AD Connect konfigurálása jelszó-visszaírási

### <a name="communicate-the-change"></a>A módosítás közlése

A tervezési fázisban fejlesztett kommunikációs terv végrehajtásának megkezdése.

### <a name="ensure-groups-are-created-and-populated"></a>A csoportok létrehozása és feltöltése

Hivatkozzon a jelszó-hitelesítési módszerek megtervezése szakaszra, és ellenőrizze, hogy elérhetők-e a tesztelési vagy a termelési megvalósítási csoport (ok), és hogy az összes megfelelő felhasználó hozzá van-e adva a csoporthoz.

### <a name="apply-licenses"></a>Licencek alkalmazása

A megvalósítani kívánt csoportokhoz hozzá kell rendelni az Azure AD Premium-licencet. A licenceket közvetlenül a csoporthoz rendelheti hozzá, vagy használhat meglévő licencfeltételeket, például a PowerShell-lel vagy a csoporton alapuló licenceléssel.

A licencek felhasználói csoportokhoz való hozzárendelésével kapcsolatos információk a cikkben találhatók, [licencek felhasználókhoz rendelése csoporttagság alapján Azure Active Directory](../users-groups-roles/licensing-groups-assign.md).

### <a name="configure-sspr"></a>Az SSPR konfigurálása

#### <a name="enable-groups-for-sspr"></a>Csoportok engedélyezése a SSPR

1. Hozzáférés a Azure Portal egy rendszergazdai fiókkal.
1. Válassza a minden szolgáltatás lehetőséget, majd a szűrő mezőbe írja be a Azure Active Directory, majd válassza a Azure Active Directory lehetőséget.
1. A Active Directory panelen válassza a jelszó alaphelyzetbe állítása lehetőséget.
1. A Tulajdonságok ablaktáblán válassza a kiválasztott lehetőséget. Ha azt szeretné, hogy minden felhasználó engedélyezve legyen, válassza az összes lehetőséget.
1. Az alapértelmezett jelszó-visszaállítási szabályzat panelen írja be az első csoport nevét, jelölje ki, majd kattintson a képernyő alján található kiválasztás lehetőségre, majd a képernyő felső részén válassza a mentés lehetőséget.
1. Ismételje meg ezt a folyamatot minden egyes csoportnál.

#### <a name="configure-the-authentication-methods"></a>A hitelesítési módszerek konfigurálása

Hivatkozzon a tervezésre a jelen dokumentumban a jelszó-hitelesítési módszerek megtervezése szakaszban.

1. Válassza a regisztráció lehetőséget, a felhasználó regisztrációjának megkövetelése a bejelentkezéskor területen válassza az Igen lehetőséget, majd állítsa be a lejárat előtti napok számát, majd kattintson a Mentés gombra.
1. Válassza az értesítés lehetőséget, és konfigurálja a tervet, majd kattintson a Mentés gombra.
1. Válassza a Testreszabás lehetőséget, és konfigurálja a tervet, majd kattintson a Mentés gombra.
1. Válassza a helyszíni integráció lehetőséget, és konfigurálja a csomagot, majd válassza a mentés lehetőséget.

### <a name="enable-sspr-in-windows"></a>SSPR engedélyezése a Windowsban

Az Azure AD-hez csatlakoztatott vagy hibrid Azure AD-hez csatlakozó, 1803-es vagy újabb verziót futtató Windows 10-es eszközök a Windows bejelentkezési képernyőjén alaphelyzetbe állíthatják a jelszavukat. A funkció konfigurálásához szükséges információk és lépések az [Azure ad jelszó-visszaállítás a bejelentkezési képernyőről](tutorial-sspr-windows.md) című cikkben találhatók.

### <a name="configure-password-writeback"></a>Jelszó visszaírási konfigurálása

A szervezethez tartozó jelszó-visszaírási konfigurálásának lépései a következő cikkben találhatók [: a jelszó visszaírási konfigurálása](howto-sspr-writeback.md).

## <a name="manage-sspr"></a>SSPR kezelése

Az önkiszolgáló jelszó-visszaállításhoz társított szolgáltatások kezeléséhez szükséges szerepkörök.

| Üzleti szerepkör/persona | Azure AD-szerepkör (ha szükséges) |
| :---: | :---: |
| 1\. szint segélyszolgálat | Jelszókezelő |
| 2\. szint helpdesk | Felhasználói rendszergazda |
| SSPR-rendszergazda | Globális rendszergazda |

### <a name="support-scenarios"></a>Támogatási forgatókönyvek

A támogatási csapat sikerességének engedélyezéséhez a felhasználóktól kapott kérdések alapján hozhat létre GYIK-et. A következő táblázat általános támogatási forgatókönyveket tartalmaz.

| Alkalmazási helyzetek | Leírás |
| --- | --- |
| A felhasználónak nincs elérhető regisztrált hitelesítési módszere. | A felhasználó megpróbálja alaphelyzetbe állítani a jelszavát, de nem rendelkezik a rendelkezésre álló hitelesítési módszerekkel (például a mobiltelefonját otthon hagyta, és nem fér hozzá az e-mailekhez) |
| A felhasználó nem kap SMS-t vagy hívást az irodában vagy a mobiltelefonján | A felhasználó szöveges vagy hívási identitást próbál meg ellenőrizni, de nem kap szöveget vagy hívást. |
| A felhasználó nem férhet hozzá a jelszó-visszaállítási portálhoz | A felhasználó szeretné visszaállítani a jelszavát, de nincs engedélyezve a jelszó-visszaállításhoz, ezért nem fér hozzá a laphoz a jelszavak frissítéséhez. |
| A felhasználó nem állíthat be új jelszót | A felhasználó ellenőrzi az ellenőrzést a jelszó-visszaállítási folyamat során, de nem állíthat be új jelszót. |
| A felhasználó nem látja a jelszó alaphelyzetbe állítása hivatkozást egy Windows 10-es eszközön | A felhasználó megpróbálja alaphelyzetbe állítani a jelszót a Windows 10 zárolási képernyőjén, de az eszköz nincs csatlakoztatva az Azure AD-hez, vagy az Intune-eszköz házirendje nincs engedélyezve |

További hibaelhárításhoz az alábbi információkat is érdemes feltüntetni:

- Mely csoportok engedélyezettek a SSPR.
- Mely hitelesítési módszerek vannak konfigurálva.
- A vállalati hálózathoz kapcsolódó hozzáférési szabályzatok.
- Gyakori forgatókönyvek hibaelhárítási lépései.

Az önkiszolgáló jelszó-visszaállítás hibaelhárítását ismertető online dokumentációban is tájékozódhat a leggyakoribb SSPR forgatókönyvek általános hibaelhárítási lépéseinek megismeréséhez.

## <a name="next-steps"></a>Következő lépések

- [Az Azure AD jelszavas védelem megvalósításának megfontolása](concept-password-ban-bad.md)

- [Vegye fontolóra az Azure AD Smart zárolás megvalósítását](howto-password-smart-lockout.md)
