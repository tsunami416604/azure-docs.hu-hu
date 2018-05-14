---
title: SaaS alkalmazás a felhasználók átadása az Azure AD automatikus |} Microsoft Docs
description: Hogyan használható az Azure AD automatikus kiépítéséhez, bemutatása leépíti, és folyamatosan frissíti a felhasználói fiókok több külső SaaS-alkalmazások között.
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: mtillman
editor: ''
ms.assetid: 58c5fa2d-bb33-4fba-8742-4441adf2cb62
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2017
ms.author: asmalser
ms.openlocfilehash: 72f796f0a4522b66feb55b827b02a83dcfdd3a01
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Felhasználói kiépítésének és megszüntetésének biztosítása SaaS-alkalmazásokhoz az Azure Active Directoryval történő automatizálásához
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>Mi az automatizált felhasználókiépítése SaaS-alkalmazásokhoz?
Azure Active Directory (Azure AD) lehetővé teszi, hogy automatizálja a létrehozását, a karbantartási és a felhő a felhasználói identitások eltávolítása ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) például a Dropbox, Salesforce, a ServiceNow és más alkalmazások.

**Az alábbiakban néhány példa arra, Mi ez a funkció lehetővé teszi:**

* Automatikusan új fiókok létrehozását a megfelelő rendszerek számára új ha azok csatlakoztatását a csapat vagy szervezet.
* Automatikusan inaktiválása fiókokat a megfelelő rendszerek amikor személyek hagyja, a csapat vagy szervezet.
* Győződjön meg arról, hogy az alkalmazások és a rendszer az identitások mindig naprakészek, a könyvtár, vagy az emberi erőforrások rendszer végbement változások alapján.
* Nem felhasználói objektumok, például a csoportokat, alkalmazásokat, amelyek támogatják a kiépítéséhez.

**Az alábbi funkciókat is automatizált felhasználókiépítése tartalmazza:**

* Felel meg a forrás- és rendszerek közötti meglévő identitások lehetővé teszi.
* Testre szabható attribútum-leképezésekhez, amelyek meghatározzák, milyen felhasználói adatokat kell a forrásrendszerben haladjanak a célrendszerbe.
* Nem kötelező e-mailes riasztásokhoz hibák történő üzembe helyezéséhez
* Jelentési és tevékenységkezelési naplófájlok segítségül hívásának figyelés és hibaelhárítás céljából.

## <a name="why-use-automated-provisioning"></a>Miért érdemes használni az automatizált üzembe helyezést?
Néhány gyakori összefüggések Ez a funkció használatához a következők:

* A költségek, a hatékonyság hiánya és a manuális üzembe helyezési folyamatok társított emberi tévedések elkerülése.
* Üzemeltetési és üzembe helyezési megoldások egyéni által fejlesztett és parancsfájlok fenntartása társított költségek elkerülése
* A szervezet biztonságos azonnal feloldhatja a felhasználói identitások kulcs Szolgáltatottszoftver-alkalmazásoknál, ha elhagyják a szervezet.
* Könnyen rendszerbe való importálás nagyszámú felhasználó egy adott SaaS-alkalmazáshoz vagy a rendszer.
* Lehetővé teszik, hogy házirendek annak eldöntéséhez, akik ki van építve, és akik való bejelentkezés egy alkalmazás egyetlen csoportja.


## <a name="how-does-automatic-provisioning-work"></a>Automatikus kiépítés működése
    
A **Azure AD-kiépítés szolgáltatás** SaaS-alkalmazásokhoz és más rendszerek, felhasználók minden egyes alkalmazás gyártója által biztosított felhasználói felügyeleti API-végpontok csatlakozva kiépítését. A felhasználó felügyeleti API végpontokkal programozott módon létrehozása, frissítése és eltávolítása a felhasználók számára az Azure AD. A kijelölt alkalmazások a létesítési szolgáltatás is létrehozhat frissítése, és további identitás kapcsolatos objektumok, például a csoportok és szerepkörök eltávolítása. 

![Kiépítés](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
*1. ábra: az Azure AD szolgáltatás kiépítését*

![Kimenő kiépítési](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
*2. ábra: "Kimenő" felhasználók átadásához a népszerű SaaS-alkalmazásokhoz az Azure ad-munkafolyamat*

![Kiépítés bejövő](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
*3. ábra: "Bejövő" felhasználói munkafolyamat népszerű emberi beruházási Management (HCM) alkalmazások az Azure Active Directoryba és a Windows Server Active Directory-kiépítés*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Milyen alkalmazások és rendszerek használható az Azure AD automatikus felhasználókiépítése?

Az Azure AD-funkciókat előre integrált számos népszerű SaaS-alkalmazásokhoz és az emberi erőforrások rendszerek támogatása, valamint általános támogatja az alkalmazások, amelyek megvalósítják az SCIM 2.0 szabvány meghatározott részeit.

Az összes olyan Azure AD támogatja az előzetesen beépített létesítési összekötő listájáért lásd: a [felhasználói kialakítási alkalmazás bemutatók felsorolása](active-directory-saas-tutorial-list.md).

Támogatás az Azure AD a felhasználók átadása alkalmazáshoz való hozzáadásáról további információkért lásd: [SCIM használata felhasználók és csoportok az Azure Active Directory alkalmazások automatikusan kiépítéséhez](active-directory-scim-provisioning.md).

Mérnöki csapat további alkalmazásokat az üzembe helyezési támogatás kéréséhez forduljon az Azure AD egy üzenet keresztül nyújt a [Azure Active Directory-visszajelzési fórumon](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).    

> [!NOTE]
> Ahhoz, hogy az alkalmazás támogassa a automatizált felhasználókiépítése azt kell adnia a szükséges felhasználói felügyeleti API-k, amelyek lehetővé teszik külső programok automatikus létrehozását, karbantartási és a felhasználók törlése. Ezért nem minden SaaS-alkalmazások kompatibilisek-e ezt a szolgáltatást. Felhasználói felügyeleti API-kat támogató alkalmazások esetében az Azure AD mérnöki csapathoz majd tudnak az alkalmazások üzembe helyezési összekötő létrehozásához, és mindezt a jelenlegi és jövőbeli ügyfél igényeitől van előrébb. 
    
    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Hogyan állíthatom be az alkalmazás automatikus kiépítés?

Az Azure AD szolgáltatás kiépítését, az indítja el a kiválasztott alkalmazás konfigurációja a  **[Azure-portálon](https://portal.azure.com)**. Az a **Azure Active Directory > Vállalati alkalmazások** szakaszban jelölje be **Hozzáadás**, majd **összes**, majd adja hozzá a forgatókönyvtől függően az alábbiak valamelyikét:

* Az összes alkalmazás a **kiemelt alkalmazások** szakasz támogatási automatikus kiépítés. Tekintse meg a [felhasználói kialakítási alkalmazás bemutatók felsorolása](active-directory-saas-tutorial-list.md) újak a.

* A "nem galéria alkalmazás" beállítást használja egyéni fejlett SCIM integrációja

![Katalógus](./media/active-directory-saas-app-provisioning/gallery.png)

Az alkalmazás felügyeleti képernyőn kiépítés konfigurálva van a **kiépítési** fülre.

![Beállítások](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* **Rendszergazdai hitelesítő adataival** , amelyek lehetővé teszik, hogy csatlakozzon a felhasználó-kezelési API, az alkalmazás által biztosított szolgáltatás kiépítését az Azure ad meg kell adni. Ez a szakasz emellett lehetővé teszi e-mail értesítések engedélyezése, ha a hitelesítő adatok sikertelen, vagy a kiosztási feladatának hiányzóra [karantén](#quarantine).

* **Attribútum-hozzárendelések** konfigurálhatók, amelyek meghatározzák a forrásrendszerben mezők (Példa: az Azure AD) lesz a tartalmukat történő szinkronizálását mezőket a célrendszeren (Példa: ServiceNow). Ha a cél alkalmazás ezt támogatja, ez a szakasz lehetővé teszi választható lehetőségként konfigurálhatja a felhasználói fiókok mellett csoportok kiépítését. "Egyező tulajdonságok" engedélyezi, hogy válassza ki, melyik mező használható felel meg a fiókokat a rendszer között. "[Kifejezések](active-directory-saas-writing-expressions-for-attribute-mappings.md)" lehetővé teszik a célrendszeren írás előtt a forrásrendszerben lekért értékek átalakító és módosításához. További információkért lásd: [testreszabása attribútum-leképezésekhez](active-directory-saas-customizing-attribute-mappings.md).

![Beállítások](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* **Helyezése hatókörszűrőkkel** kérje meg a létesítési szolgáltatás mely felhasználók és a csoport a forrásrendszerben kell hogy kiosztott és/vagy a célrendszerbe platformelőfizetés. Két szempontot való helyezése hatókörszűrőkkel, amelyek együtt lett kiértékelve, amely annak megállapítása, aki hatókör történő üzembe helyezéséhez:

    * **Szűrő attribútumértékek** -a "Forrás objektum hatóköre" menüjében a attribútum-leképezésekhez meghatározott attribútumértékek szűrését teszi lehetővé. Megadhatja például, hogy csak az "Értékesítési" a "Részleg" attribútummal rendelkező felhasználók kialakítási hatókörében kell lennie. További információkért lásd: [tartalmazó szűrőkkel](active-directory-saas-scoping-filters.md).

    * **Szűrő hozzárendelések** -létesítése "Hatókör" menüjére > beállítások a portál területen adhatja meg, hogy csak "hozzárendelt" felhasználók és csoportok kell hatókörében történő üzembe helyezéséhez, vagy kell-e az Azure AD-címtár összes felhasználója üzembe helyezve. "Hozzárendelésével" felhasználók és csoportok információkért lásd: [egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás Azure Active Directoryban](active-directory-coreapps-assign-user-azure-portal.md).
    
* **Beállítások** az alkalmazáshoz, beleértve, hogy fut-e a létesítési szolgáltatás működését.

* **A naplók** adja meg a szolgáltatás kiépítését az Azure AD által végzett minden művelet rögzíti. További részletekért lásd: a [létesítési jelentéskészítés – útmutató](active-directory-saas-provisioning-reporting.md).

![Beállítások](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

> [!NOTE]
> A szolgáltatás kiépítését az Azure AD-felhasználó is konfigurálhatók, és kezeli a [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).


## <a name="what-happens-during-provisioning"></a>Mi történik a kiépítés során?

Ha az Azure AD a forrásrendszerben, a létesítési szolgáltatás használ a [különbözeti lekérdezés funkció az Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) felhasználók és csoportok figyelése. A létesítési szolgáltatás fut egy kezdeti szinkronizálást a forrás és cél rendszer, rendszeres növekményes szinkronizálás követ. 

### <a name="initial-sync"></a>Kezdeti szinkronizálás
A létesítési szolgáltatás indításakor fogja az első szinkronizálás legalább egyszer végre:

1. Összes felhasználók és csoportok a forrásrendszerből lekérése során meghatározott összes attribútum lekérdezni a [attribútum-hozzárendelések](active-directory-saas-customizing-attribute-mappings.md).
2. A felhasználók és csoportok lett visszaadva, akkor e konfigurált szűrése [hozzárendelések](active-directory-coreapps-assign-user-azure-portal.md) vagy [Attribútumalapú helyezése hatókörszűrőkkel](active-directory-saas-scoping-filters.md).
3. Amikor egy felhasználó található hozzá kell rendelni vagy hatókör történő üzembe helyezéséhez, a szolgáltatás lekérdezi a célrendszeren egy egyező felhasználó használja a kijelölt [attribútumok megfelelő](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties). Példa: Ha a forrásrendszerben userPrincipal nevét a megfelelő attribútum, és van leképezve a felhasználónév a célrendszeren, majd a létesítési szolgáltatás lekérdezi a célrendszeren, amelyek megfelelnek a userPrincipal értékeket a forrásrendszerben felhasználónevek.
4. Ha egyező felhasználó nem található a célrendszeren, létrejön a forrás rendszer által visszaadott attribútumai használatával.
5. Ha egyező felhasználó megtalálható, frissül a forrásrendszerben által biztosított attribútumai használatával.
6. Ha az attribútum-leképezésekhez "hivatkozás" attribútumokat tartalmaznak, a szolgáltatás további frissítések a célrendszeren létrehozhatja és összekapcsolhatja a hivatkozott objektum hajtja végre. Azt jelzi, például egy felhasználó lehet-e a célrendszeren, ami a célrendszeren létrehozott egy másik felhasználó kapcsolódik a "Manager" attribútummal.
7. A kezdeti szinkronizálást, amely a kiindulási pontot nyújt az ezt követő növekményes szinkronizálás végén vízjel megőrzéséhez.

Egyes alkalmazások, például a ServiceNow, Google Apps, és be nem csak a felhasználók kiépítés, de a csoportok és azok tagjait is kiépítés támogatása. Ezekben az esetekben, ha csoport kiépítés engedélyezve van a [hozzárendelések](active-directory-saas-customizing-attribute-mappings.md), a szolgáltatás kiépítését szinkronizálja a felhasználók és a csoportokat, és ezt követően szinkronizálja a csoporttagságokat. 

### <a name="incremental-syncs"></a>Növekményes szinkronizálás
A kezdeti szinkronizálás után minden ezt követő szinkronizálások lesz:

1. A forrásrendszerben, felhasználók és csoportok, mert az utolsó vízjel tárolta frissített lekérdezése.
2. A felhasználók és csoportok lett visszaadva, akkor e konfigurált szűrése [hozzárendelések](active-directory-coreapps-assign-user-azure-portal.md) vagy [Attribútumalapú helyezése hatókörszűrőkkel](active-directory-saas-scoping-filters.md).
3. Amikor egy felhasználó található hozzá kell rendelni vagy hatókör történő üzembe helyezéséhez, a szolgáltatás lekérdezi a célrendszeren egy egyező felhasználó használja a kijelölt [attribútumok megfelelő](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties).
4. Ha egyező felhasználó nem található a célrendszeren, létrejön a forrás rendszer által visszaadott attribútumai használatával.
5. Ha egyező felhasználó megtalálható, frissül a forrásrendszerben által biztosított attribútumai használatával.
6. Ha az attribútum-leképezésekhez "hivatkozás" attribútumokat tartalmaznak, a szolgáltatás további frissítések a célrendszeren létrehozhatja és összekapcsolhatja a hivatkozott objektum hajtja végre. Azt jelzi, például egy felhasználó lehet-e a célrendszeren, ami a célrendszeren létrehozott egy másik felhasználó kapcsolódik a "Manager" attribútummal.
7. Ha a felhasználó korábban kialakítási hatókörében hatókör (beleértve a hozzá nem rendelt folyamatban) törlődik, a szolgáltatás a célrendszeren egy Update letiltja a felhasználót.
8. Ha a felhasználó korábban hatókörében állításához le van tiltva, vagy helyreállíthatóan törölt a forrásrendszerben, a szolgáltatás letiltása a felhasználó a célrendszeren egy frissítés keresztül.
9. Ha egy felhasználó kialakítási hatókörében korábban törölt a forrásrendszerben, a szolgáltatás törli a felhasználó a célrendszeren. Az Azure AD-felhasználók Miután bekerültek a helyreállíthatóan törölt törölt 30 nap.
10. A növekményes szinkronizálás, amely a kiindulási pontot nyújt az ezt követő növekményes szinkronizálás végén új vízjel megőrzéséhez.

>[!NOTE]
> Igény szerint letilthatja a létrehozási, frissítési vagy törlési műveletek használatával a **céloz objektum műveletek** jelölőnégyzetek a [attribútum-leképezésekhez](active-directory-saas-customizing-attribute-mappings.md) szakasz. Frissítés közben a felhasználó letiltása logika keresztül egy attribútum-leképezés például "accountEnabled" mező is vezérlik.

A létesítési szolgáltatás továbbra is futtassa végpont növekményes szinkronizálás határozatlan ideig meghatározott időközönként a [minden alkalmazásra vonatkoznak, az oktatóanyag](active-directory-saas-tutorial-list.md), amíg meg nem történik az alábbiak egyike:

* A manuálisan leállították a szolgáltatást az Azure portál használatával, vagy a megfelelő Graph API-parancs segítségével 
* Egy új kezdeti szinkronizálás aktiválódik, használja a **állapot törölje, majd indítsa újra** lehetőséget az Azure portálon, vagy a megfelelő Graph API-parancs használatával. Ez törli az összes tárolt vízjel, és hatására az összes adatforrás-objektumok újra kell kiértékelni.
* Egy új kezdeti szinkronizálási attribútum-leképezésekhez vagy hatókörének meghatározásához szűrők változás miatt elindul. Ez is egyetlen tárolt vízjel törli, és a hatására az összes adatforrás-objektumok újra kell kiértékelni.
* A telepítési folyamatot, a magas Hibaarány miatt (lásd alább) karanténba kerül, és négy hétnél tovább karanténban marad. Ebben az esetben a szolgáltatás automatikusan letiltásra kerül.

### <a name="errors-and-retries"></a>Hibák és a próbálkozások 
Ha egy adott felhasználó nem vehető fel, frissíteni vagy törölni a célrendszeren egy hiba miatt a célrendszeren, majd a művelet megkísérli a következő szinkronizálási ciklusban. Ha a felhasználó továbbra is sikertelen, az újbóli próbálkozások megkezdik kisebb gyakorisággal, fokozatosan méretezhetők naponta csak egy kísérlet történik. A hiba elhárításához ellenőrizze a rendszergazdák kell a [naplók](active-directory-saas-provisioning-reporting.md) a "folyamat letéti" határozza meg a legfelső szintű eseményeket okozhat, és hajtsa végre a megfelelő műveletet. Gyakori hibák a következők lehetnek:

* A forrásrendszerben, amely pedig szükséges lenne a célrendszeren feltöltve attribútum nem rendelkező felhasználók
* A forrásrendszerben nincs egyedi korlátozása a célrendszeren egy attribútum értékét, és ugyanazt az értéket rendelkező felhasználók megtalálható-e egy másik felhasználói rekordban

Ezek a hibák megoldhatók a forrásrendszerben az érintett felhasználó attribútum értékének beállításával, vagy az attribútum-leképezésekhez ütközést okoz.   

### <a name="quarantine"></a>Karantén
Ha a legtöbb vagy összes hívást felé irányuló a célrendszeren következetesen (például érvénytelen rendszergazdai hitelesítő adataival gazdabuszadaptereken) hiba miatt sikertelen, majd a kiosztási feladatának "karantén" állapotba kerül. A jelzett a [összesítő jelentés kiépítés](active-directory-saas-provisioning-reporting.md), és e-mailben Ha értesítő e-mailek be lett állítva, az Azure portálon. 

A karanténba helyezett, növekményes szinkronizálás gyakoriságának naponta egyszer fokozatosan csökken. 

A létesítési feladat összes meghatároznak a hibát okozó hibák után törlődnek a karanténba helyezett, és a következő szinkronizálási ciklusban elindul. Ha a kiosztási feladatának karantén négy hétnél tovább marad, a létesítési feladat le van tiltva.


## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Mennyi ideig tart a felhasználók létrehozásához?**

Teljesítmény attól függően, hogy a kiosztási feladatának működik-e egy kezdeti szinkronizálást, vagy egy növekményes szinkronizálás eltérőek lesznek.

A kezdeti szinkronizálás végrehajtásához szükséges idő közvetlenül függ hány felhasználók, csoportok és csoporttagok szerepelnek a forrásrendszerben lesz. Nagyon kicsi forrásrendszerek objektumok száz percek a kezdeti szinkronizálás hajthatja végre. Több ezer vagy kombinált objektumok millióit száz forrásrendszerek azonban hosszabb ideig tart.

Növekményes szinkronizálás szükséges idő az adott szinkronizálási ciklust észlelt megváltozása függ. Ha kevesebb mint 5000 felhasználó vagy csoport tagsági változása észlelhető, ezek is gyakran szinkronizálva egy 40 perces cikluson belül. 

Vegye figyelembe, hogy mind a forrás-és a függő, hogy általános teljesítményét. Néhány célrendszereket valósítja meg a kérelem sebességhatárok és a szabályozás, hogy is nagy szinkronizálási műveletek során a hatás teljesítményét, és ezekhez a rendszerekhez az összekötők kiépítés az előre elkészített Azure AD figyelembe ezt.

Teljesítmény egyben lassabb, ha sok hiba van (tárolja, amely a [naplók](active-directory-saas-provisioning-reporting.md)) és a létesítési szolgáltatás állapotba került egy "karantén" állapotba kerül.

**Hogyan javítható a teljesítmény a szinkronizálás?**

A legtöbb teljesítményproblémákat fordulhat elő, csoportok és a csoporttagok sok rendszerek kezdeti szinkronizálás során.

Ha nincs szükség a szinkronizálási csoportok és a csoporttagságokat, szinkronizálási teljesítmény nagy mértékben növelhető a szerint:

1. Beállítás a **kiépítési > Beállítások > hatókör** menü **összes**, hozzárendelt felhasználók és csoportok szinkronizálásakor helyett.
2. Használjon [helyezése hatókörszűrőkkel](active-directory-saas-scoping-filters.md) kiosztása a felhasználók listájának szűrése hozzárendelések helyett.

> [!NOTE]
> A csoport nevét és a csoport tulajdonságai (például a ServiceNow és a Google Apps) a létesítést támogató alkalmazások letiltása, ez is csökkenti egy kezdeti szinkronizáláshoz befejezéséhez szükséges időt. Ha nem szeretné, hogy csoporthoz tartozó nevek és a csoporttagságokat, az alkalmazás telepítéséhez, letilthatja ezt a a [attribútum-hozzárendelések](active-directory-saas-customizing-attribute-mappings.md) az üzembe helyezési konfigurációját.

**Hogyan követheti nyomon az aktuális üzembe helyezési feladat előrehaladásának?**

Tekintse meg a [létesítési jelentéskészítés – útmutató](active-directory-saas-provisioning-reporting.md).

**Milyen operációs rendszer, hogy ha a felhasználók nem tudnak megfelelően beolvasása kiépített?**

Összes sikertelen tárolja, amely az Azure AD a naplók. További információkért lásd: a [létesítési jelentéskészítés – útmutató](active-directory-saas-provisioning-reporting.md).

**Hogyan hozhat létre olyan alkalmazás, amely az üzembe helyezési szolgáltatással működik?**

Lásd: [SCIM használata felhasználók és csoportok az Azure Active Directory alkalmazások automatikusan kiépítéséhez](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning).

**Hogyan elküldheti a visszajelzését a mérnöki csapathoz?**

Kapcsolatfelvétel keresztül a [Azure Active Directory-visszajelzési fórumon](https://feedback.azure.com/forums/169401-azure-active-directory/).


## <a name="related-articles"></a>Kapcsolódó cikkek
* [SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [A felhasználók átadása attribútum-leképezésekhez testreszabása](active-directory-saas-customizing-attribute-mappings.md)
* [Attribútum-leképezésekhez kifejezések írása](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Helyezése Hatókörszűrőkkel felhasználói történő üzembe helyezéséhez](active-directory-saas-scoping-filters.md)
* [SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez az Azure Active Directoryból az alkalmazásokba](active-directory-scim-provisioning.md)
* [Az Azure AD szinkronizálási API – áttekintés](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
* [Részletes üzembe helyezés kimenő a felhasználók átadása, az alkalmazások tervezése](https://aka.ms/userprovisioningdeploymentplan)

