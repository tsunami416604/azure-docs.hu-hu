---
title: 'Oktatóanyag: A felhasználók automatikus átadása az Azure Active Directoryval Workday konfigurálása |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének Workday felhasználói fiókokat.
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
ms.date: 01/19/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73e5b081e85726a1fc78d92996846faa18ce616a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57897622"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása a Workday konfigurálása

Ez az oktatóanyag célja szemlélteti a lépéseket kell elvégeznie importálásához feldolgozó profilok Workday-ből az Active Directory és az Azure Active Directory visszaírással nem kötelező a Workday e-mail-címet.

## <a name="overview"></a>Áttekintés

A [-kiszolgáló üzembe helyezése az Azure Active Directory-felhasználó](../manage-apps/user-provisioning.md) integrálható a [Workday emberierőforrás API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) felhasználói fiókok üzembe helyezéséhez. Azure ad-ben Ez a kapcsolat engedélyezéséhez a következő felhasználó kiépítési munkafolyamatnak használja:

* **Az Active Directory felhasználók kiépítése** -kiválasztott kiépítése beállítja a felhasználók Workday-ből egy vagy több Active Directory-tartományok be.

* **Kizárólag felhőalapú Azure Active Directory felhasználók kiépítése** - forgatókönyvekben, ahol a helyszíni Active Directory nem használják, felhasználók bővítheti közvetlenül a Workday-ből az Azure Active Directory-kiszolgáló üzembe helyezése az Azure AD-felhasználót használatával.

* **E-mail-címek oldalán írni Workday** -kiszolgáló üzembe helyezése az Azure AD-felhasználót is írhat az Azure AD-felhasználók e-mail címét vissza Workday.

### <a name="what-human-resources-scenarios-does-it-cover"></a>Milyen emberi erőforrások forgatókönyvek terjed ki?

A Workday felhasználói kiépítési munkafolyamatokat az Azure AD-felhasználó kiépítési szolgáltatás által támogatott a következő emberi erőforrások és az identitás-életciklus felügyeleti forgatókönyvek automatizálását engedélyezése:

* **Új alkalmazottak felvétele** – egy új alkalmazotti Workday, való felvételekor egy felhasználói fiók automatikusan létrejön az Active Directory, Azure Active Directory, és igény szerint Office 365-höz és [Azure ADáltaltámogatottmásSaaS-alkalmazásokhoz](../manage-apps/user-provisioning.md), a késleltetve visszaírt a Workday e-mail-címe.

* **Alkalmazott attribútum és a profil frissítések** – amikor egy alkalmazotti rekordot a Workdayben frissítésekor (például a nevét, a cím vagy a kezelő), a felhasználói fiókjának automatikusan frissül az Active Directory, Azure Active Directory, és igény szerint Office 365-höz és [más SaaS-alkalmazásokhoz az Azure AD által támogatott](../manage-apps/user-provisioning.md).

* **Alkalmazott végződnek** – amikor egy alkalmazott megszakad a Workdayben, a felhasználói fiókjának automatikusan le van tiltva az Active Directory, Azure Active Directory, és igény szerint Office 365-höz és [más SaaS-alkalmazásokhoz az Azure által támogatott AD](../manage-apps/user-provisioning.md).

* **Alkalmazott rehires** – amikor egy alkalmazott van rehired WORKDAY, a régi fiókot automatikusan lehet aktiválni vagy újra létrehozni (rendelkezésére), hogy az Active Directory, Azure Active Directoryban, és igény szerint Office 365-höz és [más SaaS-alkalmazásokhoz az Azure AD által támogatott](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Akik esetén a felhasználó kiépítési megoldás ajánlott használhatók?

Ez a megoldás kiépítése Workday felhasználó jelenleg nyilvános előzetes verzióban érhető el, és ideális:

* Szervezetek számára, amelyek egy előre elkészített, felhőalapú megoldást Workday felhasználókiépítés igényel

* Szervezetek számára, amelyek közvetlenül a felhasználó Active Directory vagy az Azure Active Directory-kiépítés Workday-ből megkövetelése

* Szervezetek számára, amelyek a felhasználóknak ki kell építeni-adatok a Workday HCM modul kapott (lásd: [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Szervezetek számára, amely esetében a csatlakozás helyez át, és maradjon a felhasználók egy vagy több Active Directory-erdők szinkronizálja, tartományok és szervezeti egységek alapján csak a módosítsa információk a Workday HCM-modul észlelhető (lásd: [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Használja az Office 365 e-mailek szervezetek

## <a name="solution-architecture"></a>Megoldásarchitektúra

Ez a szakasz ismerteti a végpontok közötti felhasználó megoldásarchitektúra közös hibrid környezetek kiépítése. Nincsenek két kapcsolódó folyamatai:

* **Mérvadó HR adatfolyam – a helyszíni Active Directoryba a Workday-ből:** Ebből a folyamatból feldolgozó események (például új Hires, adatátvitel, felmondás) először fordulnak elő a felhőben Workday HR-bérlői és majd tovább az eseményadatokat az az Azure AD-n keresztül a helyszíni Active Directoryban és a kiépítési ügynök. Az eseményre, attól függően, vezethet létrehozása/frissítése/engedélyezését vagy letiltását az operations az ad-ben.
* **E-mailt a Jelszóvisszaíró folyamat – a Workdayből a helyszíni Active Directoryból:** A fiók létrehozásának befejezése után az Active Directoryban a keresztül az Azure AD Connect Azure AD-val szinkronizálva van, és az Active Directory származási e-mailek attribútum is visszaírását a Workday.

![Áttekintés](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Teljes körű felhasználói adatok folyamat

1. A HR-csapat feldolgozó tranzakciók (adatszolgáltatói/díjcsökkentések/kilépők vagy új felvettek/adatátvitel/felmondás) a Workday HCM hajt végre.
2. Az Azure AD-kiépítés szolgáltatás Workday HR-identitások ütemezett szinkronizálás fut, és azonosítja a módosításait, amelyeket fel kell dolgozni a szinkronizálás a helyszíni Active Directoryval kell.
3. Az Azure AD-kiépítés szolgáltatás a helyszíni AAD Connect kiépítési ügynök az AD-fiók létrehozása/frissítése/engedélyezés/letiltás műveletek tartalmazó, a kérelem hasznos adatai hív meg.
4. Az Azure AD Connect kiépítési ügynök adhat hozzá/frissíthet AD-fiókja adatait, szolgáltatás-fiókot használ.
5. Az Azure AD Connect / AD szinkronizálási motor fut. a különbözeti szinkronizálás AD-ben frissítéseket tölt.
6. Az Active Directory-frissítések az Azure Active Directoryval van szinkronizálva.
7. A Workday visszaírási összekötő úgy van beállítva, ha azt írási-készít biztonsági másolatot e-mail attribútuma a Workday, a használt egyező attribútum alapján.

## <a name="planning-your-deployment"></a>Az üzembe helyezésének megtervezése

Mielőtt megkezdené a Workday-integráció, az alábbi előfeltételek ellenőrzése, és olvassa el az aktuális Active Directory architektúrájának és követelmények kiépítése az Azure Active Directory által biztosított megoldások a felhasználó megfelelő az alábbi útmutatást. Átfogó [telepítési tervének](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) a tervezési munkalapok érhető el is segítséget nyújtanak a Workday-integrációs partner és a HR-közreműködők együtt.

Ez a szakasz ismerteti a tervezés következő aspektusait:

* [Előfeltételek](#prerequisites)
* [Üzembe helyezési összekötő alkalmazások üzembe helyezéséhez kiválasztása](#selecting-provisioning-connector-apps-to-deploy)
* [Az Azure AD Connect kiépítési ügynök telepítésének tervezése](#planning-deployment-of-azure-ad-connect-provisioning-agent)
* [Több Active Directory-tartományok integrálása](#integrating-with-multiple-active-directory-domains)
* [A Workday az Active Directory felhasználói attribútum hozzárendelésének és átalakítás megtervezése](#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)

### <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy Ön már rendelkezik a következőkkel:

* Egy érvényes Azure AD Premium P1 vagy magasabb szintű előfizetés globális rendszergazdai jogosultságokkal
* A Workday megvalósítási bérlői tesztelése és integrációs célokra
* Rendszergazdai engedélyekkel, hozzon létre egy rendszer-integrációs felhasználót, és módosítja a WORKDAY alkalmazotti adatokat tesztelési célú tesztelése
* Felhasználókiépítés az Active Directory, a kiszolgáló fut a Windows Server 2012-es vagy nagyobb .NET 4.7.1+ futtatókörnyezetben szükség gazdagépre a [helyszíni kiépítési ügynök](https://go.microsoft.com/fwlink/?linkid=847801)
* [Az Azure AD Connect](../hybrid/whatis-hybrid-identity.md) a felhasználók az Active Directory és az Azure AD közötti szinkronizálása

### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Üzembe helyezési összekötő alkalmazások üzembe helyezéséhez kiválasztása

Megkönnyítése érdekében a Workday és az Active Directory közötti kiépítési munkafolyamatokat, az Azure AD biztosít, amely az Azure AD-alkalmazásgyűjtemény adhat hozzá több üzembe helyezési összekötő alkalmazás:

![AAD-alkalmazás katalógusában](./media/workday-inbound-tutorial/wd_gallery.png)

* **Az Active Directory felhasználók átadásának WORKDAY** – Ez az alkalmazás lehetővé teszi a felhasználói fiók kiépítése Workday-ből egy egyetlen Active Directory-tartományhoz. Ha több tartományom van, az alkalmazás egy példányát az Azure AD-alkalmazásgyűjtemény minden egyes Active Directory-tartományban való létrehozásához szüksége származó is hozzáadhat.

* **Az Azure AD-felhasználó kiépítési WORKDAY** – AAD Connect pedig az eszköz, amellyel szinkronizálja az Active Directory-felhasználók számára az Azure Active Directory, az alkalmazás segítségével egyetlen Azure-bA Workday-ből csak felhőbeli felhasználók átadásának megkönnyítése Active Directory-bérlő.

* **A Jelszóvisszaíró WORKDAY** – Ez az alkalmazás lehetővé teszi a késleltetve visszaírt, a felhasználó e-mail-címeket az Azure Active Directoryból a Workday.

> [!TIP]
> A normál "Munkanap" alkalmazás beállítása az egyszeri bejelentkezés Workday és az Azure Active Directory közötti szolgál.

Az alábbi döntési folyamatábra segítségével azonosíthatja a Workday kiépítési alkalmazások kapcsolódnak a forgatókönyvhöz.
    ![Döntési folyamatábra](./media/workday-inbound-tutorial/wday_app_flowchart.png "döntési folyamatábra")

A tartalomjegyzék segítségével nyissa meg a megfelelő szakaszt az ebben az oktatóanyagban.

### <a name="planning-deployment-of-azure-ad-connect-provisioning-agent"></a>Az Azure AD Connect kiépítési ügynök telepítésének tervezése

> [!NOTE]
> Ez a szakasz fontos csak akkor, ha azt tervezi, hogy a Workday az Active Directory felhasználói kiépítés alkalmazás üzembe helyezése. Ezt kihagyhatja, ha helyezi üzembe a Workday visszaírási vagy a Workday az Azure AD-felhasználó kiépítési alkalmazás.

A Workday AD-felhasználók létrehozásának megoldáshoz telepíteni kell egy vagy több kiépítési ügynök Windows 2012 R2-t futtató kiszolgálókon vagy újabb legalább 4 GB RAM-MAL és a .NET-4.7.1+ futtatókörnyezetet. Az alábbi szempontokat a kiépítési ügynök telepítése előtt figyelembe kell venni:

* Győződjön meg arról, hogy a kiépítési ügynök futtató hálózati hozzáféréssel rendelkezik a céltartomány AD
* A kiépítési ügynök konfigurációs varázsló regisztrálja az ügynököt az Azure AD-bérlője, és a regisztrációs folyamat hozzáférést igényel a *. msappproxy.net 443-as SSL-porton keresztül. Győződjön meg arról, hogy kimenő tűzfalszabályokat, amelyek a kommunikáció helyezni. Az ügynökök által támogatott [kimenő HTTPS-proxy konfigurációs](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).
* A kiépítési ügynök a szolgáltatási fiók segítségével kommunikál a helyszíni AD-tartomány. Az ügynök a telepítés előtt javasoljuk, hogy a tartományi rendszergazdai engedélyekkel, és a egy jelszót, amely nem jár le hozzon létre egy szolgáltatásfiókot.  
* A kiépítési ügynök konfigurálása során kiválaszthatja a tartományvezérlőket, kezelje a kiépítési kérelmeket. Ha több, földrajzilag elosztott tartományvezérlő, a kiépítési ügynök telepítése az ugyanazon a helyen, mint x, megbízhatóságának és teljesítményének a teljes körű megoldás az elsődleges tartományvezérlőn zajlik
* Magas rendelkezésre állás érdekében egynél több kiépítési ügynök telepítése és regisztrálnia kell kezelni, ugyanazokat a helyszíni AD-tartományok.

> [!IMPORTANT]
> Éles környezetben a Microsoft azt javasolja, hogy rendelkezik-e legalább 3 kiépítés ügynökök az Azure AD-bérlőhöz, magas rendelkezésre állásúként konfigurálva.

### <a name="integrating-with-multiple-active-directory-domains"></a>Több Active Directory-tartományok integrálása

> [!NOTE]
> Ez a szakasz fontos csak akkor, ha azt tervezi, hogy a Workday az Active Directory felhasználói kiépítés alkalmazás üzembe helyezése. Ezt kihagyhatja, ha helyezi üzembe a Workday visszaírási vagy a Workday az Azure AD-felhasználó kiépítési alkalmazás.

Az Active Directory topológiájától függően kell dönthet arról, hogy a felhasználó kiépítése összekötő alkalmazások száma és a kiépítés ügynökök konfigurálásához. Az alábbiakban néhány gyakori üzembe helyezési mintázatokat tekintse meg az üzembe helyezés tervezése során.

#### <a name="deployment-scenario-1--single-workday-tenant---single-ad-domain"></a>Üzembe helyezés #1. forgatókönyv: Egybérlős Workday -> egyetlen AD-tartomány

Ebben a forgatókönyvben egy Workday-bérlővel rendelkezik, és konfigurálta a felhasználókat az Active Directory-tartománynak egy célt szeretné. Íme a javasolt termelési konfigurációját a központi telepítés.

|   |   |
| - | - |
| Nem. az ügynökök telepítéséhez kiépítése a helyszíni | 3 (a magas rendelkezésre állás és a feladatátvételi) |
| Nem. az AD felhasználó kiépítési alkalmazások konfigurálása az Azure Portalon munkanap | 1 |

  ![1. forgatókönyv](./media/workday-inbound-tutorial/dep_scenario1.png)

#### <a name="deployment-scenario-2--single-workday-tenant---multiple-child-ad-domains"></a>Üzembe helyezés #2. forgatókönyv: Egybérlős Workday -> több gyermek AD-tartomány

Ebben a forgatókönyvben magában foglalja a felhasználók Workday-ből az erdő több cél AD gyermektartományokat kiépítése. Íme a javasolt termelési konfigurációját a központi telepítés.

|   |   |
| - | - |
| Nem. az ügynökök telepítéséhez kiépítése a helyszíni | 3 (a magas rendelkezésre állás és a feladatátvételi) |
| Nem. az AD felhasználó kiépítési alkalmazások konfigurálása az Azure Portalon munkanap | one app per child domain |

  ![2. forgatókönyv](./media/workday-inbound-tutorial/dep_scenario2.png)

#### <a name="deployment-scenario-3--single-workday-tenant---disjoint-ad-forests"></a>Üzembe helyezés #3. forgatókönyv: Egybérlős Workday -> különálló AD-erdőhöz

Ebben a forgatókönyvben magában foglalja a felhasználók Workday-ből a különálló AD-erdőben lévő tartományok kiépítése. Íme a javasolt termelési konfigurációját a központi telepítés.

|   |   |
| - | - |
| Nem. az ügynökök telepítéséhez kiépítése a helyszíni | 3 / különálló AD-erdőhöz |
| Nem. az AD felhasználó kiépítési alkalmazások konfigurálása az Azure Portalon munkanap | one app per child domain |

  ![3. forgatókönyv](./media/workday-inbound-tutorial/dep_scenario3.png)

### <a name="planning-workday-to-active-directory-user-attribute-mapping-and-transformations"></a>A Workday az Active Directory felhasználói attribútum hozzárendelésének és átalakítás megtervezése

> [!NOTE]
> Ez a szakasz fontos csak akkor, ha azt tervezi, hogy a Workday az Active Directory felhasználói kiépítés alkalmazás üzembe helyezése. Ezt kihagyhatja, ha helyezi üzembe a Workday visszaírási vagy a Workday az Azure AD-felhasználó kiépítési alkalmazás.

Active Directory-tartományhoz történő felhasználókiépítés konfigurálása, előtt fontolja meg az alábbi kérdésekre. Az ezekre a kérdésekre adott válaszokat meghatározza, hogyan a Hatókörszűrő és attribútumleképezések meg kell adnia.

* **Mi a felhasználók WORKDAY szükség lesz az Active Directory erdőben?**

  * *Példa: Felhasználók, ahol a Workday "Vállalati" attribútum tartalmazza a "Contoso" értéket, és a "Worker_Type" attribútum tartalmazza a "Regular"*

* **Hogyan legyenek átirányítva a felhasználók be különböző szervezeti egységben (OU)?**

  * *Példa: A Workday "Település" és "Country_Region_Reference" attribútumok meghatározott felhasználók kapcsolódóak pedig az a szervezeti egységek, amelyek egy office-hely*

* **Hogyan kell a következő attribútumokkal kell feltöltenie az Active Directory?**

  * Köznapi név (cn)
    * *Példa: Emberi erőforrások által beállított a Workday User_ID érték használata*

  * Alkalmazott azonosítója (employeeId)
    * *Példa: A Workday Worker_ID érték használata*

  * SAM-fiók neve (sAMAccountName)
    * *Példa: Használja a Workday a USER_ID paraméter értékét értéket, a szűrt keresztül az Azure AD létesítési kifejezés érvénytelen karakterek eltávolítása*

  * Egyszerű felhasználónév (userPrincipalName)
    * *Példa: A Workday User_ID érték használata egy Azure AD létesítési kifejezés hozzáfűzni tartománynév*

* **Hogyan felhasználók megfelelő Workday és Active Directory között?**

  * *Példa: Felhasználók egy adott Workday "Worker_ID" értéket a rendszer összehasonlítja az Active Directory-felhasználók, ahol a "employeeID" ugyanazzal az értékkel rendelkezik. Ha a Worker_ID érték nem található az Active Directoryban, hozzon létre egy új felhasználót.*
  
* **Az Active Directory-erdő már tartalmaz a felhasználó működéséhez szükséges a megfelelő logikai azonosítók?**

  * *Példa: Ha a beállítás új Workday központi telepítést, javasoljuk, hogy az Active Directory előre ki lesz töltve a megfelelő Workday Worker_ID értékek (vagy egyedi azonosító értéket választott), hogy a megfelelő logikai lehető legegyszerűbb legyen.*

Állítsa be, és speciális üzembe helyezési összekötő alkalmazások konfigurálása az ebben az oktatóanyagban a fennmaradó részéből tárgysora. Mely alkalmazások konfigurációjától függ, a környezetében lévő bérlők kiépítése, és hány Active Directory-tartományok és az Azure AD kell rendszerek.

## <a name="configure-integration-system-user-in-workday"></a>A Workday integrációs rendszer felhasználó konfigurálása

Általános követelmény a kiépítés összekötők minden munkanap, hogy szükségük van-e csatlakozni a Workday emberierőforrás API Workday integrációs rendszer felhasználó hitelesítő adatait. Ez a szakasz a WORKDAY-integrációs rendszer felhasználó létrehozását ismerteti, és rendelkezik a következő szakaszok:

* [Egy integrációs rendszer felhasználó létrehozása](#creating-an-integration-system-user)
* [Egy integrációs biztonsági csoport létrehozása](#creating-an-integration-security-group)
* [Tartományi biztonsági házirend-engedélyek konfigurálása](#configuring-domain-security-policy-permissions)
* [Üzleti folyamat biztonsági házirendet engedélyeinek konfigurálása](#configuring-business-process-security-policy-permissions)
* [Biztonsági házirend módosításai aktiválása](#activating-security-policy-changes)

> [!NOTE]
> Akkor lehet kerülni ezt az eljárást, és Ehelyett használjon Workday globális rendszergazdai fiókot a rendszer-integrációs fiókot. Előfordulhat, hogy jól működnek a bemutatókat, de nem javasolt éles környezetekben üzemelő példányok.

### <a name="creating-an-integration-system-user"></a>Egy integrációs rendszer felhasználó létrehozása

**Egy integrációs rendszer felhasználó létrehozása:**

1. Jelentkezzen be a Workday-bérlői rendszergazdai fiók használatával. Az a **Workday alkalmazás**, adja meg felhasználó létrehozása a keresési mezőbe, és kattintson a **integrációs rendszer felhasználó létrehozása**.

    ![Felhasználó létrehozása](./media/workday-inbound-tutorial/wd_isu_01.png "felhasználó létrehozása")
2. Végezze el a **integrációs rendszer felhasználó létrehozása** feladat egy új integrációs rendszer felhasználó a felhasználónév és jelszó megadásával.  
  
* Hagyja a **új jelszó megkövetelése a következő bejelentkezés** beállítás nincs bejelölve, mert ez a felhasználó követően a naplózandó programozott módon.
* Hagyja a **munkamenet időkorlátja perc** az az alapértelmezett érték 0, amely megakadályozza, hogy a felhasználói munkamenet időtúllépés miatt megszakadt.
* Válassza a **tegye nem engedélyezése felhasználói felület munkamenetek** egy biztonsági réteggel is, amely megakadályozza, hogy az integrációs rendszer a jelszóval rendelkező felhasználó bejelentkezik a Workday biztosít.

    ![Hozzon létre a rendszer-felhasználói integrációs](./media/workday-inbound-tutorial/wd_isu_02.png "integrációs rendszer felhasználó létrehozása")

### <a name="creating-an-integration-security-group"></a>Egy integrációs biztonsági csoport létrehozása

Ebben a lépésben létrehoz egy korlátozás nélküli vagy korlátozott integrációs rendszerbiztonsági csoport WORKDAY, és rendelje hozzá az ehhez a csoporthoz az előző lépésben létrehozott integrációs rendszer felhasználót.

**Biztonsági csoport létrehozása:**

1. Adjon meg biztonsági csoport létrehozása a keresési mezőbe, és kattintson a **biztonsági csoport létrehozása**.

    ![CreateSecurity csoport](./media/workday-inbound-tutorial/wd_isu_03.png "CreateSecurity csoport")
2. Végezze el a **biztonsági csoport létrehozása** feladat. 

   * A Workday a biztonsági csoportok két típusa van:
     * **Nem korlátozott:** A biztonsági csoport minden tagja hozzáférhet a biztonsági csoport által védett minden adat-példányok.
     * **Korlátozott:** Biztonsági csoport összes tagját hozzáférése környezetfüggő adatok-példányok (sorok) férhet hozzá a biztonsági csoport egy részhalmazát.
   * Tekintse meg a Workday integrációs partnerrel válassza ki a megfelelő biztonsági csoport való integrációhoz.
   * Ha már tudja, hogy a csoport típusának, válassza ki a **integrációs rendszer biztonsági csoport (nem korlátozott lehetőséget választja)** vagy **integrációs rendszer biztonsági csoport (Constrained)** származó a **írja be a Bérlős biztonsági csoport**  legördülő listából.

     ![CreateSecurity csoport](./media/workday-inbound-tutorial/wd_isu_04.png "CreateSecurity csoport")

3. A sikeres biztonsági csoport létrehozása után megjelenik egy oldal, ahol hozzárendelheti a biztonsági csoport tagjai. Adja hozzá az új integrációs rendszer felhasználó e biztonsági csoporthoz az előző lépésben létrehozott. Ha használ *korlátozott* biztonsági csoport is szüksége lesz a megfelelő szervezeti hatókör kiválasztása.

    ![Biztonsági csoport szerkesztése](./media/workday-inbound-tutorial/wd_isu_05.png "biztonsági csoport szerkesztése")

### <a name="configuring-domain-security-policy-permissions"></a>Tartományi biztonsági házirend-engedélyek konfigurálása

Ebben a lépésben, fog "tartományi biztonság" szabályzat engedélyek megadása a feldolgozói adatok a biztonsági csoporthoz.

**Tartományi biztonsági házirend-engedélyek konfigurálása:**

1. Adja meg **tartományi biztonsági konfiguráció** a keresési mezőbe, és kattintson a hivatkozásra a **tartományi biztonsági konfigurációjának jelentése**.  

    ![Tartományi biztonsági szabályzatok](./media/workday-inbound-tutorial/wd_isu_06.png "tartományi biztonsági szabályzatok")  
2. Az a **tartomány** szövegbeviteli mezőben keresse meg a következő tartományok, és hozzáadhatja őket a szűrő egyenként.  
   * *Külső fiók kiépítése*
   * *Feldolgozó adatok: Nyilvános feldolgozó jelentések*
   * *Személy adatok: Munkahelyi kapcsolattartási adatok*
   * *Feldolgozó adatok: Minden helyzetben*
   * *Feldolgozó adatok: Aktuális személyzeti információk*
   * *Feldolgozó adatok: A feldolgozó profil üzleti címe*

     ![Tartományi biztonsági szabályzatok](./media/workday-inbound-tutorial/wd_isu_07.png "tartományi biztonsági szabályzatok")  

     ![Tartományi biztonsági szabályzatok](./media/workday-inbound-tutorial/wd_isu_08.png "tartományi biztonsági szabályzatok") 

     Kattintson az **OK** gombra.

3. A jelentésben, hogy megjelenik-e, válassza a három pontra (...), amely mellett megjelenik **külső fiók üzembe helyezésének** , majd kattintson a menüre **tartomány -> biztonsági szabályzat engedélyeinek szerkesztése**

    ![Tartományi biztonsági szabályzatok](./media/workday-inbound-tutorial/wd_isu_09.png "tartományi biztonsági szabályzatok")  

4. Az a **tartományi biztonsági házirend engedélyeinek szerkesztése** lapon görgessen le a szakasz **integrációs engedélyek**. Kattintson a "+" jel a integrációs rendszer csoport hozzáadása a biztonsági csoportok listájának a **első** és **Put** integrációs engedélyeket.

    ![Engedély szerkesztése](./media/workday-inbound-tutorial/wd_isu_10.png "engedély szerkesztése")  

5. Kattintson a "+" jel a integrációs rendszer csoport hozzáadása a biztonsági csoportok listájának a **első** és **Put** integrációs engedélyeket.

    ![Engedély szerkesztése](./media/workday-inbound-tutorial/wd_isu_11.png "engedély szerkesztése")  

6. Minden, a fennmaradó biztonsági házirendek ismételje meg a fenti 3 – 5:

   | Művelet | Tartományi biztonsági házirend |
   | ---------- | ---------- |
   | GET és Put | Feldolgozó adatok: Nyilvános feldolgozó jelentések |
   | GET és Put | Személy adatok: Munkahelyi kapcsolattartási adatok |
   | Lekérés | Feldolgozó adatok: Minden helyzetben |
   | Lekérés | Feldolgozó adatok: Aktuális személyzeti információk |
   | Lekérés | Feldolgozó adatok: A feldolgozó profil üzleti címe |

### <a name="configuring-business-process-security-policy-permissions"></a>Üzleti folyamat biztonsági házirendet engedélyeinek konfigurálása

Ebben a lépésben meg fogja engedélyezze "üzleti folyamat biztonsági" szabályzat a feldolgozó adatok a biztonsági csoporthoz. Ez a lépés akkor szükséges, a Workday visszaírási alkalmazás-összekötő beállításához.

**Üzleti folyamat biztonsági házirendet engedélyek beállításáról:**

1. Adja meg **üzleti folyamat házirend** a keresési mezőbe, és kattintson a hivatkozásra a **üzleti folyamat biztonsági házirend szerkesztése** feladat.  

    ![Üzleti folyamat biztonsági házirendek](./media/workday-inbound-tutorial/wd_isu_12.png "üzleti folyamat biztonsági szabályzatok")  

2. Az a **üzleti folyamat típusa** a keresési szövegmezőbe *forduljon* , és válassza ki **ügyfél módosítása** üzleti folyamat, és kattintson **OK**.

    ![Üzleti folyamat biztonsági házirendek](./media/workday-inbound-tutorial/wd_isu_13.png "üzleti folyamat biztonsági szabályzatok")  

3. Az a **üzleti folyamat biztonsági házirend szerkesztése** lapon, görgessen a **elérhetőségi adatok karbantartása (webszolgáltatás)** szakaszban.

    ![Üzleti folyamat biztonsági házirendek](./media/workday-inbound-tutorial/wd_isu_14.png "üzleti folyamat biztonsági szabályzatok")  

4. Válassza ki, és az új integrációs rendszerbiztonsági csoport hozzáadása a listához, amely a web services kérelem is kezdeményezhető a biztonsági csoportok. Kattintson a **kész**. 

    ![Üzleti folyamat biztonsági házirendek](./media/workday-inbound-tutorial/wd_isu_15.png "üzleti folyamat biztonsági szabályzatok")  

### <a name="activating-security-policy-changes"></a>Biztonsági házirend módosításai aktiválása

**Biztonsági házirend módosításai aktiválása:**

1. Adja meg az aktiválást a keresőmezőbe, majd kattintson a hivatkozásra **aktiválása folyamatban lévő biztonsági házirend módosításai**.

    ![Aktiválja](./media/workday-inbound-tutorial/wd_isu_16.png "aktiválása")

1. Adjon meg egy megjegyzést a naplózási célokra aktiválása folyamatban lévő biztonsági házirend módosításai feladat megkezdése, és kattintson a **OK**.
1. A jelölőnégyzet bejelölésével a következő képernyőn a feladat befejezéséhez **megerősítése**, és kattintson a **OK**.

    ![Függőben lévő biztonsági aktiválása](./media/workday-inbound-tutorial/wd_isu_18.png "függőben lévő biztonsági aktiválása")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Felhasználókiépítés konfigurálása Workday-ből az Active Directoryhoz

Ez a szakasz ismerteti a felhasználói fiók kiépítése Workday-ből az integráció hatókörén belül minden egyes Active Directory-tartományhoz.

* [Telepítse és konfigurálja a helyszíni kiépítés ügynökök](#part-1-install-and-configure-on-premises-provisioning-agents)
* [Az üzembe helyezési összekötő alkalmazás felvétele és a Workday-kapcsolat](#part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Attribútum-leképezések konfigurálása](#part-3-configure-attribute-mappings)
* [Engedélyezze, és indítsa el a felhasználók átadása](#enable-and-launch-user-provisioning)

### <a name="part-1-install-and-configure-on-premises-provisioning-agents"></a>1. rész: Telepítse és konfigurálja a helyszíni kiépítés ügynökök

Hozza létre a helyszíni Active Directory, az ügynök egy kiszolgálóra, amelyen a .NET-keretrendszer 4.7.1+ és a hálózati hozzáférést a kívánt Active Directory tartomány(ok) kell telepíthető.

> [!TIP]
> A kiszolgálón az utasításokat követve ellenőrizheti a .NET-keretrendszer verziója [Itt](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Ha a kiszolgáló nem rendelkezik .NET 4.7.1 vagy újabb verziója van telepítve, letöltheti a [Itt](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Miután telepítette a .NET 4.7.1+, letöltheti a **[helyszíni Itt a kiépítési ügynök](https://go.microsoft.com/fwlink/?linkid=847801)** kövesse a lépéseket az alábbiakban az ügynök konfigurálásának befejezéséhez.

1. Jelentkezzen be a Windows Server, ahol az új ügynököt telepíteni szeretné.
2. Indítsa el a kiépítési ügynök telepítőjét, fogadja el a feltételeket, majd kattintson a a **telepítése** gombra.

   ![Telepítse a képernyő](./media/workday-inbound-tutorial/pa_install_screen_1.png "képernyő telepítése")
3. Miután a telepítés befejeződött, a varázsló elindul, és látni fogja a **az Azure AD Connect** képernyő. Kattintson a **hitelesítés** gomb az Azure AD-példányhoz való csatlakozáshoz.

   ![Az Azure AD Connect](./media/workday-inbound-tutorial/pa_install_screen_2.png "az Azure AD Connect")
1. Az Azure AD-példány globális rendszergazdai hitelesítő adatok használatával hitelesíteni.

   ![Rendszergazdai Auth](./media/workday-inbound-tutorial/pa_install_screen_3.png "rendszergazdai hitelesítés")

> [!NOTE]
> Az Azure AD rendszergazdai hitelesítő adatok csak az Azure AD-bérlőhöz csatlakozni szolgál. Az ügynök nem tárolja a hitelesítő adatok helyben a kiszolgálón.

1. Az Azure ad-vel a sikeres hitelesítés után látni fogja a **Connect Active Directory** képernyő. Ebben a lépésben adja meg az AD-tartomány nevét, majd kattintson a a **könyvtár hozzáadása** gombra.

   ![Könyvtár hozzáadása](./media/workday-inbound-tutorial/pa_install_screen_4.png "könyvtár hozzáadása")
  
1. Most már a az AD-tartományhoz való csatlakozáshoz szükséges hitelesítő adatok megadását kéri. Ugyanazon a képernyőn, használhatja a **válassza ki a tartományt vezérlő prioritású** , adja meg a tartományvezérlők, amelyet az ügynök kiépítési kérésekkel kell használnia.

   ![Tartományi hitelesítő adatok](./media/workday-inbound-tutorial/pa_install_screen_5.png)
1. Miután a tartományhoz, a telepítő konfigurált tartományok listáját jeleníti meg. Ezen a képernyőn, ismételje meg a #5 és továbbiak hozzáadásához #6. lépés tartományok vagy kattintson a **tovább** lépjen az ügynök regisztrálása.

   ![A konfigurált tartományok](./media/workday-inbound-tutorial/pa_install_screen_6.png "konfigurált tartományok")

   > [!NOTE]
   > Ha több AD-tartomány (pl. na.contoso.com, emea.contoso.com), majd vegyen fel minden tartományhoz külön-külön a listához. Nem elegendő a csak hozzáadás, a szülőtartomány (például contoso.com) áll. Az ügynök minden egyes gyermektartomány regisztrálnia kell.
1. A konfigurációs adatokat, és kattintson a **megerősítése** regisztrálja az ügynököt.
  
   ![Erősítse meg a képernyő](./media/workday-inbound-tutorial/pa_install_screen_7.png "megerősítése képernyő")
1. A konfigurációs varázsló az ügynök regisztrációját állapotát jeleníti meg.
  
   ![Az ügynök regisztrálása](./media/workday-inbound-tutorial/pa_install_screen_8.png "az ügynök regisztrálása")
1. Ha az ügynök regisztrálása sikeresen megtörtént, kattintson a **kilépéshez** a varázslóból való kilépéshez.
  
   ![Lépjen ki a képernyő](./media/workday-inbound-tutorial/pa_install_screen_9.png "lépjen ki a képernyő")
1. Az ügynök telepítésének ellenőrzése és ellenőrizze, hogy nyissa meg a "Szolgáltatások" beépülő modul fut, és keresse meg a "Microsoft Azure AD Connect kiépítési ügynök" nevű szolgáltatást
  
   ![Szolgáltatások](./media/workday-inbound-tutorial/services.png)

### <a name="part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>2. rész: Az üzembe helyezési összekötő alkalmazás felvétele és a Workday-kapcsolat

**Az Active Directory-kiépítés konfigurálása a Workday:**

1. Nyissa meg a következőt: <https://portal.azure.com>

2. A bal oldali navigációs sávon válassza **Azure Active Directory**

3. Válassza ki **vállalati alkalmazások**, majd **minden alkalmazás**.

4. Válassza ki **alkalmazás hozzáadása**, és válassza ki a **összes** kategória.

5. Keresse meg **Workday az Active Directory-kiépítés**, és adja hozzá az alkalmazást a katalógusból.

6. Miután az alkalmazás adnak, és a Részletek képernyő látható, válassza **kiépítés**

7. Módosítsa a **kiépítés** **mód** való **automatikus**

8. Végezze el a **rendszergazdai hitelesítő adataival** szakasz az alábbiak szerint:

   * **Rendszergazdai felhasználónév** – adja meg a Workday integrációs rendszer fiók felhasználóneve a hozzáfűzi a bérlő tartományneve. Következőhöz hasonlóan kell kinéznie: **felhasználónév\@bérlő_neve**

   * **Rendszergazdai jelszó –** adja meg a jelszót a Workday-integrációs rendszer fiók

   * **A bérlői URL-cím –** adja meg az URL-címet a Workday webes szolgáltatások végpontra a bérlő számára. Ez az érték hasonlóan kell kinéznie: https://wd3-impl-services1.workday.com/ccx/service/contoso4, ahol *contoso4* a megfelelő bérlő neve helyére és *wd3-impl* a megfelelő környezetben karakterlánc helyére.

   * **Active Directory-erdő -** "Name" az Active Directory-tartomány,-ügynökkel rendelkező regisztrált. A legördülő listában segítségével válassza ki a céltartomány kiépítéséhez. Az értéke általában egy karakterlánc, például: *contoso.com*

   * **Active Directory-tároló -** adja meg a tároló megkülönböztető Nevet, amelyen az ügynök kell felhasználói fiókokat hozhat létre alapértelmezés szerint.
        Példa: *Szervezeti egység általános jogú felhasználók, OU = = Users, DC = contoso, DC = test*
     > [!NOTE]
     > Ez a beállítás csak kerülnek Play áruházban a felhasználói fiók létrehozása, ha a *parentDistinguishedName* attribútum nem történik meg az attribútum-leképezéshez. Ezt a beállítást nem használja a felhasználó keresése vagy frissítési műveletek. A teljes sub tartományfa a keresési műveletet hatálya alá esik.

   * **Értesítő e-mailt –** adja meg az e-mail-címét, és jelölje be az "e-mail küldése a hiba akkor fordul elő, ha" jelölőnégyzetet.

> [!NOTE]
> Az Azure AD-kiépítés szolgáltatás e-mailben értesítést küld, ha az üzembe helyezési feladat kerül egy [karantén](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine) állapota.

   * Kattintson a **kapcsolat tesztelése** gombra. Ha a kapcsolat tesztelése sikeres volt, kattintson a **mentése** gombra az oldal tetején. Ha nem sikerül, ellenőrizze, hogy a Workday hitelesítő adatokat, és konfigurálni az ügynök telepítése az AD-beli hitelesítő érvényesek.

     ![Azure Portal](./media/workday-inbound-tutorial/wd_1.png)

   * Ha a hitelesítő adatok vannak mentése sikeresen megtörtént, a **leképezések** szakasz jelenik meg az alapértelmezett leképezést **Workday-feldolgozók szinkronizálja a helyi Active Directory**

### <a name="part-3-configure-attribute-mappings"></a>3. rész: Attribútum-leképezések konfigurálása

Ebben a szakaszban konfigurálhatja felhasználói adatfolyamok Workday-ből az Active Directoryhoz.

1. A kiépítés lapon a **leképezések**, kattintson a **a helyi Active Directory-szinkronizálás Workday feldolgozók**.

2. Az a **Forrásobjektum hatóköre** mezőhöz, kiválaszthatja, hogy mely felhasználók WORKDAY részhalmazához Attribútumalapú szűrőkészlet definiálásával AD, a kiépítés hatókörébe kell lennie. Az alapértelmezett hatóköre a "WORKDAY minden felhasználó". Példa szűrők:

   * Példa: Hatókör 1000000 és 2000000 közötti feldolgozó azonosítókkal rendelkező felhasználók számára

      * Attribútum: WorkerID

      * Operátor: Reguláris kifejezés egyeztetése

      * Érték: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Példa: Csak az alkalmazottak és a nem a függő feldolgozók

      * Attribútum: EmployeeID

      * Operátor: NEM NULL

> [!TIP]
> Amikor először konfigurálja a kiépítési alkalmazást, szüksége lesz a tesztelési és az attribútumleképezések és kifejezések, győződjön meg arról, hogy ez lehetővé teszi a kívánt eredményt. A Microsoft azt javasolja, alapján szűri hatókörére használatával **Forrásobjektum hatóköre** a leképezéseket a Workday-ből néhány tesztfelhasználók teszteléséhez. Miután ellenőrizte, hogy a leképezések használata, majd távolítsa el a szűrőt vagy fokozatosan bontsa ki, hogy további felhasználókat tartalmazzák.

1. Az a **célobjektum-műveletek** mezőbe globálisan szűrheti az Active Directory mely műveletek mennek végbe. **Hozzon létre** és **frissítés** legtöbb.

1. Az a **attribútum-leképezések** területen megadhat egyes Workday attribútumok leképezése az Active Directory-attribútumok.

1. Kattintson a frissíteni egy meglévő attribútumleképzés, vagy kattintson a **új megfeleltetés hozzáadása** új leképezéseket hozzáadásához a képernyő alján. Az egyes attribútumleképezés támogatja ezeket a tulajdonságokat:

      * **Leképezés típusa**

         * **Közvetlen** – a Workday attribútum ír, nem kell módosítania az AD-attribútum

         * **Állandó** – egy statikus, állandó karakterláncértéket írni az AD-attribútum

         * **Kifejezés** – lehetővé teszi, hogy az AD attribútuma, egyéni értékké írni egy vagy több Workday-attribútumok alapján. [További információ: Ez a cikk a kifejezések](../manage-apps/functions-for-customizing-application-data.md).

      * **Adatforrás-attribútum** – Workday-ből a felhasználói attribútum. Ha az Ön által keresett attribútum nem található, lásd: [Workday felhasználói attribútumok listája testre szabható](#customizing-the-list-of-workday-user-attributes).

      * **Alapértelmezett érték** – nem kötelező. Az adatforrás-attribútum értéke egy üres, ha a leképezés inkább ezt az értéket fogja írni.
            Leggyakrabban használt beállítások mellett akkor hagyja üresen a mezőt.

      * **Célattribútum** – a felhasználói attribútum az Active Directoryban.

      * **Ezzel az attribútummal objektumok** – Ez a leképezés használandó egyedi azonosítására a felhasználók Workday és az Active Directory közötti-e. Ezt az értéket a feldolgozó azonosító mező általában be a a Workday, amely általában van hozzárendelve egy, az alkalmazott azonosítója attribútumok az Active Directoryban.

      * **Megfeleltetési prioritás** – több egyező attribútumok beállítása. Ha több, akkor ez a mező által meghatározott sorrendben értékeli. Amint egyezést talál, további megfelelő attribútumok értékeli ki.

      * **Leképezés alkalmazása**

         * **Mindig** – ezt a hozzárendelést alkalmazni, mind a felhasználó létrehozása és a frissítési műveletek

         * **Csak a létrehozásakor** -leképezés alkalmazása csak a felhasználó-létrehozási műveletek

1. A hozzárendelések mentéséhez kattintson az **mentése** az attribútum-hozzárendelési szakasz elején.

   ![Azure Portal](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Az alábbiakban néhány például attribútumleképezések Workday és az Active Directory közötti néhány gyakori kifejezés

* A kifejezés, amely leképezi a *parentDistinguishedName* attribútum használt egy felhasználó egy vagy több Workday paraméterforrás attribútumai alapján különböző szervezeti egységekhez. Itt ebben a példában a különböző szervezeti alapú helyezi a felhasználók melyik városban lévő vannak.

* A *userPrincipalName* attribútum az Active Directoryban jön létre a duplikátumok függvénnyel [SelectUniqueValue](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue) , amely ellenőrzi, hogy létezik-e a céltartomány AD értéke egy generált érték, és csak Beállítja azt, ha egyedi.  

* [Van itt kifejezések írása dokumentációért](../manage-apps/functions-for-customizing-application-data.md). Ez a szakasz tartalmazza a példák a speciális karakterek eltávolítása.

| MUNKANAPI ATTRIBÚTUM | AZ ACTIVE DIRECTORY-ATTRIBÚTUM |  EGYEZŐ AZONOSÍTÓ? | LÉTREHOZÁSA / FRISSÍTÉSE |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Igen** | Írt csak létrehozáskor |
| **PreferredNameData**    |  CN    |   |   Írt csak létrehozáskor |
| **SelectUniqueValue (Csatlakozás ("\@", csatlakozzon (".", \[FirstName\], \[LastName\]), "contoso.com"), csatlakozás ("\@", csatlakozzon (".", a Mid (\[FirstName\], 1, 1 ()), \[LastName\]), "contoso.com"), csatlakozás ("\@", csatlakozzon (".", a Mid (\[FirstName\], 1, 2), \[LastName\]), "contoso.com"))**   | userPrincipalName     |     | Írt csak létrehozáskor 
| **Cserélje le(Mid(cseréljele(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\ \\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         Írt csak létrehozáskor |
| **Switch (\[aktív\], "0", "True", "1", "False")** |  accountDisabled      |     | Létrehozás + frissítése |
| **Keresztnév**   | givenName       |     |    Létrehozás + frissítése |
| **Vezetéknév**   |   sorozatszám   |     |  Létrehozás + frissítése |
| **PreferredNameData**  |  displayName |     |   Létrehozás + frissítése |
| **Vállalati**         | Vállalati   |     |  Létrehozás + frissítése |
| **SupervisoryOrganization**  | Szervezeti egység  |     |  Létrehozás + frissítése |
| **ManagerReference**   | kezelő  |     |  Létrehozás + frissítése |
| **BusinessTitle**   |  cím     |     |  Létrehozás + frissítése | 
| **AddressLineData**    |  streetAddress  |     |   Létrehozás + frissítése |
| **Község**   |   l   |     | Létrehozás + frissítése |
| **CountryReferenceTwoLetter**      |   CO |     |   Létrehozás + frissítése |
| **CountryReferenceTwoLetter**    |  c  |     |         Létrehozás + frissítése |
| **CountryRegionReference** |  St     |     | Létrehozás + frissítése |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Létrehozás + frissítése |
| **Irányítószám**  |   Irányítószám  |     | Létrehozás + frissítése |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Létrehozás + frissítése |
| **Fax**      | facsimileTelephoneNumber     |     |    Létrehozás + frissítése |
| **mobil**  |    mobil       |     |       Létrehozás + frissítése |
| **LocalReference** |  preferredLanguage  |     |  Létrehozás + frissítése |                                               
| **Kapcsoló (\[település\], "szervezeti egység általános jogú felhasználók, OU = felhasználók, OU = alapértelmezett, OU = helyek, DC = = contoso, DC = com", "Dallas", "szervezeti egység általános jogú felhasználók, OU = felhasználók, OU = Dallas, OU = helyek, DC = = contoso, DC = com", "Austin", "szervezeti egység általános jogú felhasználók, OU = Felhasználók, OU = Austin, OU = helyek, DC = = contoso, DC = com ","Seattle"," szervezeti egység általános jogú felhasználók, OU = felhasználók, OU = Budapest, OU = helyek, DC = = contoso, DC = com ","London"," szervezeti egység általános jogú felhasználók, OU = felhasználók, OU = London, OU = helyek, DC = = contoso, DC = com ")**  | parentDistinguishedName     |     |  Létrehozás + frissítése |

Az attribútum leképezés konfigurálás elvégzését követően mostantól [engedélyezéséhez, majd indítsa el a kiépítési szolgáltatás felhasználó](#enable-and-launch-user-provisioning).

## <a name="configuring-user-provisioning-to-azure-ad"></a>Azure ad-ben történő felhasználókiépítés konfigurálása

A következő szakaszok ismertetik a felhasználók átadásának Workday-ből az Azure AD-hez csak felhőbeli konfigurálásának lépéseit.

* [Az Azure AD-kiépítési összekötő alkalmazás hozzáadását és a Workday-kapcsolat](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Attribútumleképezések Workday és az Azure AD konfigurálása](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Engedélyezze, és indítsa el a felhasználók átadása](#enable-and-launch-user-provisioning)

> [!IMPORTANT]
> Ha csak felhőalapú felhasználói, szükség lesz az Azure ad-hez, és nem a helyszíni Active Directory csak kövesse az alábbi eljárást.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>1. rész: Az Azure AD-kiépítési összekötő alkalmazás hozzáadását és a Workday-kapcsolat

**A csak felhőbeli felhasználók kiépítése az Azure Active Directory konfigurálása a Workday:**

1. Nyissa meg a következőt: <https://portal.azure.com>.

2. A bal oldali navigációs sávon válassza **Azure Active Directory**

3. Válassza ki **vállalati alkalmazások**, majd **minden alkalmazás**.

4. Válassza ki **alkalmazás hozzáadása**, majd válassza ki a **összes** kategória.

5. Keresse meg **Workday az Azure AD-kiépítés**, és adja hozzá az alkalmazást a katalógusból.

6. Miután az alkalmazás adnak, és a Részletek képernyő látható, válassza **kiépítés**

7. Módosítsa a **kiépítés** **mód** való **automatikus**

8. Végezze el a **rendszergazdai hitelesítő adataival** szakasz az alábbiak szerint:

   * **Rendszergazdai felhasználónév** – adja meg a Workday integrációs rendszer fiók felhasználóneve a hozzáfűzi a bérlő tartományneve. Hasonlóan kell kinéznie: username@contoso4

   * **Rendszergazdai jelszó –** adja meg a jelszót a Workday-integrációs rendszer fiók

   * **A bérlői URL-cím –** adja meg az URL-címet a Workday webes szolgáltatások végpontra a bérlő számára. Ez az érték hasonlóan kell kinéznie: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, ahol *contoso4* a megfelelő bérlő neve helyére és *wd3-impl* a megfelelő környezetben karakterlánc helyére. Ha az URL-cím nem ismert, együttműködve a Workday integrációs partner vagy támogatási képviselő meghatározásához használja a megfelelő URL-címe.

   * **Értesítő e-mailt –** adja meg az e-mail-címét, és jelölje be az "e-mail küldése a hiba akkor fordul elő, ha" jelölőnégyzetet.

   * Kattintson a **kapcsolat tesztelése** gombra.

   * Ha a kapcsolat tesztelése sikeres volt, kattintson a **mentése** gombra az oldal tetején. Ha nem sikerül, ellenőrizze, hogy a Workday URL-címet és a hitelesítő adatok érvényesek a Workday.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>2. rész: Attribútumleképezések Workday és az Azure AD konfigurálása

Ebben a szakaszban konfigurálhatja felhasználói adatfolyamok Workday-ből az Azure Active Directoryhoz csak felhőbeli felhasználók.

1. A kiépítés lapon a **leképezések**, kattintson a **feldolgozók szinkronizálása az Azure AD**.

2. Az a **Forrásobjektum hatóköre** mezőhöz, kiválaszthatja, hogy mely felhasználók WORKDAY részhalmazához definiálását a szűrők attribútum alapján az Azure AD-kiépítés hatókörébe kell lennie. Az alapértelmezett hatóköre a "WORKDAY minden felhasználó". Példa szűrők:

   * Példa: Hatókör 1000000 és 2000000 közötti feldolgozó azonosítókkal rendelkező felhasználók számára

      * Attribútum: WorkerID

      * Operátor: Reguláris kifejezés egyeztetése

      * Érték: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Példa: Csak a függő dolgozók és a nem rendszeres munkatársak számára

      * Attribútum: ContingentID

      * Operátor: NEM NULL

3. Az a **célobjektum-műveletek** mezőbe globálisan szűrheti az Azure AD milyen műveletek mennek végbe. **Hozzon létre** és **frissítés** legtöbb.

4. Az a **attribútum-leképezések** területen megadhat egyes Workday attribútumok leképezése az Active Directory-attribútumok.

5. Kattintson a frissíteni egy meglévő attribútumleképzés, vagy kattintson a **új megfeleltetés hozzáadása** új leképezéseket hozzáadásához a képernyő alján. Az egyes attribútumleképezés támogatja ezeket a tulajdonságokat:

   * **Leképezés típusa**

      * **Közvetlen** – a Workday attribútum ír, nem kell módosítania az AD-attribútum

      * **Állandó** – egy statikus, állandó karakterláncértéket írni az AD-attribútum

      * **Kifejezés** – lehetővé teszi, hogy az AD attribútuma, egyéni értékké írni egy vagy több Workday-attribútumok alapján. [További információ: Ez a cikk a kifejezések](../manage-apps/functions-for-customizing-application-data.md).

   * **Adatforrás-attribútum** – Workday-ből a felhasználói attribútum. Ha az Ön által keresett attribútum nem található, lásd: [Workday felhasználói attribútumok listája testre szabható](#customizing-the-list-of-workday-user-attributes).

   * **Alapértelmezett érték** – nem kötelező. Az adatforrás-attribútum értéke egy üres, ha a leképezés inkább ezt az értéket fogja írni.
            Leggyakrabban használt beállítások mellett akkor hagyja üresen a mezőt.

   * **Célattribútum** – az Azure ad-ben a felhasználói attribútum.

   * **Ezzel az attribútummal objektumok** – Ez az attribútum csak Workday és az Azure AD a felhasználók egyedi azonosítására használhatók-e. Ezt az értéket a feldolgozó azonosító mező általában be a a Workday, amely általában hozzárendelve az alkalmazott azonosítója attribútum (új), vagy bővítményattribútum az Azure ad-ben.

   * **Megfeleltetési prioritás** – több egyező attribútumok beállítása. Ha több, akkor ez a mező által meghatározott sorrendben értékeli. Amint egyezést talál, további megfelelő attribútumok értékeli ki.

   * **Leképezés alkalmazása**

     * **Mindig** – ezt a hozzárendelést alkalmazni, mind a felhasználó létrehozása és a frissítési műveletek

     * **Csak a létrehozásakor** -leképezés alkalmazása csak a felhasználó-létrehozási műveletek

6. A hozzárendelések mentéséhez kattintson az **mentése** az attribútum-hozzárendelési szakasz elején.

Az attribútum leképezés konfigurálás elvégzését követően mostantól [engedélyezéséhez, majd indítsa el a kiépítési szolgáltatás felhasználó](#enable-and-launch-user-provisioning).

## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Az e-mail címek workdaybe a jelszóvisszaíró konfigurálása

Kövesse az alábbi utasításokat a felhasználó e-mail-címek az Azure Active Directoryból workdaybe a visszaírás konfigurálásával.

* [A visszaírási összekötő alkalmazás felvétele és a Workday-kapcsolat](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [A jelszóvisszaíró attribútumleképezések konfigurálása](#part-2-configure-writeback-attribute-mappings)
* [Engedélyezze, és indítsa el a felhasználók átadása](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>1. rész: A visszaírási összekötő alkalmazás felvétele és a Workday-kapcsolat

**A Jelszóvisszaíró Workday-összekötő konfigurálása:**

1. Nyissa meg a következőt: <https://portal.azure.com>

2. A bal oldali navigációs sávon válassza **Azure Active Directory**

3. Válassza ki **vállalati alkalmazások**, majd **minden alkalmazás**.

4. Válassza ki **alkalmazás hozzáadása**, majd válassza ki a **összes** kategória.

5. Keresse meg **Workday visszaírási**, és adja hozzá az alkalmazást a katalógusból.

6. Miután az alkalmazás adnak, és a Részletek képernyő látható, válassza **kiépítés**

7. Módosítsa a **kiépítés** **mód** való **automatikus**

8. Végezze el a **rendszergazdai hitelesítő adataival** szakasz az alábbiak szerint:

   * **Rendszergazdai felhasználónév** – adja meg a Workday integrációs rendszer fiók felhasználóneve a hozzáfűzi a bérlő tartományneve. Hasonlóan kell kinéznie: *felhasználónév\@contoso4*

   * **Rendszergazdai jelszó –** adja meg a jelszót a Workday-integrációs rendszer fiók

   * **A bérlői URL-cím –** adja meg az URL-címet a Workday webes szolgáltatások végpontra a bérlő számára. Ez az érték hasonlóan kell kinéznie: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, ahol *contoso4* a megfelelő bérlő neve helyére és *wd3-impl* a megfelelő környezetben karakterlánc helyére (ha szükséges).

   * **Értesítő e-mailt –** adja meg az e-mail-címét, és jelölje be az "e-mail küldése a hiba akkor fordul elő, ha" jelölőnégyzetet.

   * Kattintson a **kapcsolat tesztelése** gombra. Ha a kapcsolat tesztelése sikeres volt, kattintson a **mentése** gombra az oldal tetején. Ha nem sikerül, ellenőrizze, hogy a Workday URL-címet és a hitelesítő adatok érvényesek a Workday.

### <a name="part-2-configure-writeback-attribute-mappings"></a>2. rész: A jelszóvisszaíró attribútumleképezések konfigurálása

Ebben a szakaszban konfigurálhatja hogyan visszaírási attribútumok áramlanak Azure AD-ből a Workday.

1. A kiépítés lapon a **leképezések**, kattintson a **szinkronizálása az Azure Active Directory-felhasználók workdaybe**.

2. Az a **Forrásobjektum hatóköre** mezőben, igény szerint szűrheti, különböző felhasználócsoportokhoz, az Azure Active Directoryban kell rendelkeznie a Workday visszaírja e-mail címüket. Az alapértelmezett hatóköre az "Azure AD-ben minden felhasználó".

3. Az a **attribútum-leképezések** szakaszban, a megfelelő azonosító jelzi az attribútum az Azure Active Directoryban a Workday feldolgozó vagy alkalmazott azonosítója tároló módosítása. Egy népszerű egyező metódust, hogy a Workday feldolgozó vagy extensionAttribute1-15-re alkalmazott azonosítója szinkronizálása az Azure ad-ben, és majd segítségével ez az attribútum az Azure AD vissza a Workday felhasználók egyeztetéséhez.

4. A hozzárendelések mentéséhez kattintson az **mentése** az attribútum-hozzárendelési szakasz elején.

Az attribútum leképezés konfigurálás elvégzését követően mostantól [engedélyezéséhez, majd indítsa el a kiépítési szolgáltatás felhasználó](#enable-and-launch-user-provisioning). 

## <a name="enable-and-launch-user-provisioning"></a>Engedélyezze, és indítsa el a felhasználók átadása

A Workday kiépítési alkalmazáskonfigurációk elvégzése után, bekapcsolhatja a kiépítési szolgáltatás az Azure Portalon.

> [!TIP]
> Alapértelmezés szerint ha bekapcsolja a kiépítési szolgáltatás kezdeményez üzembe helyezési műveleteinek hatókörben lévő összes felhasználó számára. Ha az egymáshoz vagy a Workday adatproblémákat okoznak hibákat, majd az üzembe helyezési feladat lehet, hogy sikertelen és a karanténba helyezett állapotba kerülnek. Ajánlott eljárásként, ennek elkerülése érdekében javasoljuk, hogy konfigurálása **Forrásobjektum hatóköre** szűrő és az attribútumleképezések néhány tesztfelhasználók és tesztelés az összes felhasználó számára a teljes szinkronizálás elindítása előtt. Miután ellenőrizte, hogy a leképezések működik, és így a kívánt eredményeket, akkor választhatja a szűrő eltávolításához kibonthatja vagy fokozatosan további felhasználókat tartalmazza.

1. Az a **kiépítési** lapra, és állítsa a **üzembe helyezési állapotra** való **a**.

2. Kattintson a **Save** (Mentés) gombra.

3. Ez a művelet indul el a kezdeti szinkronizálás, amely attól függően, hogy hány felhasználó van az a Workday-bérlői óra változó számú is igénybe vehet. 

4. Bármikor, ellenőrizze a **Auditnaplók** lap meg, milyen műveletet végzett el a kiépítési szolgáltatás, az Azure Portalon. A naplók a kiépítési szolgáltatás, például, hogy melyik felhasználók vannak kívül workdayjel képes olvasni és majd ezt követően hozzáadása vagy frissítése az Active Directory által végrehajtott minden egyes szinkronizálási események listája. Talál a hibaelhárítási utasításokért tekintse át a naplókat, és javítsa ki a kiépítési hibákat.

5. A kezdeti szinkronizálás befejezése után rendszer írási összefoglaló jelentést a **kiépítési** lapon, a lent látható módon.

   ![Azure Portal](./media/workday-inbound-tutorial/wd_3.png)

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

* **Megoldás képesség kérdések**
  * [Egy új felvételi Workday-ből feldolgozásakor hogyan nem a megoldás be az új felhasználói fiók jelszavát az Active Directoryban?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [A megoldás támogatja a küldő e-mail-értesítések műveletek teljes kiépítés után?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Hogyan kézbesítési az újonnan felvett alkalmazottak a jelszavak kezelése és biztonságos egy olyan mechanizmust új jelszót kérnek?](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [Nem gyorsítótárazzák a megoldás Workday felhasználói profilokat az Azure ad-ben felhőben vagy a kiépítési ügynök rétegben?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [Nem a megoldás támogatási hozzárendelése a helyszíni AD-csoportokat a felhasználónak?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Melyik Workday API-k használata a a megoldás lekérdezési és frissítési Workday feldolgozó profilokhoz?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Konfigurálhatok a Workday HCM-bérlőhöz két Azure AD-bérlőt?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * ["Az Azure AD Workday" felhasználókiépítés az alkalmazás miért nem támogatott, ha helyeztük az Azure AD Connect?](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [Hogyan javaslatokat vagy javasolhat új funkciókat, Workday és az Azure AD-integrációval kapcsolatos?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **A kiépítési ügynök kérdések**
  * [Mi az a kiépítési ügynök általánosan elérhető verziója?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Honnan tudhatom, hogy a kiépítési ügynök verziója?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Nem a Microsoft automatikusan frissítések leküldése a kiépítési ügynök?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [A kiépítési ügynök telepíthető ugyanazon a kiszolgálón fut az AAD Connect?](#can-i-install-the-provisioning-agent-on-the-same-server-running-aad-connect)
  * [Hogyan konfigurálhatom a kiépítési ügynök proxykiszolgáló használata a kimenő HTTP-kommunikációhoz?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Hogyan biztosítja, hogy a kiépítési ügynök nem tud kommunikálni az Azure AD-bérlővel, és nincs tűzfal forgalomszűrők blokkolják az ügynök által használt portokat?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Hogyan megszüntetéséhez regisztrálhatom a kiépítési ügynök társított tartomány?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [A kiépítési ügynök eltávolítása?](#how-do-i-uninstall-the-provisioning-agent)
  
* **A WORKDAY AD attribútum leképezési és konfigurációs kérdésekre**
  * [Hogyan biztonsági mentése és exportálása a Workday-kiépítés attribútum leképezési és a séma működő példányát?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Az egyéni attribútumokat a Workday és az Active Directory van. Hogyan konfigurálhatom a megoldást a saját egyéni attribútumok használata?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Felhasználói fénykép Workday-ből az Active Directory helyezhetek üzembe?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Hogyan szinkronizálhatom mobilszámok felhasználói beleegyezés nyilvános használat alapján Workday-ből?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Hogyan formázza a megjelenítendő nevét az ad-ben a felhasználó szervezeti egység/város attribútum és leíró regionális eltérésekre alapján?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Hogyan használhatom SelectUniqueValue egyedi értékeket az samAccountName attribútumot létrehozni?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Hogyan távolítsa el a karaktereket mellékjeleket és normál angol nyelvű betűk alakíthatja őket?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Megoldás képesség kérdések

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Egy új felvételi Workday-ből feldolgozásakor hogyan nem a megoldás be az új felhasználói fiók jelszavát az Active Directoryban?

A helyszíni kiépítési ügynök lekérdezi a kérést hozzon létre egy új AD-fiókot, ha automatikusan hoz létre egy összetett véletlenszerű jelszót ki tudja szolgálni az AD-kiszolgáló által meghatározott összetettségi követelményeknek, és megadja ezt a user objektum. Ezt a jelszót a rendszer nem naplózza bárhol.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>A megoldás támogatja a küldő e-mail-értesítések műveletek teljes kiépítés után?

E-mail értesítések küldését, miután az üzembe helyezési műveletek elvégzése nem támogatott a jelenlegi kiadásban nem.

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>Hogyan kézbesítési az újonnan felvett alkalmazottak a jelszavak kezelése és biztonságos egy olyan mechanizmust új jelszót kérnek?

Új AD fiók kiépítése utolsó lépéseit egyike az ideiglenes jelszót a felhasználó AD fiókhoz rendelt kézbesítését. Sok vállalat továbbra is használja, a felhasználó vezető, aki keresztül a jelszót az új felvételi/cselekedniük, majd átadja az ideiglenes jelszót a hagyományos megközelítés. Ez a folyamat rendelkezik egy jellemző biztonsági hiba, és elérhető az Azure AD-funkcióival esetleg jobb megközelítés megvalósításához.

A felvételi folyamat részeként a HR-csapatok általában egy háttér-ellenőrzés futtatása és a vet az új felvételi mobiltelefonszámát. A Workday AD-felhasználó kiépítési-integrációval hozhat létre a tény felett, és bevezetési egy önkiszolgáló jelszó-visszaállítási képesség a felhasználónál az 1. napi. Ez az új felvételi Workday-ből az ad-hez "Mobile Number" attribútumának propagálása, és ezután AD az Azure AD-ből az AAD Connect használatával történik. Ha a "Mobile száma" az Azure ad-ben megtalálható, engedélyezheti a [önkiszolgáló jelszó alaphelyzetbe állítása (SSPR)](../authentication/howto-sspr-authenticationdata.md) a felhasználói fiók, úgy, hogy a nap-1, egy új felvételi használhatja a regisztrált és ellenőrzött mobil számot a hitelesítéshez.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>Nem gyorsítótárazzák a megoldás Workday felhasználói profilokat az Azure ad-ben felhőben vagy a kiépítési ügynök rétegben?

Nem, a megoldás nem tart fenn a felhasználói profilok gyorsítótár. Az Azure AD létesítési szolgáltatás egy adatfeldolgozó, adatok beolvasása a Workday-ből és a cél Active Directory vagy az Azure ad-ben történő egyszerűen funkcionál. Című témakör [személyes adatok kezelése](#managing-personal-data) felhasználói adatvédelem és adatmegőrzés kapcsolatos részleteket.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>Nem a megoldás támogatási hozzárendelése a helyszíni AD-csoportokat a felhasználónak?

Ez a funkció jelenleg nem támogatott. Javasolt áthidaló megoldás, hogy üzembe helyezése a PowerShell-parancsprogram, amely az Azure AD Graph API-végpont a naplózási adatokat, és használatával, amely a elindíthatja a forgatókönyvek például a csoport-hozzárendelést. A PowerShell-szkript lehetnek a Feladatütemező csatolva és az azonos mezőbe, a kiépítési ügynök fut telepített.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Melyik Workday API-k használata a a megoldás lekérdezési és frissítési Workday feldolgozó profilokhoz?

A megoldás jelenleg használja a következő Workday API-kat:

* Get_Workers (v21.1) a munkavégző adatainak beolvasása
* A munkahelyi e-mailt a Jelszóvisszaíró szolgáltatás (v26.1) Maintain_Contact_Information

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Konfigurálhatok a Workday HCM-bérlőhöz két Azure AD-bérlőt?

Igen, ez a konfiguráció támogatott. Ebben a forgatókönyvben konfigurálása magas szintű lépései a következők:

* #1-kiépítési ügynök telepítése, és regisztrálja az Azure AD-bérlő #1.
* #2-kiépítési ügynök telepítése, és regisztrálja az Azure AD-bérlő #2.
* Minden ügynök alapján a "gyermektartományokat" minden egyes kiépítési ügynök kezelésére, konfigurálja a tartomány(ok). Egy ügynök több tartomány képes kezelni.
* Az Azure Portalon a telepítő a Workday AD-felhasználó kiépítési alkalmazásra az egyes bérlők, és konfigurálja azt a saját tartománnyal rendelkező.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>"Az Azure AD Workday" felhasználókiépítés az alkalmazás miért nem támogatott, ha helyeztük az Azure AD Connect?

Az Azure AD a hibrid üzemmódot (ahol, vegyesen tartalmazza a cloud + helyszíni felhasználók) használata esetén fontos rendelkezik a "forrás-hitelesítésszolgáltató" egyértelmű meghatározása. Általában a hibrid forgatókönyvek az Azure AD Connect telepítése szükséges. Ha az Azure AD Connect, a helyszíni AD egy mérvadó forrás. A Workday bemutatná az Azure AD-összekötő a mix be egy olyan helyzetet, ahol Workday attribútumértékek sikerült felülírhatja az Azure AD Connect által beállított értékeket vezethet. "Az Azure AD Workday" kiépítési alkalmazás használatának ezért nem támogatott, ha az Azure AD Connect engedélyezve van. Ilyen esetekben javasoljuk (Workday az AD User) kiépítése a felhasználók az alkalmazást a helyszíni AD és a majd őket az Azure AD Connect használatával az Azure AD-be.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Hogyan javaslatokat vagy javasolhat új funkciókat, Workday és az Azure AD-integrációval kapcsolatos?

Visszajelzése az magas értékű, mivel ez segít megértenünk irányának megadása a jövőbeli kiadások és fejlesztések. Azt javasoljuk, hogy küldje el a ötlete vagy fokozása javaslatát és visszajelzéseket üdvözli a [az Azure AD Visszajelzési fórum](https://feedback.azure.com/forums/169401-azure-active-directory). A Workday-integrációval kapcsolatos konkrét visszajelzése, válassza a kategória *SaaS-alkalmazások* , és keresse meg a kulcsszavakat használó *Workday* található meglévő, a Workday kapcsolatos visszajelzéseket.

![UserVoice SaaS Apps](media/workday-inbound-tutorial/uservoice_saas_apps.png)

![UserVoice Workday](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Adjon a javaslat velünk új ötleteit, ellenőrizze, ha valaki más már javasolt egy hasonló funkciót. Ebben az esetben a szolgáltatás vagy a fejlesztés kérelem akár szavazhat. Az egyedi használati esetekhez, megjelenítése a cél a támogatása, és bemutatják, hogy a szolgáltatás el értékes, túl kapcsolatban megjegyzést is hagyhatja.

### <a name="provisioning-agent-questions"></a>A kiépítési ügynök kérdések

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Mi az a kiépítési ügynök általánosan elérhető verziója?

* Az általánosan elérhető a kiépítési ügynök verziószáma 1.1.30 vagy újabb verzió.
* Ha az ügynök verziószáma kisebb, mint 1.1.30, a nyilvános előzetes verzióját használja, és automatikusan frissíti az általánosan elérhető verziót, ha a kiszolgáló, az ügynököt futtató .NET 4.7.1 futásidejű.
  * Is [.NET verziójának](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) van telepítve a kiszolgálón. Ha a kiszolgáló nem fut a .NET 4.7.1, [töltse le és telepítse a .NET 4.7.1](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows). A kiépítési ügynök automatikusan frissül az általánosan elérhető verziót .NET 4.7.1 telepítése után.

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Honnan tudhatom, hogy a kiépítési ügynök verziója?

* Jelentkezzen be a Windows-kiszolgálóra, ahol a kiépítési ügynök telepítve van-e.
* Lépjen a **Vezérlőpult** -> **programok módosítása vagy eltávolítása** menü
* Keresse meg a megfelelő verziót **Microsoft Azure AD Connect kiépítési ügynök**

  ![Azure Portal](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Nem a Microsoft automatikusan frissítések leküldése a kiépítési ügynök?

Igen, a Microsoft automatikusan frissíti a kiépítési ügynök. A Windows-szolgáltatás leállításával letilthatja az automatikus frissítések **Microsoft Azure AD Connect az ügynök frissítési**.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-aad-connect"></a>A kiépítési ügynök telepíthető ugyanazon a kiszolgálón fut az AAD Connect?

Igen, a kiépítési ügynök is telepíthető a kiszolgálóra, amelyen az AAD Connect.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>A konfigurációs idején a kiépítési ügynök kérni fogja az Azure AD rendszergazdai hitelesítő adataival. Az ügynök tárol a hitelesítő adatok helyben a kiszolgálón?

A kiépítési ügynök konfigurálása során kérni fogja, az Azure AD rendszergazdai hitelesítő adatokat, csak csatlakozni az Azure AD-bérlő. Azt nem tárolja a hitelesítő adatok helyben a kiszolgálón. Azt azonban való kapcsolódáshoz használt hitelesítő adatok megőrzése a *a helyszíni Active Directory-tartomány* helyi Windows-jelszó tárolóban.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Hogyan konfigurálhatom a kiépítési ügynök proxykiszolgáló használata a kimenő HTTP-kommunikációhoz?

A kiépítési ügynök kimenő proxy használatát támogatja. Az ügynök konfigurációs fájl szerkesztésével konfigurálhatja **C:\Program Files\Microsoft Azure AD-kiépítés Agent\AADConnectProvisioningAgent.exe.config csatlakozás**. Adja hozzá a következő sorokat, csak a Bezárás előtt a fájl vége felé `</configuration>` címke.
A változók [proxykiszolgáló] és [proxyport] cserélje le a proxykiszolgáló nevét és értékek port.

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

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Hogyan biztosítja, hogy a kiépítési ügynök nem tud kommunikálni az Azure AD-bérlővel, és nincs tűzfal forgalomszűrők blokkolják az ügynök által használt portokat?

Azt is ellenőrizze, hogy rendelkezik-e a szükséges portok megnyitásához nyissa meg az összes a [összekötő portok vizsgálati eszköz](https://aadap-portcheck.connectorporttest.msappproxy.net/) , a helyszíni hálózat. További zöld jelöljük azt jelenti, hogy a nagyobb rugalmasság.

Győződjön meg arról, hogy az eszközt a megfelelő eredményeket ad meg, hogy ne felejtse el:

* Az eszköz a böngészőben nyissa meg a kiszolgálón, amelyre a kiépítési ügynök van telepítve.
* Győződjön meg arról, hogy minden olyan proxyk vagy a tűzfalak alkalmazható a kiépítési ügynök is érvényesek, ezen a weblapon. Ezt megteheti az Internet Explorerben a **beállítások -> Internet-beállítások -> kapcsolatok LAN-beállítások ->**. Ezen a lapon láthatja a mezőben "Használatát Proxy Server számára a helyi hálózaton". Válassza ezt a jelölőnégyzetet, és a proxykiszolgáló címét helyezze az "Address" mezőbe.

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Konfigurálható egy kiépítési ügynök kiépítése több AD-tartomány?

Igen, egy kiépítési ügynök több AD-tartományok kezeléséhez, mindaddig, amíg az ügynök már üzemel az adott tartományvezérlőn konfigurálható. A Microsoft magas rendelkezésre állás biztosítása érdekében, és sikertelen keresztül támogatja ugyanazokat az AD-tartományok szolgáltató ügynökök kiépítés 3 csoportja beállítását javasolja.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Hogyan megszüntetéséhez regisztrálhatom a kiépítési ügynök társított tartomány?

* Az Azure Portalról lekérése a *bérlőazonosító* az Azure AD-bérlő.
* Jelentkezzen be a kiépítés Agent ügynököt futtató Windows-kiszolgálóra.
* Windows-rendszergazdaként nyissa meg a powershellt.
* A regisztrációs parancsfájlokat tartalmazó könyvtárba, és futtassa a következő parancsokat, és cserélje le a \[bérlőazonosító\] paraméter a következő értékkel: a bérlő azonosítója.

  ```powershell
  cd “C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder”
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* Az ügynökök, amelyek szerepelnek – a listából másolása az "id" értéke mező adott erőforrásból azon *resourceName* egyenlő az AD tartománynevét.
* Az azonosító illessze be ezt a parancsot, és hajtsa végre, a PowerShellben.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Futtassa újra az ügynök konfigurációs varázslóját.
* Bármely más ügynökök esetén, amelyek korábban már hozzá lett rendelve ehhez a tartományhoz kell konfigurálni.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>A kiépítési ügynök eltávolítása?

* Jelentkezzen be a Windows-kiszolgálóra, ahol a kiépítési ügynök telepítve van-e.
* Lépjen a **Vezérlőpult** -> **programok módosítása vagy eltávolítása** menü
* Távolítsa el a következő programokat:
  * A Microsoft Azure AD Connect kiépítési ügynök
  * A Microsoft Azure AD Connect frissítési ügynök
  * A Microsoft Azure AD Connect kiépítési ügynök csomag

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>A WORKDAY AD attribútum leképezési és konfigurációs kérdésekre

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Hogyan biztonsági mentése és exportálása a Workday-kiépítés attribútum leképezési és a séma működő példányát?

A Microsoft Graph API segítségével a Workday Felhasználókiépítés konfigurációjának exportálása. Tekintse meg a szakasz lépéseit [exportálása és importálása a Workday-kiépítés attribútum Felhasználóleképezés konfigurációs](#exporting-and-importing-your-configuration) részleteiről.

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Az egyéni attribútumokat a Workday és az Active Directory van. Hogyan konfigurálhatom a megoldást a saját egyéni attribútumok használata?

A megoldás támogatja az egyéni Workday és az Active Directory-attribútumok. Az egyéni attribútumokat adhat a leképezési sémában, nyissa meg a **attribútumleképezés** panelre, és görgessen le a szakaszt kibontva **speciális beállítások megjelenítése**. 

![Attribútumlista szerkesztése](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Az egyéni Workday-attribútumok hozzáadásához válassza a beállítást *Workday attribútumlistájának szerkesztése* , és az egyéni AD-attribútumok hozzáadásához jelölje be *a helyi Active Directory attribútumlistájának szerkesztése*.

Lásd még:

* [Workday felhasználói attribútumok listája testre szabható](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Hogyan konfigurálhatom a megoldás csak frissíthetik az attribútumokat Workday végbement változások alapján AD-ben, és nem hozzon létre új AD-fiókokat?

Ez a konfiguráció beállításával biztosíthatja a **célobjektum-műveletek** a a **attribútumleképezések** panel az alább látható módon:

![Művelet frissítése](./media/workday-inbound-tutorial/wd_target_update_only.png)

Jelölje be a "Frissítés" csak a frissítési műveletek flow Workday-ből az AD. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Felhasználói fénykép Workday-ből az Active Directory helyezhetek üzembe?

A megoldás jelenleg nem támogatja a bináris attribútumok például *thumbnailPhoto* és *jpegPhoto* az Active Directoryban.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Hogyan szinkronizálhatom mobilszámok felhasználói beleegyezés nyilvános használat alapján Workday-ből?

* Nyissa meg a Workday-kiépítés alkalmazás a "Kiépítés" panelen.
* Kattintson az attribútum-leképezéshez 
* Alatt **leképezések**válassza **a helyi Active Directory-szinkronizálás Workday feldolgozók** (vagy **Workday feldolgozók szinkronizálása az Azure AD**).
* Az attribútum-leképezéshez oldalon görgessen lefelé, és jelölje be a "Speciális beállítások megjelenítése" jelölőnégyzetet.  Kattintson a **Workday attribútumlistájának szerkesztése**
* A megnyíló panelen keresse meg a "Mobileszköz" attribútumot, és kattintson a sor szerkesztését teszi lehetővé a **API kifejezés** ![Mobile GDPR](./media/workday-inbound-tutorial/mobile_gdpr.png)

* Cserélje le a **API kifejezés** a következő új kifejezésbe lekéri a munkát a mobiltelefonszám csak akkor, ha a "nyilvános használati jelző" értéke "True", a Workday.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Mentse az attribútumlista.
* Mentse a attribútumleképezés.
* Jelenlegi állapot törlése, és indítsa újra a teljes szinkronizálás.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Hogyan formázza a megjelenítendő nevét az ad-ben a felhasználó szervezeti egység/város attribútum és leíró regionális eltérésekre alapján?

Általános követelmény konfigurálása a *displayName* attribútumot az ad-ben, hogy a felhasználó részlege és ország kapcsolatos információkat is biztosít. A példa: Kovács János működik a marketingosztály Egyesült államokbeli, előfordulhat, hogy szeretné-e a *displayName* megjelenjen *Kovács János (Marketing-US)*.

Íme, miként hozhat létre ilyen követelményei kezelheti *CN* vagy *displayName* , például a vállalat, üzleti egység, városok vagy országok attribútumait tartalmazza.

* Minden munkanap attribútum használata a mögöttes XPATH API kifejezés, amely konfigurálható a beolvasott **attribútumleképzés -> speciális szakasz Workday-attribútumlista szerkesztése >**. Íme a Workday alapértelmezett XPATH API kifejezése *PreferredFirstName*, *PreferredLastName*, *vállalati* és *SupervisoryOrganization* attribútumok.

     [!div class="mx-tdCol2BreakAll"]
     | Munkanapi attribútum | API XPATH-kifejezés |
     | ----------------- | -------------------- |
     | PreferredFirstName | WD:Worker/WD:Worker_Data/WD:Personal_Data/WD:Name_Data/WD:Preferred_Name_Data/WD:Name_Detail_Data/WD:First_Name/Text() |
     | PreferredLastName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | Vállalat | WD:Worker / wd:Worker_Data / wd:Organization_Data / wd:Worker_Organization_Data [wd:Organization_Data / wd:Organization_Type_Reference / wd:ID [@wd:type= 'Organization_Type_ID'] = "Céges"]/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | WD:Worker / wd:Worker_Data / wd:Organization_Data / wd:Worker_Organization_Data / wd:Organization_Data [wd:Organization_Type_Reference / wd:ID [@wd:type= 'Organization_Type_ID'] = 'Felügyeleti'] /wd:Organization_Name/text() |
  
   Erősítse meg a Workday-csapattal, hogy a fenti API kifejezés a Workday-bérlői konfigurációjától érvényes. Ha szükséges, módosíthatja őket a szakaszban leírt módon [Workday felhasználói attribútumok listája testre szabható](#customizing-the-list-of-workday-user-attributes).

* Hasonlóképpen ország Workday jelen van olvassa be az adatokat az alábbi XPath NYELV használatával: *wd:Worker / wd:Worker_Data / wd:Employment_Data / wd:Position_Data / wd:Business_Site_Summary_Data / wd:Address_Data / wd:Country_Reference*

     Nincsenek 5 ország kapcsolatos attribútumokat, amelyek elérhetők a Workday attribútum lista szakaszban.

     | Munkanapi attribútum | API XPATH-kifejezés |
     | ----------------- | -------------------- |
     | CountryReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Numeric-3_Code']/text() |
     | CountryReferenceTwoLetter | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-2_Code']/text() |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Ellenőrizze a Workday-csapattal, hogy a fenti API kifejezések érvényesek-e a Workday-bérlői konfigurációjától. Ha szükséges, módosíthatja őket a szakaszban leírt módon [Workday felhasználói attribútumok listája testre szabható](#customizing-the-list-of-workday-user-attributes).

* A megfelelő attribútum leképezés kifejezés hozhat létre, mely Workday attribútum "mérvadó" jelenti. a felhasználó utónevét, utolsó neve, ország és részleg azonosítása. Tegyük fel, az attribútumok *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* és *SupervisoryOrganization* jelölik. Ezzel hozhat létre az ad-kifejezés *displayName* attribútum egy megjelenített nevet, például a következőképpen beolvasásához *Kovács János (Marketing-US)*.

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Ha a jobb oldali kifejezésnek, a attribútumleképezések tábla szerkesztése, és módosítsa a *displayName* attribútumleképezés alább látható módon:   ![DisplayName leképezés](./media/workday-inbound-tutorial/wd_displayname_map.png)

* A fenti példában kiterjesztése, nézzük szeretné átalakítani a városok nevei gyorsírás értékek Workday-ből érkező, majd hozhat létre például nevének megjelenítéséhez például *Smith, a John (CHI)* vagy *Doe, Jane (NYC)*, akkor érhető el ez az eredmény egy kapcsoló kifejezés használata a Workday *település* attribútumra, mint a okokból változó.

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
  * [Váltson a függvény szintaxisát](../manage-apps/functions-for-customizing-application-data.md#switch)
  * [Csatlakozzon a függvény szintaxisát](../manage-apps/functions-for-customizing-application-data.md#join)
  * [Függvény szintaxisát hozzáfűzése](../manage-apps/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Hogyan használhatom SelectUniqueValue egyedi értékeket az samAccountName attribútumot létrehozni?

Tegyük fel szeretné létrehozni az egyedi értékeket *samAccountName* attribútum kombinációjával *FirstName* és *LastName* attribútumok Workday-ből. Egy kifejezés, amely Kezdésként használhatja az alább megadott van:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
)
```

A fenti kifejezés működése: Ha a felhasználó John Smith, először megpróbálja létrehozni JSmith, ha JSmith már létezik, akkor állít elő a JoSmith, ha már létezik, hozza azt létre JohSmith. A kifejezés emellett biztosítja, hogy a létrehozott értéket megfelel-e a hosszát és a speciális karakterek korlátozás társított *samAccountName*.

Lásd még:

* [Mid függvény szintaxisát](../manage-apps/functions-for-customizing-application-data.md#mid)
* [Cserélje le a függvény szintaxisát](../manage-apps/functions-for-customizing-application-data.md#replace)
* [SelectUniqueValue függvény szintaxisát](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Hogyan távolítsa el a karaktereket mellékjeleket és normál angol nyelvű betűk alakíthatja őket?

A függvény használata [NormalizeDiacritics](../manage-apps/functions-for-customizing-application-data.md#normalizediacritics) speciális karakterek eltávolítása a első és a felhasználó utónevét, az e-mail-címet vagy a CN összeállítása közben. érték, a felhasználó.

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

A szakasz ismerteti, hogyan háríthatók el a kiépítési útmutatást a Workday-integráció az Azure AD-naplók használatával a hibákat, és a Windows Server eseménynaplóit. Az általános hibaelhárítási lépéseket épül, és a rögzített fogalmak a [oktatóanyag: -Jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md)

Ez a szakasz ismerteti a hibaelhárítás a következő szempontokat:

* [Az ügynök hibaelhárítása Windows-Eseménynapló beállítása](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Az Azure portal naplók hibaelhárítási szolgáltatás beállítása](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Understanding naplók AD felhasználói fiók létrehozási műveletek](#understanding-logs-for-ad-user-account-create-operations)
* [Understanding naplók Manager frissítési műveletek](#understanding-logs-for-manager-update-operations)
* [Hibát gyakran feloldása](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Az ügynök hibaelhárítása Windows-Eseménynapló beállítása

* Jelentkezzen be a Windows Server-gép, ahol a kiépítési ügynök telepítése
* Nyissa meg **Windows Server-eseménynaplót** asztali alkalmazás.
* Válassza ki **Windows-naplók > alkalmazás**.
* Használja a **aktuális napló szűrése...** lehetőség segítségével megtekintheti az összes naplózott forrás alatt **aad-ben. Connect.ProvisioningAgent** eseményeket az eseményazonosító "5", "-5" szűrő megadásával, ahogy az alábbi kizárásával.

  ![Windows-Eseménynapló](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* Kattintson a **OK** és rendezheti az eredmény nézethez **dátum és idő** oszlop.

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Az Azure portal naplók hibaelhárítási szolgáltatás beállítása

* Indítsa el a [az Azure portal](https://portal.azure.com), és keresse meg a **Auditnaplók** alkalmazások kiépítése a Workday szakaszában.
* Használja a **oszlopok** az Auditnaplók oldalon csak a következő oszlopok megjelenítéséhez a nézetben (dátum, tevékenység, állapot, állapota ok) gombra. Ez a konfiguráció biztosítja, hogy arra összpontosítunk, csak a vonatkozó hibaelhárítási adatokat.

  ![Naplózási log oszlopok](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* Használja a **cél** és **dátumtartomány** lekérdezési paramétert a nézet szűréséhez. 
  * Állítsa be a **cél** lekérdezési paraméter "Feldolgozó ID" vagy "Alkalmazott azonosítója" a Workday feldolgozó objektum.
  * Állítsa be a **dátumtartomány** egy megfelelő időszakra, amelyben vizsgálni kívánt a hibák vagy problémák az üzembe.

  ![Auditálási napló szűrők](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Understanding naplók AD felhasználói fiók létrehozási műveletek

Amikor egy új felvételi WORKDAY észlel (tegyük fel, az alkalmazott azonosítója *21023*), az Azure AD létesítési szolgáltatás hozzon létre egy új AD-felhasználói fiókot, a feldolgozó és a folyamat során megpróbálja 4 naplórekordtípus hoz létre, az alább ismertetett:

  [![Auditnapló létrehozása ops](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

A naplórekordok valamelyik kattintva a **tevékenység részletei** lapot nyit meg. Mi a **tevékenység részletei** lap megjeleníti az egyes rekordtípusokra napló.

* **Munkanapi importálás** rekordot: A naplóbejegyzés Workday-ből beolvasott feldolgozó információkat jeleníti meg. Az információk a *további részleteket* adatok beolvasása a Workday-ből hibáinak elhárítása naplórekord szakaszában. Egy példa rekord együtt minden mező értelmezése mutatók alább találja.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* **AD-importálási** rekordot: A naplóbejegyzés a fiók az AD-ből beolvasott adatokat jelenít meg. Mivel a felhasználók kezdeti létrehozása során nem lett nem AD-fiókot a *tevékenység állapota OK* fogja jelezni, hogy nem a megfelelő azonosító attribútumértékkel rendelkező fiókot az Active Directoryban található. Az információk a *további részleteket* adatok beolvasása a Workday-ből hibáinak elhárítása naplórekord szakaszában. Egy példa rekord együtt minden mező értelmezése mutatók alább találja.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Az AD-importálási műveletben megfelelő kiépítési ügynök naplórekordok megkereséséhez nyissa meg a Windows Eseménynapló-naplókat, és használja a **keresése...** menüelemre keresse meg a megfelelő azonosító/csatlakoztatása tulajdonság attribútum értékét tartalmazó naplóbejegyzések (ebben az esetben *21023*).

  ![Keresés](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Keresse meg a bejegyzésben, *eseményazonosító = 9*, amely biztosítja, az LDAP szűrő beolvasni az AD-fiókot az ügynök által használt keresés. Ellenőrizheti, hogy ez-e a megfelelő keresési szűrő egyedi felhasználói bejegyzések lekéréséhez.

  ![LDAP-keresés](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  A bejegyzés azt közvetlenül követő *eseményazonosító = 2* a keresési művelet eredményét rögzíti, és ha az adott vissza eredményt.

  ![LDAP-eredmények](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* **Szinkronizálási szabályművelet** rekordot: A naplóbejegyzés az eredményeket attribútum leképezési szabályokat jeleníti meg, és az üzembe helyezési művelet feldolgozni a bejövő Workday eseményt végrehajtandó együtt Hatókörszűrő konfigurálva. Az információk a *további részleteket* naplórekord a szinkronizálási művelet hibáinak elhárítása szakaszában. Egy példa rekord együtt minden mező értelmezése mutatók alább találja.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Ha probléma adódik az attribútum leképezés kifejezések vagy veti fel a bejövő Workday-adatok (például: üres vagy null érték tartozik szükséges attribútumokat), majd megfigyelheti sikertelen ezen a ponton a hibakód a hiba részleteinek megadása.

* **AD-exportálás** rekordot: A naplóbejegyzés AD fiók létrehozási művelet, amely a folyamat során beállított attribútumértékek mellett eredményét jeleníti meg. Az információk a *további részleteket* naplórekord a fiókkal kapcsolatos problémák elhárítása szakaszában-létrehozási művelet. Egy példa rekord együtt minden mező értelmezése mutatók alább találja. A "További részleteket" területen az "EventName" értéke "EntryExportAdd", a megfelelő azonosító attribútum értéke "JoiningProperty", "SourceAnchor" van beállítva, a WorkdayID (WID) a bejegyzéshez tartozó és a "TargetAnchor" értékre van állítva a az AD "ObjectGuid" értékét az újonnan létrehozott felhasználói attribútum. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Az AD-exportálási művelet megfelelő kiépítési ügynök naplórekordok megkereséséhez nyissa meg a Windows Eseménynapló-naplókat, és használja a **keresése...** menüelemre keresse meg a megfelelő azonosító/csatlakoztatása tulajdonság attribútum értékét tartalmazó naplóbejegyzések (ebben az esetben *21023*).  

  Keresse meg az időbélyeg az exportálási művelet a megfelelő, egy HTTP POST rekord *eseményazonosító = 2*. Ez a rekord tartalmazza a kiépítési ügynök az eszközkiépítési szolgáltatás által küldött attribútumértékek.

  [![SCIM Add](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  A fenti eseményt követően azonnal egy másik eseményt, amelyek rögzítik a válaszokat a Create AD fiókművelet kell lennie. Ezt az eseményt az AD-ben létrehozott új objectGuid ad vissza, és az eszközkiépítési szolgáltatás az TargetAnchor attribútuma van beállítva.

  [![SCIM Add](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>Frissítés az operations manager naplóinak ismertetése

A kezelő attribútum a hivatkozási attribútum értéke az ad-ben. A kiépítési szolgáltatás nincs beállítva a vezetői attribútumához a felhasználó-létrehozási művelet részeként. Inkább a vezetői attribútumához részeként van állítva egy *frissítése* művelet a felhasználó AD-fiók létrehozása után. A fenti példa növekszik, tegyük fel, az alkalmazott azonosítója "21451" egy új felvételi akkor aktiválódik, a Workday és az új felvételi manager (*21023*) már rendelkezik egy AD-fiókot. Ebben a forgatókönyvben a Keresés a vizsgálati naplók 21451 felhasználó megjelenik-e 5 bejegyzéseket.

  [![Manager frissítése](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

Az első 4 rekordok hasonlóak, a kapcsolatok a Microsoft megvizsgálta részeként a felhasználó-létrehozási művelet. Az 5. bejegyzés az Exportálás társított manager attribútum frissítése. A naplórekord billable AD fiók manager frissítési művelet, amely a kezelő használatával történik *objectGuid* attribútum.

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

### <a name="resolving-commonly-encountered-errors"></a>Hibát gyakran feloldása

Ez a szakasz ismerteti a Workday-felhasználók létrehozásának és a megoldásának módjával kapcsolatban leggyakrabban észlelt hibát. Hibák a következők szerint vannak csoportosítva:

* [Az ügynök kiépítési hibák](#provisioning-agent-errors)
* [Csatlakozási hibák](#connectivity-errors)
* [AD felhasználói fiók létrehozási hibák](#ad-user-account-creation-errors)
* [AD felhasználói fiók frissítési hibáinak száma](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Az ügynök kiépítési hibák

|#|Hiba történt a forgatókönyv |Lehetséges okok|Ajánlott felbontás|
|--|---|---|---|
|1.| Hiba történt a kiépítési ügynök telepítése, hibaüzenet:  *"A Microsoft Azure AD Connect-kiépítés Agent" szolgáltatást (AADConnectProvisioningAgent) nem sikerült elindítani. Győződjön meg arról, hogy van-e megfelelő jogosultságokkal indítsa el a rendszer.* | Ez a hiba általában látható, ha a kiépítési ügynök telepítése tartományvezérlőn kívánt, és csoportházirend megakadályozza, hogy a szolgáltatás indítása.  Ha az ügynököt futtató korábbi verziójával rendelkezik, és a egy új telepítésének megkezdése előtt nem távolította is látható.| Telepítse a kiépítési ügynök egy nem tartományvezérlő kiszolgálón. Győződjön meg arról, hogy az ügynök korábbi verzióinak eltávolítása van-e az új ügynök telepítése előtt.|
|2.| "A Microsoft Azure AD Connect kiépítési ügynök" Windows szolgáltatás *kezdő* állapotát, és nem vált *futó* állapota. | A telepítés részeként az ügynök varázsló létrehoz egy helyi fiókot (**NT-szolgáltatás\\AADConnectProvisioningAgent**) van a kiszolgálón, és ez a **bejelentkezés** indításához használt fiók a a szolgáltatás. Ha a biztonsági házirend a Windows server a helyi fiókok megakadályozza a szolgáltatások futtatását, ez a hiba fog történni. | Nyissa meg a *szolgáltatások konzol*. A Windows-szolgáltatás "Microsoft Azure AD Connect kiépítési ügynök" kattintson a jobb gombbal, és a bejelentkezési lapon adja meg a szolgáltatás futtatásához egy tartományi rendszergazda fiókja. Indítsa újra a szolgáltatást. |
|3.| A kiépítési ügynök konfigurálásakor az Active Directory-tartománynak a lépésben a *Connect Active Directory*, a varázsló az AD-séma betöltése hosszabb ideig tart, és végül túllépi az időkorlátot. | Ez a hiba általában megjelenik-e a varázsló-e nem lehet csatlakozni az AD domain controller-kiszolgáló tűzfal problémák miatt. | A a *Connect Active Directory* lépésében adja meg az Active Directory-tartománynak a hitelesítő adatokat, miközben lehetősége van nevű *válassza ki a tartományt vezérlő prioritású*. Használja ezt a beállítást válassza ki egy olyan tartományvezérlőre, amely az ügynök kiszolgáló ugyanazon a helyen, és győződjön meg arról, hogy nincsenek-e a kommunikációt blokkoló tűzfal szabályok. |

#### <a name="connectivity-errors"></a>Kapcsolódási hibák

Ha a kiépítési szolgáltatás nem tud csatlakozni a Workday vagy az Active Directory, ez okozhat az üzembe helyezés a karanténba zárt állapotba kerülnek. Használja az alábbi táblázatban a kapcsolati hibák elhárításához.

|#|Hiba történt a forgatókönyv |Lehetséges okok|Ajánlott felbontás|
|--|---|---|---|
|1.| Amikor rákattint a **kapcsolat tesztelése**, a hibaüzenet jelenik meg: *Hiba történt az Active Directoryhoz. Győződjön meg arról, hogy a helyszíni kiépítési ügynök fut-e, és a megfelelő Active Directory-tartományhoz van konfigurálva.* | Ez a hiba általában megjelenik-e if a kiépítési ügynök nem fut, vagy az Azure AD közötti kommunikációt blokkoló tűzfal és a kiépítési ügynök. Ez a hiba, ha a tartomány nincs konfigurálva az ügynök varázslóban is megjelenhetnek. | Nyissa meg a *szolgáltatások* konzolon ellenőrizze, hogy fut-e az ügynököt a Windows-kiszolgálón. Nyissa meg a kiépítési ügynök varázslót, és győződjön meg arról, hogy a megfelelő tartomány regisztrálva van az ügynök.  |
|2.| A hétvégék (Fri-Sat) keresztül karantén állapotba kerül a létesítési feladat, és kapunk, hogy nincs-e hiba a szinkronizálás az e-mailben értesítést. | A hiba gyakori okai egyik, a Workday tervezett állásidőt. Workday megvalósítási bérlőt használ, ha vegye figyelembe, hogy a Workday ütemezte állásidő a megvalósítás bérlők szabadnapok (általában a szombat reggel, Friday este) keresztül, és ebben az időszakban a Workday alkalmazások kiépítése előfordulhat, hogy lépjen be állapot karanténba helyezése, mert nem tud csatlakozni a Workday. Kap vissza szokásos állapotába után a Workday megvalósítási bérlő újra online állapotba kerül. Ritka esetekben előfordulhat, hogy is ezt a hibaüzenetet, ha az integrációs rendszer felhasználó jelszavának bérlői frissítés miatt megváltozott, vagy ha a fiók zárolva van, vagy lejárt állapotban. | Ellenőrizze a Workday-rendszergazdaként vagy integrációs partneréhez megtekintéséhez, amikor a Workday figyelmen kívül hagyja a figyelmeztető üzenetek a leállás ideje alatt és után a Workday-példány újra online állapotba kerül, győződjön meg arról, hogy rendelkezésre állási üzemszünet ütemezi.  |


#### <a name="ad-user-account-creation-errors"></a>AD felhasználói fiók létrehozási hibák

|#|Hiba történt a forgatókönyv |Lehetséges okok|Ajánlott felbontás|
|--|---|---|---|
|1.| Exportálja a felügyeleti napló üzenettel művelet sikertelen *hiba: OperationsError-SvcErr: Művelet hiba történt. A címtárszolgáltatás nem felettes hivatkozás van konfigurálva. A címtárszolgáltatás probléma átirányítások az erdőn kívüli objektumok, ezért nem.* | Ez a hiba általában Ha megjelenik a *Active Directory-tároló* szervezeti egység nem megfelelően van beállítva, vagy ha probléma adódik a kifejezés leképezése használja a *parentDistinguishedName*. | Ellenőrizze a *Active Directory-tároló* elgépelések OU paramétere. Ha használ *parentDistinguishedName* a attribútumleképezés győződjön meg arról, hogy mindig értékeli egy ismert tárolóba a AD-tartományban. Ellenőrizze a *exportálása* a naplózási eseményt naplózza, tekintse meg a létrehozott értéket. |
|2.| Exportálja a művelet sikertelen a naplófájlban a következő hibakóddal: *SystemForCrossDomainIdentityManagementBadResponse* és az üzenet *hiba: ConstraintViolation-AtrErr: A kérelemben szereplő érték érvénytelen. Az attribútum értékét nem volt elfogadható értékek tartományán. \nError részletei: CONSTRAINT_ATT_TYPE - vállalati*. | Amíg ez a hiba csak a *vállalati* attribútum, előfordulhat, hogy ezt a hibaüzenetet a többi-attribútumok *CN* is. Ez a hiba jelenik meg a kényszerített AD séma megkötés miatt. Alapértelmezés szerint az attribútumokat, például *vállalati* és *CN* rendelkezik az ad-ben a felső korlátja 64 karakternél. Ha a Workday-ből érkező értéke legfeljebb 64 karakter, majd látni fogja ezt a hibaüzenetet. | Ellenőrizze a *exportálása* a hibaüzenetben jelentett esemény a naplók megtekintéséhez az attribútum értékét. Fontolja meg a Workday használatával származó érték csonkolására a [Mid](../manage-apps/functions-for-customizing-application-data.md#mid) függvény vagy módosítja a leképezéseket AD attribútuma, amely nem rendelkezik hasonló megkötés hossz alapján.  |

#### <a name="ad-user-account-update-errors"></a>AD felhasználói fiók frissítési hibáinak száma

AD felhasználói fiók frissítési folyamat során a kiépítési szolgáltatás adatokat olvas a Workday és az AD, futtatja az attribútum a leképezési szabályokat, és határozza meg, ha bármilyen módosítást érvénybe léptetéséhez. Ennek megfelelően egy frissítés az esemény akkor váltódik. Ha hibát észlel az alábbi lépések bármelyikét, a rendszer naplózza a naplók. Az alábbi táblázat segítségével frissítés előforduló gyakori hibák elhárítása.

|#|Hiba történt a forgatókönyv |Lehetséges okok|Ajánlott felbontás|
|--|---|---|---|
|1.| Szinkronizálási szabály művelet hibák v auditovacím protokolu üzenettel *EventName = EntrySynchronizationError és hibakód = EndpointUnavailable*. | Ez a hiba megjelenik-e a kiépítési szolgáltatás-e felhasználói profil adatainak lekérése az Active Directory a helyszíni által tapasztalt feldolgozási hiba miatt nem sikerült a kiépítési ügynök. | Ellenőrizze a kiépítési ügynök eseménynaplók hiba események az olvasási műveletet (Event ID 2 szűrés) kapcsolatos hibákat jelzik. |
|2.| Az ad-ben a vezetői attribútumához nem módosul az egyes felhasználók az ad-ben. | A hiba legvalószínűbb oka, ha szabályának használja, és a felhasználó nem szerepel a hatókört. Emellett futtathatja ezt a hibát, ha a manager egyező azonosító attribútum (például EmployeeID) nem található a cél Active Directory-tartománynak, vagy nem a helyes értékre állítsa. | Tekintse át a Hatókörszűrő, és adja hozzá a kezelő felhasználó hatókörében. Ellenőrizze a kezelő profil az ad-ben, győződjön meg arról, hogy nincs-e a megfelelő azonosító attribútum értékét. |

## <a name="managing-your-configuration"></a>A konfiguráció kezelése

Ez a szakasz ismerteti, hogyan tovább bővítheti, testreszabása és kezelheti a Workday-központú felhasználókiépítés konfigurálása. Azt mutatja be a következő témaköröket:

* [Workday felhasználói attribútumok listája testre szabható](#customizing-the-list-of-workday-user-attributes)  
* [A konfiguráció importálása és exportálása](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Workday felhasználói attribútumok listája testre szabható

Az alkalmazások kiépítése az Active Directory és az Azure AD egyaránt tartalmazza alapértelmezett Workday felhasználói attribútumok listáját a Workday közül választhat. Ezek a listák azonban ne legyenek átfogó. WORKDAY támogatja a több száz lehetséges felhasználói attribútumok, amelyek a standard vagy a Workday-bérlői egyedi lehet.

Az Azure AD létesítési szolgáltatás támogatja a lista vagy a Workday attribútum olyan bármely attribútumok érhető el a testre szabhatók a [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) emberi erőforrások API-művelet.

Ez a módosítás végrehajtásához kell használnia [Workday Studio](https://community.workday.com/studio-download) kibontani a XPath kifejezés, amely a használni kívánt attribútumok jelölésére, és adja hozzá a kiépítési konfigurációjának a speciális attribútum-szerkesztő használatával az Azure Portalon .

**A Workday felhasználói attribútum XPath kifejezés lekéréséhez:**

1. Töltse le és telepítse [Workday Studio](https://community.workday.com/studio-download). Szüksége lesz egy Workday közösségi fiók eléréséhez a telepítőt.

2. A Workday Human_Resources WSDL-fájljának letöltése az URL-cím: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Indítsa el a Workday Studiót.

4. A parancssávon válassza ki a **Workday > teszt webszolgáltatás Tester** lehetőséget.

5. Válassza ki **külső**, és válassza ki a letöltött Human_Resources WSDL-fájl a 2. lépésben.

    ![Munkanapi Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Állítsa be a **hely** mezőt `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, de "IMPL-CC" cserélje le a tényleges példánytípus, és a bérlő valódi neve az "BÉRLŐ".

7. Állítsa be **művelet** való **Get_Workers**

8.  Kattintson a kis **konfigurálása** a Workday hitelesítő adatok beállítása a kérelem/válasz ablaktábla alatti hivatkozásra. Ellenőrizze **hitelesítési**, majd adja meg a felhasználónevet és jelszót a Workday-integrációs system fiók. Ügyeljen arra, hogy a felhasználó nevét neveként\@bérlőben, és hagyja a **WS-biztonsági UsernameToken** lehetőség van kijelölve.

    ![Munkanapi Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. Kattintson az **OK** gombra.

10. Az a **kérelem** panelen illessze be az XML-fájl az alábbi és beállított **Employee_ID** , egy valódi felhasználónak a Workday-bérlői az alkalmazott azonosítója. Válasszon ki egy felhasználót, hogy a attribútummal rendelkezik, amelyeket meg szeretne kinyerni feltöltve.

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

11. Kattintson a **kérés küldése** (zöld nyíl), hajtsa végre a parancsot. Ha sikeres, a válaszban meg kell jelennie a **válasz** ablaktáblán. Ellenőrizze a választ, ne legyen a megadott felhasználói azonosító adatait, és nem hiba.

12. Ha sikeres, másolja az XML az **válasz** ablaktáblán, és mentse egy XML-fájlt.

13. Az a parancs sáv a Workday Studióban válassza **fájl > fájl megnyitása...**  , és nyissa meg az XML-fájlt mentette. Ez a művelet a Workday Studio XML-szerkesztővel nyissa meg a fájlt.

    ![Munkanapi Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. A fájl konzolfáján navigáljon a keresztül   **/env: Burkológörbe > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: Feldolgozó** a felhasználói adatok kereséséhez.

15. A **wd: Feldolgozó**, keresse meg a hozzáadni kívánt attribútum, és kattintson rá.

16. Másolja a kijelölt attribútum közül az XPath-kifejezést a **Hibadokumentum elérési útjának** mező.

17. Távolítsa el a **/env:Envelope / env:Body / wd:Get_Workers_Response / wd:Response_Data /** előtag a másolt kifejezés.

18. Ha a másolt kifejezés utolsó eleme-e a csomópont (Példa: "/ wd: Birth_Date"), majd fűzze **/text()** kifejezés végén. Ez nem szükséges, ha az utolsó elem egy attribútum (Példa: "/@wd: típusa").

19. Az eredmény lehet hasonló `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Az értéke be az Azure Portalra fogja másolni.

**Az egyéni Workday felhasználói attribútum hozzáadása az üzembe helyezési konfiguráció:**

1. Indítsa el a [az Azure portal](https://portal.azure.com), és lépjen a kiépítési szakaszra, az alkalmazás, kiépítés munkanap ebben az oktatóanyagban korábban leírtak szerint.

2. Állítsa be **üzembe helyezési állapotra** való **ki**, és válassza ki **mentése**. Ez a lépés biztosítja csak akkor, amikor készen áll a módosítások érvénybe léptetéséhez.

3. Alatt **leképezések**válassza **a helyi Active Directory-szinkronizálás Workday feldolgozók** (vagy **Workday feldolgozók szinkronizálása az Azure AD**).

4. A következő képernyő alján, és válassza ki **speciális beállítások megjelenítése**.

5. Válassza ki **Workday attribútumlistájának szerkesztése**.

    ![Munkanapi Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Hol tárolja a rendszer az adatbeviteli mezők attribútum lista alján görgessen.

7. A **neve**, adja meg az attribútum megjelenítendő nevét.

8. A **típus**, válassza ki a megfelelő megfelelő az attribútum típusa (**karakterlánc** leggyakrabban).

9. A **API kifejezés**, írja be a kimásolt Workday studióból XPath-kifejezés. Például: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Válassza ki **attribútum hozzáadása**.

    ![Munkanapi Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Válassza ki **mentése** a fenti, majd **Igen** a párbeszédpanelre. Zárja be a attribútumleképezés képernyő, ha folyamatban.

12. Vissza a fő **kiépítési** lapon jelölje be **a helyi Active Directory-szinkronizálás Workday feldolgozók** (vagy **feldolgozók szinkronizálása az Azure AD**) újra.

13. Válassza ki **új megfeleltetés hozzáadása**.

14. Az új attribútumot meg kell jelennie a **forrásattribútum** listája.

15. Adja hozzá az új attribútumot leképezése igény szerint.

16. Amikor végzett, ne felejtse el beállítása **üzembe helyezési állapotra** vissza **a** , és mentse.

### <a name="exporting-and-importing-your-configuration"></a>A konfiguráció importálása és exportálása

Ez a szakasz ismerteti, hogyan használhatja a Microsoft Graph API és a Graph Explorer a Workday-kiépítés attribútumleképezések és séma exportálása egy JSON-fájlt, és importálja újra az Azure AD-be.

#### <a name="step-1-retrieve-your-workday-provisioning-app-service-principal-id-object-id"></a>1. lépés: Lekérni a Workday kiépítési alkalmazás szolgáltatásnév Azonosítóját (objektumazonosító:)

1. Indítsa el a [az Azure portal](https://portal.azure.com), és keresse meg az alkalmazás-kiépítés Workday tulajdonságok szakaszát.
1. Az üzembe helyezési alkalmazás Tulajdonságok területen másolja a társított GUID értéket a *Objektumazonosító* mező. Ennek az értéknek is nevezik a **ServicePrincipalId** az alkalmazást, és azt használni kívánt Graph Explorer műveleteket.

   ![A WORKDAY App Service résztvevő-azonosító](./media/workday-inbound-tutorial/wd_export_01.png)

#### <a name="step-2-sign-into-microsoft-graph-explorer"></a>2. lépés: Jelentkezzen be a Microsoft Graph Explorer

1. Indítsa el a [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Kattintson a "Bejelentkezés a Microsoft" gombra, és jelentkezzen be az Azure AD globális rendszergazdai vagy alkalmazás rendszergazdai hitelesítő adatok használatával.

    ![Graph Sign-in](./media/workday-inbound-tutorial/wd_export_02.png)

1. Esetén a bejelentkezés sikeres megjelenik a felhasználói fiók adatait a bal oldali ablaktábla.

#### <a name="step-3-retrieve-the-provisioning-job-id-of-the-workday-provisioning-app"></a>3. lépés: A kiépítési Feladatazonosító a Workday kiépítési alkalmazás beolvasása

A Microsoft Graph Explorer futtassa a következő GET-lekérdezést, és cserélje le a [servicePrincipalId] az a **ServicePrincipalId** kinyert a [1. lépés](#step-1-retrieve-your-workday-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

A válasz jelenik meg lent látható módon. Másolja az "id"attribútum a válaszban található. Ez az érték a **ProvisioningJobId** és a mögöttes séma-metaadatok lekérése céljából használható.

   [![Üzembe helyezési feladat azonosítója](./media/workday-inbound-tutorial/wd_export_03.png)](./media/workday-inbound-tutorial/wd_export_03.png#lightbox)

#### <a name="step-4-download-the-provisioning-schema"></a>4. lépés: Töltse le a kiépítési séma

A Microsoft Graph Explorer futtassa a következő GET-lekérdezés, és cserélje le a [servicePrincipalId] és [ProvisioningJobId] a ServicePrincipalId és az előző lépésben lekért a ProvisioningJobId.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

A JSON-objektum átmásolhatja a választ, és mentse a fájlt, készítsen biztonsági másolatot a séma.

#### <a name="step-5-import-the-provisioning-schema"></a>5. lépés: A kiépítési séma importálása

> [!CAUTION]
> Csak akkor, ha a séma konfigurációjához, amely nem módosítható az Azure portal használatával módosítani kell, vagy ha a konfiguráció visszaállítása biztonsági másolatból fájl érvényes és működő séma, hajtsa végre ezt a lépést.

A Microsoft Graph Explorer konfigurálása a következő PUT-lekérdezés, és cserélje le a [servicePrincipalId] és [ProvisioningJobId] a ServicePrincipalId és az előző lépésben lekért a ProvisioningJobId.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

A "Kérelem törzse" lapon másolja a JSON-fájl tartalmát.

   [![Kérelem törzse](./media/workday-inbound-tutorial/wd_export_04.png)](./media/workday-inbound-tutorial/wd_export_04.png#lightbox)

A "Request típusú fejlécekkel együtt" lap "application/json" értékű, a Content-Type fejléc attribútum hozzáadása

   [![Kérelem fejlécei](./media/workday-inbound-tutorial/wd_export_05.png)](./media/workday-inbound-tutorial/wd_export_05.png#lightbox)

Kattintson a "Lekérdezés futtatása" gombra az új séma importálása.

## <a name="managing-personal-data"></a>Személyes adatok kezelése

A Workday megoldás kiépítése az Active Directory szükséges egy kiépítési ügynököt telepíteni kell egy helyszíni Windows server, és ez az ügynök naplók az Windows eseménynaplóban, amelyek tartalmazhatnak személyes adatait a Workday az AD-attribútum függvényében hoz létre. leképezések. Ahhoz, hogy megfeleljenek adatvédelmi kötelezettségeit felhasználói, biztosíthatja, hogy adatok nem őrződnek meg abban az esetben, ha egy Windows beállításával 48 óra naplókat ütemezett feladat törölje az eseménynaplóban.

Az Azure AD létesítési szolgáltatás lép át a **adatfeldolgozó** GDPR besorolási kategóriája. Egy adatfeldolgozó folyamatot, mint a szolgáltatás adatokat feldolgozó szolgáltatásokat nyújt a kulcs partnerek és a végfelhasználók a fogyasztók. Azure AD létesítési szolgáltatás nem hozhat létre a felhasználói adatokat, és nincs független vezérléssel rendelkezik, milyen személyes adatokat gyűjteni, és azok felhasználási módjáról. Adatok beolvasása, aggregáció, elemzés és jelentéskészítés az Azure AD létesítési szolgáltatás meglévő vállalati adatok alapján.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

Adatmegőrzés, garanciát az Azure AD létesítési szolgáltatás nem jelentések készítése, analitikai vagy tárhat fel összefüggéseket a 30 napos időszak letelte után. Ezért az Azure AD létesítési szolgáltatás nem tárol, dolgoz fel, vagy megőrizni adatokat 30 napos időszak letelte után. Ez a kialakítás az általános adatvédelmi rendelet előírásainak, a Microsoft adatvédelmi előírásoknak és az adatmegőrzési házirendek az Azure AD megfelelő.

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)
* [Ismerje meg, hogyan konfigurálása egyszeri bejelentkezéshez Workday és az Azure Active Directory között](workday-tutorial.md)
* [Ismerje meg, hogyan integrálhatja más SaaS-alkalmazásokhoz az Azure Active Directoryval](tutorial-list.md)
* [Ismerje meg, hogyan kiépítési konfigurációk kezelése a Microsoft Graph API-k használatával](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
