---
title: Útmutató és érvek alkalmazások felvétele az Azure Active Directoryban
description: Mit jelent az Azure AD hozzáadni az alkalmazáshoz, és hogyan azok be van?
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: elisol, lenalepa
ms.openlocfilehash: 5c8ae9534e79b8dc801262f85d8a007e050f4da7
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316959"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Miért és hogyan lehet az Azure AD-alkalmazások felvétele

Nincsenek alkalmazások két ábrázolásai Azure AD-ben: 
* [Alkalmazás objektumának](active-directory-application-objects.md#application-object) – Bár vannak [kivételek](#notes-and-exceptions), alkalmazás objektumának tekinthető alkalmazás definícióját.
* [Rendszerbiztonsági tagok szolgáltatás](active-directory-application-objects.md#service-principal-object) -alkalmazás egy példánya tekinthető. Szolgáltatásnevekről általában alkalmazásobjektum hivatkoznak, és egy alkalmazás objektumának hivatkozhat több szolgáltatásnevekről könyvtárak között.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Mik azok a alkalmazásobjektumok és honnan azok származnak?
Kezelheti a [alkalmazás objektumának](active-directory-application-objects.md#application-object) az Azure portálon keresztül a [App regisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade) tapasztalhat. Alkalmazás objektumának írják le az Azure AD-alkalmazás, és az alkalmazás, így a szolgáltatást, hogy tudja, hogyan kell jogkivonatokat kibocsátani, az alkalmazásnak a beállításai alapján a definíció tekinthetők. Az alkalmazási objektum csak jelen saját címtárukkal, még akkor is, ha olyan több-bérlős alkalmazás szolgáltatásnevekről támogató más címtárakban. Az application objektum tartozhat (, valamint további információkat itt nem említett) az alábbiak valamelyikét:
* Név, embléma és szoftvergyártó
* Válasz URL-címek
* Titkos kulcsokat (szimmetrikus vagy aszimmetrikus kulcsok az alkalmazás hitelesítéséhez használt)
* API-függőségek (OAuth)
* API-k/erőforrások/hatókörök közzétett (OAuth)
* Alkalmazás-szerepkörök (RBAC)
* Egyszeri bejelentkezés metaadatai és
* Felhasználók átadásához metaadatok és a konfiguráció
* Proxy metaadatai és

Alkalmazás objektumok több utat, beleértve a keresztül hozhatók létre:
* Alkalmazás regisztráció az Azure-portálon
* Egy új alkalmazás létrehozása a Visual Studio segítségével, és konfigurálni kell az Azure AD-hitelesítés használatára
* Ha egy rendszergazda ad hozzá egy alkalmazás (amely is létrehoz egy szolgáltatást egyszerű) alkalmazás-galériából
* Hozzon létre egy új alkalmazást a Microsoft Graph API-val, az Azure AD Graph API vagy a PowerShell használatával
* Sok más beleértve a különböző fejlesztői lép az Azure-ban és a API explorer elhatározásuk között fejlesztői erőforrások

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Mik azok a szolgáltatásnevekről és honnan azok származnak?
Kezelheti a [szolgáltatás rendszerbiztonsági tagok](active-directory-application-objects.md#service-principal-object) az Azure portálon keresztül a [vállalati alkalmazások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) tapasztalhat. Szolgáltatásnevekről mi szabályozására, hogy az alkalmazás csatlakoztatása az Azure AD és az alkalmazás a címtárban példányának tekinthető. Bármely adott alkalmazás egyszerre legfeljebb egy alkalmazás objektumának (amely a "saját" könyvtárban található regisztrálva) és egy vagy több szolgáltatás egyszerű objektumok az alkalmazás minden könyvtárban úgy példányát képviselő is rendelkezhetnek. 

A szolgáltatás egyszerű az alábbiakból állhat:

* Vissza az alkalmazás-azonosító tulajdonsággal keresztül alkalmazásobjektum mutató hivatkozás
* Helyi felhasználó és csoport alkalmazás-szerepkör-hozzárendelések
* Az alkalmazás számára megadott helyi felhasználói és rendszergazdai engedélyeket a rekordok
  * Példa: egy felhasználó e-mailjei eléréséhez alkalmazására vonatkozó engedély
* Rögzíti, beleértve a feltételes hozzáférési házirend helyi házirendek
* Az alkalmazás alternatív helyi beállítások rekordok
  * Jogcím átalakítási szabályok:
  * (A felhasználók átadása) attribútum-leképezésekhez
  * Címtár-specifikus alkalmazás szerepkörök (ha az alkalmazás támogatja az egyéni szerepkörök)
  * Címtár-specifikus nevét vagy embléma

Alkalmazás-objektumok, például szolgáltatásnevekről is beleértve több útvonallal keresztül hozhatók létre:

* Amikor a felhasználók bejelentkeznek egy külső alkalmazás integrált Azure AD-val
  * Bejelentkezéskor a felhasználó felkérést adhat engedélyt a profilját eléréséhez alkalmazására és egyéb engedélyeket. Az első személy a azokat egy szolgáltatásnevet, az alkalmazás a könyvtárhoz hozzáadandó jelölő okoz.
* Amikor a felhasználók bejelentkeznek a Microsoft online Services-szolgáltatásokkal, például [Office 365](http://products.office.com/)
  * Előfizetnie az Office 365 vagy egy próbaverzióra kezdődik, amikor egy vagy több szolgáltatás rendszerbiztonsági tagok jönnek létre a könyvtárban, a különböző szolgáltatások, amelyek az összes Office 365 társított funkcióját biztosításához használt képviselő.
  * Néhány Office 365-szolgáltatásokhoz, például a SharePoint szolgáltatás rendszerbiztonsági tagok létrehozása folyamatosan összetevők többek között a munkafolyamatok közötti biztonságos kommunikációhoz.
* Ha egy rendszergazda ad hozzá egy alkalmazás (ezzel is létrehoz egy alapul szolgáló app objektum) alkalmazás-galériából
* Az alkalmazás hozzáadása a [az Azure AD-alkalmazásproxy](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Egyszeri bejelentkezéshez alkalmazás csatlakoztatása a SAML-alapú vagy jelszót az egyszeri bejelentkezés (SSO)
* Programozott módon az Azure AD Graph API vagy a PowerShell használatával

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Hogyan alkalmazásobjektumok és szolgáltatásnevekről kapcsolódnak egymáshoz?
Az alkalmazás a saját címtárukkal egy vagy több szolgáltatásnevekről minden a könyvtárakhoz, ahol működik (beleértve az alkalmazás kezdőkönyvtár) által hivatkozott egy alkalmazás-objektummal rendelkezik.
![Hogyan működnek együtt a alkalmazásobjektumok és szolgáltatásnevekről egymáshoz, és az Azure AD-példányban ábrázoló diagram.][apps_service_principals_directory]

A fenti ábrán a Microsoft fenntartja a könyvtárak két belső (lásd a bal oldalon), hogy alkalmazás-közzététel használja:

* Egy a Microsoft Apps (Microsoft-szolgáltatások könyvtára)
* Egy előre integrált külső alkalmazások (App gyűjtemény könyvtár)

Alkalmazás közzétevők/szállítók, akik az Azure ad-vel integrálni kell lennie egy közzétételi könyvtár ("Some SaaS Directory", a jobb oldalon látható).

Felvenni magát alkalmazások (-ként **(saját) alkalmazás** a diagramban) tartalmazza:

* Ön által fejlesztett alkalmazások (az Azure ad-vel integrált)
* A csatlakoztatott alkalmazások single-sign-on
* Alkalmazások közzététele az Azure AD-alkalmazásproxy használatával

### <a name="notes-and-exceptions"></a>Megjegyzések és kivételek
* Nem minden szolgáltatás rendszerbiztonsági tagok vissza egy alkalmazás objektumra mutat. Ha az Azure AD eredetileg készült alkalmazások nyújtott szolgáltatások korlátozottabb és a szolgáltatás egyszerű volt elegendő ahhoz, hogy az Alkalmazásidentitás létrehozó. Az eredeti szolgáltatásnév lett alakzat közelebb a Windows Server Active Directory-szolgáltatásfiókot. Emiatt az továbbra is létre lehet hozni szolgáltatásnevekről keresztül különböző utat, például az Azure AD PowerShell, alkalmazásobjektum létrehozása nélkül. Az Azure AD Graph API egyszerű szolgáltatás létrehozása előtt egy objektum szükséges.
* A fent megnevezett információkat nem az összes jelenleg kommunikál programozott módon. A következő csak érhetők el a felhasználói felületen:
  * Jogcím átalakítási szabályok:
  * (A felhasználók átadása) attribútum-leképezésekhez
* Az egyszerű szolgáltatásnév és az alkalmazás objektumának további részletes információkért lásd: az Azure AD Graph REST API referenciadokumentációt tartalmaz:
  * [Alkalmazás](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)
  * [Egyszerű szolgáltatásnév](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Miért hajtsa végre az Azure AD integrálása alkalmazások?
Alkalmazások felvétele az Azure AD ki egy vagy több szolgáltatást nyújt, beleértve:

* Alkalmazás-hitelesítés és engedélyezés
* Felhasználói hitelesítés és engedélyezés
* SSO-t az összevonáshoz, vagy a jelszó
* A felhasználók átadása és szinkronizálás
* Szerepköralapú hozzáférés-vezérlés - alkalmazási szerepköröknek szerepköralapú hitelesítés végrehajtásához adja meg a könyvtárat használja ellenőrzi az alkalmazásban
* OAuth - engedélyezési API-k/erőforrásokhoz való hozzáférés engedélyezésére Office 365 és más Microsoft-alkalmazások által használt szolgáltatások
* Alkalmazás közzététele és a proxy - és az internet egy privát hálózatról alkalmazás közzététele

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Ki jogosult az Azure AD-példányban alkalmazások hozzáadása?
Miközben néhány feladatot, hogy csak globális rendszergazdák teheti meg (például alkalmazások hozzáadása az app-galériából, és az alkalmazás a Proxy konfigurálása) alapértelmezés szerint a címtárban szereplő összes felhasználó jogosult alkalmazás regisztrálása objektumokat, amelyek azok a fejlesztéséhez és megítélése keresztül mely alkalmazások azok megosztás/adjon hozzáférést a hozzájárulási keresztül gyűjtött szervezeti adatokhoz. Ha egy személy alkalmazáshoz való bejelentkezéshez, és a hozzájárulás az első felhasználó a címtárban, egy egyszerű szolgáltatást, amely hoz létre a bérlőben; Ellenkező esetben a hozzájárulási grant adatokat fog tárolni a meglévő egyszerű szolgáltatást.

Így a felhasználók regisztrálása és alkalmazások elképzelhető, hogy kezdetben hang vonatkozó hozzájárul, de vegye figyelembe a következőket:


* Alkalmazások volt kihasználhatják a Windows Server Active Directory a felhasználók hitelesítéséhez sok éve anélkül, hogy az alkalmazás regisztrálva vagy a könyvtárban tárolja. Mostantól a szervezet fog javult pontosan hány alkalmazást használ a könyvtárban, és milyen célból látható.
* Egy rendszergazda adatvezérelt regisztrációja és a közzétételi folyamat szükséges jogokat delegálni, ezek a jogosultságok, a felhasználók számára ellentettjét adja. Az Active Directory összevonási szolgáltatások (ADFS) volt valószínű, hogy egy rendszergazda hozzáadhat egy alkalmazást, a függő entitások nevében a fejlesztő volt-e. A fejlesztők mostantól önkiszolgáló is.
* Alkalmazások üzleti célra használja a szervezet fiókok bejelentkező felhasználók azért hasznos. Ha ezt követően elhagyják a szervezet, azok automatikusan megszűnik a hozzáférése az alkalmazásban melyikét használta éppen a fiókjára.
* Feljegyzés az adatok megosztásának időpontját, amellyel alkalmazás azért hasznos rendelkezik. Adatok több hordozható eddiginél és hasznos azt ki milyen adatokat osztanak mely alkalmazások törlése tárolja.
* API tulajdonosai, akik az Azure AD használja OAuth döntse el, pontosan milyen engedélyekkel a felhasználók csak alkalmazások megadása, és jogosultságokat igényel egy rendszergazdát, hogy elfogadja. Csak rendszergazdák is hozzájárul nagyobb hatókörök és jelentősebb engedélyek, amíg a felhasználók saját adatok és a képességek felhasználói hozzájárulás hatóköre.
* Ha a felhasználó ad hozzá, vagy lehetővé teszi, hogy egy alkalmazás számára az adatok elérését, az esemény naplózható, így megtekintheti a naplózási jelentések az Azure-portálon belül határozza meg, hogyan alkalmazás lett felvéve a címtárba.

Ha továbbra is szeretné akadályozni, hogy a címtárban szereplő felhasználó alkalmazás regisztrálása és alkalmazások rendszergazdai jóváhagyás nélkül próbál bejelentkezni, van két olyan beállítása, hogy ezekre a képességekre kikapcsolása módosíthatja:

* Megakadályozhatja, hogy a felhasználók a hozzájárul ahhoz, hogy saját maguk alkalmazások:
  1. Az Azure-portálon lépjen a [felhasználói beállítások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) a vállalati alkalmazások szakaszában.
  2. Változás **felhasználók is hozzájárul a nevében a vállalati adatokhoz hozzáférő alkalmazásokat** való **nem**. 
      > [!NOTE]
      > Ha úgy dönt, hogy kapcsolja ki a felhasználói hozzájárulás, hogy egy rendszergazda lesz szükség, bármely új alkalmazás használatára van szüksége a felhasználó beleegyezését.    
* Megakadályozhatja, hogy a felhasználók a saját alkalmazások regisztrálása:
  1. Az Azure-portálon lépjen a [felhasználói beállítások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) Azure Active Directory szakasza
  2. Változás **a felhasználók regisztrálhatják az alkalmazásokat** való **nem**.

> [!NOTE]
> Maga a Microsoft képes alkalmazások regisztrálni, és hozzájárul az alkalmazások saját maguk a felhasználók az alapértelmezett konfigurációt használ.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg

