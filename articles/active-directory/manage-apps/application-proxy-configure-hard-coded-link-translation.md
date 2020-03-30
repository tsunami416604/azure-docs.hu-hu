---
title: Linkek és URL-ek fordítása Azure AD App Proxy | Microsoft dokumentumok
description: Ismerteti az Azure AD alkalmazásproxy-összekötők alapjait.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa0dc2081aff5a24fb830b756131cccd5c6ce810
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69533703"
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Az Azure AD alkalmazásproxyval közzétett alkalmazások hoz a kódolt hivatkozások átirányítása

Az Azure AD alkalmazásproxy elérhetővé teszi a helyszíni alkalmazásokat a távoli vagy saját eszközökön lévő felhasználók számára. Egyes alkalmazásokat azonban a HTML-be ágyazott helyi hivatkozásokkal fejlesztettek ki. Ezek a hivatkozások nem működnek megfelelően, ha az alkalmazást távolról használják. Ha több helyszíni alkalmazás is rámutat egymásra, a felhasználók azt várják, hogy a hivatkozások akkor is működjenek, amikor nem az irodában tartózkodnak. 

A legjobb módja annak, hogy a hivatkozások a vállalati hálózaton belül és kívül is ugyanúgy működjenek, ha az alkalmazások külső URL-címeit úgy állítja be, hogy azok megegyeznek a belső URL-címekkel. [Egyéni tartományok](application-proxy-configure-custom-domain.md) használatával konfigurálhatja a külső URL-címeket úgy, hogy az alapértelmezett alkalmazásproxy-tartomány helyett a vállalati tartománynevet kapják.


Ha nem tudja használni az egyéni tartományok at a bérlőben, számos más lehetőség van a funkció biztosítására. Ezek mindegyike kompatibilis az egyéni tartományokkal és egymással, így szükség esetén egyéni tartományokat és más megoldásokat is konfigurálhat.

> [!NOTE]
> A Javascript en keresztül létrehozott, kódolt belső URL-ek esetében a hivatkozásfordítás nem támogatott.

**1. lehetőség: Használja a Felügyelt böngészőt vagy a Microsoft Edge-** t – Ez a megoldás csak akkor alkalmazható, ha azt szeretné ajánlani, vagy meg kívánja követelni, hogy a felhasználók az Intune felügyelt böngészőjén vagy a Microsoft Edge böngészőn keresztül férjenek hozzá az alkalmazáshoz. Ez kezeli az összes közzétett URL-eket. 

**2. lehetőség: Használja a MyApps extension** - Ez a megoldás megköveteli a felhasználókszámára, hogy telepítsen egy ügyféloldali böngészőbővítményt, de kezeli az összes közzétett URL-t, és a legnépszerűbb böngészőkkel működik. 

**3. lehetőség: Használja a linkfordítási beállítást** – Ez egy olyan rendszergazdai oldalbeállítás, amely a felhasználók számára láthatatlan. Azonban csak HTML és CSS URL-címeket kezel.   

Ez a három funkció folyamatosan működik a linkek nem számít, hol a felhasználók. Ha olyan alkalmazásokkal rendelkezik, amelyek közvetlenül a belső végpontokra vagy portokra mutatnak, ezeket a belső URL-címeket leképezheti a közzétett külső alkalmazásproxy-URL-címekre. 

 
> [!NOTE]
> Az utolsó lehetőség csak a bérlők, hogy bármilyen okból nem használhatja az egyéni tartományok, hogy ugyanazt a belső és külső URL-eket az alkalmazások. A szolgáltatás engedélyezése előtt tekintse meg, hogy [az Azure AD alkalmazásproxy egyéni tartományai](application-proxy-configure-custom-domain.md) működhetnek-e az Ön számára. 
> 
> Ha a hivatkozásfordítással konfigurálni kívánt alkalmazás SharePoint, olvassa el a [SharePoint 2013-hoz való alternatív hozzáférés-hozzárendelések konfigurálása](https://technet.microsoft.com/library/cc263208.aspx) című témakört a hivatkozások leképezésének másik megközelítéséhez. 

 
### <a name="option-1-intune-managed-browser-and-microsoft-edge-integration"></a>1. lehetőség: Az Intune felügyelt böngészője és a Microsoft Edge-integráció 

Az Intune felügyelt böngészője vagy a Microsoft Edge segítségével tovább védheti az alkalmazást és a tartalmat. A megoldás használatához meg kell követelnie/ajánlania kell a felhasználóknak az alkalmazás elérését az Intune felügyelt böngészőn keresztül. Az alkalmazásproxyval közzétett összes belső URL-t a felügyelt böngésző felismeri, és a megfelelő külső URL-re irányítja át. Ez biztosítja, hogy az összes kódolt belső URL-ek működnek, és ha a felhasználó a böngészőbe lép, és közvetlenül beírja a belső URL-t, akkor is működik, ha a felhasználó távoli.  

További információért, például a beállítás konfigurálásáról, olvassa el a [Felügyelt böngésző](https://docs.microsoft.com/intune/app-configuration-managed-browser) dokumentációját.  

### <a name="option-2-myapps-browser-extension"></a>2. lehetőség: MyApps böngészőbővítmény 

A MyApps böngészőbővítmény segítségével az alkalmazásproxyval közzétett összes belső URL-t a bővítmény felismeri, és átirányítja a megfelelő külső URL-re. Ez biztosítja, hogy az összes kódolt belső URL-ek működnek, és ha a felhasználó a böngésző címsorába lép, és közvetlenül beírja a belső URL-t, akkor is működik, ha a felhasználó távoli.  

A funkció használatához a felhasználónak le kell töltenie a bővítményt, és be kell jelentkeznie. Nincs más konfiguráció szükséges a rendszergazdák vagy a felhasználók számára. 

További információért, beleértve a beállítás konfigurálását, olvassa el a [MyApps böngészőbővítmény](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access#download-and-install-the-my-apps-secure-sign-in-extension) dokumentációját.

### <a name="option-3-link-translation-setting"></a>3. lehetőség: Hivatkozásfordítási beállítás 

Ha a hivatkozásfordítás engedélyezve van, az Alkalmazásproxy szolgáltatás html- és CSS-en keresztül keres közzétett belső hivatkozásokat, és lefordítja azokat, hogy a felhasználók megszakítás nélküli élményt kapjanak. A MyApps böngészőbővítmény használata előnyös ebbi a Hivatkozásfordítási beállításnál, mivel ez nagyobb teljesítményt nyújt a felhasználóknak.

> [!NOTE]
> Ha a 2.

## <a name="how-link-translation-works"></a>Hogyan működik a hivatkozásfordítás?

A hitelesítés után, amikor a proxykiszolgáló átadja az alkalmazás adatokat a felhasználónak, az alkalmazásproxy megvizsgálja az alkalmazást a kódolt hivatkozások után, és lecseréli azokat a megfelelő, közzétett külső URL-címekre.

Az alkalmazásproxy feltételezi, hogy az alkalmazások UTF-8-ban vannak kódolva. Ha nem ez a helyzet, adja meg a kódolás típusát `Content-Type:text/html;charset=utf-8`egy http válaszfejlécben, például .

### <a name="which-links-are-affected"></a>Mely hivatkozásokérintettek?

A hivatkozásfordítási funkció csak az okat a címkéket tartalmazó hivatkozásokat keresi meg az alkalmazás törzsében. Az alkalmazásproxy külön funkcióval rendelkezik a cookie-k vagy URL-ek fejlécekben történő fordításához. 

A helyszíni alkalmazásokban a belső kapcsolatoknak két gyakori típusa van:

- **Relatív belső hivatkozások,** amelyek egy helyi fájlstruktúrában lévő megosztott erőforrásra mutatnak, például `/claims/claims.html`. Ezek a hivatkozások automatikusan működnek az alkalmazásproxyn keresztül közzétett alkalmazásokban, és továbbra is kapcsolatfordítással vagy anélkül működnek. 
- **Kódolt belső hivatkozások** más helyszíni alkalmazásokhoz, `http://expenses` például `http://expenses/logo.jpg`a . A hivatkozásfordítási funkció a kódolt belső hivatkozásokon működik, és azokat a távoli felhasználók által átkell mennünk.

Az alkalmazásproxy által támogatott HTML-kódcímkék teljes listája a következők:
* a
* Audio
* base
* gombra
* div
* Beágyaz
* Formában
* Keret
* Fej
* html
* iframe
* kép
* bemenet
* Link
* Menuitem
* Meta
* objektum
* szkriptet.
* source
* követés
* Videó

Ezenkívül a CSS-en belül az URL-attribútum is levan fordítva.

### <a name="how-do-apps-link-to-each-other"></a>Hogyan kapcsolódnak egymáshoz az alkalmazások?

A hivatkozásfordítás minden alkalmazáshoz engedélyezve van, így alkalmazásonként szabályozhatja a felhasználói élményt. Ha azt szeretné, hogy az adott alkalmazás *hivatkozásait* lefordítsa, ne az adott *alkalmazásra* mutató hivatkozásokat. 

Tegyük fel például, hogy három alkalmazás van közzétéve az alkalmazásproxyn keresztül, amelyek mindegyike egymásra mutató hivatkozással rendelkezik: előnyök, költségek és utazás. Van egy negyedik alkalmazás, a Visszajelzés, amely nem jelenik meg az alkalmazásproxyn keresztül.

Ha engedélyezi a kapcsolatok fordítását a Előnyök alkalmazáshoz, a költségekre és az utazásra mutató hivatkozásokat a rendszer átirányítja az alkalmazások külső URL-jeire, de a visszajelzésre mutató hivatkozást nem irányítja át, mert nincs külső URL. A költségekés az utazások vissza az előnyökre mutató hivatkozások nem működnek, mert a két alkalmazás esetében nincs engedélyezve a hivatkozásfordítás.

![Az előnyökről mutató hivatkozások más alkalmazásokra, ha a hivatkozásfordítás engedélyezve van](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Mely hivatkozások nincsenek lefordítva?

A teljesítmény és a biztonság javítása érdekében egyes hivatkozások at nem fordítanak le:

- Linkek nem belül a kódcímkéket. 
- Linkek nem HTML vagy CSS. 
- Linkek URL-kódolású formátumban.
- Más programokból megnyitott belső hivatkozások. Az e-mailben vagy csevegőüzenetben küldött, illetve más dokumentumokban szereplő hivatkozásokat nem fordítják le. A felhasználóknak tudniuk kell, hogy menjen a külső URL-t.

Ha a két forgatókönyv egyikét kell támogatnia, használja ugyanazokat a belső és külső URL-címeket a hivatkozásfordítás helyett.  

## <a name="enable-link-translation"></a>Hivatkozásfordítás engedélyezése

A hivatkozások fordításának megkezdése olyan egyszerű, mint egy gombra kattintani:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) rendszergazdaként.
2. Nyissa meg az **Azure Active Directory** > **Enterprise alkalmazások:** > **Minden alkalmazás** > válassza ki a > **alkalmazásproxyt**kezelni kívánt alkalmazást.
3. Fordítsa **le fordítani az URL-címeket az alkalmazás törzsében** Igen **(Igen)** fordulatra.

   ![Válassza az Igen lehetőséget az URL-ek alkalmazástörzsben való fordításához.](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. A módosítások alkalmazásához kattintson a **Mentés** gombra.

Most, amikor a felhasználók hozzáférnek ehhez az alkalmazáshoz, a proxy automatikusan megkeresi a belső URL-címeket, amelyek közzétettalkalmazás-proxy a bérlőn keresztül.

## <a name="send-feedback"></a>Visszajelzés küldése

Azt szeretnénk, hogy a segítsége, hogy ez a funkció működik az összes apps. Több mint 30 címkét keresünk HTML és CSS formátumban. Ha van egy példa a létrehozott hivatkozásokra, amelyeket nem fordítanak le, küldjön egy kódrészletet [az alkalmazásproxy-visszajelzésnek.](mailto:aadapfeedback@microsoft.com) 

## <a name="next-steps"></a>További lépések
[Egyéni tartományok használata az Azure AD alkalmazásproxyval](application-proxy-configure-custom-domain.md) ugyanazt a belső és külső URL-címet

[Alternatív hozzáférés-hozzárendelések konfigurálása a SharePoint 2013-hoz](https://technet.microsoft.com/library/cc263208.aspx)
