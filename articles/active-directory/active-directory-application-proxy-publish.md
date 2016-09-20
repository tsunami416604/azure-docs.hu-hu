<properties
    pageTitle="Alkalmazások közzététele az Azure AD-alkalmazásproxyval | Microsoft Azure"
    description="Helyszíni alkalmazásait közzéteheti a felhőben az Azure AD-alkalmazásproxy használatával."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/19/2016"
    ms.author="kgremban"/>


# Alkalmazások közzététele az Azure AD-alkalmazásproxyval

Az Azure AD alkalmazásproxy segít a távoli feldolgozók támogatásában azáltal, hogy közzétesz helyszíni alkalmazásokat, és ezek elérhetővé válnak az interneten keresztül. Ennél a pontnál, már [engedélyezte az alkalmazásproxyt a klasszikus Azure-portálon](active-directory-application-proxy-enable.md). Ez a cikk végigvezeti a helyi hálózaton futó alkalmazások közzétételének és távoli, hálózaton kívüli biztonságos hozzáférésük kiépítésének lépésein. Miután befejezte a cikkben leírtakat, készen áll az alkalmazás személyre szabott információkkal vagy biztonsági követelményekkel történő konfigurálására.

> [AZURE.NOTE] Az alkalmazásproxy-szolgáltatás kizárólag akkor érhető el, ha frissített az Azure Active Directory Prémium vagy Alapszintű kiadására. További információk: [Azure Active Directory editions](active-directory-editions.md) (Azure Active Directory-kiadások).

## Alkalmazás közzététele a varázsló használatával

1. Jelentkezzen be rendszergazdaként a [klasszikus Azure portálra](https://manage.windowsazure.com/).
2. Lépjen az Active Directory területre, majd válassza ki azt a címtárat, amelyen az alkalmazásproxyt engedélyezte.

    ![Active Directory – ikon](./media/active-directory-application-proxy-publish/ad_icon.png)

3. Kattintson az **Alkalmazások** lapra, majd a képernyő alján található **Hozzáadás** gombra.

    ![Alkalmazás hozzáadása](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)

4. Válassza a **Publish an application that will be accessible from outside your network** (Hálózaton kívülről hozzáférhető alkalmazás közzététele) lehetőséget.

    ![Hálózaton kívülről hozzáférhető alkalmazás közzététele](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

5. Adja meg a következő információkat az alkalmazásról:

    - **Név**: Az alkalmazás felhasználóbarát neve. Ennek a címtáron belül egyedinek kell lennie.
    - **Belső URL-cím**: Az alkalmazásproxy összekötője által használt cím a magánhálózaton belülről történő alkalmazás-hozzáféréshez. Megadhat egyedi elérési utat a háttérkiszolgálón a közzétételhez, míg a kiszolgáló további része nem lesz közzétéve. Így különböző, ugyanazon a kiszolgálón található webhelyeket tehet közzé, és mindegyiknek saját nevet és hozzáférési szabályokat adhat.

        > [AZURE.TIP] Ha közzétesz egy útvonalat, győződjön meg róla, hogy az tartalmaz minden szükséges lemezképet, szkriptet és stíluslapot az alkalmazásához. Például ha az alkalmazása a https://yourapp/app helyen található, és a https://yourapp/media helyen található képeket használ, elérési útként a következőt javasolt közzétenni: https://yourapp/.

    - **Előhitelesítési módszer:** Az alkalmazásproxy által a felhasználók ellenőrzésére használt módszer az alkalmazás-hozzáférés engedélyezése előtt. A legördülő listában válasszon egyet a különböző lehetőségek közül.

        - Azure Active Directory: Az alkalmazásproxy átirányítja a felhasználókat az Azure AD bejelentkezési oldalára, ahol megtörténik a címtár és az alkalmazás használatára vonatkozó engedélyeik hitelesítése.
        - Átengedés: Nincs szükség a felhasználók hitelesítésére az alkalmazáshoz való hozzáféréshez.

    ![Az alkalmazás tulajdonságai](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)  

6. A varázsló bezárásához kattintson a képernyő alján található pipára. Az alkalmazás így már meg van adva az Azure AD-ben.


## Felhasználók és csoportok hozzárendelése az alkalmazáshoz

Ahhoz, hogy a felhasználók hozzáférjenek a közzétett alkalmazáshoz, egyénileg vagy csoportosan hozzá kell őket rendelni. (Ne feledkezzen meg a saját hozzáférésének hozzárendeléséről.) Ehhez arra van szükség, hogy minden felhasználó rendelkezzen Azure Basic vagy nagyobb licenccel. Licenceket egyénekhez vagy csoportokhoz is hozzárendelhet. További részletek: [Felhasználók hozzárendelése egy alkalmazáshoz](active-directory-applications-guiding-developers-assigning-users.md). 

Az előhitelesítést igénylő alkalmazások esetében ezzel jogosultságot is kapnak az alkalmazás használatára. Az előhitelesítést nem igénylő alkalmazások esetében is hozzárendelhetők a felhasználók az alkalmazáshoz, amely így megjelenik az alkalmazáslistájukban (például: MyApps).

1. Az Alkalmazás hozzáadása varázsló befejezését követően megjelenik az alkalmazás első lépéseinek oldala. Az alkalmazás-hozzáférés kezeléséhez válassza a **Felhasználók és csoportok** lehetőséget.

    ![Felhasználók hozzárendelése az alkalmazásproxy első lépései oldalán – képernyőfelvétel](./media/active-directory-application-proxy-publish/aad_appproxy_usersgroups.png)

2. Keressen rá a címtár adott csoportjára, vagy jelenítse meg az összes felhasználót. Kattintson a pipajelre a keresési eredmények megjelenítéséhez.

    ![Csoportok vagy felhasználók keresése – képernyőfelvétel](./media/active-directory-application-proxy-publish/aad_appproxy_search.png)

2. Válasszon ki minden olyan felhasználót vagy csoportot, amelyet hozzá szeretne rendelni az alkalmazáshoz, majd kattintson a **Hozzárendelés** gombra. E művelet végrehajtását meg kell erősítenie.

> [AZURE.NOTE] Integrált Windows-hitelesítésű alkalmazások esetében kizárólag a helyszíni Active Directoryból szinkronizált felhasználók és csoportok rendelhetők hozzá az alkalmazáshoz. A Microsoft-fiókkal bejelentkezett felhasználók és a vendégek nem rendelhetők hozzá az Azure Active Directory alkalmazásproxyjával közzétett alkalmazásokhoz. Győződjön meg arról, hogy a felhasználók ugyanannak a tartománynak a hitelesítő adataival jelentkeznek be, amelyhez a közzétett alkalmazás tartozik.

## A közzétett alkalmazás tesztelése

Miután közzétette az alkalmazást, tesztelheti azt úgy, hogy a közzétett URL-t megnyitja. Győződjön meg róla, hogy eléri az alkalmazást, az megfelelően jelenik meg, és minden a vártak szerint működik. Ha problémája van, vagy hibaüzenetet kap, próbálja meg a [hibaelhárítási útmutató](active-directory-application-proxy-troubleshoot.md) használatát.

## Az alkalmazás konfigurálása

A Konfigurálás oldalon módosíthatja a közzétett alkalmazásokat, illetve speciális beállításokat adhat meg. Ezen az oldalon a név módosításával vagy embléma feltöltésével testre is szabhatja az alkalmazást. Olyan hozzáférési szabályokat is kezelhet, mint például a előhitelesítési módszer vagy a többtényezős hitelesítés.

![Speciális konfiguráció](./media/active-directory-application-proxy-publish/aad_appproxy_configure.png)


Az Azure Active Directory alkalmazásproxyjával történő alkalmazás-közzétételt követően az alkalmazások megjelennek és kezelhetővé válnak az Azure AD alkalmazáslistájában.

Ha letiltja az alkalmazásproxy-szolgáltatásokat az alkalmazások közzétételét követően, azok a továbbiakban nem érhetők el a magánhálózaton kívülről. Ezzel nem törli az alkalmazásokat.

Egy alkalmazás megtekintéséhez és hozzáférhetőségének ellenőrzéséhez kattintson duplán az alkalmazás nevére. Ha az alkalmazásproxy le van tiltva, és az alkalmazás nem érhető el, a képernyő tetején figyelmeztető üzenet jelenik meg.

Egy alkalmazás törléséhez jelölje ki azt a listában, majd kattintson a **Törlés** gombra.

## Következő lépések

- [Alkalmazások közzététele saját tartománynév használatával](active-directory-application-proxy-custom-domains.md)
- [Egyszeri bejelentkezés engedélyezése](active-directory-application-proxy-sso-using-kcd.md)
- [Feltételes hozzáférés engedélyezése](active-directory-application-proxy-conditional-access.md)
- [Munkavégzés jogcímeket figyelembe vevő alkalmazásokkal](active-directory-application-proxy-claims-aware-apps.md)

A legújabb híreket és frissítéseket itt találja: [Alkalmazásproxy blog](http://blogs.technet.com/b/applicationproxyblog/).



<!--HONumber=sep16_HO1-->


