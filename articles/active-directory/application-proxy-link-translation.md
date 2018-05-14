---
title: Hivatkozások és az URL-címek Azure AD alkalmazás Proxy fordítása |} Microsoft Docs
description: Alapvető tudnivalók az Azure AD-alkalmazásproxy összekötők ismerteti.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: a96296e8918c658f1dc3ebeadf01bd064e80716a
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Az Azure AD alkalmazásproxy közzétett alkalmazásokhoz szoftveresen kötött hivatkozások átirányítása

Az Azure AD-alkalmazásproxy elérhetővé teszi a helyszíni alkalmazások a távoli felhasználók vagy a saját eszközeiket. Egyes alkalmazások esetében azonban fejlesztett helyi a beágyazott HTML-hivatkozásokat tartalmazó. Ezek a hivatkozások nem működnek megfelelően az alkalmazás távolról használatakor. Ha több, egymástól mutasson a helyszíni alkalmazások, a felhasználók várható tovább dolgozik, ha azok nem az irodában a hivatkozásokat. 

Győződjön meg arról, hogy hivatkozások azonos belüli és a vállalati hálózaton kívüli működik a legjobban a külső URL-címeket az alkalmazások azonos a belső URL-címek konfigurálásához. Használjon [egyéni tartományok](manage-apps/application-proxy-configure-custom-domain.md) a külső URL-címeket kell rendelkeznie a vállalati tartomány neve helyett az alapértelmezett alkalmazástartomány-proxy konfigurálása.


Ha a bérlő nem használhat egyéni tartományok, többféle módon más biztosítani ezt a funkciót. Ezek mindegyike is kompatibilis egyéni tartományok és egymással, így megadhatja az egyéni tartományok és az egyéb megoldások, ha szükséges. 

**1. lehetőség: A Managed Browser használata** – Ez a megoldás csak akkor alkalmazható, ha azt tervezi, javasoljuk, vagy hogy felhasználók érhetik el az alkalmazást az Intune Managed Browser keresztül. Az összes közzétett URL-címet fogja kezelni. 

**2. lehetőség: A MyApps a kiterjesztést használni** – Ez a megoldás megköveteli a felhasználóktól egy ügyféloldali bővítmény telepítéséhez, de az összes közzétett URL-címek és működik a legnépszerűbb böngészőben fogja kezelni. 

**3. lehetőség: Használja a kapcsolat fordítási beállítás** – Ez az egy rendszergazda ügyféloldali beállítást, amely nem látható a felhasználók számára. Azt azonban csak kezelnek HTML- és CSS URL-címeit. Kódolt belső URL-címek Javascript során generált (például) nem fog működni.  

Ezek a funkciók tartsa meg a hivatkozásokat, függetlenül attól, hol találhatók a felhasználók működik. Ha alkalmazásokat, majd közvetlenül a belső végpontok vagy a portok, a közzétett alkalmazás külső Proxy URL belső URL-is leképezheti. 

 
> [!NOTE]
> Az utolsó lehetőség van csak a bérlői, valamilyen okból nem használható egyéni tartományok azonos belső és külső URL-címéből alkalmazások rendelkeznek. Ez a funkció engedélyezése előtt tekintse meg, ha [egyéni tartományok az Azure AD alkalmazásproxy](manage-apps/application-proxy-configure-custom-domain.md) akkor is képes működni. 

>Vagy, ha az alkalmazás kell konfigurálnia a hivatkozással fordítási SharePoint című [másodlagos címek leképezése a SharePoint 2013 rendszerhez konfigurálása](https://technet.microsoft.com/library/cc263208.aspx) leképezési hivatkozások egy másik módjáról. 

 
### <a name="option-1-intune-managed-browser-integration"></a>1. lehetőség: Intune felügyelt böngészővel integráció 

Az Intune Managed Browser segítségével fokozott védelme érdekében az alkalmazások és a tartalom. Ez a megoldás használatához meg kell szükséges vagy ajánlott a felhasználók hozzáférést keresztül az Intune Managed Browser alkalmazás. Összes belső URL-cím az alkalmazásproxy közzétett ismeri fel a Managed Browser lesz, és a rendszer átirányítja a megfelelő külső URL-CÍMÉT. Ez biztosítja, hogy működik az a kódolt belső URL-címet, és ha a felhasználó a böngésző kerül, és közvetlenül meg kell adnia a belső URL-címet, akkor is, ha a felhasználó távoli működik.  

További, többek között konfigurálhatja ezt a beállítást, olvassa el a [Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser) dokumentációját.  

### <a name="option-2-myapps-browser-extension"></a>2. lehetőség: MyApps bővítmény 

A MyApps bővítmény, az összes belső URL-cím az alkalmazásproxy közzétett ismeri fel a bővítmény és átirányítja a megfelelő külső URL-CÍMÉT. Ez biztosítja, hogy működik az a kódolt belső URL-címet, és ha a felhasználó a böngésző címsorában kerül, és közvetlenül meg kell adnia a belső URL-címet, akkor is, ha a felhasználó távoli működik.  

Ez a funkció használatához a felhasználónak van szüksége, töltse le a bővítményt, és vannak, naplózva. Nincs szükség a rendszergazdák és a felhasználók más konfiguráció. 

 

### <a name="option-3-link-translation-setting"></a>3. lehetőség: Hivatkozás fordítási beállítás 

Hivatkozás fordítási engedélyezésekor a rendszer az alkalmazásproxy keresztül HTML- és CSS keres közzétett belső hivatkozásokat, és a következőkből fordítja le azokat, így a felhasználók folyamatos élményt. 



## <a name="how-link-translation-works"></a>Hogyan hivatkozás fordítási működik

A hitelesítés után a proxykiszolgáló továbbítja az alkalmazásadatok a felhasználónak, amikor alkalmazásproxy megvizsgálja az alkalmazás szoftveresen kötött hivatkozások és azok a megfelelő külső URL-címek közzé.

Alkalmazásproxy feltételezi, hogy az UTF-8 alkalmazások kódolt. Ha nem, a helyzet, adja meg a kódolási típusát http-válaszfejléc, például `Content-Type:text/html;charset=utf-8`.

### <a name="which-links-are-affected"></a>Érintett kapcsolatokat?

A hivatkozás fordítási szolgáltatást csak hivatkozásokat tartalmaz, amelyek a kód címkéket egy alkalmazás keresi. Alkalmazásproxy rendelkezik egy külön szolgáltatással a cookie-k vagy URL-címek fordítása a fejlécekben. 

A helyszíni alkalmazások belső hivatkozások két közös típusa van:

- **Belső relatív hivatkozásokat** , hogy egy megosztott erőforráson helyi fájlstruktúrájával mutasson, például `/claims/claims.html`. Ezek a hivatkozások automatikusan munka alkalmazásokat proxyn keresztül történő közzétett, és továbbra is működnek, vagy a hivatkozás fordítási nélkül. 
- **Szoftveresen kötött belső hivatkozások** más hasonló a helyszíni alkalmazások `http://expenses` vagy például a közzétett `http://expenses/logo.jpg`. A hivatkozás fordítási szolgáltatást szoftveresen kötött belső hivatkozások működik, és módosítja őket, hogy a külső URL-címeket, amelyek a távoli felhasználók érdekében át kell haladnia mutasson.

### <a name="how-do-apps-link-to-each-other"></a>Alkalmazások hogyan tegye hivatkozás egymással?

Hivatkozás fordítási minden alkalmazáshoz engedélyezve van, így befolyásolni a felhasználói élmény az alkalmazáson belüli szintjén. Kapcsolja be az alkalmazás fordítása hivatkozásra, ha azt szeretné, hogy a hivatkozások *a* fordítani, az alkalmazás nem hivatkozik *való* alkalmazást. 

Tegyük fel, hogy rendelkezik-e, hogy az összes hivatkozás egymáshoz proxyn keresztül történő közzétett három alkalmazásokról: előnyeit, a költségek és utazás. A negyedik alkalmazások visszajelzést, nem közzétett proxyn keresztül történő van.

Ha engedélyezi az előnyöket alkalmazás fordítása hivatkozás, a költségek és utazás mutató hivatkozásokat tartalmaz a rendszer átirányítja a külső URL-címéből az alkalmazások, de visszajelzési hivatkozásra nem irányítja át, mert nincsenek külső URL-cím. Vissza előnyeit a költségeket és utazás hivatkozások nem működnek, mert hivatkozás fordítási nincs engedélyezve ezen két alkalmazáshoz.

![Hivatkozások más alkalmazásokba hivatkozás fordítási engedélyezésekor a rendszer előnyei](./media/application-proxy-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Nem szerepelnek a kapcsolatokat?

A jobb teljesítmény és biztonság érdekében egyes hivatkozások nem szerepelnek:

- Hivatkozások nem kód címkék belül. 
- Nincs a HTML- vagy CSS hivatkozásokat. 
- Belső hivatkozások megnyitása más. E-mail vagy azonnali üzenet keresztül küldött, vagy más dokumentumban szereplő hivatkozások nem fordítható le. A felhasználók a külső URL-címre ugrás tudnia kell.

E helyzetek egyikében támogatásához szükséges, ha használni azonos belső és külső URL-címek hivatkozás fordítási helyett.  

## <a name="enable-link-translation"></a>Hivatkozás fordítási engedélyezése

Első lépések hivatkozásra fordítási a lehető legkönnyebben egy gombra való kattintás:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Ugrás a **Azure Active Directory** > **vállalati alkalmazások** > **összes alkalmazás** > Válassza ki a kezelni kívánt alkalmazást > **alkalmazásproxy**.
3. Kapcsolja be **URL-címek fordítása a kérelem törzsében** való **Igen**.

   ![Válassza az Igen, a kérelem törzsében lefordítani az URL-címek](./media/application-proxy-link-translation/select_yes.png).
4. Válassza ki **mentése** a módosítások alkalmazásához.

Most amikor a felhasználók hozzáférése az alkalmazáshoz, a proxy automatikusan keresni kezdi a bérlő a proxyn keresztül történő közzétett belső URL-címek.

## <a name="send-feedback"></a>Visszajelzés küldése

Azt szeretnénk, hogy a működnek az alkalmazások a szolgáltatás segítségével. A HTML-vagy CSS azt keresés több mint 30 címkék. Ha rendelkezik, amelyek nem szerepelnek alatt létrehozott hivatkozások példát, küldjön egy kódrészletet a [Application Proxy visszajelzés](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>További lépések
[Egyéni tartományok használata az Azure AD-alkalmazásproxy](manage-apps/application-proxy-configure-custom-domain.md) szeretné, hogy a belső és külső URL-CÍMÉRE

[Konfigurálja a másodlagos címek leképezése a SharePoint 2013 rendszerhez](https://technet.microsoft.com/library/cc263208.aspx)
