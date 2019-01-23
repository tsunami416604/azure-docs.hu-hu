---
title: A munkahelyi bejelentkezési lap – Azure Active Directory márkajelzés hozzáadása |} A Microsoft Docs
description: A szervezet az Azure Active Directory bejelentkezési oldalára márkajelzési adásával kapcsolatos utasításokat.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: lizross
ms.reviewer: kexia
ms.custom: it-pro, seodec18
ms.openlocfilehash: 7abfa0e52abb594668935d325835e3ef25818aa2
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452421"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Adja hozzá a szervezet Azure Active Directory bejelentkezési oldal arculat megjelenítése
A vállalat emblémája és egyéni színsémák használatával adjon meg egy egységes megjelenést és a hangulat használatát az Azure Active Directory (Azure AD) bejelentkezési oldalakon. A bejelentkezési oldal jelenik meg, ha a felhasználói bejelentkeznek a munkahelyi webes alkalmazásokhoz, például az Office 365, amely használja az Azure ad-ben az identitás-szolgáltatóként.

>[!Note]
>Egyéni arculat hozzáadása kell használni az Azure Active Directory Premium 1, 2. prémium vagy alapszintű kiadásához, vagy rendelkezik Office 365-licenccel. További információ a licencelési és -kiadás: [regisztrálhat a prémium szintű Azure AD](active-directory-get-started-premium.md).<br><br>Az Azure AD Prémium és Alapszintű kiadása az Azure Active Directory világszerte elérhető példányával érhetők el a kínai ügyfelek számára. Az Azure AD Prémium és Alapszintű kiadása jelenleg nem támogatott Kínában a 21Vianet által működtetett Azure-szolgáltatásban. További információért lépjen velünk kapcsolatba az [Azure Active Directory fórumon](https://feedback.azure.com/forums/169401-azure-active-directory/) keresztül.

## <a name="customize-your-azure-ad-sign-in-page"></a>Az Azure AD bejelentkezési oldal testreszabása
Testre szabhatja az Azure AD bejelentkezési lapok, amelyek jelenik meg, amikor a felhasználók bejelentkeznek a szervezet bérlő-specifikus alkalmazások, mint például [ *https://outlook.com/contoso.com* ](https://outlook.com/contoso.com), vagy egy, a tartomány változót, például a átadásakor[ *https://passwordreset.microsoftonline.com/?whr=contoso.com*](https://passwordreset.microsoftonline.com/?whr=contoso.com).

Az egyéni márkajelzési nem jelenik meg azonnal, amikor a helyeket, például www.office.com nyissa meg a felhasználók számára. Ehelyett a felhasználók számára való bejelentkezés előtt jelenik meg a testre szabott védjegyzést biztosít.

> [!NOTE]
> Az összes márkajelzési elemek egyike sem kötelező. Ha nincs háttérkép-megad szalagcímemblémát, például a bejelentkezési oldal jeleníti meg az embléma a célhelyről (például Office 365) alapértelmezett háttérben történő rendszerképpel.<br><br>Ezenkívül arculat megjelenítése a bejelentkezési oldal nem jelenik meg személyes Microsoft-fiókok. Ha a felhasználók vagy a vendégek személyes Microsoft-fiókkal, a bejelentkezési oldal nem jelenik meg a céges arculatot a szervezet.

### <a name="to-customize-your-branding"></a>A márkajelzési beállításokat testreszabása
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Válassza ki **Azure Active Directory**, majd válassza ki **vállalati védjegyezés**, majd válassza ki **konfigurálása**.

    ![Contoso – vállalati arculati oldalon, a konfigurálás opció kiemelésével](media/customize-branding/company-branding-configure-button.png)

3. Az a **konfigurálása a vállalati arculatot** lap, adja meg a következő információkat egy részének vagy egészének.

    >[!Important]
    >Ezen az oldalon hozzáadhat egyéni rendszerképek a kép mérete (képpont) rendelkezik, és esetleg a fájl mérete (KB), korlátozások. Ezek a korlátozások miatt legvalószínűbb kell egy fénykép-szerkesztő segítségével a jobb méretű képek létrehozása.

    - **Általános beállítások**

        ![Vállalati arculat megjelenítése a lapon befejeződött általános beállítások konfigurálása](media/customize-branding/configure-company-branding-general-settings.png)

        - **Nyelv.** A nyelv alapértelmezett értéke automatikusan, és nem módosítható.
        
        - **Bejelentkezési lap háttérképe** Válasszon ki egy .png vagy .jpg képet fájlt jelennek meg a bejelentkezési oldalakat a háttérben. 
        
            A lemezkép nem lehet nagyobb, mint 1920 x 1080 képpont, mérete, és rendelkeznie kell egy fájl mérete kisebb, mint 300 KB-os.

        - **Fejléc embléma.** Válassza ki az embléma jelenik meg a bejelentkezési oldalon, a felhasználó beírja a felhasználónevet és a egy .png vagy .jpg verzióját a **saját alkalmazások** portáloldalán.
            
            A lemezkép nem lehet magasabb, mint 36 képpont vagy nagyobb, mint 245 képpont. Azt javasoljuk, hogy transzparens rendszerkép használatával, mivel a háttér nem egyeznek az embléma háttér. Emellett javasoljuk, hogy ne adja hozzá a kép körül kitöltési, vagy lehet létrehozni, akkor keresse meg a kis méretű embléma.

        - **Felhasználónév-emlékeztető.** Írja be a segítő szöveg, amely a felhasználók számára akkor jelenik meg, ha elfelejtették a felhasználóneve. Ezt a szöveget kell lennie a Unicode használatát, hivatkozásokat vagy kód nélkül, és nem lehet hosszabb 64 karakternél. Ha vendégek jelentkezzen be az alkalmazásba, javasoljuk, hogy ne adja hozzá a mutatót.

        - **Bejelentkezési oldal szövege.** Írja be a bejelentkezési oldal alján megjelenő szöveg. Használhatja ezt a szöveget, további információt, például a telefonszám az ügyfélszolgálatot vagy egy jogi nyilatkozatot. Ez a szöveg Unicode legyen, és nem lehet hosszabb 256 karakternél. Emellett javasoljuk, hogy hivatkozásokat vagy HTML-címkéket.

    - **Speciális beállítások**
            
        ![Vállalati arculat megjelenítése a lapon befejeződött a Speciális beállítások konfigurálása](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Bejelentkezési oldal háttérszíne.** Adja meg hexadecimális szín (Ha például fehér a #FFFFFF), amely a háttérkép alacsony sávszélességű kapcsolat helyzetekben helyett megjelenítendő. A szalagcímembléma vagy a szervezet színes elsődleges színét használatát javasoljuk.

        - **Négyzetes emblémakép.** Válassza ki a (javasolt) .png vagy .jpg képet a szervezet embléma új Windows 10 Enterprise-eszközök esetén a telepítés során a felhasználók számára megjelenik. Ez a rendszerkép rendszer csak a Windows-hitelesítést használ, és akkor jelenik meg, csak a bérlők által használt [Windows Autopilot]( https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) telepítéshez vagy a jelszó megadásához egyéb Windows 10-es oldalainak észlel.
        
            A lemezkép nem lehet nagyobb, mint 240 x 240 képpont méretű, és rendelkeznie kell egy fájl mérete kisebb, mint 10 KB-os. Azt javasoljuk, hogy transzparens rendszerkép használatával, mivel a háttér nem egyeznek az embléma háttér. Emellett javasoljuk, hogy ne adja hozzá a kép körül kitöltési, vagy lehet létrehozni, akkor keresse meg a kis méretű embléma.
    
        - **Négyzetes emblémakép, sötét téma.** Ugyanaz, mint a fenti négyzetes emblémakép. Ez emblémakép történik a négyzetes emblémakép való együttes sötét háttérrel, például a Windows 10 Azure AD a csatlakoztatott képernyők out-of-box (OOBE) indítása során.  Ha az embléma fehér, sötét kék és a fekete háttérrel megfelelőnek tűnik, nem kell a lemezkép hozzáadása. 
        
        - **A bejelentkezve maradás lehetőségének megjelenítése.** Választhat, tudassa a felhasználókkal, amíg explicit módon lehet kijelentkezni az Azure ad bejelentkezve maradjanak. Ha úgy dönt, **nem**, ezt a beállítást rejtett, és a felhasználók minden alkalommal, amikor a böngésző bezárása és újból be kell jelentkeznie.
        
            >[!Note]
            >A SharePoint Online és az Office 2010 egyes funkciói attól függenek, hogy a felhasználók bejelentkezve tudnak-e maradni. Ha ennél a lehetőségnél a **Nem** értéket adja meg, a felhasználóinak további váratlan bejelentkezési felszólítások jelenhetnek meg.
   

3. Ha végzett a vállalat emblémájának hozzáadásával, válassza ki a **mentése**.

    Ha ezt a folyamatot hoz létre az első egyéni márkajelzést tartalmazó, az alapértelmezett válik a bérlő számára. Ha további konfigurációs van, beállíthatja az alapértelmezett konfiguráció fogja.
    
    >[!Important]
    >Továbbiak hozzáadásához Vállalati védjegyezés a bérlőn kívül konfigurációk ki kell választania **új nyelv** a a **Contoso - vállalati arculat** lapot. Ekkor megnyílik a **konfigurálása a vállalati arculatot** oldal, ahol a fenti ugyanazokat a lépéseket követheti.

## <a name="update-your-custom-branding"></a>Az egyéni védjegyezés frissítése
Miután létrehozta a egyéni márkajelzési beállításokat, lépjen vissza, és bármit módosítani.

### <a name="to-edit-your-custom-branding"></a>Az egyéni védjegyezés szerkesztése
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Válassza ki **Azure Active Directory**, majd válassza ki **vállalati védjegyezés**, majd válassza ki **konfigurálása**.

    ![Contoso – vállalati arculati oldala, amelyen látható alapértelmezett konfigurációja](media/customize-branding/company-branding-default-config.png)

3. Az a **konfigurálása a vállalati arculatot** lap, adja hozzá, távolítsa el vagy módosítsa az információkat, található leírások alapján a [az Azure AD bejelentkezési oldal testreszabása](#customize-your-azure-ad-sign-in-page) című szakaszát.

4. Kattintson a **Mentés** gombra.

  A bejelentkezési oldal arculatán alkalmazott módosítások megjelenítése akár egy órát is igénybe vehet.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Nyelvspecifikus vállalati arculat hozzáadása a címtárhoz
Az alapértelmezett nyelv, az eredeti konfiguráció nyelv nem módosítható. Ha más nyelven egy konfigurációs van szüksége, létrehozhat egy új konfigurációt.

### <a name="to-add-a-language-specific-branding-configuration"></a>A nyelvspecifikus márkajelzési konfiguráció hozzáadása

1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Válassza ki **Azure Active Directory**, majd válassza ki **vállalati védjegyezés**, majd válassza ki **új nyelv**.

    ![Contoso – vállalati arculati oldala, amelyen új nyelv lehetőség kiemelésével](media/customize-branding/company-branding-new-language.png)

3. Az a **konfigurálása a vállalati arculatot** lapon, válassza ki a nyelvet (például francia), majd adja hozzá a lefordított információ található leírások alapján a [az Azure AD bejelentkezési oldal testreszabása](#customize-your-azure-ad-sign-in-page) Ez a cikk szakaszában.

4. Kattintson a **Mentés** gombra.

    A **Contoso – a vállalati arculatot** frissítések megjelenítése az új francia konfiguráció lapon.

    ![Contoso – vállalati arculati oldala, amelyen látható alapértelmezett konfigurációja](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Az egyéni arculatot szeretne lapok hozzáadása
Adja hozzá az egyéni márkajelzési oldalakra módosításával az URL-CÍMÉT a szöveg végén `?whr=yourdomainname`. Ez a módosítás több oldalt, beleértve a multi-factor Authentication (MFA) beállítása lapon, az önkiszolgáló jelszó alaphelyzetbe állítása (SSPR) beállítása lapon és a bejelentkezési oldalon működik.

**Példák:**

**Eredeti URL-címe:** https://aka.ms/MFASetup<br>
**Egyéni URL-címe:** https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com

**Eredeti URL-címe:** https://aka.ms/SSPR<br>
**Egyéni URL-címe:** https://passwordreset.microsoftonline.com/?whr=contoso.com

 