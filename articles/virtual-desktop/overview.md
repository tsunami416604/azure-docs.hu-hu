---
title: Mi a Windows virtuális asztal előzetes verziója?  – Azure
description: Windows virtuális asztal előzetes verziójának áttekintése.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 6385838064c408ccfa23dacbd5785f8e82f3cc8b
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049438"
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

Győződjön meg róla, hogy telepítette a [a megfelelő licencek](https://azure.microsoft.com/pricing/details/virtual-desktop/) az asztal és alkalmazások telepítését tervezi alapján a felhasználók számára:

|Operációs rendszer|Licenc szükséges|
|---|---|
|Windows 10 Enterprise több munkamenetet vagy a Windows 10-es egyetlen munkamenet-|Microsoft 365 E3, E5, A3, A5, Business, F1<br>Windows E3, E5, A3, a5 csomag|
|Windows 7|Microsoft 365 E3, E5, A3, A5, Business, F1<br>Windows E3, E5, A3, a5 csomag|
|Windows Server 2012 R2, 2016, 2019|A távoli asztali szolgáltatások ügyfél-hozzáférési licenc (CAL) frissítési garanciával rendelkező|

Az infrastruktúra Windows virtuális asztal támogatásához a következőkre van szüksége:

* Egy [az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* A Windows Server Active Directory szinkronizálva az Azure Active Directoryval. Ez a keresztül engedélyezhető:
  * Azure AD Connect
  * Azure AD Domain Services
* Azure-előfizetéssel, egy virtuális hálózatot tartalmazó, vagy csatlakoztatva van a Windows Server Active Directory tartalmazó
  
Az Azure virtuális gépeket hoz létre a Windows virtuális asztal kell lennie:

* [Tartományhoz csatlakoztatott standard](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) vagy [Hybrid AD-hez csatlakoztatott](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan). Virtuális gépek az Azure AD-hez csatlakoztatott nem lehet.
* A következő támogatott operációsrendszer-lemezképek egyikét futtató:
  * Windows 10 Enterprise több munkamenet
  * Windows Server 2016

>[!NOTE]
>Ha egy Azure-előfizetésre van szüksége, akkor az [iratkozzon fel az egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free/). Használata az Azure ingyenes próbaverzióját, Azure AD tartományi szolgáltatásokat az Azure Active Directory szinkronizálva legyenek a Windows Server Active Directory kell használnia.

Windows virtuális asztal magában foglalja a Windows asztali számítógépek és teszi elérhetővé a felhasználóknak és a felügyeleti megoldást, amely az Azure szolgáltatás Microsoft által üzemeltetett alkalmazások. A nyilvános előzetes verzióban asztali környezet és alkalmazások telepíthetők a virtuális gépeken (VM) bármely Azure-régióban, és a felügyeleti megoldás, és ezek a virtuális gépek adatait az Egyesült államokbeli (USA keleti régiója 2 régió) lesz. Emiatt előfordulhat, hogy az adatforgalom, az Egyesült Államokban során tesztelheti a szolgáltatást a nyilvános előzetes verzióban. A felügyeleti megoldás és az adatok honosítási az összes Azure-régió általános elérhetőségét díjtól horizontális kezdjük.

Az optimális teljesítmény érdekében győződjön meg arról, hogy a hálózat megfelel a következő követelményeknek:

* Az Azure-régióba gazdagép készletek alkalmazott ahol üzenetváltási (Ezredmásodperc) az ügyfél hálózati késést a kevesebb mint 150 ms kell lennie.
* Hálózati forgalom előfordulhat, hogy flow ország külső szegélyek, ha asztali környezet és alkalmazások üzemeltető virtuális gépek csatlakozni a felügyeleti szolgáltatáshoz.
* A hálózati teljesítmény optimalizálása érdekében azt javasoljuk, hogy az a munkamenetgazda virtuális gépek közös elhelyezésű a rendszer a felügyeleti szolgáltatás és az Azure ugyanabban a régióban.

## <a name="provide-feedback"></a>Visszajelzés küldése

Látogasson el a [Windows virtuális asztal technikai Közösség](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) vitatni a virtuális asztali Windows-szolgáltatás a termékért felelős csoport és az aktív Közösség tagjai. A Microsoft jelenleg nem tart támogatási esetek Windows virtuális asztal pedig előzetes verzióban érhető el.

## <a name="next-steps"></a>További lépések

Első lépésként kell hoznia egy bérlőt. További bérlők létrehozásával kapcsolatos további információkért folytassa a bérlő létrehozása oktatóanyag.

> [!div class="nextstepaction"]
> [Bérlő létrehozása a Windows virtuális asztal előzetes verzióban érhető el](tenant-setup-azure-active-directory.md)
