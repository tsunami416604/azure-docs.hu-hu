---
title: Vállalati alkalmazások kezelése az Azure Active Directory újdonságai |} A Microsoft Docs
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
ms.openlocfilehash: fefc508679a309262d07a582fc8f5bdf9f67cfe5
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310115"
---
# <a name="whats-new-in-enterprise-application-management-in-azure-active-directory"></a>Vállalati alkalmazások kezelése az Azure Active Directory újdonságai 

Az Azure Active Directory (Azure AD) továbbfejlesztett alkalmazás vállalati eszközök, az új szolgáltatásokat és képességeket, hogy az alkalmazások kezelését, egyszerűbb és hatékony.

Íme néhány a a fejlesztések az Azure AD-t a [az Azure portal](https://portal.azure.com).

- Egy alkalmazás nagyobb katalógus észlel, egyszerűsített alkalmazás létrehozási modell, és a használt minden alkalmazás esetében támogatja. 
- Egy vadonatúj gyors üzembe helyezést biztosít, amelyek segítségével az alkalmazás egy próba megkezdéséhez. 
- Mindössze néhány kattintással önkiszolgáló-szabályzatok konfigurálása. 
- Application proxy fejlesztései egyszeri bejelentkezés beállításainak, majd válassza a saját alkalmazások felhasználói, így több munka, mint a előtt beolvasása.

## <a name="improvements-to-the-azure-active-directory-application-gallery"></a>Az Azure Active Directory Alkalmazáskatalógusában fejlesztései

Adja hozzá a kedvenc alkalmazásai a vannak-e a [alkalmazáskatalógusában](manage-apps/what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery), egyéni alkalmazásokat, Ön kiterjesztése a felhőbe, vagy új alkalmazásokat fejleszt.  Megkezdheti az új felületet a kattintva **Hozzáadás** alatt **vállalati alkalmazások** vagy **minden alkalmazás**.
 
  ![Egy alkalmazás hozzáadása](./media/active-directory-enterprise-apps-whats-new-azure-portal/01.png)

Miután a katalógusban, látni fogja a kiemelt alkalmazások, amelyek támogatják a felhasználók átadásának elöl és középen jelenik meg. Megkeresheti az Önt érdeklő alkalmazások adatfelhasználás különböző kategóriák számos, vagy a keresési funkció használhatja az alkalmazásokat integrálni szeretné gyorsan megtalálásához.

  ![Az alkalmazáskatalógusában](./media/active-directory-enterprise-apps-whats-new-azure-portal/02.png)

## <a name="add-custom-applications-from-one-place"></a>Adja hozzá az egyéni alkalmazások egy helyen

Előre integrált alkalmazások hozzáadása a katalógusból, mind a egyéni alkalmazás-konfigurációs során lép fel, hogy is használta a klasszikus felügyeleti portálon kívül mostantól lehetséges az új portálon. E kiterjesztése az alkalmazás a helyszíni integrálása saját jelszó vagy összevont egyszeri bejelentkezés alkalmazás, az alkalmazásproxy használatával, vagy egy vadonatúj alkalmazást az application registry használatával hoz létre, érheti el azt az egyetlen helyről egyetlen.

  ![Saját alkalmazás hozzáadása](./media/active-directory-enterprise-apps-whats-new-azure-portal/03.png)

 
**Első lépések a saját alkalmazás hozzáadása**:

1. Kattintson a **saját hivatkozás hozzáadása** az alkalmazásgyűjteményben tetején. 
1. Látni fogja a két lehetőség elé,: **egy meglévő alkalmazás üzembe helyezése** vagy **egy új alkalmazást fejleszthet**. Látogasson el lapunkra további tudnivalók a különbség a két lehetőség és azok használatát.

### <a name="deploying-existing-applications"></a>Meglévő alkalmazások üzembe helyezése

1. Ha van már fut egy alkalmazásom, és csak azt szeretné integrálni Azure AD egyszeri bejelentkezés a, vagy válassza a kiépítést, a **egy meglévő alkalmazás üzembe helyezése** lehetőséget. Adjon meg egy nevet az alkalmazáshoz, kattintson a **Hozzáadás**.
1. Ennyi az egész! Tudni, hogy az alkalmazás meghozni részleteinek mobilszolgáltatásokon most állíthat be az új alkalmazás a bal oldali menüben, és az alkalmazás tetszőlegesen a konfigurálása bármikor működésével.

  ![Egy meglévő alkalmazást, egyetlen kattintással hozzáadása](./media/active-directory-enterprise-apps-whats-new-azure-portal/04.png)
 
### <a name="developing-new-applications"></a>Új alkalmazások fejlesztése

1. Egy új alkalmazást fejleszt, van-e egy egyszerű módszert kellhet, az alkalmazás beállításjegyzékében a katalógusból:
1. Kattintson a **adja hozzá a saját** az alkalmazáskatalógusból, válassza ki a beállítás **a meglévő alkalmazások fejlesztése**, és láthatja, hogy közvetlenül az alkalmazás hozzáadása tapasztalatok rövid hivatkozás.

  ![Néhány kattintással egy újonnan kifejlesztett alkalmazás hozzáadása](./media/active-directory-enterprise-apps-whats-new-azure-portal/05.png)


>[!NOTE]
>Miután hozzáadta az alkalmazás beállításjegyzékében használó alkalmazások, megjelenítése a vállalati alkalmazások hol is elérheti a hozzáférési szabályzatok az új alkalmazás egyszeri bejelentkezés konfigurálására és felügyeletére listáját láthatja.

  ![Az új alkalmazás a vállalati alkalmazásokhoz való hozzáférés kezelése](./media/active-directory-enterprise-apps-whats-new-azure-portal/06.png)


## <a name="quickstart-get-going-with-your-new-application-right-away"></a>Gyors útmutató: Mellett azonnal az új alkalmazással 

Miután hozzáadott egy alkalmazást, hogy kell-e előre integrált vagy az alkalmazásba, létrehoztuk egy testre szabott gyors üzembe helyezési ahhoz, hogy az új alkalmazások felületen gyorsan földelve felhasználói élményt. Ha rendszeresen hajtsa végre a különböző lehetőségek, azt fog végigvezetik a felhasználói felületen, és bemutatják, hogyan kezdheti egy próbaprogram az új alkalmazás a lehető leggyorsabb. 
 
  ![Az új alkalmazások gyors üzembe helyezést biztosít](./media/active-directory-enterprise-apps-whats-new-azure-portal/07.png)

 Keresse fel az új gyors üzembe helyezést biztosít, bármikor és bármilyen alkalmazás kattintva **rövid** az alkalmazás bal oldali navigációs menüjében.


## <a name="updated-application-proxy-configuration"></a>Frissített application proxy konfigurációja

Most tekintsük például a hozzáadott új alkalmazások közül egy a helyszíni környezetben fut, és azt szeretné, hogy integrálja az Azure ad-ben.  A ritkán használt adatok új dolog, az új alkalmazás konfigurációs tapasztalatait az új Azure ad-portál, hogy az alkalmazás bejelentkezési mód az application proxy konfigurációja halmazra mostantól egyszerűen közzéteheti jelszavas egyszeri Bejelentkezést vagy összevont alkalmazások hogy futnak a felhőbe, a vállalati hálózaton közvetlenül az alkalmazás több példányának létrehozása nélkül.

Beállíthatja az új alkalmazások hozzáadása az új portál, beleértve a natív Windows-hitelesítési folyamattal támogató alkalmazások az Azure AD-alkalmazásproxy jobb való használatra.

  ![Az alkalmazás bejelentkezési lehetőség integrált Windows-hitelesítés konfigurálása](./media/active-directory-enterprise-apps-whats-new-azure-portal/08.png)
 
Ismerkedés a Proxy konfigurálása a natív Windows-hitelesítés alkalmazás:
1. Kattintson az egyszeri bejelentkezés navigációs elemre, és válassza a **integrált Windows-hitelesítés** bejelentkezési beállításaiban, és adja meg a tetszőlegesen a beállításokat.
1. Felül ezek új hitelesítési módok támogatása, most is feltölthet egyéni tartományokat a biztonságos végpontok a szervezeten belül futó alkalmazásokat támogatja a tanúsítványok.  
 
   ![Az alkalmazásproxy használható tanúsítvány feltöltése](./media/active-directory-enterprise-apps-whats-new-azure-portal/09.png)

1. A kedvenc helyszíni alkalmazás egy új tanúsítvány feltöltéséhez kattintson a a **alkalmazásproxy** lehetőséget a bal oldali navigációs menüben, majd kattintson a **tanúsítvány** választó, és töltsön fel egy tanúsítványt a fájl titkosítása a felhőbeli végpont érkező kéréseket az alkalmazás használatával.

## <a name="advanced-federated-single-sign-on-configuration"></a>Speciális összevont egyszeri bejelentkezési konfiguráció

Azoknak, összevont alkalmazások használatát még ma nincsenek sok új funkciója a SAML-alapú bejelentkezés beállításainak. A következővel kell kezdődnie most már, is teljes mértékben testre szabhatja, hozzáadása, eltávolítása és a kiállított SAML-jogkivonatok jogcímekként meglévő felhasználói attribútumok leképezésére.
 
  ![Egy összevont alkalmazás átadva az SAML-jogkivonat felhasználói attribútumok testreszabása](./media/active-directory-enterprise-apps-whats-new-azure-portal/10.png)

Ellenőrizze, hogy ki az új összevont egyszeri bejelentkezés konfigurálása:
1. Nyisson meg egy összevont alkalmazás **egyszeri bejelentkezési** a bal oldali navigációs menüben, és ellenőrizze, hogy a "*SAML-alapú bejelentkezés** mód van kiválasztva. 
1. Egyszer, engedélyezze az alábbi jelölőnégyzetet alatt a **felhasználói attribútumok** , amelyek átadott fejlécre a SAML-jogkivonatban szereplő összes módosítása.

Akkor is is létrehozni, helyettesítő, és összevont egyszeri bejelentkezéshez használt tanúsítványok kezelése, valamint szerkesztése, ki kap értesítést, amikor a tanúsítvány érvényessége hamarosan lejár. Ezekkel az új beállításokkal alatt látni fogja a **tanúsítványok** fejléc ugyanezen egyszeri bejelentkezéses ablaktábláján.
 
  ![A lejárati értesítést e-mailben és a tanúsítvány-aláírási beállítások testreszabása, egy új tanúsítvány létrehozása](./media/active-directory-enterprise-apps-whats-new-azure-portal/11.png)

### <a name="relay-state-parameter"></a>Továbbítási Állapot paraméter
Végül azt korábban is kiterjesztett SAML URL-cím paraméterei közé tartozik a támogatott a **továbbítási Állapot paraméter**, azaz a felhasználók ekkor megnyílik a belül egy összevont alkalmazás a bejelentkezés befejezése után a lap. Ez hasznos beállítás konfigurálása, ha azt szeretné, a felhasználónak egy adott helyen gyors őket bevezetése az alkalmazáson belül.

  ![Az SAML-továbbítási Állapot-paraméter](./media/active-directory-enterprise-apps-whats-new-azure-portal/12.png)
 
**A relay-state paraméter beállítása**:

1. Engedélyezze a **speciális URL-beállítások megjelenítése** alatt jelölőnégyzet a **tartomány és URL-címek** konfigurációs panelen egyszeri bejelentkezéses fejlécét. 
1. Egy készlet új URL-cím beviteli mező jelenik meg, amelyek lehetővé teszik, hogy ezt a paramétert, és egyéb SAML URL-cím beállításainak megadása.

## <a name="bring-your-own-password-sso-applications"></a>A saját jelszavát SSO-alkalmazások használata

Tudjuk, hogy nem minden alkalmazás támogatja-e az összevonási lehetőségeiből közvetlenül. Például esetleg a hozzáadott új alkalmazások egyikét, egy egyéni bejelentkezési képernyő, felhasználók által használt felhasználónévvel és jelszóval bejelentkezni. Ilyen típusú alkalmazásokat az Azure AD-t továbbra is integrálva az **saját alkalmazások importálása** szolgáltatást, amely már elérhető az új portálon konfigurálható.
 
  ![Egyéni jelszó vaulting alkalmazások az Azure AD integrálása](./media/active-directory-enterprise-apps-whats-new-azure-portal/13.png)

**Tekintse meg a "Saját alkalmazások importálása" funkció**:

1. Miután beállította az egyszeri bejelentkezési mód egy új egyéni alkalmazáshoz, amely a hozzáadott **jelszóalapú bejelentkezés**, adja meg az URL-címet, ahol a az alkalmazás rendereli a bejelentkezési képernyőt, és 
1. Kattintson a **Save** (Mentés) gombra.  
1. Ha így tesz, azt fogja automatikusan scrape URL-CÍMRE a felhasználónevet, és jelszó beviteli mező, és lehetővé teszik, hogy az Azure AD a jelszavakat az alkalmazást, a hozzáférési panel böngészőbővítményének használatánál használatával küldheti.

## <a name="configure-self-service-application-access"></a>Önkiszolgáló alkalmazás-hozzáférés konfigurálása

Nagy mennyiségű új alkalmazások hozzáadása után esetleg szeretné lehetővé teszi a felhasználók tallózással keresse meg és adja hozzá ezek az alkalmazások saját hozzáférési paneljükön, anélkül, hogy többet jelzi, hogy rendszergazdaként. Most ez a legújabb kiadással konfigurálása, és az új portálon közvetlenül a önkiszolgáló alkalmazás-hozzáférés kezelése.

  ![Az alkalmazás egyszeri bejelentkezési jelszó önkiszolgáló alkalmazás-hozzáférés konfigurálása](./media/active-directory-enterprise-apps-whats-new-azure-portal/14.png)
 
**Az önkiszolgáló alkalmazás-hozzáférés konfigurálása és felügyelete**:

1. Első lépésként válassza a **önkiszolgáló** az alkalmazás lehetőséget a bal oldali navigációs menü, és állítsa be a **engedélyezése a felhasználók számára az alkalmazáshoz való hozzáférés kérése?** beállítást "**Igen**". 
1. Ez lehetővé teszi, hogy kik hagyhatják jóvá az alkalmazáshoz való hozzáférést, és hozzáadódik az önkiszolgáló felhasználók csoport konfigurálása. Emellett ha az alkalmazás a jelszavas egyszeri bejelentkezést a van konfigurálva, is megjelenik egy másik lehetőség, amellyel igény szerint a jóváhagyók ezeket a jelszavakat az alkalmazáshoz hozzárendelt kezeléséhez.

## <a name="feedback"></a>Visszajelzés

Reméljük, például a továbbfejlesztett használatával az Azure AD felhasználói élményt. Ne zárja be a visszajelzés hamarosan elérhető! A visszajelzések és javítására szolgáló ötleteket közzététele a **felügyeleti portálon** szakaszában az [Visszajelzési fórum](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Azt már nagyon Izgatottan várjuk, új cuccok minden nap létrehozásával kapcsolatos és az alakzat útmutatóval használja, és határozza meg, milyen készíteni.

## <a name="next-steps"></a>További lépések

További részletekért lásd: [alkalmazások kezelése az Azure Active Directoryval](manage-apps/what-is-application-management.md).



