<properties
    pageTitle="Alkalmazások közzététele az Azure AD-alkalmazásproxyval | Microsoft Azure"
    description="Helyszíni alkalmazásait közzéteheti a felhőben az Azure AD-alkalmazásproxy használatával."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/01/2016"
    ms.author="kgremban"/>


# Alkalmazások közzététele az Azure AD-alkalmazásproxyval


A Microsoft Azure Active Directory (AD) alkalmazásproxy engedélyezését követően közzétehet helyszíni alkalmazásokat, így a távoli felhasználók a magánhálózaton kívülről is hozzájuk férhetnek.

Ez a cikk végigvezeti a helyi hálózaton futó alkalmazások közzétételének és távoli, hálózaton kívüli biztonságos hozzáférésük kiépítésének lépésein. Ha még nem állította be az alkalmazásproxyt vagy nem telepített egyetlen összekötőt sem, az itt leírtak folytatása előtt kövesse az [Alkalmazásproxy engedélyezése az Azure-portálon](active-directory-application-proxy-enable.md) cikkben ismertetett lépéseket.

Ha most használ először Azure AD-alkalmazásproxyt, alkalmazások közzététele előtt javasoljuk az összekötő tesztelését azzal, hogy közzétesz egy webhelyet a magánhálózatáról.

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
    - **Előhitelesítési módszer**: Az alkalmazásproxy által a felhasználók ellenőrzésére használt módszer az alkalmazás-hozzáférés engedélyezése előtt. A legördülő listában válasszon egyet a különböző lehetőségek közül.

        - Azure Active Directory: Az alkalmazásproxy átirányítja a felhasználókat az Azure AD bejelentkezési oldalára, ahol megtörténik a címtár és az alkalmazás használatára vonatkozó engedélyeik hitelesítése.
        - Átengedés: Nincs szükség a felhasználók hitelesítésére az alkalmazáshoz való hozzáféréshez.

    ![Az alkalmazás tulajdonságai](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)  

6. A varázsló bezárásához kattintson a képernyő alján található pipára. Az alkalmazás így már meg van adva az Azure AD-ben.


## Felhasználók és csoportok hozzárendelése az alkalmazáshoz

Ahhoz, hogy a felhasználók hozzáférjenek a közzétett alkalmazáshoz, egyénileg vagy csoportosan hozzá kell őket rendelni. Az előhitelesítést igénylő alkalmazások esetében ezzel jogosultságot is kapnak az alkalmazás használatára. Az előhitelesítést nem igénylő alkalmazások esetében a felhasználóknak nincs szükségük engedélyekre, de ez esetben is hozzá kell rendelni őket az adott alkalmazáshoz, ami megjelenik az alkalmazáslistájukban.

1. Az Alkalmazás hozzáadása varázsló befejezését követően megjelenik az alkalmazás első lépéseinek oldala. Az alkalmazás-hozzáférés kezeléséhez válassza a **Felhasználók és csoportok** lehetőséget.

    ![Felhasználók hozzárendelése az alkalmazásproxy első lépései oldalán – képernyőfelvétel](./media/active-directory-application-proxy-publish/aad_appproxy_usersgroups.png)

2. Keressen rá a címtár adott csoportjára, vagy jelenítse meg az összes felhasználót. Kattintson a pipa jelre az eredmények megjelenítéséhez.

    ![Csoportok vagy felhasználók keresése – képernyőfelvétel](./media/active-directory-application-proxy-publish/aad_appproxy_search.png)

2. Válasszon ki minden olyan felhasználót vagy csoportot, amelyet hozzá szeretne rendelni az alkalmazáshoz, majd kattintson a **Hozzárendelés** gombra. A rendszer fel fogja kérni, hogy erősítse meg ezt a műveletet.

> [AZURE.NOTE] Integrált Windows-hitelesítésű alkalmazások esetében kizárólag a helyszíni Active Directoryból szinkronizált felhasználók és csoportok rendelhetők hozzá az alkalmazáshoz. A Microsoft-fiókkal bejelentkezett felhasználók és a vendégek nem rendelhetők hozzá az Azure Active Directory alkalmazásproxyjával közzétett alkalmazásokhoz. Győződjön meg arról, hogy a felhasználók ugyanannak a tartománynak a hitelesítő adataival jelentkeznek be, amelyhez a közzétett alkalmazás tartozik.


## Speciális konfiguráció

A Konfigurálás oldalon módosíthatja a közzétett alkalmazásokat, illetve speciális beállításokat adhat meg. Ezen az oldalon a név módosításával vagy embléma feltöltésével testre is szabhatja az alkalmazást. Olyan hozzáférési szabályokat is kezelhet, mint például a előhitelesítési módszer vagy a többtényezős hitelesítés.

![Speciális konfiguráció](./media/active-directory-application-proxy-publish/aad_appproxy_configure.png)


Az Azure Active Directory alkalmazásproxyjával történő alkalmazás-közzétételt követően az alkalmazások megjelennek és kezelhetővé válnak az Azure AD alkalmazáslistájában.

Ha letiltja az alkalmazásproxy-szolgáltatásokat az alkalmazások közzétételét követően, az alkalmazások nem törlődnek, de a továbbiakban nem lesznek hozzáférhetőek a magánhálózaton kívülről.

Egy alkalmazás megtekintéséhez és hozzáférhetőségének biztosításához kattintson duplán az alkalmazás nevére. Ha az alkalmazásproxy le van tiltva, és az alkalmazás nem érhető el, a képernyő tetején figyelmeztető üzenet jelenik meg.

Egy alkalmazás törléséhez jelölje ki azt a listában, majd kattintson a **Törlés** gombra.

## További lépések

- [Alkalmazások közzététele saját tartománynév használatával](active-directory-application-proxy-custom-domains.md)
- [Egyszeri bejelentkezés engedélyezése](active-directory-application-proxy-sso-using-kcd.md)
- [Feltételes hozzáférés engedélyezése](active-directory-application-proxy-conditional-access.md)
- [Munkavégzés jogcímeket figyelembe vevő alkalmazásokkal](active-directory-application-proxy-claims-aware-apps.md)

A legújabb híreket és frissítéseket itt találja: [Alkalmazásproxy blog](http://blogs.technet.com/b/applicationproxyblog/).



<!--HONumber=Jun16_HO2-->


