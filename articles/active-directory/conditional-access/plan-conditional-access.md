---
title: Feltételes hozzáférési házirendek tervezése az Azure Active Directoryban | Microsoft dokumentumok
description: Ebből a cikkből megtudhatja, hogyan tervezheti meg az Azure Active Directory feltételes hozzáférési szabályzatait.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1c75d5022432a9a57b30aabec4dd2c4f76f2f29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671830"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>Útmutató: A feltételes hozzáférés telepítésének megtervezése az Azure Active Directoryban

A feltételes hozzáférés központi telepítésének megtervezése elengedhetetlen ahhoz, hogy a szervezetben az alkalmazásokhoz és erőforrásokhoz szükséges hozzáférési stratégiát érje el. A legtöbb időt a telepítés tervezési fázisában arra fordíthatja, hogy megtervezze a felhasználók számára a kiválasztott feltételek mellett a hozzáférést biztosító vagy letiltáshoz szükséges különböző házirendeket. Ez a dokumentum ismerteti a biztonságos és hatékony feltételes hozzáférési házirendek megvalósításához szükséges lépéseket. Mielőtt elkezdené, győződjön meg arról, hogy megértette, hogyan működik [a feltételes hozzáférés,](overview.md) és mikor kell használni.

## <a name="what-you-should-know"></a>Alapismeretek

Gondoljon a feltételes hozzáférés keretrendszerére, amely lehetővé teszi a szervezet alkalmazásaihoz és erőforrásaihoz való hozzáférés szabályozását, nem pedig önálló funkcióként. Ezért egyes feltételes hozzáférési beállítások további szolgáltatásokat igényelnek. Konfigurálhat például egy szabályzatot, amely egy adott [bejelentkezési kockázati szintre](../identity-protection/howto-identity-protection-configure-risk-policies.md)válaszol. Azonban egy szabályzat, amely egy bejelentkezési kockázati szint alapú, az [Azure Active Directory identitásvédelem](../identity-protection/overview-identity-protection.md) engedélyezéséhez szükséges.

Ha további funkciókra van szükség, előfordulhat, hogy a kapcsolódó licenceket is be kell szereznie. Például, míg a feltételes hozzáférés az Azure AD Premium P1 funkció, az identitásvédelem hez egy Azure AD Premium P2 licenc szükséges.

A feltételes hozzáférési házirendeknek két típusa van: alapkonfiguráció és normál. Az [alaptervi házirend](baseline-protection.md) egy előre definiált feltételes hozzáférési házirend. Ezeknek a házirendeknek az a célja, hogy megbizonyosodjanak arról, hogy legalább az alapszintű biztonsági szint engedélyezve van. Alapházirendek. Az alapszintű szabályzatok az Azure AD összes kiadásában elérhetők, és csak korlátozott testreszabási lehetőségeket biztosítanak. Ha egy forgatókönyv nagyobb rugalmasságot igényel, tiltsa le az alapszintű házirendet, és valósítsa meg a követelményeket egy egyéni szabványos házirendben.

Egy szabványos feltételes hozzáférési házirendben testreszabhatja az összes beállítást, hogy a házirend az üzleti igényekhez igazodjon. A standard szabályzatok hoz egy Azure AD Premium P1 licenc.

>[!NOTE]
> Azt javasoljuk, hogy az Azure AD-eszköz alapú feltételes hozzáférési szabályzat a legjobb kényszerítés a kezdeti eszköz hitelesítése után. Ez magában foglalja a munkamenetek lezárását, ha az eszköz kiesik a megfelelőségi és az eszköz kódáramlását.

## <a name="draft-policies"></a>Politikatervezetek

Az Azure Active Directory feltételes hozzáférése lehetővé teszi, hogy a felhőalapú alkalmazások védelmét egy új szintre. Ezen az új szinten a felhőalapú alkalmazások elérésének célja a statikus hozzáférési konfiguráció helyett dinamikus szabályzatkiértékelés. Feltételes hozzáférési házirend esetén meg kell adni a hozzáférési feltételre **(ha ez megtörténik)** választ **(ezt megteheti)**

![Ok és válasz](./media/plan-conditional-access/10.png)

Definiálja az összes olyan feltételes hozzáférési szabályzatot, amelyet ezzel a tervezési modellel kíván megvalósítani. A tervezési gyakorlat:

- Segít az egyes házirendek válaszainak és feltételeinek felvázolásában.
- Jól dokumentált feltételes hozzáférési házirend-katalógust eredményez a szervezet számára. 

A katalógus segítségével felmérheti, hogy a házirend megvalósítása tükrözi-e a szervezet üzleti követelményeit. 

A következő példasablon segítségével hozzon létre feltételes hozzáférési házirendeket a szervezet számára:

|Amikor *ez* megtörténik:|Akkor tegye *ezt:*|
|-|-|
|A program hozzáférési kísérletet kísérelt meg:<br>- Egy felhő app*<br>- A felhasználók és csoportok*<br>Használ:<br>- 1. feltétel (például a Corp hálózaton kívül)<br>- 2. feltétel (például eszközplatformok)|Az alkalmazáshoz való hozzáférés letiltása|
|A program hozzáférési kísérletet kísérelt meg:<br>- Egy felhő app*<br>- A felhasználók és csoportok*<br>Használ:<br>- 1. feltétel (például a Corp hálózaton kívül)<br>- 2. feltétel (például eszközplatformok)|Hozzáférés megadása az (ÉS) értékkel:<br>- 1.<br>- 2. követelmény (például eszközmegfelelőség)|
|A program hozzáférési kísérletet kísérelt meg:<br>- Egy felhő app*<br>- A felhasználók és csoportok*<br>Használ:<br>- 1. feltétel (például a Corp hálózaton kívül)<br>- 2. feltétel (például eszközplatformok)|Hozzáférés megadása a (OR) értékkel:<br>- 1.<br>- 2. követelmény (például eszközmegfelelőség)|

Legalább, **ha ez megtörténik,** meghatározza a fő **(aki**), amely megpróbálja elérni a felhő app (**mi**). Szükség esetén a hozzáférési kísérlet **elvégzésének módját** is megadhatja. A Feltételes hozzáférés, az elemek, amelyek meghatározzák a ki, mit és hogyan ismert feltételek. További információ: [Mik a feltételek az Azure Active Directory feltételes hozzáférés?](concept-conditional-access-conditions.md) 

**Ezzel adja**meg a házirend hozzáférési feltételre adott válaszát. A válaszban letilthatja vagy hozzáférést adhat további követelményekkel, például többtényezős hitelesítéssel (MFA). A teljes áttekintést a [Mik azok a hozzáférés-vezérlések az Azure Active Directory feltételes hozzáférésében?](controls.md)  

A feltételek és a hozzáférés-vezérlők kombinációja feltételes hozzáférési szabályzatot jelent.

![Ok és válasz](./media/plan-conditional-access/51.png)

További információkért tekintse meg, [hogy mi szükséges ahhoz, hogy a házirend működjön.](best-practices.md#whats-required-to-make-a-policy-work)

Ezen a ponton, ez egy jó ideje, hogy döntsön a szabályzatok elnevezési szabvány. Az elnevezési szabvány segít megtalálni a szabályzatokat, és megérteni azok célját anélkül, hogy megnyitnák őket az Azure felügyeleti portálon. Nevezze el a megjeleníteni a házirendet:

- Sorszám
- Az a felhőalkalmazás, amelyet alkalmaz
- A válasz
- Kire vonatkozik?
- Amikor a rendelet alkalmazandó (adott esetben)
 
![Elnevezési szabvány](./media/plan-conditional-access/11.png)

Bár egy leíró név segít a feltételes hozzáférés megvalósításának áttekintésében, a sorszám akkor hasznos, ha egy beszélgetésben egy házirendre kell hivatkoznia. Ha például telefonon beszél egy rendszergazdatársával, megkérheti őket, hogy nyissák meg az EM063 házirendet egy probléma megoldásához.

Az alábbi név például azt állítja, hogy a házirend a Dynamics CRP alkalmazás használatával külső hálózatokon lévő felhasználók marketingjeihez többfa szükséges:

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`

Az aktív szabályzatok mellett célszerű olyan letiltott házirendeket is megvalósítani, amelyek másodlagos [rugalmas hozzáférés-vezérlésként működnek a szolgáltatáskimaradási/vészhelyzeti forgatókönyvekben.](../authentication/concept-resilient-controls.md) A vészhelyzeti házirendek elnevezési szabványának tartalmaznia kell még néhány elemet: 

- `ENABLE IN EMERGENCY`az elején, hogy a név kitűnjön a többi politika.
- A megzavarás neve, amelyre vonatkoznia kell.
- Rendelési sorszám, amely segít a rendszergazdának tudni, hogy mely sorrendbeli házirendeket kell engedélyezni. 

A következő név például azt jelzi, hogy ez a házirend az első olyan házirend, amelyet engedélyeznie kell, ha mfa-zavar van:

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`

## <a name="plan-policies"></a>Házirendek tervezése

A feltételes hozzáférésházirend-megoldás tervezésekor mérje fel, hogy létre kell-e hoznia a következő eredmények eléréséhez szükséges házirendeket. 

### <a name="block-access"></a>Fájlhozzáférés

A hozzáférés blokkolásának lehetősége azért hatékony, mert:

- Minden más felhasználó megbízásának aduja
- Megvan a hatalma, hogy megakadályozza a teljes szervezet bejelentkezését a bérlő
 
Ha azt szeretné, hogy blokkolja a hozzáférést az összes felhasználó számára, legalább egy felhasználót (általában a segélyhívó fiókokat) ki kell zárnia a házirendből. További információt a [Felhasználók és csoportok kijelölése](block-legacy-authentication.md#select-users-and-cloud-apps)című témakörben talál.  

### <a name="require-mfa"></a>MFA megkövetelése

A felhasználók bejelentkezési élményének egyszerűsítése érdekében érdemes engedélyezni e felhasználók számára, hogy felhasználónév vel és jelszóval jelentkezzenek be a felhőalapú alkalmazásokba. Azonban általában vannak olyan forgatókönyvek, amelyekesetében tanácsos erősebb fiókellenőrzést igényelni. Feltételes hozzáférési szabályzattal korlátozhatja az MFA követelményét bizonyos esetekre. 

Az MFA-t igénylő gyakori használati esetek a következők:

- [Az adminok szerint](howto-baseline-protect-administrators.md)
- [Adott alkalmazásokra](app-based-mfa.md) 
- [Hálózati helyekről nem bízik](untrusted-networks.md)meg.

### <a name="respond-to-potentially-compromised-accounts"></a>Adott, potenciálisan feltört fiókokra adott válaszadás

Feltételes hozzáférési szabályzatok segítségével automatikus válaszokat hozhat létre a potenciálisan veszélyeztetett identitások bejelentkezések. Annak a valószínűsége, hogy egy számla feltörése kockázati szintek formájában van kifejezve. Az identitásvédelem által számított kockázati szintek: a bejelentkezési kockázat és a felhasználói kockázat két kockázati szint. A bejelentkezési kockázatra adott válasz megvalósításához két lehetősége van:

- A feltételes hozzáférési házirend [bejelentkezési kockázati feltétele](concept-conditional-access-conditions.md#sign-in-risk)
- [A bejelentkezési kockázati házirend](../identity-protection/howto-sign-in-risk-policy.md) az identitásvédelemben 

A bejelentkezési kockázat feltételként történő kezelése az előnyben részesített módszer, mivel több testreszabási lehetőséget biztosít.

A felhasználói kockázati szint csak az identitásvédelem [felhasználói kockázati házirendjeként](../identity-protection/howto-user-risk-policy.md) érhető el. 

További információ: [Mi az Azure Active Directory identity protection?](../identity-protection/overview.md) 

### <a name="require-managed-devices"></a>Felügyelt eszközök megkövetelése

A támogatott eszközök elterjedése a felhőbeli erőforrások eléréséhez segít a felhasználók termelékenységének növelésében. A másik oldalon valószínűleg nem szeretné, hogy a környezet bizonyos erőforrásait ismeretlen védelmi szintű eszközök érjék el. Az érintett erőforrások esetében meg kell követelnie, hogy a felhasználók csak felügyelt eszközzel férhessenek hozzájuk. További információ: [Felügyelt eszközök megkövetelése a felhőalapú alkalmazások hoz való hozzáféréshez feltételes hozzáféréssel.](require-managed-devices.md) 

### <a name="require-approved-client-apps"></a>Jóváhagyott ügyfélalkalmazások megkövetelése

Az egyik első döntés, amit meg kell hoznia a saját eszközök (BYOD) forgatókönyvek, hogy kell-e kezelni a teljes eszközt, vagy csak az adatokat rajta. Az alkalmazottak mobileszközöket használnak a személyes és munkahelyi feladatokhoz. Miközben gondoskodik arról, hogy alkalmazottai hatékonyak legyenek, az adatvesztést is meg szeretné akadályozni. Az Azure Active Directory (Azure AD) feltételes hozzáféréssel korlátozhatja a felhőalapú alkalmazásokhoz való hozzáférést a jóváhagyott ügyfélalkalmazásokra, amelyek megvédhetik a vállalati adatokat. További információ: [A jóváhagyott ügyfélalkalmazások megkövetelése a felhőalapú alkalmazások feltételes hozzáféréssel való eléréséhez.](app-based-conditional-access.md)

### <a name="block-legacy-authentication"></a>Régi hitelesítési folyamat letiltása

Az Azure AD támogatja a legszélesebb körben használt hitelesítési és engedélyezési protokollok, beleértve az örökölt hitelesítést. Hogyan akadályozhatja meg, hogy az örökölt hitelesítést használó alkalmazások hozzáférjenek a bérlő erőforrásaihoz? A javaslat az, hogy csak blokkolja őket egy feltételes hozzáférési szabályzattal. Szükség esetén csak bizonyos felhasználók nak és adott hálózati helyeknek engedélyezi az örökölt hitelesítésen alapuló alkalmazások használatát. További információ: [Az örökölt hitelesítés blokkolása az Azure AD feltételes hozzáféréssel című témakörben.](block-legacy-authentication.md)

## <a name="test-your-policy"></a>A szabályzat tesztelése

Mielőtt egy szabályzatot éles környezetbe, tesztelje, hogy ellenőrizze, hogy a várt módon viselkedik.Before rolling out a policy into production, you should test to verify that it is behave is a expected.

1. Tesztfelhasználók létrehozása
1. Tesztterv létrehozása
1. A szabályzat konfigurálása
1. Szimulált bejelentkezés kiértékelése
1. A szabályzat tesztelése
1. Felesleges tartalmak törlése

### <a name="create-test-users"></a>Tesztfelhasználók létrehozása

Házirend teszteléséhez hozzon létre olyan felhasználókat, amelyek hasonlóak a környezetben lévő felhasználókhoz. A tesztfelhasználók létrehozása lehetővé teszi annak ellenőrzését, hogy a házirendek a várt módon működnek-e, mielőtt hatással lenne a valódi felhasználókra, és potenciálisan megzavarná az alkalmazásokhoz és erőforrásokhoz való hozzáférésüket. 

Egyes szervezetek teszt bérlők erre a célra. Azonban nehéz lehet újra létrehozni az összes feltételt és alkalmazásokat egy tesztbérlőben, hogy teljes mértékben tesztelje a szabályzat eredményét. 

### <a name="create-a-test-plan"></a>Tesztterv létrehozása

A vizsgálati terv fontos a várt eredmények és a tényleges eredmények összehasonlítása érdekében. Mindig kell egy elvárás, mielőtt tesztelvalamit. Az alábbi táblázat a példateszteseteket ismerteti. A hitelesítésbiztosítási házirendek konfigurálása alapján módosíthatja az eseteket és a várt eredményeket.

|Szabályzat |Forgatókönyv |Várt eredmény | Eredmény |
|---|---|---|---|
|[Többszinten nem végzett munka esetén az MFA megkövetelése](/azure/active-directory/conditional-access/untrusted-networks)|A jogosult felhasználó bejelentkezik az *alkalmazásba,* miközben megbízható helyen / munkahelyen van|A rendszer nem kéri a felhasználót az MFA-ra| |
|[Többszinten nem végzett munka esetén az MFA megkövetelése](/azure/active-directory/conditional-access/untrusted-networks)|A jogosult felhasználó bejelentkezik az *alkalmazásba,* miközben nem megbízható helyen / munkahelyen|A felhasználó a rendszer kéri az MFA-t, és sikeresen bejelentkezhet| |
|[Többkori faszükséglés megkövetelése (rendszergazda esetén)](/azure/active-directory/conditional-access/howto-baseline-protect-administrators)|Globális rendszergazdai jelentkezések az *alkalmazásba*|A rendszergazda a többkori makro-együzenetben kéri a rendszergazdákat| |
|[Kockázatos bejelentkezések](/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|A felhasználó bejelentkezik *az alkalmazásba* egy [Tor böngészővel](/azure/active-directory/active-directory-identityprotection-playbook)|A rendszergazda a többkori makro-együzenetben kéri a rendszergazdákat| |
|[Eszközfelügyelet](/azure/active-directory/conditional-access/require-managed-devices)|A jogosult felhasználó hivatalos eszközről próbál bejelentkezni|Hozzáférés engedélyezve| |
|[Eszközfelügyelet](/azure/active-directory/conditional-access/require-managed-devices)|Jogosulatlan felhasználó megpróbál bejelentkezni egy jogosulatlan eszközről|Hozzáférés letiltva| |
|[Jelszómódosítása kockázatos felhasználók számára](/azure/active-directory/identity-protection/howto-user-risk-policy)|A jogosult felhasználó megpróbál bejelentkezni sérült hitelesítő adatokkal (magas kockázatú bejelentkezés)|A rendszer kéri a felhasználót, hogy változtassa meg a jelszavát, vagy a rendszer a házirend alapján letiltja a hozzáférést| |

### <a name="configure-the-policy"></a>A szabályzat konfigurálása

A feltételes hozzáférési házirendek kezelése manuális feladat. Az Azure Portalon kezelheti a feltételes hozzáférési szabályzatok egy központi helyen - a feltételes hozzáférés lap. A feltételes hozzáférés lap egyik belépési pontja az **Active Directory** navigációs **ablakának Biztonsági** szakasza. 

![Feltételes hozzáférés](media/plan-conditional-access/03.png)

Ha szeretne többet megtudni a feltételes hozzáférési szabályzatok létrehozásáról, olvassa [el az MFA megkövetelése bizonyos alkalmazásokhoz az Azure Active Directory feltételes hozzáféréssel című témakört.](app-based-mfa.md) Ez a rövid útmutató a következőket segíti:

- Ismerkedjen meg a felhasználói felülettel.
- Első benyomás a feltételes hozzáférés működéséről. 

### <a name="evaluate-a-simulated-sign-in"></a>Szimulált bejelentkezés kiértékelése

Most, hogy konfigurálta a feltételes hozzáférési házirendet, valószínűleg tudni szeretné, hogy a várt módon működik-e. Első lépésként használja a feltételes [hozzáférés, mi van, ha a házirend eszköz](what-if-tool.md) szimulálja a bejelentkezést a teszt felhasználó. A szimuláció megbecsüli a bejelentkezésnek a szabályzatokra gyakorolt hatását, és létrehoz egy szimulációs jelentést.

>[!NOTE]
> Míg a szimulált futtatás a feltételes hozzáférési házirend hatásának benyomását kelti, nem helyettesíti a tényleges tesztfuttatást.

### <a name="test-your-policy"></a>A szabályzat tesztelése

Futtasson teszteseteket a tesztterv szerint. Ebben a lépésben végigfut egy végpontok között teszt minden egyes szabályzat a teszt felhasználók számára, hogy megbizonyosodjon arról, minden szabályzat megfelelően viselkedik. Használja a fent létrehozott forgatókönyvek az egyes teszteket.

Fontos, hogy tesztelje a házirend kizárási kritériumait. Kizárhat például egy felhasználót vagy csoportot egy olyan házirendből, amely többfa-t igényel. Tesztelje, hogy a kizárt felhasználók többfa-t kérnek-e, mert más házirendek kombinációja többkori többkori be- és a felhasználók számára többfa-t igényelhet.

### <a name="cleanup"></a>Felesleges tartalmak törlése

A tisztítási eljárás a következő lépésekből áll:

1. Tiltsa le a házirendet.
1. Távolítsa el a hozzárendelt felhasználókat és csoportokat.
1. Törölje a tesztfelhasználókat.  

## <a name="move-to-production"></a>Átállás éles üzemre

Ha az új házirendek készen állnak a környezetre, telepítse őket fázisokban:

- Belső változáskommunikáció biztosítása a végfelhasználók számára.
- Kezdje a felhasználók egy kis készletével, és ellenőrizze, hogy a házirend a várt módon viselkedik-e.
- Ha egy házirendet több felhasználóra bont ki, továbbra is zárja ki az összes rendszergazdát. A rendszergazdák kizárása biztosítja, hogy szükség esetén valaki továbbra is hozzáférhessen a házirendhez.
- Csak akkor alkalmazzon házirendet az összes felhasználóra, ha az szükséges.

Ajánlott eljárásként hozzon létre legalább egy felhasználói fiókot, amely:

- A házirend-felügyeletnek szentelve
- Minden irányelvből kizárva

## <a name="rollback-steps"></a>Visszaállítási lépések

Abban az esetben, ha vissza kell vonnia az újonnan megvalósított házirendeket, az alábbi lehetőségek közül választhat a visszaállításhoz:

1. **A házirend letiltása** – A házirend letiltása biztosítja, hogy az ne legyen érvényes, amikor a felhasználó megpróbál bejelentkezni. Bármikor visszajöhet, és engedélyezheti a házirendet, ha használni szeretné.

   ![Szabályzat letiltása](media/plan-conditional-access/07.png)

1. **Felhasználó/csoport kizárása a házirendből** – Ha egy felhasználó nem tud hozzáférni az alkalmazáshoz, kizárhatja a felhasználót a szabályzatból

   ![Felhasználók kiirtása](media/plan-conditional-access/08.png)

   > [!NOTE]
   > Ezt a beállítást takarékosan kell használni, csak olyan helyzetekben, amikor a felhasználó megbízható. A felhasználót a lehető leghamarabb vissza kell adni a házirendhez vagy csoporthoz.

1. **A házirend törlése** – Ha a házirendre már nincs szükség, törölje azt.

## <a name="next-steps"></a>További lépések

Tekintse meg [az Azure AD feltételes hozzáférési dokumentációt,](index.yml) hogy áttekintést kapjon a rendelkezésre álló információkról.
