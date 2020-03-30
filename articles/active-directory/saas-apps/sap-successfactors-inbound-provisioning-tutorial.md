---
title: 'Oktatóanyag: A SuccessFactors bejövő kiépítéskonfigurálása az Azure Active Directoryban | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja a bejövő kiépítést a SuccessFactors-ból
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: 1ff90231-1312-463e-8949-7d976e433fc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2019
ms.author: chmutali
ms.openlocfilehash: d9317a68c8967fbe0728e8c47e59dd33367c6163
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249684"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning-preview"></a>Oktatóanyag: Az SAP SuccessFactors konfigurálása az Active Directory felhasználói kiépítéséhez (előzetes verzió)
Ez az oktatóanyag célja, hogy megjelenítse azokat a lépéseket, amelyeket el kell végeznie a felhasználók nak a SuccessFactors Employee Central-ból az Active Directoryba (AD) és az Azure AD-be való kiépítéséhez, az e-mail cím nem kötelező visszaírásával a SuccessFactors számára. Ez az integráció nyilvános előzetes verzióban érhető el, és több mint [70 felhasználói attribútum](../app-provisioning/sap-successfactors-attribute-reference.md) beolvasását támogatja a SuccessFactors Employee Central alkalmazástól.

>[!NOTE]
>Akkor használja ezt az oktatóanyagot, ha a SuccessFactors-ból kiépíteni kívánt felhasználóknak helyszíni AD-fiókra és szükség esetén Egy Azure AD-fiókra van szükségük. Ha a successfactors felhasználóinak csak Azure AD-fiókra van szükségük (csak felhőbeli felhasználók), akkor tekintse meg az [SAP SuccessFactors konfigurálása az Azure AD-felhasználók kiépítéséhez című](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) oktatóanyagot. 


## <a name="overview"></a>Áttekintés

Az [Azure Active Directory felhasználói kiépítési szolgáltatás](../app-provisioning/user-provisioning.md) integrálja a [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) a felhasználók identitáséletciklusának kezelése érdekében. 

Az Azure AD felhasználói létesítési szolgáltatás által támogatott SuccessFactors felhasználói kiépítési munkafolyamatok lehetővé teszik a következő emberi erőforrások és identitáséletciklus-kezelési forgatókönyvek automatizálását:

* **Új alkalmazottak felvétele** – Ha új alkalmazottat ad hozzá a SuccessFactors szolgáltatáshoz, a rendszer automatikusan létrehoz egy felhasználói fiókot az Active Directoryban, az Azure Active Directoryban, és adott esetben az Azure 365-ben és [az Azure AD által támogatott egyéb SaaS-alkalmazásokban,](../app-provisioning/user-provisioning.md)az e-mail cím sikeres tényezőknek való visszaírásával.

* **Alkalmazotti attribútum- és profilfrissítések** – Amikor egy alkalmazotti rekordot frissíta SuccessFactors (például a nevük, címük vagy kezelőjük) frissítését, felhasználói fiókjuk automatikusan frissül az Active Directoryban, az Azure Active Directoryban, és adott esetben az Office 365-ben és [az Azure AD által támogatott egyéb SaaS-alkalmazásokban.](../app-provisioning/user-provisioning.md)

* **Alkalmazottak megszűnése** – Ha egy alkalmazott a SuccessFactors szolgáltatásban megszűnik, a felhasználói fiók automatikusan le lesz tiltva az Active Directoryban, az Azure Active Directoryban, és adott esetben az Office 365-ben és [az Azure AD által támogatott egyéb SaaS-alkalmazásokban.](../app-provisioning/user-provisioning.md)

* **Alkalmazotti újrahires** – Ha egy alkalmazottat újra felvesznek a SuccessFactors szolgáltatásban, a régi fiókja automatikusan újraaktiválható vagy újra kiépíthető (a preferenciától függően) az Active Directory, az Azure Active Directory, és adott esetben az Office 365 és [az Azure AD által támogatott egyéb SaaS-alkalmazások számára.](../app-provisioning/user-provisioning.md)

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Ki ez a felhasználói kiépítési megoldás a legalkalmasabb?

Ez a SuccessFactors az Active Directory felhasználói kiépítési megoldás ideális a következőkhöz:

* Azok a szervezetek, amelyek előre elkészített, felhőalapú megoldást szeretnének a SuccessFactors felhasználói kiépítéshez

* Közvetlen felhasználói kiépítést igénylő szervezetek a SuccessFactors és az Active Directory között

* Olyan szervezetek, amelyek megkövetelik a felhasználók kiépítését a [SuccessFactors Employee Central (EK)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) által beszerzett adatok felhasználásával

* Olyan szervezetek, amelyek csak a [SuccessFactors Employee Central (EK)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) alkalmazásban észlelt változásadatok alapján szeretnék összecsatlakozni, átkell helyeznünk és a felhasználókat egy vagy több Active Directory erdővel, tartománnyal és szervezeti állapotra kell szinkronizálni.

* Szervezetek, amelyek az Office 365-ös levelezést használják

## <a name="solution-architecture"></a>Megoldás architektúrája

Ez a szakasz a közös hibrid környezetek végpontok között lévő felhasználói kiépítési megoldásarchitektúrát ismerteti. Két kapcsolódó folyamat létezik:

* **Mérvadó HR-adatfolyam – a SuccessFactors-tól a helyszíni Active Directoryig:** Ebben a folyamatban a feldolgozóesemények (például az új bérletek, átvitelek, felmondások) először a felhőben SuccessFactors Employee Central, majd az esemény adatok áramlik a helyszíni Active Directory az Azure AD és a kiépítési ügynök. Az eseménytől függően az AD-ben létrehozási/frissítési/enable/disable műveleteket eredményezhet.
* **E-mail visszaírási folyamat – a helyszíni Active Directorytól a SuccessFactors-ig:** Miután a fiók létrehozása befejeződött az Active Directoryban, az Azure AD-vel az Azure AD sync és az e-mail attribútum visszaírható a SuccessFactors szolgáltatásba.

  ![Áttekintés](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Végpontok között lévő felhasználói adatfolyam

1. A HR-csapat munkavégző tranzakciókat hajt végre (Joiners/Mozgatók/Kilépők vagy Új bérlők/Transzferek/Felmondások) a SuccessFactors Employee Central-ban
2. Az Azure AD-létesítési szolgáltatás futtatja az identitások ütemezett szinkronizálását a SuccessFactors EC-től, és azonosítja azokat a módosításokat, amelyeket fel kell dolgozni a helyszíni Active Directoryval való szinkronizáláshoz.
3. Az Azure AD-kiépítési szolgáltatás meghívja a helyszíni Azure AD Connect kiépítési ügynök egy kérelem hasznos, amely tartalmazza az AD-fiók létrehozása/frissítése/engedélyezése/letiltása műveleteket.
4. Az Azure AD Connect kiépítési ügynök egy szolgáltatásfiókot használ az AD-fiók adatainak hozzáadásához/frissítéséhez.
5. Az Azure AD Connect Sync motor javunkra szinkronizálása különbözeti szinkronizálás lekérése frissítések a D.The Azure AD Connect Sync engine runs delta sync to pull updates in AD.
6. Az Active Directory frissítései szinkronizálva vannak az Azure Active Directoryval.
7. Ha a [SuccessFactors Writeback alkalmazás](sap-successfactors-writeback-tutorial.md) konfigurálva van, a használt megfelelő attribútum alapján visszaküldi az e-mail attribútumot a SuccessFactors-nak.

## <a name="planning-your-deployment"></a>A telepítés megtervezése

A Cloud HR-vezérelt felhasználók nak a SuccessFactors-ból az AD-be történő kiépítésének konfigurálása jelentős tervezést igényel, amely különböző szempontokat fed le, mint például:
* Az Azure AD Connect kiépítési ügynök beállítása 
* Az AD-felhasználók üzembe helyező alkalmazásainak sikertényezőinek száma
* Egyező azonosító, attribútumleképezés, átalakítási és hatókörszűrők

Kérjük, olvassa el a [felhő HR-telepítési terv](../app-provisioning/plan-cloud-hr-provision.md) átfogó iránymutatásokat ezekről a témakörökről. 

## <a name="configuring-successfactors-for-the-integration"></a>A SuccessFactors konfigurálása az integrációhoz

A SuccessFactors kiépítési összekötők közös követelménye, hogy a SuccessFactors-fiók hitelesítő adataira van szükség a SuccessFactors OData API-k meghívásához megfelelő engedélyekkel. Ez a szakasz a Service-fiók sikerestényezőkben történő létrehozásának lépéseit ismerteti, és megadja a megfelelő engedélyeket. 

* [API felhasználói fiók létrehozása/azonosítása a SuccessFactors ban](#createidentify-api-user-account-in-successfactors)
* [API-engedélyszerepkör létrehozása](#create-an-api-permissions-role)
* [Engedélycsoport létrehozása az API-felhasználó számára](#create-a-permission-group-for-the-api-user)
* [Engedélyszerepkör megadása az engedélycsoportnak](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>API felhasználói fiók létrehozása/azonosítása a SuccessFactors ban
Együttműködve a SuccessFactors felügyeleti csapatával vagy a megvalósítási partnerrel, hogy hozzon létre vagy azonosítson egy felhasználói fiókot a SuccessFactors ban, amely et az OData API-k meghívására fog használni. A fiók felhasználónevének és jelszavának hitelesítő adatai szükségesek a kiépítési alkalmazások azure AD-ben történő konfigurálásakor. 

### <a name="create-an-api-permissions-role"></a>API-engedélyszerepkör létrehozása

* Jelentkezzen be az SAP SuccessFactors egy felhasználói fiókkal, amely hozzáfér a Felügyeleti központhoz.
* Keressen *engedélyszerepkörök kezelése ,* majd válassza **az Engedélyszerepkörök kezelése** a keresési eredmények között.
  ![Engedélyszerepkörök kezelése](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* Az Engedélyszerepkör-listában kattintson az **Új létrehozása gombra.**
  > [!div class="mx-imgBorder"]
  > ![Új engedélyszerepkör létrehozása](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Adja hozzá az új engedélyszerepkör **szerepkörnevét** és **leírását.** A névnek és a leírásnak azt kell jeleznie, hogy a szerepkör API-használati engedélyekhez kapcsolódik.
  > [!div class="mx-imgBorder"]
  > ![Engedélyszerepkör részletei](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* Az Engedélybeállítások csoportban kattintson az **Engedély... gombra,** majd görgessen lefelé az engedélylistában, és kattintson az **Integrációs eszközök kezelése**parancsra. Jelölje be az **OData API-hoz való hozzáférés engedélyezése egyszerű hitelesítéssel**jelölőnégyzet.
  > [!div class="mx-imgBorder"]
  > ![Integrációs eszközök kezelése](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Görgessen le ugyanabban a mezőben, és válassza **az Employee Central API**lehetőséget. Adja hozzá az engedélyeket az alábbiak szerint az ODATA API használatával történő olvasáshoz és az ODATA API használatával történő szerkesztéshez. Válassza a szerkesztési lehetőséget, ha ugyanazt a fiókot kívánja használni a Visszaértékelés a SuccessFactors forgatókönyvhöz. 
  > [!div class="mx-imgBorder"]
  > ![Írási engedélyek olvasása](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

  >[!NOTE]
  >A kiépítési alkalmazás által beolvasott attribútumok teljes listáját a [SuccessFactors attribútumhivatkozás című részében tájékozódhat.](../app-provisioning/sap-successfactors-attribute-reference.md)

* Kattintson a **Kész gombra.** Kattintson a **Save Changes** (Módosítások mentése) gombra.

### <a name="create-a-permission-group-for-the-api-user"></a>Engedélycsoport létrehozása az API-felhasználó számára

* A SuccessFactors Felügyeleti központban keresse meg az *Engedélycsoportok kezelése*kifejezést, majd válassza az **Engedélycsoportok kezelése lehetőséget** a keresési eredmények között.
  > [!div class="mx-imgBorder"]
  > ![Engedélycsoportok kezelése](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* Az Engedélycsoportok kezelése ablakban kattintson az **Új létrehozása gombra.**
  > [!div class="mx-imgBorder"]
  > ![Új csoport hozzáadása](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Adja hozzá az új csoport nevét. A csoport nevének azt kell jeleznie, hogy a csoport API-felhasználókszámára van.
  > [!div class="mx-imgBorder"]
  > ![Engedélycsoport neve](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Tagok felvétele a csoportba. Válassza például a **Felhasználónevet** a Személyek készlete legördülő menüből, majd adja meg az integrációhoz használt API-fiók felhasználónevét. 
  > [!div class="mx-imgBorder"]
  > ![Csoporttagok hozzáadása](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Az engedélycsoport létrehozásának befejezéséhez kattintson a **Kész** gombra.

### <a name="grant-permission-role-to-the-permission-group"></a>Engedélyszerepkör megadása az engedélycsoportnak

* A SuccessFactors Felügyeleti központban keresse meg az *Engedélyszerepek kezelése kifejezést,* majd válassza az **Engedélyszerepek kezelése lehetőséget** a keresési eredmények között.
* Az **Engedélyszerepkör-listában**válassza ki az API-használati engedélyekhez létrehozott szerepkört.
* A **Szerep megadása csoportban**kattintson a **Hozzáadás...** gombra.
* Válassza az **Engedélycsoport...** lehetőséget a legördülő menüben, majd a **Kijelölés parancsra** a Csoportok ablak megnyitásához és a fent létrehozott csoport kiválasztásához. 
  > [!div class="mx-imgBorder"]
  > ![Engedélycsoport hozzáadása](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Tekintse át az engedélyszerepkör-támogatást az engedélycsoportnak. 
  > [!div class="mx-imgBorder"]
  > ![Engedélyszerepkör és csoport részletei](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Kattintson a **Save Changes** (Módosítások mentése) gombra.

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>Felhasználói kiépítés konfigurálása a SuccessFactors szolgáltatásból az Active Directoryba

Ez a szakasz a Felhasználói fiókok Nakadi-tartományból az integráció hatókörén belül minden Active Directory-tartományba történő kiépítésének lépéseit ismerteti.

* [Adja hozzá a kiépítési összekötő alkalmazást, és töltse le a kiépítési ügynök](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Helyszíni kiépítési ügynök(ek) telepítése és konfigurálása](#part-2-install-and-configure-on-premises-provisioning-agents)
* [A SuccessFactors és az Active Directory kapcsolatának konfigurálása](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [Attribútumleképezések konfigurálása](#part-4-configure-attribute-mappings)
* [Felhasználói kiépítés engedélyezése és elindítása](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>1. rész: Adja hozzá a létesítési összekötő alkalmazást, és töltse le a kiépítési ügynök

**A SuccessFactors konfigurálása az Active Directory kiépítéséhez:**

1. Nyissa meg a következőt: <https://portal.azure.com>

2. A bal oldali navigációs sávon válassza az **Azure Active Directory lehetőséget.**

3. Válassza **a Vállalati alkalmazások**lehetőséget, majd az Összes **alkalmazás**lehetőséget.

4. Válassza **az Alkalmazás hozzáadása**lehetőséget, majd az **Összes** kategóriát.

5. Keressen **meg successfactors az Active Directory felhasználói kiépítés**, és adja hozzá, hogy az alkalmazás a katalógusból.

6. Az alkalmazás hozzáadása és az alkalmazás részletei képernyő megjelenítése után válassza **a Kiépítés** lehetőséget.

7. A **létesítési** **mód** módosítása **automatikusra**

8. Kattintson a megjelenő információs banner re a kiépítési ügynök letöltéséhez. 
   > [!div class="mx-imgBorder"]
   > ![Agent letöltése](./media/sap-successfactors-inbound-provisioning/download-pa-agent.png "Ügynök képernyő letöltése")


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

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>3. rész: A kiépítési alkalmazásban konfigurálja a kapcsolatot a SuccessFactors és az Active Directory számára
Ebben a lépésben kapcsolatot létesítünk a SuccessFactors és az Active Directory az Azure Portalon. 

1. Az Azure Portalon lépjen vissza a SuccessFactors az [1.](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Töltse ki a **Rendszergazdai hitelesítő adatok szakaszt** az alábbiak szerint:

   * **Rendszergazdai felhasználónév** – Adja meg a SuccessFactors API felhasználói fiókjának felhasználónevét a hozzáfűzendő vállalatazonosítóval. Ez a formátum: **felhasználónév\@companyID**

   * **Rendszergazdai jelszó –** Adja meg a SuccessFactors API felhasználói fiók jelszavát. 

   * **Bérlő url-címe –** Adja meg a SuccessFactors OData API-szolgáltatások végpontjának nevét. Csak http vagy https nélküli kiszolgáló állomásnevét adja meg. Ennek az értéknek így kell kinéznie: **<api-server-name>.successfactors.com**.

   * **Active Directory erdő -** Az Active Directory-tartomány "neve", az ügynöknél regisztráltállapotban. A legördülő menü segítségével válassza ki a kiépítés céltartományát. Ez az érték általában egy karakterlánc, mint: *contoso.com*

   * **Active Directory tároló -** Adja meg azt a tárolódnát, amelyben az ügynöknek alapértelmezés szerint felhasználói fiókokat kell létrehoznia.
        Példa: *OU=Users,DC=contoso,DC=com*
        > [!NOTE]
        > Ez a beállítás csak akkor lép életbe a felhasználói fiókok létrehozásakor, ha a *parentDistinguishedName* attribútum nincs konfigurálva az attribútumleképezésekben. Ez a beállítás nem használható felhasználói keresési vagy frissítési műveletekhez. A teljes tartományalfa a keresési művelet hatálya alá tartozik.

   * **Értesítési e-mail –** Adja meg e-mail címét, és jelölje be az "E-mail küldése hiba esetén" jelölőnégyzetet.
    > [!NOTE]
    > Az Azure AD-kiépítési szolgáltatás e-mail értesítést küld, ha a létesítési feladat [karanténállapotba](/azure/active-directory/manage-apps/application-provisioning-quarantine-status) kerül.

   * Kattintson a **Kapcsolat tesztelése** gombra. Ha a kapcsolatteszt sikeres, kattintson **a** mentés gombra a tetején. Ha nem sikerül, ellenőrizze, hogy a SuccessFactors hitelesítő adatok és az ügynök beállításán konfigurált AD hitelesítő adatok érvényesek-e.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * A hitelesítő adatok sikeres mentése után a **Hozzárendelések** szakasz megjeleníti a **SuccessFactors-felhasználók** és a helyszíni Active Directory alapértelmezett hozzárendelését.

### <a name="part-4-configure-attribute-mappings"></a>4. rész: Attribútumleképezések konfigurálása

Ebben a szakaszban konfigurálhatja, hogy a felhasználói adatok hogyan áramlanak a SuccessFactors szolgáltatásból az Active Directoryba.

1. A **Hozzárendelések**csoport Kiépítés lapján kattintson a **SuccessFactors-felhasználók szinkronizálása a helyszíni Active Directoryval**elemre.

1. A **Forrásobjektum hatóköre** mezőben az attribútumalapú szűrők készletének meghatározásával kiválaszthatja, hogy a SuccessFactors mely felhasználói csoportoknak kell az AD-be való kiépítés hatókörében lennie. Az alapértelmezett hatókör a "SuccessFactors összes felhasználója". Példa szűrők:

   * Példa: Hatókör a personIdExternal felhasználók számára 1000000 és 2000000 között (2000000 kivételével)

      * Attribútum: personIdExternal

      * Operátor: REGEX Match

      * Érték: (1[0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9])

   * Példa: Csak alkalmazottak és nem függő dolgozók

      * Attribútum: Alkalmazottazonosító

      * Operátor: NEM NULL

   > [!TIP]
   > Amikor első alkalommal konfigurálja a kiépítési alkalmazást, tesztelnie és ellenőriznie kell az attribútumleképezéseket és -kifejezéseket, hogy megbizonyosodjon arról, hogy az a kívánt eredményt adja. A Microsoft azt javasolja, hogy a **Forrásobjektum-hatókör** hatóköre hatóköre segítségével tesztelje a leképezéseket néhány tesztfelhasználóval a SuccessFactors alkalmazásból. Miután meggyőződött arról, hogy a leképezések működnek, eltávolíthatja a szűrőt, vagy fokozatosan kibonthatja, hogy több felhasználóval is felvegye.

   > [!CAUTION] 
   > A kiépítési motor alapértelmezett viselkedése a hatókörön kívül eső felhasználók letiltása/törlése. Ez nem feltétlenül kívánatos a SuccessFactors to AD integrációban. Az alapértelmezett viselkedés felülbírálásához olvassa el a [hatókörön kívül eső felhasználói fiókok törlésének kihagyása című cikket.](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. A **Célobjektum-műveletek mezőben** globálisan szűrheti, hogy milyen műveleteket hajt végre az Active Directoryban. **A létrehozás** és **a frissítés** a leggyakoribb.

1. Az **Attribútumleképezések** szakaszban meghatározhatja, hogy az egyes SuccessFactors attribútumok hogyan feleljenek meg az Active Directory-attribútumokhoz.

  >[!NOTE]
  >Az alkalmazás által támogatott SuccessFactors attribútum teljes listáját a [SuccessFactors attribútum hivatkozási száma tartalmazza.](../app-provisioning/sap-successfactors-attribute-reference.md)


1. A frissítéshez kattintson egy meglévő attribútumleképezésre, vagy kattintson az **Új leképezés hozzáadása** gombra a képernyő alján az új leképezések hozzáadásához. Egy egyéni attribútumleképezés támogatja a következő tulajdonságokat:

      * **Leképezés típusa**

         * **Közvetlen** – Az AD attribútum SuccessFactors attribútumának értékét írja be, módosítások nélkül

         * **Állandó** – írjon statikus, állandó karakterláncértéket az AD attribútumba

         * **Kifejezés** – Lehetővé teszi, hogy egyéni értéket írjon az AD attribútumba egy vagy több SuccessFactors attribútum alapján. [További információt a kifejezésekről szóló cikkben talál.](../app-provisioning/functions-for-customizing-application-data.md)

      * **Forrásattribútum** – A SuccessFactors felhasználói attribútuma

      * **Alapértelmezett érték** – nem kötelező. Ha a forrásattribútum üres értékkel rendelkezik, a leképezés ezt az értéket írja be helyette.
            A leggyakoribb konfiguráció ezt üresen hagyja.

      * **Célattribútum** – A felhasználói attribútum az Active Directoryban.

      * **Az ezzel az attribútummal egyeztethető kontakaró objektumok** – Azt jelzi, hogy ezt a leképezést kell-e használni a felhasználók és az Active Directory közötti egyedi azonosításhoz. Ez az érték általában a SuccessFactors munkavégzőazonosító mezőjében van beállítva, amely általában az Active Directory egyik alkalmazotti azonosító attribútumához van leképezve.

      * **Egyező sorrend** – Több egyező attribútum is beállítható. Ha több van, akkor a program az ebben a mezőben meghatározott sorrendben értékeli ki őket. Amint egyezést talál, a rendszer nem értékeli ki a további egyező attribútumokat.

      * **A leképezés alkalmazása**

         * **Mindig** – A leképezés alkalmazása a felhasználó létrehozási és frissítési műveletekre is

         * **Csak a létrehozás során** – A leképezés alkalmazása csak a felhasználó létrehozási műveleteire

1. A leképezések mentéséhez kattintson a **Mentés** gombra az Attribútumleképezés szakasz tetején.

Miután az attribútumleképezés konfigurációja befejeződött, most [már engedélyezheti és elindíthatja a felhasználói kiépítési szolgáltatást.](#enable-and-launch-user-provisioning)

## <a name="enable-and-launch-user-provisioning"></a>Felhasználói kiépítés engedélyezése és elindítása

Miután a SuccessFactors kiépítése alkalmazás konfigurációk befejeződött, bekapcsolhatja a kiépítési szolgáltatás az Azure Portalon.

> [!TIP]
> Alapértelmezés szerint, ha bekapcsolja a létesítési szolgáltatás, akkor kezdeményezi a létesítési műveletek et a hatókörben lévő összes felhasználó számára. Ha hibák vannak a leképezési vagy SuccessFactors adatproblémák, majd a létesítési feladat sikertelen lehet, és a karantén állapotba kerül. Ennek elkerülése érdekében ajánlott eljárásként azt javasoljuk, hogy konfigurálja **a Forrásobjektum-hatókör** szűrőt, és tesztelje az attribútum-leképezéseket néhány tesztfelhasználóval, mielőtt elindítaná a teljes szinkronizálást az összes felhasználó számára. Miután meggyőződött arról, hogy a leképezések működnek, és a kívánt eredményt adják, eltávolíthatja a szűrőt, vagy fokozatosan kibonthatja, hogy több felhasználót vegyen fel.

1. A **Kiépítés** lapon állítsa a **Kiépítési állapot** ot **Bekapcsolva**.

2. Kattintson a **Mentés** gombra.

3. Ez a művelet elindítja a kezdeti szinkronizálást, amely változó számú órát vehet igénybe attól függően, hogy hány felhasználó van a SuccessFactors bérlőben. Ellenőrizheti a folyamatjelzősávot a szinkronizálási ciklus előrehaladásának nyomon követéséhez. 

4. Bármikor tekintse meg a **naplózási naplók** lapon az Azure Portalon, hogy milyen műveleteket hajtott végre a létesítési szolgáltatás. A naplózási naplók felsorolja a kiépítési szolgáltatás által végrehajtott összes egyes szinkronizálási eseményt, például azt, hogy mely felhasználókat olvassa fel a SuccessFactors szolgáltatásból, majd ezt követően hozzáadják vagy frissítik az Active Directoryban. 

5. Miután a kezdeti szinkronizálás befejeződött, egy naplózási összefoglaló jelentést fog írni a **Kiépítés** lapon, az alábbiak szerint.

   > [!div class="mx-imgBorder"]
   > ![Kiépítés folyamatjelző](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>További lépések

* [További információ a támogatott SuccessFactors attribútumokról a bejövő kiépítéshez](../app-provisioning/sap-successfactors-attribute-reference.md)
* [További információ az e-mailek visszaírásának konfigurálásáról a SuccessFactors számára](sap-successfactors-writeback-tutorial.md)
* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
* [Megtudhatja, hogy miként konfigurálható az egyszeri bejelentkezés a SuccessFactors és az Azure Active Directory között](successfactors-tutorial.md)
* [További információ arról, hogyan integrálhat más SaaS-alkalmazásokat az Azure Active Directoryval](tutorial-list.md)
* [További információ a kiépítési konfigurációk exportálásáról és importálásáról](../app-provisioning/export-import-provisioning-configuration.md)
