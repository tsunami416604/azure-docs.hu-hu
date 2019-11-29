---
title: A Cloud HR-alkalmazás megtervezése Azure Active Directory a felhasználók üzembe helyezéséhez
description: Ez a cikk a Cloud HR-rendszerek integrálásának telepítési folyamatát ismerteti, például a munkanapokat és a Sucessfactors a Azure Active Directoryával. Az Azure AD és a Felhőbeli HR rendszer integrálásával egy teljes identitás-életciklus-kezelési rendszer is elérhető.
services: active-directory
author: martincoetzer
manager: CelesteDG
tags: azuread
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha
ms.openlocfilehash: 5d55aafc29b3b022d1023077d2d8f459b0608ae7
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555651"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>A Cloud HR-alkalmazás megtervezése Azure Active Directory a felhasználók üzembe helyezéséhez

A hagyományos informatikai munkatársak az alkalmazotti adatszinkronizáláshoz használt, például a CSV-fájlok feltöltésére, frissítésére és törlésére szolgáló manuális módszerekre támaszkodtak. Ezek a kiépítési folyamatok hibákra hajlamosak, nem biztonságosak, és nehezen kezelhetők.

Az alkalmazottak, szállítók vagy függő munkavégzők teljes körű identitási életciklusának zökkenőmentes felügyeletéhez [Azure Active Directory (Azure ad) felhasználó-kiépítési szolgáltatás](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) a felhőalapú emberi erőforrások (HR) alkalmazásokkal, például a munkanapokkal vagy a SuccessFactors való integrációt is biztosítja.

Az Azure AD ezt az integrációt használja a következő Felhőbeli HR-alkalmazás-munkafolyamatok engedélyezéséhez:

- **Felhasználók** kiépítése a Felhőbeli HR-alkalmazásokból kiválasztott felhasználók számára egy vagy több Active Directory (ad) tartományba.
- **Csak felhőalapú felhasználók kiépítése az Azure ad** -be – olyan helyzetekben, ahol az ad nem használatos, a felhasználókat közvetlenül a Cloud HR alkalmazásból az Azure ad-be kell kiépíteni.
- **Írjon vissza a Cloud HR alkalmazásba.** – az e-mail-címek és a felhasználónevek attribútumainak megírása az Azure AD-ből a Cloud HR-alkalmazásba.

> [!NOTE]
> Ez a telepítési terv végigvezeti a Felhőbeli HR-alkalmazás munkafolyamatainak üzembe helyezéséhez az Azure AD-beli felhasználók kiépítésével. Az automatikus felhasználók SaaS-alkalmazásokba történő üzembe helyezésével kapcsolatos információkért tekintse meg a [felhasználók automatikus üzembe helyezésének megtervezése](https://aka.ms/deploymentplans/provisioning)című témakört.

## <a name="enabled-hr-scenarios"></a>Engedélyezett HR-forgatókönyvek

Az Azure AD-beli felhasználói kiépítési szolgáltatás lehetővé teszi a következő HR-alapú identitás-kezelési forgatókönyvek automatizálását:

- **Új alkalmazottak bérbeadása** – ha új alkalmazottat ad hozzá a Cloud HR-alkalmazáshoz, a rendszer automatikusan létrehoz egy felhasználói FIÓKOT az ad-ben és az Azure ad-ben, hogy az e-mail-cím és a username attribútumokat a Cloud HR alkalmazásba írja vissza.
- **Alkalmazotti attribútumok és profilok frissítései** – ha egy alkalmazotti rekord frissül a FELHŐbeli HR-alkalmazásban (például a nevük, a cím vagy a felettes), a felhasználói fiókja automatikusan FRISSÜL az ad-ben és az Azure ad-ben.
- **Alkalmazotti megszakítások** – ha egy alkalmazott leáll a Cloud HR-alkalmazásban, a felhasználói fiókja automatikusan le lesz tiltva az ad-ben és az Azure ad-ben.
- **Alkalmazottak** újratelepítése – ha egy alkalmazottat a FELHŐbeli HR-alkalmazásban újra felvesznek, a régi fiók automatikusan újraaktiválható vagy kiépíthető az ad és az Azure ad szolgáltatásba.

## <a name="who-is-this-integration-best-suited-for"></a>Ki a legmegfelelőbb integrációs csomag?

A Felhőbeli HR-alkalmazás integrációja az Azure AD-vel – a felhasználók üzembe helyezése ideális olyan szervezetek számára, amelyek:

- a Cloud HR-felhasználók üzembe helyezéséhez szükséges, előre elkészített felhőalapú megoldás
- a Felhőbeli HR-alkalmazásból az AD-be vagy az Azure AD-be való közvetlen felhasználói üzembe helyezés megkövetelése
- a Felhőbeli HR-alkalmazásból beszerzett adatok használatával kell kiépíteni a felhasználókat
- a felhasználók egy vagy több AD-erdővel, tartományokkal és szervezeti egységekkel való szinkronizálásának megkövetelése csak a Cloud HR-alkalmazásban észlelt változási információk alapján
- az Office 365 használata e-mailben

## <a name="learn"></a>Oktatóanyagok

A felhasználók üzembe helyezése létrehoz egy alapot a folyamatos identitás-irányításhoz, és javítja a mérvadó személyazonossági adatokra támaszkodó üzleti folyamatok minőségét.

### <a name="terms"></a>Fogalmak

Ez a cikk a következő kifejezéseket használja:

- Forrásoldali rendszer – az Azure AD által **kiépített** felhasználók tárháza (például egy Felhőbeli HR-alkalmazás, például a munkanap és a SuccessFactors)
- Célrendszer – az Azure AD által **kiépített** felhasználók tárháza (például ad, Azure ad, Office 365 vagy más SaaS-alkalmazásokhoz)
- **Csatlakozások** – a kimaradók folyamata – egy Felhőbeli HR-alkalmazással való új bérletek, átvitelek és leállások esetén használt kifejezés, amely rekordok rendszerét használja. A folyamat akkor fejeződik be, amikor a szolgáltatás sikeresen kiépíti a szükséges attribútumokat a célszámítógépen.

### <a name="key-benefits"></a>Főbb előnyök

A HR-alapú IT-kiépítés ezen funkciója jelentős üzleti előnyöket kínál az alábbiak szerint:

- A **termelékenység növelése** – mostantól automatizálhatja a felhasználói fiókok, a Office 365-licencek hozzárendelését, és hozzáférést biztosíthat a kulcsfontosságú csoportokhoz. A hozzárendelések automatizálása lehetővé tenné, hogy az új bérletek azonnal hozzáférjenek a feladathoz, és növelik a termelékenységet.
- **Kockázatkezelés** – növelheti a biztonságot úgy, hogy az alkalmazotti állapot vagy csoporttagságok alapján automatizálja a módosításokat a Felhőbeli HR-alkalmazásból származó adatokkal. A változások automatizálása biztosítja, hogy a felhasználói identitások és a Key apps hozzáférése automatikusan megtörténjen, amikor a felhasználók áttérnek vagy elhagyják a szervezetet.
- A **címek megfelelősége és szabályozása** – az Azure ad támogatja a natív naplókat a forrás-és a célcsoportok alkalmazásai által végzett felhasználói kiépítési kérelmek esetében. A naplózás segítségével nyomon követheti, hogy ki férhet hozzá az alkalmazásokhoz egyetlen képernyőről.
- A költséghatékony kiépítés **kezelése** csökkenti a költségeket, így elkerülhető a manuális kiépítés során felmerülő eredménytelenség és emberi hiba. Ez csökkenti a régi és elavult platformok használatával az idő múlásával az egyéni fejlesztésű felhasználói üzembe helyezési megoldások igényét.

### <a name="licensing"></a>Licencelés

Ha a Cloud HR-alkalmazást az Azure AD-beli felhasználók kiépítéséhez szeretné konfigurálni, érvényes [prémium szintű Azure ad licencre](https://azure.microsoft.com/pricing/details/active-directory/) és licencre van szükség a Cloud HR-alkalmazáshoz, például a munkanap-vagy a SuccessFactors.

Emellett érvényes prémium szintű Azure AD P1 vagy magasabb szintű előfizetési licencre van szükség minden olyan felhasználóhoz, aki a Cloud HR-alkalmazásból származik, és az AD vagy az Azure AD szolgáltatáshoz lesz kiépítve. A Felhőbeli HR-alkalmazásban nem megfelelő számú licenc hibát okozhat a felhasználók kiosztása során.

### <a name="prerequisites"></a>Előfeltételek

- Az Azure AD globális rendszergazdai hozzáférése az Azure AD Connect létesítési ügynök konfigurálásához.
- A Cloud HR-alkalmazás tesztelési és éles példánya.
- Rendszergazdai jogosultságok a Cloud HR alkalmazásban egy rendszerintegrációs felhasználó létrehozásához, valamint a tesztelési célú alkalmazottak ellenőrzésének megváltoztatásához.
- Ahhoz, hogy a felhasználó kiépítse az AD-t, egy Windows Server 2012 vagy újabb rendszert futtató kiszolgáló szükséges a .NET 4.7.1 + Runtime futtatásához a [Azure ad Connect létesítési ügynök](https://go.microsoft.com/fwlink/?linkid=847801)üzemeltetéséhez.
- [Azure ad Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect) a felhasználók az ad és az Azure ad közötti szinkronizálásához.

### <a name="training-resources"></a>Erőforrások betanítása

| **Forrásanyagok** | **Hivatkozás és leírás** |
|:-|:-|
| Videók | [Mi a felhasználók üzembe helyezése az aktív Azure-címtárban?](https://youtu.be/_ZjARPpI6NI) |
| | [A felhasználók üzembe helyezésének központi telepítése az Active Directory Azure-címtárban](https://youtu.be/pKzyts6kfrw) |
| Oktatóanyagok | Az [SaaS-alkalmazások Azure ad-vel való integrálását ismertető oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) |
| | [Oktatóanyag: munkanapok konfigurálása a felhasználók automatikus kiépítési felállításához](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#frequently-asked-questions-faq) |
| Gyakori kérdések | [Automatikus felhasználó-kiépítés](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#frequently-asked-questions) |
| | [Kiépítés a munkanapokból az Azure AD-be](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Megoldásarchitektúra

Az alábbi példa bemutatja a teljes körű felhasználó kiépítési megoldási architektúráját a gyakori hibrid környezetekhez, és a következőket tartalmazza:

- **Mérvadó HR-adatfolyam – a Felhőbeli HR-alkalmazásból az ad** -ben a folyamat során a HR-esemény (az összekapcsolások – a kimaradás-kimaradók feldolgozása) a Cloud HR-alkalmazás bérlője számára kezdeményezhető. Az Azure AD kiépítési szolgáltatás és a Azure AD Connect kiépítési ügynök a Cloud HR-alkalmazás bérlője felhasználói adatait a AD-be. Az eseménytől függően előfordulhat, hogy az AD-ben létrehoz/frissít/engedélyez/letilt műveleteket.
- **Szinkronizálás az Azure ad-vel és a visszaírási e-mailekkel és felhasználónévvel a helyszíni ad-ből a Cloud HR-alkalmazásba** – miután a fiókok frissültek az ad-ben, az Azure ad-val szinkronizálva Azure ad Connect, az e-mail-címek és a felhasználónevek attribútumai pedig visszaírhatók a Cloud HR-alkalmazás bérlőbe.

![Munkafolyamat-diagram](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>A munkafolyamat leírása

A diagramon megadott legfontosabb lépések a következők:  

1. **HR-csapat** végzi el a tranzakciókat a Cloud HR-alkalmazás bérlője számára.
2. Az **Azure ad-kiépítési szolgáltatás** futtatja az ütemezett ciklusokat a Cloud HR-alkalmazás bérlőn, és azonosítja azokat a módosításokat, amelyeket az ad-vel való szinkronizáláshoz kell feldolgozni.
3. Az **Azure ad-kiépítési szolgáltatás** meghívja a Azure ad Connect üzembe helyezési ügynököt az ad-fiók létrehozási/frissítési/engedélyezési/letiltási műveleteit tartalmazó kérelem-adattartalommal.
4. **Azure ad Connect kiépítési ügynök** egy szolgáltatásfiókot használ az ad-fiókadatok kezeléséhez.
5. A **Azure ad Connect** a különbözeti [szinkronizálást](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) futtatja az ad-frissítések lekéréséhez.
6. Az **ad** -frissítések az Azure ad-vel vannak szinkronizálva.
7. Az **Azure ad kiépítési szolgáltatás** cellavisszaírásokat e-mail-attribútumot és felhasználónevet az Azure ad-ből a Cloud HR-alkalmazás bérlője számára.

## <a name="plan-the-deployment-project"></a>Az üzembe helyezési projekt megtervezése

A környezetében érdemes figyelembe vennie a szervezeti igényeket is.

### <a name="engage-the-right-stakeholders"></a>A megfelelő résztvevők bevonása

A technológiai projektek meghibásodása esetén általában a hatás, az eredmények és a felelősségek eltérő elvárásai miatt válnak elérhetővé. A buktatók elkerülése érdekében [Győződjön meg arról, hogy a megfelelő érintett feleket folytatja](https://aka.ms/deploymentplans) , és hogy a projektben érintett szerepköröket jól megértette az érintett felek és a projekt bemeneti és elszámoltathatóság dokumentálása révén.

Meg kell adnia egy képviselőt a HR-szervezettől, amely a meglévő HR üzleti folyamatokhoz és a Worker Identity + Job adatok feldolgozási követelményeihez biztosít bemeneteket.

### <a name="plan-communications"></a>Kommunikáció tervezése

A kommunikáció minden új szolgáltatás sikeressége szempontjából kritikus fontosságú. Proaktív módon kell kommunikálni a felhasználókkal, hogy a felhasználói élmény hogyan módosuljon, mikor módosul, és hogyan szerezhet támogatást, ha problémákat tapasztal.

### <a name="plan-a-pilot"></a>Pilóta megtervezése

A HR üzleti folyamatainak és az identitás munkafolyamatainak a Cloud HR-alkalmazásból a megcélzott rendszerbe való integrálásához jelentős mennyiségű adatellenőrzésre, adatátalakításra, Adattisztításra és teljes körű tesztelésre van szükség a megoldás éles környezetben történő üzembe helyezése előtt.

Azt javasoljuk, hogy a kezdeti konfigurációt [kísérleti környezetben](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans#best-practices-for-a-pilot) futtassa, mielőtt az összes éles üzemben lévő felhasználónak méreteznie kellene.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Felhőbeli HR kiépítési összekötő alkalmazásainak kiválasztása

Az Azure AD üzembe helyezési munkafolyamatok a Cloud HR-alkalmazás és az AD között történő előállításához több kiépítési összekötő-alkalmazás is hozzáadható az Azure AD-alkalmazás-gyűjteményből:

- **Cloud HR-alkalmazás az ad User kiépítési** feladatokhoz – ez a kiépítési összekötő alkalmazás a felhőalapú HR-alkalmazásból egyetlen ad-tartományba helyezi el a felhasználói fiók kiépítési folyamatát. Ha több tartománya van, az alkalmazás egy példányát hozzáadhatja az Azure AD-alkalmazás-katalógusból minden olyan AD-tartományhoz, amelyre telepítenie kell.
- **Felhőbeli HR-alkalmazás az Azure ad** -beli felhasználók számára – noha Azure ad Connect az az eszköz, amelyet az ad-felhasználók Azure ad-hez való szinkronizálásához kell használni, ez a kiépítési összekötő alkalmazás a Cloud HR-alkalmazásból egyetlen Azure ad-bérlővé való kiépítés megkönnyítésére használható.
- **Cloud HR-alkalmazás visszaírási** – ez a kiépítési összekötő-alkalmazás lehetővé teszi a felhasználó e-mail-címeinek visszaírását az Azure ad-ből a Cloud HR alkalmazásba.

Az alábbi képen például az Azure AD App Galleryben elérhető, a munkanapokat összekötő alkalmazások szerepelnek.

![Azure Active Directory portál alkalmazás-gyűjtemény](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flowchart"></a>Döntési folyamatábra

Az alábbi döntési folyamatábra segítségével azonosíthatja, hogy mely Felhőbeli HR-kiépítési alkalmazások kapcsolódnak a forgatókönyvhöz.

![Döntési folyamatábra](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img3.png)

## <a name="design-azure-ad-connect-provisioning-agent-deployment-topology"></a>Tervezési Azure AD Connect kiépítési ügynök üzembe helyezési topológiája

A Cloud HR-alkalmazás és az AD közötti integrációs szolgáltatáshoz a következő négy összetevő szükséges:

- Cloud HR-alkalmazás bérlője
- Összekötő-alkalmazás üzembe helyezése
- Azure AD Connect kiépítési ügynök
- AD-tartomány

Az Azure AD Connect kiépítési ügynök üzembe helyezési topológiája a felhőalapú HR-alkalmazás bérlői és az integrálásra tervezett AD-gyermektartomány számától függ. Ha több AD-tartománnyal rendelkezik, attól függ, hogy az AD-tartományok összefüggő vagy [különállóak](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace)-e.

Az Ön döntése alapján válasszon egyet a telepítési forgatókönyvek közül:

- Egyetlen Felhőbeli HR-alkalmazás bérlője – > cél egy vagy több AD-gyermektartomány egy megbízható erdőben
- Egyetlen Felhőbeli HR-alkalmazás bérlője – > több gyermektartomány megcélzása egy különálló AD-erdőben

### <a name="single-cloud-hr-app-tenant---single-or-multiple-target-ad-child-domains-in-a-trusted-forest"></a>Egyetlen Felhőbeli HR-alkalmazás bérlője – > egy vagy több célzott AD-gyermektartomány egy megbízható erdőben

A következő éles konfigurációt javasoljuk:

|Követelmény|Ajánlás|
|:-|:-|
|Telepítendő Azure AD Connect kiépítési ügynökök száma|2 (a magas rendelkezésre álláshoz és a feladatátvételhez)
|A konfigurálni kívánt kiépítési összekötő-alkalmazások száma|egy alkalmazás/gyermektartomány|
|Azure AD Connect kiépítési ügynök kiszolgálójának gazdagépe|Windows 2012 R2 + a földrajzi elhelyezkedésű AD-tartományvezérlőkhöz</br>A Azure AD Connect szolgáltatással együtt is létezhet|

![A helyszíni ügynökök felé irányuló folyamat](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-ad-forest"></a>Egyetlen Felhőbeli HR-alkalmazás bérlője – > több gyermektartomány megcélzása egy különálló AD-erdőben

Ez a forgatókönyv magában foglalja a felhasználókat a Cloud HR-alkalmazásból a különálló AD-erdőkben lévő tartományokra való kiépítés során.

A következő éles konfigurációt javasoljuk:

|Követelmény|Ajánlás|
|:-|:-|
|A helyszíni üzembe helyezéshez Azure AD Connect kiépítési ügynökök száma|2/különálló AD-erdő|
|A konfigurálni kívánt kiépítési összekötő-alkalmazások száma|egy alkalmazás/gyermektartomány|
|Azure AD Connect kiépítési ügynök kiszolgálójának gazdagépe|Windows 2012 R2 + a földrajzi elhelyezkedésű AD-tartományvezérlőkhöz</br>A Azure AD Connect szolgáltatással együtt is létezhet|

![Egyetlen Felhőbeli HR-alkalmazás bérlője – különálló AD-erdő](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Azure AD Connect kiépítési ügynökre vonatkozó követelmények

A Felhőbeli HR-alkalmazás az AD-felhasználó kiépítési megoldásához legalább 4 GB RAM-mal és .NET 4.7.1 + futtatókörnyezettel kell telepítenie egy vagy több Azure AD Connect kiépítési ügynököt a Windows 2012 R2 vagy újabb rendszert futtató kiszolgálókon. Győződjön meg arról, hogy a gazdagépnek van hálózati hozzáférése a cél AD-tartományhoz.

A helyszíni környezet előkészítéséhez az Azure AD Connect létesítési ügynök konfigurálása varázsló regisztrálja az ügynököt az Azure AD-Bérlővel, megnyitja a [portokat](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#open-ports), [engedélyezi az URL-címek elérését](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#allow-access-to-urls), és támogatja a [kimenő HTTPS-proxy konfigurációját](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).

A kiépítési ügynök egy szolgáltatásfiókot használ az AD-tartomány (ok) val folytatott kommunikációhoz. Az ügynök telepítése előtt javasoljuk, hogy hozzon létre egy szolgáltatásfiókot az AD-felhasználók és számítógépek számára, amelyek megfelelnek az alábbi követelményeknek:

- Olyan jelszó, amely nem jár le
- Delegált vezérlési engedélyek a felhasználói fiókok olvasásához, létrehozásához, törléséhez és kezeléséhez

Kiválaszthatja azokat a tartományvezérlőket, amelyeknek kezelniük kell a kiépítési kérelmeket. Ha több földrajzilag elosztott tartományvezérlővel rendelkezik, a teljes körű megoldás megbízhatóságának és teljesítményének javításához telepítse a kiépítési ügynököt az elsődleges tartományvezérlő (k) helyével megegyező helyen.

A magas rendelkezésre állás érdekében több Azure AD Connect üzembe helyezési ügynököt is telepíthet, és regisztrálhatja a helyszíni AD-tartományok ugyanazon készletének kezelésére.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>A hatóköri szűrők és az attribútumok megfeleltetésének megtervezése

Ha engedélyezi az üzembe helyezést a Cloud HR alkalmazásból az AD-be vagy az Azure AD-be, a Azure Portal attribútum-hozzárendelésen keresztül szabályozza az attribútumok értékét.

### <a name="define-scoping-filters"></a>Hatóköri szűrők definiálása

A [hatóköri szűrők](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters) használatával meghatározhatja azokat az attribútum-alapú szabályokat, amelyek meghatározzák, hogy mely felhasználókat kell kiépíteni a Cloud HR-alkalmazásból az ad-be vagy az Azure ad-be.

Az összekapcsolási folyamat indításakor a következő követelményeket kell összegyűjtenie:

- A Felhőbeli HR-alkalmazás az alkalmazottakat és a függő feldolgozókat is felhasználja a fedélzeten?
- Azt tervezi, hogy a Cloud HR-alkalmazást az Azure AD-ben a felhasználók és a függőben lévő munkavállalók kezelésére használja?
- Szeretné kiépíteni a Cloud HR-alkalmazást az Azure AD-felhasználók számára a Cloud HR-alkalmazás felhasználóinak egy részhalmazára (például csak alkalmazottak esetében)?

A követelményektől függően az attribútumok leképezésének konfigurálásakor beállíthatja a **forrás objektum hatóköre** mezőt, hogy kiválassza, hogy a Cloud HR-alkalmazás mely felhasználói készletei legyenek az ad-re való kiépítés hatókörében. Tekintse meg a Cloud HR-alkalmazás oktatóanyagát a gyakran használt hatóköri szűrőkhöz.

### <a name="determine-matching-attributes"></a>Egyező attribútum (ok) meghatározása

A kiépítés lehetővé teszi a meglévő fiókok egyeztetését a forrás-és a célként megadott rendszeren. Ha integrálja a Cloud HR-alkalmazást az Azure AD kiépítési szolgáltatásával, [konfigurálhatja az attribútum-hozzárendelést](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal#mappings) annak meghatározásához, hogy a felhőalapú HR-alkalmazásból milyen felhasználói adatok legyenek az ad vagy az Azure ad rendszerbe.

Az összekapcsolási folyamat indításakor a következő követelményeket kell összegyűjtenie:

- Mi a Felhőbeli HR-alkalmazás egyedi azonosítója, amely az egyes felhasználók azonosítására szolgál?
- Az identitás életciklusának perspektívájában hogyan kezeli az újratelepítést? Megőrzik a régi alkalmazotti azonosítót?
- Készen áll-e a jövőbeli bérletek feldolgozására és az AD-fiókok előre való létrehozására?
- Az identitás életciklusának perspektívájában hogyan kezelheti az alkalmazottakat a függőben lévő munkavégzők átalakítására, vagy egyéb módon?
- Megőrzik a konvertált felhasználók a régi AD-fiókot, vagy újakat kapnak?

A követelménytől függően az Azure AD támogatja a közvetlen attribútumok közötti leképezést, állandó értékek biztosítását vagy [kifejezések írását az attribútumok leképezéséhez](https://docs.microsoft.com/azure/active-directory/active-directory-saas-writing-expressions-for-attribute-mappings). Ez a rugalmasság lehetővé teszi, hogy a megrendelt app attribútumban lévő adatokra vonatkozó végső felügyeletet biztosítson. A [Microsoft Graph API](https://docs.microsoft.com/azure/active-directory/manage-apps/export-import-provisioning-configuration) és a Graph Explorer használatával exportálhatja a felhasználók kiépítési attribútumait egy JSON-fájlba, majd importálhatja azt az Azure ad-be.

**Alapértelmezés**szerint a Felhőbeli HR alkalmazás azon attribútuma, amely az egyedi alkalmazott azonosítóját jelöli, a rendszer az *ad-ben az egyedi attribútumhoz hozzárendelt* egyező attribútumként használja. A munkanap alkalmazási forgatókönyvben például a *munkanap* *WORKERID* attribútum az ad *AlkalmazottKód* attribútumra van leképezve.

Több egyező attribútumot is beállíthat, és hozzárendelheti a megfeleltetési prioritást. A rendszer kiértékeli a megfeleltetések sorrendjét. Amint talál egyezést, nem lesz kiértékelve további egyező attribútumok.

[Testre szabhatja az alapértelmezett attribútum-hozzárendeléseket](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes#understanding-attribute-mapping-types) is, például módosíthatja vagy törölheti a meglévő attribútum-hozzárendeléseket, vagy létrehozhat új attribútum-hozzárendeléseket az üzleti igényeknek megfelelően. Tekintse meg a Cloud HR-alkalmazás oktatóanyagát (például a [munkanapokat](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)) a leképezésre szolgáló egyéni attribútumok listájához.

### <a name="determine-user-account-status"></a>Felhasználói fiók állapotának meghatározása

Alapértelmezés szerint a kiépítési összekötő alkalmazás a **HR felhasználói profil állapotát** az ad/Azure ad **felhasználói fiókjának** állapotára képezi le annak megállapításához, hogy engedélyezi vagy letiltja a felhasználói fiókot.

Az összekapcsolási/kilépési folyamat indításakor a következő követelményeket kell összegyűjtenie:

| Folyamat | Követelmények |
| - | - |
| **Asztalos** | Az identitás életciklusának perspektívájában hogyan kezeli az újratelepítést? Megőrzik a régi alkalmazotti azonosítót? |
| | Készen áll-e a jövőbeli bérletek feldolgozására és az AD-fiókok előzetes létrehozására? Jönnek létre ezek a fiókok az engedélyezett/letiltott állapotban? |
| | Az identitás életciklusának perspektívájában hogyan kezelheti az alkalmazottakat a függőben lévő munkavégzők átalakítására, vagy egyéb módon? |
| | Megőrzik a konvertált felhasználók a régi AD-fiókot, vagy újakat kapnak? |
| **Iskolaelhagyók** | Az alkalmazottak és a függőben lévő munkavállalók eltérő módon vannak kezelve az AD-ben? |
| | Milyen érvényes dátumokat kell figyelembe venni a felhasználói megszakítás feldolgozásához? |
| | Hogyan befolyásolja az alkalmazottak és a függőben lévő feldolgozó a meglévő Active Directory-fiókokat? |
| | Hogyan dolgozza fel a "törlése" műveletet az AD-ben? Az elállási műveleteket kezelni kell, ha a jövőbeli dátummal rendelkező bérletek az asztalos folyamat részeként jönnek létre az AD-ben. |

A követelményektől függően testreszabhatja a leképezési logikát az [Azure ad-kifejezések](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data) használatával, hogy az ad-fiók engedélyezve legyen vagy le legyen tiltva az adatpontok kombinációja alapján.

### <a name="map-cloud-hr-app-to-ad-user-attributes"></a>Felhőbeli HR-alkalmazás hozzárendelése AD felhasználói attribútumokhoz

Minden Felhőbeli HR-alkalmazás az alapértelmezett Felhőbeli HR-alkalmazást rendeli hozzá az AD-hozzárendelésekhez.

Az összekapcsolási/mozgató/kimaradó folyamat indításakor a következő követelményeket kell összegyűjtenie:

| Folyamat | Követelmények |
| - | - |
| **Asztalos** | Az Active Directory-fiók létrehozási folyamata manuális, automatizált vagy részlegesen automatizált? |
| | Tervezi a Cloud HR-alkalmazás egyéni attribútumainak az AD-be való propagálását? |
| **Mozgatói** | Milyen attribútumokat szeretne feldolgozni a Cloud HR-alkalmazásban, amikor egy "mozgatós" művelet zajlik? |
| | A felhasználói frissítések időpontjában bizonyos attribútumok érvényesek? Ha igen, adja meg a részleteket. |
| **Iskolaelhagyók** | Az alkalmazottak és a függőben lévő munkavállalók eltérő módon vannak kezelve az AD-ben? |
| | Milyen érvényes dátumokat kell figyelembe venni a felhasználói megszakítás feldolgozásához? |
| | Hogyan befolyásolja az alkalmazottak és a függőben lévő feldolgozó a meglévő Active Directory-fiókokat? |

A követelményektől függően módosíthatja a leképezéseket, hogy azok megfeleljenek az integrációs céloknak. A leképezni kívánt egyéni attribútumok listáját a Felhőbeli HR-alkalmazásra vonatkozó oktatóanyagban (például [munkanap](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)) tekintheti meg.

### <a name="generate-unique-attribute-value"></a>Egyedi attribútumérték előállítása

Az összekapcsolási folyamat indításakor előfordulhat, hogy egyedi attribútum-értékeket kell létrehoznia, amikor olyan attribútumokat állít be, mint például a CN, a samAccountName és az UPN, amely egyedi korlátozásokkal rendelkezik.

Az Azure AD-függvény [SelectUniqueValues](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#selectuniquevalue) értékeli az egyes szabályokat, majd ellenőrzi az egyediséghez generált értéket a célként megadott rendszeren. Lásd: példa [egyedi érték létrehozásához a userPrincipalName (UPN) attribútumhoz](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#generate-unique-value-for-userprincipalname-upn-attribute).

> [!NOTE]
> Ez a függvény jelenleg csak a "munkanap Active Directory a felhasználó kiosztása esetén támogatott." Más kiépítési alkalmazásokkal nem használható.

### <a name="configure-ad-ou-container-assignment"></a>AD OU-tároló hozzárendelésének konfigurálása

Gyakori követelmény, hogy az AD-felhasználói fiókokat az üzleti egységek, helyszínek és részlegek alapján tárolóba helyezze. Amikor kezdeményezői folyamatot kezdeményez, és ha a felügyeleti szervezet módosul, előfordulhat, hogy az AD-ben át kell helyeznie a felhasználót az egyik szervezeti egységből a másikba.

A [Switch ()](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#switch) függvény használatával konfigurálja a szervezeti egység hozzárendelésének üzleti logikáját, és képezze le az ad-attribútum *parentDistinguishedName*.

Ha például a "település" HR-attribútum alapján szeretne felhasználókat létrehozni a szervezeti egységben, akkor az alábbi kifejezést használhatja.

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Ezzel a kifejezéssel, ha a település értéke Dallas, Austin, Seattle vagy London, a felhasználói fiók a megfelelő szervezeti egységben lesz létrehozva. Ha nincs egyezés, a rendszer az alapértelmezett szervezeti egységben hozza létre a fiókot.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Új felhasználói fiókok jelszavas kézbesítésének megtervezése

Az összekapcsolási folyamat indításakor be kell állítania és továbbítania kell egy ideiglenes jelszót az új felhasználói fiókokhoz. A Felhőbeli HR és az Azure AD felhasználói üzembe helyezése esetén az Azure AD önkiszolgáló [jelszó-visszaállítási](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR) funkcióját az 1. napon tekintheti meg a felhasználó számára.

A SSPR egy egyszerű módja annak, hogy a rendszergazdák lehetővé tegyék a felhasználók számára a jelszavuk visszaállítását vagy a fiókok feloldását. A **Mobile Number** attribútumot kiépítheti a Cloud HR-alkalmazásból az ad-be, és szinkronizálhatja az Azure ad-vel. Miután a **Mobile Number** attribútum az Azure ad-ben, engedélyezheti a SSPR a felhasználói fiókhoz, hogy az 1. napon az új felhasználók a regisztrált és ellenőrzött mobil számot használják a hitelesítéshez.

## <a name="plan-for-initial-cycle"></a>A kezdeti ciklus megtervezése

Amikor az Azure AD-létesítési szolgáltatás első alkalommal fut, a a Cloud HR-alkalmazásból [kezdeti ciklust](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#what-happens-during-provisioning) hajt végre a Cloud HR alkalmazásban lévő összes felhasználói objektum pillanatképének létrehozásához. A kezdeti ciklusokhoz szükséges idő közvetlenül attól függ, hogy hány felhasználó van jelen a forrásoldali rendszeren. Néhány Felhőbeli HR-alkalmazás, több mint 100 000 felhasználó esetén a kezdeti ciklus hosszú időt vehet igénybe.

A **nagyméretű Felhőbeli HR-alkalmazások bérlői számára (> 30 000 felhasználó) azt javasoljuk,** hogy a kezdeti ciklust fokozatosan futtassa, és csak azt követően indítsa el a növekményes frissítéseket, hogy a megfelelő attribútumok be vannak ÁLLÍTVA az ad-ben a különböző felhasználói kiépítési forgatókönyvek esetében. Kövesse az alábbi sorrendet:

1. A kiindulási ciklust csak korlátozott felhasználók számára futtassa a hatókör- [szűrő](#plan-scoping-filters-and-attribute-mapping)beállításával.
2. Ellenőrizze az Active Directory-fiók üzembe helyezését és az első futtatáshoz kiválasztott felhasználók számára beállított attribútum-értékeket. Ha az eredmény megfelel az elvárásainak, bontsa ki a hatókör-szűrőt, hogy fokozatosan több felhasználót tartalmazzon, és ellenőrizze a második Futtatás eredményét.

Ha elégedett a kezdeti ciklus eredményével a felhasználók teszteléséhez, elindíthatja a [növekményes frissítéseket](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#incremental-cycles).

## <a name="plan-testing-and-security"></a>A tesztelés és a biztonság tervezése

Az üzembe helyezés minden egyes fázisában a kezdeti tesztelésen keresztül a felhasználók kiépítésének engedélyezésével győződjön meg arról, hogy a várt módon teszteli az eredményeket, és naplózza a kiépítési ciklusokat.

### <a name="plan-testing"></a>Tesztelési terv

Miután beállította a Cloud HR-alkalmazást az Azure AD-felhasználók üzembe helyezéséhez, tesztelési eseteket kell futtatnia annak ellenőrzéséhez, hogy a megoldás megfelel-e a szervezet követelményeinek.

|Alkalmazási helyzetek|Várt eredmények|
|:-|:-|
|Új alkalmazottak felvétele a Cloud HR-alkalmazásba| – A felhasználói fiók kiépítve az AD-ben.</br>– A felhasználók bejelentkezhetnek az Active Directory tartományi alkalmazásokba, és elvégezhetik a kívánt műveleteket.</br>-Ha a HRE-kapcsolat szinkronizálása be van állítva, akkor a felhasználói fiók is létrejön az Azure AD-ben.
|A felhasználó leáll a Cloud HR-alkalmazásban|– A felhasználói fiók le van tiltva az Active Directoryban.</br>– A felhasználó nem tud bejelentkezni az AD által védett vállalati alkalmazásokba.
|A felhasználó-felügyeleti szervezet frissítve van a Cloud HR-alkalmazásban|Az attribútumok leképezése alapján a felhasználói fiók az egyik szervezeti egységből egy másikba kerül át az AD-ben.|
|HR a Cloud HR-alkalmazás felhasználói kezelőjének frissítése|Az Active Directory Manager mezője frissül, hogy tükrözze az új felettes nevét.|
|HR új szerepkörbe helyezi át az alkalmazottat.|A viselkedés attól függ, hogy a Cloud HR-alkalmazás hogyan állítható be az alkalmazotti azonosítók létrehozásához:</br>-Ha a régi alkalmazott AZONOSÍTÓját újra felhasználják az újrafelhasználáshoz, akkor az összekötő engedélyezi a meglévő AD-fiókot a felhasználó számára.</br>– Ha az új alkalmazottak AZONOSÍTÓjának beszerzése megtörténik, az összekötő létrehoz egy új AD-fiókot a felhasználó számára.|
|HR átalakítja az alkalmazottat a szerződéses feldolgozóra, vagy fordítva|Új AD-fiók jön létre az új personához, és a régi fiók le lesz tiltva az átalakítás érvényességi dátumán.|

A fenti eredmények segítségével megtudhatja, hogyan alakíthatja át az automatikus felhasználó-kiépítési implementációt éles környezetbe a létrehozott ütemtervek alapján.

> [!TIP]
> Ajánlott olyan technikákat használni, mint például az adatok csökkentése és az adattisztítás, ha éles környezetben frissítjük a tesztkörnyezet adatait, hogy az adatvédelmi és biztonsági normáknak való megfelelés érdekében a bizalmas személyes adatokat (személyazonosításra alkalmas adatokat) is el lehessen távolítani.

### <a name="plan-security"></a>A biztonság megtervezése

Gyakori, hogy egy új szolgáltatás üzembe helyezésének részeként biztonsági felülvizsgálatra van szükség. Ha egy biztonsági felülvizsgálatra van szükség vagy még nem történt meg, tekintse át a számos Azure AD-tanulmányt [, amely](https://www.microsoft.com/download/details.aspx?id=36391) áttekintést nyújt az identitásról szolgáltatásról.

### <a name="plan-rollback"></a>Terv visszaállítása

Ha a Felhőbeli HR-felhasználó kiépítési implementációja nem működik megfelelően az éles környezetben, a következő visszaállítási lépések segítséget nyújthatnak a korábbi ismert jó állapot visszaállításához:

1. Tekintse át a [kiépítési összegző jelentést](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting#getting-provisioning-reports-from-the-azure-management-portal) és a [kiépítési naplókat](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting#provisioning-audit-logs) (lásd: a [Felhőbeli HR-alkalmazások kezelésének kezelése](#manage-your-configuration)) annak meghatározásához, hogy milyen műveleteket hajtottak végre az érintett felhasználókon és/vagy csoportokon.
2. Az érintett felhasználók és/vagy csoportok utolsó ismert megfelelő állapota a kiépítési naplók alapján vagy a megcélzott rendszerek (Azure AD vagy AD) áttekintésével határozható meg.
3. Az alkalmazás tulajdonosával az utolsó ismert helyes állapot értékeit használva frissítheti a közvetlenül az alkalmazásban érintett felhasználókat és/vagy csoportokat.

## <a name="deploy-the-cloud-hr-app"></a>A Cloud HR-alkalmazás üzembe helyezése

Válassza ki a Felhőbeli HR-alkalmazást, amely igazodik a megoldás követelményeihez.

**Munkanap** – útmutató: munkanapokat [konfigurálhat](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-your-deployment) a munkavégző profilok a munkanapokból az AD és az Azure ad-be történő importálásához szükséges lépések elvégzéséhez, és az e-mail-cím és a Felhasználónév munkanapokon történő opcionális visszaírásával.

## <a name="manage-your-configuration"></a>A konfiguráció kezelése

Az Azure AD további betekintést nyújt a szervezet felhasználó általi használatára és működési állapotára a naplók és a jelentések segítségével.

### <a name="gain-insights-from-reports-and-logs"></a>Jelentések és naplók betekintése

A sikeres [kezdeti ciklust](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#what-happens-during-provisioning)követően az Azure ad kiépítési szolgáltatás továbbra is határozatlan ideig futtatja az egyes alkalmazásokra vonatkozó oktatóanyagokban meghatározott időközöket, amíg az alábbi események valamelyike be nem következik:

- A szolgáltatás kézi leállítása megtörtént, és a rendszer a [Azure Portal](https://portal.azure.com/) vagy a megfelelő [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) -parancs használatával indítja el az új kezdeti ciklust.
- Az attribútum-hozzárendelések és a hatóköri szűrők módosítása miatt új kezdeti ciklust indítunk el.
- A kiépítési folyamat magas szintű hibák miatt Karanténba kerül, és a karanténban marad, több mint négy hétig, amikor az automatikusan le lesz tiltva.

Az események áttekintéséhez, valamint a kiépítési szolgáltatás által végzett összes egyéb tevékenységről [megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet le jelentéseket a kiépítési tevékenységekről](https://docs.microsoft.com/azure/active-directory/manage-apps/check-status-user-account-provisioning).

#### <a name="azure-monitor-logs"></a>Azure Monitor-naplók

A kiépítési szolgáltatás által végrehajtott összes tevékenységet az **Azure ad-naplók**rögzítik. További elemzés céljából átirányíthatja az Azure AD-naplókat Azure Monitor naplókba. **Azure monitor a naplók (más néven log Analytics munkaterület)** lehetővé teszik az adatok lekérdezését, az események keresését, a trendek elemzését és a különböző adatforrások közötti korreláció elvégzését. Tekintse meg ezt a [videót](https://youtu.be/MP5IaCTwkQg) , amelyből megtudhatja, milyen előnyökkel jár a Azure monitor naplók használata az Azure ad-naplókhoz gyakorlati felhasználói helyzetekben.

A [log Analytics-nézeteket telepítheti az Azure ad-tevékenységek naplóiba](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views) , hogy hozzáférjen az [előre elkészített jelentésekhez](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) a környezetében lévő kiépítési események köré.

További információ: [Az Azure ad-beli tevékenységek naplóinak elemzése a Azure monitor naplókkal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

### <a name="manage-personal-data"></a>Személyes adatok kezelése

A Windows Serverre telepített Azure AD Connect üzembe helyezési ügynök olyan naplókat hoz létre a Windows-eseménynaplóban, amelyek a Felhőbeli HR-alkalmazástól függően személyes adatait is tartalmazhatják az AD attribútum-hozzárendelésekhez. A felhasználói adatvédelmi kötelezettségek betartása érdekében beállíthat egy ütemezett Windows-feladatot az Eseménynapló törléséhez, és gondoskodhat arról, hogy a 48 órán túli adatok ne maradjanak meg.

Az Azure AD-kiépítési szolgáltatás nem hoz létre jelentéseket, nem végez elemzést, vagy 30 napon belül betekintést nyújt. Ezért az Azure AD kiépítési szolgáltatás 30 napon belül nem tárolja, dolgozza fel és nem őrzi meg az összes adatát. 

### <a name="troubleshoot"></a>Hibaelhárítás

A kiépítés során esetlegesen felmerülő problémák elhárításához tekintse meg az alábbi hivatkozásokat:

- [Hiba történt a felhasználók Azure AD Gallery-alkalmazásba való konfigurálásának beállításakor](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)
- [Egy attribútum szinkronizálása a helyszíni Active Directory az Azure AD-be az alkalmazásba való kiépítéshez](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning-sync-attributes-for-mapping)
- [Az Azure AD Gallery alkalmazásba történő felhasználói üzembe helyezése órákat vesz igénybe](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish)
- [Probléma a rendszergazdai hitelesítő adatok mentésekor, miközben a felhasználók üzembe helyezését egy Azure Active Directory Gallery-alkalmazáshoz konfigurálja](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-storage-limit)
- [Egyetlen felhasználó sincs kiépítve egy Azure AD Gallery-alkalmazásba](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned)
- [Helytelenek a felhasználók egy Azure AD Gallery-alkalmazásban való üzembe helyezése](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-wrong-users-provisioned)
- [Windows Eseménynapló beállítása az ügynökhöz – hibaelhárítás](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Azure Portal naplók beállítása a szolgáltatás hibaelhárításához](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Az AD felhasználói fiók létrehozási műveleteinek naplói](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#understanding-logs-for-ad-user-account-create-operations)
- [A kezelői frissítési műveletek naplóinak ismertetése](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#understanding-logs-for-manager-update-operations)
- [Gyakran előforduló hibák elhárítása](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Következő lépések

- [Kifejezések írása attribútum-hozzárendelésekhez](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)
- [Az Azure AD szinkronizációs API áttekintése](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- [A hatókörön kívüli felhasználói fiókok törlésének kihagyása](https://docs.microsoft.com/azure/active-directory/manage-apps/skip-out-of-scope-deletions)
- [Azure AD Connect kiépítési ügynök: verziók kiadásának előzményei](https://docs.microsoft.com/azure/active-directory/manage-apps/provisioning-agent-release-version-history)
