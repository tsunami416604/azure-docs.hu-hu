---
title: Windows 10 Nagyvállalati többmunkamenetes gyakori kérdések – Azure
description: Gyakori kérdések és gyakorlati tanácsok a Windows 10 Enterprise többmunkamenetes windowsos virtuális asztalhoz való használatával kapcsolatban.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d7ea99e2ee8e2882c211ee17acec70222dc058a8
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637102"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>A Windows 10 Enterprise több munkamenetes használata – gyakori kérdések

Ez a cikk választ ad a gyakran feltett kérdésekre, és ismerteti a Windows 10 Enterprise többmunkamenetes munkamenetekkel kapcsolatos gyakorlati tanácsait.
 
## <a name="what-is-windows-10-enterprise-multi-session"></a>Mi az a Windows 10 Enterprise többmunkamenetes munkamenet?

A Windows 10 Enterprise többmunkamenetes, korábbi nevén Windows 10 Enterprise for Virtual Desktops (EVD) egy új Távoli asztali munkamenetgazda, amely több egyidejű interaktív munkamenetet tesz lehetővé. Korábban csak a Windows Server képes volt erre. Ez a funkció ismerős Windows 10-élményt nyújt a felhasználóknak, míg az informatikai szolgáltatások élvezhetik a többmunkamenetes munkamenet ek árelőnyeit, és a távoli asztali ügyféllicencek helyett a meglévő felhasználónkénti Windows-licenceket használhatják. A licencekről és az árakról a [Windows virtuális asztal díjszabása](https://azure.microsoft.com/pricing/details/virtual-desktop/)című témakörben talál további információt. 
 
## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Hány felhasználó tarthat egyszerre interaktív munkamenetet a Windows 10 Enterprise többmunkamenetes munkamenetén?

Az, hogy egyszerre hány interaktív munkamenet lehet aktív, a rendszer hardvererőforrásaira (vCPU, memória, lemez és vGPU) támaszkodik, hogy a felhasználók hogyan használják az alkalmazásaikat a munkamenetbe való bejelentkezés során, és milyen nehéz a rendszer munkaterhelése. Javasoljuk, hogy ellenőrizze a rendszer teljesítményét, hogy megértse, hány felhasználója lehet a Windows 10 Enterprise többmunkamenetes munkamenetében. További információ: [Windows Virtual Desktop pricing](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>Miért jelenti az alkalmazás a Windows 10 Enterprise többmunkamenetes munkamenetet kiszolgálói operációs rendszerként?

A Windows 10 Enterprise többmunkamenetes munkamenet a Windows 10 Enterprise virtuális kiadása. Az egyik különbség az, hogy ez az operációs rendszer (OS) jelenti a [ProductType,](/windows/win32/cimwin32prov/win32-operatingsystem) mint amelynek értéke 3, azonos értékű, mint a Windows Server. Ez a tulajdonság az operációs rendszert kompatibilisen tartja a meglévő RDSH felügyeleti eszközökkel, az RDSH többmunkamenet-érzékeny alkalmazásokkal és a többnyire alacsony szintű rendszerteljesítmény-optimalizálással RDSH-környezetekben. Egyes alkalmazástelepítők blokkolhatják a windows 10-es többmunkamenetes telepítést attól függően, hogy észlelik-e, hogy a ProductType ügyfélre van állítva. Ha az alkalmazás nem telepíthető, kérjen frissített verziót az alkalmazás forgalmazójához. 
 
## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Futtatható a Windows 10 Enterprise többmunkamenetes helyszíni munkamenete?

A Windows 10 Enterprise többmunkamenetes munkamenete nem futtatható a helyszíni éles környezetben, mert az azure-hoz készült Windows Virtual Desktop szolgáltatásra van optimalizálva. A licencszerződés ellenében futtatja a Windows 10 Enterprise többmunkamenetes munkamenetet az Azure-on kívül éles környezetben. A Windows 10 Enterprise többmunkamenetes munkamenete nem aktiválódik a helyszíni kulcskezelő szolgáltatások (KMS) ellen.
 
## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>Hogyan szabhatom testre a Windows 10 Enterprise többmunkamenetes lemezképét a szervezetem számára?

Elindíthat egy virtuális gépet (VM) az Azure-ban a Windows 10 Windows 10 Enterprise többmunkamenetes munkamenettel, és testre szabhatja azt az ÜZLETÁG-alkalmazások, a sysprep/generalize telepítésével, majd létrehozhat egy lemezképet az Azure Portalon.  
 
Első lépésekhez hozzon létre egy virtuális gép az Azure-ban a Windows 10 Enterprise többmunkamenetes. Ahelyett, hogy a virtuális gép az Azure-ban, letöltheti a virtuális merevlemez közvetlenül. Ezt követően a letöltött virtuális merevlemez t, hogy hozzon létre egy új Generációs 1 virtuális gép a Windows 10 PC Hyper-V engedélyezve van.

Az üzletági alkalmazások telepítésével és a lemezkép sysprep telepítésével testreszabhatja a lemezképet az igényeinek megfelelően. Ha végzett a testreszabással, töltse fel a képet az Azure-ba a virtuális merevlemez belsejében. Ezt követően a Windows virtuális asztal az Azure Piactérről, és használja egy új gazdagépkészlet üzembe helyezéséhez a testreszabott lemezkép.
 
## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>Hogyan kezelhetem a Windows 10 Enterprise többmunkamenetes telepítését követően?

Bármely támogatott konfigurációs eszközt használhat, de a Configuration Manager 1906-os verzióját javasoljuk, mert támogatja a Windows 10 Enterprise többmunkamenetes munkamenetet. Jelenleg dolgozunk a Microsoft Intune támogatásán.
 
## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Csatlakozhat-e a Windows 10 Enterprise többmunkamenetes többmunkamenetes csatlakozású az Azure Active Directoryhoz?!

A Windows 10 Enterprise többmunkamenetes munkamenet jelenleg támogatott hibrid Azure AD-csatlakozással. Miután a Windows 10 Enterprise többmunkamenetes tartományhoz csatlakozott, használja a meglévő csoportházirend-objektum az Azure AD-regisztráció engedélyezéséhez. További információ: [A hibrid Azure Active Directory-csatlakozás megvalósításának megtervezése.](../active-directory/devices/hybrid-azuread-join-plan.md)
 
## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Hol találom a Windows 10 Enterprise többmunkamenetes lemezképét?

A Windows 10 Enterprise többmunkamenetes munkamenete az Azure-galériában található. A kereséshez keresse meg az Azure Portalt, és keresse meg a Windows 10 Enterprise for Virtual Desktops kiadását. Az Office Pro Plus alkalmazásba integrált lemezképekért keresse meg az Azure portált, és keresse meg a Microsoft Windows 10 + Office 365 ProPlus alkalmazást.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>Melyik Windows 10 Enterprise többmunkamenetes lemezképet használjam?

Az Azure-gyűjtemény számos kiadással rendelkezik, többek között a Windows 10 Enterprise többmunkamenetes, 1809-es és Windows 10 Enterprise többmunkamenetes, 1903-as verziójával. A jobb teljesítmény és megbízhatóság érdekében a legújabb verzió használatát javasoljuk.
 
## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>Mely Windows 10 Enterprise többmunkamenetes verziók támogatottak?

A Windows 10 Enterprise többmunkamenetes, 1809-es és újabb verziói támogatottak, és elérhetők az Azure-galériában. Ezek a kiadások ugyanazt a támogatási életciklus-házirendet követik, mint a Windows 10 Enterprise, ami azt jelenti, hogy a tavaszi kiadás 18 hónapig, az őszi kiadás pedig 30 hónapig támogatott.
 
## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Melyik profilkezelési megoldást használjam a Windows 10 Enterprise többmunkamenetes munkamenetéhez?

Azt javasoljuk, hogy fslogix profiltárolókat használjon, amikor a Windows 10 Enterprise szolgáltatást nem állandó környezetekben vagy más, központilag tárolt profilra szoruló forgatókönyvekben konfigurálja. Az FSLogix biztosítja, hogy a felhasználói profil minden felhasználói munkamenethez elérhető és naprakész legyen. Azt is javasoljuk, hogy használja az FSLogix-profiltárolótároljon egy felhasználói profilt bármely SMB-megosztáson a megfelelő engedélyekkel, de szükség esetén tárolhatja a felhasználói profilokat az Azure-ban a lapblob-tárolóban. A Windows virtual desktop felhasználói további költségek nélkül használhatják az FSLogix-ot.
 
Az FSLogix profiltároló konfigurálásáról az [FSLogix profiltároló konfigurálása](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)című témakörben talál további információt.  

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Milyen licencre van szükségem a Windows 10 Enterprise többmunkamenetes eléréséhez?

A vonatkozó licencek teljes listáját a [Windows virtuális asztal díjszabása ( Windows Virtual Desktop ) díjszabása](https://azure.microsoft.com/pricing/details/virtual-desktop/)tartalmazza.

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>Miért tűnnek el az alkalmazásaim a kijelentkezés után?

Ez azért történik, mert a Windows 10 Enterprise többmunkamenetes munkamenetét olyan profilkezelési megoldással használja, mint az FSLogix. A rendszergazdai vagy profilmegoldás úgy konfigurálta a rendszert, hogy törölje a felhasználói profilokat, amikor a felhasználók kijelentkeznek. Ez a konfiguráció azt jelenti, hogy amikor a rendszer a kijelentkezés után törli a felhasználói profilt, akkor a munkamenet során telepített alkalmazásokat is eltávolítja. Ha meg szeretné tartani a telepített alkalmazásokat, meg kell kérnie a rendszergazdát, hogy ezeket az alkalmazásokat a Windows Virtuális asztal környezetében lévő összes felhasználó számára hozza létre.

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>Hogyan győződhetek meg arról, hogy az alkalmazások nem tűnnek el, amikor a felhasználók kijelentkeznek?

A legtöbb virtualizált környezet alapértelmezés szerint úgy van beállítva, hogy a felhasználók ne telepítsenek további alkalmazásokat a profiljukba. Ha meg szeretne győződni arról, hogy egy alkalmazás nem tűnik el, amikor a felhasználó kijelentkezik a Windows virtuális asztalról, ki kell építenie azt az alkalmazást a környezetében lévő összes felhasználói profilhoz. Az alkalmazások kiépítésével kapcsolatos további információkért tekintse meg az alábbi forrásokat:

- [Beépített alkalmazások közzététele a Windows Virtuális asztalon](publish-apps.md)
- [A DISM alkalmazáscsomag karbantartásának parancssori beállításai](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [Add-AppxProvisionedPackage](https://docs.microsoft.com/powershell/module/dism/add-appxprovisionedpackage?view=win10-ps)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>Hogyan győződhetek meg arról, hogy a felhasználók nem töltenek le és nem telepítenek alkalmazásokat a Microsoft Store-ból?

Letilthatja a Microsoft Store alkalmazást, így biztosíthatja, hogy a felhasználók ne töltsenek le további alkalmazásokat a számukra már kiépített alkalmazásokon kívül.

Az Áruház alkalmazás letiltása:

1. Új csoportházirend létrehozása.
2. Válassza a **Számítógép konfigurációja** > **felügyeleti sablonok** > **Windows-összetevők lehetőséget.**
3. Válassza **az Áruház**lehetőséget.
4. Válassza az **Áruházi alkalmazás lehetőséget.**
5. Válassza **a Letiltva**lehetőséget, majd az **OK**gombot.
6. Kattintson az **Alkalmaz** gombra.
 
## <a name="next-steps"></a>További lépések

További információ a Windows Virtuális asztalról és a Windows 10 Enterprise többmunkamenetes munkamenetről:

- Olvassa el a [Windows virtuális asztal előzetes verziójának dokumentációját](overview.md)
- Látogassa meg a [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- A Windows virtuális asztal központi telepítésének beállítása a [Windows virtuális asztal oktatóanyagaival](tenant-setup-azure-active-directory.md)
