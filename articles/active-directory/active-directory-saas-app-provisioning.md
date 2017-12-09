---
title: "SaaS alkalmazás a felhasználók átadása az Azure AD automatikus |} Microsoft Docs"
description: "Hogyan használható az Azure AD automatikus kiépítéséhez, bemutatása leépíti, és folyamatosan frissíti a felhasználói fiókok több külső SaaS-alkalmazások között."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 58c5fa2d-bb33-4fba-8742-4441adf2cb62
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2017
ms.author: asmalser
ms.openlocfilehash: 0fdb1605487cca7ba3dc7f13fe8cbe3d1be78d44
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
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
* Testreszabási beállítások súgójában talál az Azure AD fér el a jelen konfigurációkat, az alkalmazások és azok a rendszerek, a szervezet által jelenleg használt.
* Nem kötelező e-mailes riasztásokhoz hibák történő üzembe helyezéséhez.
* Jelentési és tevékenységkezelési naplófájlok segítségül hívásának figyelés és hibaelhárítás céljából.

## <a name="why-use-automated-provisioning"></a>Miért érdemes használni az automatizált üzembe helyezést?
Néhány gyakori összefüggések Ez a funkció használatához a következők:

* A költségek, a hatékonyság hiánya és a manuális üzembe helyezési folyamatok társított emberi tévedések elkerülése érdekében.
* Üzemeltetési és üzembe helyezési megoldások egyéni által fejlesztett és parancsfájlok fenntartása társított költségek elkerülése érdekében
* A szervezet biztonságos azonnal feloldhatja a felhasználói identitások kulcs Szolgáltatottszoftver-alkalmazásoknál, ha elhagyják a szervezet.
* Könnyen rendszerbe való importálás érdekében a felhasználók tömeges számát egy adott SaaS-alkalmazáshoz vagy a rendszer.
* Lehetővé teszik a felhasználók kényelme érdekében, hogy a kiépítési megoldás a azonos alkalmazás-hozzáférési házirendjeit, amelyet az Azure AD egyszeri bejelentkezéshez megadott kijelentkezés futtatni.


## <a name="how-does-automatic-provisioning-work"></a>Automatikus kiépítés működése
    
A **Azure AD-kiépítés szolgáltatás** SaaS-alkalmazásokhoz és más rendszerek, felhasználók minden egyes alkalmazás gyártója által biztosított felhasználói felügyeleti API-végpontok csatlakozva kiépítését. A felhasználó felügyeleti API végpontokkal programozott módon létrehozása, frissítése és eltávolítása a felhasználók számára az Azure AD. A kijelölt alkalmazások a létesítési szolgáltatás is létrehozhat frissítése, és további identitás kapcsolatos objektumok, például a csoportok és szerepkörök eltávolítása. 

![Kiépítés](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
*1. ábra: az Azure AD szolgáltatás kiépítését*

![Kimenő kiépítési](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
*2. ábra: "Kimenő" felhasználók átadásához a népszerű SaaS-alkalmazásokhoz az Azure ad-munkafolyamat*

![Kiépítés bejövő](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
*3. ábra: "Bejövő" felhasználói munkafolyamat népszerű emberi beruházási Management (HCM) alkalmazások az Azure Active Directoryba és a Windows Server Active Directory-kiépítés*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Milyen alkalmazások és rendszerek használható az Azure AD automatikus felhasználókiépítése?

Az Azure AD-funkciókat előre integrált számos népszerű SaaS-alkalmazásokhoz és az emberi erőforrások rendszerek támogatása, valamint az alkalmazások, amelyek megvalósítják az adott részei általános támogatja a [SCIM 2.0 szabvány](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning).

A "Kiemelt" alkalmazások az Azure AD alkalmazás-katalógus mindegyiken automatizált felhasználókiépítése. [A kiemelt alkalmazások listája megtekinthetők.](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured)

Ahhoz, hogy egy alkalmazás támogassa a automatizált felhasználók átadásához akkor először biztosítania kell a szükséges felhasználói felügyeleti végpontok, amelyek lehetővé teszik külső programok automatikus létrehozását, karbantartási és a felhasználók törlése. Ezért nem minden SaaS-alkalmazások kompatibilisek-e ezt a szolgáltatást. Felhasználói felügyeleti API-kat támogató alkalmazások esetében az Azure AD mérnöki csapathoz majd tudnak az alkalmazások üzembe helyezési összekötő létrehozásához, és mindezt a jelenlegi és jövőbeli ügyfél igényeitől van előrébb. 

Mérnöki csapat további alkalmazásokat az üzembe helyezési támogatás kéréséhez forduljon az Azure AD egy üzenet keresztül nyújt a [Azure Active Directory-visszajelzési fórumon](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/category/172035-user-provisioning). 
    
    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Hogyan állíthatom be az alkalmazás automatikus kiépítés?

Az Azure AD szolgáltatás kiépítését, az indítja el a kiválasztott alkalmazás konfigurációja a  **[Azure-portálon](https://potal.azure.com)**. Az a **Azure Active Directory > Vállalati alkalmazások** szakaszban jelölje be **Hozzáadás**, majd **összes**, majd adja hozzá a forgatókönyvtől függően az alábbiak valamelyikét:

* Az összes alkalmazás a **kiemelt alkalmazások** szakasz támogatási automatikus kiépítés

* A "nem galéria alkalmazás" beállítást használja egyéni fejlett SCIM integrációja

![Katalógus](./media/active-directory-saas-app-provisioning/gallery.png)

Az alkalmazás felügyeleti képernyőn kiépítés konfigurálva van a **kiépítési** fülre.

![Beállítások](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* **Rendszergazdai hitelesítő adataival** , amelyek lehetővé teszik, hogy csatlakozzon a felhasználó-kezelési API, az alkalmazás által biztosított szolgáltatás kiépítését az Azure ad meg kell adni.

* **Attribútum-hozzárendelések** konfigurálhatók, amelyek meghatározzák a forrásrendszerben mezők (Példa: az Azure AD) lesz a tartalmukat történő szinkronizálását mezőket a célrendszeren (Példa: ServiceNow). Ha a cél alkalmazás ezt támogatja, ez a szakasz lehetővé teszi választható lehetőségként konfigurálhatja a felhasználói fiókok mellett csoportok kiépítését. "Egyező tulajdonságok" engedélyezi, hogy válassza ki, melyik mező használható felel meg a fiókokat a rendszer között. "[Kifejezések](active-directory-saas-writing-expressions-for-attribute-mappings.md)" lehetővé teszik a célrendszeren írás előtt a forrásrendszerben lekért értékek átalakító és módosításához. További információkért lásd: [testreszabása attribútum-leképezésekhez](active-directory-saas-customizing-attribute-mappings.md).

![Beállítások](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* **Helyezése hatókörszűrőkkel** kérje meg a létesítési szolgáltatás mely felhasználók és a csoport a forrásrendszerben kell hogy kiosztott és/vagy a célrendszerbe platformelőfizetés. Két szempontot való helyezése hatókörszűrőkkel, amelyek együtt lett kiértékelve, amely annak megállapítása, aki hatókör történő üzembe helyezéséhez:

* **Szűrő attribútumértékek** -a "Forrás objektum hatóköre" menüjében a attribútum-leképezésekhez meghatározott attribútumértékek szűrését teszi lehetővé. Megadhatja például, hogy csak az "Értékesítési" a "Részleg" attribútummal rendelkező felhasználók kialakítási hatókörében kell lennie.

* **Szűrő hozzárendelések** -létesítése "Hatókör" menüjére > beállítások a portál területen adhatja meg, hogy csak "hozzárendelt" felhasználók és csoportok kell hatókörében történő üzembe helyezéséhez, vagy kell-e az Azure AD-címtár összes felhasználója üzembe helyezve. "Hozzárendelésével" felhasználók és csoportok információkért lásd: [egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás Azure Active Directoryban](active-directory-coreapps-assign-user-azure-portal.md).
    
* **Beállítások** az alkalmazáshoz, beleértve, hogy fut-e a létesítési szolgáltatás működését.

* **A naplók** adja meg a szolgáltatás kiépítését az Azure AD által végzett minden művelet rögzíti. További részletekért lásd: a [létesítési jelentéskészítés – útmutató](active-directory-saas-provisioning-reporting.md).

![Beállítások](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

## <a name="what-happens-during-provisioning"></a>Mi történik a kiépítés során?

1. Ha engedélyezi az alkalmazás első indításakor kiépítés, a következő műveleteket kell végrehajtani:
   * Az Azure AD megkísérli minden meglévő felhasználók kereséséhez a könyvtárban megfelelő letöltésük a SaaS-alkalmazás. Amikor egy felhasználó egyezik, azok *nem* automatikusan engedélyezve van, az egyszeri bejelentkezést. Ahhoz, hogy egy felhasználó hozzáférjen az alkalmazáshoz ezek társítva kell lenni explicit módon az alkalmazás az Azure AD közvetlenül, vagy csoporttagság.
   * Ha korábban már megadta felhasználókat hozzá kell rendelni az alkalmazáshoz, és az Azure AD sikertelen lesz a meglévő fiókokat azoknak a felhasználóknak, az Azure AD kiépíti új fiókokat számukra az alkalmazásban.
2. Miután a kezdeti szinkronizálás befejezése után fent leírt módon, az Azure AD a következő módosítások 20 percenként ellenőrzi:
   * Ha nincsenek hozzárendelve az új felhasználók az alkalmazás (közvetlenül vagy csoporttagság), majd kiépítésüket a SaaS-alkalmazás új fiókkal.
   * Ha a felhasználó hozzáférési el lett távolítva, akkor az SaaS-alkalmazás fiókjuk van megjelölve (felhasználók soha nem teljesen törlődnek, amely megakadályozza, hogy adatvesztés esetén a helytelen konfiguráció).
   * Ha a felhasználó nemrég lett hozzárendelve az alkalmazást, és a Szolgáltatottszoftver-alkalmazás már volt egy fiókot, fiók van megjelölve, mivel engedélyezve van, és bizonyos felhasználói tulajdonságok frissülhet, ha azok leírásai a könyvtár képest.
   * Ha a felhasználó adatai (például a telefonszám, irodák) megváltozott a könyvtárban, majd ezt az információt is frissül az SaaS-alkalmazáshoz.


## <a name="frequently-asked-questions"></a>Gyakori kérdések
**Milyen gyakran nem írható az Azure AD directory változásait a SaaS-alkalmazásokhoz?**

Egy kezdeti teljes szinkronizálás befejeződése után az Azure AD kiosztása általában a szolgáltatás ellenőrzi a módosításokat 20 percenként. 

A Szolgáltatottszoftver-alkalmazáshoz (például érvénytelen rendszergazdai hitelesítő adataival gazdabuszadaptereken) számos hibát ad vissza, ha majd az Azure AD fokozatosan lelassítják a hibák megoldásáig naponta egyszer akár a gyakoriságát. Ez akkor fordul elő, amikor a Azure AD-létesítési feladat "karantén" állapotba kerül, és azt jelzi, hogy ez csak a [összesítő jelentés kiépítés](active-directory-saas-provisioning-reporting.md).

**Mennyi ideig tart a felhasználók létrehozásához?**

Miután egy kezdeti teljes szinkronizálás befejeződött, a növekményes változásokat általában 20-40 percen belül fordulhat elő. Ha annak a könyvtárnak a legtöbb kiépítéséhez, majd azt számától függ a felhasználók és csoportok van. Teljesítmény a felhasználó felügyeleti API-t a kiépítési szolgáltatások segítségével a forrásrendszerben adatokat olvasni és írni a célrendszerbe teljesítményétől függ. 

Teljesítmény egyben lassabb, ha sok hiba van (tárolja, amely a [naplók](active-directory-saas-provisioning-reporting.md)) és a létesítési szolgáltatás állapotba került egy "karantén" állapotba kerül.

**Mi az, hogy egy kezdeti teljes szinkronizálást, és Miért tart hosszabb, mint az ezt követő szinkronizálások?**

Az első futtatásakor a szolgáltatás kiépítését az Azure AD egy adott alkalmazáshoz, az "pillanatfelvételt" a felhasználók (és igény szerint csoportosítja) a forráskönyvtárban. A pillanatkép lehetővé teszi, hogy a létesítési szolgáltatás adatváltások számát csökkenteni a forrás és cél API-k, és lehetővé teszi, hogy a későbbi "különbözeti" szinkronizálások hatékonyabban viselkedését. 

A kezdeti teljes szinkronizálást, a felhasználók gyakran kis könyvtárak percben végezheti, de nagyobb könyvtárak több óráig is eltarthat. Vállalati könyvtárak felhasználók ezreit száz a kezdeti szinkronizálás végrehajtásához a több óráig is eltarthat. Azonban a kezdeti szinkronizálás után "különbözeti" ezt követő szinkronizálások fordulhat elő, sokkal gyorsabban.

> [!NOTE]
> Csoportokkal vagy csoporttagsággal a létesítést támogató alkalmazások esetében az engedélyezése Ez jelentős mértékben növeli a teljes szinkronizálás végrehajtásához szükséges idő. Ha nem szeretné, hogy csoporthoz tartozó nevek és a csoporttagságokat, az alkalmazás telepítéséhez, letilthatja ezt a a [attribútum-hozzárendelések](active-directory-saas-customizing-attribute-mappings.md) az üzembe helyezési konfigurációját.

**Hogyan követheti nyomon az aktuális üzembe helyezési feladat előrehaladásának?**

Tekintse meg a [létesítési jelentéskészítés – útmutató](active-directory-saas-provisioning-reporting.md).

**Milyen operációs rendszer, hogy ha a felhasználók nem tudnak megfelelően beolvasása kiépített?**

Összes sikertelen tárolja, amely az Azure AD a naplók. További információkért lásd: a [létesítési jelentéskészítés – útmutató](active-directory-saas-provisioning-reporting.md).

**Hogyan elküldheti a visszajelzését a mérnöki csapathoz?**

Kapcsolatfelvétel keresztül a [Azure Active Directory-visszajelzési fórumon](https://feedback.azure.com/forums/169401-azure-active-directory/).


## <a name="related-articles"></a>Kapcsolódó cikkek
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
* [A felhasználók átadása attribútum-leképezésekhez testreszabása](active-directory-saas-customizing-attribute-mappings.md)
* [Attribútum-leképezésekhez kifejezések írása](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Helyezése Hatókörszűrőkkel felhasználói történő üzembe helyezéséhez](active-directory-saas-scoping-filters.md)
* [SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez az Azure Active Directoryból az alkalmazásokba](active-directory-scim-provisioning.md)
* [Alkalmazás-kiépítési értesítések](active-directory-saas-account-provisioning-notifications.md)
* [SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)

