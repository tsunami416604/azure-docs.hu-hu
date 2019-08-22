---
title: Mi a Windows rendszerű virtuális asztali előnézet?  – Azure
description: A Windows rendszerű virtuális asztali előnézet áttekintése.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 08/07/2019
ms.author: helohr
ms.openlocfilehash: 8827aa660d348ef9e42c617067c2837b8697ba6e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876780"
---
# <a name="what-is-windows-virtual-desktop-preview"></a>Mi a Windows rendszerű virtuális asztali előnézet? 

A nyilvános előzetes verzióban elérhető Windows Virtual Desktop Preview egy asztali és app Virtualization szolgáltatás, amely a felhőben fut.

A következő műveleteket végezheti el a Windows rendszerű virtuális asztalok Azure-beli futtatásakor:

* Többmunkamenetes Windows 10-es telepítés beállítása, amely teljes körű Windows 10-es rendszert biztosít a méretezhetőséggel
* Az Office 365 virtualizálása ProPlus, és a többfelhasználós virtuális forgatókönyvek futtatásának optimalizálása
* Ingyenes bővített biztonsági frissítésekkel rendelkező Windows 7 rendszerű virtuális asztalok biztosítása
* Meglévő Távoli asztali szolgáltatások (RDS) és Windows Server rendszerű asztali számítógépek és alkalmazások bármely számítógépre
* Az asztali számítógépek és az alkalmazások virtualizálása
* A Windows 10, a Windows Server és a Windows 7 rendszerű asztali számítógépek és alkalmazások felügyelete egységes felügyeleti felülettel

## <a name="introductory-video"></a>Bevezető videó

Ismerje meg a Windows rendszerű virtuális asztalt, hogy miért egyedi, és mi a videó újdonságai:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

További videók a Windows rendszerű virtuális asztalról: [lejátszási lista](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev).

## <a name="key-capabilities"></a>Főbb képességek

A Windows virtuális asztal használatával méretezhető és rugalmas környezetet állíthat be:

* Hozzon létre egy teljes asztali virtualizációs környezetet az Azure-előfizetésben anélkül, hogy további átjáró-kiszolgálókat kellene futtatnia.
* Tegyen közzé annyi gazdagépet, amennyire szüksége van a különböző munkaterhelések kielégítéséhez.
* Saját rendszerkép használata éles számítási feladatokhoz vagy teszteléshez az Azure-katalógusból.
* Csökkentse a költségeket a készletezett, több munkamenetes erőforrásokkal. Az új Windows 10 Enterprise multi-session képességgel, amely kizárólag a Windows rendszerű virtuális asztali és Távoli asztal munkamenet-gazdagép (RDSH) szerepkörrel rendelkezik a Windows Serveren, nagy mértékben csökkentheti a virtuális gépek számát és az operációs rendszer (OS) terhelését, miközben továbbra is ugyanazokat az erőforrásokat biztosítja a felhasználók számára.
* Egyéni tulajdonjogot biztosíthat személyes (állandó) asztali számítógépeken.

A virtuális asztalok üzembe helyezéséhez és kezeléséhez a következőket teheti:

* A Windows rendszerű virtuális asztali PowerShell és a REST felületek segítségével konfigurálja a gazdagépeket, hozzon létre alkalmazásokat, rendeljen hozzá felhasználókat, és tegye közzé az erőforrásokat.
* Teljes asztali vagy egyéni távoli alkalmazások közzététele egyetlen gazdagépről, egyéni Alkalmazáscsoport létrehozása különböző felhasználói csoportokhoz, vagy akár több alkalmazás-csoporthoz is rendelhet felhasználókat a képek számának csökkentése érdekében.
* A környezet kezelése során a beépített delegált hozzáférés használatával szerepköröket rendelhet hozzá, és diagnosztikai adatokat gyűjthet a különböző konfigurációs vagy felhasználói hibák megismeréséhez.
* A hibák elhárításához használja az új diagnosztikai szolgáltatást.
* Csak a rendszerkép és a virtuális gépek kezelése, nem az infrastruktúra. Nem kell személyesen kezelnie a Távoli asztal-szerepköröket, mint a Távoli asztali szolgáltatások, csak az Azure-előfizetésében lévő virtuális gépek.

A felhasználókat a virtuális asztalokhoz is hozzárendelheti és összekapcsolhatja:

* A hozzárendelést követően a felhasználók bármelyik Windows rendszerű virtuális asztali ügyfelet elindíthatják a felhasználók a közzétett Windows-asztali számítógépekhez és alkalmazásokhoz való összekapcsolásához. Bármely eszközről egy natív alkalmazáson keresztül kapcsolódhat az eszközön vagy a Windows Virtual Desktop HTML5 webes ügyfelén.
* Biztonságosan létesíthet felhasználókat a szolgáltatás fordított kapcsolataival, így soha nem kell megnyitnia a bejövő portokat.

## <a name="requirements"></a>Követelmények

Néhány dolog szükséges a Windows rendszerű virtuális asztali környezet beállításához és a felhasználók Windows rendszerű asztali számítógépekhez és alkalmazásokhoz való sikeres összekapcsolásához.

Azt tervezzük, hogy támogatást biztosítunk a következő operációs rendszerekhez, ezért győződjön meg arról, hogy rendelkezik a [megfelelő licenccel](https://azure.microsoft.com/pricing/details/virtual-desktop/) a felhasználók számára a telepíteni kívánt asztali és alkalmazások alapján:

|OS|Szükséges licenc|
|---|---|
|Windows 10 Enterprise multi-session vagy Windows 10 Enterprise|Microsoft 365 E3, E5, a3, a5, F1, Business<br>Windows E3, E5, a3, a5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, a3, a5, F1, Business<br>Windows E3, E5, a3, a5|
|Windows Server 2012 R2, 2016, 2019|Távoli asztali ügyféllicenc (CAL) frissítési garanciával|

Az infrastruktúrának a következő dolgokra van szüksége a Windows rendszerű virtuális asztalok támogatásához:

* Egy [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* A Windows Server Active Directory Azure Active Directorysal szinkronizálva. Ezt a következő módon engedélyezheti:
  * Azure AD Connect
  * Azure AD Domain Services
* Egy Azure-előfizetés, amely egy olyan virtuális hálózatot tartalmaz, amely vagy amely a Windows Serverhez csatlakozik, vagy amely kapcsolódik a Active Directory
  
A Windows rendszerű virtuális asztali környezethez létrehozott Azure-beli virtuális gépeknek a következőknek kell lenniük:

* [Szabványos tartományhoz csatlakoztatott](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) vagy [hibrid ad-csatlakozás](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan). A virtuális gépek nem csatlakoztathatók Azure AD-hez.
* A következő [támogatott operációsrendszer](#supported-virtual-machine-os-images)-lemezképek egyikének futtatása.

>[!NOTE]
>Ha Azure-előfizetésre van szüksége, [regisztrálhat egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free/). Ha az Azure ingyenes próbaverzióját használja, akkor a Azure AD Domain Services segítségével tartsa a Windows Server Active Directory szinkronizálását Azure Active Directory használatával.

A Windows rendszerű virtuális asztali szolgáltatások a felhasználók és a felügyeleti megoldás által szolgáltatott Windows-asztalok és-alkalmazások, amelyeket a Microsoft az Azure-szolgáltatásként üzemeltet. A nyilvános előzetes verzióban az asztali számítógépek és alkalmazások bármely Azure-régióban üzembe helyezhetők virtuális gépeken, valamint a felügyeleti megoldás és az ilyen virtuális gépekhez tartozó adat a Egyesült Államok (az USA 2. keleti régiójában) található. Ez adatátvitelt eredményezhet a Egyesült Államok a szolgáltatás nyilvános előzetes verzióban történő tesztelése során. Megkezdjük a felügyeleti megoldás és az adathonosítás felskálázását az összes Azure-régióban az általánosan elérhetővé.

Az optimális teljesítmény érdekében győződjön meg arról, hogy a hálózat megfelel a következő követelményeknek:

* Az ügyfél hálózatáról az Azure-régióba, ahol a gazdagépek üzembe helyezése megtörtént, a RTT késése nem haladhatja meg a 150 MS-ot.
* A hálózati forgalom az ország/régió határain kívül is eltarthat, amikor asztali számítógépeket és alkalmazásokat futtató virtuális gépek csatlakoznak a felügyeleti szolgáltatáshoz.
* A hálózati teljesítmény optimalizálása érdekében javasoljuk, hogy a munkamenet-gazdagép virtuális gépei a közös elhelyezésű azonos Azure-régióban legyenek.

## <a name="supported-remote-desktop-clients"></a>Támogatott Távoli asztal ügyfelek

A következő Távoli asztal-ügyfelek támogatják a Windows rendszerű virtuális asztalt:

* [Windows](https://docs.microsoft.com/azure/virtual-desktop/connect-windows-7-and-10)
* [HTML5](https://docs.microsoft.com/azure/virtual-desktop/connect-web)

## <a name="supported-virtual-machine-os-images"></a>Támogatott virtuális gépek operációsrendszer-lemezképei

A Windows virtuális asztal a következő operációsrendszer-lemezképeket támogatja:

* Windows 10 Enterprise multi-session
* Windows Server 2016

## <a name="next-steps"></a>További lépések

Első lépésként létre kell hoznia egy bérlőt. Ha többet szeretne megtudni a bérlők létrehozásáról, folytassa a bérlői létrehozási oktatóanyaggal.

> [!div class="nextstepaction"]
> [Bérlő létrehozása a Windows rendszerű virtuális asztali verzióban – előzetes verzió](tenant-setup-azure-active-directory.md)
