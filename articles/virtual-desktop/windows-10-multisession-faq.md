---
title: Windows 10 Enterprise multi-session GYIK – Azure
description: Gyakori kérdések és ajánlott eljárások a Windows 10 Enterprise multi-session for Windows Virtual Desktop használatához.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: helohr
ms.openlocfilehash: 10724407b8ba5568b38a844f2bf475060e2b7699
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227667"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>A Windows 10 Enterprise több munkamenetes használata – gyakori kérdések

Ez a cikk a gyakori kérdések megválaszolásával és a Windows 10 Enterprise multi-Sessions szolgáltatással kapcsolatos ajánlott eljárásokat tárgyalja.
 
## <a name="what-is-windows-10-enterprise-multi-session"></a>Mi a Windows 10 Enterprise több munkamenet? 

A Windows 10 Enterprise multi-session (korábbi nevén Windows 10 Enterprise for Virtual desktops) egy új Távoli asztal EVD, amely egyszerre több egyidejű interaktív munkamenetet tesz lehetővé, amelyek korábban csak a Windows Servert tudták elvégezni. Ez a funkció lehetővé teszi a felhasználók számára a Windows 10 ismerős élményét, miközben a több munkamenetre kiterjedő, illetve a meglévő felhasználónkénti Windows-licencelést használja az RDS ügyféllicencek (CAL) helyett. A licencekkel és a díjszabással kapcsolatos további információkért lásd: a [Windows rendszerű virtuális asztali díjszabása](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Hány felhasználó tud egyidejűleg interaktív munkamenetet összekapcsolni a Windows 10-es nagyvállalati munkamenetben?

Hány interaktív munkamenet lehet aktív egyszerre a rendszer hardveres erőforrásaira (vCPU, memória, lemez és vGPU), hogy a felhasználók hogyan használják az alkalmazásaikat a munkamenetbe való bejelentkezve, és hogy mekkora a rendszer számítási feladatai. Javasoljuk, hogy ellenőrizze a rendszer teljesítményét, hogy megtudja, hány felhasználóval rendelkezhet a Windows 10 Enterprise több munkamenetben. További információ: a [Windows virtuális asztal díjszabása](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>Miért jelent az alkalmazásom a Windows 10-es nagyvállalati többmunkamenetes kiszolgáló operációs rendszert?

A Windows 10 Enterprise multi-session a Windows 10 Enterprise virtuális kiadása. Az egyik különbség az, hogy ez az operációs rendszer (OS) a [ProductType](https://docs.microsoft.com/windows/desktop/cimwin32prov/win32-operatingsystem) a 3 értékkel, a Windows Serverrel megegyező értékkel jelenti. Ezzel a tulajdonsággal az operációs rendszer kompatibilis a meglévő RDSH-kezelési eszközökkel, RDSH több munkamenetet támogató alkalmazásokat, és többnyire alacsony szintű rendszerteljesítmény-optimalizálást biztosít a RDSH környezetekhez. Egyes alkalmazás-telepítők letilthatják a telepítést a Windows 10-es rendszeren attól függően, hogy a ProductType az ügyfélre van-e állítva. Ha az alkalmazás nem lesz telepítve, a frissített verzióért forduljon az alkalmazás forgalmazójához. 
 
## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Futtathatom a Windows 10 Enterprise multi-session szolgáltatást a helyszínen?

A Windows 10 Enterprise rendszerű több munkamenet nem futtatható helyszíni éles környezetekben, mert az az Azure-hoz készült Windows Virtual Desktop szolgáltatáshoz van optimalizálva. Ez a licencszerződés arra vonatkozik, hogy az Azure-on kívüli Windows 10 Enterprise-munkamenetet az éles környezetben futtassa. A Windows 10 Enterprise multi-session szolgáltatás nem aktiválja a helyszíni kulcskezelő szolgáltatásokat (KMS).
 
## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>Hogyan testre szabhatja a Windows 10 Enterprise multi-session rendszerképet a saját szervezetem számára?

Elindíthat egy virtuális gépet (VM) az Azure-ban a Windows 10 rendszerű Windows 10 Enterprise multi-session modullal, és testre szabhatja a LOB-alkalmazások, a Sysprep/általánosítások telepítésével, majd a rendszerkép létrehozásával a Azure Portal használatával.  
 
Első lépésként hozzon létre egy virtuális gépet az Azure-ban a Windows 10 Windows 10 Enterprise multi-session. A virtuális gép Azure-beli elindítása helyett közvetlenül letöltheti a virtuális merevlemezt. Ezután használhatja a letöltött VHD-t egy új generációs 1 virtuális gép létrehozásához egy Windows 10 rendszerű számítógépen, amelyen engedélyezve van a Hyper-V.

A rendszerképet a LOB-alkalmazások telepítésével és a Sysprep eszközzel testreszabhatja igényei szerint. Ha elkészült a testreszabással, töltse fel a rendszerképet az Azure-ba a virtuális merevlemezen belül. Ezt követően szerezze be a Windows rendszerű virtuális asztalt az Azure Marketplace-ről, és használja azt egy új címkészlet üzembe helyezéséhez a testreszabott rendszerkép használatával.
 
## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>Az üzembe helyezést követően Hogyan a Windows 10 Enterprise multi-session-munkamenet kezelése?

Bármilyen támogatott konfigurációs eszközt használhat, de azt javasoljuk, hogy System Center Configuration Manager 1906, mivel ez a Windows 10 Enterprise több munkamenetet is támogatja. Jelenleg Microsoft Intune támogatással dolgozunk.
 
## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Lehet a Windows 10 Enterprise multi-session Azure Active Directory (AD)-hez csatlakoztatva?

A Windows 10 Enterprise multi-session jelenleg támogatott hibrid Azure AD-hez. Ha a Windows 10 Enterprise multi-session a tartományhoz van csatlakoztatva, a meglévő Csoportházirend objektum használatával engedélyezheti az Azure AD-regisztrációt. További információ: [a hibrid Azure Active Directory csatlakoztatásának megtervezése](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan).
 
## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Hol találhatom meg a Windows 10 Enterprise multi-session rendszerképet?

A Windows 10 Enterprise multi-session az Azure-katalógusban található. A kereséshez keresse meg a Azure Portal, és keresse meg a Windows 10 Enterprise for Virtual desktops kiadását. Az Office Pro Plus szolgáltatással integrált rendszerképekhez lépjen a Azure Portalra, és keresse meg a Microsoft Windows 10 + Office 365 ProPlus.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>Melyik Windows 10 Enterprise több munkamenetből álló képet szeretném használni?

Az Azure Gallery számos kiadással rendelkezik, többek között a Windows 10 Enterprise multi-session, a 1809-es és a Windows 10 Enterprise multi-session, a 1903-es verzióval. A jobb teljesítmény és megbízhatóság érdekében javasoljuk, hogy a legújabb verziót használja.
 
## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>Mely Windows 10 Enterprise több munkamenetes verzió támogatott?

A Windows 10 Enterprise multi-session, a 1809-es és újabb verziói támogatottak, és elérhetők az Azure Galleryben. Ezek a kiadások a Windows 10 Enterprise verzióval azonos támogatási életciklus-szabályzatot követnek, ami azt jelenti, hogy a Spring kiadás 18 hónapig, a csökkenés pedig 30 hónapig érvényes.
 
## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Milyen profil-felügyeleti megoldást használhatok a Windows 10 Enterprise multi-session szolgáltatáshoz?

Javasoljuk, hogy használjon FSLogix-profilok tárolókat, ha a Windows 10-es vállalatot nem állandó környezetekben konfigurálja, vagy olyan egyéb helyzetekben, amelyeknek központilag tárolt profilra van szükségük. A FSLogix biztosítja a felhasználói profil elérhetőségét és naprakészen tartása minden felhasználói munkamenethez. Javasoljuk továbbá, hogy a FSLogix-profil tárolójában tárolja a megfelelő engedélyekkel rendelkező SMB-megosztásokban lévő felhasználói profilokat, de szükség esetén a felhasználói profilokat az Azure-oldal blob Storage-ban is tárolhatja. A Windows rendszerű virtuális asztali felhasználók további díjak nélkül használhatják a FSLogix.
 
A FSLogix-profilok tárolójának konfigurálásával kapcsolatos további információkért lásd: [a FSLogix-profil tárolójának konfigurálása](create-host-pools-user-profile.md#configure-the-fslogix-profile-container).  

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Milyen licencre van szükségem a Windows 10 Enterprise multi-session szolgáltatáshoz?

A megfelelő licencek teljes listáját a [Windows rendszerű virtuális asztali díjszabás](https://azure.microsoft.com/pricing/details/virtual-desktop/)című témakörben tekintheti meg.
 
## <a name="next-steps"></a>Következő lépések

További információ a Windows rendszerű virtuális asztali környezetről és a Windows 10-es nagyvállalati multi-session szolgáltatásról:

- Olvassa el a [Windows rendszerű virtuális asztali előnézet dokumentációját](overview.md)
- Látogasson el a [Windows rendszerű virtuális asztali TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Windows rendszerű virtuális asztali környezet beállítása a [Windows rendszerű virtuális asztali oktatóanyagokkal](tenant-setup-azure-active-directory.md)
