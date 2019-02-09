---
title: Lefordítja a hivatkozások és URL-címek az Azure AD-alkalmazásproxyval |} A Microsoft Docs
description: Az Azure AD-alkalmazásproxy összekötőit alapjait ismerteti.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 6660d6f93ac89d83456fe50d60d8346a954a7753
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55965273"
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Átirányítási szoftveresen kötött mutató hivatkozásokat az Azure AD-alkalmazásproxyval közzétett alkalmazások

Az Azure AD-alkalmazásproxy elérhetővé teszi a helyszíni alkalmazások távoli felhasználók vagy a saját eszközeiket. Egyes alkalmazások esetében azonban fejlesztett helyi HTML ágyazott hivatkozásokkal. Ezek a hivatkozások nem működnek megfelelően az alkalmazás távoli használatakor. Mutasson az egymástól több helyszíni alkalmazásokkal rendelkezik, amikor a felhasználók várható a hivatkozások dolgozhat tovább, ha azok még nem az irodában. 

Győződjön meg arról, hogy hivatkozásokat azonos belül és a vállalati hálózaton kívül is működik, a legjobb módja, hogy a külső URL-címek konfigurálása az alkalmazások azonos a belső URL-címeit. Használat [egyéni tartományok](application-proxy-configure-custom-domain.md) konfigurálása a külső URL-címek pedig vállalati tartománynév kell application proxy alapértelmezett tartomány helyett.


Ha a bérlő nem használhat egyéni tartományokat, többféle módon más, amelyek biztosítják ezt a funkciót. Ezek mindegyike is kompatibilis az egyéni tartományok és egymással, így megadhatja az egyéni tartományok és más megoldásokkal, ha szükséges. 

**1. lehetőség: A Managed Browsert használhassák** – Ez a megoldás csak akkor alkalmazható, ha azt tervezi, hogy a javasolt vagy szükséges, hogy a felhasználók az alkalmazás eléréséhez az Intune Managed Browser keresztül. Az összes közzétett URL-címek azt fogja kezelni. 

**2. lehetőség: A MyApps bővítményével** – Ez a megoldás a felhasználónak az egy ügyféloldali bővítmény telepítéséhez, de azt fogja kezelni az minden közzétett URL-címeket, és a legnépszerűbb böngészőkben működik. 

**3. lehetőség: A hivatkozás a fordítási beállítással** – Ez egy nem rendszergazdai ügyféloldali beállítás, amely nem látható a felhasználók számára. Csak azonban azt fogja kezelni a HTML és CSS URL-címeket. Változtatható belső URL-címek Javascript keresztül létrehozott (például) nem fog működni.  

Ezeket a funkciókat folyamatosan dolgozik, függetlenül attól, hol találhatók a felhasználók a hivatkozásokat. Ha alkalmazások, amelyek közvetlenül a belső végpontok vagy -portokat, leképezheti belső URL-közzétett külső Application Proxy URL-címeket. 

 
> [!NOTE]
> Az utolsó lehetőség van csak azon bérlők számára, valamilyen okból nem használható egyéni tartományok alkalmazások azonos belső és külső URL-rendelkezik. Mielőtt engedélyezi ezt a funkciót, tekintse át, ha [egyéni tartományok az Azure AD-alkalmazásproxy](application-proxy-configure-custom-domain.md) is. 

>Vagy, ha az alkalmazást konfigurálnia kell a hivatkozás fordítási SharePoint, lásd: [konfigurálása a SharePoint 2013-hoz készült másodlagos címek leképezése](https://technet.microsoft.com/library/cc263208.aspx) egy másik módszer a leképezés hivatkozásokat. 

 
### <a name="option-1-intune-managed-browser-integration"></a>Option 1: Az Intune által felügyelt böngésző-integráció 

Az Intune Managed Browser használatával további védelme érdekében az alkalmazás és a tartalom. Ez a megoldás használatához meg kell szükséges vagy ajánlott felhasználói hozzáférés az alkalmazást az Intune Managed Browser. Belső URL-címekhez alkalmazásproxyval közzétett ismeri fel a Managed Browser lesz, és a rendszer átirányítja a megfelelő külső URL-CÍMÉT. Ez biztosítja, hogy működik a szokott belső URL-címet, és ha a felhasználó a böngésző kerül, és közvetlenül a belső URL-cím-típusok, akkor is, ha a felhasználó távoli működik.  

További információkért, hogyan konfigurálhatja ezt a beállítást, többek között tekintse át a [Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser) dokumentációját.  

### <a name="option-2-myapps-browser-extension"></a>Option 2: MyApps böngészőbővítmény 

A MyApps böngésző kiterjesztésű belső URL-címekhez alkalmazásproxyval közzétett ismeri fel a bővítmény és a megfelelő külső URL-címre irányítja át. Ez biztosítja, hogy működik a szokott belső URL-címet, és ha a felhasználó a böngésző címsorában kerül, és közvetlenül a belső URL-cím-típusok, akkor is, ha a felhasználó távoli működik.  

Ez a funkció használatához a felhasználónak van szüksége, töltse le a bővítményt, és bekerülhet. Nem tartozik a rendszergazdák vagy a felhasználó szükséges többi konfiguráció. 

 

### <a name="option-3-link-translation-setting"></a>3. lehetőség: Hivatkozás fordítási beállítás 

Ha hivatkozás címfordítás engedélyezve van, az alkalmazásproxy-szolgáltatás HTML és CSS használatával keres közzétett belső hivatkozásokat, és fordítja le őket, hogy a felhasználók egy folyamatos tapasztalatokat szerezhet. 



## <a name="how-link-translation-works"></a>Hogyan hivatkozni a fordítási működése

A hitelesítés után a proxykiszolgáló átadja az alkalmazásadatok a felhasználónak, ha az alkalmazásproxy megvizsgálja az alkalmazás nem változtatható hivatkozások és azok megfelelő, közzé külső URL-címek.

Alkalmazásproxy azt feltételezi, hogy az UTF-8 kódolt kérelmek. Ha nem, amely a helyzet, adja meg a kódolási típus egy http-válaszfejléc például `Content-Type:text/html;charset=utf-8`.

### <a name="which-links-are-affected"></a>Melyik hivatkozások vannak hatással?

A hivatkozás fordítási szolgáltatást kód címkék alkalmazás törzsében lévő hivatkozásokat csak keres. Az alkalmazásproxy rendelkezik egy külön szolgáltatás, a cookie-kat vagy URL-címek fordítása a fejlécekben. 

A helyszíni alkalmazások belső hivatkozások közös két típusa van:

- **Belső relatív hivatkozásokat** , hogy egy közös erőforráshoz egy helyi fájlszerkezetet a pont, például `/claims/claims.html`. Ezek a hivatkozások automatikusan alkalmazásokat, amelyek alkalmazásproxyn keresztül közzétett, és továbbra is működik, vagy a hivatkozás fordítási nélkül működik. 
- **Szoftveresen kötött belső hivatkozások** hasonló más helyszíni alkalmazások `http://expenses` vagy hasonló fájlok közzétett `http://expenses/logo.jpg`. A hivatkozás fordítási szolgáltatást szoftveresen kötött belső hivatkozások működik, és módosítja őket, hogy a külső URL-címeket, a távoli felhasználók oldhatják mutasson.

### <a name="how-do-apps-link-to-each-other"></a>Alkalmazások hogyan tegye hivatkozni egymással?

Hivatkozás fordítási engedélyezve van minden alkalmazáshoz, hogy a felhasználói élmény felett az alkalmazásonkénti szintjén. Kapcsolja be a hivatkozás fordítási egy alkalmazáshoz, ha azt szeretné, hogy a hivatkozások *a* nem fordítható le, az alkalmazás hivatkozásokat tartalmaz *való* , amelyet az alkalmazás. 

Tegyük fel például, hogy három olyan alkalmazásokkal rendelkezik, hogy az összes hivatkozás egymáshoz alkalmazásproxyn keresztül közzétett: Előnyök, a költségek és a megtételéhez szükséges. Van egy negyedik alkalmazást, visszajelzés, alkalmazásproxyn keresztül közzétett nem.

Hivatkozás fordítási az előnyök alkalmazás engedélyezésekor a költségek és utazási mutató hivatkozásokat tartalmaz a rendszer átirányítja a külső URL-címek ezen alkalmazások esetén, de visszajelzés mutató hivatkozás nem irányítja át, mert nincsenek külső URL-CÍMÉT. Vissza előnyeit a költségek és utazás hivatkozások nem működik, mert a hivatkozás fordítási nincs engedélyezve ezen két alkalmazások esetén.

![Hivatkozások más alkalmazásoknak, ha engedélyezve van a hivatkozás fordítási előnyei](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Melyik hivatkozások nem szerepelnek?

A jobb teljesítmény és biztonság érdekében egyes hivatkozások nem szerepelnek:

- Hivatkozások nem kód címkék belül. 
- Hivatkozások nem szereplő HTML és CSS. 
- A hivatkozások URL-ként kódolt formátumban.
- Belső hivatkozások más programok nyithatók meg. E-mailben vagy csevegőüzenetekkel keresztül, vagy más dokumentumokban szereplő hivatkozások nem fordítható le. Nyissa meg a külső URL-cím, ismernie kell a felhasználókat.

Kell egy két forgatókönyv esetében támogatott, ha használja az azonos belső és külső URL-címek helyett hivatkozást fordítási.  

## <a name="enable-link-translation"></a>Hivatkozás fordítási engedélyezése

Hivatkozás fordítási használatbavétele kell tennie, csak egy gombra:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Lépjen a **Azure Active Directory** > **vállalati alkalmazások** > **minden alkalmazás** > Válassza ki a kezelni kívánt alkalmazást >  **Az alkalmazásproxy**.
3. Kapcsolja be **URL-címek fordítása a kérelem törzsében** való **Igen**.

   ![Válassza az Igen lehetőséget a kérelem törzsében szereplő URL-címek lefordítása](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Válassza ki **mentése** alkalmazza a módosításokat.

Most a felhasználók számára az alkalmazás eléréséhez, ha a proxy automatikusan ellenőrizze a belső URL-címeket, a bérlő alkalmazásproxyn keresztül közzétett.

## <a name="send-feedback"></a>Visszajelzés küldése

Szeretnénk az Ön segítségét kérni, hogy ez a szolgáltatás összes alkalmazás számára. HTML és CSS több mint 30-címkékkel keresés azt. Ha egy példa a létrehozott hivatkozásokat, amely nem áll fordítás alatt van, küldjön egy kódrészletet [visszajelzés az alkalmazásról Proxy](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>További lépések
[Egyéni tartományok használata az Azure AD-alkalmazásproxy](application-proxy-configure-custom-domain.md) szeretné, hogy az azonos belső és külső URL-cím

[Konfigurálja a másodlagos címek leképezése a SharePoint 2013 rendszerhez](https://technet.microsoft.com/library/cc263208.aspx)
