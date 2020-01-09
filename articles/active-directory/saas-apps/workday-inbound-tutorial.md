---
title: 'Oktatóanyag: munkanapok konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja a Azure Active Directoryt a felhasználói fiókok automatikus kiépítéséhez és üzembe helyezéséhez a munkanapokon.
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
ms.openlocfilehash: 94fc50bf238a74b7d8b45625d88b2d23d7dd1a13
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75613762"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Oktatóanyag: munkanapok konfigurálása a felhasználók automatikus kiépítési felállításához

Ennek az oktatóanyagnak a célja, hogy megmutassa a munkavégző profilok munkanapokból történő importálásához szükséges lépéseket a Active Directory és a Azure Active Directory között, az e-mail-cím és a Felhasználónév munkanapokon történő opcionális visszaírásával.

## <a name="overview"></a>Áttekintés

A felhasználói fiókok kiépítéséhez az [Azure Active Directory felhasználó-kiépítési szolgáltatás](../manage-apps/user-provisioning.md) integrálva van a [MUNKANAP emberi erőforrások API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) -val. Az Azure AD ezt a kapcsolódást használja a következő felhasználó-kiépítési munkafolyamatok engedélyezéséhez:

* A felhasználók kiépítésével **Active Directory** kiépíteni a kiválasztott felhasználók csoportját a munkanapokból egy vagy több Active Directory tartományba.

* **Csak a felhőben lévő felhasználók** kiépítésére Azure Active Directory – olyan helyzetekben, amikor a helyszíni Active Directory nem használatos, a felhasználók az Azure ad-alapú felhasználói létesítési szolgáltatás használatával közvetlenül a munkahelyről is üzembe helyezhetők Azure Active Directory.

* **E-mail-cím és Felhasználónév visszaírása munkanapokba** – az Azure ad-beli felhasználói kiépítési szolgáltatás az Azure ad-ből származó e-mail-címeket és felhasználóneveket visszaállíthatja munkanapokra.

### <a name="what-human-resources-scenarios-does-it-cover"></a>Milyen emberi erőforrás-forgatókönyveket takar?

Az Azure AD-alapú felhasználó-kiépítési szolgáltatás által támogatott munkafolyamatok munkafolyamatai lehetővé teszik a következő emberi erőforrások és az identitás-életciklus felügyeleti forgatókönyvek automatizálását:

* **Új alkalmazottak felvétele** – új alkalmazottak munkanapokhoz való hozzáadásakor a rendszer automatikusan létrehoz egy felhasználói fiókot Active Directory, Azure Active Directory és opcionálisan az Office 365-ben és az [Azure ad által támogatott egyéb SaaS-alkalmazásokban](../manage-apps/user-provisioning.md), és az e-mail-címet a munkanapokra írja vissza.

* **Alkalmazotti attribútumok és profilok frissítései** – ha egy alkalmazotti rekordot munkanapon frissítenek (például a nevük, a cím vagy a felettes), a felhasználói fiókja automatikusan frissül Active Directory, Azure Active Directory és opcionálisan az Office 365-ben és [Az Azure ad által támogatott egyéb SaaS-alkalmazásokban](../manage-apps/user-provisioning.md)is.

* **Alkalmazotti megszakítások** – ha egy alkalmazott munkanapokon leáll, a felhasználói fiókja automatikusan le van tiltva Active Directory, Azure Active Directory és opcionálisan az Office 365 és az [Azure ad által támogatott egyéb SaaS-alkalmazások](../manage-apps/user-provisioning.md)esetében.

* **Alkalmazottak** újratelepítése – ha egy alkalmazottat munkanapokon helyeztek át újra, a régi fiókjuk automatikusan újraaktiválható vagy újra kiépíthető (az Ön igényeitől függően), hogy Active Directory, Azure Active Directory és opcionálisan az Office 365-et és [Az Azure ad által támogatott egyéb SaaS-alkalmazásokat](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Ki ez a felhasználó-kiépítési megoldás a legmegfelelőbb?

Ez a munkanap felhasználói kiépítési megoldás ideális a következőkhöz:

* Olyan szervezetek, amelyek előre elkészített, felhőalapú megoldást kívánnak a munkanapokon történő felhasználói kiépítéshez

* Azok a szervezetek, amelyekhez közvetlen felhasználó-kiépítés szükséges a munkanapokból a Active Directoryba vagy Azure Active Directory

* Azok a szervezetek, amelyekhez a felhasználók számára a munkanap HCM-modulból beszerzett adatok használatával kell kiépíteni a felhasználókat (lásd: [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Azokat a szervezeteket, amelyeknek a felhasználókhoz való csatlakozást, áthelyezést és ellépést kell elhagynia egy vagy több Active Directory-erdőben, tartományon és szervezeti egységen, csak a munkanap HCM- [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)modulban észlelt változási információk alapján

* Office 365-t használó szervezetek e-mailben

## <a name="solution-architecture"></a>Megoldásarchitektúra

Ez a szakasz a közös hibrid környezetek teljes körű felhasználói üzembe helyezési megoldásának architektúráját ismerteti. Két kapcsolódó folyamat létezik:

* **Mérvadó HR-adatfolyam – a munkanap és a helyszíni Active Directory között:** Ebben a folyamatban lévő munkavégző esemény (például új bérletek, átvitelek, megszakítások) először a Felhőbeli munkanap HR-bérlőben fordul elő, majd az esemény az Azure AD-n és a kiépítési ügynökön keresztül a helyszíni Active Directoryba áramlik. Az eseménytől függően előfordulhat, hogy az AD-ben létrehoz/frissít/engedélyez/letilt műveleteket.
* **E-mail-cím és Felhasználónév visszaírási folyamat – helyszíni Active Directoryról munkanapokra:** Ha a fiók létrehozása befejeződött a Active Directoryban, az Azure AD-vel való szinkronizálása a Azure AD Connect és az e-mailek és a username attribútum használatával is visszaírható a munkanapokba.

![Áttekintés](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Végpontok közötti felhasználói adatfolyam

1. A HR-csapat munkavégző tranzakciókat (összekötőket/Mozgatókat/kimaradt vagy új bérleteket/átviteleket/megszakításokat) végez a munkanap HCM-ben.
2. Az Azure AD kiépítési szolgáltatás a munkanap HR-ből futtatja az identitások ütemezett szinkronizálását, és azonosítja azokat a módosításokat, amelyeket a helyszíni Active Directorysal való szinkronizáláshoz kell feldolgozni.
3. Az Azure AD-kiépítési szolgáltatás meghívja a helyszíni Azure AD Connect kiépítési ügynököt az AD-fiók létrehozási/frissítési/engedélyezési/letiltási műveleteit tartalmazó kérelem-adattartalommal.
4. Az Azure AD Connect-kiépítési ügynök egy szolgáltatásfiókot használ az AD-fiókadatok hozzáadásához/frissítéséhez.
5. A Azure AD Connect/AD-szinkronizáló motor különbözeti szinkronizálást futtat a frissítések AD-ben való lekéréséhez.
6. A Active Directory frissítések szinkronizálva vannak a Azure Active Directoryval.
7. Ha a munkanap visszaírási-összekötő konfigurálva van, az e-mail-attribútumot és a felhasználónevet a munkanap értékre írja, a használt attribútum alapján.

## <a name="planning-your-deployment"></a>Az üzembe helyezés megtervezése

A munkanap-integráció megkezdése előtt tekintse át az alábbi előfeltételeket, és olvassa el az alábbi útmutatást arról, hogyan egyeznek meg a jelenlegi Active Directory architektúrával és a felhasználói kiépítési követelményekkel a Azure Active Directory által biztosított megoldás (ok) val. A tervezési munkalapokkal átfogó [üzembe helyezési terv](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) is rendelkezésre áll, amely segítséget nyújt a munkanap-integrációs partner és a HR-résztvevő együttműködéséhez.

Ez a szakasz a tervezés következő szempontjait ismerteti:

* [Előfeltételek](#prerequisites)
* [Üzembe helyezési összekötő-alkalmazások kiválasztásának kiválasztása](#selecting-provisioning-connector-apps-to-deploy)
* [Azure AD Connect kiépítési ügynök központi telepítésének megtervezése](#planning-deployment-of-azure-ad-connect-provisioning-agent)
* [Integráció több Active Directory tartománnyal](#integrating-with-multiple-active-directory-domains)
* [Munkanapok tervezése a felhasználói attribútumok leképezésének és átalakításának Active Directory](#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)

### <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

* Érvényes prémium szintű Azure AD P1 vagy magasabb szintű előfizetési licenc minden olyan felhasználó számára, aki a munkanapokból származik, és amelyet a helyszíni Active Directoryba vagy Azure Active Directoryba kell kiépíteni.
* Azure AD globális rendszergazdai hozzáférés a kiépítési ügynök konfigurálásához
* Egy munkanap-implementációs bérlő tesztelési és integrációs célokra
* Rendszergazdai engedélyek a munkanapokon rendszerintegrációs felhasználó létrehozásához, valamint a tesztelési célú alkalmazottakra vonatkozó adatellenőrzések módosítása
* A felhasználók Active Directory való kiépítéséhez a .NET 4.7.1 + Runtime használatával a Windows Server 2012 vagy újabb rendszert futtató kiszolgáló szükséges a helyszíni [kiépítési ügynök](https://go.microsoft.com/fwlink/?linkid=847801) üzemeltetéséhez.
* [Azure ad Connect](../hybrid/whatis-hybrid-identity.md) a felhasználók Active Directory és az Azure ad közötti szinkronizálásához

### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Üzembe helyezési összekötő-alkalmazások kiválasztásának kiválasztása

A munkafolyamatok munkanap és Active Directory közötti kiépítés megkönnyítéséhez az Azure AD több üzembe helyezési összekötő alkalmazást is biztosít, amelyeket hozzáadhat az Azure AD-alkalmazás-katalógusból:

![Azure AD alkalmazás gyűjtemény](./media/workday-inbound-tutorial/wd_gallery.png)

* **Munkanapokat Active Directory a felhasználók üzembe** helyezéséhez – ez az alkalmazás megkönnyíti a felhasználói fiókok kiépítési idejét egyetlen Active Directory tartományba. Ha több tartománya van, az alkalmazás egy példányát hozzáadhatja az Azure AD-alkalmazás-katalógusból minden Active Directory-tartományhoz, amelyre telepítenie kell.

* **Munkanapokat az Azure ad** -felhasználók üzembe helyezéséhez – míg a Azure ad Connect az az eszköz, amellyel a Azure Active Directory Active Directory felhasználókat szinkronizálni kell, ez az alkalmazás a Felhőbeli felhasználók munkanapokból egyetlen Azure Active Directory bérlővé való kiépítés megkönnyítésére használható.

* **Munkanap visszaírási** – ez az alkalmazás lehetővé teszi a felhasználó e-mail-címeinek visszaírását Azure Active Directoryról munkanapokra.

> [!TIP]
> A normál "munkanap" alkalmazás a munkanapok és a Azure Active Directory közötti egyszeri bejelentkezés beállítására szolgál.

Az alábbi döntési folyamatábra segítségével azonosíthatja, hogy mely munkanapokat kell kiépíteni az adott forgatókönyvhöz.
    ![Döntési folyamatábra](./media/workday-inbound-tutorial/wday_app_flowchart.png "Döntési folyamatábra")

A tartalomjegyzék használatával lépjen az oktatóanyag vonatkozó szakaszára.

### <a name="planning-deployment-of-azure-ad-connect-provisioning-agent"></a>Azure AD Connect kiépítési ügynök központi telepítésének megtervezése

> [!NOTE]
> Ez a szakasz csak akkor érvényes, ha azt tervezi, hogy üzembe helyezi a munkanapokat Active Directory a felhasználó kiépítési alkalmazásához. Ezt kihagyhatja, ha a munkanap visszaírási vagy munkanapon üzembe helyezi az Azure AD-felhasználó kiépítési alkalmazását.

Az AD-felhasználó kiépítési megoldásához szükséges munkanapokon legalább 4 GB RAM-mal és .NET 4.7.1 + futtatókörnyezettel kell telepíteni egy vagy több kiépítési ügynököt a Windows 2012 R2 vagy újabb rendszert futtató kiszolgálókra. A kiépítési ügynök telepítése előtt a következő szempontokat kell figyelembe venni:

* Győződjön meg arról, hogy a kiépítési ügynököt futtató gazdagépnek van hálózati hozzáférése a cél AD-tartományhoz
* A kiépítési ügynök konfigurációs varázslója regisztrálja az ügynököt az Azure AD-Bérlővel, és a regisztrációs folyamathoz hozzáférés szükséges a *. msappproxy.net az SSL 443-es porton keresztül. Győződjön meg arról, hogy a kimenő tűzfalszabályok vannak érvényben, amelyek lehetővé teszik a kommunikációt. Az ügynök támogatja a [kimenő HTTPS-proxy konfigurációját](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).
* A kiépítési ügynök egy szolgáltatásfiók használatával kommunikál a helyszíni AD-tartomány (ok) val. Az ügynök telepítése előtt javasoljuk, hogy hozzon létre egy tartományi rendszergazdai engedélyekkel rendelkező szolgáltatásfiókot, és egy jelszót, amely nem jár le.  
* A kiépítési ügynök konfigurálása során kiválaszthatja azokat a tartományvezérlőket, amelyeknek kezelniük kell a kiépítési kérelmeket. Ha több földrajzilag elosztott tartományvezérlővel rendelkezik, telepítse a kiépítési ügynököt ugyanazon a helyen, mint az elsődleges tartományvezérlő (k) a teljes körű megoldás megbízhatóságának és teljesítményének javítása érdekében.
* A magas rendelkezésre állás érdekében több kiépítési ügynököt is telepíthet, és regisztrálhatja a helyszíni AD-tartományok ugyanazon készletének kezelésére.

> [!IMPORTANT]
> Éles környezetekben a Microsoft azt javasolja, hogy a magas rendelkezésre állás érdekében legalább 3 kiépítési ügynök legyen konfigurálva az Azure AD-Bérlővel.

### <a name="integrating-with-multiple-active-directory-domains"></a>Integráció több Active Directory tartománnyal

> [!NOTE]
> Ez a szakasz csak akkor érvényes, ha azt tervezi, hogy üzembe helyezi a munkanapokat Active Directory a felhasználó kiépítési alkalmazásához. Ezt kihagyhatja, ha a munkanap visszaírási vagy munkanapon üzembe helyezi az Azure AD-felhasználó kiépítési alkalmazását.

A Active Directory-topológiától függően meg kell határoznia a felhasználók üzembe helyezési összekötő alkalmazásainak számát és a konfigurálni kívánt kiépítési ügynökök számát. Az alábbiakban felsorolunk néhány olyan általános telepítési mintát, amelyet az üzembe helyezés megtervezésekor használhat.

#### <a name="deployment-scenario-1--single-workday-tenant---single-ad-domain"></a>Üzembe helyezési forgatókönyv #1: egymunkanapos bérlő – > egyetlen AD-tartomány

Ebben a forgatókönyvben egy munkanap Bérlővel rendelkezik, és a felhasználókat egyetlen célzott AD-tartományba szeretné kiépíteni. Itt látható az üzemelő példányhoz tartozó ajánlott éles konfiguráció.

|   |   |
| - | - |
| Nem. helyszíni üzembe helyezési ügynökök | 3 (a magas rendelkezésre álláshoz és a feladatátvételhez) |
| Nem. a munkanapokon az AD felhasználói kiépítési alkalmazások konfigurálásához Azure Portal | 1 |

  ![1\. eset](./media/workday-inbound-tutorial/dep_scenario1.png)

#### <a name="deployment-scenario-2--single-workday-tenant---multiple-child-ad-domains"></a>Üzembe helyezési forgatókönyv #2: egyetlen munkanap bérlő – > több alárendelt AD-tartomány

Ez a forgatókönyv azt jelenti, hogy a felhasználók a munkanapokból több célzott AD-gyermektartomány számára is üzembe helyezhetők egy erdőben. Itt látható az üzemelő példányhoz tartozó ajánlott éles konfiguráció.

|   |   |
| - | - |
| Nem. helyszíni üzembe helyezési ügynökök | 3 (a magas rendelkezésre álláshoz és a feladatátvételhez) |
| Nem. a munkanapokon az AD felhasználói kiépítési alkalmazások konfigurálásához Azure Portal | egy alkalmazás/gyermektartomány |

  ![2\. forgatókönyv](./media/workday-inbound-tutorial/dep_scenario2.png)

#### <a name="deployment-scenario-3--single-workday-tenant---disjoint-ad-forests"></a>Üzembe helyezési forgatókönyv #3: egyetlen munkanap bérlője – > különálló AD-erdők

Ez a forgatókönyv magában foglalja a felhasználók munkanapokból való kivonását a különálló AD-erdőkben lévő tartományokra. Itt látható az üzemelő példányhoz tartozó ajánlott éles konfiguráció.

|   |   |
| - | - |
| Nem. helyszíni üzembe helyezési ügynökök | 3/különálló AD-erdő |
| Nem. a munkanapokon az AD felhasználói kiépítési alkalmazások konfigurálásához Azure Portal | egy alkalmazás/gyermektartomány |

  ![3\. forgatókönyv](./media/workday-inbound-tutorial/dep_scenario3.png)

### <a name="planning-workday-to-active-directory-user-attribute-mapping-and-transformations"></a>Munkanapok tervezése a felhasználói attribútumok leképezésének és átalakításának Active Directory

> [!NOTE]
> Ez a szakasz csak akkor érvényes, ha azt tervezi, hogy üzembe helyezi a munkanapokat Active Directory a felhasználó kiépítési alkalmazásához. Ezt kihagyhatja, ha a munkanap visszaírási vagy munkanapon üzembe helyezi az Azure AD-felhasználó kiépítési alkalmazását.

Mielőtt a felhasználó üzembe helyezését egy Active Directory tartományra konfigurálja, vegye figyelembe az alábbi kérdéseket. Az ezekre a kérdésekre adott válaszok határozzák meg, hogyan kell beállítani a hatókörhöz tartozó szűrőket és attribútum-hozzárendeléseket.

* **A munkanapokat milyen felhasználóknak kell kiépíteni a Active Directory erdőben?**

  * *Példa: azok a felhasználók, amelyekben a "vállalati" attribútum "contoso" értéket tartalmaz, a "Worker_Type" attribútum pedig "normál" értéket tartalmaz.*

* **Hogyan irányíthatók a felhasználók a különböző szervezeti egységekre (OU-ket)?**

  * *Példa: a felhasználók olyan szervezeti egységekhez vannak irányítva, amelyek egy Office-helynek felelnek meg a munkanap "helyhatóság" és "Country_Region_Reference" attribútumaiban meghatározottak szerint.*

* **Hogyan legyenek feltöltve a következő attribútumok a Active Directory?**

  * Köznapi név (CN)
    * *Példa: használja a munkanap User_ID értéket, amelyet emberi erőforrások állítanak be*

  * Alkalmazott azonosítója (Alkalmazottkód)
    * *Példa: a munkanap Worker_ID értékének használata*

  * SAM-fiók neve (sAMAccountName)
    * *Példa: a munkanap User_ID értékének használata az Azure AD-létesítési kifejezéssel szűrve az érvénytelen karakterek eltávolításához*

  * Egyszerű felhasználónév (userPrincipalName)
    * *Példa: használja a munkanap User_ID értéket egy Azure AD kiépítési kifejezéssel a tartománynév hozzáfűzéséhez*

* **Hogyan kell egyeztetni a felhasználókat a munkanap és a Active Directory között?**

  * *Példa: egy adott munkanap "Worker_ID" értékkel rendelkező felhasználók Active Directory felhasználókkal egyeznek meg, ahol az "Alkalmazottkód" értéke megegyezik. Ha a Worker_ID érték nem található a Active Directoryban, akkor hozzon létre egy új felhasználót.*
  
* **A Active Directory erdő már tartalmazza a megfelelő logikai működéshez szükséges felhasználói azonosítókat?**

  * *Példa: Ha ez a beállítás egy új munkanapokon üzemelő példány, ajánlott előre kitölteni Active Directory a megfelelő munkanapokat Worker_ID értékekkel (vagy egyedi azonosító értékkel), hogy a lehető legegyszerűbb legyen a megfelelő logika.*

A speciális kiépítési összekötő-alkalmazások beállítása és konfigurálása az oktatóanyag hátralévő fejezeteinek tárgya. A konfigurálni kívánt alkalmazások attól függnek, hogy mely rendszerekre van szükség, és hogy hány Active Directory tartomány és Azure AD-bérlő van a környezetében.

## <a name="configure-integration-system-user-in-workday"></a>Integrációs rendszer felhasználójának konfigurálása munkanapokon

Az összes munkanap-létesítési összekötő általános követelménye, hogy egy munkanap-integrációs rendszer felhasználójának hitelesítő adatait igénylik a munkanap emberi erőforrások API-hoz való csatlakozáshoz. Ez a szakasz azt ismerteti, hogyan lehet integrációs rendszerbeli felhasználót létrehozni a munkanapokon, és a következő szakaszokból áll:

* [Integrációs rendszer felhasználójának létrehozása](#creating-an-integration-system-user)
* [Integrációs biztonsági csoport létrehozása](#creating-an-integration-security-group)
* [Tartományi biztonsági házirend engedélyeinek konfigurálása](#configuring-domain-security-policy-permissions)
* [Üzleti folyamatokra vonatkozó biztonsági házirend engedélyeinek konfigurálása](#configuring-business-process-security-policy-permissions)
* [A biztonsági szabályzat módosításainak aktiválása](#activating-security-policy-changes)

> [!NOTE]
> Ezt az eljárást figyelmen kívül hagyhatja, és ehelyett egy munkanapon globális rendszergazdai fiókot kell használnia rendszerintegrációs fiókként. Ez kiválóan használható a demók számára, de nem ajánlott éles környezetekben üzemelő példányokhoz.

### <a name="creating-an-integration-system-user"></a>Integrációs rendszer felhasználójának létrehozása

**Integrációs rendszer felhasználójának létrehozása:**

1. Jelentkezzen be a munkanap bérlőbe egy rendszergazdai fiók használatával. A **munkanap alkalmazásban**írja be a felhasználó létrehozása kifejezést a keresőmezőbe, majd kattintson az **integrációs rendszer felhasználójának létrehozása**elemre.

   ![Felhasználó létrehozása](./media/workday-inbound-tutorial/wd_isu_01.png "Felhasználó létrehozása")
2. Fejezze be az **integrációs rendszer felhasználói feladat létrehozása** nevű felhasználót egy új integrációs rendszer felhasználójának felhasználónevével és jelszavával.  
  
   * Ha nem jelölte be az **új jelszó megkövetelése** jelölőnégyzetet, akkor a következő bejelentkezéskor nem kell bejelentkeznie, mert ez a felhasználó programozott módon lesz bejelentkezve.
   * Hagyja meg a **munkamenet időkorlátját percben** a 0 alapértelmezett értékkel, ami megakadályozza, hogy a felhasználó munkamenete idő előtt ne legyen időtúllépés.
   * Válassza a **felhasználói felületi munkamenetek tiltása** lehetőséget, mivel ez egy hozzáadott biztonsági réteget biztosít, amely megakadályozza, hogy a felhasználó az integrációs rendszer jelszavával ne jelentkezzen be munkanapokba.

   ![Integrációs rendszer felhasználójának létrehozása](./media/workday-inbound-tutorial/wd_isu_02.png "Integrációs rendszer felhasználójának létrehozása")

### <a name="creating-an-integration-security-group"></a>Integrációs biztonsági csoport létrehozása

Ebben a lépésben létrehoz egy nem korlátozott vagy korlátozott integrációs rendszer biztonsági csoportot a munkanapokon, és hozzárendeli az előző lépésben létrehozott integrációs rendszer-felhasználót ehhez a csoporthoz.

**Biztonsági csoport létrehozása:**

1. A keresőmezőbe írja be a biztonsági csoport létrehozása kifejezést, majd kattintson a **biztonsági csoport létrehozása**elemre.

    ![CreateSecurity csoport](./media/workday-inbound-tutorial/wd_isu_03.png "CreateSecurity csoport")
2. Fejezze be a **biztonsági csoport létrehozása** feladatot. 

   * A munkanapokon két típusú biztonsági csoport létezik:
     * Nem **korlátozott:** A biztonsági csoport összes tagja hozzáférhet a biztonsági csoport által védett összes adatpéldányhoz.
     * **Korlátozott:** Az összes biztonsági csoport tagjai környezetfüggő hozzáféréssel rendelkeznek a biztonsági csoport által elérhető adatpéldányok (sorok) egy részhalmazához.
   * A megfelelő biztonsági csoport típusának kiválasztásához válassza ki a munkahelye integrációs partnerét az integrációhoz.
   * Ha ismeri a csoport típusát, válassza az **integrációs rendszer biztonsági csoport (nem korlátozott)** vagy az **integrációs rendszer biztonsági csoport (korlátozott)** lehetőséget a **bérlői biztonsági csoport** legördülő listából.

     ![CreateSecurity csoport](./media/workday-inbound-tutorial/wd_isu_04.png "CreateSecurity csoport")

3. Ha a biztonsági csoport létrehozása sikeres volt, egy oldal jelenik meg, ahol tagokat rendelhet a biztonsági csoporthoz. Adja hozzá az előző lépésben létrehozott új integrációs rendszer felhasználót ehhez a biztonsági csoporthoz. Ha *korlátozott* biztonsági csoportot használ, ki kell választania a megfelelő szervezeti hatókört is.

    ![Biztonsági csoport szerkesztése](./media/workday-inbound-tutorial/wd_isu_05.png "Biztonsági csoport szerkesztése")

### <a name="configuring-domain-security-policy-permissions"></a>Tartományi biztonsági házirend engedélyeinek konfigurálása

Ebben a lépésben "tartományi biztonsági" házirend-engedélyeket ad a munkavégző adatnak a biztonsági csoport számára.

**Tartományi biztonsági házirend engedélyeinek konfigurálása:**

1. A keresőmezőbe írja be a **tartomány biztonsági konfigurációját** , majd kattintson a **tartomány biztonsági konfigurációs jelentés**hivatkozására.  

    ![Tartományi biztonsági házirendek](./media/workday-inbound-tutorial/wd_isu_06.png "Tartományi biztonsági házirendek")  
2. A **tartomány** szövegmezőben keresse meg a következő tartományokat, és adja hozzá őket a szűrőhöz eggyel.  
   * *Külső fiók kiépítés*
   * *Worker-jelentések: Public Worker-jelentések*
   * *Személyes adatok: munkahelyi kapcsolattartási adatok*
   * *Feldolgozói adatfeldolgozás: összes pozíció*
   * *Munkavégző adatok: aktuális személyzeti információ*
   * *Worker-adatfeldolgozók: a munkavégző profilban szereplő üzleti cím*
   * *Munkanap-fiókok*
   
     ![Tartományi biztonsági házirendek](./media/workday-inbound-tutorial/wd_isu_07.png "Tartományi biztonsági házirendek")  

     ![Tartományi biztonsági házirendek](./media/workday-inbound-tutorial/wd_isu_08.png "Tartományi biztonsági házirendek") 

     Kattintson az **OK** gombra.

3. A megjelenített jelentésben válassza ki a **külső fiók kiépítés** mellett megjelenő három pontot (...), majd kattintson a **tartomány – > biztonsági házirend módosítása engedélyekre** .

    ![Tartományi biztonsági házirendek](./media/workday-inbound-tutorial/wd_isu_09.png "Tartományi biztonsági házirendek")  

4. A **tartomány biztonsági házirendjének szerkesztése** lapon görgessen le az **integrációs engedélyek**szakaszhoz. A "+" jelre kattintva adja hozzá az integrációs rendszer csoportot a biztonsági csoportok listájához a **Get** és a **put** integrációs engedélyekkel.

    ![Szerkesztési engedély](./media/workday-inbound-tutorial/wd_isu_10.png "Szerkesztési engedély")  

5. A "+" jelre kattintva adja hozzá az integrációs rendszer csoportot a biztonsági csoportok listájához a **Get** és a **put** integrációs engedélyekkel.

    ![Szerkesztési engedély](./media/workday-inbound-tutorial/wd_isu_11.png "Szerkesztési engedély")  

6. Ismételje meg a fenti 3-5. lépést a fennmaradó biztonsági házirendek mindegyikéhez:

   | Művelet | Tartományi biztonsági házirend |
   | ---------- | ---------- |
   | Beolvasás és üzembe helyezés | Worker-jelentések: Public Worker-jelentések |
   | Beolvasás és üzembe helyezés | Személyes adatok: munkahelyi kapcsolattartási adatok |
   | Beszerzés | Feldolgozói adatfeldolgozás: összes pozíció |
   | Beszerzés | Munkavégző adatok: aktuális személyzeti információ |
   | Beszerzés | Worker-adatfeldolgozók: a munkavégző profilban szereplő üzleti cím |
   | Beolvasás és üzembe helyezés | Munkanap-fiókok |

### <a name="configuring-business-process-security-policy-permissions"></a>Üzleti folyamatokra vonatkozó biztonsági házirend engedélyeinek konfigurálása

Ebben a lépésben az "üzleti folyamatok biztonsága" házirend engedélyeit fogja megadni a munkavégző adat számára a biztonsági csoport számára. Ez a lépés szükséges a munkanap visszaírási alkalmazás-összekötő beállításához.

**Üzleti folyamatokra vonatkozó biztonsági házirend engedélyeinek konfigurálása:**

1. A keresőmezőbe írja be az **üzleti folyamat házirendjét** , majd kattintson az **üzleti folyamat biztonsági házirendjének szerkesztése** feladat hivatkozásra.  

    ![Üzleti folyamatok biztonsági házirendjei](./media/workday-inbound-tutorial/wd_isu_12.png "Üzleti folyamatok biztonsági házirendjei")  

2. Az **üzleti folyamat típusa** szövegmezőben keressen a *Contact* (kapcsolattartó) elemre, és válassza az üzleti folyamat **megváltozása** lehetőséget, majd kattintson **az OK**gombra.

    ![Üzleti folyamatok biztonsági házirendjei](./media/workday-inbound-tutorial/wd_isu_13.png "Üzleti folyamatok biztonsági házirendjei")  

3. Az **üzleti folyamat biztonsági házirendjének szerkesztése** lapon görgessen a **kapcsolattartási adatok karbantartása (webszolgáltatás)** szakaszhoz.

    ![Üzleti folyamatok biztonsági házirendjei](./media/workday-inbound-tutorial/wd_isu_14.png "Üzleti folyamatok biztonsági házirendjei")  

4. Válassza ki és adja hozzá az új integrációs rendszer biztonsági csoportját azon biztonsági csoportok listájához, amelyek elindíthatják a webszolgáltatások kérelmét. Kattintson a **kész**gombra. 

    ![Üzleti folyamatok biztonsági házirendjei](./media/workday-inbound-tutorial/wd_isu_15.png "Üzleti folyamatok biztonsági házirendjei")  

### <a name="activating-security-policy-changes"></a>A biztonsági szabályzat módosításainak aktiválása

**A biztonsági szabályzat módosításainak aktiválása:**

1. A keresőmezőbe írja be az aktiválás kifejezést, majd kattintson a **függőben lévő biztonsági házirend módosításainak aktiválása**hivatkozásra.

    ![Aktiválás](./media/workday-inbound-tutorial/wd_isu_16.png "Aktiválás")

1. A függőben lévő biztonsági házirend módosítására vonatkozó feladat megkezdéséhez adjon meg egy hozzászólást a naplózási célokhoz, majd kattintson **az OK**gombra.
1. A következő képernyőn hajtsa végre a feladatot a jelölőnégyzet **bejelölésével**, majd kattintson az **OK**gombra.

    ![A függőben lévő biztonság aktiválása](./media/workday-inbound-tutorial/wd_isu_18.png "A függőben lévő biztonság aktiválása")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>A felhasználó üzembe helyezésének beállítása munkahelyről Active Directory

Ez a szakasz a felhasználói fiókok kiépítésének lépéseit ismerteti a munkanapokról az egyes Active Directory tartományokra az integráció hatókörén belül.

* [Adja hozzá a kiépítési összekötő alkalmazást, és töltse le a kiépítési ügynököt.](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Helyszíni kiépítési ügynök (ek) telepítése és konfigurálása](#part-2-install-and-configure-on-premises-provisioning-agents)
* [A munkanapokhoz és Active Directoryhoz való kapcsolódás konfigurálása](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)
* [Attribútumok leképezésének konfigurálása](#part-4-configure-attribute-mappings)
* [A felhasználók üzembe helyezésének engedélyezése és elindítása](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>1\. rész: a kiépítési összekötő alkalmazás hozzáadása és a kiépítési ügynök letöltése

**Munkanapok konfigurálása Active Directory kiépítés megkezdéséhez:**

1. Nyissa meg a következőt: <https://portal.azure.com>.

2. A Azure Portal keresse meg és válassza a **Azure Active Directory**lehetőséget.

3. Válassza a **vállalati alkalmazások**, majd **az összes alkalmazás**lehetőséget.

4. Válassza az **alkalmazás hozzáadása**lehetőséget, és válassza az **összes** kategóriát.

5. Keresse meg a **munkanapokon való kiépítés Active Directoryét**, és adja hozzá az alkalmazást a katalógusból.

6. Miután hozzáadta az alkalmazást, és megjelenik az alkalmazás részletei képernyő, válassza a **kiépítés**lehetőséget.

7. Módosítsa a **kiépítési** **módot** **automatikusra**.

8. A kiépítési ügynök letöltéséhez kattintson a megjelenő információs szalagcímre. 

   ![Ügynök letöltése](./media/workday-inbound-tutorial/pa-download-agent.png "Ügynök letöltése képernyő")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>2\. rész: a helyszíni kiépítési ügynök (ek) telepítése és konfigurálása

A helyszíni Active Directory létrehozásához a kiépítési ügynököt olyan kiszolgálóra kell telepíteni, amely .NET 4.7.1 + keretrendszert és hálózati hozzáférést biztosít a kívánt Active Directory tartomány (ok) hoz.

> [!TIP]
> A .NET-keretrendszer verzióját a kiszolgálón tekintheti meg az [itt](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)megadott utasítások alapján.
> Ha a kiszolgáló nem rendelkezik .NET 4.7.1 vagy újabb verzióval, letöltheti [innen.](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows)  

Vigye át a letöltött ügynök telepítőjét a kiszolgálói gazdagépre, és kövesse az alábbi lépéseket az ügynök konfigurációjának befejezéséhez.

1. Jelentkezzen be arra a Windows Serverre, amelyre telepíteni kívánja az új ügynököt.

1. Indítsa el a kiépítési ügynök telepítőjét, fogadja el a feltételeket, majd kattintson a **telepítés** gombra.

   ![Telepítési képernyő](./media/workday-inbound-tutorial/pa_install_screen_1.png "Telepítési képernyő")
   
1. A telepítés befejezése után elindul a varázsló, és megjelenik az **Azure ad** -hez való csatlakozási képernyő. Az Azure AD-példányhoz való kapcsolódáshoz kattintson a **hitelesítés** gombra.

   ![Az Azure AD összekötése](./media/workday-inbound-tutorial/pa_install_screen_2.png "Az Azure AD csatlakoztatása")
   
1. A globális rendszergazdai hitelesítő adatok használatával hitelesítse magát az Azure AD-példányon.

   ![Rendszergazdai hitelesítés](./media/workday-inbound-tutorial/pa_install_screen_3.png "Rendszergazdai hitelesítés")

   > [!NOTE]
   > Az Azure AD rendszergazdai hitelesítő adatai csak az Azure AD-bérlőhöz való kapcsolódáshoz használatosak. Az ügynök nem tárolja helyileg a hitelesítő adatokat a kiszolgálón.

1. Az Azure AD-vel való sikeres hitelesítés után megjelenik a **csatlakozási Active Directory** képernyő. Ebben a lépésben adja meg az AD-tartománynevet, és kattintson a **könyvtár hozzáadása** gombra.

   ![Könyvtár hozzáadása](./media/workday-inbound-tutorial/pa_install_screen_4.png "Könyvtár hozzáadása")
  
1. Ekkor a rendszer felszólítja az AD-tartományhoz való csatlakozáshoz szükséges hitelesítő adatok megadására. Ugyanazon a képernyőn a **tartományvezérlő kiválasztása prioritással** adhatja meg azokat a tartományvezérlőket, amelyeket az ügynöknek használnia kell a kiépítési kérelmek küldéséhez.

   ![Tartományi hitelesítő adatok](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. A tartomány konfigurálása után a telepítő megjeleníti a konfigurált tartományok listáját. Ezen a képernyőn megismételheti a #5 és #6 lépést, ha további tartományokat szeretne hozzáadni, vagy kattintson a **tovább** gombra az ügynök regisztrációjának folytatásához.

   ![Konfigurált tartományok](./media/workday-inbound-tutorial/pa_install_screen_6.png "Konfigurált tartományok")

   > [!NOTE]
   > Ha több AD-tartománnyal is rendelkezik (például na.contoso.com, emea.contoso.com), akkor egyenként adja hozzá az egyes tartományokat a listához.
   > Csak a szülőtartomány hozzáadása (például contoso.com) nem elegendő. Minden alárendelt tartományt regisztrálnia kell az ügynökkel.
   
1. Tekintse át a konfiguráció részleteit, és kattintson a **Confirm (megerősítés** ) gombra az ügynök regisztrálásához.
  
   ![Képernyő megerősítése](./media/workday-inbound-tutorial/pa_install_screen_7.png "Képernyő megerősítése")
   
1. A konfigurációs varázsló megjeleníti az ügynök regisztrációjának állapotát.
  
   ![Ügynök regisztrációja](./media/workday-inbound-tutorial/pa_install_screen_8.png "Ügynök regisztrációja")
   
1. Ha az ügynök regisztrációja sikeres volt, kattintson a **Kilépés** gombra a varázslóból való kilépéshez.
  
   ![Kilépés képernyő](./media/workday-inbound-tutorial/pa_install_screen_9.png "Kilépés képernyő")
   
1. Ellenőrizze az ügynök telepítését, és ellenőrizze, hogy fut-e. Ehhez nyissa meg a "szolgáltatások" beépülő modult, és keresse meg a "Microsoft Azure AD kapcsolat létesítése ügynök" nevű szolgáltatást.
  
   ![Szolgáltatások](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory"></a>3\. rész: a kiépítési alkalmazásban konfigurálja a munkanapokhoz és a Active Directoryhoz való kapcsolódást.
Ebben a lépésben kapcsolatot létesít a munkanapokkal, és Active Directory a Azure Portal. 

1. A Azure Portal lépjen vissza a munkaterületre az [1. részben](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent) létrehozott felhasználói üzembe helyezési alkalmazás Active Directoryához.

1. Fejezze be a **rendszergazdai hitelesítő adatok** szakaszt a következőképpen:

   * **Rendszergazdai Felhasználónév** – adja meg a munkanap-integrációs rendszer fiókjának felhasználónevét a bérlői tartománynév hozzáfűzésével. A következőhöz hasonlóan kell kinéznie: **username\@tenant_name**

   * **Rendszergazdai jelszó –** Adja meg a munkanap-integrációs rendszerfiók jelszavát

   * **Bérlői URL-cím –** Adja meg a bérlőhöz tartozó munkanap webszolgáltatások végpontjának URL-címét. Ennek az értéknek a következőképpen kell kinéznie: https://wd3-impl-services1.workday.com/ccx/service/contoso4, ahol a *contoso4* helyére a megfelelő bérlő nevét kell cserélni, és a *wd3-Impl* helyére a megfelelő környezeti karakterlánc kerül.

   * **Active Directory erdő –** A Active Directory tartományának neve, az ügynökkel való regisztrálással. A legördülő listából válassza ki a kiépítés célját. Ez az érték általában karakterlánc, például: *contoso.com*

   * **Active Directory tároló –** Adja meg a tároló DN-t, amelyben az ügynöknek alapértelmezés szerint létre kell hoznia a felhasználói fiókokat.
        Példa: *ou = standard felhasználók, OU = felhasználók, DC = contoso, DC = test*
        
     > [!NOTE]
     > Ez a beállítás csak akkor kerül lejátszásra a felhasználói fiókok létrehozásához, ha a *parentDistinguishedName* attribútum nincs konfigurálva az attribútum-hozzárendelésekben. Ez a beállítás nem használatos a felhasználói keresési vagy frissítési műveletekhez. A tartomány teljes alfájának a keresési művelet hatóköre esik.

   * **Értesítő e-mail –** Adja meg az e-mail-címét, és jelölje be az "e-mail küldése, ha hiba történik" jelölőnégyzetet.

     > [!NOTE]
     > Az Azure AD kiépítési szolgáltatás e-mailes értesítést küld, ha a kiépítési feladatok [karantén](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) állapotba kerülnek.

   * Kattintson a **kapcsolatok tesztelése** gombra. Ha a kapcsolatok tesztelése sikeres, kattintson a felül található **Save (Mentés** ) gombra. Ha nem sikerül, ellenőrizze, hogy a munkanapokhoz tartozó hitelesítő adatok és az ügynök telepítésére konfigurált AD hitelesítő adatok érvényesek-e.

     ![Azure portál](./media/workday-inbound-tutorial/wd_1.png)

   * Miután a hitelesítő adatok mentése sikeresen megtörtént, a **leképezések** szakasz megjeleníti az alapértelmezett hozzárendelések **szinkronizálása munkanapokat a helyszíni munkatársaival Active Directory**

### <a name="part-4-configure-attribute-mappings"></a>4\. rész: attribútumok hozzárendelésének konfigurálása

Ebben a szakaszban azt fogja beállítani, hogy a felhasználói adatok hogyan áramlanak a munkanapokról a Active Directoryra.

1. A **hozzárendelések**alatt a kiépítés lapon kattintson a **munkanap-munkavégzők szinkronizálása a helyszíni Active Directory**lehetőségre.

1. A **forrás objektum hatóköre** mezőben kiválaszthatja, hogy a munkanapokon mely felhasználóknak kell szerepelniük az ad-hoz való kiépítés hatókörében, az attribútumok alapján létrehozott szűrők definiálásával. Az alapértelmezett hatókör a "minden felhasználó a munkanapokon". Példa szűrők:

   * Példa: hatókör azon felhasználók számára, akiknek a munkavégző azonosítója 1000000 és 2000000 között van (kivéve 2000000)

      * Attribútum: WorkerID

      * Operátor: REGEX egyezés

      * Érték: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Példa: csak alkalmazottak és nem függő feldolgozók

      * Attribútum: Alkalmazottkód

      * Operátor: nem NULL

   > [!TIP]
   > Amikor első alkalommal konfigurálja a kiépítési alkalmazást, meg kell vizsgálnia és ellenőriznie kell az attribútumok hozzárendeléseit és kifejezéseit, hogy biztosan megadja a kívánt eredményt. A Microsoft azt javasolja, hogy a **forrás objektum hatókörében** lévő hatókör-szűrők használatával tesztelje a leképezéseket néhány, munkanapokon tesztelő felhasználóval. Miután meggyőződött arról, hogy a leképezések működnek, távolítsa el a szűrőt, vagy fokozatosan bontsa ki, hogy több felhasználót is tartalmazzon.

   > [!CAUTION] 
   > A kiépítési motor alapértelmezett viselkedése, hogy letiltsa/törölje a hatókörön kívüli felhasználókat. Előfordulhat, hogy az AD-integrációhoz nem lehet szükség az adott munkanapon belül. Az alapértelmezett viselkedés felülbírálásához tekintse meg a [Hatókörön kívüli felhasználói fiókok törlésének kihagyása](../manage-apps/skip-out-of-scope-deletions.md) című cikket.
  
1. A **cél objektum műveletek** mezőben globálisan szűrheti, hogy a Active Directory milyen műveleteket hajtson végre. A **Létrehozás** és a **frissítés** a leggyakoribb.

1. Az **attribútum-hozzárendelések** szakaszban megadhatja, hogy az egyes munkanapok attribútumai hogyan Active Directory attribútumaikat.

1. Kattintson egy meglévő attribútum-hozzárendelésre a frissítéséhez, vagy kattintson az **Új leképezés hozzáadása** lehetőségre a képernyő alján új hozzárendelések hozzáadásához. Az egyes attribútumok megfeleltetése a következő tulajdonságokat támogatja:

      * **Leképezés típusa**

         * **Direct** – a munkanap attribútum értékének beírása az ad attribútumba módosítás nélkül

         * **Konstans** – statikus, állandó karakterlánc-érték írása az ad-attribútumba

         * **Kifejezés** – lehetővé teszi egyéni érték írását az ad-attribútumnak egy vagy több munkanap-attribútum alapján. [További információkért tekintse meg ezt a cikket a kifejezésekkel kapcsolatban](../manage-apps/functions-for-customizing-application-data.md).

      * **Forrásoldali attribútum** – a felhasználó attribútuma a munkanap alapján. Ha a keresett attribútum nem található, tekintse meg [a munkanapokat használó felhasználói attribútumok listájának testreszabása](#customizing-the-list-of-workday-user-attributes)című témakört.

      * **Alapértelmezett érték** – nem kötelező. Ha a forrás attribútum üres értékkel rendelkezik, a leképezés Ehelyett ezt az értéket fogja írni.
            A leggyakoribb konfiguráció az, hogy ezt üresen hagyja.

      * **Target attribútum** – a felhasználó attribútuma Active Directoryban.

      * **Objektumok egyeztetése ezzel az attribútummal** – függetlenül attól, hogy ezt a leképezést kell-e használni a felhasználók munkanap és Active Directory közötti egyedi azonosításához. Ez az érték általában a munkavégző azonosító mezőjére van beállítva, amelyet általában a Active Directoryban lévő alkalmazotti azonosító attribútumok egyikére kell leképezni.

      * **Megfeleltetési prioritás** – a rendszer több egyező attribútumot is beállíthat. Ha több is van, a rendszer a mező által meghatározott sorrendben értékeli ki őket. Amint talál egyezést, nem lesz kiértékelve további egyező attribútumok.

      * **A leképezés alkalmazása**

         * **Mindig** – alkalmazza ezt a leképezést a felhasználói létrehozási és frissítési műveletekre is

         * **Csak a létrehozás során** – alkalmazza ezt a leképezést kizárólag felhasználói létrehozási műveletekre

1. A leképezések mentéséhez kattintson a **Save (Mentés** ) gombra az attribútum-leképezési szakasz tetején.

   ![Azure portál](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Az alábbiakban néhány példát mutatunk be a munkanap és a Active Directory között, néhány gyakori kifejezéssel

* A *parentDistinguishedName* attribútumra leképező kifejezés a felhasználó különböző szervezeti egységekhez való kiépítésére szolgál egy vagy több munkanap forrás attribútuma alapján. Ebben a példában a felhasználók különböző szervezeti egységekben vannak elhelyezve attól függően, hogy milyen városokban vannak.

* A Active Directory *userPrincipalName* attribútumát a rendszer a deduplikálás függvény [SelectUniqueValue](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue) hozza létre, amely ellenőrzi, hogy létezik-e egy GENERÁLT érték a cél ad-tartományban, és csak akkor állítja be, ha az egyedi.  

* [A kifejezések írásához itt talál dokumentációt](../manage-apps/functions-for-customizing-application-data.md). Ez a szakasz példákat tartalmaz a speciális karakterek eltávolítására.

| MUNKANAP ATTRIBÚTUM | ACTIVE DIRECTORY-ATTRIBÚTUM |  EGYEZŐ AZONOSÍTÓ? | LÉTREHOZÁS/FRISSÍTÉS |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Igen** | Csak létrehozásra írva |
| **PreferredNameData**    |  CN    |   |   Csak létrehozásra írva |
| **SelectUniqueValue (JOIN ("\@", JOIN (".", \[FirstName\], \[LastName\]), "contoso.com"), csatlakozás ("\@", csatlakozás (".", Mid (\[FirstName\], 1, 1), \[LastName\]), "contoso.com"), csatlakozás ("\@", csatlakozás (".", Mid (\[FirstName\], 1, 2), \[LastName\]), "contoso.com")**   | userPrincipalName     |     | Csak létrehozásra írva 
| **Replace (Mid (a Replace (\[UserID\],, "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\]) ",," ",,), 1, 20),," ([\\\\.)\*\$] (file:///\\.) *$)", , "", , )**      |    sAMAccountName            |     |         Csak létrehozásra írva |
| **Kapcsoló (\[aktív\], "0", "true", "1", "false")** |  accountDisabled      |     | Létrehozás + frissítés |
| **FirstName**   | givenName       |     |    Létrehozás + frissítés |
| **LastName**   |   sn   |     |  Létrehozás + frissítés |
| **PreferredNameData**  |  displayName |     |   Létrehozás + frissítés |
| **Vállalat**         | company   |     |  Létrehozás + frissítés |
| **SupervisoryOrganization**  | Részleg  |     |  Létrehozás + frissítés |
| **ManagerReference**   | manager  |     |  Létrehozás + frissítés |
| **BusinessTitle**   |  title     |     |  Létrehozás + frissítés | 
| **AddressLineData**    |  streetAddress  |     |   Létrehozás + frissítés |
| **Önkormányzat**   |   l   |     | Létrehozás + frissítés |
| **CountryReferenceTwoLetter**      |   Co |     |   Létrehozás + frissítés |
| **CountryReferenceTwoLetter**    |  c  |     |         Létrehozás + frissítés |
| **CountryRegionReference** |  st     |     | Létrehozás + frissítés |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Létrehozás + frissítés |
| **Irányítószám**  |   Irányítószám  |     | Létrehozás + frissítés |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Létrehozás + frissítés |
| **Fax**      | Érték facsimiletelephonenumber     |     |    Létrehozás + frissítés |
| **Mobil**  |    mobil       |     |       Létrehozás + frissítés |
| **LocalReference** |  preferredLanguage  |     |  Létrehozás + frissítés |                                               
| **Switch (\[önkormányzat\], "OU = standard felhasználók, OU = felhasználók, OU = default, OU = Locations, DC = contoso, DC = com", "Dallas", "OU = standard felhasználók, OU = felhasználók, OU = Dallas, OU = Locations, DC = contoso, DC = com", "Austin", "OU = standard felhasználók, OU = felhasználók, OU = Austin, OU = Locations, DC = contoso, DC = com", "Seattle", "OU = standard felhasználók, OU = felhasználók, OU = Seattle, OU = Locations, DC = contoso, DC = com", "London", "OU = standard felhasználók, OU = felhasználók, OU = London, OU = Locations, DC = contoso, DC = com")**  | parentDistinguishedName     |     |  Létrehozás + frissítés |

Miután az attribútum-hozzárendelési konfiguráció elkészült, mostantól [engedélyezheti és elindíthatja a felhasználó kiépítési szolgáltatását](#enable-and-launch-user-provisioning).

## <a name="configuring-user-provisioning-to-azure-ad"></a>A felhasználók üzembe helyezésének konfigurálása az Azure AD-ben

A következő szakaszok ismertetik a felhasználók kiépítésének a munkahelyről az Azure AD-be való konfigurálásának lépéseit a csak felhőalapú környezetekben.

* [Az Azure AD kiépítési összekötő alkalmazás hozzáadása és a munkanapokhoz való csatlakozás létrehozása](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [A munkanap és az Azure AD attribútum-hozzárendelések konfigurálása](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [A felhasználók üzembe helyezésének engedélyezése és elindítása](#enable-and-launch-user-provisioning)

> [!IMPORTANT]
> Csak akkor kövesse az alábbi eljárást, ha csak felhőalapú felhasználókat szeretne kiépíteni az Azure AD-be és nem helyszíni Active Directory.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>1\. rész: az Azure AD-kiépítési összekötő alkalmazás hozzáadása és a munkanapokhoz való csatlakozás létrehozása

**Munkanapok konfigurálása a csak felhőalapú felhasználók Azure Active Directory kiépítés esetén:**

1. Nyissa meg a következőt: <https://portal.azure.com>.

2. A Azure Portal keresse meg és válassza a **Azure Active Directory**lehetőséget.

3. Válassza a **vállalati alkalmazások**, majd **az összes alkalmazás**lehetőséget.

4. Válassza az **alkalmazás hozzáadása**lehetőséget, majd válassza az **összes** kategóriát.

5. Keresse meg a **munkanapokat az Azure ad**-ben, és adja hozzá az alkalmazást a katalógusból.

6. Miután hozzáadta az alkalmazást, és megjelenik az alkalmazás részletei képernyő, válassza a **kiépítés**lehetőséget.

7. Módosítsa a **kiépítési** **módot** **automatikusra**.

8. Fejezze be a **rendszergazdai hitelesítő adatok** szakaszt a következőképpen:

   * **Rendszergazdai Felhasználónév** – adja meg a munkanap-integrációs rendszer fiókjának felhasználónevét a bérlői tartománynév hozzáfűzésével. Valahogy így kell kinéznie: username@contoso4

   * **Rendszergazdai jelszó –** Adja meg a munkanap-integrációs rendszerfiók jelszavát

   * **Bérlői URL-cím –** Adja meg a bérlőhöz tartozó munkanap webszolgáltatások végpontjának URL-címét. Ennek az értéknek a következőképpen kell kinéznie: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, ahol a *contoso4* helyére a megfelelő bérlő nevét kell cserélni, és a *wd3-Impl* helyére a megfelelő környezeti karakterlánc kerül. Ha ez az URL-cím nem ismert, használja a munkanap integrációs partnerét vagy a támogatási képviselőt a megfelelő URL-cím meghatározásához.

   * **Értesítő e-mail –** Adja meg az e-mail-címét, és jelölje be az "e-mail küldése, ha hiba történik" jelölőnégyzetet.

   * Kattintson a **kapcsolatok tesztelése** gombra.

   * Ha a kapcsolatok tesztelése sikeres, kattintson a felül található **Save (Mentés** ) gombra. Ha nem sikerül, ellenőrizze, hogy a munkanap URL-címe és a hitelesítő adatok a munkanapokon érvényesek-e.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>2\. rész: a munkanap és az Azure AD attribútum-hozzárendelések konfigurálása

Ebben a szakaszban azt fogja konfigurálni, hogy a felhasználói adatok hogyan áramlanak a munkanapokból a csak felhőalapú felhasználók Azure Active Directory.

1. A **hozzárendelések**alatt a kiépítés lapon kattintson a **munkatársak szinkronizálása az Azure ad-be**lehetőségre.

2. A **forrás objektum hatóköre** mezőben kiválaszthatja, hogy a munkanapokon mely felhasználóknak kell szerepelniük az Azure ad-be való kiépítés hatókörében az attribútumok alapján létrehozott szűrők definiálásával. Az alapértelmezett hatókör a "minden felhasználó a munkanapokon". Példa szűrők:

   * Példa: hatókör azon felhasználók számára, akiknek a munkavégző azonosítója 1000000 és 2000000 között van

      * Attribútum: WorkerID

      * Operátor: REGEX egyezés

      * Érték: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Példa: csak a feltételes feldolgozók és nem rendszeres alkalmazottak

      * Attribútum: ContingentID

      * Operátor: nem NULL

3. A **cél objektum műveletek** mezőben globálisan szűrheti, hogy milyen műveleteket hajtson végre az Azure ad-ben. A **Létrehozás** és a **frissítés** a leggyakoribb.

4. Az **attribútum-hozzárendelések** szakaszban megadhatja, hogy az egyes munkanapok attribútumai hogyan Active Directory attribútumaikat.

5. Kattintson egy meglévő attribútum-hozzárendelésre a frissítéséhez, vagy kattintson az **Új leképezés hozzáadása** lehetőségre a képernyő alján új hozzárendelések hozzáadásához. Az egyes attribútumok megfeleltetése a következő tulajdonságokat támogatja:

   * **Leképezés típusa**

      * **Direct** – a munkanap attribútum értékének beírása az ad attribútumba módosítás nélkül

      * **Konstans** – statikus, állandó karakterlánc-érték írása az ad-attribútumba

      * **Kifejezés** – lehetővé teszi egyéni érték írását az ad-attribútumnak egy vagy több munkanap-attribútum alapján. [További információkért tekintse meg ezt a cikket a kifejezésekkel kapcsolatban](../manage-apps/functions-for-customizing-application-data.md).

   * **Forrásoldali attribútum** – a felhasználó attribútuma a munkanap alapján. Ha a keresett attribútum nem található, tekintse meg [a munkanapokat használó felhasználói attribútumok listájának testreszabása](#customizing-the-list-of-workday-user-attributes)című témakört.

   * **Alapértelmezett érték** – nem kötelező. Ha a forrás attribútum üres értékkel rendelkezik, a leképezés Ehelyett ezt az értéket fogja írni.
            A leggyakoribb konfiguráció az, hogy ezt üresen hagyja.

   * **Target attribútum** – a User attribútum az Azure ad-ben.

   * **Objektumok egyeztetése ezzel az attribútummal** – függetlenül attól, hogy ezt az attribútumot kell-e használni a felhasználók munkanap és az Azure ad közötti egyedi azonosításához. Ez az érték általában a munkavégző azonosító mezőjére van beállítva, amely általában az Employee ID attribútum (új) vagy egy Extension attribútum az Azure AD-ben van leképezve.

   * **Megfeleltetési prioritás** – a rendszer több egyező attribútumot is beállíthat. Ha több is van, a rendszer a mező által meghatározott sorrendben értékeli ki őket. Amint talál egyezést, nem lesz kiértékelve további egyező attribútumok.

   * **A leképezés alkalmazása**

     * **Mindig** – alkalmazza ezt a leképezést a felhasználói létrehozási és frissítési műveletekre is

     * **Csak a létrehozás során** – alkalmazza ezt a leképezést kizárólag felhasználói létrehozási műveletekre

6. A leképezések mentéséhez kattintson a **Save (Mentés** ) gombra az attribútum-leképezési szakasz tetején.

Miután az attribútum-hozzárendelési konfiguráció elkészült, mostantól [engedélyezheti és elindíthatja a felhasználó kiépítési szolgáltatását](#enable-and-launch-user-provisioning).

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Az Azure AD-attribútumok visszaírási konfigurálása munkanapokra

Kövesse ezeket az utasításokat a felhasználói e-mail-címek és felhasználónevek visszaírási konfigurálásához Azure Active Directoryról munkanapokra.

* [A visszaírási-összekötő alkalmazás hozzáadása és a munkanapokhoz való csatlakozás létrehozása](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Visszaírási-attribútumok megfeleltetésének konfigurálása](#part-2-configure-writeback-attribute-mappings)
* [A felhasználók üzembe helyezésének engedélyezése és elindítása](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>1\. rész: a visszaírási-összekötő alkalmazás hozzáadása és a kapcsolat létrehozása a munkanapokhoz

**A munkanap visszaírási-összekötő konfigurálása:**

1. Nyissa meg a következőt: <https://portal.azure.com>.

2. A Azure Portal keresse meg és válassza a **Azure Active Directory**lehetőséget.

3. Válassza a **vállalati alkalmazások**, majd **az összes alkalmazás**lehetőséget.

4. Válassza az **alkalmazás hozzáadása**lehetőséget, majd válassza az **összes** kategóriát.

5. Keresse meg a **munkanap visszaírási**, és adja hozzá az alkalmazást a katalógusból.

6. Miután hozzáadta az alkalmazást, és megjelenik az alkalmazás részletei képernyő, válassza a **kiépítés**lehetőséget.

7. Módosítsa a **kiépítési** **módot** **automatikusra**.

8. Fejezze be a **rendszergazdai hitelesítő adatok** szakaszt a következőképpen:

   * **Rendszergazdai Felhasználónév** – adja meg a munkanap-integrációs rendszer fiókjának felhasználónevét a bérlői tartománynév hozzáfűzésével. Valahogy így kell kinéznie: *felhasználónév\@contoso4*

   * **Rendszergazdai jelszó –** Adja meg a munkanap-integrációs rendszerfiók jelszavát

   * **Bérlői URL-cím –** Adja meg a bérlőhöz tartozó munkanap webszolgáltatások végpontjának URL-címét. Ennek az értéknek a következőképpen kell kinéznie: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, ahol a *contoso4* helyére a megfelelő bérlő nevét kell cserélni, és a *wd3-Impl* helyére a megfelelő környezeti karakterlánc kerül (ha szükséges).

   * **Értesítő e-mail –** Adja meg az e-mail-címét, és jelölje be az "e-mail küldése, ha hiba történik" jelölőnégyzetet.

   * Kattintson a **kapcsolatok tesztelése** gombra. Ha a kapcsolatok tesztelése sikeres, kattintson a felül található **Save (Mentés** ) gombra. Ha nem sikerül, ellenőrizze, hogy a munkanap URL-címe és a hitelesítő adatok a munkanapokon érvényesek-e.

### <a name="part-2-configure-writeback-attribute-mappings"></a>2\. rész: a visszaírási attribútum-hozzárendelések konfigurálása

Ebben a szakaszban azt fogja beállítani, hogy a visszaírási-attribútumok hogyan áramlanak az Azure AD-től a munkanapokig. Jelenleg az összekötő csak az e-mail-cím és a Felhasználónév visszaírási támogatja a munkanapokon.

1. A **hozzárendelések**alatt a kiépítés lapon kattintson a **Azure Active Directory felhasználók szinkronizálása munkanapokra**elemre.

2. A **forrásoldali objektum hatóköre** mezőben opcionálisan szűrheti, hogy a Azure Active Directory felhasználók mely készletei rendelkeznek a munkanapokra írt e-mail-címekkel. Az alapértelmezett hatókör a "minden felhasználó az Azure AD-ben".

3. Az **attribútum-hozzárendelések** szakaszban frissítse a megfelelő azonosítót, hogy jelezze a Azure Active Directory attribútumát, ahol a munkanapon dolgozó azonosítója vagy az alkalmazott azonosítója található. A népszerű egyeztetési módszer a munkanap munkavégző AZONOSÍTÓjának vagy az alkalmazott AZONOSÍTÓjának szinkronizálása az Azure AD-ben a extensionAttribute1-15 értékre, majd az Azure AD-ben ezt az attribútumot használva a felhasználók visszatérhetnek a munkanapokhoz.

4. Általában az Azure AD *userPrincipalName* attribútumát a munkanapokhoz tartozó *userid* attribútumra képezi le, és leképezi az Azure ad *mail* attribútumot a munkanap *EmailAddress* attribútumára. A leképezések mentéséhez kattintson a **Save (Mentés** ) gombra az attribútum-leképezési szakasz tetején.

Miután az attribútum-hozzárendelési konfiguráció elkészült, mostantól [engedélyezheti és elindíthatja a felhasználó kiépítési szolgáltatását](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>A felhasználók üzembe helyezésének engedélyezése és elindítása

Miután befejezte a munkaidő-kiépítési alkalmazás konfigurációját, bekapcsolhatja a kiépítési szolgáltatást a Azure Portal.

> [!TIP]
> Alapértelmezés szerint a kiépítési szolgáltatás bekapcsolásakor a rendszer kiépítési műveleteket kezdeményez a hatókörben lévő összes felhasználó számára. Ha hibák léptek fel a leképezési vagy a munkanapokon tárolt adatokkal kapcsolatban, a kiépítési feladat meghiúsulhat, és a karanténba helyezési állapotba kerülhet. Ennek elkerüléséhez ajánlott eljárásként Azt javasoljuk, hogy a **forrás objektum hatókör** -szűrőjét konfigurálja, és tesztelje az attribútumok hozzárendeléseit néhány tesztelési felhasználóval, mielőtt elindítja a teljes szinkronizálást az összes felhasználó számára. Miután meggyőződött arról, hogy a leképezések működnek, és megadja a kívánt eredményeket, távolítsa el a szűrőt, vagy fokozatosan bontsa ki, hogy több felhasználót is tartalmazzon.

1. A **létesítés** lapon állítsa be a **kiépítési állapotot** **a**következőre:.

2. Kattintson a **Mentés** gombra.

3. Ez a művelet elindítja a kezdeti szinkronizálást, amely a munkanapokhoz tartozó bérlők számától függően több órát is igénybe vehet. 

4. A Azure Portal **naplók** lapján bármikor megtekintheti a kiépítési szolgáltatás által végrehajtott műveleteket. A naplók a kiépítési szolgáltatás által végrehajtott összes egyéni szinkronizálási eseményt felsorolják, például hogy mely felhasználók olvasnak el munkanapokon, majd ezt követően hozzáadják vagy frissítették Active Directory. A naplók áttekintésével és a kiépítési hibák kijavításával kapcsolatos utasításokért tekintse meg a hibaelhárítás című szakaszt.

5. A kezdeti szinkronizálás befejezésekor a rendszer egy naplózási összesítő jelentést ír a **létesítés** lapon az alább látható módon.

   ![Azure portál](./media/workday-inbound-tutorial/wd_3.png)

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

* **Megoldási képességgel kapcsolatos kérdések**
  * [Amikor új bérletet dolgoz fel a munkanapokból, hogyan állítja be a megoldás az új felhasználói fiók jelszavát Active Directory?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [Támogatja a megoldás az e-mail-értesítések küldését a kiépítési műveletek befejeződése után?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Hogyan a jelszavak kézbesítését az új bérlők számára, és biztonságosan biztosítson egy mechanizmust a jelszavuk alaphelyzetbe állításához?](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [Az Azure AD-felhőben vagy a létesítési ügynök rétegében a megoldás gyorsítótár-munkanapokhoz tartozó felhasználói profilok vannak?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [Támogatja a megoldás a helyszíni AD-csoportok a felhasználóhoz való hozzárendelését?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Milyen munkanap API-kat használ a megoldás a munkanap-munkavégző profilok lekérdezéséhez és frissítéséhez?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Konfigurálható a munkanap HCM-bérlő két Azure AD-Bérlővel?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Miért nem támogatott a "munkanap – Azure AD" felhasználó-kiépítési alkalmazás, ha üzembe helyezte a Azure AD Connect?](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [Hogyan a munkanapokkal és az Azure AD-integrációval kapcsolatos új funkciókra vonatkozó javaslatokat?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Kiépítési ügynökkel kapcsolatos kérdések**
  * [Mi a kiépítési ügynök GA-verziója?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Hogyan ismeri a kiépítési ügynök verzióját?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [A Microsoft automatikusan leküldi a kiépítési ügynök frissítéseit?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Telepíthetem a kiépítési ügynököt ugyanarra a kiszolgálóra, amelyen Azure AD Connect fut?](#can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect)
  * [Hogyan konfigurálja a kiépítési ügynököt, hogy proxykiszolgálót használjon a kimenő HTTP-kommunikációhoz?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Hogyan gondoskodjon arról, hogy a kiépítési ügynök képes legyen kommunikálni az Azure AD-Bérlővel, és egyetlen tűzfal sem blokkolja az ügynök által igényelt portokat?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Hogyan a kiépítési ügynökhöz társított tartomány regisztrációját?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Hogyan eltávolítja a kiépítési ügynököt?](#how-do-i-uninstall-the-provisioning-agent)
  
* **Az Active Directory-attribútumok hozzárendelésére és konfigurálására vonatkozó kérdések munkanapokon**
  * [A munkaidő-kiépítési attribútum leképezésének és sémájának munkapéldányának biztonsági mentése vagy exportálása Hogyan?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Egyéni attribútumok vannak a munkanapokon és a Active Directory. Hogyan konfigurálja a megoldást az egyéni attribútumokkal való munkavégzéshez?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Felépíthetem a felhasználó fényképét a munkanapból a Active Directoryra?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Hogyan szinkronizálni a munkanapokat a nyilvános használatra vonatkozó felhasználói beleegyezikés alapján?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [A Hogyan formátum a felhasználó részlege/ország/város attribútumai alapján jeleníti meg az AD-beli neveket, és kezeli a regionális eltéréseket?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Hogyan használhatom a SelectUniqueValue-t egyedi értékek létrehozásához a samAccountName attribútumhoz?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Hogyan a mellékjeleket és a normál angol Ábécébe konvertálhatja a karaktereket?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Megoldási képességgel kapcsolatos kérdések

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Amikor új bérletet dolgoz fel a munkanapokból, hogyan állítja be a megoldás az új felhasználói fiók jelszavát Active Directory?

Ha a helyszíni kiépítési ügynök új AD-fiók létrehozására vonatkozó kérést kap, automatikusan létrehoz egy összetett véletlenszerű jelszót, amely megfelel az AD-kiszolgáló által meghatározott jelszó-összetettségi követelményeknek, és beállítja azt a felhasználói objektumon. Ez a jelszó bárhol nincs naplózva.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>Támogatja a megoldás az e-mail-értesítések küldését a kiépítési műveletek befejeződése után?

Nem, e-mail-értesítések küldése a kiépítési műveletek befejezése után az aktuális kiadásban nem támogatott.

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>Hogyan a jelszavak kézbesítését az új bérlők számára, és biztonságosan biztosítson egy mechanizmust a jelszavuk alaphelyzetbe állításához?

Az új AD-fiók üzembe helyezésének egyik végső lépése a felhasználó AD-fiókjához rendelt ideiglenes jelszó továbbítása. Számos vállalat továbbra is a hagyományos módszert használja az ideiglenes jelszó továbbítására a felhasználó felettesének, aki ezt követően átadja a jelszót az új bérlet/függő feldolgozónak. Ez a folyamat tartalmaz egy belső biztonsági hibát, és rendelkezésre áll egy lehetőség az Azure AD-funkciókkal való jobb megközelítés megvalósítására.

A felvételi folyamat részeként a HR-csapatok általában egy háttér-ellenőrzési műveletet futtatnak, és az új bérlet mobil számát. Ha az AD-t a felhasználók kiépítéséhez szeretné kiépíteni, akkor a tényre épülve kiépítheti a felhasználó önkiszolgáló jelszó-visszaállítási funkcióját az 1. napon. Ezt úgy érheti el, hogy az új bérlet "Mobile Number" attribútumát az AD-be, majd az AD-ből az Azure AD-be a Azure AD Connect használatával propagálja. Miután az Azure AD-ban szerepel a "Mobile Number" (mobil szám), engedélyezheti a felhasználói fiókhoz tartozó önkiszolgáló [jelszó-visszaállítást (SSPR)](../authentication/howto-sspr-authenticationdata.md) , így az 1. napon az új bérlet a regisztrált és ellenőrzött mobil számot is használhatja a hitelesítéshez.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>Az Azure AD-felhőben vagy a létesítési ügynök rétegében a megoldás gyorsítótár-munkanapokhoz tartozó felhasználói profilok vannak?

Nem, a megoldás nem tart fenn felhasználói profilok gyorsítótárát. Az Azure AD-kiépítési szolgáltatás egyszerűen adatfeldolgozóként funkcionál, adatok beolvasása a munkanapokból, és írás a cél Active Directory vagy az Azure AD-ba. Tekintse meg a [személyes adatok kezelése](#managing-personal-data) a felhasználói adatvédelmet és az adatmegőrzéssel kapcsolatos részleteket ismertető szakaszt.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>Támogatja a megoldás a helyszíni AD-csoportok a felhasználóhoz való hozzárendelését?

Ez a funkció jelenleg nem támogatott. Az ajánlott Áthidaló megoldás egy olyan PowerShell-parancsfájl üzembe helyezése, amely lekérdezi az Azure AD Graph API végpontot a naplózási adatokhoz, és ezzel olyan forgatókönyveket indít el, mint például a csoport hozzárendelése. Ez a PowerShell-parancsfájl csatolható egy Feladatütemezőhöz, és a kiépítési ügynököt futtató ugyanazon a gépen is üzembe helyezhető.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Milyen munkanap API-kat használ a megoldás a munkanap-munkavégző profilok lekérdezéséhez és frissítéséhez?

A megoldás jelenleg a következő munkanap API-kat használja:

* Get_Workers (v 21.1) a feldolgozói adatok beolvasásához
* Maintain_Contact_Information (v 26.1) a munkahelyi e-mailek visszaírási szolgáltatásához
* Update_Workday_Account (v 31.2) a username visszaírási szolgáltatáshoz

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Konfigurálható a munkanap HCM-bérlő két Azure AD-Bérlővel?

Igen, ez a konfiguráció támogatott. A forgatókönyv konfigurálásának lépései a következők:

* A kiépítési ügynök üzembe helyezése #1 és regisztrálása az Azure AD-bérlő #1.
* A kiépítési ügynök üzembe helyezése #2 és regisztrálása az Azure AD-bérlő #2.
* Az egyes kiépítési ügynökök által felügyelt "gyermektartomány" alapján minden ügynököt konfigurálhat a tartomány (ok) hoz. Egy ügynök több tartományt is képes kezelni.
* A Azure Portalban állítsa be a munkanapokat az AD-felhasználó kiépítési alkalmazására minden egyes bérlőn, és konfigurálja azt a megfelelő tartományokkal.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>Miért nem támogatott a "munkanap – Azure AD" felhasználó-kiépítési alkalmazás, ha üzembe helyezte a Azure AD Connect?

Ha az Azure AD hibrid módban van használatban (ahol a Felhőbeli és a helyszíni felhasználók kombinációját tartalmazza), fontos, hogy egyértelmű definíciót tartalmazzon a "szolgáltatói forrás" kifejezésből. A hibrid forgatókönyvek általában a Azure AD Connect üzembe helyezését igénylik. Azure AD Connect telepítésekor a helyszíni AD a szolgáltató forrása. Az Azure AD Connectornak a mix szolgáltatásba való bevezetésével olyan helyzetet eredményezhet, amelyben a munkanap attribútum értékei felülírhatják a Azure AD Connect által beállított értékeket. Így a "munkanap – Azure AD" kiépítési alkalmazás használata nem támogatott, ha Azure AD Connect engedélyezve van. Ilyen helyzetekben azt javasoljuk, hogy a felhasználók helyszíni AD-be való beszerzéséhez és az Azure Azure AD Connect AD-ba való szinkronizálásához használja a "munkanap az AD-felhasználó számára" kiépítési alkalmazást.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Hogyan a munkanapokkal és az Azure AD-integrációval kapcsolatos új funkciókra vonatkozó javaslatokat?

Az Ön visszajelzése nagyon értékes, mivel segít megállapítani a jövőbeli kiadások és fejlesztések irányát. Üdvözöljük az összes visszajelzést, és javasoljuk, hogy küldje el ötleteit vagy tökéletesítését az [Azure ad visszajelzési fórumában](https://feedback.azure.com/forums/169401-azure-active-directory). A munkanap-integrációval kapcsolatos konkrét Visszajelzésért válassza az *SaaS-alkalmazások* kategóriát, és a *kulcsszavak munkanapon való* kereséssel keresse meg a munkanapokhoz kapcsolódó meglévő visszajelzést.

![UserVoice SaaS-alkalmazások](media/workday-inbound-tutorial/uservoice_saas_apps.png)

![UserVoice munkanap](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Ha új ötletet javasol, ellenőrizze, hogy valaki más már javasolta-e a hasonló funkció megjelenítését. Ebben az esetben lehetősége van szavazni a funkció vagy a javító kérelem számára. Megjegyzést is megadhat a konkrét használati esettel kapcsolatban, hogy megjelenítse az ötlete támogatását, és mutassa be, hogy a funkció milyen értékes lesz az Ön számára.

### <a name="provisioning-agent-questions"></a>Kiépítési ügynökkel kapcsolatos kérdések

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Mi a kiépítési ügynök GA-verziója?

* A kiépítési ügynök GA-verziója 1.1.30 vagy újabb.
* Ha az ügynök verziója kisebb, mint 1.1.30, a nyilvános előzetes verziót futtatja, és a rendszer automatikusan frissíti a GA verzióra, ha az ügynököt futtató kiszolgáló .NET 4.7.1 futtatókörnyezettel rendelkezik.
  * A kiszolgálón telepített [.NET-verziót is megtekintheti](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) . Ha a kiszolgáló nem .NET-4.7.1 fut, [letöltheti és telepítheti a .net-4.7.1](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows). A .NET-4.7.1 telepítése után a kiépítési ügynök automatikusan frissülni fog a GA verzióra.

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Hogyan ismeri a kiépítési ügynök verzióját?

* Jelentkezzen be arra a Windows-kiszolgálóra, amelyen a kiépítési ügynök telepítve van.
* Nyissa meg a **vezérlőpultot** -> **távolítsa el vagy módosítsa a program** menüt
* Keresse meg a bejegyzésnek megfelelő verziót **Microsoft Azure ad kapcsolódás kiépítési ügynökhöz**

  ![Azure portál](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>A Microsoft automatikusan leküldi a kiépítési ügynök frissítéseit?

Igen, a Microsoft automatikusan frissíti a kiépítési ügynököt. Az automatikus frissítések letiltásához állítsa le a Windows-szolgáltatás **Microsoft Azure ad a csatlakozási ügynök frissítését**.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect"></a>Telepíthetem a kiépítési ügynököt ugyanarra a kiszolgálóra, amelyen Azure AD Connect fut?

Igen, a kiépítési ügynököt ugyanarra a kiszolgálóra is telepítheti, amelyen Azure AD Connect fut.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>A konfigurálás időpontjában a kiépítési ügynök az Azure AD rendszergazdai hitelesítő adatait kéri. Az ügynök helyileg tárolja a hitelesítő adatokat a kiszolgálón?

A konfiguráció során a kiépítési ügynök csak az Azure ad-bérlőhöz való csatlakozásra kéri az Azure AD rendszergazdai hitelesítő adatait. A hitelesítő adatokat nem tárolja helyileg a kiszolgálón. Ugyanakkor megőrzi a helyszíni *Active Directory tartományhoz* való kapcsolódáshoz használt hitelesítő adatokat egy helyi Windows-jelszó-tárolóban.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Hogyan konfigurálja a kiépítési ügynököt, hogy proxykiszolgálót használjon a kimenő HTTP-kommunikációhoz?

A kiépítési ügynök támogatja a kimenő proxy használatát. A konfigurálásához módosítsa az ügynök konfigurációs fájljának **C:\Program Files\Microsoft Azure ad Connect kiépítési Agent\AADConnectProvisioningAgent.exe.config**. Adja hozzá a következő sorokat a fájl végéhez közvetlenül a záró `</configuration>` címke előtt.
Cserélje le a [Proxy-Server] és a [proxy-port] változót a proxykiszolgáló nevére és a port értékeire.

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

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Hogyan gondoskodjon arról, hogy a kiépítési ügynök képes legyen kommunikálni az Azure AD-Bérlővel, és egyetlen tűzfal sem blokkolja az ügynök által igényelt portokat?

Azt is ellenőrizheti, hogy az összes szükséges portot megnyitotta-e. Ehhez nyissa meg az [összekötő portok tesztelése eszközt](https://aadap-portcheck.connectorporttest.msappproxy.net/) a helyszíni hálózatról. A további zöld pipa nagyobb rugalmasságot jelent.

Győződjön meg arról, hogy az eszköz biztosítja a megfelelő eredményeket:

* Nyissa meg az eszközt egy böngészőben azon a kiszolgálón, amelyen a kiépítési ügynököt telepítette.
* Győződjön meg arról, hogy a kiépítési ügynökre érvényes proxyk vagy tűzfalak is érvényesek erre az oldalra. Ezt az Internet Explorerben a **Beállítások-> Internetbeállítások-> kapcsolatok-> LAN-beállítások**menüpontban teheti meg. Ezen az oldalon a "proxykiszolgáló használata a helyi hálózathoz" mező látható. Jelölje be ezt a jelölőnégyzetet, és helyezze el a proxy címe mezőt a "címek" mezőbe.

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>A kiépítési ügynökök több AD-tartomány kiépítésére is konfigurálhatók?

Igen, az egyik kiépítési ügynök úgy konfigurálható, hogy több AD-tartomány kezelésére is alkalmas legyen, ha az ügynöknek a megfelelő tartományvezérlők felé irányuló látványa van. A Microsoft azt javasolja, hogy a magas rendelkezésre állás biztosítása érdekében 3 kiépítési ügynökből álló csoportot hozzon létre, amelyek ugyanazt az AD-tartományt szolgálják, és biztosítson feladatátvételi támogatást.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Hogyan a kiépítési ügynökhöz társított tartomány regisztrációját?

* A Azure Portal beszerezheti az Azure AD-bérlő *bérlői azonosítóját* .
* Jelentkezzen be a kiépítési ügynököt futtató Windows Server kiszolgálóra.
* Nyissa meg a PowerShellt Windows-rendszergazdaként.
* Váltson a regisztrációs parancsfájlokat tartalmazó könyvtárra, és futtassa az alábbi parancsokat, és cserélje le a \[bérlői azonosító\] paramétert a bérlői azonosító értékére.

  ```powershell
  cd “C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder”
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* A megjelenő ügynökök listájáról másolja a `id` mező értékét az adott erőforrásból, amelynek *resourcename* az ad-tartománynévvel egyenlő.
* Illessze be az azonosító értékét ebbe a parancsba, és futtassa a parancsot a PowerShellben.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Futtassa újra az ügynök konfigurációs varázslóját.
* A korábban ehhez a tartományhoz rendelt egyéb ügynököket újra kell konfigurálni.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Hogyan eltávolítja a kiépítési ügynököt?

* Jelentkezzen be arra a Windows-kiszolgálóra, amelyen a kiépítési ügynök telepítve van.
* Nyissa meg a **vezérlőpultot** -> **távolítsa el vagy módosítsa a program** menüt
* Távolítsa el a következő programokat:
  * Microsoft Azure AD kiépítési ügynök összekötése
  * Microsoft Azure AD összekapcsolási ügynök frissítése
  * Microsoft Azure AD létesítési ügynök csomagjának összekötése

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Az Active Directory-attribútumok hozzárendelésére és konfigurálására vonatkozó kérdések munkanapokon

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>A munkaidő-kiépítési attribútum leképezésének és sémájának munkapéldányának biztonsági mentése vagy exportálása Hogyan?

Microsoft Graph API-val exportálhatja a munkanapokat használó felhasználó üzembe helyezési konfigurációját. A részletekért tekintse meg a következő témakör lépéseit: a [munkanap felhasználó kiépítési attribútumának leképezése konfigurációjának exportálása és importálása](#exporting-and-importing-your-configuration) .

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Egyéni attribútumok vannak a munkanapokon és a Active Directory. Hogyan konfigurálja a megoldást az egyéni attribútumokkal való munkavégzéshez?

A megoldás támogatja az egyéni munkanapokat és a Active Directory attribútumokat. Ha egyéni attribútumait hozzá szeretné adni a leképezési sémához, nyissa meg az **attribútumok leképezése** panelt, és görgessen le a **Speciális beállítások megjelenítése**szakasz kibontásához. 

![Attribútumok listájának szerkesztése](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Az egyéni munkanapok attribútumainak hozzáadásához válassza az *attribútumok szerkesztése a munkanapokhoz* lehetőséget, majd az egyéni ad-attribútumok hozzáadásához válassza az *attribútumok szerkesztése a helyszíni Active Directory*lehetőséget.

Lásd még:

* [A munkanap felhasználói attribútumaik listájának testreszabása](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Hogyan úgy konfigurálja a megoldást, hogy csak a munkanapok változásai alapján frissítse az Active Directory-attribútumokat, és ne hozzon létre új AD-fiókot?

Ezt a konfigurációt úgy érheti el, ha a **cél objektum műveleteit** az **attribútum-hozzárendelések** panelen az alábbi ábrán látható módon állítja be:

![Művelet frissítése](./media/workday-inbound-tutorial/wd_target_update_only.png)

Jelölje be a "frissítés" jelölőnégyzetet csak a munkanapokból az AD-be irányuló frissítési műveleteknél. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Felépíthetem a felhasználó fényképét a munkanapból a Active Directoryra?

A megoldás jelenleg nem támogatja a bináris attribútumok, például a *thumbnailPhoto* és a *jpegPhoto* beállítását Active Directoryban.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Hogyan szinkronizálni a munkanapokat a nyilvános használatra vonatkozó felhasználói beleegyezikés alapján?

* Nyissa meg a munkanap kiépítési alkalmazásának "kiépítés" paneljét.
* Kattintson az attribútum-hozzárendelések elemre. 
* A **leképezések**területen válassza **a munkavégző munkatársak szinkronizálása a helyszíni Active Directory** (vagy a **munkanapokon dolgozók szinkronizálása az Azure ad**-be) lehetőséget.
* Az attribútum-hozzárendelések lapon görgessen le, és jelölje be a "speciális beállítások megjelenítése" jelölőnégyzetet.  Kattintson az **attribútumok szerkesztése munkanapokhoz** lehetőségre
* A megnyíló panelen keresse meg a "Mobile" attribútumot, és kattintson a sorra, így szerkesztheti az **API-kifejezést** ![Mobile GDPR](./media/workday-inbound-tutorial/mobile_gdpr.png)

* Cserélje le az **API-kifejezést** a következő új kifejezésre, amely csak akkor kéri le a Work Mobile-számot, ha a "Public használati jelző" értéke "true" (igaz).

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Mentse az attribútumok listáját.
* Mentse az attribútum-hozzárendelést.
* Törölje az aktuális állapotot, és indítsa újra a teljes szinkronizálást.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>A Hogyan formátum a felhasználó részlege/ország/város attribútumai alapján jeleníti meg az AD-beli neveket, és kezeli a regionális eltéréseket?

Gyakori követelmény, hogy a *DisplayName* attribútumot az ad-ben konfigurálja, így a felhasználó részlegével és országával/régiójával kapcsolatos információkat is biztosít. Például, ha John Smith az Egyesült államokbeli marketing részlegen dolgozik, érdemes lehet a *DisplayName* , hogy megmutasson *Kovács Jánosként (marketing-US)* .

Itt láthatja, hogyan kezelheti ezeket a követelményeket a *CN* vagy a *DisplayName* összeállításához, hogy olyan attribútumokat tartalmazzon, mint például a vállalat, az üzleti egység, a város vagy az ország/régió.

* A rendszer minden egyes munkanap attribútumot egy mögöttes XPATH API-kifejezéssel kérdez le, amely az **attribútumok leképezése – > speciális szakasz – > a munkanapokhoz tartozó attribútumok listájának szerkesztése**. Itt látható az alapértelmezett XPATH API-kifejezés a munkanap *PreferredFirstName*, a *PreferredLastName*, a *vállalati* és a *SupervisoryOrganization* attribútumokhoz.

     | Munkanap attribútum | API XPATH kifejezés |
     | ----------------- | -------------------- |
     | PreferredFirstName | WD: Worker/WD: Worker_Data/WD: Personal_Data/WD: Name_Data/WD: Preferred_Name_Data/WD: Name_Detail_Data/WD: First_Name/Text () |
     | PreferredLastName | WD: Worker/WD: Worker_Data/WD: Personal_Data/WD: Name_Data/WD: Preferred_Name_Data/WD: Name_Detail_Data/WD: Last_Name/Text () |
     | Cég | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: azonosító [@wd:type= ' Organization_Type_ID '] = ' vállalat ']/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [WD: Organization_Type_Reference/WD: ID [@wd:type= ' Organization_Type_ID '] = ' felügyelet ']/WD: Organization_Name/Text () |
  
   Erősítse meg a munkanap csapatát, hogy a fenti API-kifejezés érvényes a munkanap bérlői konfigurációjához. Szükség esetén szerkesztheti őket a [munkanap felhasználói attribútumok listájának testreszabása](#customizing-the-list-of-workday-user-attributes)című szakaszban leírtak szerint.

* Hasonlóképpen a munkanapokban található országbeli információk a következő XPATH használatával kérhetők le: *WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference*

     A munkanap-attribútumok listája szakaszban 5 országhoz kapcsolódó attribútum érhető el.

     | Munkanap attribútum | API XPATH kifejezés |
     | ----------------- | -------------------- |
     | CountryReference | WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: azonosító [@wd:type= ' ISO_3166-1_Alpha-3_Code ']/text () |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: azonosító [@wd:type= ' ISO_3166-1_Numeric-3_Code ']/text () |
     | CountryReferenceTwoLetter | WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: azonosító [@wd:type= ' ISO_3166-1_Alpha-2_Code ']/text () |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Erősítse meg a munkanapokat tartalmazó csapatát, hogy a fenti API-kifejezések érvényesek a munkanap bérlői konfigurációjához. Szükség esetén szerkesztheti őket a [munkanap felhasználói attribútumok listájának testreszabása](#customizing-the-list-of-workday-user-attributes)című szakaszban leírtak szerint.

* A megfelelő attribútum-hozzárendelési kifejezés kiépítéséhez határozza meg, hogy a "mérvadóan" melyik munkanap-attribútum a felhasználó utónevét, vezetéknevét, országát/régióját és osztályát jelöli. Tegyük fel, hogy az attribútumok a következők: *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* és *SupervisoryOrganization* . A következőképpen hozhat létre egy kifejezést az AD *DisplayName* attribútumhoz az alábbiak szerint, hogy megjelenítse a megjelenítendő nevet *, például: Smith, John (marketing-US)* .

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Ha rendelkezik a megfelelő kifejezéssel, szerkessze az attribútum-hozzárendelések táblát, és módosítsa a *DisplayName* attribútum leképezését az alább látható módon: ![displayName leképezés](./media/workday-inbound-tutorial/wd_displayname_map.png)

* A fenti példát kiterjesztve tegyük fel, hogy a *munkanapokból* származó városokat a Gyorsírás értékekre szeretné átalakítani, majd felhasználja a megjelenítendő nevek (például a *Smith, John (Chi)* vagy *DOE, Jane (NYC))* összeállítására

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
  * [Switch függvény szintaxisa](../manage-apps/functions-for-customizing-application-data.md#switch)
  * [Illesztési függvény szintaxisa](../manage-apps/functions-for-customizing-application-data.md#join)
  * [Függvény hozzáfűzése szintaxisa](../manage-apps/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Hogyan használhatom a SelectUniqueValue-t egyedi értékek létrehozásához a samAccountName attribútumhoz?

Tegyük fel, hogy egyedi értékeket kíván előállítani a *sAMAccountName* attribútumhoz az *Utónév* és a *LastName* attribútum együttes használatával a munkanapból. Alább látható egy kifejezés, amely a következővel kezdődhet:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

A fenti kifejezés működése: Ha a felhasználó János Smith, először a JSmith-t próbálja generálni, ha a JSmith már létezik, akkor a JoSmith generál, és ha létezik, akkor létrehozza a JohSmith. A kifejezés azt is biztosítja, hogy a generált érték megfelel a *sAMAccountName*-hez társított hossz-korlátozásnak és a speciális karakterek korlátozásának.

Lásd még:

* [A Mid függvény szintaxisa](../manage-apps/functions-for-customizing-application-data.md#mid)
* [Függvény szintaxisának cseréje](../manage-apps/functions-for-customizing-application-data.md#replace)
* [SelectUniqueValue függvény szintaxisa](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Hogyan a mellékjeleket és a normál angol Ábécébe konvertálhatja a karaktereket?

A [NormalizeDiacritics](../manage-apps/functions-for-customizing-application-data.md#normalizediacritics) függvénnyel eltávolíthat speciális karaktereket a felhasználó keresztneve és vezetékneve alapján, a felhasználó e-mail-címének vagy a CN-értékének összeállításával.

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

Ez a szakasz részletesen ismerteti, hogyan lehet elhárítani az Azure AD-naplók és a Windows Server Eseménynapló naplók használatával a munkanapokkal való integrációval kapcsolatos problémákat. Ez az oktatóanyagban rögzített általános hibaelhárítási lépésekre és fogalmakra épül [: jelentéskészítés az automatikus felhasználói fiók kiépítésekor](../manage-apps/check-status-user-account-provisioning.md)

Ez a szakasz a hibaelhárítás következő szempontjait ismerteti:

* [Windows Eseménynapló beállítása az ügynökhöz – hibaelhárítás](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Azure Portal naplók beállítása a szolgáltatás hibaelhárításához](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Az AD felhasználói fiók létrehozási műveleteinek naplói](#understanding-logs-for-ad-user-account-create-operations)
* [A kezelői frissítési műveletek naplóinak ismertetése](#understanding-logs-for-manager-update-operations)
* [Gyakran előforduló hibák elhárítása](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Windows Eseménynapló beállítása az ügynökhöz – hibaelhárítás

* Jelentkezzen be arra a Windows Server-gépre, amelyen a kiépítési ügynök telepítve van
* Nyissa meg a **Windows Server Eseménynapló** asztali alkalmazást.
* Válassza a **Windows-naplók > alkalmazást**.
* Az **aktuális napló szűrése..** . lehetőség a forrás HRE alatt naplózott összes esemény megtekintéséhez **. A. ProvisioningAgent** és az "5" azonosítójú események kihagyása az alább látható "-5" szűrő megadásával.

  ![Windows Eseménynapló](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* Kattintson **az OK gombra** , és rendezze az eredmény nézetet **dátum és idő** oszlop szerint.

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Azure Portal naplók beállítása a szolgáltatás hibaelhárításához

* Indítsa el a [Azure Portalt](https://portal.azure.com), és navigáljon a munkaidő-kiépítési alkalmazás **naplók** szakaszához.
* A naplók lapon lévő **oszlopok** gomb használatával csak a következő oszlopokat jelenítheti meg a nézetben (dátum, tevékenység, állapot, állapot oka). Ez a konfiguráció biztosítja, hogy csak a hibaelhárításhoz szükséges adatokat koncentrálja.

  ![Naplók oszlopai](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* A nézet szűréséhez használja a **cél** és a **dátumtartomány** lekérdezési paramétereit. 
  * Állítsa a **cél** lekérdezési paramétert a munkanap Worker objektum "Worker id" vagy "Employee id" értékére.
  * Állítsa be a **dátumtartományt** arra a megfelelő időszakra, amelyre vonatkozóan meg szeretné vizsgálni a hibákat vagy a kiépítés során felmerülő problémákat.

  ![Naplók szűrése](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Az AD felhasználói fiók létrehozási műveleteinek naplói

Ha a rendszer egy új felvételt észlel a munkanapokon (tegyük fel, hogy a *21023*-es ALKALMAZOTTi azonosítóval), az Azure ad-kiépítési szolgáltatás megpróbál létrehozni egy új ad-felhasználói fiókot a feldolgozó számára, és a folyamat 4 naplóbeli rekordot hoz létre az alább leírtak szerint:

  [![naplóbeli Create Ops](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Ha rákattint valamelyik naplóbeli rekordra, megnyílik a **tevékenység részletei** lap. Itt látható, hogy az egyes naplók bejegyzéstípusa milyen **tevékenység részleteit** jeleníti meg.

* **Munkanap importálási** rekordja: Ez a naplóbejegyzés a munkavégző adatokat jeleníti meg a munkanapokból beolvasott adatokból. A naplózási rekord *További részletek* szakaszában található információk segítségével elháríthatja az adatok munkanapokból való beolvasásával kapcsolatos problémákat. Alább látható egy példa a rekordokra az egyes mezők értelmezésére szolgáló mutatókkal együtt.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* **Ad-importálási** rekord: Ez a naplóbejegyzés az ad-ből beolvasott fiók adatait jeleníti meg. Ahogy a kezdeti felhasználó létrehozásakor nincs AD-fiók, a *tevékenység állapotának indoklása* arra utal, hogy nem található a megfelelő azonosító attribútum értékkel rendelkező fiók a Active Directory. A naplózási rekord *További részletek* szakaszában található információk segítségével elháríthatja az adatok munkanapokból való beolvasásával kapcsolatos problémákat. Alább látható egy példa a rekordokra az egyes mezők értelmezésére szolgáló mutatókkal együtt.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Az AD-importálási műveletnek megfelelő kiépítési ügynök naplófájljainak megkereséséhez nyissa meg a Windows Eseménynapló-naplókat, és használja a **Keresés...** menüpont a megfelelő azonosító/összekapcsolási tulajdonság attribútum értékét tartalmazó naplóbejegyzések kereséséhez (ebben az esetben *21023*).

  ![Keresés](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Keresse meg az *Event ID = 9*azonosítójú bejegyzést, amely megadja az ügynök által az ad-fiók beolvasásához használt LDAP-keresési szűrőt. Az egyedi felhasználói bejegyzések beolvasásához ellenőrizze, hogy ez a megfelelő keresési szűrő-e.

  ![LDAP-keresés](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  A közvetlenül az *Event ID = 2* azonosítójú rekord rögzíti a keresési művelet eredményét, és ha bármilyen eredményt adott vissza.

  ![LDAP-eredmények](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* **Szinkronizálási szabály műveleti** rekordja: Ez a naplóbejegyzés az attribútum-leképezési szabályok és a konfigurált hatóköri szűrők eredményét jeleníti meg a bejövő munkanap eseményének feldolgozásához szükséges kiépítési művelettel együtt. A szinkronizálási művelettel kapcsolatos hibák elhárításához használja a naplófájl *További részletek* szakaszában található információkat. Alább látható egy példa a rekordokra az egyes mezők értelmezésére szolgáló mutatókkal együtt.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Ha problémák merülnek fel az attribútum-hozzárendelési kifejezésekkel vagy a bejövő munkanapokkal kapcsolatos problémákkal kapcsolatban (például: üres vagy Null érték a kötelező attribútumok esetében), akkor ebben a szakaszban a hiba részleteinek megadásával megfigyelheti a ErrorCode.

* **Ad-exportálási** rekord: Ez a naplóbejegyzés az ad-fiók létrehozási műveletének eredményét jeleníti meg, valamint a folyamatban beállított attribútumok értékeit. A log rekord *További részletek* szakaszában található információk segítségével elháríthatja a fiók-létrehozási művelettel kapcsolatos problémákat. Alább látható egy példa a rekordokra az egyes mezők értelmezésére szolgáló mutatókkal együtt. A "További részletek" szakaszban a "EventName" tulajdonság értéke "EntryExportAdd", a "JoiningProperty" a megfelelő azonosító attribútum értékére van állítva, a "SourceAnchor" értéke a rekordhoz társított WorkdayID (WID), a "TargetAnchor" pedig a következőre van beállítva: az újonnan létrehozott felhasználó AD "ObjectGuid" attribútumának értéke. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Az AD exportálási műveletnek megfelelő kiépítési ügynök naplófájljainak megkereséséhez nyissa meg a Windows Eseménynapló-naplókat, és használja a **Keresés...** menüpont a megfelelő azonosító/összekapcsolási tulajdonság attribútum értékét tartalmazó naplóbejegyzések kereséséhez (ebben az esetben *21023*).  

  Keresse meg az exportálási művelet időbélyegének megfelelő HTTP-bejegyzést a következő azonosítójú *eseménynél: eseményazonosító = 2*. Ez a rekord a kiépítési szolgáltatás által a kiépítési ügynöknek elküldett attribútum-értékeket fogja tartalmazni.

  [![SCIM hozzáadása](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  Közvetlenül a fenti eseményt követően egy másik eseménynek kell lennie, amely rögzíti az AD-fiók létrehozása művelet válaszát. Ez az esemény az AD-ben létrehozott új objectGuid adja vissza, és a kiépítési szolgáltatásban a TargetAnchor attribútumként van beállítva.

  [![SCIM hozzáadása](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>A kezelői frissítési műveletek naplóinak ismertetése

A Manager attribútum az AD egyik hivatkozási attribútuma. A kiépítési szolgáltatás nem állítja be a felettes attribútumot a felhasználói létrehozási művelet részeként. Ehelyett a Manager-attribútum egy *frissítési* művelet részeként van beállítva, miután létrejött az Active Directory-fiók a felhasználó számára. A fenti példa kibővítésével tegyük fel, hogy a "21451" alkalmazotti AZONOSÍTÓval rendelkező új bérlet aktív, és az új bérlet kezelőjének (*21023*) már van egy ad-fiókja. Ebben a forgatókönyvben a 21451-es felhasználóhoz tartozó naplók keresése 5 bejegyzést mutat be.

  [![Manager frissítése](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

Az első 4 rekord például a felhasználói létrehozási művelet részeként feltárt. Az 5. rekord a Manager-attribútum frissítéséhez társított exportálás. A napló rekord az AD Account Manager frissítési műveletének eredményét jeleníti meg, amelyet a rendszer a kezelő *ObjectGUID* attribútumával hajt végre.

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

### <a name="resolving-commonly-encountered-errors"></a>Gyakran előforduló hibák elhárítása

Ez a szakasz gyakran észlelt hibákat tartalmaz a munkanapokat használó felhasználók üzembe helyezésével és megoldásával kapcsolatban. A hibák a következőképpen vannak csoportosítva:

* [Kiépítési ügynök hibái](#provisioning-agent-errors)
* [Csatlakozási hibák](#connectivity-errors)
* [AD felhasználói fiókok létrehozásával kapcsolatos hibák](#ad-user-account-creation-errors)
* [Az AD felhasználói fiók frissítésével kapcsolatos hibák](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Kiépítési ügynök hibái

|#|Hiba forgatókönyv |Lehetséges okok|Ajánlott megoldás|
|--|---|---|---|
|1.| Hiba történt a kiépítési ügynök telepítésekor: a (z *) "Microsoft Azure ad kapcsolódási ügynökhöz (AADConnectProvisioningAgent)" szolgáltatás nem indult el. Ellenőrizze, hogy rendelkezik-e megfelelő jogosultságokkal a rendszer elindításához.* | Ez a hiba általában akkor jelenik meg, ha a kiépítési ügynököt tartományvezérlőre próbálja telepíteni, és a csoportházirend megakadályozza a szolgáltatás indulását.  Azt is láthatja, hogy az ügynök egy korábbi verziója fut-e, és még nem távolította el az új telepítés megkezdése előtt.| Telepítse a kiépítési ügynököt egy nem TARTOMÁNYVEZÉRLŐi kiszolgálóra. Az új ügynök telepítése előtt győződjön meg arról, hogy az ügynök korábbi verziói el lesznek távolítva.|
|2.| A Windows-szolgáltatás "Microsoft Azure AD-kapcsolat létesítési ügynöke" *kezdő* állapotban van, és nem a *futó* állapotra vált. | A telepítés részeként az ügynök varázsló létrehoz egy helyi fiókot (**NT Service\\AADConnectProvisioningAgent**) a kiszolgálón, és ez a szolgáltatás indításához használt bejelentkezési fiók. Ha a Windows-kiszolgálón egy biztonsági házirend megakadályozza, hogy a helyi fiókok futtassák a szolgáltatásokat, akkor ezt a hibát fogja tapasztalni. | Nyissa meg a *szolgáltatások konzolt*. Kattintson a jobb gombbal a Windows-szolgáltatás "Microsoft Azure AD kapcsolat létesítési ügynöke" elemre, és a bejelentkezés lapon adja meg a szolgáltatás futtatásához szükséges tartományi rendszergazda fiókját. Indítsa újra a szolgáltatást. |
|3.| Ha a létesítési ügynököt az AD-tartományhoz konfigurálja a *csatlakozás Active Directory*lépésben, a varázsló hosszú időt vesz igénybe az ad-séma betöltésére, és végül időtúllépést okoz. | Ez a hiba általában akkor jelentkezik, ha a varázsló tűzfalproblémák miatt nem tud csatlakozni az AD tartományvezérlői kiszolgálóhoz. | A Active Directory-varázsló *kapcsolódása* képernyőn, miközben megadja az ad-tartományhoz tartozó hitelesítő adatokat, a *tartományvezérlő prioritásának kiválasztása*lehetőségre van szükség. Ezzel a beállítással kiválaszthatja azt a tartományvezérlőt, amely ugyanabban a helyen található, mint az ügynök kiszolgálója, és gondoskodhat arról, hogy ne legyenek tűzfalszabályok blokkolja a kommunikációt. |

#### <a name="connectivity-errors"></a>Csatlakozási hibák

Ha a létesítési szolgáltatás nem tud csatlakozni a munkanapokhoz vagy a Active Directoryhoz, a kiépítés a karanténba helyezett állapotba léphet. A kapcsolódási problémák elhárításához használja az alábbi táblázatot.

|#|Hiba forgatókönyv |Lehetséges okok|Ajánlott megoldás|
|--|---|---|---|
|1.| Ha a **kapcsolat tesztelése**gombra kattint, a következő hibaüzenet jelenik meg: *hiba történt a Active Directoryhoz való csatlakozáskor. Győződjön meg arról, hogy a helyszíni kiépítési ügynök fut, és a megfelelő Active Directory tartománnyal van konfigurálva.* | Ez a hiba általában akkor jelenik meg, ha a kiépítési ügynök nem fut, vagy egy tűzfal blokkolja az Azure AD és a kiépítési ügynök közötti kommunikációt. Ezt a hibát akkor is láthatja, ha a tartomány nincs konfigurálva az ügynök varázslóban. | Nyissa meg a *szolgáltatások* konzolt a Windows Serveren, és győződjön meg arról, hogy az ügynök fut. Nyissa meg a létesítési ügynök varázslót, és ellenőrizze, hogy a megfelelő tartomány regisztrálva van-e az ügynökben.  |
|2.| A kiépítési feladatok a hétvégén (Pén-Szo) a karantén állapotba kerülnek, és e-mailben értesítést küldünk arról, hogy hiba történt a szinkronizálás során. | A hiba egyik gyakori oka a Workday tervezett állásideje. Ha Ön Workday megvalósítási bérlőt használ, vegye figyelembe, hogy a Workday megvalósítási bérlőihez állásidő van ütemezve hétvégére (általában péntek estétől szombat reggelig), és ebben az időszakban a Workday kiépítési alkalmazások karanténba helyezett állapotba léphetnek, mivel nem tudnak csatlakozni a Workdayhez. Amint a Workday megvalósítási bérlője újra elérhetővé válik, visszaállnak normál állapotba. Ritka esetekben ez a hibaüzenet akkor is megjelenhet, ha az integrációs rendszer felhasználójának jelszava megváltozott a bérlő frissítése miatt, vagy ha a fiók zárolva lett, illetve lejárt. | Beszéljen Workday-rendszergazdájával vagy integrációs partnerével, hogy megtudja, mikorra van ütemezve a Workday állásideje, így figyelmen kívül hagyhatja a figyelmeztető üzeneteket a leállás ideje alatt, és meggyőződhet a rendelkezésre állásról, amint a Workday-példány újra elérhető.  |


#### <a name="ad-user-account-creation-errors"></a>AD felhasználói fiókok létrehozásával kapcsolatos hibák

|#|Hiba forgatókönyv |Lehetséges okok|Ajánlott megoldás|
|--|---|---|---|
|1.| A naplózási hibák exportálása a naplóba hibaüzenet *: OperationsError-SvcErr: működési hiba történt. Nem lett konfigurálva kiváló hivatkozás a címtárszolgáltatások számára. A címtárszolgáltatás ezért nem tudja kiadni az átirányításokat az erdőn kívüli objektumokra.* | Ez a hiba általában akkor jelenik meg, ha a *Active Directory-tároló* szervezeti egysége helytelenül van beállítva, vagy ha problémák merülnek fel a *parentDistinguishedName*használt kifejezés-hozzárendeléssel kapcsolatban. | Az elíráshoz keresse meg a *Active Directory Container* ou paramétert. Ha a *parentDistinguishedName* elemet használja az attribútumleképezésben, győződjön meg arról, hogy mindig egy AD-tartományon belüli ismert tárolóba adja vissza az értékeket. A generált érték megjelenítéséhez tekintse meg az *Exportálás* eseményt a naplókban. |
|2.| Művelet-meghibásodások exportálása a naplóban hibakód: *SystemForCrossDomainIdentityManagementBadResponse* és üzenet *: ConstraintViolation-AtrErr: a kérelemben szereplő érték érvénytelen. Az attribútum értéke nem az elfogadható tartományba esik. \nError részletei: CONSTRAINT_ATT_TYPE – vállalat*. | Habár ez a hiba a *vállalati* attribútumra jellemző, ezt a hibát más attribútumok, például a *CN* is láthatja. Ez a hiba az AD által kényszerített séma megkötése miatt jelenik meg. Alapértelmezés szerint az AD-ben a *vállalat* és a *CN* -hez hasonló attribútumok felső határértéke 64 karakter. Ha a munkanaptól érkező érték több mint 64 karakterből áll, akkor ez a hibaüzenet jelenik meg. | Ellenőrizze az *Exportálás* eseményt a naplókban, hogy megjelenjen-e a hibaüzenetben jelentett attribútum értéke. Érdemes lehet a munkanapokból származó értéket a [Mid](../manage-apps/functions-for-customizing-application-data.md#mid) függvénnyel lerövidíteni, vagy a leképezéseket egy olyan ad-attribútumra módosítani, amely nem rendelkezik hasonló hosszúságú korlátozásokkal.  |

#### <a name="ad-user-account-update-errors"></a>Az AD felhasználói fiók frissítésével kapcsolatos hibák

Az Active Directory felhasználói fiókjának frissítési folyamata során a kiépítési szolgáltatás a munkanapokon és az AD-ben is beolvassa az adatokat, futtatja az attribútum-leképezési szabályokat, és meghatározza, hogy a módosítások életbe lépjenek. Ennek megfelelően a rendszer elindítja a frissítési eseményt. Ha bármelyik lépés hibát észlel, akkor a rendszer naplózza a naplókban. A gyakori frissítési hibák elhárításához használja az alábbi táblázatot.

|#|Hiba forgatókönyv |Lehetséges okok|Ajánlott megoldás|
|--|---|---|---|
|1.| A szinkronizálási szabály műveleti hibák a naplóban a következő üzenettel: *EventName = EntrySynchronizationError és ErrorCode = EndpointUnavailable*. | Ez a hiba akkor jelenik meg, ha a kiépítési szolgáltatás nem tudja lekérni a felhasználói profil adatait a Active Directory miatt, mert a helyszíni kiépítési ügynök feldolgozási hibát észlelt. | Tekintse meg a kiépítési ügynököt, Eseménynapló naplózza az olvasási művelettel kapcsolatos problémákat jelző hibaüzeneteket (szűrés eseményazonosító alapján #2). |
|2.| Az Active Directory Manager-attribútuma nem frissül az AD egyes felhasználói számára. | A hiba legvalószínűbb oka az, ha hatóköri szabályokat használ, és a felhasználó felettese nem része a hatókörnek. Ezt a problémát akkor is futtathatja, ha a felettes egyező azonosító attribútuma (például Alkalmazottkód) nem található a cél AD-tartományban, vagy nem a megfelelő értékre van állítva. | Tekintse át a hatóköri szűrőt, és adja hozzá a kezelő felhasználót a hatókörben. Ellenőrizze a felettes profilját az Active Directoryban, és ellenőrizze, hogy van-e érték a megfelelő azonosító attribútumhoz. |

## <a name="managing-your-configuration"></a>A konfiguráció kezelése

Ez a szakasz azt ismerteti, hogyan lehet tovább bővíteni, testreszabni és felügyelni a munkanapokon alapuló felhasználók kiépítési konfigurációját. A következő témaköröket tartalmazza:

* [A munkanap felhasználói attribútumaik listájának testreszabása](#customizing-the-list-of-workday-user-attributes)  
* [A konfiguráció exportálása és importálása](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>A munkanap felhasználói attribútumaik listájának testreszabása

A Active Directory és az Azure AD-hoz készült munkanapokat kiépítő alkalmazások is tartalmazzák a munkanapokhoz tartozó felhasználói attribútumok alapértelmezett listáját, amelyet választhat. Ezek a felsorolások azonban nem átfogóak. A munkanap számos száz lehetséges felhasználói attribútumot támogat, amelyek lehetnek standard vagy egyediek a munkanapok bérlője számára.

Az Azure AD-kiépítési szolgáltatás lehetővé teszi a lista vagy a munkanap attribútum testreszabását, hogy tartalmazza a Human Resources API [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) műveletében elérhetővé tett attribútumokat.

Ennek a módosításnak a végrehajtásához a [munkanap Studio](https://community.workday.com/studio-download) használatával ki kell bontania a használni kívánt attribútumokat jelölő XPath-kifejezéseket, majd hozzá kell adnia őket a kiépítési konfigurációhoz a Azure Portal speciális attribútumok szerkesztőjének használatával.

**XPath-kifejezés beolvasása egy munkanap felhasználói attribútumhoz:**

1. Töltse le és telepítse a [munkanap Studio](https://community.workday.com/studio-download)alkalmazást. A telepítőhöz a munkanapokhoz tartozó közösségi fiókra lesz szüksége.

2. Töltse le a munkanap Human_Resources WSDL-fájlt erről az URL-címről: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Indítsa el a munkanap studiót.

4. A parancssorban válassza ki a **munkanap > test Web Service in Tester** (tesztelési lehetőség) lehetőséget.

5. Válassza a **külső**lehetőséget, majd válassza ki a 2. lépésben letöltött Human_Resources WSDL-fájlt.

    ![Munkanap Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Állítsa a **Location (hely** ) mezőt `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`értékre, de cserélje le a "IMPL-CC" értéket a tényleges példány típusával, a "Bérlővel" pedig a valódi bérlő nevét.

7. **Művelet** beállítása **Get_Workersre**

8.  Kattintson a kis **Konfigurálás** hivatkozásra a kérelem/válasz ablaktáblán a munkanap hitelesítő adatainak megadásához. Győződjön meg a **hitelesítésről**, majd adja meg a munkanap-integrációs rendszer fiókjához tartozó felhasználónevet és jelszót. Ügyeljen arra, hogy a felhasználónevet\@bérlőként formázza, és hagyja bejelölve a **WS-Security UsernameToken** beállítást.

    ![Munkanap Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. Kattintson az **OK** gombra.

10. A **kérelem** ablaktáblában illessze be az alábbi XML-fájlt, és állítsa be **Employee_IDt** egy valós felhasználó alkalmazotti azonosítójára a munkahelye bérlője számára. Válasszon ki egy olyan felhasználót, aki rendelkezik a kinyerni kívánt attribútummal.

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

11. A parancs végrehajtásához kattintson a **küldési kérelemre** (zöld nyíl). Ha a művelet sikeres, a válasznak szerepelnie kell a **Válasz** ablaktáblában. Ellenőrizze a választ, hogy rendelkezik-e a megadott felhasználói AZONOSÍTÓval, és ne legyen hiba.

12. Ha a művelet sikeres, másolja az XML-fájlt a **Válasz** ablaktábláról, és mentse XML-fájlként.

13. A munkanap Studio menüsávján válassza a **fájl > fájl megnyitása..** . lehetőséget, majd nyissa meg a mentett XML-fájlt. Ez a művelet megnyitja a fájlt a munkanap Studio XML-szerkesztőjében.

    ![Munkanap Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. A fájl faszerkezetében navigáljon a **/env: boríték > env: Body > WD: Get_Workers_Response > WD: Response_Data > WD: Worker** a felhasználó adatai megkereséséhez.

15. A **WD: Worker**alatt keresse meg a hozzáadni kívánt attribútumot, és válassza ki.

16. Másolja ki a kijelölt attribútum XPath-kifejezését a **dokumentum elérési útja** mezőből.

17. Távolítsa el a **/env: boríték/env: Body/WD: Get_Workers_Response/WD: Response_Data/** előtagot a másolt kifejezésből.

18. Ha a másolt kifejezés utolsó eleme egy csomópont (például: "/WD: Birth_Date"), akkor fűzze hozzá a **/text ()** kifejezést a kifejezés végén. Erre nincs szükség, ha az utolsó elem egy attribútum (például: "/@wd: típus").

19. Az eredménynek olyannak kell lennie, mint a `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Ezt az értéket másolja a Azure Portalba.

**Egyéni munkanap felhasználói attribútum hozzáadása a létesítési konfigurációhoz:**

1. Indítsa el a [Azure Portalt](https://portal.azure.com), és navigáljon a munkaidő-kiépítési alkalmazás üzembe helyezési szakaszához az oktatóanyag korábbi részében leírtak szerint.

2. Állítsa ki a **kiépítési állapotot** **kikapcsolva**értékre, majd válassza a **Mentés**lehetőséget. Ez a lépés segít biztosítani, hogy a módosítások csak akkor lépnek érvénybe, ha készen áll.

3. A **leképezések**területen válassza **a munkavégző munkatársak szinkronizálása a helyszíni Active Directory** (vagy a **munkanapokon dolgozók szinkronizálása az Azure ad**-be) lehetőséget.

4. Görgessen a következő képernyő aljára, és válassza a **Speciális beállítások megjelenítése**lehetőséget.

5. Válassza **az attribútumok szerkesztése munkanapokhoz**elemet.

    ![Munkanap Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Görgessen az attribútumok listájának aljára, ahol a beviteli mezők szerepelnek.

7. A **név**mezőben adja meg az attribútum megjelenítendő nevét.

8. A **Típus mezőben**válassza ki a megfelelő típust, amely megfelel az attribútumnak (a**karakterlánc** a leggyakoribb).

9. Az **API-kifejezés**mezőben adja meg a munkanap studióból másolt XPath-kifejezést. Például: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Válassza az **attribútum hozzáadása**elemet.

    ![Munkanap Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Válassza a fenti **Mentés** , majd az **Igen** lehetőséget a párbeszédpanelre. Ha még meg van nyitva, az attribútum-leképezési képernyő bezárásához.

12. A fő **kiépítés** lapon válassza a **munkavégző munkatársak szinkronizálása a helyszínen Active Directory** (vagy a **munkatársak szinkronizálása az Azure ad**-be) lehetőséget.

13. Válassza az **Új leképezés hozzáadása**lehetőséget.

14. Az új attribútumnak ekkor meg kell jelennie a **forrás attribútum** listában.

15. Szükség szerint adjon hozzá egy leképezést az új attribútumhoz.

16. Ha elkészült, ne felejtse el visszaállítani a **kiépítési állapotot** **a be** értékre, és mentse.

### <a name="exporting-and-importing-your-configuration"></a>Konfiguráció exportálása és importálása

A [kiépítési konfiguráció exportálásával és importálásával](../manage-apps/export-import-provisioning-configuration.md) kapcsolatban tekintse meg a cikket.

## <a name="managing-personal-data"></a>Személyes adatok kezelése

A Active Directory munkahelyhez való kiépítési megoldásához egy üzembe helyezési ügynököt kell telepíteni egy helyszíni Windows Serverre, és ez az ügynök naplókat hoz létre a Windows-eseménynaplóban, amely a munkanaptól az AD-attribútumtól függően személyes adatait is tartalmazhatja. hozzárendelések. A felhasználói adatvédelmi kötelezettségek betartása érdekében gondoskodhat arról, hogy az eseménynaplókban ne őrizzen meg adatokat az 48 órán túli adatnaplóban egy Windows ütemezett feladat beállításával az Eseménynapló törléséhez.

Az Azure AD-kiépítési szolgáltatás a GDPR-besorolás **adatfeldolgozó** kategóriába tartozik. Adatfeldolgozó folyamatként a szolgáltatás adatfeldolgozási szolgáltatásokat biztosít a legfontosabb partnerek és a végfelhasználók számára. Az Azure AD kiépítési szolgáltatás nem hoz létre felhasználói adatokat, és nincs független vezérlése a személyes adatok gyűjtésének és felhasználásának módjától. Az Azure AD-létesítési szolgáltatás Adatlekérdezési, összesítési, elemzési és jelentéskészítési adatai a meglévő vállalati adatokon alapulnak.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

Az adatmegőrzés tekintetében az Azure AD-létesítési szolgáltatás nem hoz létre jelentéseket, elemzéseket végez, vagy 30 napon belül nem nyújt betekintést. Ezért az Azure AD kiépítési szolgáltatás 30 napon belül nem tárolja, dolgozza fel és nem őrzi meg az összes adatát. Ez a kialakítás megfelel a GDPR-szabályozásoknak, a Microsoft adatvédelmi rendelkezéseinek és az Azure AD adatmegőrzési szabályzatának.

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)
* [Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a munkanap és a Azure Active Directory között](workday-tutorial.md)
* [Ismerje meg, hogyan integrálhat más SaaS-alkalmazásokat a Azure Active Directory](tutorial-list.md)
* [Ismerje meg, hogyan használhatja a Microsoft Graph API-kat a kiépítési konfigurációk kezeléséhez](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
