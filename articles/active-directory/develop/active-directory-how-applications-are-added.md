---
title: "Hogyan alkalmazások felvétele az Azure Active Directoryban."
description: "Ez a cikk ismerteti, hogyan alkalmazások felvétele az Azure Active Directory példánya."
services: active-directory
documentationcenter: 
author: shoatman
manager: kbrint
editor: 
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2016
ms.author: shoatman
ms.custom: aaddev
ms.openlocfilehash: a97c6af38b1d3191447ad16a789a034a9d475422
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Miért és hogyan lehet az Azure AD-alkalmazások felvétele
A kezdeti puzzling dolog, ha az alkalmazások listáját jeleníti meg az Azure Active Directory-példány az ismertetése, az alkalmazások származási helyét, és miért van.  Ez a cikk nyújt a magas szintű áttekintését hogyan az alkalmazások jelennek meg a könyvtárban, és biztosít, amely segítséget nyújt a megértése, hogyan kérelmet kapott a könyvtárban kellene lennie a környezetben.

## <a name="what-services-does-azure-ad-provide-to-applications"></a>Milyen szolgáltatásokat nyújt az Azure AD az alkalmazások?
Alkalmazások felvétele az Azure AD ki egy vagy több szolgáltatásokról.  Ezek a szolgáltatások a következők:

* Alkalmazás-hitelesítés és engedélyezés
* Felhasználói hitelesítés és engedélyezés
* Egyszeri bejelentkezés (SSO) összevonási vagy a jelszó használata
* A felhasználók átadása & szinkronizálás
* Szerepköralapú hozzáférés-vezérlés; A könyvtár szerepkörök végrehajtásához alkalmazás szerepkörök definiálása engedélyezési ellenőrzések alkalmazáson belüli alapuló.
* oAuth hitelesítési szolgáltatásokat (által használt Office 365 és más Microsoft-alkalmazások API-k/erőforrásokhoz való hozzáférés engedélyezésére.)
* Alkalmazás közzététele & proxy; Az internetre privát hálózatról alkalmazások közzététele

## <a name="how-are-applications-represented-in-the-directory"></a>Alkalmazások hogyan vannak megadva a címtár?
Az Azure AD-2-objektumok jelennek meg az alkalmazások: az alkalmazás objektum és a szolgáltatás egyszerű objektum.  Egy alkalmazás objektum, a regisztrált "otthoni" / "owner" vagy "közzétételének" könyvtárat és egy vagy több szolgáltatás egyszerű objektumok az alkalmazás minden könyvtárban úgy jelző.  

Az application objektum ismerteti az alkalmazást az Azure AD (a több-bérlős szolgáltatás) és tartalmazhatja a következő: (*Megjegyzés*: Ez nem egy tárgyal minden releváns kérdést.)

* Nevét, emblémáját & Publisher
* Titkos kulcsokat (szimmetrikus vagy aszimmetrikus kulcsok az alkalmazás hitelesítéséhez használt)
* API-függőségek (oAuth)
* API-k/erőforrások/hatókörök közzétett (oAuth)
* Alkalmazás-szerepkörök (RBAC)
* Egyszeri bejelentkezés metaadatai és (SSO)
* Felhasználók átadásához metaadatok és a konfiguráció
* Proxy metaadatai és

A szolgáltatás egyszerű az alkalmazás minden könyvtárban, ahol az alkalmazás működik, beleértve a saját címtárukkal rekord.  A szolgáltatás egyszerű:

* App id tulajdonságon keresztül alkalmazás objektumra hivatkozik vissza
* Rekordok helyi felhasználó és csoport alkalmazás-szerepkör-hozzárendelések
* Helyi felhasználói és rendszergazdai engedélyekkel rendelkeznek rögzíti az alkalmazás
  * Például: az alkalmazásnak, hogy egy adott felhasználók e-mailek elérését engedély
* Rögzíti a helyi házirendek többek között a feltételes hozzáférési házirend
* Rekordok helyi alternatív helyi beállítások alkalmazás
  * Jogcím átalakítási szabályok:
  * (A felhasználók átadása) attribútum-leképezésekhez
  * Adott alkalmazást szerepkörök bérlői (ha az alkalmazás támogatja az egyéni szerepkörök)
  * Név vagy embléma

### <a name="a-diagram-of-application-objects-and-service-principals-across-directories"></a>Egy alkalmazás objektumának és könyvtárak között szolgáltatásnevekről ábrája
![Hogyan alkalmazás objektumokat, és az Azure AD-példányban meglévő rendszerbiztonsági tagok szolgáltatás ábrázoló diagram.][apps_service_principals_directory]

A fenti ábrán látható.  A Microsoft fenntartja a két címtár belső (a bal oldalon) alkalmazás-közzétételhez használja.

* Egy a Microsoft Apps (Microsoft-szolgáltatások könyvtára)
* Egy előre integrált 3. fél alkalmazások (Alkalmazásgyűjtemény könyvtár)

Alkalmazás közzétevők/szállítók, akik az Azure ad-vel integrálni kell lennie egy közzétételi könyvtár.  (Egyes Szolgáltatottszoftver-könyvtár).

Alkalmazások, amelyeket saját maga a következők:

* Alkalmazások kidolgozott (integrált AAD-ben)
* A csatlakoztatott alkalmazások single-sign-on
* Az Azure AD-alkalmazásproxy használatával közzétett alkalmazásokhoz.

### <a name="a-couple-of-notes-and-exceptions"></a>Megjegyzések és kivételek néhány
* Nem minden szolgáltatás rendszerbiztonsági tagok mutathat vissza alkalmazás objektumokat.  Huh? Ha az Azure AD eredetileg készült alkalmazások nyújtott szolgáltatások korlátozottabb és a szolgáltatás egyszerű volt elegendő-e az alkalmazás identitását létrehozó.  Az eredeti szolgáltatásnév lett alakzat közelebb a Windows Server Active Directory-szolgáltatásfiókot.  Ezért a továbbra is létre lehet hozni szolgáltatásnevekről alkalmazásobjektum létrehozása nélkül az Azure AD PowerShell segítségével.  A Graph API app objektum szükséges egyszerű szolgáltatás létrehozása előtt.
* A fent megnevezett információkat nem az összes jelenleg kommunikál programozott módon.  A következő csak érhetők el a felhasználói felületen:
  * Jogcím átalakítási szabályok:
  * (A felhasználók átadása) attribútum-leképezésekhez
* További részletes információk az egyszerű szolgáltatás és alkalmazás objektumának tekintse meg az Azure AD Graph REST API referenciadokumentációt tartalmaz.  *Mutató*: az Azure AD Graph API-dokumentáció áll a legközelebbi tudnivaló, hogy a séma-referencia az Azure AD, hogy jelenleg rendelkezésre áll.  
  * [Alkalmazás](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)
  * [Egyszerű szolgáltatásnév](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity)

## <a name="how-are-apps-added-to-my-azure-ad-instance"></a>Hogyan kerülnek alkalmazásokat a saját Azure AD-példányban?
Egy alkalmazás felveheti az Azure AD számos módja van:

* Alkalmazás hozzáadása a [Azure Active Directory-Alkalmazásgyűjtemény](https://azure.microsoft.com/updates/azure-active-directory-over-1000-apps/)
* Jelentkezzen be- / egy 3. fél alkalmazás Azure Active Directoryval integrált be (például: [Smartsheet](https://app.smartsheet.com/b/home) vagy [DocuSign](https://www.docusign.net/member/MemberLogin.aspx))
  * Fel/felhasználók a bejelentkezés során a rendszer felkéri hozzáférése az alkalmazáshoz a profilját és egyéb engedélyek.  Az első személy azokat fel kell venni a directory szolgáltatás egyszerű jelző az alkalmazás okozza.
* Fel/a Microsoft online services bejelentkezési, például [Office 365](http://products.office.com/)
  * Amikor előfizetnie az Office 365 vagy egy próbaverzióra egy vagy több szolgáltatás rendszerbiztonsági tagok jönnek létre a könyvtárban, a különböző szolgáltatások, amelyek az összes Office 365 társított funkcióját biztosításához használt képviselő megkezdéséhez.
  * Néhány Office 365-szolgáltatásokhoz, például a SharePoint szolgáltatásnevekről létrehozása a folyamatos jelleggel összetevők többek között a munkafolyamatok közötti biztonságos kommunikációhoz.
* Az Azure felügyeleti portálon itt talál: kidolgozása alkalmazás hozzáadása: https://msdn.microsoft.com/library/azure/dn132599.aspx
* Adja hozzá a Visual Studio használatával kidolgozása alkalmazás lásd:
  * [ASP.Net hitelesítési módszerek](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)
  * [Kapcsolódó szolgáltatások](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)
* Hozzáadhat egy alkalmazást a is használja a [az Azure AD-alkalmazásproxy](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Az egyszeri bejelentkezéshez a SAML-alapú vagy a jelszó SSO alkalmazás csatlakoztatása
* Sok más többek között a különböző fejlesztői lép az Azure-ban és a/az API explorer észlel fejlesztői erőforrások között

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Ki jogosult az Azure AD-példányban alkalmazások hozzáadása?
Csak a globális rendszergazdák a következőket teheti:

* Hozzáadhat alkalmazásokat az Azure AD-alkalmazásgyűjtemény (előre integrált 3. fél alkalmazások)
* Az Azure AD-alkalmazásproxy használatával alkalmazások közzététele

A címtárban szereplő összes felhasználó rendelkezik, azok fejleszt alkalmazásokat és mely alkalmazások keresztül azok megosztás/adjon hozzáférést a szervezeti adatok megítélése hozzáadására vonatkozó jogok.  *A felhasználói bejelentkezési fel/fontos, hogy egy alkalmazást, és jogosultságokat eredményezhet egy egyszerű szolgáltatásnév létrehozása folyamatban.*

Ez előfordulhat, hogy kezdetben hang vonatkozó, de tartsa szem előtt a következőket:

* Alkalmazások kihasználhatják a Windows Server Active Directory a felhasználók hitelesítéséhez sok éve anélkül, hogy az alkalmazás lehet, hogy a címtárban regisztrált/rögzített törölték.  Mostantól a szervezet fog javult válnak láthatóvá az pontosan hogyan sok alkalmazás a könyvtár és what for használja.
* A rendszergazda által vezérelt alkalmazás közzétételi/regisztrációs folyamat nem szükséges.  Az Active Directory összevonási szolgáltatások volt valószínű, hogy egy rendszergazda adja hozzá az alkalmazás nevében fejlesztők egy függő entitás volt-e.  A fejlesztők mostantól önkiszolgáló is.
* Be- / legfeljebb alkalmazások üzleti célra használja a szervezet fiókok bejelentkező felhasználók azért hasznos.  Ha ezt követően elhagyják a szervezet, akkor megszűnik a hozzáférése az alkalmazásban melyikét használta éppen a fiókjára.
* Feljegyzés az adatok megosztásának időpontját, amellyel alkalmazás azért hasznos rendelkezik.  Adatok minden eddiginél több hordozható, és hogy milyen adatokat osztanak ki mely alkalmazások törlése rekordja hasznos.
* Az Azure AD az oAuth használó alkalmazások döntse el, hogy pontosan milyen engedélyeket, amelyek lehetővé tevő jogot az alkalmazások és engedélyeket ehhez szükséges egy rendszergazdát, hogy elfogadja.  Azt, hogy csak a rendszergazdák nagyobb hatókörökkel és jelentősebb engedélyeket is hozzájárulás értetődő kell lépjen.
* Felhasználók hozzáadása és az alkalmazások számára az adatok elérését lehetővé naplózott események, így az alkalmazások hogyan lett felvéve a címtárba meghatározásához az Azure Managmentet portálon naplózási jelentések megtekintéséhez.

**Megjegyzés:** *magát a Microsoft operációs a következő sok hónapig használ alapértelmezett konfigurációt.*

Az összes, az említett lehetséges, hogy a címtárban a felhasználók ne alkalmazások hozzáadása és megítélése gyakorló keresztül milyen információkat a közös alkalmazások által az Azure felügyeleti portál címtár konfigurációjához.  A következő konfigurációs elérése az Azure felügyeleti portálon a címtár "Beállítása" lapon.

![Egy integrált alkalmazás-beállítások konfigurálása a felhasználói felülete képernyőképe][app_settings]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Következő lépések
További tudnivalók az Azure AD-alkalmazások hozzáadása és az NFS-alkalmazások konfigurálása.

* A fejlesztők: [megtudhatja, hogyan integrálható egy alkalmazás AAD-ben](https://msdn.microsoft.com/library/azure/dn151122.aspx)
* A fejlesztők: [tekintse át a Githubon Azure Active Directoryval integrált alkalmazások mintakód](https://github.com/AzureADSamples)
* A fejlesztők és informatikai szakemberek számára: [tekintse át az Azure Active Directory Graph API REST API dokumentációja](https://msdn.microsoft.com/library/azure/hh974478.aspx)
* Az informatikusok: [Azure Active Directory előre integrált alkalmazások használata az alkalmazás-galériából](https://msdn.microsoft.com/library/azure/dn308590.aspx)
* Az informatikusok: [oktatóprogramjai meghatározott előre integrált alkalmazások konfigurálása](https://msdn.microsoft.com/library/azure/dn893637.aspx)
* Az informatikusok: [útmutató az Azure Active Directory Alkalmazásproxyjával használó alkalmazás közzététele](https://msdn.microsoft.com/library/azure/dn768219.aspx)

## <a name="see-also"></a>Lásd még:
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](../active-directory-apps-index.md)

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
[app_settings]:../media/active-directory-how-applications-are-added/IntegratedAppSettings.jpg
