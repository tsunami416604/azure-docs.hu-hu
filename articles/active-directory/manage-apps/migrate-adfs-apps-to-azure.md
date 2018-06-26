---
title: AD FS helyszíni alkalmazások migrálása az Azure-ba | Microsoft Docs
description: Ez a cikk azt ismerteti, hogy a vállalatoknak hogyan érdemes az Azure AD-be migrálniuk a helyszíni alkalmazásaikat, és kifejezetten az összevont SaaS-alkalmazásaikat.
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/02/2018
ms.author: barbkess
ms.openlocfilehash: 71fa305c0151ba2ff319694cdd3df55bf351e0c5
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2018
ms.locfileid: "36946485"
---
# <a name="migrate-ad-fs-on-premises-apps-to-azure"></a>AD FS helyszíni alkalmazások migrálása az Azure-ba 

Ez a cikk leírja, hogyan lehet a helyszíni alkalmazásokat az Azure Active Directory-ba (Azure AD) migrálni. A dokumentum az összevont SaaS-alkalmazásokra helyezi a hangsúlyt. 

A dokumentum nem tartalmaz részletes útmutatást. Elméleti támogatást nyújt a sikeres migráláshoz, mivel segít megérteni, hogy a helyszíni konfigurációk hogyan alakulnak át az Azure AD-ben. Emellett ismerteti a gyakori forgatókönyveket is.

## <a name="introduction"></a>Bevezetés

Ha van felhasználói fiókokat tartalmazó helyszíni címtára, valószínűleg legalább egy vagy két alkalmazással is rendelkezik. Ezek az alkalmazások pedig úgy vannak konfigurálva, hogy a felhasználók a címtárban lévő identitásukkal bejelentkezve használhatják őket.

Ha vállalata a legtöbb vállalathoz hasonló, akkor valószínűleg már elkezdte a felhőalapú alkalmazások és identitások bevezetését. Lehet, hogy már használják is az Office 365-öt és az Azure AD Connectet. Lehet, hogy egyes fontosabb számítási feladatokhoz már alakítottak ki felhőalapú SaaS-alkalmazásokat, azonban még nem mindegyikhez.  

Az Office 365- és az Azure AD-alapú alkalmazások mellett számos vállalat rendelkezik olyan SaaS vagy egyéni üzletági (LoB) alkalmazásokkal, amelyek közvetlenül össze vannak vonva valamilyen helyszíni bejelentkezési szolgáltatással, például az Active Directory összevonási szolgáltatással (AD FS). Ez a migrációs útmutató leírja, hogy miért és hogyan érdemes a helyszíni alkalmazásokat az Azure AD-be migrálni.

>[!NOTE]
>Az útmutató részletes információkat tartalmaz a SaaS-alkalmazások konfigurálásáról és migrálásáról, valamint áttekintést nyújt az egyéni üzletági alkalmazásokról. A jövőben tervezünk részletesebb útmutatót is kiadni az egyéni üzletági alkalmazásokról.

![Közvetlenül a helyszínen csatlakoztatott alkalmazások](media/migrate-adfs-apps-to-azure/migrate1.png)

![Az Azure AD-n keresztül összevont alkalmazások](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="reasons-for-migrating-apps-to-azure-ad"></a>Amiért érdemes az Azure AD-be migrálni az alkalmazásokat

Az olyan vállalatok számára, amelyek az AD FS-t, a Pinget vagy valamely más helyszíni hitelesítési szolgáltatót használnak, az alkalmazások Azure AD-be való migrálása a következő előnyöket biztosítja:

**Biztonságosabb hozzáférés**
- Részletesebb alkalmazásonkénti hozzáférés-vezérlés konfigurálása, beleértve az Azure Multi-Factor Authenticationt az [Azure AD feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md) használatával. A szabályzatok ugyanúgy alkalmazhatók az SaaS- és az egyéni alkalmazásokra, ahogy jelenleg az Office 365-alkalmazásokra.
- Az [Azure AD Identity Protection](../active-directory-identityprotection.md) használatával kiaknázhatja annak képességeit a fenyegetések gépi tanulással és heurisztikával való észleléséhez és a bejelentkezések védelméhez.

**Azure AD B2B együttműködés**
- Miután az SaaS-alkalmazásokba való bejelentkezést átállította Azure AD-alapúra, az [Azure AD B2B együttműködés](../b2b/what-is-b2b.md) segítségével hozzáférést biztosíthat partnereinek a felhőalapú erőforrásokhoz.

**Könnyebben kezelhető rendszergazdai felületek és az Azure AD további képességei**
- Az SaaS-alkalmazások identitásszolgáltatójaként az Azure AD további képességeket is támogat, például:
  - Alkalmazásonkénti jogkivonat-aláíró tanúsítványok.
  - [A tanúsítványok konfigurálható lejárati dátuma](manage-certificates-for-federated-single-sign-on.md).
  - [A felhasználói fiókok automatikus üzembe helyezése](../active-directory-saas-app-provisioning.md) (a legfontosabb Azure Marketplace-alkalmazásokban) az Azure AD-identitások alapján.

**A helyszíni identitásszolgáltatók által biztosított előnyök megmaradnak**
- Az Azure AD előnyei mellett továbbra is használhatja a helyszíni hitelesítési megoldását. Így például a helyszíni többtényezős hitelesítési megoldások, a naplózás, a felülvizsgálat és az egyéb előnyök nem vesznek el. 

**Segítség a helyszíni identitásszolgáltató kivezetésében**
- Az olyan vállalatok számára, amelyek meg kívánják szüntetni a helyszíni hitelesítési megoldásaikat, az alkalmazások Azure AD-be való migrálása megkönnyíti a váltást, mivel a munka egy részét leveszi a vállukról. 

## <a name="mapping-types-of-apps-on-premises-to-types-of-apps-in-azure-ad"></a>A helyszíni alkalmazástípusok megfeleltetése az Azure AD-alkalmazástípusoknak
A legtöbb alkalmazás a bejelentkezés típusa alapján néhány kategóriába sorolható. Ezek a kategóriák határozzák meg, hogy az alkalmazás hogyan jelenik meg az Azure AD-ben.

Az SAML 2.0-alkalmazások a Marketplace-en Azure AD alkalmazáskatalógusán keresztül vagy nem Marketplace-alkalmazásként integrálhatók az Azure AD-be. Az OAuth 2.0 vagy OpenID Connect hitelesítést használó alkalmazások hasonló módon, *alkalmazásregisztrációként* integrálhatók az Azure AD-be. A további részletekért olvasson tovább.

### <a name="federated-saas-apps-vs-custom-lob-apps"></a>Összevont SaaS-alkalmazások vagy egyéni üzletági alkalmazások
Az összevont alkalmazások az alábbi kategóriákba tartozó alkalmazásokat foglalják magukban:

- SaaS-alkalmazások 
    - Ha a vállalati felhasználók SaaS-alkalmazásokba (például Salesforce, ServiceNow vagy Workday) jelentkeznek be, és ehhez integrált egy helyszíni identitásszolgáltatót (például AD FS vagy Ping), akkor összevont bejelentkezést használ az SaaS-alkalmazásokhoz.
    - Az alkalmazások általában az SAML 2.0 protokollt használják az összevont bejelentkezéshez.
    - Az ebbe a kategóriába tartozó alkalmazások vállalati alkalmazásként integrálhatók az Azure AD-be, akár a Marketplace-ről, akár nem Marketplace-beli alkalmazásokként.
- Egyéni üzletági alkalmazások
    - Ez a nem SaaS-alkalmazásokat jelenti, amelyeket a vállalat maga fejlesztett ki, vagy amelyek standard csomagolt termékként érhetők el, és telepítve vannak a vállalati adatközpontba. Ide tartoznak a SharePoint-alkalmazások és a Windows Identity Foundation alapú alkalmazások.
    - Az alkalmazások SAML 2.0, WS-Federation, OAuth vagy OpenID Connect hitelesítést használhatnak az összevont bejelentkezéshez.
    - Az OAuth 2.0, OpenID Connect vagy WS-Federation hitelesítést használó egyéni alkalmazások hasonló módon, alkalmazásregisztrációként integrálhatók az Azure AD-be. Az SAML 2.0 vagy WS-Federation hitelesítést használó egyéni alkalmazások nem Marketplace-alkalmazásokként integrálhatók a vállalati alkalmazásokba.

### <a name="non-federated-apps"></a>Nem összevont alkalmazások
Emellett a nem összevont alkalmazások is integrálhatók az Azure AD-be az Azure AD-alkalmazásproxy és a kapcsolódó képességek segítségével. A nem összevont alkalmazások közé tartoznak:
- Az integrált Windows-hitelesítést az Active Directory-val közvetlenül használó alkalmazások. Ezek az alkalmazások az [Azure AD-alkalmazásproxyval](application-proxy-publish-azure-portal.md) integrálhatók az Azure AD-be.
- Az egyszeri bejelentkezésszolgáltatóval egy ügynökön keresztül integrált, a fejlécalapú hitelesítést használó alkalmazások. A telepített ügynökön keresztül bejelentkező, fejlécalapú hitelesítést használó helyszíni alkalmazások az Azure AD-alkalmazásproxy és a [Ping Access for Azure AD](https://blogs.technet.microsoft.com/enterprisemobility/2017/06/15/ping-access-for-azure-ad-is-now-generally-available-ga/) használatával konfigurálhatók az Azure AD-alapú bejelentkezésre.

## <a name="translating-on-premises-federated-apps-to-azure-ad"></a>Helyszíni összevont alkalmazások átalakítása az Azure AD-ben való használathoz 
Az AD FS és az Azure AD hasonlóan működik, így a megbízhatóság konfigurálásával, a bejelentkezési és kijelentkezési URL-címekkel és az azonosítókkal kapcsolatos fogalmak mindkettőre vonatkoznak. Van azonban néhány kisebb eltérés, amelyeket a sikeres váltáshoz ismernie kell.

Ebben a táblázatban összefoglaltuk az AD FS, az Azure AD és az SaaS-alkalmazások fontosabb közös fogalmait, hogy ezzel segítsük a váltást. 

### <a name="representing-the-app-in-azure-ad-or-ad-fs"></a>Az alkalmazás megjelenítése az Azure AD-ben vagy az AD FS-ben
A migrálás első lépéseként mérje fel az alkalmazás helyszíni konfigurációját, majd képezze le az Azure AD-be. Az alábbi táblázatban egy AD FS-beli függő entitás konfigurációjának elemeit képeztük le a megfelelő elemekre az Azure AD-ben.  
- AD FS-beli kifejezés: Függő entitás vagy függő entitás megbízhatósága.
- Azure AD-beli kifejezés: Vállalati alkalmazás vagy alkalmazásregisztráció (az alkalmazás típusától függően).

|Alkalmazáskonfigurációs elem|Leírás|Hely az AD-FS-beli konfigurációban|A megfelelő hely az Azure AD-beli konfigurációban|SAML-jogkivonat eleme|
|-----|-----|-----|-----|-----|
|Alkalmazás bejelentkezési URL-címe|Az alkalmazás bejelentkezési oldalának URL-címe. A felhasználó itt jelentkezhet be az alkalmazásba az SP által kezdeményezett SAML-folyamatok keretében.|–|Az Azure AD-ben a bejelentkezési URL-cím az Azure Portalon, az alkalmazás **Egyszeri bejelentkezési** tulajdonságainál, bejelentkezési URL-cím néven van konfigurálva.</br></br>(Lehetséges, hogy a bejelentkezési URL-cím megjelenítéséhez a **Speciális URL-beállítások megjelenítése** elemre kell kattintania.)|–|
|Alkalmazás válasz URL-címe|Az alkalmazás az identitásszolgáltató által használt URL-címe. Az identitásszolgáltató ide küldi a felhasználó adatait és a jogkivonatot, miután a felhasználó bejelentkezett az identitásszolgáltatóra.</br></br> Ezt az „SAML helyességi feltétel fogyasztói végpontjának” is hívják.|Az alkalmazás AD FS-beli függő entitás megbízhatóságának részét képezi. Kattintson a jobb gombbal a függő entitásra, válassza a **Tulajdonságok** parancsot, majd nyissa meg a **Végpontok** lapot.|Az Azure AD-ben a válasz URL-cím az Azure Portalon, az alkalmazás **Egyszeri bejelentkezési** tulajdonságainál, válasz URL-cím néven van konfigurálva.</br></br>(Lehetséges, hogy a bejelentkezési URL-cím megjelenítéséhez a **Speciális URL-beállítások megjelenítése** elemre kell kattintania.)|Az SAML-jogkivonat **Cél** elemének felel meg.</br></br> Példaérték: https://contoso.my.salesforce.com|
|Alkalmazás kijelentkezési URL-címe|Erre az URL-címre a felhasználók kijelentkezésekor a „kijelentkezési tisztítási” kérések érkeznek, amelyek az összes többi alkalmazásból kijelentkeztetik a felhasználót, amelyekbe az identitásszolgáltató bejelentkeztette.|Az AD FS-kezelőben a **Függő entitások megbízhatósága** alatt található. Kattintson a jobb gombbal a függő entitásra, válassza a **Tulajdonságok** parancsot, majd nyissa meg a **Végpontok** lapot.|N/A. Az Azure AD nem támogatja az „egyszeri kijelentkezést”, azaz az összes alkalmazásból való kijelentkezést. Egyszerűen magából az Azure AD-ből jelentkezteti ki a felhasználót.|–|
|Alkalmazásazonosító|Az alkalmazás identitásszolgáltató által használt azonosítója. Gyakran (de nem mindig) a bejelentkezési URL-cím az azonosító.</br></br> Az alkalmazás időnként „entitásazonosítóként” hivatkozik rá.|Az AD FS-ben ez a függő entitás azonosítója. Kattintson a jobb gombbal a függő entitás megbízhatóságára, válassza a **Tulajdonságok** parancsot, majd nyissa meg az **Azonosítók** lapot.|Az Azure AD-ben az azonosító az Azure Portalon, az alkalmazás **egyszeri bejelentkezési** tulajdonságainál, a **Tartomány és URL-címek** területen, Azonosító néven van konfigurálva. (Lehetséges, hogy a megjelenítéséhez be kell jelölnie a **Speciális URL-beállítások megjelenítése** jelölőnégyzetet.)|Az SAML-jogkivonat **Célközönség** elemének felel meg.|
|Alkalmazás összevonási metaadatai|Az alkalmazás összevonási metaadatainak helye. Az identitásszolgáltató használja egyes konfigurációs beállítások, például a végpontok vagy a titkosítási tanúsítványok automatikus frissítéséhez.|Az alkalmazás összevonási metaadatainak URL-címe az alkalmazás AD FS-beli függő entitás megbízhatóságának részét képezi. Kattintson jobb gombbal a megbízhatóságra, válassza a **Tulajdonságok** parancsot, majd válassza a **Figyelés** lapot.|N/A. Az Azure AD nem támogatja az alkalmazások összevonási metaadatainak közvetlen felhasználását.|–|
|Felhasználói azonosító /**NameID**|A felhasználó identitását az Azure AD-ből vagy az AD FS-ből az alkalmazás felé egyértelműen azonosító attribútum.</br></br> Általában a felhasználó egyszerű felhasználóneve vagy e-mail-címe.|Az AD FS-ben ez a függő entitásra vonatkozó jogcímszabályként található meg. A legtöbb esetben a jogcímszabály ad ki egy jogcímet a „nameidentifier” karakterláncra végződő típussal.|Az Azure AD-ben a felhasználói azonosító az Azure Portalon, az alkalmazás **Egyszeri bejelentkezési** tulajdonságainál található a **Felhasználói attribútumok** címszó alatt.</br></br>A rendszer alapértelmezés szerint az egyszerű felhasználónevet használja.|Az SAML-jogkivonat **NameID** elemeként jut el az identitásszolgáltatótól az alkalmazáshoz.|
|Az alkalmazásnak küldendő egyéb jogcímek|A felhasználói azonosító/**NameID** mellett az identitásszolgáltató gyakran egyéb jogcím-információkat is küld az alkalmazásnak. Például a felhasználó utónevét, vezetéknevét, e-mail-címét és csoporttagságait.|Az AD FS-ben ez a függő entitásra vonatkozó egyéb jogcímszabályokként található meg.|Az Azure AD-ben a felhasználói azonosító az Azure Portalon, az alkalmazás **Egyszeri bejelentkezési** tulajdonságainál található a **Felhasználói attribútumok** címszó alatt. Válassza ki az **Egyéb felhasználói attribútumok megtekintése** és szerkesztése elemet.|–|  

### <a name="representing-azure-ad-as-an-identity-provider-in-an-saas-app"></a>Az Azure AD identitásszolgáltatóként való szerepeltetése SaaS-alkalmazásokban
A migrálás keretében az alkalmazást konfigurálnia kell, hogy az Azure AD-re (és ne a helyszíni identitásszolgáltatóra) mutasson. Ez a szakasz elsősorban az SAML protokollt használó SaaS-alkalmazásokkal, és nem az egyéni üzletági alkalmazásokkal foglalkozik. A leírt alapelvek azonban az egyéni üzletági alkalmazásokra is érvényesek. 

Lényegében csak néhány főbb dolog szükséges az SaaS-alkalmazások az Azure AD felé való irányításához:
- Identitásszolgáltató kibocsátója: https&#58;//sts.windows.net/{bérlőazonosító}/
- Identitásszolgáltató bejelentkezési URL-címe: https&#58;//login.microsoftonline.com/{bérlőazonosító}/saml2
- Identitásszolgáltató kijelentkezési URL-címe: https&#58;//login.microsoftonline.com/{bérlőazonosító}/saml2 
- Összevonási metaadatok helye: https&#58;//login.windows.net/{bérlőazonosító} <bérlőazonosító>/federationmetadata/2007-06/federationmetadata.xml?appid={<alkalmazásazonosító} 

A {bérlőazonosító} helyére a bérlő azonosítóját kell írni, amely az Azure Portalon az **Azure Active Directory** > **Tulajdonságok** területen **Címtár-azonosító** néven található. A {bérlőazonosító} helyére a bérlő azonosítóját kell írni, amely az alkalmazás tulajdonságok területen **Alkalmazásazonosító** néven található.

A következő táblázat ismerteti az identitásszolgáltató konfigurációs fő elemeit, amelyek az alkalmazások egyszeri bejelentkezési beállításainak konfigurálására használhatók, valamint ezek értékeit és helyét az AD FS-ben és az Azure AD-ben. A tábla hivatkozási alapja az SaaS-alkalmazás, amelynek tudnia kell, hogy hová küldheti a hitelesítési kéréseket, és hogyan érvényesítheti a visszakapott jogkivonatokat.

|Konfigurációs elem|Leírás|AD FS|Azure AD|
|---|---|---|---|
|IdP </br>bejelentkezési </br>URL-cím|Az identitásszolgáltató az alkalmazás által használt bejelentkezési URL-címe (ahová a rendszer átirányítja a felhasználót a bejelentkezéshez).|Az AD FS bejelentkezési URL-címe az AD FS összevonási szolgáltatás neve az „/adfs/ls/” utótaggal kiegészítve. Például: https&#58;//fs.contoso.com/adfs/ls/|Az Azure AD megfelelő értéke azt a mintát követi, ahol a {bérlőazonosító} helyére a bérlő azonosítóját kell írni. Az Azure Portalon az **Azure Active Directory** > **Tulajdonságok** területen **Címtár-azonosító** néven található.</br></br>Az SAML-P protokollt használó alkalmazások esetében: https&#58;//login.microsoftonline.com/{bérlőazonosító}/saml2 </br></br>A WS-Federation protokollt használó alkalmazások esetében: https&#58;//login.microsoftonline.com/{bérlőazonosító}/wsfed|
|IdP </br>kijelentkezés </br>URL-cím|Az identitásszolgáltató az alkalmazás által használt kijelentkezési URL-címe (ahová a rendszer átirányítja a felhasználót, amikor kijelentkezik az alkalmazásból).|Az AD FS esetében a kijelentkezési URL-cím vagy azonos a bejelentkezési URL-címmel, vagy megegyezik vele, de a „wa=wsignout1.0” utótaggal egészül ki. Például: https&#58;//fs.contoso.com/adfs/ls/?wa=wsignout1.0|Az Azure AD megfelelő értéke attól függ, hogy az alkalmazás támogatja-e az SAML 2.0-alapú kijelentkezést.</br></br>Ha az alkalmazás támogatja az SAML-alapú kijelentkezést, az érték azt a mintát követi, ahol a {bérlőazonosító} helyére a bérlő azonosítóját kell írni. Az Azure Portalon az **Azure Active Directory** > **Tulajdonságok** területen **Címtár-azonosító** néven található: https&#58;//login.microsoftonline.com/{bérlőazonosító}/saml2</br></br>Ha az alkalmazás nem támogatja az SAML-alapú kijelentkezést: https&#58;//login.microsoftonline.com/common/wsfederation?wa=wsignout1.0|
|Jogkivonat </br>aláírás </br>tanúsítvány|A tanúsítvány, amelynek a titkos kulcsát az identitásszolgáltató a kiállított jogkivonatok aláírásához használja. Igazolja, hogy a jogkivonat attól az identitásszolgáltatótól származik, amellyel az alkalmazás megbízhatósági kapcsolata konfigurálva van.|Az AD FS jogkivonat-aláíró tanúsítványa az AD FS-kezelőben a **Tanúsítványok** területen található.|Az Azure AD-ben a jogkivonat-aláíró tanúsítvány az Azure Portalon, az alkalmazás **egyszeri bejelentkezési** tulajdonságainál található **SAML aláíró tanúsítvány** néven. Innen letöltheti a tanúsítványt, hogy feltöltse az alkalmazásba.</br></br> Ha az alkalmazás több tanúsítvánnyal is rendelkezik, az összes tanúsítvány megtalálható az összevonási metaadatokat tartalmazó XML-fájlban.|
|Azonosító/</br>„kiállító”|Az identitásszolgáltató az alkalmazás által használt azonosítója (más néven „Kiállítóazonosító”).</br></br>Az SAML-jogkivonatban ez az érték a **Kiállító** elemként található meg.|Az AD FS esetében az azonosító általában az összevonási szolgáltatás azonosítója az AD FS-kezelőben, amely a **Szolgáltatás** > **Összevonási szolgáltatás tulajdonságainak szerkesztése** területen található. Például: http&#58;//fs.contoso.com/adfs/services/trust.|Az Azure AD megfelelő értéke azt a mintát követi, ahol a {bérlőazonosító} helyére a bérlő azonosítóját kell írni. Az Azure Portalon az **Azure Active Directory** > **Tulajdonságok** területen **Címtár-azonosító**néven található: https&#58;//sts.windows.net/{bérlőazonosító}/|
|IdP </br>összevonás </br>metaadatok|Az identitásszolgáltató nyilvánosan elérhető összevonási metaadatainak helye. (Az összevonási metaadatokat egyes alkalmazások alternatív megoldásként használják, hogy a rendszergazdának ne kelljen egyenként konfigurálnia az URL-címeket, azonosítókat és jogkivonat-aláíró tanúsítványokat.)|Az AD FS összevonási metaadatainak URL-címe az AD FS-kezelőben a **Szolgáltatás** > **Végpontok** > **Metaadatok** > **Típus: összevonási metaadatok** területen található. Például: https&#58;//fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml|Az Azure AD megfelelő értéke a https&#58;//login.microsoftonline.com/{BérlőTartományNeve}/FederationMetadata/2007-06/FederationMetadata.xml. mintát követi. Ahol a {BérlőTartományNeve} helyére a bérlő nevét kell írni „contoso.onmicrosoft.com” formátumban. </br></br>További információkat itt talál: [Összevonási metaadatok](../develop/active-directory-federation-metadata.md).

## <a name="migrating-saas-apps"></a>SaaS-alkalmazások migrálása
Az SaaS-alkalmazások az AD FS-ből vagy más identitásszolgáltatóról az Azure AD-be való migrálása jelenleg manuálisan hajtható végre. Alkalmazásspecifikus útmutatásért lásd a [Marketplace-en található SaaS-alkalmazások integrálását ismertető oktatóanyagok listáját](../saas-apps/tutorial-list.md).

Az integrációs oktatóanyagok feltételezik, hogy zöldmezős integrációt hajt végre. Néhány, a migrálásra vonatkozó fontos alapelvet érdemes figyelembe vennie az alkalmazások tervezése, felmérése, konfigurálása és átállítása során:  
- Egyes alkalmazások migrálása egyszerű. Az összetettebb követelményekkel rendelkező, például egyedi jogcímeket igénylő alkalmazások esetében azonban további konfigurálásra lehet szükség az Azure AD-ben és/vagy az Azure AD Connectben.
- A leggyakoribb forgatókönyvekben csak a **NameID** jogcím és egyéb általános felhasználóazonosító jogcímek szükségesek az alkalmazáshoz. Annak meghatározásához, hogy szükséges-e bármilyen további jogcím, vizsgálja meg, milyen jogcímeket bocsát ki az AD FS vagy a külső identitásszolgáltató.
- Miután megállapította, hogy további jogcímek szükségesek, meg kell győződnie róla, hogy az Azure AD-ben elérhetők. Ellenőrizze az Azure AD Connect szinkronizálási konfigurációjában, hogy a szükséges attribútumok, például a **samAccountName** attribútum szinkronizálódik-e az Azure AD-be.
- Amint az attribútumok elérhetők az Azure AD-ben, adjon hozzá jogcímkiállítási szabályokat az Azure AD-ben, hogy ezek az attribútumok jogcímként szerepeljenek a kiállított jogkivonatokban. Az alkalmazás **egyszeri bejelentkezési** tulajdonságain belül adhatja hozzá ezeket az Azure AD-ben.

### <a name="assess-what-can-be-migrated"></a>A migrálható tartalmak felmérése
Az SAML 2.0-alkalmazások a Marketplace-en Azure AD alkalmazáskatalógusán keresztül vagy nem Marketplace-alkalmazásként integrálhatók az Azure AD-be.  

Bizonyos konfigurációk Azure AD-beli beállításához további lépések szükségesek, más konfigurációk pedig jelenleg nem támogatottak. Annak megállapításához, hogy mi migrálható, tekintse át az egyes alkalmazások aktuális konfigurációját. Különös tekintettel a következőkre:
- A konfigurált jogcímszabályok (kiadásátalakítási szabályok).
- Az SAML **NameID** formátum és attribútum.
- A kiállított SAML-jogkivonatverziók.
- Egyéb konfigurációk, például kiállításengedélyezési szabályok vagy hozzáférés-vezérlési szabályzatok és többtényezős hitelesítési (további hitelesítési) szabályok.

#### <a name="what-can-be-migrated-today"></a>A jelenleg migrálható alkalmazások
A jelenleg könnyen migrálható alkalmazások közé a konfigurációs elemek és jogcímek szabványos készletét használó SAML 2.0-alkalmazások tartoznak. Ezek az alkalmazások a következőket tartalmazhatják:
- Egyszerű felhasználónév.
- E-mail-cím.
- Utónév.
- Vezetéknév.
- Az SAML **NameID**-ként használt alternatív attribútum, például az Azure AD Mail attribútum, a Mail előtag, az alkalmazott azonosítója, az 1-15 bővítményattribútumok vagy a helyszíni **SamAccountName**. További információkat itt talál: A [NameIdentifier](../develop/active-directory-saml-claims-customization.md) jogcím szerkesztése.
- Egyéni jogcímek. A támogatott jogcímek társításával kapcsolatos információért tekintse meg a [Jogcímtársítások az Azure Active Directory-ban](../active-directory-claims-mapping.md) és az [SAML-jogkivonatban kiadott jogcímek testreszabása vállalati alkalmazások esetén az Azure Active Directory-ban](../develop/active-directory-saml-claims-customization.md) témakört.

Az egyéni jogcímek és **NameID** elemek mellett az Azure AD-ben a migráció keretében további konfigurációs lépéseket igénylő konfigurációk a következők:
- Az AD FS egyéni hitelesítési vagy többtényezős hitelesítési szabályai. Az [Azure AD feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md) funkciójával konfigurálhatja őket.
- A több SAML-végponttal rendelkező alkalmazások. Az Azure AD-ben a PowerShell segítségével konfigurálhatja őket. (Ez a funkció a portálon nem érhető el.)
- A WS-Federationt használó alkalmazások, például a SharePoint-alkalmazások, amelyek SAML 1.1-es verziójú jogkivonatot igényelnek. Ezeket manuális módszerrel kell konfigurálnia a PowerShell használatával.

#### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Az Azure AD-ben jelenleg nem támogatott alkalmazások és konfigurációk
A következő képességeket igénylő alkalmazások jelenleg nem migrálhatók. Ha ilyen szolgáltatásokat igénylő alkalmazásokkal rendelkezik, küldjön visszajegyzést a megjegyzések szakaszban.
- Protokollképességek:
    - Az SAML az összes bejelentkezett alkalmazásból való egyszeri kijelentkezés (SLO) szolgáltatásának támogatása.
    - A WS-Trust ActAs minta támogatása.
    - Az SAML-összetevő felbontása.
    - Az aláírt SAML-kérések aláírás-ellenőrzése. Megjegyzendő, hogy a rendszer elfogadja az aláírt kéréseket, de nem ellenőrzi az aláírást.
- Jogkivonat-képességek: 
    - SAML-jogkivonat titkosítása. 
    - SAML 1.1-es verziójú jogkivonatok az SAML-protokoll válaszaiban. 
- Jogkivonatokban lévő jogcímek képességei:
    - Helyszíni csoportnevek kiállítása jogcímekként.
    - Az Azure AD-től eltérő tárolókból származó jogcímek.
    - Összetett jogcímkiadás-átalakítási szabályok. A támogatott jogcímek társításával kapcsolatos információért tekintse meg a [Jogcímtársítások az Azure Active Directory-ban](../active-directory-claims-mapping.md) és az [SAML-jogkivonatban kiadott jogcímek testreszabása vállalati alkalmazások esetén az Azure Active Directory-ban](../develop/active-directory-saml-claims-customization.md) témakört.
    - Címtárbővítmények kiállítása jogcímekként.
    - A **NameID**-formátum egyedi meghatározása.
    - Többértékű attribútumok kiállítása.

>[!NOTE]
>Az Azure AD folyamatosan további képességekkel bővül ezen a területen. Ezt a cikket rendszeresen frissítjük. 

### <a name="configure-azure-ad"></a>Az Azure AD konfigurálása    
#### <a name="configure-single-sign-on-sso-settings-for-the-saas-app"></a>Az SaaS-alkalmazás egyszeri bejelentkezési (SSO) beállításainak konfigurálása

Az Azure AD-ben az SAML-alapú bejelentkezés (az alkalmazás igényeinek megfelelően) az alkalmazás **Egyszeri bejelentkezési** tulajdonságainál, a **Felhasználói attribútumok** területen konfigurálható.

![Egyszeri bejelentkezési tulajdonságok „Felhasználói attribútumok” szakasszal](media/migrate-adfs-apps-to-azure/migrate3.png)

Válassza ki az **Egyéb felhasználói attribútumok megtekintése és szerkesztése** elemet a biztonsági jogkivonatban jogcímekként küldendő attribútumok megtekintéséhez.

![Attribútumok listája](media/migrate-adfs-apps-to-azure/migrate4.png)

A szerkesztéshez kattintson egy attribútum sorára, új attribútum hozzáadásához pedig az **Attribútum hozzáadása** elemre.

![„Attribútum szerkesztése” panel](media/migrate-adfs-apps-to-azure/migrate5.png)

#### <a name="assign-users-to-the-app"></a>Felhasználók hozzárendelése az alkalmazáshoz
Ahhoz, hogy az Azure AD-beli felhasználók bejelentkezhessenek egy adott SaaS-alkalmazásba, hozzáférést kell adnia nekik.

A felhasználók az Azure AD Portalon való hozzárendeléséhez lépjen az SaaS-alkalmazás oldalra, és kattintson az oldalsáv **Felhasználók és csoportok** elemére. Felhasználó vagy csoport hozzáadásához kattintson a képernyő tetején a **Felhasználó hozzáadása** elemre. 

![„Felhasználó hozzáadása” gomb a „Felhasználók és csoportok” területen](media/migrate-adfs-apps-to-azure/migrate6.png) 

![„Hozzárendelés hozzáadása” panel](media/migrate-adfs-apps-to-azure/migrate7.png)

A hozzáférés ellenőrzéséhez győződjön meg róla, hogy a felhasználó látja-e az adott SaaS-alkalmazást a bejelentkezéskor a [hozzáférési panelen](../active-directory-saas-access-panel-introduction.md). A hozzáférési panel itt található http://myapps.microsoft.com. Ebben a példában az alábbi felhasználóhoz sikeresen hozzá lett rendelve a Salesforce- és a ServiceNow-hozzáférés.

![Példa: hozzáférési panel a Salesforce és a ServiceNow alkalmazásokkal](media/migrate-adfs-apps-to-azure/migrate8.png)

### <a name="configure-the-saas-app"></a>Az SaaS-alkalmazás konfigurálása
A helyszíni összevonási megoldásról az Azure AD-re való váltás folyamata attól függ, hogy az érintett SaaS-alkalmazás támogatja-e több identitásszolgáltató használatát. Itt találja a több identitásszolgáltató használatának támogatására vonatkozó gyakori kérdéseket:

   **K: Mit jelent, ha egy adott alkalmazás több identitásszolgáltatót támogat?**
    
   V: A több identitásszolgáltató használatát támogató SaaS-alkalmazásokban az új identitásszolgáltató (esetünkben az Azure AD) minden adata megadható a bejelentkezési felület módosításainak véglegesítése előtt. Miután végzett a konfiguráció megadásával, átválthatja az alkalmazás hitelesítési konfigurációját, hogy az Azure AD-re mutasson.

   **K: Mi a jelentősége, hogy egy SaaS-alkalmazás támogatja-e több identitásszolgáltató használatát?**

   V: Ha az alkalmazás nem támogatja több identitásszolgáltató használatát, a rendszergazdának meg kell jelölnie egy rövid szerviz- vagy karbantartási időszakot. Az alkalmazás ezalatt nem lesz elérhető, mivel a rendszergazda konfigurálja az Azure AD-t az alkalmazás új identitásszolgáltatójaként. A felhasználókat tájékoztatni kell, hogy a szolgáltatáskimaradás közben nem fognak tudni a bejelentkezni fiókba.

   Ha az alkalmazás támogatja több identitásszolgáltató használatát, az új identitásszolgáltató konfigurálása előre elvégezhető. A rendszergazda az Azure átállításakor az identitásszolgáltatót is egyszerűen átválthatja.

   Ha az alkalmazás támogatja több identitásszolgáltató használatát, és Ön úgy dönt, hogy egyidejűleg több identitásszolgáltató is kezelheti a bejelentkezések hitelesítését, a felhasználó a bejelentkezési oldalon eldöntheti, hogy melyik identitásszolgáltatót használja a hitelesítéshez.

#### <a name="example-support-for-multiple-idps"></a>Példa: Több identitásszolgáltató támogatása
A Salesforce-ban például az identitásszolgáltató konfigurációja a **Beállítások** > **Vállalati beállítások** > **Saját tartomány** > **Hitelesítési konfiguráció** területen található.

![„Hitelesítés konfiguráció” szakasz a Salesforce alkalmazásban](media/migrate-adfs-apps-to-azure/migrate9.png)

A korábban az **Identitás** > **Egyszeri bejelentkezés beállításai** felületen létrehozott konfigurációnak köszönhetően módosíthatja a hitelesítési konfiguráció identitásszolgáltatóját. Például átválthat az AD FS-ről az Azure AD-re. 

![Az Azure AD kiválasztása hitelesítési szolgáltatásként](media/migrate-adfs-apps-to-azure/migrate10.png)

### <a name="optional-configure-user-provisioning-in-azure-ad"></a>Választható: Felhasználók átadásának konfigurálása az Azure AD-ben
Ha szeretné megtudni, hogyan kezelje közvetlenül az Azure AD a felhasználókiépítést egy SaaS-alkalmazásban, tekintse meg [A felhasználókiépítés és -megszüntetés automatizálása az SaaS-alkalmazásokban az Azure Active Directoryval](../active-directory-saas-app-provisioning.md) témakort.

## <a name="next-steps"></a>További lépések

- [Alkalmazások kezelése az Azure Active Directoryval](what-is-application-management.md)
- [Alkalmazások hozzáférésének kezelése](what-is-access-management.md)
- [Azure AD Connect-összevonás](../active-directory-aadconnectfed-whatis.md)
