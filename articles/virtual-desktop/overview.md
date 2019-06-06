---
title: Mi a Windows virtuális asztal előzetes verziója?  – Azure
description: Windows virtuális asztal előzetes verziójának áttekintése.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 05/31/2019
ms.author: helohr
ms.openlocfilehash: 296ea271e88dfbbd91b901dc1b24d49fc31c139e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476726"
---
# <a name="what-is-windows-virtual-desktop-preview"></a>Mi a Windows virtuális asztal előzetes verziója? 

Mostantól elérhető nyilvános előzetes verzióban érhető el Windows virtuális asztal előzetes egy olyan asztali és alkalmazás virtualizációs szolgáltatás, amely a felhőben futtat.

Íme, mi mindent az Azure-ban Windows virtuális asztal futtatásakor:

* Állítsa be, amely egy teljes Windows 10-es méretezhetőséget kínál több munkamenet a Windows 10 központi telepítés
* Az Office 365 ProPlus virtualizálását, és optimalizálhatja a többfelhasználós virtuális forgatókönyvek futtatását
* Adja meg a Windows 7 virtuális asztalok együtt ingyenes kibővített biztonsági frissítések
* Minden számítógép, a meglévő távoli asztali szolgáltatások (RDS) és a Windows Server asztali környezet és az alkalmazások használata
* Asztali számítógép és az alkalmazások virtualizálása
* A Windows 10, Windows Server és Windows 7 rendszerű asztali számítógépek és egységes felügyeleti kezelőfelület-alkalmazások kezelése

## <a name="introductory-video"></a>Bevezető videó

Az alábbi videó Scott Manchester mutat be, bizonyos Windows virtuális asztal képességeket:

<br></br><iframe src="https://www.youtube-nocookie.com/embed/30dOLcZ4_9U" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

## <a name="key-capabilities"></a>Főbb képességek

A Windows virtuális asztal beállíthat egy méretezhető és rugalmas környezet:

* Teljes asztali virtualizálási környezet létrehozása az Azure-előfizetésében további átjárókiszolgálókat futtatása nélkül.
* Számos készletek üzemeltetésére, hogy megfeleljen a különböző számítási feladatok igény szerint közzétegye.
* Saját rendszerkép használata a termelési számítási feladatokhoz, vagy tesztelje az Azure katalógusából.
* Csökkentheti a költségeket a készletbe vont, több munkamenet-erőforrásokkal. Az új Windows 10 Enterprise több munkamenet képesség exkluzív Windows virtuális asztal és a távoli asztali munkamenetgazda (RDSH) szerepkör, Windows Server, a nagy mértékben csökkentheti a virtuális gépek és az operációs rendszer (OS) számát többletterhelést miközben továbbra is ugyanazokat az erőforrásokat biztosít a felhasználók számára.
* Adja meg a személyes (állandó) asztalok egyes tulajdonjogát.

Telepítheti és virtuális asztalok kezelése:

* A gazdagép-címkészleteket, az alkalmazás létrehozása, hozzárendelése a felhasználók és erőforrások közzétételét a Windows virtuális asztal PowerShell és a REST felületeken használatával.
* Teljes asztali vagy egyéni egyetlen gazdagép-készletből távoli alkalmazások közzététele, hozza létre az egyes alkalmazásokra csoportokat a felhasználók más-más részhalmazához, vagy akár felhasználók képek számának csökkentése érdekében több alkalmazás csoportokhoz rendelhetők hozzá.
* Kezelheti a környezetében, használja a beépített delegált hozzáférés szerepkörök hozzárendelése és diagnosztikai adatok összegyűjtéséig különböző konfigurációs vagy felhasználói hibák megértése.
* Az új diagnosztikai szolgáltatás használatával kapcsolatos hibák elhárítása.
* A lemezkép és a virtuális gépek, nem az infrastruktúra csak kezelése. Nem kell a távoli asztal szerepkörök személyes kezelése, mint amikor a távoli asztali szolgáltatások, csak a virtuális gépek Azure-előfizetésében.

Rendelje hozzá is, és csatlakoztatja a felhasználókat a virtuális asztali környezetek:

* Kiosztás után a felhasználók indíthatják el bármely Windows virtuális asztali ügyféllel csatlakozzon a felhasználók közzétett Windows asztali számítógépek és alkalmazásokat. Csatlakozás bármely eszközről, vagy egy natív alkalmazást az eszközön vagy a Windows virtuális asztal HTML5 webes ügyfél keresztül.
* Biztonságos létrehozása, nyissa meg a felhasználókat a szolgáltatásba, így soha nem kell a bejövő portra hagyja fordított kapcsolatokon keresztül.

## <a name="requirements"></a>Követelmények

Néhány dolgot végre kell Windows virtuális asztal beállítását, és sikeresen csatlakozott-e a Windows asztali számítógépek és az alkalmazások a felhasználók számára.

A következő OSE-kre támogatása, ezért ügyeljen arra, hogy tervezzük a [a megfelelő licencek](https://azure.microsoft.com/pricing/details/virtual-desktop/) az asztal és alkalmazások telepítését tervezi alapján a felhasználók számára:

|Operációs rendszer|Licenc szükséges|
|---|---|
|Windows 10 Enterprise több munkamenetet vagy a Windows 10 Enterprise|A Microsoft 365 E3, E5, A3, a5 méretű, F1, üzleti<br>Windows E3, E5, A3, a5 csomag|
|Windows 7 Enterprise |A Microsoft 365 E3, E5, A3, a5 méretű, F1, üzleti<br>Windows E3, E5, A3, a5 csomag|
|Windows Server 2012 R2, 2016, 2019|A távoli asztali szolgáltatások ügyfél-hozzáférési licenc (CAL) frissítési garanciával rendelkező|

Az infrastruktúra Windows virtuális asztal támogatásához a következőkre van szüksége:

* Egy [az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* A Windows Server Active Directory szinkronizálva az Azure Active Directoryval. Ez a keresztül engedélyezhető:
  * Azure AD Connect
  * Azure AD Domain Services
* Azure-előfizetéssel, egy virtuális hálózatot tartalmazó, vagy csatlakoztatva van a Windows Server Active Directory tartalmazó
  
Az Azure virtuális gépeket hoz létre a Windows virtuális asztal kell lennie:

* [Tartományhoz csatlakoztatott standard](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) vagy [Hybrid AD-hez csatlakoztatott](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan). Virtuális gépek az Azure AD-hez csatlakoztatott nem lehet.
* Az alábbi futtató [támogatott operációsrendszer-lemezképek](#supported-virtual-machine-os-image).

>[!NOTE]
>Ha egy Azure-előfizetésre van szüksége, akkor az [iratkozzon fel az egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free/). Használata az Azure ingyenes próbaverzióját, Azure AD tartományi szolgáltatásokat az Azure Active Directory szinkronizálva legyenek a Windows Server Active Directory kell használnia.

Windows virtuális asztal magában foglalja a Windows asztali számítógépek és teszi elérhetővé a felhasználóknak és a felügyeleti megoldást, amely az Azure szolgáltatás Microsoft által üzemeltetett alkalmazások. A nyilvános előzetes verzióban asztali környezet és alkalmazások telepíthetők a virtuális gépeken (VM) bármely Azure-régióban, és a felügyeleti megoldás, és ezek a virtuális gépek adatait az Egyesült államokbeli (USA keleti régiója 2 régió) lesz. Emiatt előfordulhat, hogy az adatforgalom, az Egyesült Államokban során tesztelheti a szolgáltatást a nyilvános előzetes verzióban. A felügyeleti megoldás és az adatok honosítási az összes Azure-régió általános elérhetőségét díjtól horizontális kezdjük.

Az optimális teljesítmény érdekében győződjön meg arról, hogy a hálózat megfelel a következő követelményeknek:

* Az Azure-régióba gazdagép készletek alkalmazott ahol üzenetváltási (Ezredmásodperc) az ügyfél hálózati késést a kevesebb mint 150 ms kell lennie.
* Hálózati forgalom előfordulhat, hogy flow határokon kívül ország/régió, ha asztali környezet és alkalmazások üzemeltető virtuális gépek csatlakozni a felügyeleti szolgáltatáshoz.
* A hálózati teljesítmény optimalizálása érdekében azt javasoljuk, hogy az a munkamenetgazda virtuális gépek közös elhelyezésű a rendszer a felügyeleti szolgáltatás és az Azure ugyanabban a régióban.

## <a name="supported-remote-desktop-clients"></a>Támogatott a távoli asztali ügyfelek

A következő távoli asztali ügyfelek támogatják a Windows virtuális asztal:

* [Windows](https://docs.microsoft.com/azure/virtual-desktop/connect-windows-7-and-10)
* [HTML5](https://docs.microsoft.com/azure/virtual-desktop/connect-web)

## <a name="supported-virtual-machine-os-image"></a>Támogatott a virtuális gép operációsrendszer-lemezkép

Windows virtuális asztal támogatja a következő operációsrendszer-lemezképek:

* Windows 10 Enterprise több munkamenet
* Windows Server 2016

## <a name="provide-feedback"></a>Visszajelzés küldése

Látogasson el a [Windows virtuális asztal technikai Közösség](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) vitatni a virtuális asztali Windows-szolgáltatás a termékért felelős csoport és az aktív Közösség tagjai. A Microsoft jelenleg nem tart támogatási esetek Windows virtuális asztal pedig előzetes verzióban érhető el.

## <a name="next-steps"></a>További lépések

Első lépésként kell hoznia egy bérlőt. További bérlők létrehozásával kapcsolatos további információkért folytassa a bérlő létrehozása oktatóanyag.

> [!div class="nextstepaction"]
> [Bérlő létrehozása a Windows virtuális asztal előzetes verzióban érhető el](tenant-setup-azure-active-directory.md)
