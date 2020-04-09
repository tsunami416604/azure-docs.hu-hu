---
title: Alkalmazáshitelesítés áthelyezése az AD FS szolgáltatásból az Azure Active Directoryba
description: Ez a cikk célja, hogy segítsen a szervezeteknek megérteni, hogyan helyezheti át az alkalmazásokat az Azure AD-be, az összevont SaaS-alkalmazásokra összpontosítva.
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/01/2020
ms.author: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcb39606cdbb6488ccdee2828029d3617d689508
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891938"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Alkalmazáshitelesítés áthelyezése az Active Directory összevonási szolgáltatásokból az Azure Active Directoryba

[Az Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) egy univerzális identitásplatformot kínál, amely egyetlen identitást biztosít az emberek, partnerek és az ügyfelek számára az alkalmazások eléréséhez és az együttműködéshez bármely platformról és eszközről. Az Azure AD [az identitáskezelési képességek teljes csomagjával rendelkezik.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) Az alkalmazás (alkalmazás) hitelesítésének és az Azure AD-nek való engedélyezésének szabványosítása lehetővé teszi a képességek által nyújtott előnyöket. 

> [!NOTE]
> Ez a cikk az alkalmazáshitelesítés áthelyezése a helyszíni Active Directory és az Active Directory összevonási szolgáltatások az Azure AD.This article focuses to moving application authentication from on-premises Active Directory and Active Directory Federation Services to Azure AD. Az áthelyezés megtervezésének áttekintését tekintse meg az [alkalmazáshitelesítés Azure AD-be való áttelepítéséről](https://aka.ms/migrateapps/whitepaper) szóló tanulmányban. A tanulmány bemutatja, hogyan tervezd meg az áttelepítést, a tesztelést és az elemzéseket.

## <a name="introduction"></a>Introduction (Bevezetés)

Ha olyan helyszíni címtárral rendelkezik, amely felhasználói fiókokat tartalmaz, valószínűleg sok olyan alkalmazással rendelkezik, amelyekhez a felhasználók hitelesítik magukat. Ezen alkalmazások mindegyike úgy van beállítva, hogy a felhasználók identitásuk használatával férjenek hozzá. 


A felhasználók közvetlenül is hitelesíthetik magukat a helyszíni Active Directoryval. Az Active Directory összevonási szolgáltatások (AD FS) egy helyszíni identitáskezelési szolgáltatáson alapuló szabványok. Az AD FS kibővíti az egyszeri bejelentkezés (SSO) funkcióit a megbízható üzleti partnerek között anélkül, hogy a felhasználóknak külön kellene bejelentkezniük az egyes alkalmazásokba. Ezt Föderációnak nevezik.

Számos szervezet rendelkezik Szoftver szolgáltatásként (SaaS) vagy egyéni üzletági (LOB) alkalmazásokkal, amelyekközvetlenül az AD FS-hez vannak összeszervezve, az Office 365 és az Azure AD-alapú alkalmazások mellett. 

![Közvetlenül a helyszínen csatlakoztatott alkalmazások](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**Az alkalmazások biztonságának növelése érdekében a cél az, hogy egyetlen hozzáférés-vezérlési és szabályzatkészlettel rendelkezik a helyszíni és a felhőalapú környezetekben.** 

![Az Azure AD-n keresztül csatlakoztatott alkalmazások](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>Az áttelepítendő alkalmazások típusai

Az alkalmazás hitelesítésének áttelepítése az Azure AD-be optimális, mivel egyetlen vezérlősíkot biztosít az identitás- és hozzáférés-kezeléshez.

Az alkalmazások modern vagy régebbi protokollokat használhatnak a hitelesítéshez. Fontolja meg először a modern hitelesítési protokollokat (például SAML és Open ID Connect) használó alkalmazások áttelepítését. Ezek az alkalmazások újrakonfigurálhatók az Azure AD-vel való hitelesítéshez az Alkalmazásgalériában egy beépített összekötőn keresztül, vagy az alkalmazás Azure AD-ben történő regisztrálásával. A régebbi protokollokat használó alkalmazások integrálhatók [az Alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy)használatával. 

További információ: [Milyen típusú alkalmazások integrálhatók az Azure AD-vel?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-management)

Az [AD FS-alkalmazástevékenység-jelentés segítségével áttelepítheti az alkalmazásokat az Azure AD-be,](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) ha engedélyezve van az [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs). 

### <a name="the-migration-process"></a>Az áttelepítési folyamat

Az alkalmazáshitelesítés Azure AD-be való áthelyezésének folyamata során megfelelően tesztelheti az alkalmazásokat és a konfigurációt. Azt javasoljuk, hogy továbbra is használja a meglévő tesztkörnyezetek áttelepítési tesztelés áthelyezése az éles környezetbe. Ha egy tesztkörnyezet jelenleg nem érhető el, az alkalmazás architektúrájának függően beállíthat egyet az [Azure App Service](https://azure.microsoft.com/services/app-service/) vagy az Azure virtuális [gépek](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)használatával.

Dönthet úgy, hogy egy külön teszt et azure AD-bérlő az alkalmazás konfigurációk fejlesztése során használni. 

Az áttelepítési folyamat a következőképpen nézhet ki:

**1. szakasz – Jelenlegi állapot: Az AD FS-sel hitelesítő éles alkalmazás**

![Áttelepítési szakasz 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

 
**2. szakasz – NEM KÖTELEZŐ: Az Azure-bérlő tesztelésére mutató alkalmazás tesztpéldányának tesztelése**

Frissítse a konfigurációt, hogy az alkalmazás tesztpéldányát egy teszt Azure AD-bérlőre irányítsa, és hajtsa végre a szükséges módosításokat. Az alkalmazás tesztelhető a felhasználók a teszt Az Azure AD-bérlő. A fejlesztési folyamat során olyan eszközökkel, mint a [Fiddler,](https://www.telerik.com/fiddler) összehasonlíthatja és ellenőrizheti a kéréseket és a válaszokat.

Ha egy külön tesztbérlő beállítása nem valósítható meg, hagyja ki ezt a szakaszt, és álljon fel egy alkalmazás tesztpéldányára, és irányítsa az éles Azure AD-bérlőre az alábbi 3.

![2. migrációs szakasz ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**3. szakasz – Az éles Azure-bérlőre mutató tesztalkalmazás**

Frissítse a konfigurációt, hogy az alkalmazás tesztpéldányát az Azure éles példányára irányítsa. Most már tesztelheti a felhasználók kal a termelési példányban. Ha szükséges, tekintse át a jelen cikk átalakuló felhasználókról szóló szakaszát.

![Migrációs szakasz ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**4. szakasz – Éles aD-bérlőre mutató éles alkalmazás**

Frissítse az éles alkalmazás konfigurációját, hogy az éles Azure-bérlőre mutasson.

![Áttelepítési szakasz 1 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 Az AD FS szolgáltatással hitelesítő alkalmazások az Active Directory-csoportokat használhatják az engedélyekhez. Az [Azure AD Connect szinkronizálása](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) segítségével szinkronizálhatja az identitásadatokat a helyszíni környezet és az Azure AD között az áttelepítés megkezdése előtt. Ellenőrizze ezeket a csoportokat és a tagságot az áttelepítés előtt, hogy az alkalmazás áttelepítésekekénél hozzáférést biztosítson ugyanazoknak a felhasználóknak.

### <a name="line-of-business-lob-apps"></a>Üzletági (LOB) alkalmazások

A LOB-alkalmazásokat a szervezet belsőleg fejleszti, vagy az adatközpontban telepített szabványos csomagolt termékként érhető el. Ilyenek például a Windows Identity Foundation és a SharePoint-alkalmazásokra épülő alkalmazások (nem a SharePoint Online). 

Az OAuth 2.0, az OpenID Connect vagy a WS-Federation szolgáltatást használó lob-alkalmazások integrálhatók az Azure AD-vel [alkalmazásregisztrációként.](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users) Integrálja azokat az egyéni alkalmazásokat, amelyek az SAML 2.0 vagy a WS-Federation szolgáltatást [nem galériaalkalmazásokként](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) használják az [Azure Portal](https://portal.azure.com/)vállalati alkalmazáslapján.

## <a name="saml-based-single-sign-on"></a>SAML-alapú egyszeri bejelentkezés

A hitelesítéshez SAML 2.0-s hitelesítést használó alkalmazások konfigurálhatók [saml-alapú egyszeri bejelentkezéshez](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (SAML-alapú egyszeri bejelentkezés). Az [SAML-alapú egyszeri bejelentkezéssel](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)a felhasználókat az SAML-jogcímekben megadott szabályok alapján adott alkalmazásszerepkörökhöz képezheti. 

Saml-alapú egyszeri bejelentkezéss saas-alkalmazás konfigurálásához olvassa [el az SAML-alapú egyszeri bejelentkezés konfigurálása című témakört.](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications) 

![SSO SAML felhasználói képernyőképek ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

 
Számos SaaS-alkalmazás rendelkezik egy [alkalmazásspecifikus oktatóanyaggal,](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) amely végiglépaz SAML-alapú egyszeri bejelentkezés konfigurációján.

![alkalmazás oktatóanyaga](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Egyes alkalmazások migrálása egyszerű. Az összetettebb követelményekkel rendelkező, például egyedi jogcímeket igénylő alkalmazások esetében azonban további konfigurálásra lehet szükség az Azure AD-ben és/vagy az Azure AD Connectben. A támogatott jogcím-hozzárendelésekről a [Jogcímleképezés az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)című témakörben talál további információt.

Ne feledje, hogy az attribútumok leképezésekénél a következő korlátozások vannak:

* Az AD FS-ben kiadható attribútumok közül nem mindegyik jelenik meg az Azure AD-ben az SAML-jogkivonatok számára kibocsátott attribútumként, még akkor is, ha ezek az attribútumok szinkronizálva vannak. Az attribútum szerkesztésekor az Érték legördülő lista megmutatja az Azure AD-ben elérhető különböző attribútumokat. Ellenőrizze [az Azure AD Connect szinkronizálási](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) konfigurációját, és győződjön meg arról, hogy egy szükséges attribútum – például samAccountName – szinkronizálás alatt áll az Azure AD-vel. A bővítményattribútumok segítségével bármely jogcím, amely nem része az Azure AD általános jogszámú felhasználói séma.

* A leggyakoribb forgatókönyvekben csak a NameID jogcím és egyéb általános felhasználóazonosító jogcímek szükségesek az alkalmazáshoz. Annak megállapításához, hogy szükség van-e további jogcímekre, vizsgálja meg, hogy milyen jogcímeket állít ki az AD FS-től.

* Nem minden jogcím lehet probléma, mivel egyes jogcímek védettek az Azure AD-ben. 

* A titkosított SAML-tokenek használatának lehetősége már előzetes verzióban érhető el. Lásd: [Az SAML-jogkivonatban kiadott jogcímek testreszabása vállalati alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

 

### <a name="software-as-a-service-saas-apps"></a>Szoftver szolgáltatásként (SaaS) alkalmazások

Ha a felhasználó bejelentkezik a SaaS-alkalmazásokba, például a Salesforce, a ServiceNow vagy a Workday, és integrálva van az AD FS-sel, akkor összevont bejelentkezést használ az SaaS-alkalmazásokhoz. 

A legtöbb SaaS-alkalmazás már konfigurálható az Azure AD-ben. A Microsoft számos előre konfigurált kapcsolattal rendelkezik az [Azure AD-alkalmazások gyűjteményében](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)található SaaS-alkalmazásokkal, amelyek megkönnyítik az átállást. Az SAML 2.0-s alkalmazások integrálhatók az Azure AD-vel az Azure AD alkalmazásgalérián vagy [nem galériaalkalmazásokon](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app)keresztül. 

Az OAuth 2.0 vagy OpenID Connect hitelesítést használó alkalmazások hasonló módon, [alkalmazásregisztrációként](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users) integrálhatók az Azure AD-be. Az örökölt protokollokat használó alkalmazások az [Azure AD-alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) használatával hitelesíthetik magukat az Azure AD-vel.

A SaaS-alkalmazások bevezetésével kapcsolatos problémákesetén forduljon a [SaaS-alkalmazásintegrációtámogatási aliashoz.](mailto:SaaSApplicationIntegrations@service.microsoft.com)

**SAML-aláíró tanúsítványok az SSO-hoz:** Az aláíró tanúsítványok fontos részét képezik az SSO telepítésének. Az Azure AD létrehozza az aláíró tanúsítványokat saml-alapú összevont egyszeri bejelentkezés létrehozásához a SaaS-alkalmazásokhoz. Miután hozzáadta a katalógust vagy a nem katalógusalkalmazásokat, konfigurálni fogja a hozzáadott alkalmazást az összevont egyszeri egyszeri megt, és konfigurálni fogja. Lásd: [Tanúsítványok kezelése az azure-beli egyszeri bejelentkezéshez.](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on)

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Alkalmazások és konfigurációk, amelyek ma áthelyezhetők

A ma könnyen mozgatható alkalmazások olyan SAML 2.0-alkalmazásokat tartalmaznak, amelyek a konfigurációs elemek és jogcímek szabványos készletét használják:

* Felhasználó egyszerű neve

* E-mail-cím

* Utónév

* vezetéknév;

* Az SAML **NameID**-ként használt alternatív attribútum, például az Azure AD Mail attribútum, a Mail előtag, az alkalmazott azonosítója, az 1-15 bővítményattribútumok vagy a helyszíni **SamAccountName**. További információkat itt talál: A [NameIdentifier](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization) jogcím szerkesztése.

* Egyéni jogcímek.

Az alábbi további konfigurációs lépéseket igényel az Azure AD-re való áttelepítéshez:

* Egyéni engedélyezési vagy többtényezős hitelesítési (MFA) szabályok az AD FS-ben. Konfigurálja őket az [Azure AD feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) szolgáltatás használatával.

* Több Válasz URL-végponttal rendelkező alkalmazások. Konfigurálja őket az Azure AD-ben a PowerShell vagy az Azure Portal felületen.

* A WS-Federationt használó alkalmazások, például a SharePoint-alkalmazások, amelyek SAML 1.1-es verziójú jogkivonatot igényelnek. Manuálisan konfigurálhatja őket a PowerShell használatával. A katalógusból előre integrált általános sablont is hozzáadhat a SharePoint- és SAML 1.1-es alkalmazásokhoz. Támogatjuk az SAML 2.0 protokollt.

* Az összetett jogcímek kiállítása átalakítja a szabályokat. A támogatott jogcím-hozzárendelésekről a következő témakörökben talál tájékoztatást:
   *  [Jogcímtársítások az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping) 
   * [Az Azure Active Directoryban nagyvállalati alkalmazások SAML-jogkivonatában kiadott jogcímek testreszabása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

 

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Az Azure AD-ben jelenleg nem támogatott alkalmazások és konfigurációk

A következő képességeket igénylő alkalmazások ma nem telepíthetők át.

**Protokoll-képességek**

* A WS-Trust ActAs minta támogatása

* Az SAML-összetevő felbontása

* Aláírt SAML-kérelmek aláírása  
Vegye figyelembe, hogy az aláírt kérelmeket a rendszer elfogadja, de az aláírást nem ellenőrzi.  
Mivel az Azure AD csak az alkalmazásban előre konfigurált végpontok jogkivonatát adja vissza, a legtöbb esetben valószínűleg nincs szükség aláírás-ellenőrzésre.

**Jogcímek jogkivonat-képességekben**

* Az Azure AD-címtártól eltérő attribútumtárolókból származó jogcímek, kivéve, ha az adatok szinkronizálva vannak az Azure AD-vel. További információt az [Azure AD szinkronizálási API áttekintése című témakörben talál.](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)

* Címtár többértékű attribútumainak kiadása. Például jelenleg nem adhatunk ki többértékű jogcímet a proxycímekre vonatkozóan.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Alkalmazásbeállítások leképezése az AD FS-ről az Azure AD-re

A migrálás első lépéseként mérje fel az alkalmazás helyszíni konfigurációját, majd képezze le az Azure AD-be. Az AD FS és az Azure AD hasonlóan működik, így a megbízhatósági, a bejelentkezési és kijelentkezési URL-címek és az azonosítók konfigurálásának fogalma mindkét esetben érvényes. Dokumentálja az alkalmazások AD FS konfigurációs beállításait, hogy könnyen konfigurálhassa őket az Azure AD-ben.

### <a name="map-app-configuration-settings"></a>Alkalmazáskonfigurációs beállítások leképezése

Az alábbi táblázat az AD FS-függő entitás megbízhatósága és az Azure AD Enterprise Alkalmazás beállításainak leggyakoribb leképezését ismerteti:

* AD FS – Keresse meg a beállítást az Alkalmazás AD FS függő entitás megbízhatósági kapcsolatában. Kattintson a jobb gombbal a függő entitásra, és válassza a Tulajdonságok parancsot. 

* Azure AD– A beállítás az [Azure Portalon](https://portal.azure.com/) belül konfigurálva van az egyes alkalmazások egyszeri bejelentkezési tulajdonságaiban.

| Konfigurációs beállítás| AD FS| Konfigurálás az Azure AD-ben| SAML-token |
| - | - | - | - |
| **Alkalmazás bejelentkezési URL-címe** <p>A felhasználó által kezdeményezett SAML-folyamatban az alkalmazásba való bejelentkezéshez megadott URL-cím.| N/A| Egyszerű SAML-konfiguráció megnyitása SAML alapú bejelentkezésből| N/A |
| **Alkalmazás válasz URL-címe** <p>Az alkalmazás URL-címe az identitásszolgáltató (IdP) szemszögéből. Az IdP elküldi a felhasználót és a jogkivonatot, miután a felhasználó bejelentkezett az IdP-be.  Ezt **SAML-helyességi feltétel fogyasztói végpontnak is nevezik.**| A **Végpontok** lap kijelölése| Egyszerű SAML-konfiguráció megnyitása SAML alapú bejelentkezésből| Az SAML-jogkivonat céleleme. Példa érték:[https://contoso.my.salesforce.com](https://contoso.my.salesforce.com/) |
| **Alkalmazás kijelentkezési URL-címe** <p>Ez az az URL-cím, amelyre a "kijelentkezési törlési" kérelmeket küldi el a rendszer, amikor egy felhasználó kijelentkezik egy alkalmazásból. Az IdP elküldi a kérelmet, hogy jelentkezzen ki a felhasználó az összes többi alkalmazás is.| A **Végpontok** lap kijelölése| Egyszerű SAML-konfiguráció megnyitása SAML alapú bejelentkezésből| N/A |
| **Alkalmazásazonosító** <p>Ez az idp szempontjából az alkalmazás azonosítója. A bejelentkezési URL-értéket gyakran használják az azonosítóhoz (de nem mindig).  Néha az alkalmazás ezt az "entitásazonosítónak" nevezi.| Az **Azonosítók** lap kijelölése|Egyszerű SAML-konfiguráció megnyitása SAML alapú bejelentkezésből| Leképezi az SAML token **Közönség** elemére. |
| **Alkalmazás összevonási metaadatai** <p>Ez az alkalmazás összevonási metaadatainak helye. Az identitásszolgáltató használja egyes konfigurációs beállítások, például a végpontok vagy a titkosítási tanúsítványok automatikus frissítéséhez.| A **Figyelés** lap kijelölése| N/A. Az Azure AD nem támogatja az alkalmazásösszevonási metaadatok közvetlen fogyasztását. Az összevonási metaadatokat manuálisan is importálhatja.| N/A |
| **Felhasználói azonosító/ névazonosító** <p>A felhasználó identitását az Azure AD-ből vagy az AD FS-ből az alkalmazás felé egyértelműen azonosító attribútum.  Ez az attribútum általában vagy az upn vagy a felhasználó e-mail címe.| Igénylési szabályok. A legtöbb esetben a jogcímszabály olyan jogcímet ad ki, amely a NameIdentifier végződésű.| Az azonosítót a **Felhasználói attribútumok és jogcímek**fejléc alatt találja. Alapértelmezés szerint az upn| Leképezi az SAML token **NameID** eleméhez. |
| **Egyéb követelések** <p>Az IdP által az alkalmazásnak általában küldött egyéb jogcímadatok közé tartozik a utónév, a vezetéknév, az e-mail-cím és a csoporttagság.| Az AD FS-ben ez a függő entitásra vonatkozó egyéb jogcímszabályokként található meg.| Az azonosítót a **Felhasználói attribútumok & jogcímek**fejlécalatt találja. Válassza ki az **Egyéb felhasználói attribútumok megtekintése** és szerkesztése elemet.| N/A |


### <a name="map-identity-provider-idp-settings"></a>Az identitásszolgáltató (IdP) beállításai

Konfigurálja úgy az alkalmazásokat, hogy az Azure AD és az AD FS sso pontra mutassanak. Itt az SAML protokollt használó SaaS-alkalmazásokra összpontosítunk. Ez a koncepció azonban az egyéni LOB-alkalmazásokra is kiterjed.

> [!NOTE]
> Az Azure AD konfigurációs értékei azt a mintát követik, amelyben az Azure-bérlői azonosító lecseréli a(z) {tenant-id} azonosítót, és az alkalmazásazonosító a(z) {application-id} helyébe lép. Ezeket az információkat az [Azure Active](https://portal.azure.com/) Directory > tulajdonságok csoportban találja az Azure Active Directory > tulajdonságai csoportban: 

* Válassza a címtárazonosítót a bérlői azonosító megtekintéséhez. 

* Válassza az alkalmazásazonosító t az alkalmazásazonosító megtekintéséhez.

 Magas szinten rendelje hozzá a következő fontos SaaS-alkalmazások konfigurációs elemeit az Azure AD-hez. 

 

| Elem| Konfigurációs érték |
| - | - |
| Identitásszolgáltató kibocsátója| [https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| Identitásszolgáltató bejelentkezési URL-címe| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Identitásszolgáltató kijelentkezési URL-címe| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Összevonási metaadatok helye| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |


### <a name="map-sso-settings-for-saas-apps"></a>SSO-beállítások leképezése SaaS-alkalmazásokhoz

SaaS-alkalmazások tudnia kell, hogy hová küldje a hitelesítési kérelmeket, és hogyan érvényesítheti a fogadott jogkivonatokat. Az alábbi táblázat ismerteti az alkalmazás SSO-beállításainak konfigurálásának elemeit, valamint azok értékeit vagy helyét az AD FS-en és az Azure AD-n belül

| Konfigurációs beállítás| AD FS| Konfigurálás az Azure AD-ben |
| - | - | - |
| **IdP bejelentkezési URL-címe** <p>Az IdP bejelentkezési URL-címe az alkalmazás szemszögéből (ahol a felhasználó torkig van irányítva a bejelentkezéshez).| Az AD FS bejelentkezési URL-címe az AD FS összevonási szolgáltatás neve, amelyet a "/adfs/ls/" követ. <p>Például:[https://fs.contoso.com/adfs/ls/](https://fs.contoso.com/adfs/ls/)| Cserélje le a(z) {tenant-id} azonosítóját a bérlői azonosítóra. <p> Saml-P protokollt használó alkalmazások esetén:[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>A WS-Federation protokollt használó alkalmazások esetén:[https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **IdP kijelentkezési URL-címe**<p>Az IdP kijelentkezési URL-címe az alkalmazás szemszögéből (ahol a felhasználó átirányításra kerül, amikor úgy dönt, hogy kijelentkezik az alkalmazásból).| A kijelentkezési URL-cím vagy megegyezik a bejelentkezési URL-címmel, vagy ugyanaz ta- Például:[https://fs.contoso.com/adfs/ls/?wa=wsignout1.0](https://fs.contoso.com/adfs/ls/?wa=wsignout1.0)| Cserélje le a(z) {tenant-id} azonosítóját a bérlői azonosítóra.<p>Saml-P protokollt használó alkalmazások esetén:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> A WS-Federation protokollt használó alkalmazások esetén:[https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Jogkivonat-aláíró tanúsítvány**<p>Az IdP a tanúsítvány személyes kulcsát használja a kiállított jogkivonatok aláírásához. Igazolja, hogy a jogkivonat attól az identitásszolgáltatótól származik, amellyel az alkalmazás megbízhatósági kapcsolata konfigurálva van.| Az AD FS jogkivonat-aláíró tanúsítványa az AD FS-kezelőben a **Tanúsítványok** területen található.| Keresse meg az Azure Portalon az alkalmazás **egyszeri bejelentkezési tulajdonságai** alatt a fejléc **SAML aláíró tanúsítvány.** Innen letöltheti a tanúsítványt, hogy feltöltse az alkalmazásba.  <p>Ha az alkalmazás egynél több tanúsítvánnyal rendelkezik, az összes tanúsítvány megtalálható az összevonási metaadat-XML-fájlban. |
| **Azonosító/ "kibocsátó"**<p>Az IdP azonosítója az alkalmazás szemszögéből (néha "kibocsátóazonosító").<p>Az SAML-tokenben az érték a Kiállító elemként jelenik meg.| Az AD FS azonosítója általában az AD FS Management összevonási szolgáltatásazonosítója **a Szolgáltatás > az összevonási szolgáltatás tulajdonságainak szerkesztése területen.** Például:[http://fs.contoso.com/adfs/services/trust](http://fs.contoso.com/adfs/services/trust)| Cserélje le a(z) {tenant-id} azonosítóját a bérlői azonosítóra.<p>[https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| **IdP összevonási metaadatok**<p>Az IdP nyilvánosan elérhető összevonási metaadatainak helye. (Az összevonási metaadatokat egyes alkalmazások alternatív megoldásként használják, hogy a rendszergazdának ne kelljen egyenként konfigurálnia az URL-címeket, azonosítókat és jogkivonat-aláíró tanúsítványokat.)| Keresse meg az AD FS összevonás metaadat-CÍMÉT az AD FS-kezelés **szolgáltatás> végpontok > metaadatok > Típus: Összevonási metaadatok**csoportban. Például:[https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml](https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml)| Az Azure AD megfelelő értéke [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml)a mintát követi. Cserélje le a(z) {TenantDomainName} nevet a bérlő nevére "contoso.onmicrosoft.com" formátumban.   <p>További információkat itt talál: [Összevonási metaadatok](https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-federation-metadata). |


## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>AD FS biztonsági szabályzatok az Azure AD-ben

Amikor áthelyezi az alkalmazáshitelesítést az Azure AD-be, hozzon létre leképezéseket a meglévő biztonsági szabályzatokból az Azure AD-ben elérhető egyenértékű vagy alternatív változatokba. Annak biztosítása, hogy ezek a leképezések elvégezhetők legyenek az alkalmazástulajdonosok által megkövetelt biztonsági szabványok teljesítése mellett, jelentősen megkönnyíti az alkalmazás áttelepítésének többi részét.

Minden szabálytípus és példák, azt javasoljuk, hogy itt a szabály hogyan néz ki az AD FS, az AD FS szabály nyelvi egyenértékű kódot, és hogyan ez a térkép az Azure AD-ben.

### <a name="map-authorization-rules"></a>Engedélyezési szabályok leképezése

Az alábbiakban példákat mutatunk be az AD FS-ben található engedélyezési szabályok típusaira, valamint arra, hogyan képezheti le őket az Azure AD-hez:

#### <a name="example-1-permit-access-to-all-users"></a>1. példa: Hozzáférés engedélyezése minden felhasználó számára

Hozzáférés engedélyezése minden felhasználószámára így néz ki az AD FS-ben: 

![Áttelepítési szakasz 1 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


Ez az alábbi módokon képezi le az Azure AD-t:

Az [Azure portalon:](https://portal.azure.com/)
* 1. lehetőség: Állítsa a szükséges felhasználói hozzárendelést Nem-re ![hozzáférés-vezérlési házirend szerkesztése SaaS-alkalmazásokhoz ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    Vegye figyelembe, hogy a Felhasználói hozzárendelés szükséges kapcsoló igen beállításához a felhasználóknak hozzá kell rendelniük az alkalmazáshoz a hozzáféréshez. Ha a beállítás Nem, minden felhasználó nak hozzáférése van. Ez a kapcsoló nem szabályozza, hogy mit jelenít meg a felhasználók számára a Saját alkalmazások élményben. 

 
* 2. lehetőség: A Felhasználók és csoportok lapon rendelje hozzá az alkalmazást a "Minden felhasználó" automatikus csoporthoz. <p>
Engedélyeznie kell [a dinamikus csoportok](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) az Azure AD-bérlőaz alapértelmezett "Minden felhasználó" csoport elérhetővé kell tenni.

   ![Saját SaaS-alkalmazások az Azure AD-ben ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>2. példa: Csoport explicit engedélyezése

Explicit csoportengedélyezés az AD FS-ben:


![kiállításengedélyezési szabályok ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


A szabály így kerül az Azure AD-re:

Az [Azure Portalon](https://portal.azure.com/)először létre kell [hoznia egy felhasználói csoportot,](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) amely megfelel az AD FS felhasználói csoportjának, majd alkalmazásengedélyeket rendel a csoporthoz:

![Hozzárendelés hozzáadása ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>3. példa: Adott felhasználó engedélyezése

Explicit felhasználói engedélyezés az AD FS-ben:

![kiállításengedélyezési szabályok ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

A szabály így kerül az Azure AD-re:

Az [Azure Portalon](https://portal.azure.com/)az alkalmazás Hozzárendelés hozzáadása lapján az alkalmazás hoz egy felhasználót az alábbi módon:

![Saját SaaS-alkalmazások az Azure-ban ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

 
### <a name="map-multi-factor-authentication-rules"></a>Többtényezős hitelesítési szabályok leképezése 

A [többtényezős hitelesítés (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication) és az AD FS helyszíni telepítése az áttelepítés után is működni fog, mivel az AD FS-sel van összeegyezve. Fontolja meg azonban az Azure beépített MFA-képességeire való áttérést, amelyek az Azure AD feltételes hozzáférésű munkafolyamataihoz vannak kötve. 

Az alábbiakban példákat mutatunk be az AD FS-ben található MFA-szabályok típusaira, valamint arra, hogyan képezheti le őket az Azure AD-hez különböző feltételek alapján:

MFA-szabályok beállításai az AD FS-ben:

![Az Azure AD MFA-beállításai](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>1. példa: Többkori fa érvényesítése felhasználók/csoportok alapján

A Felhasználó/csoport választó egy olyan szabály, amely lehetővé teszi az MFA kényszerítése csoportonként (csoport-SID) vagy felhasználónkénti (elsődleges SID) alapon. A Felhasználó/csoportok hozzárendeléseken kívül az AD FS MFA konfigurációs felhasználói felületének minden további jelölőnégyzete további szabályként működik, amelyeket a rendszer a Felhasználó/csoportok szabály érvényesítése után értékel ki. 


Adja meg az MFA-szabályokat egy felhasználóhoz vagy csoporthoz az Azure AD-ben:

1. Hozzon létre egy [új feltételes hozzáférési szabályzatot](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

2. Válassza **a Hozzárendelések**lehetőséget. Adja hozzá azokat a felhasználókat vagy csoportokat, amelyeken az MFA-t érvényesíteni szeretné.

3. Az **Access-vezérlők** beállításainak konfigurálása az alábbi módon:  
‎

![AAD MFA-beállítások](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)

 
 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>2. példa: Többkori fa érvényesítésénem regisztrált eszközökre

Adja meg a nem regisztrált eszközök MFA-szabályait az Azure AD-ben:

1. Hozzon létre egy [új feltételes hozzáférési szabályzatot](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

2. Állítsa a **Hozzárendelések** beállítását **minden felhasználóra.**

3. Az **Access-vezérlők** beállításainak konfigurálása az alábbi módon:  
‎

![AAD MFA-beállítások](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

 
Ha a Több vezérlőhöz beállítást úgy állítja be, hogy a kijelölt vezérlők egyikének megkövetelése legyen, az azt jelenti, hogy ha a jelölőnégyzetben megadott feltételek bármelyike teljesül a felhasználó által, akkor hozzáférést kap az alkalmazáshoz.

#### <a name="example-3-enforce-mfa-based-on-location"></a>3. példa: Az MFA érvényesítése a hely alapján

Adja meg az MFA-szabályokat a felhasználó helye alapján az Azure AD-ben:

1. Hozzon létre egy [új feltételes hozzáférési szabályzatot](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

1. Állítsa a **Hozzárendelések** beállítását **minden felhasználóra.**

1. [Konfigurálja a névvel ellátott helyeket az Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) egyébként összevonása a vállalati hálózaton belül megbízható. 

1. Konfigurálja a **Feltételek szabályokat,** hogy adja meg azokat a helyeket, amelyekre az MFA érvényesítéséhez.

![Az Azure AD MFA-beállításai](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. Az **Access-vezérlők** beállításainak konfigurálása az alábbi módon:


![Hozzáférés-vezérlési házirendek leképezése](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

 
### <a name="map-emit-attributes-as-claims-rule"></a>Emit attribútumok leképezése jogcímszabályként

Íme egy példa arra, hogyan vannak leképezve attribútumok az AD FS-ben:

![Az Azure AD MFA-beállításai](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


A szabály így kerül az Azure AD-re:

Az [Azure Portalon](https://portal.azure.com/)válassza az **Enterprise Applications**lehetőséget, **válassza az egyszeri bejelentkezés**lehetőséget, és adja hozzá az **SAML token attribútumokat** az alábbiak szerint:

![Az Azure AD MFA-beállításai](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>Beépített hozzáférés-vezérlési házirendek leképezése

Az AD FS 2016 számos beépített hozzáférés-vezérlési szabályzatközül választhat:

![Beépített Azure AD hozzáférés-vezérlés](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)

 
Az Azure AD beépített szabályzatok megvalósításához használhat egy [új feltételes hozzáférési szabályzatot,](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json) és konfigurálhatja a hozzáférés-vezérléseket, vagy használhatja az AD FS 2016 egyéni szabályzattervezőjét a hozzáférés-vezérlési házirendek konfigurálásához. A szabályszerkesztő nek van egy kimerítő listája az engedélyről és a lehetőségek kivételével, amelyek segíthetnek mindenféle permutációt készíteni.

![Azure AD hozzáférés-vezérlési szabályzatok](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



Ebben a táblázatban felsoroltunk néhány hasznos engedély és kivéve lehetőségeket, és hogyan vannak leképezve az Azure AD-re. 


| | Hogyan konfigurálható az Engedélyezés lehetőség az Azure AD-ben?| Hogyan lehet beállítani, kivéve lehetőség az Azure AD? |
| - | - | - |
| Adott hálózatról| Leképezi a [megnevezett hely az](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) Azure AD-ben| A **Kizárás** lehetőség [használata megbízható helyekhez](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) |
| Meghatározott csoportokból| [Felhasználói/csoporthozzárendelés beállítása](https://docs.microsoft.com/azure/active-directory/manage-apps/assign-user-or-group-access-portal)| A **Kizárás** lehetőség használata a Felhasználók és csoportok csoportban |
| Adott megbízhatósági szinttel rendelkező eszközökről| Állítsa be ezt az "Eszközállapot" vezérlőből a Hozzárendelések -> feltételek csoportban| A **Kizárás** lehetőség használata az Eszközállapot és az **Összes eszköz belefoglalása csoportban** |
| Konkrét követelésekkel a kérelemben| Ez a beállítás nem telepíthető át| Ez a beállítás nem telepíthető át |


Példa a megbízható helyek kizárása beállításkonfigurálására az Azure Portalon:

![Képernyőkép a leképezési hozzáférés-vezérlési házirendekről](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Felhasználók áttérése az AD FS-ről az Azure AD-re

### <a name="sync-ad-fs-groups-in-azure-ad"></a>AD FS-csoportok szinkronizálása az Azure AD-ben

Az engedélyezési szabályok leképezésekénél az AD FS-sel hitelesítő alkalmazások az Active Directory-csoportokat használhatják az engedélyekhez. Ebben az esetben az [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) használatával szinkronizálja ezeket a csoportokat az Azure AD-vel az alkalmazások áttelepítése előtt. Győződjön meg arról, hogy az áttelepítés előtt ellenőrzi ezeket a csoportokat és a tagságot, hogy az alkalmazás áttelepítésekekénél hozzáférést biztosítson ugyanazoknak a felhasználóknak.

További információ: [Az Active Directoryból szinkronizált csoportattribútumok használatának előfeltételei című témakörben talál.](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims)

### <a name="setup-user-self-provisioning"></a>Felhasználó önkiépítési beállításai 

Egyes SaaS-alkalmazások támogatják a felhasználók önkiszolgáló kiépítését, amikor először jelentkeznek be az alkalmazásba. Az Azure Active Directoryban (Azure AD) az alkalmazáskiépítés kifejezés a felhasználói identitások és szerepkörök automatikus létrehozására utal a felhőben ([SaaS)](https://azure.microsoft.com/overview/what-is-saas/)alkalmazások, amelyekhez a felhasználóknak hozzáférésre van szükségük. Az áttelepített felhasználók már rendelkeznek egy fiókkal a SaaS-alkalmazásban. Az áttelepítés után hozzáadott új felhasználókat ki kell építeni. Tesztelje [az SaaS-alkalmazások kiépítését](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) az alkalmazás áttelepítése után.

### <a name="sync-external-users-in-azure-ad"></a>Külső felhasználók szinkronizálása az Azure AD-ben

A meglévő külső felhasználók kétféleképpen állíthatók be az AD FS-en belül:

#### <a name="external-users-with-a-local-account-within-your-organization"></a>Külső felhasználók helyi fiókkal a szervezeten belül

Ezeket a fiókokat továbbra is ugyanúgy használhatja, mint a belső felhasználói fiókokat. Ezek a külső felhasználói fiókok elvi névvel rendelkeznek a szervezeten belül, bár a fiók e-mail címe külsőleg is rámutathat. Az áttelepítés előrehaladásával kihasználhatja az [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) által nyújtott előnyöket, ha ezeket a felhasználókat áttelepíti saját vállalati identitásuk használatára, ha ilyen identitás érhető el. Ez leegyszerűsíti a bejelentkezési folyamatot a felhasználók számára, mivel gyakran saját vállalati bejelentkezéssel jelentkeznek be. A szervezet felügyelete is enyhülni fog azáltal, hogy a továbbiakban nem kell külső felhasználók fiókjait kezelnie.

#### <a name="federated-external-identities"></a>Összevont külső identitások

Ha jelenleg egy külső szervezettel van összeegyezve, néhány módszert kell figyelembe vennie:

* [Adja hozzá az Azure Active Directory B2B együttműködési felhasználóit az Azure Portalon.](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator) Proaktív módon elküldheti a B2B együttműködési meghívókat az Azure AD felügyeleti portálról a partnerszervezetnek, hogy az egyes tagok továbbra is használhatják a hozzájuk használt alkalmazásokat és eszközöket. 

* [Hozzon létre egy önkiszolgáló B2B regisztrációs munkafolyamatot,](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal) amely a B2B meghívási API használatával kérelmet generál a partnerszervezet egyéni felhasználói számára.

Nem számít, hogy a meglévő külső felhasználók vannak konfigurálva, akkor valószínűleg rendelkeznek engedélyekkel, amelyek a fiókjukhoz vannak társítva, akár csoporttagságban, akár adott engedélyekben. Kikell-e dolgozni, hogy ezeket az engedélyeket át kell-e telepíteni vagy meg kell tisztítani. A szervezeten belüli, külső felhasználót képviselő fiókokat le kell tiltani, miután a felhasználót áttelepítették egy külső identitásba. Az áttelepítési folyamatot meg kell vitatni az üzleti partnerekkel, mivel előfordulhat, hogy megszakad az erőforrásokhoz való kapcsolódási képességük.

## <a name="migrate-and-test-your-apps"></a>Alkalmazások áttelepítése és tesztelése

Kövesse az ebben a cikkben részletezett áttelepítési folyamatot.

Ezután nyissa meg az [Azure Portalon,](https://aad.portal.azure.com/) és ellenőrizze, hogy az áttelepítés sikeres volt-e. Kövesse az alábbi utasításokat:
1. Válassza a **Vállalati alkalmazások** > **minden alkalmazás lehetőséget,** és keresse meg az alkalmazást a listából.

1. Válassza a**Felhasználók és csoportok** kezelése **lehetőséget,** > ha legalább egy felhasználót vagy csoportot szeretne az alkalmazáshoz rendelni.

1. Válassza a**Feltételes hozzáférés** **kezelése** > lehetőséget. Tekintse át a házirendek listáját, és győződjön meg arról, hogy nem blokkolja az alkalmazáshoz való hozzáférést [feltételes hozzáférési szabályzattal.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

Az alkalmazás konfigurálásától függően ellenőrizze, hogy az SSO megfelelően működik-e. 

| Hitelesítés típusa| Tesztelés |
| - | - |
| OAuth / OpenID Connect| Válassza a **Vállalati alkalmazások > engedélyek et,** és győződjön meg arról, hogy hozzájárult ahhoz, hogy az alkalmazás az alkalmazás felhasználói beállításaiban a szervezetben használható legyen.  
‎ |
| SAML-alapú egyszeri bejelentkezés| Használja az Egyszeri bejelentkezés csoportban található [SAML-beállítások](https://docs.microsoft.com/azure/active-directory/develop/howto-v1-debug-saml-sso-issues) **tesztelése**gombját.  
‎ |
| Jelszóalapú egyszeri bejelentkezés| Töltse le és telepítse a [MyApps Secure Sign](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[-](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[in Extension alkalmazást.](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction) Ez a bővítmény segít elindítani a szervezet bármely felhőalapú alkalmazását, amelyek egy SSO-folyamat használatát igénylik.  
‎ |
| Alkalmazásproxy| Győződjön meg arról, hogy az összekötő fut, és az alkalmazáshoz van rendelve. További segítségért látogasson el az [alkalmazásproxy hibaelhárítási útmutatójába.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot) [ ](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot)  
‎ |

> [!NOTE]
> A régi AD FS-környezetből származó cookie-k továbbra is állandóak lesznek a felhasználó gépein. Ezek a cookie-k problémákat okozhatnak az áttelepítés, mivel a felhasználók lehet irányítani a régi AD FS bejelentkezési környezet, szemben az új Azure AD bejelentkezési. Előfordulhat, hogy manuálisan vagy parancsfájl használatával kell törölnie a felhasználói böngésző cookie-jait. Használhatja a System Center Configuration Manager vagy egy hasonló platform is.

### <a name="troubleshoot"></a>Hibaelhárítás

Ha az áttelepített alkalmazások tesztjének hibái vannak, a hibaelhárítás lehet az első lépés, mielőtt visszaesne a meglévő AD FS függő felekre. Lásd: [Saml-alapú egyszeri bejelentkezés az Alkalmazásokba az Azure Active Directoryban című témakört.](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-v1-debug-saml-sso-issues)

### <a name="rollback-migration"></a>Visszaváltás áttelepítése

Ha az áttelepítés sikertelen, javasoljuk, hogy hagyja a meglévő függő feleket az AD FS-kiszolgálókon, és távolítsa el a hozzáférést a függő entitásokhoz. Ez lehetővé teszi a gyors tartalék, ha szükséges a központi telepítés során.

### <a name="end-user-communication"></a>Végfelhasználói kommunikáció

Bár maga a tervezett kimaradási ablak minimális lehet, továbbra is tervezze meg, hogy ezeket az időkereteket proaktív módon kommunikálja az alkalmazottakkal, miközben az AD FS-ről az Azure AD-re való átépítést teszi lehetővé. Győződjön meg arról, hogy az alkalmazás élménye rendelkezik egy Visszajelzés gombbal, vagy az ügyfélszolgálatra mutató mutatóval kapcsolatos problémákat.

Az üzembe helyezés befejezése után elküldheti a kommunikációt, tájékoztatva a felhasználókat a sikeres üzembe helyezésről, és emlékeztetheti őket a szükséges új lépésekre.

* Utasítsa a felhasználókat, hogy a [Hozzáférési panel](https://myapps.microsoft.com.com/) segítségével férjenek el az összes áttelepített alkalmazáshoz. 

* Emlékeztesse a felhasználókat, hogy szükség lehet frissíteni a MFA-beállításokat. 

* Ha telepítve van az önkiszolgáló jelszó-visszaállítás, előfordulhat, hogy a felhasználóknak frissíteniük kell vagy ellenőrizniük kell a hitelesítési módszereiket. Lásd: [MFA](https://aka.ms/mfatemplates) és [SSPR](https://aka.ms/ssprtemplates) végfelhasználói kommunikációs sablonok.

Kommunikáció külső felhasználókkal: Ez a felhasználói csoport általában a leginkább érintett problémák esetén. Ez különösen akkor igaz, ha a biztonsági állapot a feltételes hozzáférés szabályainak vagy a külső partnerek kockázati profiljainak különböző készletét diktálja. Győződjön meg arról, hogy a külső partnerek tisztában vannak a felhőmigrálási ütemezéssel, és rendelkeznek egy időkerettel, amely alatt arra ösztönzik őket, hogy vegyenek részt egy kísérleti telepítésben, amely a külső együttműködésre egyedi folyamatokat tesztel. Végül győződjön meg arról, hogy van egy módja annak, hogy hozzáférjenek az ügyfélszolgálathoz, ha a problémák megszakadnak.

## <a name="next-steps"></a>Következő lépések
Az [Azure AD-re való migrálási alkalmazáshitelesítés olvasása](https://aka.ms/migrateapps/whitepaper)<p>
Feltételes [hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) és [többéves kor –, mint az MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) beállítása
