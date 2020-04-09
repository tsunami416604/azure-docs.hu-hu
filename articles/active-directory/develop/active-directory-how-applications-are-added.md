---
title: Hogyan és miért adhatók hozzá az alkalmazások az Azure AD szolgáltatáshoz
titleSuffix: Microsoft identity platform
description: Mit jelent egy alkalmazás hozzáadása az Azure AD-hez, és hogyan jutnak el oda?
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: 01ea22af472877abe34236ec82a7750eccfcdfb9
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884273"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Hogyan és miért kerülnek hozzá az alkalmazások az Azure AD-hez?

Az Azure AD-ben az alkalmazások két ábrázolása létezik:

* [Alkalmazásobjektumok](app-objects-and-service-principals.md#application-object) – Bár vannak [kivételek,](#notes-and-exceptions)az alkalmazásobjektumok egy alkalmazás definíciójának tekinthetők.
* [Szolgáltatásnévi tagok](app-objects-and-service-principals.md#service-principal-object) – egy alkalmazás példányának tekinthető. A szolgáltatásnévi tagok általában egy alkalmazásobjektumra hivatkoznak, és egy alkalmazásobjektumra több szolgáltatásnév hivatkozhat a könyvtárak között.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Mik azok az alkalmazásobjektumok, és honnan származnak?

Az Azure Portalon az [alkalmazás-regisztrációk](https://aka.ms/appregistrations) felületén kezelheti az [alkalmazásobjektumokat.](app-objects-and-service-principals.md#application-object) Az alkalmazásobjektumok leírják az alkalmazást az Azure AD-nek, és az alkalmazás definíciójának tekinthetők, így a szolgáltatás a beállítások alapján megtudhatja, hogyan adhat ki jogkivonatokat az alkalmazásnak. Az alkalmazásobjektum csak akkor is létezik a kezdőkönyvtárban, ha ez egy több-bérlős alkalmazás, amely támogatja a szolgáltatásnévmás könyvtárakban. Az alkalmazás objektum a következők bármelyikét tartalmazhatja (valamint az itt nem említett további információkat):

* Név, embléma és közzétevő
* Uri-k átirányítása
* Titkos kulcsok (szimmetrikus és/vagy aszimmetrikus kulcsok az alkalmazás hitelesítéséhez)
* API-függőségek (OAuth)
* Közzétett API-k/erőforrások/hatókörök (OAuth)
* Alkalmazásszerepkörök (RBAC)
* SSO-metaadatok és konfiguráció
* A felhasználók metaadatait és konfigurációját létesíti
* Proxy metaadatai és konfigurációja

Az alkalmazásobjektumok több útvonalon keresztül hozhatók létre, többek között a következőkön:

* Alkalmazásregisztrációk az Azure Portalon
* Új alkalmazás létrehozása a Visual Studio használatával, és konfigurálása az Azure AD-hitelesítés használatára
* Amikor egy rendszergazda hozzáad egy alkalmazást az alkalmazásgyűjteményből (amely egy egyszerű szolgáltatást is létrehoz)
* Új alkalmazás létrehozása a Microsoft Graph API vagy a PowerShell használatával
* Sokan mások, beleértve a különböző fejlesztői élményt az Azure-ban és az API Explorer tapasztalatok között fejlesztői központok

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Mik azok a szolgáltatástagok és honnan származnak?

Az Azure Portalon az [Azure-alkalmazások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) szolgáltatáshasználatával kezelheti a [szolgáltatásnéveket.](app-objects-and-service-principals.md#service-principal-object) Az Azure AD-hez csatlakozó alkalmazást a szolgáltatásnévi tagok szabályozzák, és az alkalmazás példányának tekinthetők a címtárban. Bármely adott alkalmazás esetében legfeljebb egy alkalmazásobjektummal (amely egy "kezdő" címtárban van regisztrálva) és egy vagy több egyszerű szolgáltatásobjektummal rendelkezhet, amelyek az alkalmazás példányait képviselik minden olyan könyvtárban, amelyben jár. 

Az egyszerű szolgáltatás a következőket tartalmazhatja:

* Hivatkozás egy alkalmazásobjektumra az alkalmazásazonosító tulajdonságon keresztül
* Helyi felhasználói és csoportalkalmazás-szerepkör-hozzárendelések nyilvántartásai
* Az alkalmazásnak megadott helyi felhasználói és rendszergazdai engedélyek nyilvántartásai
  * Például: engedély az alkalmazás számára egy adott felhasználó e-mail címének eléréséhez
* Helyi házirendek, köztük feltételes hozzáférési házirend ek rekordjai
* Az alkalmazás alternatív helyi beállításainak rekordjai
  * Jogcímek átalakítási szabályai
  * Attribútumleképezések (felhasználói kiépítés)
  * Címtárspecifikus alkalmazásszerepkörök (ha az alkalmazás egyéni szerepköröket támogat)
  * Címtárspecifikus név vagy embléma

Az alkalmazásobjektumokhoz hasonlóan a szolgáltatásnévi tagok is létrehozhatók több útvonalon keresztül, többek között:

* Amikor a felhasználók bejelentkeznek egy azure AD-vel integrált, harmadik féltől származó alkalmazásba
  * A bejelentkezés során a felhasználóknak engedélyt kell adniuk az alkalmazásnak a profiljuk és más engedélyeik elérésére. Az első személy, aki beleegyezését adja, az alkalmazást képviselő egyszerű szolgáltatás hozzáadása a címtárhoz.
* Amikor a felhasználók bejelentkeznek a Microsoft online szolgáltatásaiba, például az [Office 365-be](https://products.office.com/)
  * Amikor előfizet az Office 365-re, vagy megkezdi a próbaverziót, egy vagy több egyszerű szolgáltatás jön létre a címtárban, amely az Office 365-höz kapcsolódó összes funkció nyújtásához használt különböző szolgáltatásokat képviseli.
  * Egyes Office 365-szolgáltatások, például a SharePoint folyamatosan létrehoznak egyszerű szolgáltatást, hogy lehetővé tegyék az összetevők, köztük a munkafolyamatok közötti biztonságos kommunikációt.
* Amikor egy rendszergazda hozzáad egy alkalmazást az alkalmazásgalériából (ez egy mögöttes alkalmazásobjektumot is létrehoz)
* Az [Azure AD-alkalmazásproxy](/azure/active-directory/manage-apps/application-proxy) használatához használt alkalmazás hozzáadása
* Az egyszeri bejelentkezéshez használt alkalmazás csatlakoztatása SAML vagy jelszó egyszeri bejelentkezéssel (Egyszeri bejelentkezés)
* Programozott módon a Microsoft Graph API-n vagy a PowerShellen keresztül

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Hogyan kapcsolódnak egymáshoz az alkalmazásobjektumok és a szolgáltatásnévtagok?

Egy alkalmazás egy alkalmazásobjektummal rendelkezik a kezdőkönyvtárában, amelyre egy vagy több szolgáltatásnév hivatkozik minden olyan könyvtárban, ahol működik (beleértve az alkalmazás kezdőkönyvtárát is).

![Az alkalmazásobjektumok és a szolgáltatásnévi tagok közötti kapcsolat megjelenítése][apps_service_principals_directory]

Az előző ábrán a Microsoft két belső könyvtárat tart fenn (a bal oldalon látható), amelyeket az alkalmazások közzétételére használ:

* Az egyik a Microsoft Apps (Microsoft-szolgáltatások könyvtár)
* Egy előre integrált, harmadik féltől származó alkalmazásokhoz (App gallery könyvtár)

Az Azure AD-vel integrálható alkalmazásközzétevőknek/szállítóknak rendelkezniük kell egy közzétételi könyvtárral (a jobb oldalon "Néhány SaaS-címtár" néven jelenik meg).

A saját maga által hozzáadott **(az ábrán alkalmazásként (a tiédként)** ábrázolt alkalmazások a következők:

* Az Ön által kifejlesztett alkalmazások (az Azure AD-vel integrálva)
* Az egyszeri bejelentkezéshez csatlakoztatott alkalmazások
* Az Azure AD alkalmazásproxyhasználatával közzétett alkalmazások

### <a name="notes-and-exceptions"></a>Megjegyzések és kivételek

* Nem minden szolgáltatásnév mutat vissza egy alkalmazásobjektumra. Amikor az Azure AD eredetileg épült, az alkalmazások számára nyújtott szolgáltatások korlátozottabbak voltak, és az egyszerű szolgáltatás elegendő volt az alkalmazásidentitás létrehozásához. Az eredeti egyszerű szolgáltatás közelebb állt a Windows Server Active Directory szolgáltatásfiókhoz. Emiatt továbbra is lehetőség van a szolgáltatásnévi mentelmi műveletek különböző útvonalakon keresztül, például az Azure AD PowerShell használatával, anélkül, hogy először egy alkalmazásobjektum létrehozása. A Microsoft Graph API-hoz egy alkalmazásobjektum szükséges az egyszerű szolgáltatás létrehozása előtt.
* A fent leírt információk közül nem mindegyik van programozott programozott módon elérhetővé. A következő csak a felhasználói felületen érhető el:
  * Jogcímek átalakítási szabályai
  * Attribútumleképezések (felhasználói kiépítés)
* Az egyszerű szolgáltatás és az alkalmazásobjektumokkal kapcsolatos részletesebb tudnivalókat a Microsoft Graph API referenciadokumentációjában találja:
  * [Alkalmazás](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0)
  * [Szolgáltatásnév](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Miért integrálhatók az alkalmazások az Azure AD-vel?

Az alkalmazások hozzáadódnak az Azure AD-hez, hogy kihasználhassanak egy vagy több általa nyújtott szolgáltatást, beleértve a következőket:

* Alkalmazás hitelesítése és engedélyezése
* Felhasználói hitelesítés és engedélyezés
* SSO összevonással vagy jelszóval
* Felhasználói kiépítés és szinkronizálás
* Szerepköralapú hozzáférés-vezérlés – A címtár használatával alkalmazásszerepköröket definiálaz alkalmazásalapú engedélyezési ellenőrzések végrehajtásához egy alkalmazásban
* OAuth engedélyezési szolgáltatások – Az Office 365 és más Microsoft-alkalmazások használják az API-khoz/erőforrásokhoz való hozzáférés engedélyezéséhez
* Alkalmazás közzététele és proxyja – Alkalmazás közzététele magánhálózatról az internetre

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Kinek van engedélye alkalmazások hozzáadására az Azure AD-példányhoz?

Bár vannak olyan feladatok, amelyeket csak a globális rendszergazdák végezhetnek el (például alkalmazásokhozzáadása az alkalmazásgalériából, és egy alkalmazás konfigurálása az alkalmazásproxy használatára) alapértelmezés szerint a címtár ban lévő összes felhasználónak joga van regisztrálni az okat fejlesztő alkalmazásobjektumokat, és belátása szerint, hogy mely alkalmazásokat osztják meg/adják meg a szervezeti adataikhoz hozzájárulással. Ha egy személy az első felhasználó a címtárban, aki bejelentkezik egy alkalmazásba, és megadja a jóváhagyást, amely létrehoz egy egyszerű szolgáltatás a bérlőben; ellenkező esetben a hozzájárulási engedély információit a meglévő egyszerű szolgáltatás tárolja.

Az alkalmazások regisztrálásának és beleegyezésének engedélyezése kezdetben megalapozott lehet, de a következőket szem előtt tartva:


* Az alkalmazások évek óta használhatják a Windows Server Active Directoryt a felhasználói hitelesítéshez anélkül, hogy az alkalmazást regisztrálni vagy rögzíteni kellene a címtárban. Most a szervezet jobb láthatóságot, hogy pontosan hány alkalmazás használja a könyvtárat, és milyen célra.
* Ezeknek a feladatoknak a felhasználókra történő delegálása szükségtelenné teszi a rendszergazda által vezérelt alkalmazásregisztrációs és közzétételi folyamatot. Az Active Directory összevonási szolgáltatások (ADFS) esetében valószínű volt, hogy egy rendszergazdának függő entitásként kellett hozzáadnia egy alkalmazást a fejlesztők nevében. Most a fejlesztők önkiszolgáló.
* A szervezetfiókjaikkal üzleti célokra bejelentkező felhasználók jó dolog. Ha ezt követően elhagyják a szervezetet, automatikusan elveszítik a hozzáférést a fiókjukhoz az általuk használt alkalmazásban.
* Miután a rekord, hogy milyen adatokat osztottak meg, amely alkalmazás egy jó dolog. Az adatok minden eddiginél hordozhatóbbak, és hasznos, ha egyértelműen rögzítik, hogy ki milyen adatokat osztott meg mely alkalmazásokkal.
* Az Azure-tulajdonosok, akik az Azure AD oauth-hoz dönti el, hogy pontosan milyen engedélyeket adhatnak a felhasználók az alkalmazásoknak, és mely engedélyekhez szükséges egy rendszergazda beleegyezése. Csak a rendszergazdák járulhatnak hozzá a nagyobb hatókörök és jelentősebb engedélyek, míg a felhasználói hozzájárulás hatóköre a felhasználók saját adatait és képességeit.
* Amikor egy felhasználó hozzáadja vagy engedélyezi egy alkalmazás számára az adataik elérését, az esemény naplózható, így megtekintheti a naplózási jelentéseket az Azure Portalon, hogy megállapíthassa, hogyan lett hozzáadva egy alkalmazás a címtárhoz.

Ha továbbra is meg szeretné akadályozni, hogy a címtár felhasználói alkalmazásokat regisztráljanak, és rendszergazdai jóváhagyás nélkül jelentkezzenek be az alkalmazásokba, két beállítás módosítható a képességek kikapcsolásához:

* Annak megakadályozása, hogy a felhasználók saját nevükben járuljanak hozzá az alkalmazásokhoz:
  1. Az Azure Portalon nyissa meg a [Felhasználói beállítások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) szakasz a Vállalati alkalmazások csoportban.
  2. A **Módosítás A felhasználók beleegyezhetnek abba, hogy a vállalati adatokhoz a nevükben hozzáférő alkalmazások** **száma.**
     
     > [!NOTE]
     > Ha úgy dönt, hogy kikapcsolja a felhasználó beleegyezését, a rendszergazdának hozzá kell járulnia minden olyan új alkalmazáshoz, amelyet a felhasználónak használnia kell.

* Annak megakadályozása, hogy a felhasználók regisztrálják saját alkalmazásaikat:
  1. Az Azure Portalon nyissa meg a [Felhasználói beállítások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) szakaszt az Azure Active Directory ban.
  2. A Felhasználók módosítása az alkalmazásokat **nem**számra **regisztrálhatja.**

> [!NOTE]
> Maga a Microsoft használja az alapértelmezett konfigurációt azokkal a felhasználókkal, akik regisztrálhatják az alkalmazásokat, és saját nevükben járulhatnak hozzá az alkalmazásokhoz.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
