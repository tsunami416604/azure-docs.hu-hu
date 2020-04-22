---
title: 'Oktatóanyag: A Workday konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok automatikus kiépítésére és a Workday szolgáltatásba való kiépítésének kioldására.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdf0cbfb91332d60516432a7a67fb10404d89113
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683850"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Oktatóanyag: A Munkanap konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy megjelenítse azokkal a lépésekkel, amelyeket el kell végeznie a munkavégző profilok importálásához a Workday szolgáltatásból az Active Directoryba és az Azure Active Directoryba, az e-mail cím és a felhasználónév nem kötelező leírásával a Workday szolgáltatásba.

## <a name="overview"></a>Áttekintés

Az [Azure Active Directory felhasználói kiépítési szolgáltatás](../app-provisioning/user-provisioning.md) integrálja a [Workday emberi erőforrások API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) felhasználói fiókok kiépítése érdekében. Az Azure AD ezt a kapcsolatot használja a következő felhasználói kiépítési munkafolyamatok engedélyezéséhez:

* **Felhasználók kiépítése az Active Directoryba** – A kijelölt felhasználócsoportok kiépítése a Workday szolgáltatásból egy vagy több Active Directory-tartományba.

* **Csak felhőalapú felhasználók kiépítése az Azure Active Directoryba** – Olyan esetekben, amikor a helyszíni Active Directory nem használatos, a felhasználók közvetlenül a Workday-ről az Azure Active Directoryba építhetők ki az Azure AD felhasználói létesítési szolgáltatás használatával.

* **Írjon vissza e-mail címet és felhasználónevet a Workday szolgáltatásnak** – Az Azure AD felhasználói létesítési szolgáltatás a levelezési címeket és a felhasználónevet az Azure AD-ből visszaírhatja a Workday-be.

### <a name="what-human-resources-scenarios-does-it-cover"></a>Milyen humánerőforrás-forgatókönyveket fed le?

Az Azure AD felhasználói létesítési szolgáltatás által támogatott Workday felhasználói kiépítési munkafolyamatok lehetővé teszik a következő emberi erőforrások és identitáséletciklus-kezelési forgatókönyvek automatizálását:

* **Új alkalmazottak felvétele** – Ha új alkalmazottat ad hozzá a Workday szolgáltatáshoz, a rendszer automatikusan létrehoz egy felhasználói fiókot az Active Directoryban, az Azure Active Directoryban, és adott esetben az Azure 365-ben és [az Azure AD által támogatott egyéb SaaS-alkalmazásokban,](../app-provisioning/user-provisioning.md)az e-mail cím workday-be való visszaírásával.

* **Alkalmazotti attribútum- és profilfrissítések** – Amikor egy alkalmazotti rekordot frissíta workday-ben (például a nevüket, címüket vagy kezelőjüket), felhasználói fiókjuk automatikusan frissül az Active Directoryban, az Azure Active Directoryban, és adott esetben az Office 365-ben és [az Azure AD által támogatott egyéb SaaS-alkalmazásokban.](../app-provisioning/user-provisioning.md)

* **Alkalmazottak megszűnése** – Ha egy alkalmazott megszűnik a Workday szolgáltatásban, a felhasználói fiók automatikusan le lesz tiltva az Active Directoryban, az Azure Active Directoryban, és adott esetben az Office 365-ben és [az Azure AD által támogatott egyéb SaaS-alkalmazásokban.](../app-provisioning/user-provisioning.md)

* **Alkalmazotti újrahires** – Ha egy alkalmazottat újra felvesznek a Workday szolgáltatásba, a régi fiókja automatikusan újraaktiválható vagy újra kiépíthető (a ttól függően) az Active Directory, az Azure Active Directory, és adott esetben az Office 365 és [az Azure AD által támogatott egyéb SaaS-alkalmazások számára.](../app-provisioning/user-provisioning.md)

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Ki ez a felhasználói kiépítési megoldás a legalkalmasabb?

Ez a Workday felhasználói kiépítési megoldás ideális a következőkhöz:

* Olyan szervezetek, amelyek előre elkészített, felhőalapú megoldást szeretnének a Workday felhasználói kiépítéséhez

* Közvetlen felhasználói kiépítést igénylő szervezetek a Workday szolgáltatásból az Active Directoryba vagy az Azure Active Directoryba

* Szervezetek, amelyek megkövetelik a felhasználók kiépítését a Workday HCM modulból kapott adatok felhasználásával (lásd [Get_Workers)](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)

* Olyan szervezetek, amelyek csak a Workday HCM modulban észlelt változásadatok alapján szeretnék a felhasználókat egy vagy több Active Directory erdővel, tartománnyal és szervezeti egységekkel szinkronizálni [(lásd Get_Workers)](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)

* Szervezetek, amelyek az Office 365-ös levelezést használják

## <a name="solution-architecture"></a>Megoldás architektúrája

Ez a szakasz a közös hibrid környezetek végpontok között lévő felhasználói kiépítési megoldásarchitektúrát ismerteti. Két kapcsolódó folyamat létezik:

* **Mérvadó HR-adatfolyam – a Workday-től a helyszíni Active Directoryig:** Ebben a folyamatban a feldolgozóesemények (például az új bérleti, átvitelek, felmondások) először a felhőben Workday HR-bérlő, majd az esemény adatok áramlik a helyszíni Active Directory az Azure AD és a kiépítési ügynök. Az eseménytől függően az AD-ben létrehozási/frissítési/enable/disable műveleteket eredményezhet.
* **E-mail és felhasználónév-visszaírás – a helyszíni Active Directorytól a Workday-ig:** Miután a fiók létrehozása befejeződött az Active Directoryban, az Azure AD-vel az Azure AD Connecten keresztül szinkronizálódik, és az e-mail és a felhasználónév attribútum visszaírható a Workday-be.

![Áttekintés](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Végpontok között lévő felhasználói adatfolyam

1. A HR-csapat munkavégző tranzakciókat hajt végre (Asztalosok/Mozgatók/Kilépők vagy Új bérlők/Transzferek/Felmondások) a Workday HCM-ben
2. Az Azure AD-létesítési szolgáltatás fut ütemezett szinkronizálása identitások workday HR és azonosítja a módosításokat, amelyeket fel kell dolgozni a szinkronizálás a helyszíni Active Directory.
3. Az Azure AD-kiépítési szolgáltatás meghívja a helyszíni Azure AD Connect kiépítési ügynök egy kérelem hasznos, amely tartalmazza az AD-fiók létrehozása/frissítése/engedélyezése/letiltása műveleteket.
4. Az Azure AD Connect kiépítési ügynök egy szolgáltatásfiókot használ az AD-fiók adatainak hozzáadásához/frissítéséhez.
5. Az Azure AD Connect / AD Sync motor különbözeti szinkronizálást futtat az AD-ben a frissítések lekérése érdekében.
6. Az Active Directory frissítései szinkronizálva vannak az Azure Active Directoryval.
7. Ha a Workday Writeback összekötő konfigurálva van, a használt megfelelő attribútum alapján visszaküldi az e-mail attribútumot és a felhasználónevet a Workday-nek.

## <a name="planning-your-deployment"></a>A telepítés megtervezése

A Workday-integráció megkezdése előtt ellenőrizze az alábbi előfeltételeket, és olvassa el az alábbi útmutatást arról, hogyan felelmeg az aktuális Active Directory architektúrának és a felhasználói kiépítési követelményeknek az Azure Active Directory által biztosított megoldás(ok)nak. A tervezési munkalapokat tartalmazó átfogó [üzembe helyezési terv](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) is rendelkezésre áll, amely segítséget nyújt a Workday-integrációs partnerrel és a HR-érdekelt felekkel való együttműködésben.

Ez a szakasz a tervezés következő szempontjaival foglalkozik:

* [Előfeltételek](#prerequisites)
* [Az üzembe helyezendő összekötőalkalmazások kiépítési alkalmazásainak kiválasztása](#selecting-provisioning-connector-apps-to-deploy)
* [Az Azure AD Connect kiépítési ügynök telepítésének megtervezése](#planning-deployment-of-azure-ad-connect-provisioning-agent)
* [Integráció több Active Directory-tartománnyal](#integrating-with-multiple-active-directory-domains)
* [Munkanap tervezése az Active Directory felhasználói attribútumleképezéséhez és átalakításához](#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)

### <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

* Érvényes Azure AD Premium P1 vagy magasabb előfizetési licenc minden olyan felhasználó számára, amely a Workday-ből származik, és a helyszíni Active Directoryba vagy az Azure Active Directoryba lesz kiépítve.
* Az Azure AD globális rendszergazdai hozzáférése a kiépítési ügynök konfigurálásához
* A Workday megvalósítási bérlő tesztelési és integrációs célokra
* Rendszergazdai engedélyek a Workday alkalmazásban a rendszerintegrációs felhasználó létrehozásához és az alkalmazotti adatok tesztelési célokra történő teszteléséhez
* Az Active Directoryba történő felhasználói kiépítéshez a [helyszíni létesítő ügynök](https://go.microsoft.com/fwlink/?linkid=847801) üzemeltetéséhez Windows Server 2012 vagy nagyobb, .NET 4.7.1+ futtatókörnyezettel rendelkező kiszolgáló szükséges.
* [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) a felhasználók active directory és Azure AD közötti szinkronizálásához

### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Az üzembe helyezendő összekötőalkalmazások kiépítési alkalmazásainak kiválasztása

A Workday és az Active Directory közötti munkafolyamatok kiépítésének megkönnyítése érdekében az Azure AD több üzembe létesítési összekötő alkalmazást biztosít, amelyeket az Azure AD alkalmazásgalériából adhat hozzá:

![Az Azure AD Alkalmazás gyűjteménye](./media/workday-inbound-tutorial/wd_gallery.png)

* **Workday to Active Directory user provisioning** – Ez az alkalmazás megkönnyíti a felhasználói fiókok kiépítését a Workday-ből egyetlen Active Directory-tartományba. Ha több tartománnyal rendelkezik, az alkalmazás egy példányát hozzáadhatja az Azure AD alkalmazásgyűjteményéből minden olyan Active Directory-tartományhoz, amelybe ki kell építenie.

* **Workday to Azure AD user provisioning** – Bár az Azure AD Connect az az eszköz, amelyet az Active Directory-felhasználók Azure Active Directoryval való szinkronizálásához kell használni, ez az alkalmazás a csak felhőalapú felhasználók kiépítésének megkönnyítésére használható a Workday-től egyetlen Azure Active Directory-bérlőig.

* **Workday Writeback** – Ez az alkalmazás megkönnyíti a felhasználó e-mail címeinek visszaírását az Azure Active Directoryból a Workday szolgáltatásba.

> [!TIP]
> A szokásos "Workday" alkalmazás a Workday és az Azure Active Directory közötti egyszeri bejelentkezés beállításához használatos.

Az alábbi döntési folyamatábrával azonosíthatja, hogy mely Workday-kiépítési alkalmazások relevánsak a forgatókönyv szempontjából.
    ![Döntési folyamatábra](./media/workday-inbound-tutorial/wday_app_flowchart.png "Döntési folyamatábra")

A tartalomjegyzék segítségével lépjen az oktatóanyag megfelelő szakaszára.

### <a name="planning-deployment-of-azure-ad-connect-provisioning-agent"></a>Az Azure AD Connect kiépítési ügynök telepítésének megtervezése

> [!NOTE]
> Ez a szakasz csak akkor releváns, ha a Workday-t az Active Directory felhasználói kiépítési alkalmazásba kívánja telepíteni. Ezt kihagyhatja, ha a Workday writeback vagy workday üzembe helyezése az Azure AD felhasználói kiépítési alkalmazás.

A Workday to AD felhasználói kiépítési megoldás hoz üzembe egy vagy több kiépítési ügynökök a windows 2012 R2 vagy nagyobb, legalább 4 GB RAM és .NET 4.7.1+ futtatókörnyezet. A kiépítési ügynök telepítése előtt a következő szempontokat kell figyelembe venni:

* Győződjön meg arról, hogy a létesítő kiszolgálót futtató gazdakiszolgáló hálózati hozzáféréssel rendelkezik a cél AD tartományhoz
* A kiépítési ügynök konfigurációs varázsló regisztrálja az ügynököt az Azure AD-bérlővel, és a regisztrációs folyamat hozzáférést igényel a *.msappproxy.net-hez a 443-as TLS-porton keresztül. Győződjön meg arról, hogy a kimenő tűzfalszabályok vannak érvényben, amelyek lehetővé teszik ezt a kommunikációt. Az ügynök támogatja a [kimenő HTTPS-proxy konfigurációt.](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
* A kiépítési ügynök egy szolgáltatásfiókot használ a helyszíni AD-tartomány(ok) kommunikálására. Az ügynök telepítése előtt ajánlott tartományi rendszergazdai engedélyekkel és nem lejáró jelszóval rendelkező szolgáltatásfiókot létrehozni.  
* A kiépítési ügynök konfigurációja során kiválaszthatja azokat a tartományvezérlőket, amelyeknek kezelniük kell a létesítési kérelmeket. Ha több földrajzilag elosztott tartományvezérlővel rendelkezik, telepítse a kiépítési ügynököt ugyanazon a helyen, mint az előnyben részesített tartományvezérlő(k)et a végpontok közötti megoldás megbízhatóságának és teljesítményének javítása érdekében.
* A magas rendelkezésre állás érdekében több kiépítési ügynök üzembe helyezhető, és regisztrálja azt a helyszíni AD-tartományok azonos készletének kezeléséhez.

> [!IMPORTANT]
> Éles környezetekben a Microsoft azt javasolja, hogy legalább 3 kiépítési ügynökök konfigurálva az Azure AD-bérlő magas rendelkezésre állás érdekében.

### <a name="integrating-with-multiple-active-directory-domains"></a>Integráció több Active Directory-tartománnyal

> [!NOTE]
> Ez a szakasz csak akkor releváns, ha a Workday-t az Active Directory felhasználói kiépítési alkalmazásba kívánja telepíteni. Ezt kihagyhatja, ha a Workday writeback vagy workday üzembe helyezése az Azure AD felhasználói kiépítési alkalmazás.

Az Active Directory topológiájától függően el kell döntenie a felhasználói kiépítési összekötő alkalmazások számát és a konfigurálandó kiépítési ügynökök számát. Az alábbiakban felsorolunk néhány gyakori telepítési mintákat, amelyek a központi telepítés megtervezésekor hivatkozhat.

#### <a name="deployment-scenario-1--single-workday-tenant---single-ad-domain"></a>Telepítési #1: Egy munkanapos bérlő -> egy AD-tartomány

Ebben a forgatókönyvben egy Workday-bérlővel rendelkezik, és egyetlen cél AD-tartományba szeretné kiépíteni a felhasználókat. Itt van az ajánlott éles konfiguráció ehhez a központi telepítéshez.

|   |   |
| - | - |
| Nem. a helyszíni telepítéshez | 3 (magas rendelkezésre állás és feladatátvétel esetén) |
| Nem. A Workday az AD-felhasználók számára kiépítési alkalmazások konfigurálásához az Azure Portalon | 1 |

  ![1. példa](./media/workday-inbound-tutorial/dep_scenario1.png)

#### <a name="deployment-scenario-2--single-workday-tenant---multiple-child-ad-domains"></a>Telepítési forgatókönyv #2: Egy munkanapos bérlő -> több gyermek AD-tartomány

Ebben a forgatókönyvben a felhasználók kiépítése a Workday-től az erdő több cél AD gyermektartományába. Itt van az ajánlott éles konfiguráció ehhez a központi telepítéshez.

|   |   |
| - | - |
| Nem. a helyszíni telepítéshez | 3 (magas rendelkezésre állás és feladatátvétel esetén) |
| Nem. A Workday az AD-felhasználók számára kiépítési alkalmazások konfigurálásához az Azure Portalon | gyermektartományonként egy alkalmazás |

  ![2. példa](./media/workday-inbound-tutorial/dep_scenario2.png)

#### <a name="deployment-scenario-3--single-workday-tenant---disjoint-ad-forests"></a>Telepítési #3: Egy munkanapos bérlő –> különálló AD-erdők

Ebben a forgatókönyvben a felhasználók kiépítése a Workday tartományok között különálló AD erdőkben. Itt van az ajánlott éles konfiguráció ehhez a központi telepítéshez.

|   |   |
| - | - |
| Nem. a helyszíni telepítéshez | 3 különálló AD erdőnként |
| Nem. A Workday az AD-felhasználók számára kiépítési alkalmazások konfigurálásához az Azure Portalon | gyermektartományonként egy alkalmazás |

  ![3. példa](./media/workday-inbound-tutorial/dep_scenario3.png)

### <a name="planning-workday-to-active-directory-user-attribute-mapping-and-transformations"></a>Munkanap tervezése az Active Directory felhasználói attribútumleképezéséhez és átalakításához

> [!NOTE]
> Ez a szakasz csak akkor releváns, ha a Workday-t az Active Directory felhasználói kiépítési alkalmazásba kívánja telepíteni. Ezt kihagyhatja, ha a Workday writeback vagy workday üzembe helyezése az Azure AD felhasználói kiépítési alkalmazás.

Mielőtt konfigurálna egy felhasználót egy Active Directory-tartományba, vegye figyelembe a következő kérdéseket. Az ezekre a kérdésekre adott válaszok határozzák meg, hogyan kell beállítani a hatókörszűrőket és az attribútumleképezéseket.

* **Milyen felhasználók at Workday kell kiépíteni az Active Directory erdő?**

  * *Példa: Azok a felhasználók, akiknél a Workday "Vállalat" attribútum a "Contoso" értéket, a "Worker_Type" attribútum pedig a "Normál" értéket tartalmazza.*

* **Hogyan történik a felhasználók irányítva a különböző szervezeti egységekbe?**

  * *Példa: A felhasználók a Workday "Önkormányzat" és a "Country_Region_Reference" attribútumban meghatározott, egy irodai helynek megfelelő oUs-okhoz kerülnek.*

* **Hogyan kell a következő attribútumokat feltölteni az Active Directoryban?**

  * Köznapi név (cn)
    * *Példa: Használja a Workday User_ID értéket az emberi erőforrások által meghatározott érték szerint*

  * Alkalmazott azonosítója (alkalmazottazonosító)
    * *Példa: Használja a Munkanap Worker_ID értéket*

  * SAM-fiók neve (sAMAccountName)
    * *Példa: Használja a Workday User_ID érték, szűrt egy Azure AD létesítési kifejezés az illegális karakterek eltávolítása*

  * Egyszerű felhasználónév (userPrincipalName)
    * *Példa: Használja a Workday User_ID érték, egy Azure AD kiépítési kifejezéssel a tartománynév hozzáfűzéséhez*

* **Hogyan kell a felhasználókat egyeztetni a Workday és az Active Directory között?**

  * *Példa: A workday "Worker_ID" értékkel rendelkező felhasználók egyeznek az Active Directory felhasználóival, ahol az "employeeID" értéke megegyezik. Ha a Worker_ID érték nem található az Active Directoryban, akkor hozzon létre egy új felhasználót.*
  
* **Az Active Directory-erdő már tartalmazza a megfelelő logika működéséhez szükséges felhasználói azonosítókat?**

  * *Példa: Ha ez a beállítás egy új Workday-telepítés, ajánlott az Active Directory előzetesen kitöltve a megfelelő Workday Worker_ID értékekkel (vagy egyedi azonosítóval választott értékkel), hogy az egyeztetési logika a lehető legegyszerűbb legyen.*

Ezeket a speciális kiépítési összekötő alkalmazásokat az oktatóanyag további szakaszainak beállítása és konfigurálása képezi. A konfigurálandó alkalmazások attól függnek, hogy mely rendszerekbe kell kiépítenie, és hogy hány Active Directory-tartományba és Az Azure AD-bérlők a környezetben vannak.

## <a name="configure-integration-system-user-in-workday"></a>Integrációs rendszer felhasználójának konfigurálása a Workday-ben

A Workday létesítési összekötők közös követelménye, hogy a Workday integrációs rendszer felhasználójának hitelesítő adataira van szükség a Workday emberi erőforrások API-hoz való csatlakozáshoz. Ez a szakasz azt ismerteti, hogyan hozhat létre integrációs rendszerfelhasználót a Workday szolgáltatásban, és a következő szakaszokat ismerteti:

* [Integrációs rendszer felhasználójának létrehozása](#creating-an-integration-system-user)
* [Integrációs biztonsági csoport létrehozása](#creating-an-integration-security-group)
* [Tartományi biztonsági házirend-engedélyek konfigurálása](#configuring-domain-security-policy-permissions)
* [Üzleti folyamat biztonsági házirend-engedélyeinek konfigurálása](#configuring-business-process-security-policy-permissions)
* [A biztonsági házirend változásainak aktiválása](#activating-security-policy-changes)

> [!NOTE]
> Lehetőség van megkerülni ezt az eljárást, és ehelyett a Workday globális rendszergazdai fiókot használhatja rendszerintegrációs fiókként. Ez jól működhet a bemutatók, de nem ajánlott éles környezetben.

### <a name="creating-an-integration-system-user"></a>Integrációs rendszer felhasználójának létrehozása

**Integrációs rendszer felhasználójának létrehozása:**

1. Jelentkezzen be a Workday-bérlőhöz rendszergazdai fiókkal. A **Munkanap alkalmazásban**írja be a Felhasználó létrehozása kifejezést a keresőmezőbe, majd kattintson az **Integrációs rendszer felhasználójának létrehozása gombra.**

   ![Felhasználó létrehozása](./media/workday-inbound-tutorial/wd_isu_01.png "Felhasználó létrehozása")
2. Hajtsa végre az **Integrációs rendszer felhasználóinak létrehozása** feladatot egy új integrációs rendszer felhasználójának felhasználónevének és jelszavának megadásával.  
  
   * Hagyja bejelölve az **Új jelszó megkövetelése a Következő bejelentkezéskor** beállítást, mert ez a felhasználó programozott módon fog bejelentkezni.
   * Hagyja a **munkamenet időtúllépési percét** az alapértelmezett 0 értékkel, ami megakadályozza, hogy a felhasználó munkamenetei idő előtt idő előtt idő előtt idő előtt lehalkuljon.
   * Válassza a **Felhasználói felületi munkamenetek engedélyezése** lehetőséget, mivel olyan biztonsági réteget biztosít, amely megakadályozza, hogy az integrációs rendszer jelszavával rendelkező felhasználó bejelentkezzen a Workday szolgáltatásba.

   ![Integrációs rendszer felhasználójának létrehozása](./media/workday-inbound-tutorial/wd_isu_02.png "Integrációs rendszer felhasználójának létrehozása")

### <a name="creating-an-integration-security-group"></a>Integrációs biztonsági csoport létrehozása

Ebben a lépésben létrehoz egy korlátlan vagy korlátozott integrációs rendszer biztonsági csoportot a Workday alkalmazásban, és hozzárendeli az előző lépésben létrehozott integrációs rendszer felhasználóját ehhez a csoporthoz.

**Biztonsági csoport létrehozása:**

1. Írja be a Biztonsági csoport létrehozása kifejezést a keresőmezőbe, majd kattintson a **Biztonsági csoport létrehozása gombra.**

    ![Biztonsági csoport létrehozása](./media/workday-inbound-tutorial/wd_isu_03.png "Biztonsági csoport létrehozása")
2. A **Biztonsági csoport létrehozása** feladat végrehajtása. 

   * A Workday-ben kétféle biztonsági csoport létezik:
     * **Korlátlan:** A biztonsági csoport minden tagja hozzáférhet a biztonsági csoport által védett összes adatpéldányhoz.
     * **Kényszerezve:** A biztonsági csoport minden tagja környezetfüggő hozzáféréssel rendelkezik az adatpéldányok (sorok) egy részhalmazához, amelyhez a biztonsági csoport hozzáférhet.
   * Kérjük, érdeklődjön a Workday integrációs partnerével, és válassza ki az integrációhoz megfelelő biztonsági csoporttípust.
   * Miután ismeri a csoport típusát, válassza **az Integrációs rendszer biztonsági csoportját (korlátozás nélkül)** vagy **az integrációs rendszer biztonsági csoportját (kényszerezett)** a **Bérlőbiztonsági csoport típusa** legördülő menüből.

     ![Biztonsági csoport létrehozása](./media/workday-inbound-tutorial/wd_isu_04.png "Biztonsági csoport létrehozása")

3. A biztonsági csoport létrehozása sikeres sé válik, és megjelenik egy lap, amelyen tagokat rendelhet a Biztonsági csoporthoz. Adja hozzá az előző lépésben létrehozott új integrációs rendszerfelhasználót ehhez a biztonsági csoporthoz. Ha *korlátozott* biztonsági csoportot használ, ki kell választania a megfelelő szervezeti hatókört is.

    ![Biztonsági csoport szerkesztése](./media/workday-inbound-tutorial/wd_isu_05.png "Biztonsági csoport szerkesztése")

### <a name="configuring-domain-security-policy-permissions"></a>Tartományi biztonsági házirend-engedélyek konfigurálása

Ebben a lépésben a "tartomány biztonsági" házirend-engedélyeket a feldolgozói adatok a biztonsági csoport.

**Tartománybiztonsági házirend-engedélyek konfigurálása:**

1. Írja be a **Tartománybiztonság konfigurációját** a keresőmezőbe, majd kattintson a **Domain Security Configuration Report (Tartománybiztonsági konfigurációs jelentés)** hivatkozásra.  

    ![Tartományi biztonsági házirendek](./media/workday-inbound-tutorial/wd_isu_06.png "Tartományi biztonsági házirendek")  
2. A **Domain (Tartomány)** mezőben keresse meg a következő tartományokat, és egyenként vegye fel őket a szűrőbe.  
   * *Külső számlakiépítés*
   * *Dolgozói adatok: Dolgozók*
   * *Dolgozói adatok: Nyilvános dolgozói jelentések*
   * *Személy adatai: Munkahelyi kapcsolattartási adatok*
   * *Dolgozói adatok: Minden beosztás*
   * *Dolgozói adatok: Aktuális személyzeti információk*
   * *Dolgozói adatok: Üzleti cím a dolgozói profilon*
   * *Munkanap-fiókok*
   
     ![Tartományi biztonsági házirendek](./media/workday-inbound-tutorial/wd_isu_07.png "Tartományi biztonsági házirendek")  

     ![Tartományi biztonsági házirendek](./media/workday-inbound-tutorial/wd_isu_08.png "Tartományi biztonsági házirendek") 

     Kattintson az **OK** gombra.

3. A megjelenő jelentésben jelölje ki a **külső fiók kiépítése** mellett megjelenő három pontot (...), és kattintson a **Domain -> Biztonsági házirend engedélyeinek szerkesztése** menüpontra.

    ![Tartományi biztonsági házirendek](./media/workday-inbound-tutorial/wd_isu_09.png "Tartományi biztonsági házirendek")  

4. A **Tartománybiztonsági házirend engedélyeinek szerkesztése** lapon görgessen le az **Integrációs engedélyek**szakaszig. Kattintson a "+" jelre, ha hozzá szeretné adni az integrációs rendszercsoportot a **beési** és **put** integrációs engedélyekkel rendelkező biztonsági csoportok listájához.

    ![Engedély szerkesztése](./media/workday-inbound-tutorial/wd_isu_10.png "Engedély szerkesztése")  

5. Kattintson a "+" jelre, ha hozzá szeretné adni az integrációs rendszercsoportot a **beési** és **put** integrációs engedélyekkel rendelkező biztonsági csoportok listájához.

    ![Engedély szerkesztése](./media/workday-inbound-tutorial/wd_isu_11.png "Engedély szerkesztése")  

6. Ismételje meg a fenti 3-5.

   | Művelet | Tartomány biztonsági házirendje |
   | ---------- | ---------- |
   | Be- és put | Dolgozói adatok: Nyilvános dolgozói jelentések |
   | Be- és put | Személy adatai: Munkahelyi kapcsolattartási adatok |
   | Lekérés | Dolgozói adatok: Dolgozók |
   | Lekérés | Dolgozói adatok: Minden beosztás |
   | Lekérés | Dolgozói adatok: Aktuális személyzeti információk |
   | Lekérés | Dolgozói adatok: Üzleti cím a dolgozói profilon |
   | Be- és put | Munkanap-fiókok |

### <a name="configuring-business-process-security-policy-permissions"></a>Üzleti folyamat biztonsági házirend-engedélyeinek konfigurálása

Ebben a lépésben a biztonsági csoportnak "üzleti folyamat biztonsága" házirend-engedélyeket ad a dolgozói adatokhoz. Ez a lépés a Workday Writeback alkalmazásösszekötő beállításához szükséges.

**Az üzleti folyamatok biztonsági házirendjének engedélyeinek konfigurálása:**

1. Írja be az **Üzleti folyamat házirendjét** a keresőmezőbe, majd kattintson az **Üzleti folyamat biztonsági házirendfeladatának szerkesztése** hivatkozásra.  

    ![Üzleti folyamatok biztonsági házirendjei](./media/workday-inbound-tutorial/wd_isu_12.png "Üzleti folyamatok biztonsági házirendjei")  

2. Az **Üzleti folyamat típusa** szövegmezőben keresse meg a *Kapcsolattartó* elemet, és válassza a **Kapcsolattartó módosítása** üzleti folyamat lehetőséget, majd kattintson az **OK**gombra.

    ![Üzleti folyamatok biztonsági házirendjei](./media/workday-inbound-tutorial/wd_isu_13.png "Üzleti folyamatok biztonsági házirendjei")  

3. Az **Üzleti folyamatok biztonsági házirendjének szerkesztése** lapon görgessen a **Kapcsolatfelvételi adatok (Webszolgáltatás) karbantartása (Webszolgáltatás)** szakaszhoz.

    ![Üzleti folyamatok biztonsági házirendjei](./media/workday-inbound-tutorial/wd_isu_14.png "Üzleti folyamatok biztonsági házirendjei")  

4. Jelölje ki és adja hozzá az új integrációs rendszer biztonsági csoportját azon biztonsági csoportok listájához, amelyek kezdeményezhetik a webszolgáltatások iránti kérelmet. Kattintson a **Kész gombra.** 

    ![Üzleti folyamatok biztonsági házirendjei](./media/workday-inbound-tutorial/wd_isu_15.png "Üzleti folyamatok biztonsági házirendjei")  

### <a name="activating-security-policy-changes"></a>A biztonsági házirend változásainak aktiválása

**A biztonsági házirend változásainak aktiválása:**

1. Írja be az aktiválást a keresőmezőbe, majd kattintson a **Függőben lévő biztonsági házirend módosításai aktiválása**hivatkozásra.

    ![Aktiválja](./media/workday-inbound-tutorial/wd_isu_16.png "Aktiválás")

1. A Függőben lévő biztonsági házirend módosításai aktiválása feladat megkezdése egy megjegyzés naplózási célú megadásával, majd kattintson az **OK**gombra.
1. A következő képernyőn végezze el a feladatot a **Megerősítés**jelölőnégyzet bejelölésével, majd kattintson az **OK**gombra.

    ![Függőben lévő biztonság aktiválása](./media/workday-inbound-tutorial/wd_isu_18.png "Függőben lévő biztonság aktiválása")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Felhasználói kiépítés konfigurálása a Workday szolgáltatásból az Active Directoryba

Ez a szakasz a felhasználói fiókok Workday-ből az integráció hatókörén belül az egyes Active Directory-tartományokba történő kiépítésének lépéseit ismerteti.

* [Adja hozzá a kiépítési összekötő alkalmazást, és töltse le a kiépítési ügynök](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Helyszíni kiépítési ügynök(ek) telepítése és konfigurálása](#part-2-install-and-configure-on-premises-provisioning-agents)
* [A Workday és az Active Directory kapcsolatának konfigurálása](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)
* [Attribútumleképezések konfigurálása](#part-4-configure-attribute-mappings)
* [Felhasználói kiépítés engedélyezése és elindítása](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>1. rész: Adja hozzá a létesítési összekötő alkalmazást, és töltse le a kiépítési ügynök

**A Workday konfigurálása az Active Directory kiépítéséhez:**

1. Nyissa meg a következőt: <https://portal.azure.com>.

2. Az Azure Portalon keresse meg és válassza az **Azure Active Directoryt.**

3. Válassza **a Vállalati alkalmazások**lehetőséget, majd az Összes **alkalmazás**lehetőséget.

4. Válassza **az Alkalmazás hozzáadása**lehetőséget, majd az **Összes** kategóriát.

5. Keresse meg **a Workday Kiépítés az Active Directoryba,** és adja hozzá az alkalmazást a katalógusból.

6. Az alkalmazás hozzáadása és az alkalmazás részletei képernyő megjelenítése után válassza **a Kiépítés**lehetőséget.

7. Módosítsa a **létesítési** **módot** **Automatikusra.**

8. Kattintson a megjelenő információs banner re a kiépítési ügynök letöltéséhez. 

   ![Agent letöltése](./media/workday-inbound-tutorial/pa-download-agent.png "Ügynök képernyő letöltése")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>2. rész: Helyszíni kiépítési ügynök(ek) telepítése és konfigurálása

A helyszíni Active Directoryba való kiépítéshez a kiépítő ügynököt telepíteni kell egy olyan kiszolgálóra, amely .NET 4.7.1+ keretrendszerrel és hálózati hozzáféréssel rendelkezik a kívánt Active Directory-tartomány(ok)hoz.

> [!TIP]
> A kiszolgálón a . [here](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)
> Ha a kiszolgálón nincs telepítve .NET 4.7.1 vagy újabb, akkor [innen](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows)töltheti le.  

Vigye át a letöltött ügynök telepítőjét a kiszolgálóállomásra, és kövesse az alábbi lépéseket az ügynök konfigurációjának befejezéséhez.

1. Jelentkezzen be a Windows Server kiszolgálóra, ahová telepíteni szeretné az új ügynököt.

1. Indítsa el a kiépítési ügynök telepítő, elfogadja a feltételeket, és kattintson a **Telepítés** gombra.

   ![Képernyő telepítése](./media/workday-inbound-tutorial/pa_install_screen_1.png "Képernyő telepítése")
   
1. A telepítés befejezése után a varázsló elindul, és megjelenik az **Azure AD csatlakoztatása** képernyő. Kattintson a **Hitelesítés gombra** az Azure AD-példányhoz való csatlakozáshoz.

   ![Az Azure AD csatlakoztatása](./media/workday-inbound-tutorial/pa_install_screen_2.png "Az Azure AD csatlakoztatása")
   
1. Hitelesítse magát az Azure AD-példány globális rendszergazdai hitelesítő adatok használatával.

   ![Admin Auth](./media/workday-inbound-tutorial/pa_install_screen_3.png "Admin Auth")

   > [!NOTE]
   > Az Azure AD-rendszergazdai hitelesítő adatok csak az Azure AD-bérlőhöz való csatlakozáshoz használatos. Az ügynök nem tárolja a hitelesítő adatokat helyileg a kiszolgálón.

1. Az Azure AD-vel való sikeres hitelesítés után megjelenik az **Active Directory csatlakoztatása** képernyő. Ebben a lépésben adja meg az AD tartománynevét, és kattintson a **Címtár hozzáadása** gombra.

   ![Könyvtár hozzáadása](./media/workday-inbound-tutorial/pa_install_screen_4.png "Könyvtár hozzáadása")
  
1. A rendszer most kéri, hogy adja meg az AD-tartományhoz való csatlakozáshoz szükséges hitelesítő adatokat. Ugyanezen a képernyőn a **Tartományvezérlő kiválasztása prioritással** adhatja meg azokat a tartományvezérlőket, amelyeket az ügynöknek a létesítési kérelmek küldéséhez kell használnia.

   ![Tartományi hitelesítő adatok](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. A tartomány konfigurálása után a telepítő megjeleníti a konfigurált tartományok listáját. Ezen a képernyőn megismételheti a #5 és #6 lépést további tartományok hozzáadásához, vagy kattintson a **Tovább** gombra az ügyintéző-regisztrációhoz.

   ![Konfigurált tartományok](./media/workday-inbound-tutorial/pa_install_screen_6.png "Konfigurált tartományok")

   > [!NOTE]
   > Ha több AD-tartománnyal rendelkezik (pl. na.contoso.com, emea.contoso.com), akkor kérjük, minden domaint külön-külön adjon hozzá a listához.
   > Csak a szülőtartomány hozzáadása (pl. contoso.com) nem elegendő. Minden gyermektartományt regisztrálnia kell az ügynöknél.
   
1. Tekintse át a konfiguráció részleteit, és kattintson a **Megerősítés** gombra az ügynök regisztrálásához.
  
   ![Képernyő megerősítése](./media/workday-inbound-tutorial/pa_install_screen_7.png "Képernyő megerősítése")
   
1. A konfigurációs varázsló megjeleníti az ügynök regisztrációjának előrehaladását.
  
   ![Ügynök regisztrációja](./media/workday-inbound-tutorial/pa_install_screen_8.png "Ügynök regisztrációja")
   
1. Miután az ügynök regisztrációja sikeres volt, a **Kilépés** gombra kattintva kiléphet a varázslóból.
  
   ![Kilépés képernyő](./media/workday-inbound-tutorial/pa_install_screen_9.png "Kilépés képernyő")
   
1. Ellenőrizze az ügynök telepítését, és győződjön meg arról, hogy fut a "Szolgáltatások" beépülő modul megnyitásával, és keresse meg a "Microsoft Azure AD Connect kiépítési ügynök" nevű szolgáltatást.
  
   ![Szolgáltatások](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory"></a>3. rész: A kiépítési alkalmazásban konfigurálja a kapcsolatot a Workday és az Active Directory számára
Ebben a lépésben kapcsolatot létesítünk a Workday és az Active Directory az Azure Portalon. 

1. Az Azure Portalon lépjen vissza a Workday az [1.](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Töltse ki a **Rendszergazdai hitelesítő adatok szakaszt** az alábbiak szerint:

   * **Workday Username** – Adja meg a Munkanap-integrációs rendszerfiók felhasználónevét a bérlői tartománynév hozzáfűzésével. Meg kell kinéznie valami ilyesmi: **felhasználónév\@tenant_name**

   * **Munkanap jelszava –** Adja meg a Workday integrációs rendszerfiók jelszavát

   * **Workday Web Services API URL-címe –** Adja meg a Workday webszolgáltatások végpontjának URL-címét a bérlő számára. Ez az érték https://wd3-impl-services1.workday.com/ccx/service/contoso4így kell kinéznie: , ahol *contoso4* helyett a megfelelő bérlő nevét és *wd3-impl* helyébe a megfelelő környezeti karakterláncot.

     > [!NOTE]
     > Alapértelmezés szerint az alkalmazás a Workday Web Services 21.1-es verzióját használja, ha az URL-címben nincs megadva verzióinformáció. Egy adott Workday Web Services API-verzió használatához használja az URL-formátumot:https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# <br>
     > Például: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v31.0

   * **Active Directory erdő -** Az Active Directory-tartomány "neve", az ügynöknél regisztráltállapotban. A legördülő menü segítségével válassza ki a kiépítés céltartományát. Ez az érték általában egy karakterlánc, mint: *contoso.com*

   * **Active Directory tároló -** Adja meg azt a tárolódnát, amelyben az ügynöknek alapértelmezés szerint felhasználói fiókokat kell létrehoznia.
        Példa: *OU=Standard Users,OU=Users,DC=contoso,DC=test*
        
     > [!NOTE]
     > Ez a beállítás csak akkor lép életbe a felhasználói fiókok létrehozásakor, ha a *parentDistinguishedName* attribútum nincs konfigurálva az attribútumleképezésekben. Ez a beállítás nem használható felhasználói keresési vagy frissítési műveletekhez. A teljes tartományalfa a keresési művelet hatálya alá tartozik.

   * **Értesítési e-mail –** Adja meg e-mail címét, és jelölje be az "E-mail küldése hiba esetén" jelölőnégyzetet.

     > [!NOTE]
     > Az Azure AD-kiépítési szolgáltatás e-mail értesítést küld, ha a létesítési feladat [karanténállapotba](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) kerül.

   * Kattintson a **Kapcsolat tesztelése** gombra. Ha a kapcsolatteszt sikeres, kattintson **a** mentés gombra a tetején. Ha nem sikerül, ellenőrizze, hogy a Workday hitelesítő adatok és az ügynök beállításán konfigurált AD hitelesítő adatok érvényesek-e.

     ![Azure Portal](./media/workday-inbound-tutorial/wd_1.png)

   * A hitelesítő adatok sikeres mentése után a **Hozzárendelések** szakasz megjeleníti a **munkanapdolgozók szinkronizálásának alapértelmezett hozzárendelését a helyszíni Active Directoryhoz**

### <a name="part-4-configure-attribute-mappings"></a>4. rész: Attribútumleképezések konfigurálása

Ebben a szakaszban konfigurálhatja, hogy a felhasználói adatok hogyan áramlanak a Workday szolgáltatásból az Active Directoryba.

1. A **Hozzárendelések**csoport Kiépítés lapján kattintson a **Munkanapi dolgozók szinkronizálása a helyszíni Active Directoryval**elemre.

1. A **Forrásobjektum hatóköre** mezőben az attribútumalapú szűrők készletének meghatározásával kiválaszthatja, hogy a Workday mely felhasználói csoportjai legyenek hatókörben az AD-hez való kiépítéshez. Az alapértelmezett hatókör a "Workday összes felhasználója". Példa szűrők:

   * Példa: Hatókör 1000000 és 2000000 közötti dolgozói azonosítóval rendelkező felhasználók számára (2000000 kivételével)

      * Attribútum: WorkerID

      * Operátor: REGEX Match

      * Érték: (1[0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9])

   * Példa: Csak alkalmazottak és nem függő dolgozók

      * Attribútum: Alkalmazottazonosító

      * Operátor: NEM NULL

   > [!TIP]
   > Amikor első alkalommal konfigurálja a kiépítési alkalmazást, tesztelnie és ellenőriznie kell az attribútumleképezéseket és -kifejezéseket, hogy megbizonyosodjon arról, hogy az a kívánt eredményt adja. A Microsoft azt javasolja, hogy a **Forrásobjektum-hatókör** hatóköre hatóköre segítségével tesztelje a leképezéseket néhány, a Workday tesztfelhasználójával. Miután meggyőződött arról, hogy a leképezések működnek, eltávolíthatja a szűrőt, vagy fokozatosan kibonthatja, hogy több felhasználóval is felvegye.

   > [!CAUTION] 
   > A kiépítési motor alapértelmezett viselkedése a hatókörön kívül eső felhasználók letiltása/törlése. Ez nem feltétlenül kívánatos a Workday to AD integrációban. Az alapértelmezett viselkedés felülbírálásához olvassa el a [hatókörön kívül eső felhasználói fiókok törlésének kihagyása című cikket.](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. A **Célobjektum-műveletek mezőben** globálisan szűrheti, hogy milyen műveleteket hajt végre az Active Directoryban. **A létrehozás** és **a frissítés** a leggyakoribb.

1. Az **Attribútumleképezések** szakaszban meghatározhatja, hogy az egyes Workday attribútumok hogyan feleljenek meg az Active Directory-attribútumokhoz.

1. A frissítéshez kattintson egy meglévő attribútumleképezésre, vagy kattintson az **Új leképezés hozzáadása** gombra a képernyő alján az új leképezések hozzáadásához. Egy egyéni attribútumleképezés támogatja a következő tulajdonságokat:

      * **Leképezés típusa**

         * **Közvetlen** – A Workday attribútum értékét írja az AD attribútumba, módosítások nélkül

         * **Állandó** – írjon statikus, állandó karakterláncértéket az AD attribútumba

         * **Kifejezés** – Lehetővé teszi, hogy egyéni értéket írjon az AD attribútumba egy vagy több Workday attribútum alapján. [További információt a kifejezésekről szóló cikkben talál.](../app-provisioning/functions-for-customizing-application-data.md)

      * **Forrásattribútum** – A Workday felhasználói attribútuma. Ha a keresett attribútum nincs jelen, olvassa el [a Workday felhasználói attribútumok listájának testreszabása című témakört.](#customizing-the-list-of-workday-user-attributes)

      * **Alapértelmezett érték** – nem kötelező. Ha a forrásattribútum üres értékkel rendelkezik, a leképezés ezt az értéket írja be helyette.
            A leggyakoribb konfiguráció ezt üresen hagyja.

      * **Célattribútum** – A felhasználói attribútum az Active Directoryban.

      * **Az objektumok egyeztetése ezzel az attribútummal** – Azt jelzi, hogy ezt a leképezést kell-e használni a felhasználók egyedi azonosítására a Workday és az Active Directory között. Ez az érték általában a Workday munkavégzőazonosító mezőjében van beállítva, amely általában az Active Directory egyik alkalmazotti azonosító attribútumához van leképezve.

      * **Egyező sorrend** – Több egyező attribútum is beállítható. Ha több van, akkor a program az ebben a mezőben meghatározott sorrendben értékeli ki őket. Amint egyezést talál, a rendszer nem értékeli ki a további egyező attribútumokat.

      * **A leképezés alkalmazása**

         * **Mindig** – A leképezés alkalmazása a felhasználó létrehozási és frissítési műveletekre is

         * **Csak a létrehozás során** – A leképezés alkalmazása csak a felhasználó létrehozási műveleteire

1. A leképezések mentéséhez kattintson a **Mentés** gombra az Attribútumleképezés szakasz tetején.

   ![Azure Portal](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Az alábbiakban néhány példa attribútumleképezést mutat be a Workday és az Active Directory között, néhány gyakori kifejezéssel

* A *parentDistinguishedName* attribútumhoz leképező kifejezés segítségével egy vagy több Workday forrásattribútum alapján a felhasználót különböző számítógép-gyártókhoz lehet kiépíteni. Ez a példa itt helyezi a felhasználók különböző ousok alapján, hogy milyen városban vannak.

* Az Active Directory *userPrincipalName* attribútuma a [SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) de-duplikációs függvénnyel jön létre, amely ellenőrzi a létrehozott érték létezését a cél AD tartományban, és csak akkor állítja be, ha az egyedi.  

* [Van dokumentáció írásban kifejezések itt](../app-provisioning/functions-for-customizing-application-data.md). Ez a szakasz példákat tartalmaz a speciális karakterek eltávolítására.

| WORKDAY ATTRIBÚTUM | ACTIVE DIRECTORY ATTRIBÚTUM |  Megfelelő azonosító? | LÉTREHOZÁS / FRISSÍTÉS |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Igen** | Csak a létrehozásra írva |
| **PreferredNameData (ElsődlegesnameData)**    |  Cn    |   |   Csak a létrehozásra írva |
| **SelectUniqueValue(\@Join(" ", \[Join(".", Keresztnév\] \[, Utónév\]\@), "contoso.com"), Join("\[", Join(".", Mid( Keresztnév\], 1, 1), Vezetéknév),\] \[\] \[\]"contoso.com"), "csatlakozás("\@", Csatlakozás(".",\[Utónév , 1, 2), Vezetéknév ), "contoso.com"))**   | userPrincipalName     |     | Csak a létrehozásra írva 
| **Replace(Mid(Replace(\[\]UserID ,\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\,\\\\\\ "( ; \\\|\\\\=\\\\,\\\\+\\\\\*\\\\? \\\\\\)", "", 1, 20), " ([[ .) \\ &lt; \\ \\ &gt; \] \* \$\\ *$)", , "", , )**      |    sAMAccountName            |     |         Csak a létrehozásra írva |
| **Kapcsoló(\[\]Aktív , , "0", "Igaz", "1", "Hamis")** |  fiókLetiltva      |     | Létrehozás + frissítés |
| **Utónév**   | givenName       |     |    Létrehozás + frissítés |
| **Vezetéknév**   |   sn   |     |  Létrehozás + frissítés |
| **PreferredNameData (ElsődlegesnameData)**  |  displayName |     |   Létrehozás + frissítés |
| **Vállalat**         | cég   |     |  Létrehozás + frissítés |
| **Felügyeleti szervezet**  | Részleg  |     |  Létrehozás + frissítés |
| **ManagerReferencia**   | manager  |     |  Létrehozás + frissítés |
| **Üzleti cím**   |  cím     |     |  Létrehozás + frissítés | 
| **AddressLineData (Címsoradatai)**    |  utcacím  |     |   Létrehozás + frissítés |
| **Önkormányzat**   |   l   |     | Létrehozás + frissítés |
| **CountryReferenceTwoLetter**      |   Co |     |   Létrehozás + frissítés |
| **CountryReferenceTwoLetter**    |  c  |     |         Létrehozás + frissítés |
| **CountryRegionReference** |  st     |     | Létrehozás + frissítés |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Létrehozás + frissítés |
| **Irányítószám**  |   postai kód  |     | Létrehozás + frissítés |
| **PrimaryWorkTelefon**  |  telefonszáma   |     | Létrehozás + frissítés |
| **Fax**      | faxphoneTelefonszám     |     |    Létrehozás + frissítés |
| **Mobil**  |    mobil       |     |       Létrehozás + frissítés |
| **Helyi hivatkozás** |  preferredLanguage (elsődleges nyelv)  |     |  Létrehozás + frissítés |                                               
| **Switch(\[Önkormányzat\], "OU=Standard users,OU=Users,OU=Default,OU=Locations,DC=contoso,DC=com", "Dallas", "OU=Standard Users,OU=Users,OU=Dallas,OU=Locations,DC=contoso,DC=com", "Austin", "OU=Standard Users,OU=Users,OU=Austin,OU=Locations,DC=contoso,DC=com", "Seattle", "OU=Standard Users,OU=Users,OU=Seattle,OU=Locations,DC=contoso,DC=com", "OU=Standard Users,OU=Users,OU=London,OU=Locations,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  Létrehozás + frissítés |

Miután az attribútumleképezés konfigurációja befejeződött, most [már engedélyezheti és elindíthatja a felhasználói kiépítési szolgáltatást.](#enable-and-launch-user-provisioning)

## <a name="configuring-user-provisioning-to-azure-ad"></a>Felhasználói kiépítés konfigurálása az Azure AD szolgáltatásba

Az alábbi szakaszok ismertetik a felhasználók kiépítési munkanapi azure-ból Azure AD-be való üzembe helyezésének lépéseit.

* [Az Azure AD-kiépítési összekötő alkalmazás hozzáadása és a kapcsolat létrehozása a Workday-hez](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Workday és Azure AD attribútumleképezések konfigurálása](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Felhasználói kiépítés engedélyezése és elindítása](#enable-and-launch-user-provisioning)

> [!IMPORTANT]
> Csak kövesse az alábbi eljárást, ha csak felhőalapú felhasználók, amelyek ki kell építeni az Azure AD, és nem a helyszíni Active Directory.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>1. rész: Az Azure AD-kiépítési összekötő alkalmazás hozzáadása és a kapcsolat létrehozása a Workday-hez

**A Workday konfigurálása az Azure Active Directory kiépítéséhez csak felhőalapú felhasználók számára:**

1. Nyissa meg a következőt: <https://portal.azure.com>.

2. Az Azure Portalon keresse meg és válassza az **Azure Active Directoryt.**

3. Válassza **a Vállalati alkalmazások**lehetőséget, majd az Összes **alkalmazás**lehetőséget.

4. Válassza **az Alkalmazás hozzáadása**lehetőséget, majd az **Összes** kategóriát.

5. Keresse meg a **Workday az Azure AD kiépítése,** és adja hozzá, hogy az alkalmazás a katalógusból.

6. Az alkalmazás hozzáadása és az alkalmazás részletei képernyő megjelenítése után válassza **a Kiépítés**lehetőséget.

7. Módosítsa a **létesítési** **módot** **Automatikusra.**

8. Töltse ki a **Rendszergazdai hitelesítő adatok szakaszt** az alábbiak szerint:

   * **Workday Username** – Adja meg a Munkanap-integrációs rendszerfiók felhasználónevét a bérlői tartománynév hozzáfűzésével. Kell kinéznie valami ilyesmi:username@contoso4

   * **Munkanap jelszava –** Adja meg a Workday integrációs rendszerfiók jelszavát

   * **Workday Web Services API URL-címe –** Adja meg a Workday webszolgáltatások végpontjának URL-címét a bérlő számára. Ez az érték https://wd3-impl-services1.workday.com/ccx/service/contoso4így kell kinéznie: , ahol *contoso4* helyett a megfelelő bérlő nevét és *wd3-impl* helyébe a megfelelő környezeti karakterláncot. Ha ez az URL-cím nem ismert, kérjük, működjön együtt a Workday integrációs partnerével vagy a támogatási képviselőjével a megfelelő URL-cím meghatározásához.

     > [!NOTE]
     > Alapértelmezés szerint az alkalmazás a Workday Web Services 21.1-es verzióját használja, ha az URL-címben nincs megadva verzióinformáció. Egy adott Workday Web Services API-verzió használatához használja az URL-formátumot:https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# <br>
     > Például: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v31.0


   * **Értesítési e-mail –** Adja meg e-mail címét, és jelölje be az "E-mail küldése hiba esetén" jelölőnégyzetet.

   * Kattintson a **Kapcsolat tesztelése** gombra.

   * Ha a kapcsolatteszt sikeres, kattintson **a** mentés gombra a tetején. Ha ez nem sikerül, ellenőrizze, hogy a Workday URL-címe és hitelesítő adatai érvényesek-e a Workday-ben.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>2. rész: A Workday és az Azure AD attribútum-hozzárendelések konfigurálása

Ebben a szakaszban konfigurálja, hogy a felhasználói adatok hogyan áramlanak a Workday-ről az Azure Active Directoryba csak felhőalapú felhasználók számára.

1. A **Hozzárendelések**csoport Kiépítés lapján kattintson a **Dolgozók szinkronizálása az Azure AD-hez**gombra.

2. A **Forrásobjektum-hatókör** mezőben kiválaszthatja, hogy a Workday mely felhasználói csoportjai legyenek az Azure AD-be való kiépítés hatókörében, attribútumalapú szűrők készletének meghatározásával. Az alapértelmezett hatókör a "Workday összes felhasználója". Példa szűrők:

   * Példa: Hatókör 1000000 és 2000000 közötti dolgozói azonosítóval rendelkező felhasználók számára

      * Attribútum: WorkerID

      * Operátor: REGEX Match

      * Érték: (1[0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9])

   * Példa: Csak a függő munkavállalók, a törzsben nem foglalkoztatottak.

      * Attribútum: Függő azonosító

      * Operátor: NEM NULL

3. A **célobjektum-műveletek** mezőben globálisan szűrheti, hogy milyen műveleteket hajt végre az Azure AD.In the Target Object Actions field, you can globally filter what actions are performed on Azure AD. **A létrehozás** és **a frissítés** a leggyakoribb.

4. Az **Attribútumleképezések** szakaszban meghatározhatja, hogy az egyes Workday attribútumok hogyan feleljenek meg az Active Directory-attribútumokhoz.

5. A frissítéshez kattintson egy meglévő attribútumleképezésre, vagy kattintson az **Új leképezés hozzáadása** gombra a képernyő alján az új leképezések hozzáadásához. Egy egyéni attribútumleképezés támogatja a következő tulajdonságokat:

   * **Leképezés típusa**

      * **Közvetlen** – A Workday attribútum értékét írja az AD attribútumba, módosítások nélkül

      * **Állandó** – írjon statikus, állandó karakterláncértéket az AD attribútumba

      * **Kifejezés** – Lehetővé teszi, hogy egyéni értéket írjon az AD attribútumba egy vagy több Workday attribútum alapján. [További információt a kifejezésekről szóló cikkben talál.](../app-provisioning/functions-for-customizing-application-data.md)

   * **Forrásattribútum** – A Workday felhasználói attribútuma. Ha a keresett attribútum nincs jelen, olvassa el [a Workday felhasználói attribútumok listájának testreszabása című témakört.](#customizing-the-list-of-workday-user-attributes)

   * **Alapértelmezett érték** – nem kötelező. Ha a forrásattribútum üres értékkel rendelkezik, a leképezés ezt az értéket írja be helyette.
            A leggyakoribb konfiguráció ezt üresen hagyja.

   * **Célattribútum** – A felhasználói attribútum az Azure AD-ben.

   * **Objektumok egyeztetése ezzel az attribútummal** – Függetlenül attól, hogy ezt az attribútumot kell használni, hogy egyedileg azonosítani a felhasználókat a Workday és az Azure AD között. Ez az érték általában a Workday dolgozói azonosító mezőjében van beállítva, amely általában az Alkalmazott-azonosító attribútumhoz (új) vagy egy azure AD-ben egy bővítmény attribútumhoz van rendelve.

   * **Egyező sorrend** – Több egyező attribútum is beállítható. Ha több van, akkor a program az ebben a mezőben meghatározott sorrendben értékeli ki őket. Amint egyezést talál, a rendszer nem értékeli ki a további egyező attribútumokat.

   * **A leképezés alkalmazása**

     * **Mindig** – A leképezés alkalmazása a felhasználó létrehozási és frissítési műveletekre is

     * **Csak a létrehozás során** – A leképezés alkalmazása csak a felhasználó létrehozási műveleteire

6. A leképezések mentéséhez kattintson a **Mentés** gombra az Attribútumleképezés szakasz tetején.

Miután az attribútumleképezés konfigurációja befejeződött, most [már engedélyezheti és elindíthatja a felhasználói kiépítési szolgáltatást.](#enable-and-launch-user-provisioning)

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Az Azure AD attribútum-visszaírás konfigurálása a Workday szolgáltatásba

Kövesse ezeket az utasításokat a felhasználói e-mail címek és felhasználónév visszaírásának konfigurálásához az Azure Active Directoryból a Workday szolgáltatásba.

* [A Visszaírás összekötő alkalmazás hozzáadása és a kapcsolat létrehozása a Workday szolgáltatással](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Visszaíró attribútum-hozzárendelések konfigurálása](#part-2-configure-writeback-attribute-mappings)
* [Felhasználói kiépítés engedélyezése és elindítása](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>1. rész: A Visszaírás összekötő alkalmazás hozzáadása és a kapcsolat létrehozása a Workday-hez

**A Workday Writeback összekötő konfigurálása:**

1. Nyissa meg a következőt: <https://portal.azure.com>.

2. Az Azure Portalon keresse meg és válassza az **Azure Active Directoryt.**

3. Válassza **a Vállalati alkalmazások**lehetőséget, majd az Összes **alkalmazás**lehetőséget.

4. Válassza **az Alkalmazás hozzáadása**lehetőséget, majd az **Összes** kategóriát.

5. Keresse meg a **Workday Writeback**, és adja hozzá, hogy az alkalmazás a galériában.

6. Az alkalmazás hozzáadása és az alkalmazás részletei képernyő megjelenítése után válassza **a Kiépítés**lehetőséget.

7. Módosítsa a **létesítési** **módot** **Automatikusra.**

8. Töltse ki a **Rendszergazdai hitelesítő adatok szakaszt** az alábbiak szerint:

   * **Rendszergazdai felhasználónév** – Adja meg a Workday integrációs rendszerfiók felhasználónevét a bérlői tartománynév hozzáfűzésével. A következőhöz kell *hasonlítania:\@contoso4 felhasználónév*

   * **Rendszergazdai jelszó –** Adja meg a Workday integrációs rendszerfiók jelszavát

   * **Bérlő url-címe –** Adja meg a Workday webszolgáltatások végpontjának URL-címét a bérlő számára. Ez az érték https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resourcesígy kell kinéznie: , ahol *contoso4* helyett a megfelelő bérlő nevét és *wd3-impl* helyébe a megfelelő környezeti karakterlánc (ha szükséges).

   * **Értesítési e-mail –** Adja meg e-mail címét, és jelölje be az "E-mail küldése hiba esetén" jelölőnégyzetet.

   * Kattintson a **Kapcsolat tesztelése** gombra. Ha a kapcsolatteszt sikeres, kattintson **a** mentés gombra a tetején. Ha ez nem sikerül, ellenőrizze, hogy a Workday URL-címe és hitelesítő adatai érvényesek-e a Workday-ben.

### <a name="part-2-configure-writeback-attribute-mappings"></a>2. rész: Visszaíró attribútum-hozzárendelések konfigurálása

Ebben a szakaszban konfigurálja, hogyan writeback attribútumok áramlását az Azure AD workday. Jelenleg az összekötő csak támogatja az e-mail cím és a felhasználónév workday-be történő visszaírását.

1. A **Hozzárendelések**csoport Kiépítés lapján kattintson az **Azure Active Directory felhasználóinak szinkronizálása a workday szolgáltatásba**elemre.

2. A **Forrásobjektum-hatókör** mezőben tetszés szerint szűrheti, hogy az Azure Active Directory felhasználóinak mely készletei nek kell visszaírniuk az e-mail címüket a Workday-nek. Az alapértelmezett hatókör "az Azure AD összes felhasználója".

3. Az **Attribútum-leképezések** szakaszban frissítse az egyező azonosítót, hogy jelezze az attribútumot az Azure Active Directoryban, ahol a Workday munkavégző azonosító vagy alkalmazottazonosítója található. Egy népszerű egyezési módszer a Workday munkavégző azonosító vagy alkalmazotti azonosító szinkronizálása az Attribute1-15 bővítményhez az Azure AD-ben, majd használja ezt az attribútumot az Azure AD-ben a felhasználók nak a Workday-ben való egyeztetéséhez.

4. Általában leképezi az Azure AD *userPrincipalName* attribútumot a Workday *UserID* attribútumhoz, és leképezi az Azure AD *mail* attribútumot a Workday *EmailAddress* attribútumhoz. A leképezések mentéséhez kattintson a **Mentés** gombra az Attribútumleképezés szakasz tetején.

Miután az attribútumleképezés konfigurációja befejeződött, most [már engedélyezheti és elindíthatja a felhasználói kiépítési szolgáltatást.](#enable-and-launch-user-provisioning)

## <a name="enable-and-launch-user-provisioning"></a>Felhasználói kiépítés engedélyezése és elindítása

Miután a Workday kiépítési alkalmazás konfigurációk befejeződött, bekapcsolhatja a létesítési szolgáltatás az Azure Portalon.

> [!TIP]
> Alapértelmezés szerint, ha bekapcsolja a létesítési szolgáltatás, akkor kezdeményezi a létesítési műveletek et a hatókörben lévő összes felhasználó számára. Ha hibák vannak a leképezési vagy workday-i adatproblémákban, akkor a létesítési feladat sikertelen lehet, és a karantén állapotba kerülhet. Ennek elkerülése érdekében ajánlott eljárásként azt javasoljuk, hogy konfigurálja **a Forrásobjektum-hatókör** szűrőt, és tesztelje az attribútum-leképezéseket néhány tesztfelhasználóval, mielőtt elindítaná a teljes szinkronizálást az összes felhasználó számára. Miután meggyőződött arról, hogy a leképezések működnek, és a kívánt eredményt adják, eltávolíthatja a szűrőt, vagy fokozatosan kibonthatja, hogy több felhasználót vegyen fel.

1. A **Kiépítés** lapon állítsa a **Kiépítési állapot** ot **Bekapcsolva**.

2. Kattintson a **Save** (Mentés) gombra.

3. Ez a művelet elindítja a kezdeti szinkronizálást, amely változó számú órát vehet igénybe attól függően, hogy hány felhasználó van a Workday-bérlőben. 

4. Bármikor tekintse meg a **naplózási naplók** lapon az Azure Portalon, hogy milyen műveleteket hajtott végre a létesítési szolgáltatás. A naplózási naplók felsorolja a kiépítési szolgáltatás által végrehajtott összes egyes szinkronizálási eseményt, például azt, hogy mely felhasználókat olvassa fel a Workday szolgáltatásból, majd ezt követően hozzáadják vagy frissítik az Active Directoryban. A naplózási naplók áttekintésével és a kiépítési hibák javításával kapcsolatos tudnivalókat a Hibaelhárítás című szakaszban találja.

5. Miután a kezdeti szinkronizálás befejeződött, egy naplózási összefoglaló jelentést fog írni a **Kiépítés** lapon, az alábbiak szerint.

   ![Azure Portal](./media/workday-inbound-tutorial/wd_3.png)

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

* **Megoldási képességekkel kapcsolatos kérdések**
  * [Amikor új bérlést dolgoz fel a Workday-ből, hogyan állítja be a megoldás az új felhasználói fiók jelszavát az Active Directoryban?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [A megoldás támogatja az e-mail es értesítések küldését a kiépítési műveletek befejezése után?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Hogyan kezelhetem az új alkalmazottak jelszavainak kézbesítését, és hogyan biztosíthatok biztonságos mechanizmust a jelszó visszaállításához?](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [A megoldás gyorsítótárazza a Workday felhasználói profilokat az Azure AD-felhőben vagy a kiépítési ügynök rétegében?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [A megoldás támogatja a helyszíni AD-csoportok hozzárendelését a felhasználóhoz?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Mely workday API-kat használ a megoldás a Workday munkavégző profilok lekérdezéséhez és frissítéséhez?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Konfigurálhatom a Workday HCM-bérlőmet két Azure AD-bérlővel?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Miért "Workday to Azure AD" felhasználói kiépítési alkalmazás nem támogatott, ha már üzembe az Azure AD Connect?](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [Hogyan javasolhatok fejlesztéseket, vagy kérhetek új funkciókat a Workday és az Azure AD-integrációval kapcsolatban?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Ügynök kiépítésével kapcsolatos kérdések**
  * [Mi a kiépítési ügynök GA verziója?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Honnan tudhatom a kiépítési ügynökem verzióját?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [A Microsoft automatikusan lenyomja a kiépítési ügynök frissítéseit?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Telepíthetem a kiépítési ügynök ugyanazon a kiszolgálón futó Azure AD Connect?](#can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect)
  * [Hogyan konfigurálhatom a kiépítési ügynököt úgy, hogy proxykiszolgálót használjon a kimenő HTTP-kommunikációhoz?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Hogyan biztosíthatom, hogy a kiépítési ügynök képes kommunikálni az Azure AD-bérlővel, és nincs tűzfal blokkolja az ügynök által igényelt portokat?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Hogyan tudom törölni a létesítési ügynökhöz társított tartomány regisztrációjának törlését?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Hogyan távolíthatom el a kiépítési ügynököt?](#how-do-i-uninstall-the-provisioning-agent)
  
* **Munkanap-AD attribútumleképezési és konfigurációs kérdések**
  * [Hogyan készíthetek biztonsági másolatot vagy exportálhatok a Workday kiépítési attribútumleképezésésésésési és séma-leképezésének és sémának egy működő példányát?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Egyéni attribútumok vannak a Workday és az Active Directory könyvtárban. Hogyan konfigurálhatom a megoldást az egyéni attribútumokkal való együttműködésre?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Kiépíthetem a felhasználó fényképét a Workday szolgáltatásból az Active Directoryba?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Hogyan szinkronizálhatom a Mobileszámokat a Workday-ből a nyilvános használathoz való felhasználói hozzájárulás alapján?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Hogyan formázhatom a megjelenítendő neveket az AD-ben a felhasználó osztály-/ország/város attribútumai alapján, és hogyan kezelhetem a regionális eltéréseket?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Hogyan hozhatok létre egyedi értékeket a(z) samAccountName attribútumhoz?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Hogyan távolíthatom el a mellékjeleket, és hogyan alakíthatom őket normál angol ábécévé?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Megoldási képességekkel kapcsolatos kérdések

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Amikor új bérlést dolgoz fel a Workday-ből, hogyan állítja be a megoldás az új felhasználói fiók jelszavát az Active Directoryban?

Amikor a helyszíni létesítő ügynök egy új AD-fiók létrehozására irányuló kérelmet kap, automatikusan létrehoz egy összetett véletlenszerű jelszót, amely megfelel az AD-kiszolgáló által meghatározott jelszóösszetettségi követelményeknek, és beállítja ezt a felhasználói objektumon. Ez a jelszó nincs bejelentkezve sehol.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>A megoldás támogatja az e-mail es értesítések küldését a kiépítési műveletek befejezése után?

Nem, az e-mail es értesítések küldése a kiépítési műveletek befejezése után nem támogatott a jelenlegi kiadásban.

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>Hogyan kezelhetem az új alkalmazottak jelszavainak kézbesítését, és hogyan biztosíthatok biztonságos mechanizmust a jelszó visszaállításához?

Az új AD-fiók-kiépítés egyik utolsó lépése a felhasználó AD-fiókjához rendelt ideiglenes jelszó kézbesítése. Sok vállalat továbbra is a hagyományos megközelítést használja az ideiglenes jelszó kézbesítéséhez a felhasználó vezetőjének, aki ezután átadja a jelszót az új alkalmazottnak/függő dolgozónak. Ez a folyamat egy belső biztonsági hibával rendelkezik, és van egy lehetőség az Azure AD-képességek használatával jobb megközelítés megvalósításához.

A felvételi folyamat részeként a HR csapatok általában háttérellenőrzést futtatnak, és ellenőrzik az új felvétel mobilszámát. A Workday az AD-felhasználók kiépítési integráció, hozhat létre a tetején ezt a tényt, és egy önkiszolgáló jelszó-visszaállítási képesség bevezetése a felhasználó számára az 1. Ez úgy érhető el, hogy az azure AD Connect használatával propagálja az új hire "Mobilszám" attribútumát a Workday-től az AD-ig, majd az AD-től az Azure AD-ig. Miután a "Mobilszám" megtalálható az Azure AD-ben, engedélyezheti az [önkiszolgáló jelszó-visszaállítás (SSPR)](../authentication/howto-sspr-authenticationdata.md) a felhasználó fiókjához, így az első napon egy új hire használhatja a regisztrált és ellenőrzött mobilszámot a hitelesítéshez.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>A megoldás gyorsítótárazza a Workday felhasználói profilokat az Azure AD-felhőben vagy a kiépítési ügynök rétegében?

Nem, a megoldás nem tartja karban a felhasználói profilok gyorsítótárát. Az Azure AD-kiépítési szolgáltatás egyszerűen adatfeldolgozóként működik, adatokat olvas fel a Workday-ből, és a cél Active Directoryba vagy az Azure AD-be ír. A [személyes adatok kezelése](#managing-personal-data) című szakaszban a felhasználók adatainak védelmével és az adatok megőrzésével kapcsolatos részleteket találja.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>A megoldás támogatja a helyszíni AD-csoportok hozzárendelését a felhasználóhoz?

Ez a funkció jelenleg nem támogatott. Ajánlott megoldás egy PowerShell-parancsfájl üzembe helyezése, amely lekérdezi a Microsoft Graph API-végpontot a [naplóadatok](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-beta) lekérdezéséhez, és ezt használja olyan forgatókönyvek indításához, mint például a csoporthozzárendelés. Ez a PowerShell-parancsfájl csatolható egy feladatütemezőhöz, és üzembe helyezhető ugyanabban a mezőben a létesítő ügynök.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Mely workday API-kat használ a megoldás a Workday munkavégző profilok lekérdezéséhez és frissítéséhez?

A megoldás jelenleg a következő Workday API-kat használja:

* A **Felügyeleti hitelesítő adatok** szakaszban használt **Workday Web Services API URL-formátum** határozza meg a Get_Workers
  * Ha az URL-formátum:\#\#\#\#\.\.https:// workday com/ccx/service/tenantName , majd az API v21.1 lesz használva. 
  * Ha az URL formátuma: https://\#\#\#\#\.workday\.com/ccx/service/tenantName/Human\_Resources , akkor az API v21.1-et használja a rendszer. 
  * Ha az URL-formátum:\#\#\#\#\.\.https:// workday com/ccx/service/tenantName/Human\_Resources/v,\# \# \. \# akkor a megadott API-verziót használja a rendszer. (Példa: ha a 34.0-s paraméter van megadva, akkor a használatban van.)  
   
* Workday Email Writeback funkció Maintain_Contact_Information (v26.1) 
* Workday Felhasználónév Writeback szolgáltatás Update_Workday_Account (v31.2) 

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Konfigurálhatom a Workday HCM-bérlőmet két Azure AD-bérlővel?

Igen, ez a konfiguráció támogatott. A forgatókönyv konfigurálásának magas szintű lépései:

* Üzembe helyezheti a #1 üzembe helyezési ügynökét, és regisztrálja azt az Azure AD-bérlői #1.
* Üzembe helyezheti a #2 üzembe helyezési ügynökét, és regisztrálja az Azure AD-#2.
* Az egyes kiépítési megbízottak által kezelt "gyermektartományok" alapján konfigurálja az egyes ügynököket a tartomány(ok)kal. Egy ügynök több tartományt is képes kezelni.
* Az Azure Portalon állítsa be a Workday az AD-felhasználó kiépítési alkalmazás minden bérlőben, és konfigurálja azt a megfelelő tartományokat.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>Miért "Workday to Azure AD" felhasználói kiépítési alkalmazás nem támogatott, ha már üzembe az Azure AD Connect?

Ha az Azure AD-t hibrid módban használják (ahol felhőalapú + helyszíni felhasználók kombinációját tartalmazza), fontos, hogy egyértelműdefinícióval rendelkezik a "jogosultságforrás" fogalmában. Általában hibrid forgatókönyvek az Azure AD Connect üzembe helyezését igénylik. Az Azure AD Connect üzembe helyezésekor a helyszíni AD a hatóság forrása. A Workday az Azure AD-összekötő bemutatkozása a mixben olyan helyzetet eredményezhet, amelyben a Workday attribútumértékek potenciálisan felülírhatják az Azure AD Connect által beállított értékeket. Ezért a "Workday to Azure AD" kiépítési alkalmazás használata nem támogatott, ha az Azure AD Connect engedélyezve van. Ilyen esetekben azt javasoljuk, hogy a "Workday to AD User" kiépítési alkalmazást használja a felhasználók helyszíni AD-be való bejuttatásához, majd szinkronizálja őket az Azure AD Connect használatával.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Hogyan javasolhatok fejlesztéseket, vagy kérhetek új funkciókat a Workday és az Azure AD-integrációval kapcsolatban?

Visszajelzéseit nagyra értékeljük, mivel segít meghatározni a jövőbeli kiadások és fejlesztések irányát. Minden visszajelzést szívesen fogadunk, és javasoljuk, hogy küldje el ötletét vagy fejlesztési javaslatát az [Azure AD visszajelzési fórumában.](https://feedback.azure.com/forums/169401-azure-active-directory) A Workday-integrációval kapcsolatos konkrét visszajelzések értéséhez válassza ki a *SaaS-alkalmazások kategóriát,* és keressen a *Workday* kulcsszavak használatával a Workday-hez kapcsolódó meglévő visszajelzések megkereséséhez.

![UserVoice SaaS-alkalmazások](media/workday-inbound-tutorial/uservoice_saas_apps.png)

![UserVoice munkanap](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Amikor új ötletre utalsz, ellenőrizd, hogy valaki más már javasolt-e hasonló funkciót. Ebben az esetben, akkor akár szavazni a funkció vagy javítása kérelmet. Ön is hagy egy magyarázat vonatkozólag -a különleges használ eset -hoz mutat -a támogat részére a eszme és bemutat hogyan a vonás lesz értékes érted túl.

### <a name="provisioning-agent-questions"></a>Ügynök kiépítésével kapcsolatos kérdések

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Mi a kiépítési ügynök GA verziója?

* A kiépítési ügynök GA verziója 1.1.30 vagy újabb.
* Ha az ügynök verziója kevesebb, mint 1.1.30, akkor a nyilvános előzetes verziót futtatja, és automatikusan frissül a ga verzióra, ha az ügynököt üzemeltető kiszolgáló .NET 4.7.1 futtatóhek.
  * Ellenőrizheti a kiszolgálón telepített [.NET verziót.](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) Ha a kiszolgálón nem fut .NET 4.7.1, [letöltheti és telepítheti a .NET 4.7.1 programot.](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows) A kiépítési ügynök a .NET 4.7.1 telepítése után automatikusan frissül a ga verzióra.

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Honnan tudhatom a kiépítési ügynökem verzióját?

* Jelentkezzen be arra a Windows-kiszolgálóra, amelyen a kiépítési ügynök telepítve van.
* A **Vezérlőpult** -> **eltávolítása vagy módosítása menüben**
* Keresse meg a Microsoft **Azure AD Connect kiépítési ügynök** bejegyzésnek megfelelő verziót

  ![Azure Portal](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>A Microsoft automatikusan lenyomja a kiépítési ügynök frissítéseit?

Igen, a Microsoft automatikusan frissíti a kiépítési ügynököt. Az automatikus frissítések letiltásához állítsa le a **Microsoft Azure AD Connect Agent Updater**windowsos szolgáltatását.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect"></a>Telepíthetem a kiépítési ügynök ugyanazon a kiszolgálón futó Azure AD Connect?

Igen, telepítheti a kiépítési ügynök ugyanazon a kiszolgálón, amely az Azure AD Connect fut.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>A konfiguráció időpontjában a kiépítési ügynök kéri az Azure AD-rendszergazdai hitelesítő adatokat. Az ügynök helyileg tárolja a hitelesítő adatokat a kiszolgálón?

A konfiguráció során a kiépítési ügynök csak az Azure AD-rendszergazdai hitelesítő adatokat kér az Azure AD-bérlőhöz való csatlakozáshoz. Nem tárolja a hitelesítő adatokat helyileg a kiszolgálón. Azonban megtartja a helyi Windows *jelszótárolóban a helyszíni Active Directory-tartományhoz* való csatlakozáshoz használt hitelesítő adatokat.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Hogyan konfigurálhatom a kiépítési ügynököt úgy, hogy proxykiszolgálót használjon a kimenő HTTP-kommunikációhoz?

A kiépítési ügynök támogatja a kimenő proxy használatát. Konfigurálhatja a **C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config**ügynökkonfigurációs fájl szerkesztésével. Adja hozzá a következő sorokat, a fájl vége `</configuration>` felé közvetlenül a záró címke előtt.
Cserélje le a változókat [proxy-kiszolgáló] és a [proxy-port] a proxykiszolgáló nevére és portértékeire.

```xml
    <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
             <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
             />
         </defaultProxy>
    </system.net>
```

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Hogyan biztosíthatom, hogy a kiépítési ügynök képes kommunikálni az Azure AD-bérlővel, és nincs tűzfal blokkolja az ügynök által igényelt portokat?

Azt is ellenőrizheti, hogy az összes szükséges port nyitva van-e, ha megnyitja az [összekötő portok teszteszközét](https://aadap-portcheck.connectorporttest.msappproxy.net/) a helyszíni hálózatról. Több zöld pipa nagyobb rugalmasságot jelent.

Annak érdekében, hogy az eszköz a megfelelő eredményeket adja, győződjön meg róla, hogy:

* Nyissa meg az eszközt egy böngészőben arról a kiszolgálóról, amelyen telepítette a kiépítési ügynököt.
* Győződjön meg arról, hogy a kiépítési ügynökre vonatkozó proxyk vagy tűzfalak is vonatkoznak erre az oldalra. Ezt az Internet Explorer programban úgy teheti meg, hogy a **Beállítások -> Internetbeállítások -> kapcsolatok -> LAN-beállítások lehetőséget**találja. Ezen az oldalon a "Proxykiszolgáló használata a helyi hálózathoz" mező látható. Jelölje be ezt a jelölőnégyzetet, és helyezze a proxycímet a "Cím" mezőbe.

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Konfigurálható egy kiépítési ügynök több AD-tartomány kiépítésére?

Igen, egy kiépítési ügynök beállítható több AD-tartomány kezelésére mindaddig, amíg az ügynök a megfelelő tartományvezérlők számára megfelelő látótávolsággal rendelkezik. A Microsoft azt javasolja, hogy a magas rendelkezésre állás biztosítása és a feladatátvételi támogatás biztosítása érdekében állítson be egy 3 kiépítési ügynökből álló csoportot, amely ugyanazokat az AD-tartományokat szolgálja ki.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Hogyan tudom törölni a létesítési ügynökhöz társított tartomány regisztrációjának törlését?

* Az Azure Portalon az Azure AD-bérlő *bérlői azonosítóját.*
* Jelentkezzen be a Kiépítési ügynököt futtató Windows-kiszolgálóra.
* Nyissa meg a PowerShellt Windows-rendszergazdaként.
* Váltson a regisztrációs parancsfájlokat tartalmazó könyvtárra, \[és futtassa a következő parancsokat, amelyek a bérlői azonosító\] paramétert a bérlői azonosító értékére cserélik.

  ```powershell
  cd “C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder”
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* A megjelenő ügyintézők listájából másolja a `id` mező értékét abból az erőforrásból, amelynek *erőforrásneve* megegyezik az AD tartománynevével.
* Illessze be az azonosító értékét ebbe a parancsba, és hajtsa végre a parancsot a PowerShellben.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Futtassa újra az Ügynök konfigurációs varázslóját.
* Minden más, a tartományhoz korábban hozzárendelt ügyintézőt újra kell konfigurálni.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Hogyan távolíthatom el a kiépítési ügynököt?

* Jelentkezzen be arra a Windows-kiszolgálóra, amelyen a kiépítési ügynök telepítve van.
* A **Vezérlőpult** -> **eltávolítása vagy módosítása menüben**
* Távolítsa el a következő programokat:
  * Microsoft Azure AD Connect kiépítési ügynök
  * Microsoft Azure AD Connect ügynökfrissítő
  * Microsoft Azure AD Connect kiépítési ügynök csomag

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Munkanap-AD attribútumleképezési és konfigurációs kérdések

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Hogyan készíthetek biztonsági másolatot vagy exportálhatok a Workday kiépítési attribútumleképezésésésésési és séma-leképezésének és sémának egy működő példányát?

A Microsoft Graph API segítségével exportálhatja a Workday felhasználói kiépítési konfigurációját. A részleteket a [Workday User provisioning Attribute Mapping konfiguráció exportálása és importálása című](#exporting-and-importing-your-configuration) szakaszban ismertetett lépésekben találja.

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Egyéni attribútumok vannak a Workday és az Active Directory könyvtárban. Hogyan konfigurálhatom a megoldást az egyéni attribútumokkal való együttműködésre?

A megoldás támogatja az egyéni Workday és Active Directory attribútumokat. Ha egyéni attribútumokat szeretne hozzáadni a leképezési sémához, nyissa meg az **Attribútumleképezés** panelt, és görgessen le a **Speciális beállítások megjelenítése**című szakasz kibontásához. 

![Attribútumlista szerkesztése](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Az egyéni Munkanap attribútumok hozzáadásához válassza a *Workday attribútumlistájának szerkesztése* lehetőséget, és adja hozzá az egyéni AD-attribútumokat, válassza a *Helyszíni Active Directory attribútumlistájának szerkesztése*lehetőséget.

Lásd még:

* [A Workday felhasználói attribútumainak testreszabása](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Hogyan konfigurálhatom a megoldást úgy, hogy csak az AD attribútumait frissítse a Workday módosításai alapján, és ne hozzon létre új AD-fiókokat?

Ez a konfiguráció az **Attribútumleképezések** panelen a **célobjektum-műveletek** beállításával érhető el az alábbi módon:

![Frissítési művelet](./media/workday-inbound-tutorial/wd_target_update_only.png)

Jelölje be a "Frissítés" jelölőnégyzetet, ha csak a Workday-ről AD-re irányuló frissítési műveletekhez van bejelölve. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Kiépíthetem a felhasználó fényképét a Workday szolgáltatásból az Active Directoryba?

A megoldás jelenleg nem támogatja a bináris attribútumok, például *a thumbnailPhoto* és *a jpegPhoto* beállítását az Active Directoryban.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Hogyan szinkronizálhatom a Mobileszámokat a Workday-ből a nyilvános használathoz való felhasználói hozzájárulás alapján?

* Lépjen a "Kiépítés" panel a workday kiépítés alkalmazás.
* Kattintson az Attribútumleképezések elemre 
* A **Leképezések**csoportban válassza **a Workday Workers szinkronizálása a helyszíni Active Directoryval** (vagy **a munkanap-dolgozók szinkronizálása az Azure AD-vel) lehetőséget.**
* Az Attribútumleképezések lapon görgessen le, és jelölje be a "Speciális beállítások megjelenítése" jelölőnégyzetet.  Kattintson a **Workday attribútumlistájának szerkesztése elemre**
* A megnyíló panelen keresse meg a "Mobile" attribútumot, és kattintson a sorra, hogy szerkeszthesse az **API Expression** ![Mobile GDPR-t](./media/workday-inbound-tutorial/mobile_gdpr.png)

* Cserélje le az **API-kifejezést** a következő új kifejezésre, amely csak akkor olvassa be a munkahelyi mobilszámot, ha a "Nyilvános használati jelző" értéke "True" a Workday alkalmazásban.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Mentse az attribútumlistát.
* Mentse az attribútumleképezést.
* Törölje az aktuális állapotot, és indítsa újra a teljes szinkronizálást.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Hogyan formázhatom a megjelenítendő neveket az AD-ben a felhasználó osztály-/ország/város attribútumai alapján, és hogyan kezelhetem a regionális eltéréseket?

Gyakori követelmény, hogy az *AD-ben* konfigurálja a displayName attribútumot, hogy az a felhasználó részlegéről és országáról/régiójáról is információt nyújtjon. Például, ha John Smith dolgozik a Marketing Department az Usa-ban, érdemes lehet a *displayName* jelenik meg, mint *Smith, John (Marketing-US)*.

Itt van, hogyan lehet kezelni az ilyen követelményeket a *KN* vagy *displayName* létrehozásához, hogy olyan attribútumokat tartalmazzon, mint a vállalat, a részleg, a város vagy az ország/régió.

* Minden Workday attribútum ot egy mögöttes XPATH API-kifejezéssel olvassa be, amely konfigurálható az **Attribute Mapping -> Advanced Section -> Edit attribútumlistában a Workday számára.** Itt van a Workday *PreferredFirstName*, *PreferredLastName*, *Company* és *SupervisoryOrganization* attribútumok alapértelmezett XPATH API-kifejezése.

     | Munkanap attribútum | API XPATH-kifejezés |
     | ----------------- | -------------------- |
     | PreferredFirstName (Elsődlegesutónév) | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:First_Name/text() |
     | PreferredLastName (PreferredLastName) | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | Vállalat | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']=''Vállalat']/wd:Organization_Reference/@wd:Descriptor |
     | Felügyeleti szervezet | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Supervisory']/wd:Organization_Name/text() |
  
   Erősítse meg a Workday-csapattal, hogy a fenti API-kifejezés érvényes-e a Workday bérlői konfigurációjára. Szükség esetén szerkesztheti őket a [Workday felhasználói attribútumok listájának testreszabása](#customizing-the-list-of-workday-user-attributes)című szakaszban leírtak szerint.

* Hasonlóképpen a Workday-ben szereplő országadatokat a következő XPATH használatával olvassa be a rendszer: *wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference*

     A Workday attribútumlista szakaszban 5 országgal kapcsolatos attribútum érhető el.

     | Munkanap attribútum | API XPATH-kifejezés |
     | ----------------- | -------------------- |
     | Országhivatkozás | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferenceFriendly (Országhivatkozásbarát) | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Numeric-3_Code']/text() |
     | CountryReferenceTwoLetter | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-2_Code']/text() |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Erősítse meg a Workday-csapattal, hogy a fenti API-kifejezések érvényesek-e a Workday bérlői konfigurációjára. Szükség esetén szerkesztheti őket a [Workday felhasználói attribútumok listájának testreszabása](#customizing-the-list-of-workday-user-attributes)című szakaszban leírtak szerint.

* A megfelelő attribútumleképezési kifejezés létrehozásához azonosítsa, hogy a "mérvadó" Workday attribútum melyik a felhasználó keresztnevét, vezetéknevét, országát/régióját és részlegét jelöli. Tegyük fel, hogy az attribútumok *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* és *SupervisoryOrganization.* Ezzel az AD *displayName* attribútum kifejezésének létrehozásához az alábbiak szerint kaphat megjelenítendő nevet, például *Smith, John (Marketing-US)* nevet.

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Miután rendelkezik a megfelelő kifejezéssel, módosítsa az Attribútumleképezések táblát, ![és módosítsa a *displayName* attribútumleképezést az alábbi módon: DisplayName Mapping](./media/workday-inbound-tutorial/wd_displayname_map.png)

* A fenti példa kiterjesztésével tegyük fel, hogy a Workday-ből érkező városneveket gyorsírási értékekké szeretné konvertálni, majd olyan megjelenítendő nevek létrehozásához, mint *Smith, John (CHI)* vagy *Doe, Jane (NYC),* majd ez az eredmény egy Kapcsoló kifejezés használatával érhető el, amelynek a Workday *Önkormányzat* attribútuma a meghatározó változó.

     ```
    Switch
    (
      [Municipality],
      Join(", ", [PreferredLastName], [PreferredFirstName]),  
           "Chicago", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(CHI)"),
           "New York", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(NYC)"),
           "Phoenix", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(PHX)")
    )
     ```
    Lásd még:
  * [Kapcsoló függvény szintaxisa](../app-provisioning/functions-for-customizing-application-data.md#switch)
  * [Illesztés függvény szintaxisához](../app-provisioning/functions-for-customizing-application-data.md#join)
  * [Függvény szintaxisának hozzáfűzése](../app-provisioning/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Hogyan hozhatok létre egyedi értékeket a(z) samAccountName attribútumhoz?

Tegyük fel, hogy egyedi értékeket szeretne létrehozni a *samAccountName* attribútumhoz a Workday *Utónév* és *vezetéknév* attribútumok kombinációjával. Az alábbiakban egy kifejezés, hogy lehet kezdeni:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

Hogyan működik a fenti kifejezés: Ha a felhasználó John Smith, először megpróbálja generálni JSmith, ha JSmith már létezik, akkor generál JoSmith, ha létezik, generál JohSmith. A kifejezés azt is biztosítja, hogy a létrehozott érték megfeleljen a *samAccountName*kapcsolatban szereplő hosszkorlátozásnak és speciális karakterkorlátozásnak.

Lásd még:

* [Középső függvény szintaxisa](../app-provisioning/functions-for-customizing-application-data.md#mid)
* [Függvény szintaxisának cseréje](../app-provisioning/functions-for-customizing-application-data.md#replace)
* [SelectUniqueValue függvény szintaxisa](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Hogyan távolíthatom el a mellékjeleket, és hogyan alakíthatom őket normál angol ábécévé?

A [NormalizeDiacritics függvénnyel](../app-provisioning/functions-for-customizing-application-data.md#normalizediacritics) eltávolíthatja a felhasználó kereszt- és vezetéknevének speciális karaktereit, miközben a felhasználó e-mail címét vagy CN-értékét építi fel.

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

Ez a szakasz konkrét útmutatást nyújt a workday-integrációval kapcsolatos kiépítési problémák elhárításához az Azure AD naplózási naplók és a Windows Server Eseménynapló-naplók használatával. Az oktatóanyagban rögzített általános hibaelhárítási lépésekre és fogalmakra [épül: A felhasználói fiókok automatikus kiépítéséről szóló jelentés](../app-provisioning/check-status-user-account-provisioning.md)

Ez a szakasz a hibaelhárítás következő szempontjait ismerteti:

* [A Windows Eseménynapló beállítása ügynökhibaelhárításhoz](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Az Azure Portal naplózási naplóinak beállítása a szolgáltatások kalkapcsolatos hibaelhárításához](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Az AD felhasználói fiók létrehozásához felelős műveletek naplóinak ismertetése](#understanding-logs-for-ad-user-account-create-operations)
* [A Kezelő frissítési műveleteinek naplóinak ismertetése](#understanding-logs-for-manager-update-operations)
* [Gyakran előforduló hibák megoldása](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>A Windows Eseménynapló beállítása ügynökhibaelhárításhoz

* Bejelentkezés a Kiépítési ügynök üzembe helyezését tartalmazó Windows Server-rendszerbe
* Nyissa meg a **Windows Server Event Viewer** asztali alkalmazását.
* Válassza **a Windows naplók > alkalmazás lehetőséget.**
* Az **aktuális szűrőnapló használata...** lehetőséget az AAD forrás alatt naplózott összes esemény **megtekintésére. Connect.ProvisioningAgent** és zárja események Event ID "5", a szűrő megadásával "-5" az alábbiak szerint.

  ![Windows Eseménynapló](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* Kattintson **az OK gombra,** és rendezze az eredménynézetet **dátum és idő** oszlop szerint.

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Az Azure Portal naplózási naplóinak beállítása a szolgáltatások kalkapcsolatos hibaelhárításához

* Indítsa el az [Azure Portalon,](https://portal.azure.com)és keresse meg a Workday kiépítési alkalmazás **naplózási naplók** szakaszát.
* A Naplók naplózása lap **Oszlopok** gombjával csak a következő oszlopokjelennek meg a nézetben (Dátum, Tevékenység, Állapot, Állapot oka). Ez a konfiguráció biztosítja, hogy csak a hibaelhárításszempontjából fontos adatokra összpontosítson.

  ![Naplóoszlopok naplózása](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* A nézet szűréséhez használja a **Cél-** és **dátumtartomány** lekérdezési paramétereket. 
  * Állítsa a **Cél** lekérdezési paramétert a Workday munkavégző objektum "Dolgozóazonosító" vagy "Alkalmazott-azonosító" értékre.
  * Állítsa be a **dátumtartományt** egy megfelelő időszakra, amely alatt meg szeretné vizsgálni a kiépítéssel kapcsolatos hibákat vagy problémákat.

  ![Naplószűrők naplózása](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Az AD felhasználói fiók létrehozásához felelős műveletek naplóinak ismertetése

Amikor a Workday új felvételét észleli a rendszer (tegyüken a *21023-as*alkalmazotti azonosítóval), az Azure AD-kiépítési szolgáltatás megkísérli létrehozni egy új AD felhasználói fiókot a dolgozó számára, és a folyamat során 4 naplórekordot hoz létre az alábbiak szerint:

  [![Napló létrehozása ops](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Ha valamelyik naplórekordra kattint, megnyílik a **Tevékenység részletei** lap. Itt látható, hogy a **Tevékenység részletei** lap mit jelenít meg az egyes naplóbejegyzés-típusokhoz.

* **Munkanap importálási** rekord: Ez a naplórekord a Workday-ből beolvasott munkavégző adatokat jeleníti meg. A naplórekord *További részletek* szakaszában található információk segítségével elháríthatja az adatok Workday-ből történő beolvasásával kapcsolatos problémákat. Az alábbiakban egy példarekord látható az egyes mezők értelmezésére mutató mutatókkal együtt.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* **AD importálási** rekord: Ez a naplórekord az AD-ből beolvasott fiók adatait jeleníti meg. Mivel a kezdeti felhasználólétrehozása során nincs AD-fiók, a *Tevékenység állapotának oka* azt jelzi, hogy az Active Directoryban nem található egy megfelelő azonosító attribútumértékű fiók. A naplórekord *További részletek* szakaszában található információk segítségével elháríthatja az adatok Workday-ből történő beolvasásával kapcsolatos problémákat. Az alábbiakban egy példarekord látható az egyes mezők értelmezésére mutató mutatókkal együtt.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Az AD importálási műveletnek megfelelő kiépítési ügynök naplórekordjainak megkereséséhez nyissa meg a Windows Eseménynapló naplóit, és használja a **Keresés...** menüpont a megfelelő azonosítót/csatlakozási tulajdonságattribútumot tartalmazó naplóbejegyzések megkereséséhez (ebben az esetben *21023).*

  ![Keresés](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Keresse meg a bejegyzés *eseményazonosító = 9*, amely megadja az LDAP keresési szűrő által használt ügynök letölteni az AD-fiók. Ellenőrizheti, hogy ez-e a megfelelő keresési szűrő az egyedi felhasználói bejegyzések beolvasásához.

  ![LDAP-keresés](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  Az *eseményazonosító = 2-vel* azonnal követő rekord rögzíti a keresési művelet eredményét, és ha eredményt adott vissza.

  ![LDAP-eredmények](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* **Szinkronizálási szabály műveletrekord:** Ez a naplórekord az attribútumleképezési szabályok és a konfigurált hatókörszűrők eredményeit, valamint a bejövő Workday-esemény feldolgozásához szükséges létesítési műveletet jeleníti meg. A naplórekord *További részletek* szakaszában található információk segítségével hárítsa el a szinkronizálási művelettel kapcsolatos problémákat. Az alábbiakban egy példarekord látható az egyes mezők értelmezésére mutató mutatókkal együtt.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Ha problémák merülnek fel az attribútumleképezési kifejezésekkel kapcsolatban, vagy a bejövő Workday-adatoknak problémái vannak (például: üres vagy null érték a szükséges attribútumokhoz), akkor ebben a szakaszban egy hibát fog észlelni az ErrorCode-mal, amely részletezi a hibát.

* **AD exportálási** rekord: Ez a naplórekord az AD-fiók létrehozásának eredményét, valamint a folyamatban beállított attribútumértékeket jeleníti meg. A naplórekord *További részletek* szakaszában található információk segítségével hárítsa el a fióklétrehozási művelettel kapcsolatos problémákat. Az alábbiakban egy példarekord látható az egyes mezők értelmezésére mutató mutatókkal együtt. A "További részletek" szakaszban az "EventName" értéke "EntryExportAdd", a "JoinProperty" értéke az egyező azonosító attribútum értéke, a "SourceAnchor" a rekordhoz társított WorkdayID (WID) értékre, a "TargetAnchor" pedig az újonnan létrehozott felhasználó AD "ObjectGuid" attribútumának értékére van állítva. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Az AD exportálási műveletnek megfelelő kiépítési ügynök naplórekordjainak megkereséséhez nyissa meg a Windows Eseménynapló naplóit, és használja a **Keresés...** menüpont a megfelelő azonosítót/csatlakozási tulajdonságattribútumot tartalmazó naplóbejegyzések megkereséséhez (ebben az esetben *21023).*  

  Keressen egy HTTP POST-rekordot, amely megfelel az exportálási művelet időbélyegének *az Event ID = 2*azonosítóval. Ez a rekord a kiépítési szolgáltatás által a kiépítési ügynöknek küldött attribútumértékeket fogja tartalmazni.

  [![SciM hozzáadása](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  Közvetlenül a fenti esemény után egy másik eseménynek kell lennie, amely rögzíti az AD-fiók létrehozása művelet válaszát. Ez az esemény az AD-ben létrehozott új objectGuid értéket adja vissza, és a kiépítési szolgáltatás TargetAnchor attribútumaként van beállítva.

  [![SciM hozzáadása](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>A kezelőfrissítési műveletek naplóinak ismertetése

A kezelőattribútum az AD hivatkozási attribútuma. A létesítési szolgáltatás nem állítja be a kezelő attribútumot a felhasználó létrehozási művelet részeként. A kezelői attribútum inkább egy *frissítési* művelet részeként van beállítva, miután a felhasználó számára létrehozta az AD-fiókot. A fenti példa kibontásával tegyük fel, hogy a "21451" alkalmazotti azonosítóval rendelkező új felvétel aktiválva van a Workday-ben, és az új alkalmazott kezelője (*21023*) már rendelkezik AD-fiókkal. Ebben az esetben a 21451-es felhasználó naplózási naplóiban 5 bejegyzés jelenik meg.

  [![Kezelőfrissítése](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

Az első 4 rekordok olyanok, mint az általunk feltárt részeként a felhasználó létrehozása művelet. Az ötödik rekord a kezelőattribútum frissítéséhez társított exportálás. A naplórekord az AD fiókkezelő frissítési műveletének eredményét jeleníti meg, amelyet a kezelő *objectGuid* attribútumával hajt végre.

  ```JSON
  // Modified Properties
  Name : manager
  New Value : "83f0156c-3222-407e-939c-56677831d525" // objectGuid of the user 21023

  // Additional Details
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportUpdate // Implies that object will be created
  JoiningProperty : 21451 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : 9603bf594b9901693f307815bf21870a // WorkdayID of the user
  TargetAnchor : 43b668e7-1d73-401c-a00a-fed14d31a1a8 // objectGuid of the user 21451

  ```

### <a name="resolving-commonly-encountered-errors"></a>Gyakran előforduló hibák megoldása

Ez a szakasz a Workday felhasználói kiépítésével gyakran előforduló hibákat ismerteti, és hogyan oldhatja meg azokat. A hibák csoportosítása a következő:

* [Ügynök kiépítési hibái](#provisioning-agent-errors)
* [Csatlakozási hibák](#connectivity-errors)
* [AD felhasználói fiók létrehozása hibák](#ad-user-account-creation-errors)
* [AD felhasználói fiók frissítési hibái](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Ügynök kiépítési hibái

|#|Hibaforgatókönyv |Valószínű okok|Ajánlott felbontás|
|--|---|---|---|
|1.| Hiba történt a kiépítési ügynök telepítésével a következő hibaüzenettel: *A "Microsoft Azure AD Connect provisioning agent" (AADConnectProvisioningAgent) szolgáltatás nem indult el. Ellenőrizze, hogy rendelkezik-e a rendszer elindításához szükséges jogosultságokkal.* | Ez a hiba általában akkor jelenik meg, ha a kiépítési ügynököt tartományvezérlőre próbálja telepíteni, és a csoportházirend megakadályozza a szolgáltatás indítását.  Az is látható, ha az ügynök egy korábbi verziója fut, és nem távolítja el az új telepítés megkezdése előtt.| Telepítse a kiépítési ügynököt egy nem tartományvezérlő-kiszolgálóra. Az új ügynök telepítése előtt győződjön meg arról, hogy az ügynök korábbi verzióit eltávolította.|
|2.| A "Microsoft Azure AD Connect provisioning agent" Windows-szolgáltatás indítási állapotban *van,* és nem vált *futó* állapotra. | A telepítés részeként az ügynök varázsló létrehoz egy helyi fiókot (**NT Service\\AADConnectProvisioningAgent**) a kiszolgálón, és ez a szolgáltatás indításához használt bejelentkezési fiók. Ha a Windows-kiszolgáló egyik biztonsági házirendje megakadályozza, hogy a helyi fiókok futnak a szolgáltatások, ez a hiba fog bekövetkezni. | Nyissa meg a *Szolgáltatások konzolt*. Kattintson a jobb gombbal a "Microsoft Azure AD Connect provisioning agent" Windows-szolgáltatásra, és a bejelentkezési lapon adja meg a szolgáltatás futtatásához a tartományi rendszergazda fiókját. Indítsa újra a szolgáltatást. |
|3.| Amikor a kiépítési ügynököt az Active *Directory csatlakoztatása*lépésben konfigurálja az AD-tartománnyal, a varázsló hosszú időt vesz igénybe az AD-séma betöltésének megkísérlése, és végül túllépi az időtúllépést. | Ez a hiba általában akkor jelentkezik, ha a varázsló tűzfalproblémák miatt nem tud csatlakozni az AD tartományvezérlői kiszolgálóhoz. | Az *Active Directory csatlakoztatása* varázsló képernyőjén, miközben megadja az AD-tartomány hitelesítő adatait, van egy *lehetőség,* amelynek neve Tartományvezérlő kiválasztása prioritás . Ezzel a beállítással olyan tartományvezérlőt választhat ki, amely ugyanazon a helyen található, mint az ügynökkiszolgáló, és győződjön meg arról, hogy nincsenek tűzfalszabályok, amelyek blokkolják a kommunikációt. |

#### <a name="connectivity-errors"></a>Csatlakozási hibák

Ha a létesítési szolgáltatás nem tud csatlakozni a Workday vagy az Active Directory, ez okozhat a kiépítés, hogy egy karanténba helyezett állapotba. Az alábbi táblázat segítségével elháríthatja a kapcsolódási problémákat.

|#|Hibaforgatókönyv |Valószínű okok|Ajánlott felbontás|
|--|---|---|---|
|1.| Ha a **Kapcsolat tesztelése**elemre kattint, a következő hibaüzenet jelenik meg: *Hiba történt az Active Directoryhoz való csatlakozáskor. Győződjön meg arról, hogy a helyszíni létesítő ügynök fut, és a megfelelő Active Directory-tartománnyal van konfigurálva.* | Ez a hiba általában akkor jelenik meg, ha a létesítő ügynök nem fut, vagy tűzfal blokkolja az Azure AD és a kiépítési ügynök közötti kommunikációt. Ez a hibaüzenet akkor is megjelenhet, ha a tartomány nincs konfigurálva az Ügynök varázslóban. | Nyissa meg a *Szolgáltatások* konzolt a Windows kiszolgálón, és ellenőrizze, hogy fut-e az ügynök. Nyissa meg a kiépítési ügynök varázslót, és ellenőrizze, hogy a megfelelő tartomány regisztrálva van-e az ügynöknél.  |
|2.| A létesítési feladat a hétvégén (P-Sat) karanténba kerül, és e-mailben értesítést kapunk arról, hogy hiba van a szinkronizálással. | A hiba egyik gyakori oka a Workday tervezett állásideje. Ha Ön Workday megvalósítási bérlőt használ, vegye figyelembe, hogy a Workday megvalósítási bérlőihez állásidő van ütemezve hétvégére (általában péntek estétől szombat reggelig), és ebben az időszakban a Workday kiépítési alkalmazások karanténba helyezett állapotba léphetnek, mivel nem tudnak csatlakozni a Workdayhez. Amint a Workday megvalósítási bérlője újra elérhetővé válik, visszaállnak normál állapotba. Ritka esetekben ez a hibaüzenet akkor is megjelenhet, ha az integrációs rendszer felhasználójának jelszava megváltozott a bérlő frissítése miatt, vagy ha a fiók zárolva lett, illetve lejárt. | Beszéljen Workday-rendszergazdájával vagy integrációs partnerével, hogy megtudja, mikorra van ütemezve a Workday állásideje, így figyelmen kívül hagyhatja a figyelmeztető üzeneteket a leállás ideje alatt, és meggyőződhet a rendelkezésre állásról, amint a Workday-példány újra elérhető.  |


#### <a name="ad-user-account-creation-errors"></a>AD felhasználói fiók létrehozása hibák

|#|Hibaforgatókönyv |Valószínű okok|Ajánlott felbontás|
|--|---|---|---|
|1.| Exportálási művelet hibák a naplóban a *következő üzenettel: OperationsError-SvcErr: Működési hiba történt. Nincs jobb hivatkozás konfigurálva a címtárszolgáltatáshoz. A címtárszolgáltatás ezért nem tud átirányítást kiadni az erdőn kívüli objektumokra.* | Ez a hiba általában akkor jelenik meg, ha az *Active Directory tárolószervezeti* egység nincs megfelelően beállítva, vagy ha problémák merülnek fel a *parentDistinguishedName*kifejezésleképezésével kapcsolatban. | Ellenőrizze az *Active Directory tárolószervezeti* egység paraméterében az elírásokat. Ha a *parentDistinguishedName* elemet használja az attribútumleképezésben, győződjön meg arról, hogy mindig egy AD-tartományon belüli ismert tárolóba adja vissza az értékeket. Ellenőrizze az *Exportálás* eseményt a naplóban a létrehozott érték megtekintéséhez. |
|2.| A napló ban a következő hibakóddal rendelkező exportálási művelethibák: *SystemForDomainIdentityManagementBadResponse* és message *Error: ConstraintViolation-AtrErr: A kérelem egyik értéke érvénytelen. Az attribútum értéke nem volt elfogadható értéktartományban. \nHiba Részletek: CONSTRAINT_ATT_TYPE - cég*. | Bár ez a hiba a *vállalati* attribútumra vonatkozik, ezt a hibát más attribútumok, például a *CN* is láthatja. Ez a hiba az AD által kényszerített sémamegkötés miatt jelenik meg. Alapértelmezés szerint a *vállalat* és a *KN* az AD-ben legfeljebb 64 karakterből áll. Ha a Workday értéke több mint 64 karakterből származik, akkor ez a hibaüzenet jelenik meg. | Ellenőrizze az *Exportálás* eseményt a naplóban, hogy megtekintse az attribútum nak a hibaüzenetben jelentett értékét. Fontolja meg a Workday függvényből érkező érték csonkolását a [Mid](../app-provisioning/functions-for-customizing-application-data.md#mid) függvénnyel, vagy módosítsa a hozzárendeléseket olyan AD attribútumra, amely nem rendelkezik hasonló hosszkorlátokkal.  |

#### <a name="ad-user-account-update-errors"></a>AD felhasználói fiók frissítési hibái

Az AD felhasználói fiók frissítési folyamata során a kiépítési szolgáltatás beolvassa az információkat mind a Workday, mind az AD-ből, futtatja az attribútumleképezési szabályokat, és meghatározza, hogy szükség van-e módosításokra. Ennek megfelelően egy frissítési esemény aktiválódik. Ha a lépések bármelyike hibát tapasztal, a rendszer naplózza a naplóba. Az alábbi táblázat segítségével elháríthatja a gyakori frissítési hibákat.

|#|Hibaforgatókönyv |Valószínű okok|Ajánlott felbontás|
|--|---|---|---|
|1.| A szinkronizálási szabály művelethibái a naplóban az *EventName = EntrySynchronizationError és ErrorCode = EndpointUnavailable*üzenettel. | Ez a hiba akkor jelenik meg, ha a létesítési szolgáltatás nem tudja lekérni a felhasználói profil adatait az Active Directoryból a helyszíni létesítő ügynök által tapasztalt feldolgozási hiba miatt. | Ellenőrizze a Kiépítési ügynök eseménynaplóiban az olvasási művelettel kapcsolatos problémákat jelző hibaeseményeket (#2 eseményazonosító szerinti szűrés). |
|2.| Az AD kezelői attribútuma nem frissül az AD bizonyos felhasználói számára. | A hiba legvalószínűbb oka az, ha hatókörszabályokat használ, és a felhasználó kezelője nem része a hatókörnek. A probléma akkor is előfordulhat, ha a kezelő megfelelő azonosítóattribútuma (pl. EmployeeID) nem található a cél AD tartományban, vagy nem a megfelelő értékre van beállítva. | Tekintse át a hatókörszűrőt, és adja hozzá a kezelő felhasználóját a hatókörhöz. Ellenőrizze a kezelő profilját az AD-ben, és győződjön meg arról, hogy van-e érték az egyező azonosító attribútumhoz. |

## <a name="managing-your-configuration"></a>A konfiguráció kezelése

Ez a szakasz azt ismerteti, hogyan bővítheti, szabhatja testre és kezelheti a Workday által vezérelt felhasználói kiépítési konfigurációt. A következő témákkal foglalkozik:

* [A Workday felhasználói attribútumainak testreszabása](#customizing-the-list-of-workday-user-attributes)  
* [Konfiguráció exportálása és importálása](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>A Workday felhasználói attribútumainak testreszabása

A Workday kiépítési alkalmazások az Active Directory és az Azure AD egyaránt tartalmaz egy alapértelmezett listát a Workday felhasználói attribútumok közül választhat. Ezek a listák azonban nem átfogóak. Workday támogatja a több száz lehetséges felhasználói attribútumok, amelyek lehetnek szabványos vagy egyedi a Workday bérlő.

Az Azure AD-kiépítési szolgáltatás támogatja a lista vagy a Workday attribútum testreszabását, hogy az emberi erőforrások API [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) működésében elérhetővé tett attribútumokat tartalmazza.

Ehhez a módosításhoz a [Workday Studio](https://community.workday.com/studio-download) használatával kell kinyernie az XPath-kifejezéseket, amelyek a használni kívánt attribútumokat jelölik, majd hozzá kell adnia őket a létesítési konfigurációhoz az Azure Portalon a speciális attribútumszerkesztő használatával.

**Egy Workday felhasználói attribútum XPath-kifejezésének beolvasása:**

1. Töltse le és telepítse [a Workday Studio alkalmazást.](https://community.workday.com/studio-download) A telepítő eléréséhez Workday közösségi fiókra lesz szükség.

2. Töltse le a Workday Human_Resources WSDL-fájlt erről az URL-címről:https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Indítsa el a Workday Studio-t.

4. A parancssávon válassza a **Workday > Test webszolgáltatás** tesztszolgáltatását.

5. Válassza **a Külső**lehetőséget, és válassza ki a Human_Resources 2.

    ![Munkanap Stúdió](./media/workday-inbound-tutorial/wdstudio1.png)

6. Állítsa **Location** a Hely `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`mezőt a , de az "IMPL-CC" szót a tényleges példánytípusra, a "BÉRLŐ" szót pedig a valódi bérlő nevére.

7. **A művelet** beállítása **Get_Workers**

8.  A Workday hitelesítő adatainak beállításához kattintson a Kérelem/válasz ablaktáblák alatti kis **konfigurálási** hivatkozásra. Jelölje be a **Hitelesítés**jelölőnégyzetet, majd adja meg a Workday-integrációs rendszerfiók felhasználónevét és jelszavát. Ügyeljen arra, hogy a\@felhasználónevet bérlőként formázza, és hagyja a **WS-Security UsernameToken** beállítást.

    ![Munkanap Stúdió](./media/workday-inbound-tutorial/wdstudio2.png)

9. Válassza **az OK gombot.**

10. A **Kérelem** ablaktáblán illessze be az alábbi XML-t, és állítsa **be a Employee_ID** a Workday-bérlő valós felhasználójának alkalmazotti azonosítójára. Válassza ki azt a felhasználót, akinek az attribútuma fel van töltve, amelyet ki szeretne bontani.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
          <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```

11. A **parancs** végrehajtásához kattintson a Kérelem küldése (zöld nyíl) gombra. Ha sikeres, a válasznak meg kell jelennie a **Válasz** ablaktáblán. Ellenőrizze a választ, hogy rendelkezik-e a megadott felhasználói azonosító adataival, és ne hibával.

12. Ha sikeres, másolja az XML-t a **Válasz** ablaktáblából, és mentse XML-fájlként.

13. A Workday Studio parancssávján jelölje be a **Fájl fájl > Fájl megnyitása...** és nyissa meg a mentett XML-fájlt. Ez a művelet megnyitja a fájlt a Workday Studio XML-szerkesztőjében.

    ![Munkanap Stúdió](./media/workday-inbound-tutorial/wdstudio3.png)

14. A fájlfában navigáljon a **/env: Envelope > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: Worker(Dolgozó)** között a felhasználó adatainak megkereséséhez.

15. A **wd: Worker**csoportban keresse meg a hozzáadni kívánt attribútumot, és jelölje ki.

16. Másolja a kijelölt attribútum XPath-kifejezését a **Dokumentum útvonala** mezőből.

17. Távolítsa el a **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** előtagot a másolt kifejezésből.

18. Ha a másolt kifejezés utolsó eleme csomópont (például: "/wd: Birth_Date"), akkor a kifejezés végén található **.text()** hozzáfűzés. Erre nincs szükség, ha az utolsó elem attribútum/@wd: (például" típus").

19. Az eredmény kell `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`valami ilyesmi . Ezt az értéket fogja másolni az Azure Portalon.

**Egyéni Workday felhasználói attribútum hozzáadása a létesítési konfigurációhoz:**

1. Indítsa el az [Azure Portalon,](https://portal.azure.com)és keresse meg a Workday kiépítési alkalmazás kiépítés szakaszát, az oktatóanyag korábbi részében leírtak szerint.

2. Állítsa **a Kiépítés állapotát** **Ki**állásra, és válassza a **Mentés**lehetőséget. Ez a lépés segít abban, hogy a módosítások csak akkor lépnek érvénybe, ha készen áll.

3. A **Leképezések**csoportban válassza **a Workday Workers szinkronizálása a helyszíni Active Directoryval** (vagy **a munkanap-dolgozók szinkronizálása az Azure AD-vel) lehetőséget.**

4. Görgessen a következő képernyő aljára, és válassza a **Speciális beállítások megjelenítése**lehetőséget.

5. Válassza **a Workday attribútumlistájának szerkesztése lehetőséget.**

    ![Munkanap Stúdió](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Görgessen az attribútumlista aljára a beviteli mezők höz.

7. A **Név**mezőbe írja be az attribútum megjelenítendő nevét.

8. A **Típus mezőben**válassza ki az attribútumnak megfelelően megfelelő típust (**A karakterlánc** a leggyakoribb).

9. Az **API Expression mezőbe**írja be a Workday Studio-ból másolt XPath-kifejezést. Például: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Válassza **az Attribútum hozzáadása lehetőséget.**

    ![Munkanap Stúdió](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Válassza **a** Mentés gombot, majd a párbeszédpanel **igen** beállítását. Zárja be az Attribútum-leképezés képernyőt, ha még nyitva van.

12. A fő **kiépítés** lapon válassza **a Workday Workers szinkronizálása a helyszíni Active Directoryval** (vagy **a Dolgozók szinkronizálása az Azure AD-vel)** lehetőséget.

13. Válassza **az Új leképezés hozzáadása**lehetőséget.

14. Az új attribútumnak most meg kell jelennie a **Forrás attribútumlistában.**

15. Igény szerint adjon hozzá egy leképezést az új attribútumhoz.

16. Ha elkészült, ne felejtse el visszaállítani **a kiépítési állapotot** **Be** és mentésre.

### <a name="exporting-and-importing-your-configuration"></a>Konfiguráció exportálása és importálása

Tekintse meg a [cikket A kiépítési konfiguráció exportálása és importálása](../app-provisioning/export-import-provisioning-configuration.md)

## <a name="managing-personal-data"></a>Személyes adatok kezelése

Az Active Directory Workday létesítési megoldása megköveteli, hogy egy kiépítési ügynök telepítve legyen egy helyszíni Windows-kiszolgálóra, és ez az ügynök naplókat hoz létre a Windows eseménynaplójában, amelyek személyes adatokat tartalmazhatnak a Workday és az AD attribútumleképezésektől függően. A felhasználói adatvédelmi kötelezettségek nek való megfelelés érdekében az eseménynaplóban 48 órán túl semmilyen adatot megőrizhet, ha beállít egy Windows-ra ütemezett feladatot az eseménynapló törléséhez.

Az Azure AD-kiépítési szolgáltatás a GDPR-besorolás **adatfeldolgozó** kategóriájába tartozik. Adatfeldolgozó folyamatként a szolgáltatás adatfeldolgozási szolgáltatásokat nyújt a legfontosabb partnereknek és a végfelhasználóknak. Az Azure AD-kiépítési szolgáltatás nem hoz létre felhasználói adatokat, és nincs független szabályozása a személyes adatok gyűjtésének és felhasználásának felett. Az Azure AD-kiépítési szolgáltatásban az adatok lekérése, összesítése, elemzése és jelentése meglévő vállalati adatokon alapul.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

Az adatmegőrzés tekintetében az Azure AD-kiépítési szolgáltatás nem hoz létre jelentéseket, elemzéseket, és 30 napon túl nem biztosít elemzéseket. Ezért az Azure AD-létesítési szolgáltatás nem tárolja, dolgozza fel, és nem tárolja az adatokat 30 napon belül. Ez a kialakítás megfelel a GDPR-szabályozásoknak, a Microsoft adatvédelmi megfelelőségi előírásainak és az Azure AD adatmegőrzési szabályzatainak.

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
* [Megtudhatja, hogy miként konfigurálható az egyszeri bejelentkezés a Workday és az Azure Active Directory között](workday-tutorial.md)
* [További információ arról, hogyan integrálhat más SaaS-alkalmazásokat az Azure Active Directoryval](tutorial-list.md)
* [Megtudhatja, hogy miként kezelheti a Kiépítési konfigurációkat a Microsoft Graph API-k használatával](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
