---
title: What's new in Azure Active Directory vállalati Alkalmazáskezelés |} Microsoft Docs
description: Ismerje meg a vállalati alkalmazások kezelése az Azure Active Directory újdonságai.
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
editor: ''
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: asteen
ms.reviewer: asteen
ms.openlocfilehash: 8d1b24708380aeed6055912fcf3538f0e5319e2d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="whats-new-in-enterprise-application-management-in-azure-active-directory"></a>Vállalati alkalmazások kezelése az Azure Active Directory újdonságai 

Azure Active Directory (Azure AD) továbbfejlesztett vállalati alkalmazás felügyeleti eszközöket, az új szolgáltatásokat és képességeket egyszerűbb és hatékony kezelése alkalmazások tételére.

Az alábbiakban néhány olyan a fejlesztésen esett át az Azure AD-t a [Azure-portálon](https://portal.azure.com).

- Egy továbbfejlesztett alkalmazáskatalógusában egy egyszerűsített létrehozása alkalmazásmodell élményt, és a használt összes alkalmazástípusokat támogatja. 
- Egy vadonatúj gyors üzembe helyezési élményt nyújt segítséget az alkalmazás a próbaüzem az induláshoz. 
- Néhány kattintással önkiszolgáló-szabályzatok konfigurálása. 
- Az alkalmazásproxy, az egyszeri bejelentkezés konfigurálása és a saját felhasználói élményre, teszi több kész, mint előtt kapcsolja.

## <a name="improvements-to-the-azure-active-directory-application-gallery"></a>Az Azure Active Directory Alkalmazáskatalógusában fejlesztései

Adja hozzá a kedvenc alkalmazások, hogy származó a [alkalmazáskatalógusában](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery), akkor most kiterjesztése a felhőbe egyéni alkalmazások vagy kidolgozása új alkalmazások.  Ismerkedés az új felhasználói élmény a kattintva **Hozzáadás** a a **vállalati alkalmazások** áttekintése vagy **összes alkalmazás** paneleken.
 
  ![Egy alkalmazás hozzáadása](./media/active-directory-enterprise-apps-whats-new-azure-portal/01.png)

Egyszer az oldalon láthatja a kiemelt alkalmazások a felhasználók átadása támogató első középső jelenik meg.  Megkeresheti az Önt érdeklő alkalmazások elemezze különböző kategóriákban kívüli bármilyen típusú, vagy használhatja a keresési élményt biztosít az alkalmazásokat, amelyet integrálni szeretne gyorsan kereséséhez.

  ![Az alkalmazás gyűjteménye](./media/active-directory-enterprise-apps-whats-new-azure-portal/02.png)

## <a name="add-custom-applications-from-one-place"></a>Adja hozzá az egyéni alkalmazások egyetlen helyről

Mellett előre integrált alkalmazások hozzáadása a gyűjteményből, az egyéni alkalmazás konfigurációjának észlel volt amellyel a klasszikus felügyeleti portálon mindegyike most már az új portálon lehetséges. E kiterjesztése a helyszíni saját jelszó vagy összevont egyszeri Bejelentkezéses alkalmazások integrálása az alkalmazásproxy használatával olyan alkalmazást, vagy egy, az alkalmazás beállításjegyzékkel vadonatúj alkalmazás létrehozása, is elérheti azt minden a Ez egy helyen.

  ![Saját alkalmazás hozzáadása](./media/active-directory-enterprise-apps-whats-new-azure-portal/03.png)

 
**Első lépések hozzáadása a saját alkalmazás**:

1. Kattintson a **saját hivatkozás hozzáadása** a alkalmazáskatalógusában tetején. 
2. Látni fogja, elé, két lehetőség közül választhat: **telepítsen egy meglévő alkalmazást** vagy **új alkalmazások fejlesztése**. Olvassa el a további tudnivalók a különbség a két beállítás és a használatukat.

### <a name="deploying-existing-applications"></a>Meglévő alkalmazások központi telepítése

1. Ha van egy alkalmazás már fut, és csak szeretné integrálni Azure ad-val egyszeri bejelentkezést a vagy átadása, válassza ki a **telepítsen egy meglévő alkalmazást** lehetőséget. Adjon meg egy nevet az alkalmazáshoz, kattintson a **Hozzáadás**.
2. Ennyi az egész! Helyett ismerete szükséges előre az alkalmazás adatait, most állíthatja be az új alkalmazás működése a bal oldali menü nézetre lépne, és a kérelem a tetszőlegesen a konfigurálásával bármikor.

  ![Egy kattintással egy meglévő alkalmazás hozzáadása](./media/active-directory-enterprise-apps-whats-new-azure-portal/04.png)
 
### <a name="developing-new-applications"></a>Új alkalmazások fejlesztése

1. Ha az új alkalmazást, nincs egyszerűen tehet szert az alkalmazás beállításjegyzék jobb a gyűjteményből:
2. Kattintson a a **hozzáadása a saját** lehetőséget az alkalmazás gyűjteményből, válassza ki a **kifejleszthet egy meglévő alkalmazást** választás, és megjelenik egy Gyorshivatkozás az alkalmazás hozzáadása élmény való jog.

  ![Néhány kattintással egy újonnan által fejlesztett alkalmazás hozzáadása](./media/active-directory-enterprise-apps-whats-new-azure-portal/05.png)


>[!NOTE]
>Miután hozzáadta az alkalmazás beállításjegyzék használó alkalmazások, láthatja, ahol képes lesz az egyszeri bejelentkezés konfigurálása és kezelése az új alkalmazás-hozzáférési házirendjeit a vállalati alkalmazások listája jelenik meg.

  ![Az új alkalmazást a vállalati alkalmazásokhoz való hozzáférés kezelése](./media/active-directory-enterprise-apps-whats-new-azure-portal/06.png)


## <a name="quick-start-get-going-with-your-new-application-right-away"></a>Gyors üzembe helyezési: azonnal az új alkalmazás használata 

Miután hozzáadott egy alkalmazást, hogy előre integrált kell vagy az alkalmazásba, az első gyorsan földelve az új alkalmazások élményt nyújt a következőkhöz ideális gyors üzembe helyezési élmény nagyságú volt. Ha rendszeresen kövesse a beállításokat, azt fogja a felhasználói felületen keresztül, illetve megmutatja, hogyan induláshoz a próbaüzem az új alkalmazás a lehető leggyorsabban tegye. 
 
  ![Az új alkalmazások gyors start élmény](./media/active-directory-enterprise-apps-whats-new-azure-portal/07.png)

 Az új – gyors üzembe helyezési felhasználói élmény tetszőleges időpontban, és bármely alkalmazás, letöltheti kattintva **gyors üzembe helyezési** alkalmazás bal oldali navigációs menüjében.


## <a name="updated-application-proxy-configuration"></a>Frissített alkalmazás proxy konfigurációja
Most most szóbeli a hozzáadott új alkalmazások közül a helyszíni környezetben fut, és szeretné integrálni az Azure AD-val.  Az új alkalmazás konfigurációs tapasztalatairól a ritkán használt adatok új dolog az új Azure ad portálon, hogy az alkalmazás bejelentkezés módja az alkalmazás proxykonfigurációt a felosztás is most könnyen teszi Egyszeri jelszót, vagy összevont alkalmazásokat, hogy a vállalati hálózat közvetlenül a felhőbe, anélkül, hogy az alkalmazás több példánya létrehozásához.

Továbbá a is konfigurálhat az új alkalmazások felvett új portálról, beleértve az alkalmazások, amelyek támogatják a natív Windows-hitelesítés lép az Azure AD-alkalmazásproxy jobb való használatra.

  ![Az alkalmazás bejelentkezésre lehetőség integrált Windows-hitelesítés konfigurálása](./media/active-directory-enterprise-apps-whats-new-azure-portal/08.png)
 

A natív Windows-hitelesítés alkalmazás konfigurálása az alkalmazásproxy első lépések:
1. Kattintson az egyszeri bejelentkezés navigációs elemre, és válassza a **integrált Windows-hitelesítés** a bejelentkezési beállítások panelen, és konfigurálja a tetszőlegesen a beállításokat.
2. Fölött támogató ezek új hitelesítési módok, most már is feltöltheti a tanúsítványokat a egyéni tartományok biztonságos végpontok a szervezeten belül futó alkalmazásokat támogatja.  
 
   ![Az alkalmazásproxy használni kívánt tanúsítvány feltöltése](./media/active-directory-enterprise-apps-whats-new-azure-portal/09.png)

3. Töltse fel a kedvenc a helyszíni alkalmazások új tanúsítványt, kattintson a a **alkalmazásproxy** a bal oldali navigációs menü, majd a **tanúsítvány** választó, és azt használatával titkosítsa tanúsítványfájl kéri le a felhőbeli végpont az alkalmazás feltöltése.

## <a name="advanced-federated-single-sign-on-configuration"></a>Speciális összevont egyszeri bejelentkezés konfigurálása

Azok a még ma a összevont alkalmazásokat használ nincsenek számos új funkciója a SAML-alapú bejelentkezés beállítási paneljén. Kezdő-és most meg teljesen testreszabása, hozzáadása, távolítsa el, és a meglévő felhasználói attribútumok a SAML-jogkivonatokat jogcímekként kiadott megfeleltetése.
 
  ![Egy összevont alkalmazás átadott a SAML-jogkivonat felhasználói attribútumok testreszabása](./media/active-directory-enterprise-apps-whats-new-azure-portal/10.png)


Ellenőrizze, hogy meg az új összevont egyszeri bejelentkezés konfigurálása:
1. Nyisson meg egy összevont alkalmazás **egyszeri bejelentkezés** panel a bal oldali navigációs menü, és győződjön meg arról, hogy a "*SAML-alapú bejelentkezés** mód van kiválasztva. 
2. Egyszer, engedélyezze a jelölőnégyzet alatti a **felhasználói attribútumok** módosításához az összes attribútum szerepel a SAML-jogkivonat címsor átadott alkalmazásra.

Akkor is is létrehozása, helyettesítő, és összevont egyszeri bejelentkezéshez használt tanúsítványok kezelése, valamint szerkesztheti, akik lekérdezi értesíti, ha a tanúsítvány érvényessége hamarosan lejár. Látni fogja az alábbi új beállításait a **tanúsítványok** az azonos egyszeri bejelentkezés panel fejléc.
 
  ![Egy új tanúsítványt, a lejárat értesítő e-mailt és a tanúsítvány-aláírási beállítások testreszabása létrehozása](./media/active-directory-enterprise-apps-whats-new-azure-portal/11.png)

### <a name="relay-state-paramenter"></a>Továbbítási állapotot paramenter
Végezetül korábban is kiterjesztettük SAML-alapú URL-cím paraméterei közé tartozik a támogatott a **továbbítási-State paraméter**, vagyis a lapon, a felhasználók megnyílik az összevont alkalmazás belül a bejelentkezés befejezése után. Ez az nagyon hasznos beállítással konfigurálható, ha azt szeretné, hogy a felhasználónak egy adott helyre, hogy melyek gyorsan az alkalmazásban.

  ![A SAML továbbítási-State paraméter beállítása](./media/active-directory-enterprise-apps-whats-new-azure-portal/12.png)
 
**A relay-state paraméter beállítása**:

1. Engedélyezze a **megjelenítése speciális URL-beállításainak** jelölőnégyzetet, a a **tartomány és az URL-címek** konfigurációs panel egyszeri bejelentkezést a fejléc. 
2. Ezután a, láthatja, új URL-cím csoportja bemeneti mezőkbe jelenik meg, amely lehetővé teszi a erről és más, SAML-alapú URL-beállításainak beállítása.

## <a name="bring-your-own-password-sso-applications"></a>Kapcsolja a saját jelszavát SSO alkalmazások

Tudjuk, hogy nem minden alkalmazás támogatja-e az összevonási jobb kívül a mezőbe. Például lehet, hogy a hozzáadott új alkalmazások egyike egy egyéni bejelentkezési képernyő, felhasználók által használt felhasználónévvel és jelszóval bejelentkezni. Ilyen típusú alkalmazások az Azure AD használatával továbbra is integrálhatja a **kapcsolja a saját alkalmazásai** szolgáltatás, amely érhető el az új portálon konfigurálható.
 
  ![Egyéni jelszó vaulting alkalmazások az Azure AD integrálása](./media/active-directory-enterprise-apps-whats-new-azure-portal/13.png)

**A "Kapcsolja a saját alkalmazásai" funkció kivétele**:

1. Miután beállította a egyetlen bejelentkezés módját egy új egyéni alkalmazás, amely való felvételét **jelszóalapú bejelentkezés**, adja meg az URL-cím, ahol az alkalmazás Ez a beállítás a bejelentkezési képernyőt, és kattintson a **mentése**.  
2. Ha így tesz, azt fogja automatikusan scrape a felhasználónévhez URL-címet, és jelszó beviteli mező, és biztonságos átvitelére a jelszavakat, hogy a hozzáférési panel bővítmény használó alkalmazások az Azure AD használatára.

## <a name="configure-self-service-application-access"></a>Önkiszolgáló alkalmazás-hozzáférés konfigurálása

Nagy mennyiségű új alkalmazások hozzáadása után lehet, hogy engedélyezni szeretné a felhasználókat, keresse meg és adja hozzá ezeket az alkalmazásokat a saját hozzáférés panelek anélkül, hogy rendszergazdaként többet jelzi, hogy. Most ezzel a legújabb verzióval, konfigurálható és kezelhető önkiszolgáló alkalmazás-hozzáférés közvetlenül a az új portál.

  ![Önkiszolgáló alkalmazás hozzáférése a jelszót egyszeri bejelentkezési alkalmazás konfigurálása](./media/active-directory-enterprise-apps-whats-new-azure-portal/14.png)
 
**Önkiszolgáló alkalmazás-hozzáférés kezelésére és beállítására**:

1. Először jelölje ki a **önkiszolgáló** lehetőséget az alkalmazás a bal oldali navigációs menü és állítsa be a **az alkalmazáshoz való hozzáférés kérését?** lehetőséggel "**Igen**". 
2. Ez lehetővé teszi ki jogosult az alkalmazáshoz való hozzáférés jóváhagyásához, és megkapja az önkiszolgáló felhasználók csoport konfigurálása. Emellett ha az alkalmazás van konfigurálva, a jelszó egyszeri bejelentkezést, azt is megtudhatja egy másik lehetőség, amely lehetővé teszi, hogy ezek jóváhagyóknak kezelheti a jelszavakat az alkalmazáshoz rendelt opcionálisan engedélyezése.

## <a name="feedback"></a>Visszajelzés

Reméljük, például a továbbfejlesztett használata az Azure AD felhasználói élmény. Ne hamarosan visszajelzését! Visszajelzését és ötleteket javítására szolgáló utáni a **felügyeleti portál** szakasza a [visszajelzési fórumon](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Ritkán használt adatok új dolgai minden nap kiépítésével foglalkozó még érdeklődőbbek és az útmutató a shape felhasználása és határozza meg, mi készíteni.

## <a name="next-steps"></a>További lépések

További részletekért lásd: [alkalmazások kezelése az Azure Active Directoryval](active-directory-enable-sso-scenario.md).



