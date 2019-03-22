---
title: Hogyan és miért alkalmazások felvétele az Azure Active Directory
description: Mit jelent az Azure AD hozzá az alkalmazáshoz, és hogyan azok be van?
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: elisol, lenalepa
ms.collection: M365-identity-device-management
ms.openlocfilehash: 257f7b66163b72141ceb6405768e912a263fb14b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124218"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Hogyan és miért érdemes az Azure AD-alkalmazások felvétele

Nincsenek alkalmazások két ábrázolása az Azure ad-ben: 
* [Alkalmazásobjektumok](app-objects-and-service-principals.md#application-object) – Bár vannak [kivételek](#notes-and-exceptions), alkalmazásobjektumok alkalmazás definíciójának lehessen venni.
* [Egyszerű szolgáltatások](app-objects-and-service-principals.md#service-principal-object) -lehessen venni egy alkalmazás egy példányát. Szolgáltatásnevek általában egy alkalmazásobjektumot hivatkoznak, és a egy alkalmazásobjektumot hivatkozhat több szolgáltatásnevek címtárak között.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Mi alkalmazásobjektumok és honnan azok származnak?
Kezelheti [alkalmazásobjektumok](app-objects-and-service-principals.md#application-object) keresztül az Azure Portalon a [Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade) tapasztalható. Alkalmazásobjektumok az alkalmazást az Azure AD ismertetik, és az alkalmazás lehetővé teszi a szolgáltatást, hogy ismerjék a jogkivonatok kiadása az alkalmazásnak a beállításai alapján a definíció lehessen venni. Az alkalmazásobjektum fogja csak szerepel a kezdőkönyvtárba kerül, akkor is, ha egy több-bérlős alkalmazás egyszerű szolgáltatásokat támogató más címtárakban. Az alkalmazásobjektum tartalmazhatja a következő (, valamint további információkat itt nem említett):
* Név, embléma és közzétevő
* Válasz URL-címek
* Titkos kulcsok (szimmetrikus és/vagy aszimmetrikus kulcsokkal az alkalmazás hitelesítéséhez használt)
* API-függőségek (OAuth)
* Közzétett API-k/erőforrás/hatókörök (OAuth)
* Alkalmazás-szerepkörök (RBAC)
* Egyszeri bejelentkezés metaadatai és
* Felhasználókiépítés az metaadatai és
* Proxy metaadatai és

Alkalmazásobjektumok több utat, beleértve a keresztül hozhatók létre:
* Az Azure Portalon alkalmazásregisztráció
* Új alkalmazás létrehozása Visual studióval, majd konfigurálni az Azure AD-hitelesítés használatára
* Amikor egy rendszergazda ad hozzá egy alkalmazás (amely fog is egy egyszerű szolgáltatás létrehozása) alkalmazás-katalógusból
* Hozzon létre egy új alkalmazást a Microsoft Graph API-val, az Azure AD Graph API vagy a PowerShell használatával
* Sok más Azure-ban, és az API-explorer élményt többek között különböző fejlesztői környezetek között fejlesztői központok

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Mi a szolgáltatásnevek és honnan azok származnak?
Kezelheti [egyszerű szolgáltatások](app-objects-and-service-principals.md#service-principal-object) keresztül az Azure Portalon a [vállalati alkalmazások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) tapasztalható. Szolgáltatásnevek mi szabályozzák, hogy egy alkalmazás csatlakozik az Azure ad-ben, és az alkalmazás címtárban példánya tekinthető. Bármely adott alkalmazáshoz legfeljebb egy alkalmazás objektum ("" Kezdőkönyvtár regisztrálva van) és a egy vagy több egyszerű szolgáltatási objektumok minden címtárat, amelyben funkcionál az alkalmazás egy példányát képviselő is rendelkezhetnek. 

A szolgáltatásnév a következők lehetnek:

* Egy hivatkozást egy alkalmazásobjektumot az application ID tulajdonság keresztül
* Rekordok helyi felhasználó és csoport alkalmazás szerepkör-hozzárendelések
* Az alkalmazáshoz rendelt helyi felhasználói és rendszergazdai engedély rekordok
  * Például: az alkalmazás egy adott felhasználó e-mail hozzáférését engedély
* Rögzíti, beleértve a feltételes hozzáférési szabályzat helyi házirendek
* Rekordok alternatív helyi alkalmazás beállítása
  * Jogcím-átalakítási szabályok
  * (A felhasználók átadásának) attribútum-leképezéshez
  * Címtár-specifikus alkalmazás-szerepkörök (ha az alkalmazás támogatja az egyéni szerepkörök)
  * Címtár-specifikus nevére vagy emblémájára

Alkalmazás objektumok, például az egyszerű szolgáltatások is több útvonallal, beleértve a keresztül hozhatók létre:

* Az Azure AD-vel integrált amikor bejelentkeznek egy külső alkalmazás
  * Során bejelentkezéskor a rendszer kéri a felhasználóktól engedélyt az alkalmazás hozzáférését, hogy a profil, és más engedélyt adni. Az első, aki adja meg beleegyezését, amely jelöli az alkalmazást a könyvtárhoz hozzáadandó szolgáltatásnév okoz.
* Amikor a felhasználók bejelentkeznek a Microsoft online szolgáltatásaihoz, például [Office 365-höz](https://products.office.com/)
  * Amikor az Office 365 szolgáltatásra vagy egy próba megkezdéséhez, egy vagy több szolgáltatásnevek jönnek létre a címtárban, a különböző szolgáltatások, amelyek az összes Office 365-höz tartozó funkciók biztosításához jelölő.
  * Egyes Office 365-szolgáltatásokhoz, például a SharePoint-összetevők többek között a munkafolyamatok közötti biztonságos kommunikációhoz töltheti egyszerű szolgáltatások létrehozása.
* Amikor egy rendszergazda ad hozzá egy alkalmazás (ezzel is létrehoz egy alapul szolgáló alkalmazás objektum) alkalmazás-galériából
* Az alkalmazás hozzáadása a [Azure AD-alkalmazásproxy](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Csatlakozás az alkalmazás egyszeri bejelentkezési SAML vagy jelszót az egyszeri bejelentkezés (SSO)
* Programozott módon az Azure AD Graph API vagy a Powershellen keresztül

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Hogyan alkalmazásobjektumok és egyszerű szolgáltatások kapcsolódnak egymáshoz?
Egy alkalmazás egy objektum egy vagy több szolgáltatásnevek minden azokat a címtárakat, ahol működik (beleértve az alkalmazás kezdőkönyvtár) által hivatkozott kezdőkönyvtárral rendelkezik.
![Egy diagram, így bemutatja, hogyan alkalmazásobjektumok és egyszerű szolgáltatások kommunikáljanak egymással és az Azure AD-példányban.][apps_service_principals_directory]

A fenti ábrán a Microsoft fenntart két címtár belsőleg (lásd a bal oldalon), hogy az alkalmazás-közzétételhez használja:

* Egy a Microsoft Apps (Microsoft-szolgáltatások könyvtár)
* Egy előre integrált külső alkalmazásokhoz (alkalmazás katalógus könyvtár)

Alkalmazás közzétevők/szállítók és az Azure AD integrálása egy közzétételi könyvtárat ("Egyes SaaS-Directory", a jobb oldalon látható) van szüksége.

Az alkalmazásokat, amelyek adja hozzá saját magát (-kiszolgálókként **(az Ön szervezetébe) alkalmazás** a diagram) tartalmazza:

* (Az Azure ad-vel integrált) fejlesztett alkalmazások
* A csatlakoztatott alkalmazások single-sign-on
* Alkalmazások közzététele az Azure AD-alkalmazásproxy használatával

### <a name="notes-and-exceptions"></a>Megjegyzések és kivételek
* Nem minden szolgáltatásnevek mutathat vissza egy alkalmazásobjektumot. Elkészítésekor az Azure AD eredetileg alkalmazásokhoz nyújtott szolgáltatások korlátozottabbak voltak, és az egyszerű szolgáltatás létrehozásához szükséges Alkalmazásidentitás elegendő volt. Az eredeti egyszerű szolgáltatás minősége közelebb a Windows Server Active Directory-szolgáltatásfiók lett. Ezért fontos továbbra is létre lehet hozni az egyszerű szolgáltatások segítségével különböző utakat, például az Azure AD PowerShell használatával egy alkalmazásobjektumot létrehozása nélkül. Az Azure AD Graph API egyszerű szolgáltatás létrehozása előtt egy objektum szükséges.
* Nem az összes fent leírt adatokat jelenleg közvetlenül programozott módon. Az alábbiakban csak a felhasználói felületen érhető el:
  * Jogcím-átalakítási szabályok
  * (A felhasználók átadásának) attribútum-leképezéshez
* Az egyszerű szolgáltatás és alkalmazás objektumának további részletes információkért lásd: az Azure AD Graph REST API referenciadokumentációt tartalmaz:
  * [Alkalmazás](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)
  * [Egyszerű szolgáltatás](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Alkalmazások miért tegye integrálása az Azure ad-vel?
Alkalmazások felvétele az Azure ad-ben kihasználhatja a biztosít, beleértve a szolgáltatások valamelyikét:

* Alkalmazás hitelesítése és engedélyezése
* Felhasználói hitelesítés és engedélyezés
* SSO-t az összevonáshoz, vagy a jelszó
* Felhasználókiépítés és -szinkronizálás
* Szerepköralapú hozzáférés-vezérlés – a könyvtár használatával határozza meg az alkalmazás-szerepkörök, szerepkör-alapú hitelesítést végrehajtani ellenőrzi egy alkalmazásban
* OAuth - engedélyezési API-k/erőforrásokhoz való hozzáférés engedélyezésére az Office 365-höz és más Microsoft-alkalmazások által használt szolgáltatások
* Alkalmazás közzététele és a proxy - közzétenni egy alkalmazást az interneten egy privát hálózatról

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Kinek van engedélye alkalmazásokat a saját Azure AD-példányt?
Bár vannak bizonyos feladatokat, hogy csak a globális rendszergazdák teheti meg (például alkalmazások hozzáadása az app-katalógusából, és a Proxy használatát egy alkalmazás konfigurálása) alapértelmezés szerint a címtár összes felhasználója jogosult alkalmazás regisztrálása objektumok fejlesztés és a saját belátása szerint értékeli mely alkalmazások azok megosztás/adjon hozzáférést a hozzájárulási szervezeti adatait az. Ha egy személy jelentkezni egy alkalmazásba, és hozzájárulás megadása az első felhasználót a címtárban, egy egyszerű szolgáltatást, amely hoz létre a bérlőben; Ellenkező esetben a hozzájárulás megadása adatokat fog tárolni a meglévő egyszerű szolgáltatást.

Így a felhasználók regisztrálása és járul hozzá az alkalmazások előfordulhat, hogy kezdetben eredményes vonatkozó, de vegye figyelembe a következőket:


* Alkalmazások kihasználhatja a Windows Server Active Directory felhasználói hitelesítés sok éve anélkül, hogy az alkalmazás regisztrálva, vagy a könyvtárban rögzített törölték. Mostantól a szervezet fog rendelkezik átláthatóbb alkalmazások pontosan használja a könyvtárban, és milyen célra.
* Egy rendszergazda által készített alkalmazás regisztrálása és a közzétételi folyamat van szükség a felhasználók számára ezen feladatok delegálása ellentettjét adja. Az Active Directory összevonási szolgáltatások (ADFS) volt valószínű, hogy egy rendszergazda hozzáadása egy függő entitás fejlesztő nevében egy alkalmazás volt-e. Most már a fejlesztők számára önkiszolgáló is.
* Bejelentkezés szervezeti fiókjukkal üzleti célú alkalmazást a felhasználók azért hasznos. Ha később elhagyja a szervezetet-fiókjához az alkalmazásban, mintha automatikusan elveszítik a hozzáférést.
* Egy rekord, milyen adatokat megosztották, amelyekkel alkalmazást azért hasznos kellene. Adatok több hordozható, mint valaha és hasznos tartozik, mely alkalmazások milyen adatokat aki megosztva egyértelmű rekord.
* API-tulajdonosok, akik használni az Azure AD OAuth döntse el, pontosan milyen engedélyek felhasználók számára az alkalmazások, és milyen engedélyeket igényel egy rendszergazdát, hogy fogadja el. Csak a rendszergazdák engedélyezhetik, hogy nagyobb hatókörök és jelentősebb engedélyeket, amíg a felhasználók saját adatok és a képességek felhasználói beleegyezés hatókörét.
* Amikor egy felhasználó hozzáad, vagy lehetővé teszi az adatok elérését, az esemény naplózható, így az auditálási jelentéseket egy alkalmazás hogyan lett felvéve a címtárba meghatározásához az Azure Portalon is megtekintheti.

Továbbra is szeretné, hogy a felhasználók a címtárban, alkalmazások regisztrációjának és bejelentkezésének rendszergazdai jóváhagyás nélküli alkalmazások, van-e két beállítás, amelyet módosíthat, tiltsa le ezeket a képességeket:

* Megakadályozza, hogy a felhasználók saját maguk alkalmazások jóváhagyják:
  1. Az Azure Portalon nyissa meg a [felhasználói beállítások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) a vállalati alkalmazások szakaszában.
  2. Változás **felhasználók engedélyezhetik, hogy az alkalmazások hozzáférjenek a céges adatok saját nevükben való** való **nem**. 
     
     > [!NOTE]
     > Ha úgy dönt, hogy kapcsolja ki a felhasználói beleegyezés, egy rendszergazda lesz szükséges, hogy engedélyt adjanak az minden olyan új alkalmazás a felhasználó kell használnia.    
* Megakadályozza, hogy a felhasználók a saját alkalmazások regisztrálása:
  1. Az Azure Portalon nyissa meg a [felhasználói beállítások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) szakaszban az Azure Active Directory
  2. Változás **felhasználók regisztrálhatnak alkalmazásokat** való **nem**.

> [!NOTE]
> Csak a Microsoft-alkalmazások regisztrálását és járul hozzá az alkalmazások saját maguk a felhasználók az alapértelmezett konfigurációt használ.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg

