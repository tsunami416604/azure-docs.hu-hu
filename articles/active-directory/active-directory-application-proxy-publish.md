---
title: "Alkalmazások közzététele az Azure AD-alkalmazásproxyval | Microsoft Docs"
description: "Helyszíni alkalmazásait közzéteheti a felhőben az Azure AD-alkalmazásproxy használatával a klasszikus portálon."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/04/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 526efc5b025f8050f40c49d0a07a9c2713509493
ms.contentlocale: hu-hu
ms.lasthandoff: 05/05/2017


---
# <a name="publish-applications-using-azure-ad-application-proxy"></a>Alkalmazások közzététele az Azure AD-alkalmazásproxyval

> [!div class="op_single_selector"]
> * [Azure Portal](application-proxy-publish-azure-portal.md)
> * [klasszikus Azure portál](active-directory-application-proxy-publish.md)

Az Azure AD alkalmazásproxy segít a távoli feldolgozók támogatásában azáltal, hogy közzétesz helyszíni alkalmazásokat, és ezek elérhetővé válnak az interneten keresztül. Ennél a pontnál, már [engedélyezte az alkalmazásproxyt a klasszikus Azure-portálon](active-directory-application-proxy-enable.md). Ez a cikk végigvezeti a helyi hálózaton futó alkalmazások közzétételének és távoli, hálózaton kívüli biztonságos hozzáférésük kiépítésének lépésein. Miután befejezte a cikkben leírtakat, készen áll az alkalmazás személyre szabott információkkal vagy biztonsági követelményekkel történő konfigurálására.

> [!NOTE]
> Az alkalmazásproxy-szolgáltatás kizárólag akkor érhető el, ha frissített az Azure Active Directory Prémium vagy Alapszintű kiadására. További információk: [Azure Active Directory editions](active-directory-editions.md) (Azure Active Directory-kiadások). Ha szeretné az alkalmazásproxyt használni, ezt megteheti az [alkalmazások közzétételével az Azure Portalban](application-proxy-publish-azure-portal.md).

## <a name="publish-an-app-using-the-wizard"></a>Alkalmazás közzététele a varázsló használatával
1. Jelentkezzen be rendszergazdaként a [klasszikus Azure portálra](https://manage.windowsazure.com/).
2. Lépjen az Active Directory területre, majd válassza ki azt a címtárat, amelyen az alkalmazásproxyt engedélyezte.
   
    ![Active Directory – ikon](./media/active-directory-application-proxy-publish/ad_icon.png)
3. Kattintson az **Alkalmazások** lapra, majd a képernyő alján található **Hozzáadás** gombra.
   
    ![Alkalmazás hozzáadása](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)
4. Válassza a **Publish an application that will be accessible from outside your network** (Hálózaton kívülről hozzáférhető alkalmazás közzététele) lehetőséget.
   
    ![Hálózaton kívülről hozzáférhető alkalmazás közzététele](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)
5. Adja meg a következő információkat az alkalmazásról:
   
   * **Név**: Az alkalmazás felhasználóbarát neve. Ennek a címtáron belül egyedinek kell lennie.
   * **Belső URL-cím**: Az alkalmazásproxy összekötője által használt cím a magánhálózaton belülről történő alkalmazás-hozzáféréshez. Megadhat egyedi elérési utat a háttérkiszolgálón a közzétételhez, míg a kiszolgáló további része nem lesz közzétéve. Így különböző, ugyanazon a kiszolgálón található webhelyeket tehet közzé, és mindegyiknek saját nevet és hozzáférési szabályokat adhat.
     
     > [!TIP]
     > Ha közzétesz egy útvonalat, győződjön meg róla, hogy az tartalmaz minden szükséges lemezképet, szkriptet és stíluslapot az alkalmazásához. Például ha az alkalmazása a https://yourapp/app helyen található, és a https://yourapp/media helyen található képeket használ, elérési útként a következőt javasolt közzétenni: https://yourapp/.
     > 
     > 
   * **Előhitelesítési módszer:** Az alkalmazásproxy által a felhasználók ellenőrzésére használt módszer az alkalmazás-hozzáférés engedélyezése előtt. A legördülő listában válasszon egyet a különböző lehetőségek közül.
     
     * Azure Active Directory: Az alkalmazásproxy átirányítja a felhasználókat az Azure AD bejelentkezési oldalára, ahol megtörténik a címtár és az alkalmazás használatára vonatkozó engedélyeik hitelesítése.
     * Átengedés: Nincs szükség a felhasználók hitelesítésére az alkalmazáshoz való hozzáféréshez.
     
     ![Az alkalmazás tulajdonságai](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)  
6. A varázsló bezárásához kattintson a képernyő alján található pipára. Az alkalmazás így már meg van adva az Azure AD-ben.

## <a name="assign-users-and-groups-to-the-application"></a>Felhasználók és csoportok hozzárendelése az alkalmazáshoz
Ahhoz, hogy a felhasználók hozzáférjenek a közzétett alkalmazáshoz, egyénileg vagy csoportosan hozzá kell őket rendelni. (Ne feledkezzen meg a saját hozzáférésének hozzárendeléséről.) Ehhez arra van szükség, hogy minden felhasználó rendelkezzen Azure Basic vagy nagyobb licenccel. Licenceket egyénekhez vagy csoportokhoz is hozzárendelhet. További részletek: [Felhasználók hozzárendelése egy alkalmazáshoz](active-directory-applications-guiding-developers-assigning-users.md). 

Az előhitelesítést igénylő alkalmazások esetében ezzel jogosultságot is kapnak az alkalmazás használatára. Az előhitelesítést nem igénylő alkalmazások esetében is hozzárendelhetők a felhasználók az alkalmazáshoz, amely így megjelenik az alkalmazáslistájukban (például: MyApps).

1. Az Alkalmazás hozzáadása varázsló befejezését követően megjelenik az alkalmazás első lépéseinek oldala. Az alkalmazás-hozzáférés kezeléséhez válassza a **Felhasználók és csoportok** lehetőséget.
   
    ![Felhasználók hozzárendelése az alkalmazásproxy első lépései oldalán – képernyőfelvétel](./media/active-directory-application-proxy-publish/aad_appproxy_usersgroups.png)
2. Keressen rá a címtár adott csoportjára, vagy jelenítse meg az összes felhasználót. Kattintson a pipajelre a keresési eredmények megjelenítéséhez.
   
      ![Csoportok vagy felhasználók keresése – képernyőfelvétel](./media/active-directory-application-proxy-publish/aad_appproxy_search.png)
3. Válasszon ki minden olyan felhasználót vagy csoportot, amelyet hozzá szeretne rendelni az alkalmazáshoz, majd kattintson a **Hozzárendelés** gombra. E művelet végrehajtását meg kell erősítenie.

> [!NOTE]
> Integrált Windows-hitelesítésű alkalmazások esetében kizárólag a helyszíni Active Directoryból szinkronizált felhasználók és csoportok rendelhetők hozzá az alkalmazáshoz. A Microsoft-fiókkal bejelentkezett felhasználók és a vendégek nem rendelhetők hozzá az Azure Active Directory alkalmazásproxyjával közzétett alkalmazásokhoz. Győződjön meg arról, hogy a felhasználók ugyanannak a tartománynak a hitelesítő adataival jelentkeznek be, amelyhez a közzétett alkalmazás tartozik.
> 
> 

## <a name="test-your-published-application"></a>A közzétett alkalmazás tesztelése
Miután közzétette az alkalmazást, tesztelheti azt úgy, hogy a közzétett URL-t megnyitja. Győződjön meg róla, hogy az alkalmazás elérhető, megfelelően jelenik meg, és minden a vártak szerint működik. Ha problémája van, vagy hibaüzenetet kap, próbálja meg a [hibaelhárítási útmutató](active-directory-application-proxy-troubleshoot.md) használatát.

## <a name="configure-your-application"></a>Az alkalmazás konfigurálása
A Konfigurálás oldalon módosíthatja a közzétett alkalmazásokat, illetve speciális beállításokat adhat meg. Ezen az oldalon a név módosításával vagy embléma feltöltésével testre is szabhatja az alkalmazást. Olyan hozzáférési szabályokat is kezelhet, mint például a előhitelesítési módszer vagy a többtényezős hitelesítés.

![Speciális konfiguráció](./media/active-directory-application-proxy-publish/aad_appproxy_configure.png)

Az Azure Active Directory alkalmazásproxyjával történő alkalmazás-közzétételt követően az alkalmazások megjelennek és kezelhetővé válnak az Azure AD alkalmazáslistájában.

Ha letiltja az alkalmazásproxy-szolgáltatásokat az alkalmazások közzétételét követően, azok a továbbiakban nem érhetők el a magánhálózaton kívülről. Ezzel nem törli az alkalmazásokat.

Egy alkalmazás megtekintéséhez és hozzáférhetőségének ellenőrzéséhez kattintson duplán az alkalmazás nevére. Ha az alkalmazásproxy le van tiltva, és az alkalmazás nem érhető el, a képernyő tetején figyelmeztető üzenet jelenik meg.

Egy alkalmazás törléséhez jelölje ki azt a listában, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések
* [Alkalmazások közzététele saját tartománynév használatával](active-directory-application-proxy-custom-domains.md)
* [Egyszeri bejelentkezés engedélyezése](active-directory-application-proxy-sso-using-kcd.md)
* [Feltételes hozzáférés engedélyezése](active-directory-application-proxy-conditional-access.md)
* [Munkavégzés jogcímeket figyelembe vevő alkalmazásokkal](active-directory-application-proxy-claims-aware-apps.md)

A legújabb híreket és frissítéseket itt találja: [Alkalmazásproxy blog](http://blogs.technet.com/b/applicationproxyblog/).


