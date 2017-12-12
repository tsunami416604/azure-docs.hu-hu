---
title: "Hivatkozások és az URL-címek Azure AD alkalmazás Proxy fordítása |} Microsoft Docs"
description: "Alapvető tudnivalók az Azure AD-alkalmazásproxy összekötők ismerteti."
services: active-directory
documentationcenter: 
author: kgremban
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 877c4491b3ebc2164f7cb2ecb9392d9c71e1667a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Az Azure AD alkalmazásproxy közzétett alkalmazásokhoz szoftveresen kötött hivatkozások átirányítása

Az Azure AD-alkalmazásproxy elérhetővé teszi a helyszíni alkalmazások a távoli felhasználók vagy a saját eszközeiket. Egyes alkalmazások esetében azonban fejlesztett helyi a beágyazott HTML-hivatkozásokat tartalmazó. Ezek a hivatkozások nem működnek megfelelően az alkalmazás távolról használatakor. Ha több, egymástól mutasson a helyszíni alkalmazások, a felhasználók várható tovább dolgozik, ha azok nem az irodában a hivatkozásokat. 

Győződjön meg arról, hogy hivatkozások azonos belüli és a vállalati hálózaton kívüli működik a legjobban a külső URL-címeket az alkalmazások azonos a belső URL-címek konfigurálásához. Használjon [egyéni tartományok](active-directory-application-proxy-custom-domains.md) a külső URL-címeket kell rendelkeznie a vállalati tartomány neve helyett az alapértelmezett alkalmazástartomány-proxy konfigurálása.

Ha a bérlő nem használhat egyéni tartományok, a hivatkozás fordítási szolgáltatást az alkalmazásproxy tartja a hivatkozásokat, függetlenül attól, hol találhatók a felhasználók működik. Ha alkalmazásokat, majd közvetlenül a belső végpontok vagy a portok, a közzétett alkalmazás külső Proxy URL belső URL-is leképezheti. Ha engedélyezve van a hivatkozás fordítása, és a közzétett belső hivatkozások használatával HTML, CSS, és válassza a JavaScript-címkék alkalmazásproxy történő. Majd az alkalmazásproxy fordítja le őket, hogy a felhasználók beolvasása folyamatos élményt.

>[!NOTE]
>A hivatkozás fordítási van a bérlők számára, hogy valamilyen okból nem használható egyéni tartományok azonos belső és külső URL-címéből alkalmazások rendelkeznek. Ez a funkció engedélyezése előtt tekintse meg, ha [egyéni tartományok az Azure AD alkalmazásproxy](active-directory-application-proxy-custom-domains.md) akkor is képes működni.
>
>Vagy, ha az alkalmazás kell konfigurálnia a hivatkozással fordítási SharePoint című [másodlagos címek leképezése a SharePoint 2013 rendszerhez konfigurálása](https://technet.microsoft.com/library/cc263208.aspx) leképezési hivatkozások egy másik módjáról.

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
- Hivatkozások nem HTML, CSS vagy JavaScript. 
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

Azt szeretnénk, hogy a működnek az alkalmazások a szolgáltatás segítségével. Azt több mint 30 címkék keresse meg a HTML-vagy CSS és is figyelembe véve, amely JavaScript esetekben támogatásához. Ha rendelkezik, amelyek nem szerepelnek alatt létrehozott hivatkozások példát, küldjön egy kódrészletet a [Application Proxy visszajelzés](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Következő lépések
[Egyéni tartományok használata az Azure AD-alkalmazásproxy](active-directory-application-proxy-custom-domains.md) szeretné, hogy a belső és külső URL-CÍMÉRE

[Konfigurálja a másodlagos címek leképezése a SharePoint 2013 rendszerhez](https://technet.microsoft.com/library/cc263208.aspx)
