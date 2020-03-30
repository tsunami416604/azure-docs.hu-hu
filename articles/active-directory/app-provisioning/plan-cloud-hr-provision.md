---
title: Felhőalapú HR-alkalmazás tervezése az Azure Active Directory felhasználói kiépítéséhez
description: Ez a cikk ismerteti a felhőbeli HR-rendszerek, például a Workday és a SuccessFactors integrálásának üzembe helyezését az Azure Active Directoryval. Az Azure AD és a felhőhr-rendszer integrálása teljes identitáséletciklus-kezelési rendszert eredményez.
services: active-directory
author: martincoetzer
manager: CelesteDG
tags: azuread
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha
ms.openlocfilehash: 28abe2dfa5a1a13ba09e20202180cb5e47d94072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522432"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Felhőalapú HR-alkalmazás tervezése az Azure Active Directory felhasználói kiépítéséhez

Az informatikai munkatársak korábban manuális módszerekre támaszkodtak az alkalmazottak létrehozásához, frissítéséhez és törléséhez. Olyan módszereket használtak, mint például CSV-fájlok vagy egyéni parancsfájlok feltöltése az alkalmazottak adatainak szinkronizálásához. Ezek a kiépítési folyamatok hibalehetőségek, nem biztonságosak és nehezen kezelhetők.

Az alkalmazottak, szállítók vagy függő dolgozók identitáséletciklusának kezeléséhez az [Azure Active Directory (Azure AD) felhasználói kiépítési szolgáltatás](../app-provisioning/user-provisioning.md) a felhőalapú humánerőforrás-alkalmazásokkal (HR) való integrációt kínál. Az alkalmazások közé tartozik például a Workday vagy a SuccessFactors.

Az Azure AD ezt az integrációt használja a következő felhőalapú HR-alkalmazások (alkalmazás) munkafolyamatok engedélyezéséhez:

- **Felhasználók kiépítése az Active Directoryba:** A felhőbeli HR-alkalmazásból kiválasztott felhasználói csoportok at egy vagy több Active Directory-tartományba helyezheti.
- **Csak felhőalapú felhasználók kiépítése az Azure AD-hez:** Olyan esetekben, ahol az Active Directory nincs használatban, a felhasználók kiépítése közvetlenül a felhőhr-alkalmazásból az Azure AD.In scenarios where Active Directory is isn't used, provision users directly from the cloud HR app to Azure AD.
- **Írjon vissza a felhőhr-alkalmazásba:** Írja vissza az e-mail-címeket és a felhasználónév-attribútumokat az Azure AD-ből a felhőhr-alkalmazásba.

> [!NOTE]
> Ez a telepítési terv bemutatja, hogyan telepítheti a felhőbeli HR-alkalmazás munkafolyamatait az Azure AD-felhasználók kiépítésével. Az automatikus felhasználói kiépítés szolgáltatásként (SaaS) alkalmazásokra történő központi telepítéséről a [Felhasználói üzembe helyezés megtervezése című témakörben](https://aka.ms/deploymentplans/provisioning)talál további információt.

## <a name="enabled-hr-scenarios"></a>Engedélyezett HR-forgatókönyvek

Az Azure AD felhasználói létesítési szolgáltatás lehetővé teszi a következő HR-alapú identitáséletciklus-kezelési forgatókönyvek automatizálását:

- **Új alkalmazotti felvétel:** Amikor új alkalmazottat ad hozzá a felhőhr-alkalmazáshoz, a rendszer automatikusan létrehoz egy felhasználói fiókot az Active Directoryban és az Azure AD-ben azzal a lehetőséggel, hogy visszaírja az e-mail címet és a felhasználónév-attribútumokat a felhőhr-alkalmazásba.
- **Alkalmazotti attribútum- és profilfrissítések:** Amikor egy alkalmazotti rekord, például a név, a cím vagy a kezelő frissül a felhőhr-alkalmazásban, a felhasználói fiók automatikusan frissül az Active Directoryban és az Azure AD-ben.
- **Az alkalmazottak felmondása:** Ha egy alkalmazott megszűnik a felhőhr-alkalmazásban, a felhasználói fiók automatikusan le van tiltva az Active Directoryban és az Azure AD-ben.
- **Alkalmazott újrafelvétel:** Amikor egy alkalmazottat újra felvesznek a felhőhr-alkalmazásban, a régi fiók automatikusan újraaktiválható vagy újra kiépíthető az Active Directoryés az Azure AD számára.

## <a name="who-is-this-integration-best-suited-for"></a>Kinek a legalkalmasabb ez az integráció?

A felhőbeli HR-alkalmazás integrációja az Azure AD-felhasználó-kiépítéssel ideális olyan szervezetek számára, amelyek:

- Előre összeállított, felhőalapú megoldást szeretne a felhőbeli HR-felhasználók kiépítéséhez.
- Közvetlen felhasználói kiépítés megkövetelése a felhőbeli HR-alkalmazásból az Active Directoryba vagy az Azure AD-be.
- A felhasználók kiépítése a felhőhr-alkalmazásból beszerzett adatok használatával.
- Csatlakozás, áthelyezés és a felhasználók szinkronizálásának megkövetelése egy vagy több Active Directory-erdővel, tartománnyal és számítógép-rendszerrel csak a felhőbeli HR-alkalmazásban észlelt változásadatok alapján.
- Használja az Office 365-öt levelezéshez.

## <a name="learn"></a>Tanulás

A felhasználói kiépítés megteremti a folyamatos identitás-szabályozás alapjait. Javítja a mérvadó identitásadatokra támaszkodó üzleti folyamatok minőségét.

### <a name="terms"></a>Fogalmak

Ez a cikk a következő kifejezéseket használja:

- **Forrásrendszer**: A felhasználók tárháza, amelyből az Azure AD rendelkezéseket. Egy példa egy felhőHR-alkalmazás, például a Workday vagy a SuccessFactors.
- **Célrendszer:** A felhasználók tárháza, amely az Azure AD rendelkezéseket. Ilyenek például az Active Directory, az Azure AD, az Office 365 vagy más SaaS-alkalmazások.
- **Joiners-Movers-Leavers folyamat**: Az új alkalmazottak, átvitelek és végződtetések kifejezése egy felhőhr-alkalmazás rekordrendszerként való használatával. A folyamat akkor fejeződik be, amikor a szolgáltatás sikeresen leteszi a célrendszerhez szükséges attribútumokat.

### <a name="key-benefits"></a>Főbb előnyök

A HR-alapú it-ellátás ezen képessége a következő jelentős üzleti előnyöket kínálja:

- **Növelje a termelékenységet:** Most már automatizálhatja a felhasználói fiókok és az Office 365-licencek hozzárendelését, és hozzáférést biztosíthat a kulcscsoportokhoz. A feladatok automatizálása azonnali hozzáférést biztosít az új alkalmazottakszámára a munkaeszközeikhez, és növeli a termelékenységet.
- **Kockázat kezelése:** Növelheti a biztonságot azáltal, hogy automatizálja a módosításokat az alkalmazottak állapota vagy a csoporttagságok alapján a felhőhr-alkalmazásból beáramló adatokkal. A módosítások automatizálása biztosítja, hogy a felhasználói identitások és a kulcsfontosságú alkalmazásokhoz való hozzáférés automatikusan frissüljön, amikor a felhasználók átváltanak vagy elhagyják a szervezetet.
- **A megfelelőség és az irányítás kezelése:** Az Azure AD támogatja a natív naplózási naplók a felhasználói kiépítési kérelmek által végrehajtott alkalmazások mind a forrás-, mind a célrendszerek. A naplózással egyetlen képernyőről követheti nyomon, hogy ki férhet hozzá az alkalmazásokhoz.
- **Költség kezelése:** Az automatikus kiépítés csökkenti a költségeket azáltal, hogy elkerüli a hatékonysági hiányosságokat és a manuális kiépítéshez kapcsolódó emberi hibákat. Csökkenti az örökölt és elavult platformok használatával idővel épített, egyedi fejlesztésű felhasználói kiépítési megoldások szükségességét.

### <a name="licensing"></a>Licencek

A felhőbeli HR-alkalmazás konfigurálásához az Azure AD-felhasználók kiépítési integrációja, szüksége van egy érvényes [Azure AD Premium-licenc](https://azure.microsoft.com/pricing/details/active-directory/) és egy licenc a felhőHR-alkalmazás, például a Workday vagy a SuccessFactors.

Emellett minden olyan felhasználóhoz érvényes Azure AD Premium P1 vagy magasabb előfizetési licencre is szüksége van, amely a felhőhr-alkalmazásból származik, és az Active Directoryba vagy az Azure AD-be lesz kiépítve. A felhőhr-alkalmazásban tulajdonában lévő licencek nem megfelelő száma hibákat okozhat a felhasználó kiépítése során.

### <a name="prerequisites"></a>Előfeltételek

- Az Azure AD globális rendszergazdai hozzáférésaz Azure AD Connect kiépítési ügynök konfigurálásához.
- A felhőhr-alkalmazás teszt- és éles példánya.
- Rendszergazdai engedélyek a felhőhr-alkalmazásban a rendszerintegrációs felhasználó létrehozásához és az alkalmazotti adatok tesztelési célokra történő teszteléséhez.
- Az Azure [AD Connect kiépítési ügynök](https://go.microsoft.com/fwlink/?linkid=847801)üzemeltetéséhez windows Server 2012 vagy nagyobb, .NET 4.7.1+ futtató vel rendelkező kiszolgáló szükséges az Active Directoryba való kiépítéshez.
- [Az Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) a felhasználók szinkronizálása az Active Directory és az Azure AD között.

### <a name="training-resources"></a>Képzési források

| **Erőforrások** | **Hivatkozás és leírás** |
|:-|:-|
| Videók | [Mi a felhasználói kiépítés az Active Azure Directoryban?](https://youtu.be/_ZjARPpI6NI) |
| | [A felhasználói kiépítés telepítése az Active Azure Directoryban](https://youtu.be/pKzyts6kfrw) |
| Oktatóanyagok | [Az SaaS-alkalmazások Azure AD-vel való integrálásáról szóló oktatóanyagok listája](../saas-apps/tutorial-list.md) |
| | [Oktatóanyag: A Munkanap konfigurálása automatikus felhasználói kiépítéshez](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| GYIK | [Automatikus felhasználói kiépítés](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Kiépítés a Workday-től az Azure AD-ig](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Megoldásarchitektúra

A következő példa a közös hibrid környezetek végpontok között történő felhasználói kiépítési megoldásarchitektúráját ismerteti, és a következőket tartalmazza:

- **Mérvadó HR-adatok a felhőhr-alkalmazásból az Active Directoryba.** Ebben a folyamatban a HR-esemény (Joiners-Movers-Leavers folyamat) a felhőbeli HR-alkalmazás bérlője. Az Azure AD-kiépítési szolgáltatás és az Azure AD Connect kiépítési ügynök kiépítése a felhasználói adatokat a felhőbeli HR-alkalmazás bérlőaz Active Directoryba. Az eseménytől függően az Active Directoryban műveletek et hozhat létre, frissíthet, engedélyezhet és letilthatja.
- **Szinkronizáljon az Azure AD-vel, és írja vissza az e-maileket és a felhasználóneveket a helyszíni Active Directoryból a felhőalapú HR-alkalmazásba.** Miután a fiókok frissítése az Active Directoryban, az Azure AD-vel az Azure AD-n keresztül szinkronizálódik. Az e-mail-címek és a felhasználónév attribútumok írhatók vissza a felhőhr-alkalmazás bérlője.

![Munkafolyamat-diagram](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>A munkafolyamat leírása

Az alábbi kulcsfontosságú lépéseket az ábra jelzi:  

1. **A HR-csapat** a tranzakciókat a felhőbeli HR-alkalmazás bérlőjében hajtja végre.
2. **Az Azure AD-kiépítési szolgáltatás** futtatja az ütemezett ciklusokat a felhőbeli HR-alkalmazás bérlőjéből, és azonosítja azokat a módosításokat, amelyeket fel kell dolgozni az Active Directoryval való szinkronizáláshoz.
3. **Az Azure AD-kiépítési szolgáltatás** meghívja az Azure AD Connect kiépítési ügynök egy kérelem hasznos, amely tartalmazza az Active Directory-fiók létrehozása, frissítése, engedélyezése és letiltása műveleteket.
4. **Az Azure AD Connect kiépítési ügynök** egy szolgáltatásfiókot használ az Active Directory-fiók adatainak kezeléséhez.
5. **Az Azure AD Connect** a [különbözeti szinkronizálást](../hybrid/how-to-connect-sync-whatis.md) futtatja a frissítések lekérése az Active Directoryban.
6. **Az Active Directory-frissítések** szinkronizálása az Azure AD-vel történik.
7. **Az Azure AD létesítési szolgáltatás** írási támogatja az e-mail attribútum és a felhasználónév az Azure AD a felhőHR-alkalmazás bérlője.

## <a name="plan-the-deployment-project"></a>A telepítési projekt megtervezése

Vegye figyelembe a szervezeti igényeket, amíg meghatározza a környezetben a központi telepítés stratégiáját.

### <a name="engage-the-right-stakeholders"></a>Vonja be a megfelelő érdekelt feleket

Ha a technológiai projektek kudarcot vallanak, ezt általában a hatásra, az eredményekre és a felelősségre vonatkozó, nem megfelelő elvárások miatt teszik. Hogy elkerülje ezeket a buktatókat, [győződjön meg arról, hogy a megfelelő érdekelt feleket vonja be.](https://aka.ms/deploymentplans) Győződjön meg arról is, hogy az érdekelt felek szerepei a projektben jól ismertek. Dokumentálja az érdekelt feleket, valamint projektbemeneteiket és elszámoltathatóságukat.

A HR-szervezet képviselőjének felvétele, aki a meglévő HR-üzleti folyamatokhoz és dolgozói identitáshoz, valamint a feladatadat-feldolgozási követelményekhez képest bemeneteket tud biztosítani.

### <a name="plan-communications"></a>A kommunikáció tervezése

A kommunikáció minden új szolgáltatás sikeréhez elengedhetetlen. Proaktív módon kommunikáljon a felhasználókkal arról, hogy mikor és hogyan változik a felhasználói élményük. Hadd tudják meg, hogyan szerezhetnek támogatást, ha problémákat tapasztalnak.

### <a name="plan-a-pilot"></a>Pilóta megtervezése

A HR üzleti folyamatok és identitás-munkafolyamatok integrálása a felhőhr-alkalmazásból a célrendszerekbe jelentős mennyiségű adatérvényesítést, adatátalakítást, adattisztítást és teljes körű tesztelést igényel, mielőtt üzembe helyezheti a megoldást az éles környezetben.

Futtassa a kezdeti konfigurációt egy [próbakörnyezetben,](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) mielőtt az éles környezetben lévő összes felhasználóra méretezi.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Válassza ki a felhőbeli HR-kiépítési összekötő alkalmazásokat

Az Azure AD-nek a felhőbeli HR-alkalmazás és az Active Directory közötti üzembe építési munkafolyamatainak megkönnyítése érdekében az Azure AD alkalmazásgalériából több üzembe létesítési összekötő alkalmazást is hozzáadhat:

- **Felhőhr-alkalmazás az Active Directory felhasználói kiépítés:** Ez a kiépítési összekötő alkalmazás megkönnyíti a felhasználói fiók kiépítését a felhőHR-alkalmazás egyetlen Active Directory-tartományba. Ha több tartománnyal rendelkezik, az alkalmazás egy példányát hozzáadhatja az Azure AD alkalmazásgyűjteményéből minden olyan Active Directory-tartományhoz, amelybe ki kell építenie.
- **Felhőbeli HR-alkalmazás az Azure AD-felhasználók kiépítése:** Bár az Azure AD Connect az az eszköz, amelyet az Active Directory-felhasználók azure AD-vel való szinkronizálásához kell használni, ez a kiépítési összekötő alkalmazás segítségével megkönnyítheti a csak felhőbeli felhasználók kiépítését a felhőbeli HR-alkalmazásból egyetlen Azure AD-bérlőhöz.
- **Felhőbeli HR-alkalmazás-visszaírás:** Ez a kiépítési összekötő alkalmazás megkönnyíti a felhasználó e-mail-címeinek visszaírását az Azure AD-ből a felhőbeli HR-alkalmazásba.

Például az alábbi kép felsorolja a Workday-összekötő alkalmazások, amelyek az Azure AD-alkalmazás gyűjteményében érhetők el.

![Az Azure Active Directory portálalkalmazás-gyűjtemény](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>Döntési folyamatdiagram

A következő döntési folyamatábrával azonosíthatja, hogy mely felhőalapú HR-kiépítési alkalmazások relevánsak a forgatókönyv szempontjából.

![Döntési folyamatdiagram](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>Tervezze meg az Azure AD Connect kiépítési ügynök üzembe helyezési topológiáját

A felhőhr-alkalmazás és az Active Directory közötti integráció négy összetevőt igényel:

- Felhőbeli HR-alkalmazás bérlője
- Összekötő alkalmazás kiépítése
- Azure AD Connect kiépítési ügynök
- Active Directory-tartomány

Az Azure AD Connect üzembe helyezési ügynök telepítési topológiája a felhőbeli HR-alkalmazás-bérlők és az Active Directory alárendelt tartományok, amelyek integrálását tervezi. Ha több Active Directory-tartománnyal rendelkezik, az attól függ, [disjoint](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace)hogy az Active Directory-tartományok összefüggőek vagy különállóak-e.

A döntés alapján válasszon egyet a telepítési forgatókönyvek közül:

- Egyfelhős HR-alkalmazás bérlője - > egy vagy több Active Directory gyermektartományt céloz meg egy megbízható erdőben
- Egyfelhős HR-alkalmazás bérlője -> több gyermektartományt céloz meg egy különálló Active Directory-erdőben

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>Egyfelhős HR-alkalmazás bérlője - > egy vagy több Active Directory gyermektartományt céloz meg egy megbízható erdőben

A következő éles környezetet javasoljuk:

|Követelmény|Ajánlás|
|:-|:-|
|Az üzembe helyezhető Azure AD Connect-kiépítési ügynökök száma|Kettő (magas rendelkezésre állás és feladatátvétel esetén)
|Konfigurálandó összekötőalkalmazások kiépítési száma|Gyermektartományonként egy alkalmazás|
|Kiszolgálóállomás az Azure AD Connect kiépítési ügynökéhez|Windows 2012 R2+ a földrajzi lagúna active directory tartományvezérlőklátótávolságával</br>Együtt létezhet az Azure AD Connect szolgáltatással|

![Flow a helyszíni ügynökökhöz](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>Egyfelhős HR-alkalmazás bérlője -> több gyermektartományt céloz meg egy különálló Active Directory-erdőben

Ebben a forgatókönyvben a felhasználók kiépítése a felhőhr-alkalmazásból a tartományok különálló Active Directory-erdőkben.

A következő éles környezetet javasoljuk:

|Követelmény|Ajánlás|
|:-|:-|
|A helyszíni üzembe helyezendő Azure AD Connect-létesítési ügynökök száma|Különálló Active Directory-erdőnként kettő|
|Konfigurálandó összekötőalkalmazások kiépítési száma|Gyermektartományonként egy alkalmazás|
|Kiszolgálóállomás az Azure AD Connect kiépítési ügynökéhez|Windows 2012 R2+ a földrajzi lagúna active directory tartományvezérlőklátótávolságával</br>Együtt létezhet az Azure AD Connect szolgáltatással|

![Egyfelhős HR-alkalmazás-bérlő szétválasztják az Active Directory erdőt](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Az Azure AD Connect kiépítési ügynökre vonatkozó követelmények

A felhőhr-alkalmazás az Active Directory felhasználói kiépítési megoldás megköveteli, hogy egy vagy több Azure AD Connect kiépítési ügynökök a Windows 2012 R2 vagy újabb rendszert futtató kiszolgálókon. A kiszolgálóknak legalább 4 GB RAM memóriával és .NET 4.7.1+ futásidővel kell rendelkezniük. Győződjön meg arról, hogy a gazdakiszolgáló hálózati hozzáféréssel rendelkezik a cél Active Directory tartományhoz.

A helyszíni környezet előkészítéséhez az Azure AD Connect kiépítési ügynök konfigurációs varázsló regisztrálja az ügynököt az Azure AD-bérlővel, megnyitja a [portokat](../manage-apps/application-proxy-add-on-premises-application.md#open-ports), engedélyezi az [URL-címekelérését,](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)és támogatja a [kimenő HTTPS-proxykonfigurációt.](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)

A kiépítési ügynök egy szolgáltatásfiókot használ az Active Directory-tartományokkal való kommunikációhoz. Az ügynök telepítése előtt hozzon létre egy olyan szolgáltatásfiókot az Active Directory – felhasználók és számítógépek beépülő modulban, amely megfelel az alábbi követelményeknek:

- Nem lejáró jelszó
- Delegált vezérlési engedélyek a felhasználói fiókok olvasásához, létrehozásához, törléséhez és kezeléséhez

Kiválaszthatja azokat a tartományvezérlőket, amelyeknek kezelniük kell a létesítési kérelmeket. Ha több földrajzilag elosztott tartományvezérlővel rendelkezik, telepítse a létesítő ügynököt ugyanazon a helyen, mint az előnyben részesített tartományvezérlők. Ez a pozicionálás javítja a végpontok között megoldás megbízhatóságát és teljesítményét.

Magas rendelkezésre állás érdekében több Azure AD Connect-létesítési ügynök is üzembe helyezhető. Regisztrálja az ügynököt a helyszíni Active Directory-tartományok azonos készletének kezeléséhez.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Hatókörszűrők és attribútumleképezések tervezése

Ha engedélyezi a kiépítést a felhőhr-alkalmazásból az Active Directoryba vagy az Azure AD-be, az Azure Portal az attribútumértékekattribútum-leképezésen keresztül szabályozza az attribútumértékeket.

### <a name="define-scoping-filters"></a>Hatókörszűrők definiálása

A [hatókör-szűrők](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) használatával határozza meg az attribútum-alapú szabályokat, amelyek meghatározzák, hogy mely felhasználókat kell kiépíteni a felhőhr-alkalmazásból az Active Directoryba vagy az Azure AD-be.

Az Asztalosok folyamatának indításakor gyűjtse össze a következő követelményeket:

- A felhőhr-alkalmazás az alkalmazottak és a függő alkalmazottak fedélzetére történő bejárására szolgál?
- Azt tervezi, hogy a felhőhr-alkalmazást használja az Azure AD-felhasználók kiépítéséhez az alkalmazottak és a függő dolgozók kezelésére?
- Tervezi, hogy a felhőbeli HR-alkalmazást csak a felhőbeli HR-alkalmazás felhasználóinak kiépítése az Azure AD-felhasználók számára szeretné kiépíteni? Példalehet, hogy csak alkalmazottak.

A követelményektől függően az attribútumleképezések konfigurálásakor beállíthatja a **Forrásobjektum-hatókör** mezőt, hogy kiválassza, hogy a felhőhr-alkalmazásban mely felhasználói csoportok legyenek hatókörben az Active Directoryba való kiépítéshez. További információkért tekintse meg a felhőhr-alkalmazás oktatóanyaga a gyakran használt hatókörszűrők.

### <a name="determine-matching-attributes"></a>Egyező attribútumok meghatározása

A kiépítés, a forrás és a célrendszer közötti meglévő fiókok egyeztetése. Amikor integrálja a felhőhr-alkalmazást az Azure AD-kiépítési szolgáltatással, [konfigurálhatja az attribútum-leképezést](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) annak meghatározásához, hogy milyen felhasználói adatoknak kell a felhőhr-alkalmazásból az Active Directoryba vagy az Azure AD-be áramlania.

Az Asztalosok folyamatának indításakor gyűjtse össze a következő követelményeket:

- Mi az egyedi azonosító ebben a felhőbeli HR-alkalmazásban, amely az egyes felhasználók azonosítására szolgál?
- Az identitás életciklusának szempontjából hogyan kezeli az újrafelvett? Az újrafelvett alkalmazottak megtartják a régi alkalmazotti azonosítóikat?
- Feldolgozza a jövőbeli dátummal ellátott alkalmazottakat, és előre létrehoz számukra Active Directory-fiókokat?
- Az identitás életciklusának szempontjából hogyan kezeli az alkalmazottfüggő dolgozói átalakítást, vagy más módon?
- A konvertált felhasználók megtartják a régi Active Directory-fiókjukat, vagy újakat kapnak?

A követelményektől függően az Azure AD támogatja a közvetlen attribútum-attribútum leképezést állandó értékek biztosításával vagy [kifejezések írása az attribútumleképezésekhez.](../app-provisioning/functions-for-customizing-application-data.md) Ez a rugalmasság biztosítja a végső ellenőrzést, hogy mi van feltöltve a célzott alkalmazás attribútum. A Microsoft [Graph API és](../app-provisioning/export-import-provisioning-configuration.md) a Graph Explorer segítségével exportálhatja a felhasználói kiépítési attribútum-hozzárendeléseket és sémát egy JSON-fájlba, és importálhatja azt az Azure AD-be.

Alapértelmezés szerint az attribútum a felhőhr-alkalmazásban, amely az egyedi alkalmazotti azonosítót jelöli, az *Active Directory egyedi attribútumához leképezett megfelelő* attribútumként lesz használatos. Például a Workday alkalmazás forgatókönyv, a **Workday** **WorkerID** attribútum van leképezve az Active Directory **employeeID** attribútum.

Több egyező attribútumot is beállíthat, és hozzárendelheti az egyeztetési prioritást. A rendszer egyező prioritással értékeli ki őket. Amint egyezést talál, a rendszer nem értékeli ki a további egyező attribútumokat.

[Testre szabhatja az alapértelmezett attribútumleképezéseket](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types)is, például módosíthatja vagy módosíthatja a meglévő attribútumleképezéseket. Az üzleti igényeknek megfelelően új attribútumleképezéseket is létrehozhat. További információkért tekintse meg a felhő HR-alkalmazás oktatóanyag (például [Workday)](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)az egyéni attribútumok leképezéséhez.

### <a name="determine-user-account-status"></a>Felhasználói fiók állapotának meghatározása

Alapértelmezés szerint a létesítési összekötő alkalmazás leképezi a HR felhasználói profil állapotát a felhasználói fiók állapotát az Active Directoryban vagy az Azure AD-ben annak meghatározásához, hogy engedélyezi vagy letiltja a felhasználói fiókot.

Amikor elindítja a Joiners-Leavers folyamatot, gyűjtse össze az alábbi követelményeket.

| Folyamat | Követelmények |
| - | - |
| **Asztalos** | Az identitás életciklusának szempontjából hogyan kezeli az újrafelvett? Az újrafelvett alkalmazottak megtartják a régi alkalmazotti azonosítóikat? |
| | Feldolgozza a jövőbeli dátummal ellátott alkalmazottakat, és előre létrehoz számukra Active Directory-fiókokat? Ezek a fiókok engedélyezett vagy letiltott állapotban jönnek létre? |
| | Az identitás életciklusának szempontjából hogyan kezeli az alkalmazottfüggő dolgozói átalakítást, vagy más módon? |
| | A konvertált felhasználók megtartják a régi Active Directory-fiókjukat, vagy újakat kapnak? |
| **Kilépők** | Az Active Directoryban dolgozó alkalmazottak és függő dolgozók esetében eltérően kezelik a felmondásokat? |
| | Milyen érvényességi dátumokat kell figyelembe venni a felhasználó megszüntetésének feldolgozásához? |
| | Hogyan befolyásolják az alkalmazottak és a függő dolgozók átalakításai a meglévő Active Directory-fiókokat? |
| | Hogyan dolgozza fel a Rescind műveletet az Active Directoryban? A visszasiktatási műveleteket kezelni kell, ha a jövőbeli dátummal ellátott alkalmazottak az Active Directoryban jönnek létre az Asztalegyesítő folyamat részeként. |

A követelményektől függően testreszabhatja a leképezési logikát [az Azure AD-kifejezések](../app-provisioning/functions-for-customizing-application-data.md) használatával, hogy az Active Directory-fiók engedélyezve van vagy le van tiltva az adatpontok kombinációja alapján.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>Felhőalapú HR-alkalmazás hozzárendelése az Active Directory felhasználói attribútumaihoz

Minden felhőalapú HR-alkalmazás az alapértelmezett felhőhr-alkalmazással az Active Directory-hozzárendelésekhez kapcsolódik.

Amikor elindítja a Joiners-Movers-Leavers folyamatot, gyűjtse össze a következő követelményeket.

| Folyamat | Követelmények |
| - | - |
| **Asztalos** | Az Active Directory-fiók létrehozási folyamatának kézikönyve automatikus vagy részben automatizált? |
| | Tervezi az egyéni attribútumok propagálását a felhőhr-alkalmazásból az Active Directoryba? |
| **Mozgatói** | Milyen attribútumokat szeretne feldolgozni, ha egy Movers-művelet a felhőhr-alkalmazásban történik? |
| | Végez konkrét attribútum-ellenőrzést a felhasználói frissítések idején? Ha igen, adja meg a részleteket. |
| **Kilépők** | Az Active Directoryban dolgozó alkalmazottak és függő dolgozók esetében eltérően kezelik a felmondásokat? |
| | Milyen érvényességi dátumokat kell figyelembe venni a felhasználó megszüntetésének feldolgozásához? |
| | Hogyan befolyásolják az alkalmazottak és a függő dolgozók átalakításai a meglévő Active Directory-fiókokat? |

A követelményektől függően módosíthatja a leképezéseket, hogy megfeleljenek az integrációs céloknak. További információkért tekintse meg az adott felhőHR-alkalmazás oktatóanyagát (például [a Workday-t)](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)a leképezendő egyéni attribútumok listájáért.

### <a name="generate-a-unique-attribute-value"></a>Egyedi attribútumérték létrehozása

Az Asztalosok folyamatának kezdeményezésekor előfordulhat, hogy egyedi attribútumértékeket kell létrehoznia, amikor olyan attribútumokat állít be, mint a CN, a samAccountName és az egyedi megkötésekkel rendelkező upn.

Az Azure AD függvény [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) kiértékeli az egyes szabályokat, és majd ellenőrzi a létrehozott érték egyediségét a célrendszerben. Például [lásd: Egyedi érték létrehozása a userPrincipalName (UPN) attribútumhoz.](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute)

> [!NOTE]
> Ez a funkció jelenleg csak az Active Directory felhasználói kiépítése esetén támogatott a Workday szolgáltatásban. Más kiépítési alkalmazásokkal nem használható.

### <a name="configure-active-directory-ou-container-assignment"></a>Active Directory szervezeti egység tároló-hozzárendelésének konfigurálása

Gyakori követelmény, hogy az Active Directory felhasználói fiókjait részlegek, helyek és részlegek alapján tárolókba helyezze. Amikor kezdeményez egy Mozgatos folyamatot, és a felügyeleti szervezet változása történik, előfordulhat, hogy át kell helyeznie a felhasználót az egyik szervezeti egységből a másikba az Active Directoryban.

A [Switch()](../app-provisioning/functions-for-customizing-application-data.md#switch) függvénnyel konfigurálhatja a szervezeti egység hozzárendelésének üzleti logikáját, és leképezheti azt az Active Directory **parentDistinguishedName**attribútumra.

Ha például a Helyi község HR-attribútuma alapján szeretne felhasználókat létrehozni a szervezeti egységben, a következő kifejezést **használhatja:**

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Ezzel a kifejezéssel, ha az önkormányzati érték Dallas, Austin, Seattle vagy London, a felhasználói fiók a megfelelő szervezeti egységben jön létre. Ha nincs egyezés, akkor a fiók az alapértelmezett szervezeti egységben jön létre.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Új felhasználói fiókok jelszókézbesítésének megtervezése

Amikor elindítja a Joiners folyamatot, be kell állítania és biztosítania kell az új felhasználói fiókok ideiglenes jelszavát. A felhőHR-es Azure AD-felhasználók kiépítése, az Azure AD [önkiszolgáló jelszó-visszaállítási](../authentication/quickstart-sspr.md) (SSPR) képesség a felhasználó számára az első napon.

Az SSPR egy egyszerű eszköz a rendszergazdák számára, hogy lehetővé tegyék a felhasználók számára jelszavuk visszaállítását vagy fiókjuk feloldását. A **mobilszám** attribútum a felhőhr-alkalmazásból az Active Directoryba, és szinkronizálhatja az Azure AD-vel. Miután a **Mobilszám** attribútum az Azure AD-ben, engedélyezheti az SSPR a felhasználó fiókjához. Ezután az első napon az új felhasználó használhatja a regisztrált és ellenőrzött mobilszámot a hitelesítéshez.

## <a name="plan-for-initial-cycle"></a>A kezdeti ciklus megtervezése

Amikor az Azure AD-kiépítési szolgáltatás első alkalommal fut, egy [kezdeti ciklust](../app-provisioning/how-provisioning-works.md#initial-cycle) hajt végre a felhőhr-alkalmazással, hogy pillanatképet hozzon létre a felhőbeli HR-alkalmazás összes felhasználói objektumáról. A kezdeti ciklusok idejéből közvetlenül függ, hogy hány felhasználó van jelen a forrásrendszerben. A kezdeti ciklus néhány felhőbeli HR-alkalmazás bérlők több mint 100.000 felhasználó is eltarthat egy hosszú ideig.

**A nagy felhőbeli HR-alkalmazások bérlői (>30 000 felhasználó)** futtassa a kezdeti ciklust progresszív szakaszokban. Csak azt követően indítsa el a növekményes frissítéseket, hogy ellenőrizze, hogy a megfelelő attribútumok vannak-e beállítva az Active Directoryban a különböző felhasználói kiépítési forgatókönyvekhez. Kövesse a sorrendet itt.

1. Futtassa a kezdeti ciklust csak a felhasználók korlátozott köre számára a [hatókörszűrő](#plan-scoping-filters-and-attribute-mapping)beállításával.
2. Ellenőrizze az Active Directory-fiók kiépítését és az első futtatásra kiválasztott felhasználók attribútumértékeit. Ha az eredmény megfelel az elvárásoknak, bontsa ki a hatókörszűrőt, hogy fokozatosan több felhasználót vegyen fel, és ellenőrizze a második futtatás eredményeit.

Miután elégedett a tesztfelhasználók kezdeti ciklusának eredményeivel, indítsa el a [növekményes frissítéseket.](../app-provisioning/how-provisioning-works.md#incremental-cycles)

## <a name="plan-testing-and-security"></a>Terv tesztelése és biztonsága

A központi telepítés minden egyes szakaszában a kezdeti próbaverzión keresztül a felhasználói kiépítés engedélyezéséig győződjön meg arról, hogy teszteli, hogy az eredmények a várt módon, és a kiépítési ciklusok naplózása.

### <a name="plan-testing"></a>Terv tesztelése

Miután konfigurálta a felhőhr-alkalmazást az Azure AD-felhasználók kiépítésére, futtasson teszteseteket annak ellenőrzésére, hogy ez a megoldás megfelel-e a szervezet követelményeinek.

|Forgatókönyvek|Várt eredmények|
|:-|:-|
|Új alkalmazottat veszünk fel a felhőHR-alkalmazásban.| - A felhasználói fiók ki van építve az Active Directoryban.</br>- A felhasználó bejelentkezhet az Active Directory tartományi alkalmazásokba, és végrehajthatja a kívánt műveleteket.</br>- Ha az Azure AD Connect szinkronizálás konfigurálva van, a felhasználói fiók is jön létre az Azure AD-ben.
|A felhasználó leáll a felhőHR-alkalmazásban.|- A felhasználói fiók le van tiltva az Active Directoryban.</br>- A felhasználó nem tud bejelentkezni az Active Directory által védett vállalati alkalmazásokba.
|A felhasználói felügyeleti szervezet frissül a felhőhr-alkalmazásban.|Az attribútumleképezés alapján a felhasználói fiók az Active Directory egyik szervezeti egységéből a másikba kerül.|
|A HR frissíti a felhasználó kezelőjét a felhőHR-alkalmazásban.|Az Active Directory kezelője mezője frissül, hogy tükrözze az új vezető nevét.|
|A HR újra felvesz egy alkalmazottat egy új szerepkörbe.|A viselkedés attól függ, hogy a felhőalapú HR-alkalmazás hogyan van konfigurálva az alkalmazottazonosítók létrehozásához:</br>- Ha a régi alkalmazottazonosítót újra felhasználja egy újrafelvételhez, az összekötő engedélyezi a meglévő Active Directory-fiókot a felhasználó számára.</br>- Ha az újbóli felvétel új alkalmazottazonosítót kap, az összekötő új Active Directory-fiókot hoz létre a felhasználó számára.|
|A HR az alkalmazottat szerződéses dolgozóvá alakítja, vagy fordítva.|Új Active Directory-fiók jön létre az új személyhez, és a régi fiók letiltásra kerül a konvertálás hatálybalépési napján.|

A korábbi eredmények segítségével határozza meg, hogyan válthatja át az automatikus felhasználói kiépítési megvalósítási megvalósításéles környezetbe a megadott ütemtervek alapján.

> [!TIP]
> Olyan technikákat alkalmazhat, mint az adatcsökkentés és az adattisztítás, amikor a tesztkörnyezetet éles adatokkal frissíti, hogy eltávolítson vagy elfedje a bizalmas személyes adatokat, hogy megfeleljen az adatvédelmi és biztonsági szabványoknak. 

### <a name="plan-security"></a>A biztonság megtervezése

Gyakori, hogy egy biztonsági felülvizsgálatot kell kérni egy új szolgáltatás központi telepítésének részeként. Ha biztonsági felülvizsgálatra van szükség, vagy még nem hajtották végre, tekintse meg a számos Azure [AD-tanulmányok,](https://www.microsoft.com/download/details.aspx?id=36391) amelyek áttekintést nyújtanak az identitás, mint szolgáltatás.

### <a name="plan-rollback"></a>Terv visszaállítása

Előfordulhat, hogy a felhőbeli HR-felhasználó létesítési megvalósítása nem működik a kívánt módon az éles környezetben. Ha igen, a következő visszaállítási lépések segíthetnek a korábbi ismert jó állapotra való visszatérésben.

1. Tekintse át a [kiépítési összefoglaló jelentést](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) és [a kiépítési naplókat,](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) hogy megállapítsa, milyen helytelen műveleteket hajtottak végre az érintett felhasználókon vagy csoportokon. Az összefoglaló jelentésről és naplókról a [Felhőbeli HR-alkalmazás felhasználóinak kiépítése című témakörben](#manage-your-configuration)talál további információt.
2. Az érintett felhasználók vagy csoportok utolsó ismert helyes állapota meghatározható a létesítési naplózási naplók vagy a célrendszerek (Azure AD vagy Active Directory) áttekintésével.
3. Az alkalmazás tulajdonosával együttműködve frissítse az érintett felhasználókat vagy csoportokat közvetlenül az alkalmazásban az utolsó ismert helyes állapotértékek használatával.

## <a name="deploy-the-cloud-hr-app"></a>A felhőhr-alkalmazás üzembe helyezése

Válassza ki a megoldási követelményekhez igazodó felhőhr-alkalmazást.

**Munkanap**: Ha munkavégző profilokat szeretne importálni a Workday szolgáltatásból az Active Directoryba és az Azure AD-be, olvassa el [az Oktatóanyag: A munkanap konfigurálása automatikus felhasználói kiépítéshez című témakört.](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment) Opcionálisan visszaírhatja az e-mail címet és a felhasználónevet a Workday-nek.

## <a name="manage-your-configuration"></a>A konfiguráció kezelése

Az Azure AD további betekintést nyújthat a szervezet felhasználói kiépítési használatába és működési állapotába a naplók és jelentések segítségével.

### <a name="gain-insights-from-reports-and-logs"></a>Betekintést nyerhet a jelentésekből és naplókból

Sikeres [kezdeti ciklus](../app-provisioning/how-provisioning-works.md#initial-cycle)után az Azure AD-kiépítési szolgáltatás továbbra is fut back-to-back növekményes frissítések et határozatlan ideig, időközönként meghatározott oktatóanyagok az egyes alkalmazások, amíg az alábbi események egyike történik:

- A szolgáltatás manuálisan leáll. Egy új kezdeti ciklus az [Azure Portal](https://portal.azure.com/) vagy a megfelelő [Microsoft Graph API-parancs](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) használatával aktiválódik.
- Az attribútumleképezések vagy a hatókörszűrők változása miatt új kezdeti ciklus indul el.
- A kiépítési folyamat magas hibaarány miatt karanténba kerül. Több mint négy hétig marad karanténban, ekkor automatikusan le van tiltva.

Ezeknek az eseményeknek és a kiépítési szolgáltatás által végzett összes egyéb tevékenységnek a megtekintéséhez és a [kiépítési tevékenységről szóló jelentések betekintéséhez olvassa el a naplók áttekintését.](../app-provisioning/check-status-user-account-provisioning.md)

#### <a name="azure-monitor-logs"></a>Azure Monitor-naplók

A kiépítési szolgáltatás által végrehajtott összes tevékenység et az Azure AD naplózási naplói rögzítik. Az Azure AD naplózási naplóit további elemzés céljából átirányíthatja az Azure Monitor-naplókba. Az Azure Monitor naplók (más néven Log Analytics-munkaterület) segítségével adatokat kérdezhet le az események megkereséséhez, trendek elemzéséhez és a különböző adatforrások közötti korreláció végrehajtásához. Ebből a [videóból](https://youtu.be/MP5IaCTwkQg) megtudhatja, milyen előnyökkel jár az Azure Monitor-naplók használata az Azure AD-naplókhoz gyakorlati felhasználói forgatókönyvekben.

Telepítse az [Azure AD-tevékenységnaplók naplóelemzési nézeteit,](../reports-monitoring/howto-install-use-log-analytics-views.md) hogy hozzáférjen a környezetében lévő események kiépítési eseményeiről szóló [előre összeállított jelentésekhez.](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views)

További információkért tekintse meg, hogyan [elemezheti az Azure AD-tevékenységnaplók at az Azure Monitor naplók.](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)

### <a name="manage-personal-data"></a>Személyes adatok kezelése

A Windows-kiszolgálóra telepített Azure AD Connect-kiépítési ügynök naplókat hoz létre a Windows eseménynaplójában, amelyek a felhőhr-alkalmazástól az Active Directory-hozzárendelésekhez való függően személyes adatokat tartalmazhatnak. A felhasználói adatvédelmi kötelezettségek nek való megfelelés érdekében állítson be egy Windows-ütemezett feladatot az eseménynapló törlésére, és biztosítsa, hogy az adatok at 48 óránál tovább ne tárolják.

Az Azure AD-kiépítési szolgáltatás nem hoz létre jelentéseket, elemzéseket, és nem nyújt elemzéseket 30 napon túl, mert a szolgáltatás nem tárol, nem dolgoz fel és nem tárol semmilyen adatot 30 napon túl.

### <a name="troubleshoot"></a>Hibaelhárítás

A kiépítés során esetlegesen felmerülő problémák elhárításához olvassa el az alábbi cikkeket:

- [Probléma a felhasználók Azure AD Gallery-alkalmazásba való kiépítésének konfigurálásakor](application-provisioning-config-problem.md)
- [Attribútum szinkronizálása a helyszíni Active Directoryból az Azure AD-hez egy alkalmazásba való kiépítéshez](user-provisioning-sync-attributes-for-mapping.md)
- [Az Azure AD Gallery-alkalmazásba való felhasználói kiépítés órákat vagy többet vesz igénybe](application-provisioning-when-will-provisioning-finish.md)
- [Probléma a rendszergazdai hitelesítő adatok mentése az Azure Active Directory-csoportalkalmazásba való felhasználói kiépítés konfigurálása során](application-provisioning-config-problem-storage-limit.md)
- [Egyetlen felhasználó sincs kiépítve egy Azure AD Gallery-alkalmazáshoz](application-provisioning-config-problem-no-users-provisioned.md)
- [A felhasználók helytelen készlete van kiépítve egy Azure AD Gallery-alkalmazáshoz](application-provisioning-config-problem-wrong-users-provisioned.md)
- [A Windows Eseménynapló beállítása ügynökhibaelhárításhoz](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Az Azure Portal naplózási naplóinak beállítása a szolgáltatások kalkapcsolatos hibaelhárításához](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Az AD felhasználói fiók létrehozásához felelős műveletek naplóinak ismertetése](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [A Kezelő frissítési műveleteinek naplóinak ismertetése](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [Gyakran előforduló hibák megoldása](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>További lépések

- [Kifejezések írása attribútumleképezéshez](functions-for-customizing-application-data.md)
- [Az Azure AD szinkronizálási API áttekintése](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- [A hatókörön kívül eső felhasználói fiókok törlésének kihagyása](skip-out-of-scope-deletions.md)
- [Azure AD Connect kiépítési ügynök: verziókiadási előzmények](provisioning-agent-release-version-history.md)
