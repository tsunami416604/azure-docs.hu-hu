---
title: Feltételes hozzáférési szabályzatok tervezése a Azure Active Directoryban | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan tervezhet feltételes hozzáférési szabályzatokat a Azure Active Directoryhoz.
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
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671830"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>Útmutató: a feltételes hozzáférési telepítés megtervezése Azure Active Directory

A feltételes hozzáférés központi telepítésének megtervezése kritikus fontosságú ahhoz, hogy a szervezet alkalmazásai és erőforrásai számára elérhetővé tegye a szükséges hozzáférési stratégiát. Az üzembe helyezés tervezési fázisában az idő nagy részében az Ön által választott feltételek alapján megtervezheti a felhasználók hozzáférésének megadásához vagy letiltásához szükséges különböző házirendeket. Ez a dokumentum ismerteti a biztonságos és hatékony feltételes hozzáférési házirendek megvalósításának lépéseit. Mielőtt elkezdené, győződjön meg arról, hogyan működik a [feltételes hozzáférés](overview.md) , és hogy mikor érdemes használni.

## <a name="what-you-should-know"></a>Alapismeretek

Tekintse át a feltételes hozzáférést olyan keretrendszerként, amely lehetővé teszi a szervezet alkalmazásaihoz és erőforrásaihoz való hozzáférés szabályozását önálló szolgáltatás helyett. Ennek következtében bizonyos feltételes hozzáférési beállításokhoz további funkciók konfigurálására van szükség. Beállíthat például egy olyan szabályzatot, amely egy adott [bejelentkezési kockázati szintre](../identity-protection/howto-identity-protection-configure-risk-policies.md)válaszol. Azonban a bejelentkezési kockázati szinten alapuló házirendhez engedélyezni kell [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md) szolgáltatást.

Ha további funkciókra van szükség, szükség lehet a kapcsolódó licencek beszerzésére is. Ha például a feltételes hozzáférés prémium szintű Azure AD P1 funkció, az Identity Protection prémium szintű Azure AD P2-licencet igényel.

A feltételes hozzáférési szabályzatok két típusa létezik: alapterv és standard. Az alapkonfiguráció [szabályzata](baseline-protection.md) előre definiált feltételes hozzáférési szabályzat. Ezen szabályzatok célja, hogy ellenőrizze, hogy rendelkezik-e legalább az alapszintű biztonsági beállítással. Alapterv-házirendek. Az alapkonfigurációs házirendek az Azure AD minden kiadásában elérhetők, és csak korlátozott testreszabási lehetőségeket biztosítanak. Ha egy forgatókönyv nagyobb rugalmasságot igényel, tiltsa le az alapszintű szabályzatot, és alkalmazza a követelményeit egy egyéni szabványos házirendben.

A standard feltételes hozzáférési szabályzatban az összes beállítást testreszabhatja, hogy a szabályzatot az üzleti igényeknek megfelelően módosítsa. A standard szabályzatokhoz prémium szintű Azure AD P1 licenc szükséges.

>[!NOTE]
> Azt javasoljuk, hogy az Azure AD-alapú feltételes hozzáférési szabályzat használatával a kezdeti eszköz hitelesítése után a legjobb kényszerítést kapja meg. Ez magában foglalja a záró munkameneteket, ha az eszköz nem felel meg a megfelelőségi és az eszköz kódjának.

## <a name="draft-policies"></a>Piszkozat-szabályzatok

Azure Active Directory feltételes hozzáférés lehetővé teszi, hogy a Felhőbeli alkalmazások védelmét új szintre vigye. Ebben az új szinten a felhőalapú alkalmazások elérésének módja a statikus hozzáférési konfiguráció helyett a dinamikus szabályzat kiértékelésén alapul. A feltételes hozzáférési szabályzattal választ adhat meg a hozzáférési feltételnek **(** **Ha ez történik**).

![Ok és válasz](./media/plan-conditional-access/10.png)

Határozza meg a tervezési modell használatával megvalósítani kívánt feltételes hozzáférési szabályzatokat. A tervezési gyakorlat:

- Segít az egyes szabályzatok válaszait és feltételeit körvonalazni.
- Egy jól dokumentált feltételes hozzáférési házirend-katalógust eredményez a szervezet számára. 

A katalógus használatával kivizsgálhatja, hogy a szabályzat megvalósítása megfelel-e a szervezet üzleti követelményeinek. 

A következő példa sablonnal feltételes hozzáférési szabályzatokat hozhat létre a szervezet számára:

|Ha *Ez* történik:|Ezután tegye *a*következőket:|
|-|-|
|Hozzáférési kísérlet történt:<br>-Cloud app *<br>– felhasználók és csoportok szerint*<br>Használatával<br>– 1. feltétel (például a Corp hálózaton kívül)<br>– 2. feltétel (például az eszközök platformja)|Az alkalmazáshoz való hozzáférés letiltása|
|Hozzáférési kísérlet történt:<br>-Cloud app *<br>– felhasználók és csoportok szerint*<br>Használatával<br>– 1. feltétel (például a Corp hálózaton kívül)<br>– 2. feltétel (például az eszközök platformja)|Hozzáférés biztosítása a következővel (és):<br>– 1. követelmény (például MFA)<br>– 2. követelmény (például eszköz megfelelősége)|
|Hozzáférési kísérlet történt:<br>-Cloud app *<br>– felhasználók és csoportok szerint*<br>Használatával<br>– 1. feltétel (például a Corp hálózaton kívül)<br>– 2. feltétel (például az eszközök platformja)|Hozzáférés biztosítása (vagy):<br>– 1. követelmény (például MFA)<br>– 2. követelmény (például eszköz megfelelősége)|

**Ha ez megtörténik, akkor ez** határozza meg azt a rendszerbiztonsági tag (**WHO**), amely megpróbál hozzáférni a felhőalapú alkalmazásokhoz (**mi**). Szükség esetén megadhatja a hozzáférési kísérlet **módját** is. A feltételes hozzáférésben azok az elemek határozzák meg, amelyek meghatározzák a WHO, mi, és hogyan ismert feltételeket. További információ: [Mi a feltételek a Azure Active Directory feltételes hozzáférésben?](concept-conditional-access-conditions.md) 

**Ezt követően**megadhatja a házirendre adott választ a hozzáférési feltételnek. A válaszában a további követelményekkel, például a többtényezős hitelesítéssel (MFA) is blokkolhatja vagy engedélyezheti a hozzáférést. A teljes áttekintést lásd: [Mi a hozzáférés-vezérlés Azure Active Directory feltételes hozzáférés?](controls.md)  

A feltételek és a hozzáférés-vezérlések kombinációja feltételes hozzáférési szabályzatot jelöl.

![Ok és válasz](./media/plan-conditional-access/51.png)

További információ: [Mi szükséges a szabályzat működéséhez](best-practices.md#whats-required-to-make-a-policy-work).

Ezen a ponton érdemes eldönteni, hogy a szabályzatok elnevezési szabványa milyen. Az elnevezési szabvány segítségével megtalálhatja a szabályzatokat, és megtekintheti azok célját anélkül, hogy megnyitná őket az Azure felügyeleti portálon. Nevezze el a szabályzatot, hogy megjelenjen:

- Sorszám
- A felhőalapú alkalmazás a következőre vonatkozik
- A válasz
- Kik érvényesek
- Ha érvényes (ha van ilyen)
 
![Elnevezési szabvány](./media/plan-conditional-access/11.png)

Míg a leíró név segít megőrizni a feltételes hozzáférési megvalósítás áttekintését, a sorozatszám hasznos lehet, ha egy beszélgetési szabályzatra kell hivatkoznia. Ha például egy társ-rendszergazdával kommunikál a telefonon, megkérheti őket, hogy a probléma megoldásához nyissa meg a szabályzat EM063.

A következő név például azt jelzi, hogy a házirendben a többtényezős hitelesítés szükséges a külső hálózatokon a Dynamics CRP-alkalmazás használatával:

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`

Az aktív szabályzatok mellett javasolt olyan letiltott szabályzatokat is megvalósítani, amelyek másodlagos [rugalmas hozzáférés-vezérlést biztosítanak az kimaradási/vészhelyzeti helyzetekben](../authentication/concept-resilient-controls.md). A készenléti szabályzatok elnevezési szabványának néhány további elemet is tartalmaznia kell: 

- `ENABLE IN EMERGENCY` az elején, hogy a név kitűnjön a többi szabályzat között.
- Annak a megszakadásnak a neve, amelyre érvényesnek kell lennie.
- Egy megrendelési sorozatszám, amely segít a rendszergazdának megismerni, hogy mely rendelési házirendeket kell engedélyezni. 

A következő név például azt jelzi, hogy ez a házirend az első négy házirendből áll, ha MFA-megszakadást tapasztal:

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`

## <a name="plan-policies"></a>Szabályzatok tervezése

A feltételes hozzáférési szabályzat megtervezésekor mérje fel, hogy létre kell-e hoznia házirendeket a következő eredmények eléréséhez. 

### <a name="block-access"></a>Hozzáférés letiltása

A hozzáférés blokkolásának lehetősége hatékony, mert:

- Kiszorítja a felhasználó összes többi hozzárendelését
- A teljes szervezet beléptetésének letiltása a bérlőre való bejelentkezéssel
 
Ha le szeretné tiltani az összes felhasználó hozzáférését, legalább egy felhasználót ki kell zárnia (általában vészhelyzeti hozzáférési fiókok) a szabályzatból. További információ: [felhasználók és csoportok kiválasztása](block-legacy-authentication.md#select-users-and-cloud-apps).  

### <a name="require-mfa"></a>MFA megkövetelése

A felhasználók bejelentkezési élményének egyszerűsítése érdekében érdemes lehet engedélyezni a felhőalapú alkalmazásoknak a Felhasználónév és jelszó használatával történő bejelentkezést. Azonban általában vannak olyan helyzetek, amelyeknek javasolt a fiókok ellenőrzésének erősebb formáját követelni. A feltételes hozzáférési szabályzattal bizonyos helyzetekben korlátozhatja az MFA követelményeit. 

Az MFA megkövetelésének gyakori felhasználási esetei a következők:

- [Adminisztrátorok szerint](howto-baseline-protect-administrators.md)
- [Adott alkalmazások számára](app-based-mfa.md) 
- [A hálózati helyekről nem bízik meg](untrusted-networks.md).

### <a name="respond-to-potentially-compromised-accounts"></a>Válaszadás potenciálisan feltört fiókokra

A feltételes hozzáférési szabályzatokkal automatizált válaszokat alkalmazhat a bejelentkezések potenciálisan feltört identitásokból való bevezetésére. A fiók biztonságának valószínűsége kockázati szintek formájában van kifejezve. Az Identity Protection két kockázati szintet számít ki: a bejelentkezési kockázat és a felhasználói kockázat. A bejelentkezési kockázatra adott válasz megvalósításához két lehetőség közül választhat:

- [A bejelentkezési kockázati feltétel](concept-conditional-access-conditions.md#sign-in-risk) a feltételes hozzáférési házirendben
- [A bejelentkezési kockázati szabályzat](../identity-protection/howto-sign-in-risk-policy.md) az Identity Protectionben 

A bejelentkezési kockázat feltételként való kezelése az előnyben részesített módszer, mert további testreszabási lehetőségeket biztosít.

A felhasználói kockázati szint csak az Identity Protection [felhasználói kockázati házirendjében](../identity-protection/howto-user-risk-policy.md) érhető el. 

További információ: [Mi az Azure Active Directory Identity Protection?](../identity-protection/overview.md) 

### <a name="require-managed-devices"></a>Felügyelt eszközök megkövetelése

A támogatott eszközöknek a Felhőbeli erőforrások eléréséhez való elterjedése segíti a felhasználók termelékenységének növelését. A flip oldalon valószínűleg nem szeretné, hogy a környezet bizonyos erőforrásaihoz olyan eszközök férhessenek hozzá, amelyek ismeretlen védelmi szinttel rendelkeznek. Az érintett erőforrások esetében meg kell követelnie, hogy a felhasználók csak felügyelt eszköz használatával férhessenek hozzájuk. További információkért lásd: [felügyelt eszközök megkövetelése a Cloud app Accesshez feltételes hozzáféréssel](require-managed-devices.md). 

### <a name="require-approved-client-apps"></a>Jóváhagyott ügyfélalkalmazások megkövetelése

A saját eszközök (BYOD) használatának egyik első döntése, hogy szükség van-e a teljes eszköz vagy csak a rajta lévő adatok kezelésére. Az alkalmazottak mobileszközöket használnak a személyes és munkahelyi feladatokhoz. Az adatvesztés elkerülése érdekében az alkalmazottak is hatékonyak lehetnek. A Azure Active Directory (Azure AD) feltételes hozzáféréssel korlátozhatja a Felhőbeli alkalmazásokhoz való hozzáférést a vállalati adatai védelme érdekében jóváhagyott ügyfélalkalmazások számára. További információkért lásd: [jóváhagyott ügyfélalkalmazások megkövetelése a Cloud app Accesshez feltételes hozzáféréssel](app-based-conditional-access.md).

### <a name="block-legacy-authentication"></a>Régi hitelesítési folyamat letiltása

Az Azure AD számos, a legszélesebb körben használt hitelesítési és engedélyezési protokollt támogat, beleértve az örökölt hitelesítést is. Hogyan akadályozhatja meg, hogy az örökölt hitelesítést használó alkalmazások hozzáférjenek a bérlő erőforrásaihoz? A javaslat célja, hogy csak feltételes hozzáférési házirenddel blokkolja őket. Ha szükséges, csak bizonyos felhasználóknak és adott hálózati helyszíneknek engedélyezi az örökölt hitelesítésen alapuló alkalmazások használatát. További információ: [az örökölt hitelesítés letiltása az Azure ad-ben feltételes hozzáféréssel](block-legacy-authentication.md).

## <a name="test-your-policy"></a>A szabályzat tesztelése

Mielőtt üzembe helyezi a szabályzatot az éles környezetben, tesztelje, hogy a várt módon viselkedik-e.

1. Tesztelési felhasználók létrehozása
1. Tesztelési terv létrehozása
1. A házirend konfigurálása
1. Szimulált bejelentkezés kiértékelése
1. A szabályzat tesztelése
1. Felesleges tartalmak törlése

### <a name="create-test-users"></a>Tesztelési felhasználók létrehozása

A szabályzatok teszteléséhez hozzon létre egy olyan felhasználói készletet, amely hasonló a környezetben lévő felhasználókhoz. A tesztek felhasználóinak létrehozásával ellenőrizheti, hogy a szabályzatok a várt módon működnek-e, mielőtt a valós felhasználókra hatással lenne, és potenciálisan megzavarják az alkalmazásokhoz és erőforrásokhoz való hozzáférésüket. 

Egyes szervezetek erre a célra rendelkeznek tesztelési Bérlővel. Azonban nehéz lehet az összes feltételt és alkalmazást újra létrehozni egy tesztelési bérlőben, hogy teljes körűen tesztelje a szabályzatok eredményét. 

### <a name="create-a-test-plan"></a>Tesztelési terv létrehozása

A tesztelési terv fontos, hogy összehasonlítsa a várt eredmények és a tényleges eredmények összehasonlítását. A tesztelés előtt mindig legyen várakozás. A következő táblázat példákat mutat be a tesztelési esetekre. Állítsa be a forgatókönyveket és a várt eredményeket a HITELESÍTÉSSZOLGÁLTATÓI házirendek konfigurálásának módja alapján.

|Szabályzat |Forgatókönyv |Várt eredmény | Eredmény |
|---|---|---|---|
|[MFA megkövetelése, ha nem működik](/azure/active-directory/conditional-access/untrusted-networks)|A meghatalmazott felhasználó bejelentkezik az *alkalmazásba* egy megbízható helyen/munkahelyen|A felhasználónak nem kell megadnia az MFA-t| |
|[MFA megkövetelése, ha nem működik](/azure/active-directory/conditional-access/untrusted-networks)|Az engedélyezve lévő felhasználó bejelentkezik az *alkalmazásba* , és nem megbízható helyen/munkahelyen|A rendszer felszólítja a felhasználót az MFA használatára, és sikeresen bejelentkezhet| |
|[MFA megkövetelése (rendszergazda)](/azure/active-directory/conditional-access/howto-baseline-protect-administrators)|Globális rendszergazda bejelentkezik az *alkalmazásba*|A rendszergazda az MFA-t kéri| |
|[Kockázatos bejelentkezések](/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|A felhasználó bejelentkezik az *alkalmazásba* egy [Tor-böngésző](/azure/active-directory/active-directory-identityprotection-playbook) használatával|A rendszergazda az MFA-t kéri| |
|[Eszközkezelés](/azure/active-directory/conditional-access/require-managed-devices)|A jogosultsággal rendelkező felhasználó megpróbál bejelentkezni egy jóváhagyott eszközről|Hozzáférés engedélyezve| |
|[Eszközkezelés](/azure/active-directory/conditional-access/require-managed-devices)|Az engedéllyel rendelkező felhasználó megpróbál bejelentkezni egy jogosulatlan eszközről|Hozzáférés letiltva| |
|[A kockázatos felhasználók jelszavának módosítása](/azure/active-directory/identity-protection/howto-user-risk-policy)|A jogosultsággal rendelkező felhasználó megpróbál bejelentkezni a feltört hitelesítő adatokkal (nagy kockázatú bejelentkezés)|A rendszer megkéri a felhasználót, hogy változtassa meg a jelszót, vagy a hozzáférés le legyen tiltva a szabályzat alapján| |

### <a name="configure-the-policy"></a>A házirend konfigurálása

A feltételes hozzáférési szabályzatok kezelése kézi feladat. A Azure Portal a feltételes hozzáférési szabályzatokat egyetlen központi helyen kezelheti – a feltételes hozzáférés lapon. A feltételes hozzáférés oldalának egyik belépési pontja a **Active Directory** navigációs ablaktábla **biztonsági** szakasza. 

![Feltételes hozzáférés](media/plan-conditional-access/03.png)

Ha többet szeretne megtudni a feltételes hozzáférési szabályzatok létrehozásáról, tekintse meg a többtényezős hitelesítés [megkövetelése adott alkalmazásokhoz Azure Active Directory feltételes hozzáféréssel](app-based-mfa.md)című témakört. Ez a rövid útmutató a következőket teszi lehetővé:

- Ismerkedjen meg a felhasználói felülettel.
- A feltételes hozzáférés működésének első benyomása. 

### <a name="evaluate-a-simulated-sign-in"></a>Szimulált bejelentkezés kiértékelése

Most, hogy konfigurálta a feltételes hozzáférési házirendet, érdemes tudnia, hogy a várt módon működik-e. Első lépésként használja a feltételes hozzáférés, [Mi a teendő, ha a szabályzat eszközzel](what-if-tool.md) szimulálni kívánja a felhasználó bejelentkezési adatait. A szimuláció megbecsüli a bejelentkezésnek a szabályzatokra gyakorolt hatását, és létrehoz egy szimulációs jelentést.

>[!NOTE]
> Míg a szimulált futtatáskor bemutatjuk, hogy milyen hatással van a feltételes hozzáférési házirend, nem helyettesíti a tényleges teszt-futtatást.

### <a name="test-your-policy"></a>A szabályzat tesztelése

Tesztelési esetek futtatása a tesztelési tervnek megfelelően. Ebben a lépésben az egyes szabályzatok teljes körű tesztelésén keresztül futtatja a felhasználók számára, hogy biztosan minden házirend megfelelően működjön. A fent létrehozott forgatókönyvek segítségével hajtsa végre az egyes teszteket.

Fontos, hogy ellenőrizze a szabályzat kizárási feltételeinek tesztelését. Előfordulhat például, hogy kizár egy felhasználót vagy csoportot egy olyan szabályzatból, amely MFA-t igényel. Annak tesztelése, hogy a rendszer a kizárt felhasználókat kéri-e a többtényezős hitelesítésre, mert az egyéb házirendek kombinációja megköveteli az MFA használatát a felhasználók számára.

### <a name="cleanup"></a>Felesleges tartalmak törlése

A tisztítási eljárás a következő lépésekből áll:

1. Tiltsa le a házirendet.
1. Távolítsa el a hozzárendelt felhasználókat és csoportokat.
1. Törölje a tesztek felhasználóit.  

## <a name="move-to-production"></a>Átállás éles üzemre

Ha az új szabályzatok készen állnak a környezetre, a következő fázisokban telepítse őket:

- Belső módosítási kommunikáció biztosítása a végfelhasználók számára.
- Kezdje a felhasználókat egy kis készlettel, és ellenőrizze, hogy a szabályzat a várt módon működik-e.
- Ha kibővít egy szabályzatot több felhasználó belefoglalásához, folytassa az összes rendszergazda kizárásával. A rendszergazdák kizárásával gondoskodhat arról, hogy valaki továbbra is hozzáférhessen egy szabályzathoz, ha módosításra van szükség.
- Csak akkor alkalmazza a házirendet az összes felhasználóra, ha szükséges.

Ajánlott eljárásként hozzon létre legalább egy felhasználói fiókot:

- Házirend-felügyeletre dedikált
- Kizárva az összes házirendből

## <a name="rollback-steps"></a>Visszaállítási lépések

Ha vissza kell állítania az újonnan megvalósított szabályzatokat, az alábbi beállítások közül egyet vagy többet kell használnia a visszaállításhoz:

1. **A házirend letiltása** – a szabályzat letiltásával gondoskodhat arról, hogy ne legyen érvényes, amikor egy felhasználó megpróbál bejelentkezni. Bármikor visszatérhet, és engedélyezheti a szabályzatot, ha azt szeretné használni.

   ![Szabályzat letiltása](media/plan-conditional-access/07.png)

1. **Felhasználó/csoport kizárása egy házirendből** – ha a felhasználó nem tud hozzáférni az alkalmazáshoz, dönthet úgy, hogy kizárja a felhasználót a szabályzatból.

   ![Exluce-felhasználók](media/plan-conditional-access/08.png)

   > [!NOTE]
   > Ezt a beállítást takarékosan kell használni, csak olyan helyzetekben, amikor a felhasználó megbízható. A felhasználót a lehető leghamarabb vissza kell venni a szabályzatba vagy a csoportba.

1. **A házirend törlése** – ha a házirend már nem szükséges, törölje.

## <a name="next-steps"></a>További lépések

Tekintse át az [Azure ad feltételes hozzáférési dokumentációját](index.yml) , és tekintse át a rendelkezésre álló információkat.
