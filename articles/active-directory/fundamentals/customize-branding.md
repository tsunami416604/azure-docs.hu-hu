---
title: Márkajelzés hozzáadása a szervezet bejelentkezési lapjához – Azure AD
description: Útmutató a szervezet márkajelzésének az Azure Active Directory bejelentkezési lapjához való hozzáadásáról.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: ajburnle
ms.reviewer: kexia
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 441fdd14cc2c734b6ce532f3ad1d30663b2f56c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049795"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Márkajelzés hozzáadása a szervezet Azure Active Directory bejelentkezési lapjához
A szervezet emblémájának és egyéni színsémáinak használatával egységes megjelenést biztosíthat az Azure Active Directory (Azure AD) bejelentkezési lapjain. A bejelentkezési lapok akkor jelennek meg, amikor a felhasználók bejelentkeznek a szervezet webalapú alkalmazásaiba, például az Office 365-be, amely az Azure AD-t használja identitásszolgáltatóként.

>[!Note]
>Egyéni márkajelzés hozzáadásához az Azure Active Directory Premium 1, Premium 2 vagy Basic kiadásokat kell használnia, vagy Office 365-licenccel kell rendelkeznie. A licenceléssel és a kiadásokkal kapcsolatos további információkért [lásd: Regisztráció az Azure AD Premium szolgáltatásra című témakörben.](active-directory-get-started-premium.md)<br><br>Az Azure AD Prémium és Alapszintű kiadása az Azure Active Directory világszerte elérhető példányával érhetők el a kínai ügyfelek számára. Az Azure AD Prémium és Alapszintű kiadása jelenleg nem támogatott Kínában a 21Vianet által működtetett Azure-szolgáltatásban. További információért lépjen velünk kapcsolatba az [Azure Active Directory fórumon](https://feedback.azure.com/forums/169401-azure-active-directory/) keresztül.

## <a name="customize-your-azure-ad-sign-in-page"></a>Az Azure AD bejelentkezési lapjának testreszabása
Testreszabhatja az Azure AD bejelentkezési lapjait, amelyek akkor jelennek meg, amikor a `https://outlook.com/contoso.com`felhasználók bejelentkeznek a szervezet `https://passwordreset.microsoftonline.com/?whr=contoso.com`bérlőspecifikus alkalmazásaiba, például a , vagy egy tartományváltozó, például a .

Az egyéni márkajelzés nem jelenik meg azonnal, amikor\.a felhasználók olyan webhelyekre mennek, mint például a www office.com. Ehelyett a felhasználónak be kell jelentkeznie, mielőtt a testreszabott márkajelzés megjelenik. A felhasználó bejelentkezése után a márkajelzés megjelenése akár 15 percet is igénybe vehet. 

> [!NOTE]
> Minden márkajelzési elem nem kötelező. Ha például háttérkép nélküli szalagcím-emblémát ad meg, a bejelentkezési lapon megjelenik az embléma a célwebhelyről származó alapértelmezett háttérképpel (például az Office 365-ben).<br><br>Emellett a bejelentkezési oldal márkajelzése nem vihető át a személyes Microsoft-fiókokra. Ha a felhasználók vagy az üzleti vendégek személyes Microsoft-fiókkal jelentkeznek be, a bejelentkezési oldal nem tükrözi a szervezet márkajelzését.

### <a name="to-customize-your-branding"></a>A márkajelzés testreszabása
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Válassza az **Azure Active Directory**lehetőséget, majd a Vállalati **márkajelzés**lehetőséget, majd a **Konfigurálás**lehetőséget.

    ![Contoso – Vállalati márkajelzési lap, Beállítás kiemelve](media/customize-branding/company-branding-configure-button.png)

3. A **Vállalati márkajelzés konfigurálása** lapon adja meg az alábbi információk bármelyikét vagy mindegyikét.

    >[!Important]
    >Az ezen az oldalon hozzáadott összes egyéni kép képmérettel (képpont) és potenciálisan fájlmérettel (KB) rendelkezik, korlátozásokkal. A korlátozások miatt valószínűleg fényképszerkesztőt kell használnia a megfelelő méretű képek létrehozásához.

    - **Általános beállítások**

        ![A vállalati márkajelzési lap konfigurálása az általános beállítások kal kiegészítve](media/customize-branding/configure-company-branding-general-settings.png)

        - **Nyelv.** A nyelv automatikusan alapértelmezettként van beállítva, és nem módosítható.
        
        - **Bejelentkezési oldal háttérképe.** Jelöljön ki egy .png vagy .jpg képfájlt, amely a bejelentkezési oldalak háttereként jelenik meg. 
        
            A kép mérete nem lehet nagyobb 1920x1080 képpontnál, és 300 KB-nál kisebb fájlmérettel kell rendelkeznie.

        - **Banner embléma.** Válassza ki az embléma .png vagy .jpg verzióját, amely a bejelentkezési lapon jelenik meg, miután a felhasználó felhasználónevet adott meg, valamint a **Saját alkalmazások** portál lapon.
            
            A kép nem lehet 60 képpontnál magasabb vagy 280 képpontnál szélesebb. Javasoljuk, hogy használjon átlátszó képet, mivel előfordulhat, hogy a háttér nem egyezik meg az embléma hátterével. Azt is javasoljuk, hogy ne adjon hozzá kitöltést a kép hez, különben az is, hogy az embléma kicsinek tűnjön.

        - **Felhasználónév tipp.** Írja be a felhasználók számára megjelenő tippszöveget, ha elfelejtik a felhasználónevüket. Ennek a szövegnek Unicode kódolásúnak kell lennie, hivatkozások és kód nélkül, és nem lehet hosszabb 64 karakter. Ha a vendégek bejelentkeznek az alkalmazásba, javasoljuk, hogy ne adja meg ezt a tippet.

        - **Bejelentkezési oldal szövege.** Írja be a bejelentkezési oldal alján megjelenő szöveget. Ezzel a szöveggel további információkat is közlönylhet, például a telefonszámot az ügyfélszolgálatnak vagy egy jogi nyilatkozatot. Ennek a szövegnek Unicode formátumúnak és legfeljebb 256 karakternek kell lennie. Azt is javasoljuk, hogy ne tartalmazza a linkeket vagy HTML-címkéket.

    - **Speciális beállítások**
            
        ![A vállalati márkajelzési lap konfigurálása speciális beállításokkal](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Bejelentkezési oldal háttérszíne.** Adja meg azt a hexadecimális színt (például a fehér #FFFFFF), amely kis sávszélességű kapcsolatesetén a háttérkép helyén jelenik meg. Javasoljuk, hogy a szalagcím emblémájának vagy a szervezet színének elsődleges színét használja.

        - **Szögletes embléma kép.** Válassza ki a szervezet emblémájának .png (előnyben részesített) vagy .jpg (jpg) képét, amely megjelenik a felhasználók számára az új Windows 10 Enterprise eszközök telepítési folyamata során. Ez a lemezkép csak Windows-hitelesítéshez használatos, és csak azokon a bérlőkön jelenik meg, amelyek a [Windows Autopilot ot]( https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) használják telepítésre vagy jelszóbeviteli lapokra más Windows 10-környezetben. Bizonyos esetekben a hozzájárulási párbeszédpanelen is megjelenhet.
        
            A kép mérete nem lehet nagyobb 240x240 képpontnál, és a fájlmérete nem lehet 10 KB-nál kisebb. Javasoljuk, hogy használjon átlátszó képet, mivel előfordulhat, hogy a háttér nem egyezik meg az embléma hátterével. Azt is javasoljuk, hogy ne adjon hozzá kitöltést a kép hez, különben az is, hogy az embléma kicsinek tűnjön.
    
        - **Szögletes embléma kép, sötét téma.** Ugyanaz, mint a négyzet alakú logó fenti képen. Ez az emblémakép a négyzet alakú emblémakép helyét foglalja el, ha sötét háttérrel használja, például a Windows 10 Azure AD-hez csatlakozott képernyőkön a kezdőélmény (OOBE) során.  Ha az embléma jól néz ki fehér, sötétkék és fekete háttérrel, nem kell hozzáadnia ezt a képet. 
        
        - **Megjelenítési lehetőség, hogy továbbra is bejelentkezve maradjon.** Beállíthatja, hogy a felhasználók továbbra is bejelentkezve maradnak az Azure AD-be, amíg kifejezetten ki nem jelentkeznek. Ha a **Nem**lehetőséget választja, ez a beállítás rejtett, és a felhasználóknak minden alkalommal be kell jelentkezniük, amikor a böngészőt bezárják és újra megnyitják.
        
            >[!Note]
            >A SharePoint Online és az Office 2010 egyes funkciói attól függenek, hogy a felhasználók bejelentkezve tudnak-e maradni. Ha ennél a lehetőségnél a **Nem** értéket adja meg, a felhasználóinak további váratlan bejelentkezési felszólítások jelenhetnek meg.
   

3. Miután befejezte a márkajelzés hozzáadását, válassza a **Mentés lehetőséget.**

    Ha ez a folyamat létrehozza az első egyéni márkajelzési konfigurációt, ez lesz az alapértelmezett a bérlő számára. Ha további konfigurációkkal rendelkezik, kiválaszthatja az alapértelmezett konfigurációt.
    
    >[!Important]
    >További vállalati márkaépítési konfigurációk hozzáadása a bérlőhöz, válassza az **új nyelvet** a **Contoso - Vállalati márkajelzési** lapon. Ez megnyitja a **Vállalati márkajelzés konfigurálása** lapot, ahol ugyanazokat a lépéseket hajthatja végre, mint fent.

## <a name="update-your-custom-branding"></a>Az egyéni márkajelzés frissítése
Miután létrehozta az egyéni márkajelzést, visszaléphet, és bármit módosíthat.

### <a name="to-edit-your-custom-branding"></a>Az egyéni márkajelzés szerkesztése
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Válassza az **Azure Active Directory**lehetőséget, majd a Vállalati **márkajelzés**lehetőséget, majd a **Konfigurálás**lehetőséget.

    ![Contoso – Vállalati márkajelzési lap, az alapértelmezett konfigurációval](media/customize-branding/company-branding-default-config.png)

3. A **Vállalati márkajelzés konfigurálása** lapon adja hozzá, távolítsa el vagy módosítsa az adatokat a cikk [Azure AD-bejelentkezési lapjának testreszabása](#customize-your-azure-ad-sign-in-page) című szakaszban található leírások alapján.

4. Kattintson a **Mentés** gombra.

   A bejelentkezési oldal arculatán alkalmazott módosítások megjelenítése akár egy órát is igénybe vehet.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Nyelvspecifikus vállalati arculat hozzáadása a címtárhoz
Az eredeti konfiguráció nyelve nem módosítható az alapértelmezett nyelvhez képest. Ha azonban más nyelven szeretne konfigurációt használni, létrehozhat egy új konfigurációt.

### <a name="to-add-a-language-specific-branding-configuration"></a>Nyelvspecifikus márkajelzési konfiguráció hozzáadása

1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Válassza az **Azure Active Directory**lehetőséget, majd a Vállalati **márkajelzés**lehetőséget, majd az **Új nyelv**lehetőséget.

    ![Contoso – Vállalati márkajelzési lap, kiemelve az Új nyelv beállítással](media/customize-branding/company-branding-new-language.png)

3. A **Vállalati márkajelzés konfigurálása** lapon válassza ki a nyelvet (például francia), majd adja hozzá a lefordított adatokat a cikk [Azure AD bejelentkezési lapjának testreszabása](#customize-your-azure-ad-sign-in-page) című szakaszban található leírások alapján.

4. Kattintson a **Mentés** gombra.

    A **Contoso – Cég márkajelzési** lapja frissül az új francia konfiguráció megjelenítéséhez.

    ![Contoso – Vállalati márkajelzési lap, az alapértelmezett konfigurációval](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Egyéni márkajelzés hozzáadása a lapokhoz
Adja hozzá az egyéni márkajelzést az oldalakhoz úgy, hogy módosítja az URL végét a szöveggel. `?whr=yourdomainname` Ez a módosítás több oldalon is működik, beleértve a Többtényezős hitelesítés (MFA) beállítási lapját, az Önkiszolgáló jelszó-visszaállítás (SSPR) beállítási lapját és a bejelentkezési lapot.

**Példák:**

**Eredeti URL:Original URL:**https://aka.ms/MFASetup<br>
**Egyéni URL-cím:**`https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com`

**Eredeti URL:Original URL:**https://aka.ms/SSPR<br>
**Egyéni URL-cím:**`https://passwordreset.microsoftonline.com/?whr=contoso.com`

 
