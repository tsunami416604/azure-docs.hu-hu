---
title: Mit takar a Windows Virtual Desktop? – Azure
description: A Windows virtuális asztal áttekintése.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e62b3c551f41bca0055f35cf6bf62c59d921c73b
ms.sourcegitcommit: fab450a18a600d72b583ecfbe6c5e53afd43408c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80294828"
---
# <a name="what-is-windows-virtual-desktop"></a>Mit takar a Windows Virtual Desktop? 

A Windows Virtual Desktop egy asztali és alkalmazásvirtualizációs szolgáltatás, amely a felhőben fut.

A Windows Virtuális asztal Azure-on való futtatásakor a következőket teheti:

* Többmunkamenetes Windows 10-telepítés beállítása, amely teljes Windows 10-et biztosít méretezhetőséggel
* Az Office 365 ProPlus virtualizálása és optimalizálása többfelhasználós virtuális forgatókönyvekben való futtatásra
* Ingyenes kiterjesztett biztonsági frissítések biztosítása a Windows 7 virtuális asztalokszámára
* Meglévő Távoli asztali szolgáltatások (RDS) és Windows Server-asztalok és -alkalmazások bármely számítógépre való elszállítása
* Az asztalok és az alkalmazások virtualizálása
* A Windows 10, a Windows Server és a Windows 7 asztali számítógépek és alkalmazások kezelése egységes felügyeleti környezettel

## <a name="introductory-video"></a>Bevezető videó

Információ a Windows virtuális asztalról, annak egyedi szolmának és az újdonságokról:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

A Windows virtuális asztalról további videókat a [lejátszási listánkon](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev)láthatsz.

## <a name="key-capabilities"></a>Főbb képességek

A Windows Virtual Desktop segítségével méretezhető és rugalmas környezetet állíthat be:

* Hozzon létre egy teljes asztali virtualizálási környezetet az Azure-előfizetésében anélkül, hogy további átjárókiszolgálókat kellene futtatnia.
* A különböző munkaterhelések kezeléséhez szükséges annyi gazdagépkészletet tehet közzé.
* Hozza létre saját lemezképét éles számítási feladatokhoz, vagy tesztelje az Azure Gallery-ből.
* Csökkentse költségeit a közös, többmunkamenetes erőforrásokkal. A Windows 10 Enterprise új, kizárólag a Windows Virtual Desktop és a Remote Desktop Session Host (RDSH) szerepkörrel rendelkező Windows Server szerepkörrel jelentősen csökkentheti a virtuális gépek és az operációs rendszer (OS) terhelését, miközben továbbra is ugyanazokat az erőforrásokat biztosítja a felhasználók számára.
* Egyéni tulajdonjog biztosítása személyes (állandó) asztali számítógépeken keresztül.

Virtuális asztalok at telepíthet és kezelhet:

* A Windows Virtual Desktop PowerShell és REST felületek segítségével konfigurálhatja a gazdakészleteket, alkalmazáscsoportokat hozhat létre, felhasználókat rendelhet hozzá, és erőforrásokat tehet közzé.
* Teljes asztali vagy különálló távoli alkalmazásokat tehet közzé egyetlen gazdagépkészletből, egyéni alkalmazáscsoportokat hozhat létre a különböző felhasználói csoportok számára, vagy akár több alkalmazáscsoporthoz is rendelhet felhasználókat a képek számának csökkentése érdekében.
* A környezet kezelése során a beépített delegált hozzáférés sel rendeljen szerepköröket, és gyűjtse össze a diagnosztikát a különböző konfigurációs vagy felhasználói hibák megértéséhez.
* Az új diagnosztikai szolgáltatás segítségével elháríthatja a hibákat.
* Csak a lemezképet és a virtuális gépeket kezelje, az infrastruktúrát nem. Nem kell személyesen kezelni a Távoli asztal szerepkörök, mint te a Távoli asztali szolgáltatások, csak a virtuális gépek az Azure-előfizetésben.

A felhasználókat a virtuális asztalokhoz is hozzárendelheti és csatlakoztathatja:

* A hozzárendelt felhasználók bármelyik Windows Virtuális asztali ügyfél programot elindíthatják, hogy a felhasználókat a közzétett Windows-asztalokhoz és -alkalmazásokhoz kapcsolhassák. Bármilyen eszközről csatlakozhat az eszközön lévő natív alkalmazáson vagy a Windows Virtual Desktop HTML5 webes ügyfélen keresztül.
* Biztonságosan hozza létre a felhasználókat a szolgáltatáshoz való fordított kapcsolaton keresztül, így soha nem kell nyitva hagynia a bejövő portokat.

## <a name="requirements"></a>Követelmények

Van néhány dolog, amit be kell állítania a Windows virtuális asztal, és sikeresen csatlakoztassa a felhasználókat a Windows asztali és alkalmazások.

Azt tervezzük, hogy a következő os-ok támogatását biztosítjuk, ezért győződjön meg arról, hogy rendelkezik a [megfelelő licencekkel](https://azure.microsoft.com/pricing/details/virtual-desktop/) a felhasználók számára az asztal és a telepíteni kívánt alkalmazások alapján:

|Operációs rendszer|Szükséges licenc|
|---|---|
|Windows 10 Enterprise többmunkamenetes vagy Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F1, Üzleti<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F1, Üzleti<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|Távoli asztali ügyféllicenc (CAL) frissítési garanciával|

Az infrastruktúrának a következő dolgokra van szüksége a Windows virtuális asztal támogatásához:

* [Egy Azure Active Directory](/azure/active-directory/)
* Az Azure Active Directoryval szinkronban lévő Windows Server Active Directory. Ezt az alábbi beállításokkal állíthatja be:
  * Azure AD Connect (hibrid szervezetek számára)
  * Azure AD tartományi szolgáltatások (hibrid vagy felhőalapú szervezetek számára)
* Olyan Azure-előfizetés, amely a Windows Server Active Directoryt tartalmazó vagy ahhoz kapcsolódó virtuális hálózatot tartalmaz
  
A Windows virtuális asztalhoz létrehozott Azure virtuális gépeknek a következőknek kell lenniük:

* [Normál tartományhoz vagy](../active-directory-domain-services/active-directory-ds-comparison.md) [hibrid AD-hez csatlakozott](../active-directory/devices/hybrid-azuread-join-plan.md). Virtuális gépek nem lehet Az Azure AD-hez.
* Az alábbi [támogatott operációsrendszer-lemezképek](#supported-virtual-machine-os-images)egyikének futtatása.

>[!NOTE]
>Ha Azure-előfizetésre van szüksége, [regisztrálhat egy egyhónapos ingyenes próbaverzióra.](https://azure.microsoft.com/free/) Ha az Azure ingyenes próbaverzióját használja, az Azure AD tartományi szolgáltatások használatával tartsa a Windows Server Active Directoryszinkron-t az Azure Active Directoryval.

A Windows virtuális asztalhoz létrehozott Azure virtuális gépeknek a következő URL-címekhez kell hozzáférniük:

|Cím|Kimenő port|Cél|
|---|---|---|
|*.wvd.microsoft.com|443-as TCP-port|Szolgáltatási forgalom|
|*.blob.core.windows.net|443-as TCP-port|Ügynök, SXS verem frissítések és ügynök forgalom|
|*.core.windows.net|443-as TCP-port|Ügynökforgalma|
|*.servicebus.windows.net|443-as TCP-port|Ügynökforgalma|
|prod.warmpath.msftcloudes.com|443-as TCP-port|Ügynökforgalma|
|catalogartifact.azureedge.net|443-as TCP-port|Azure Piactér|
|kms.core.windows.net|1688-as TCP-port|Windows 10 aktiválása|

>[!IMPORTANT]
>Ezeknek az URL-címeknek a megnyitása elengedhetetlen a Windows virtuális asztal megbízható telepítéséhez. Az URL-címekhez való hozzáférés letiltása nem támogatott, és hatással lesz a szolgáltatás működésére. Ezek az URL-címek csak a Windows virtuális asztali webhelyeknek és erőforrásoknak felelnek meg, és nem tartalmaznak URL-címeket más szolgáltatásokhoz, például az Azure Active Directoryhoz.

>[!NOTE]
>A Windows virtuális asztal jelenleg nem rendelkezik olyan IP-címtartományokkal, amelyeket a hálózati forgalom engedélyezéséhez engedélyezési listához használhatna. Jelenleg csak bizonyos URL-címek engedélyezési listázását támogatjuk.
>
>A szolgáltatásforgalmat érintő URL-címekhez a helyettesítő karaktert (*) kell használnia. Ha nem szeretné használni a * karaktert az ügynökkel kapcsolatos forgalomhoz, az alábbiak szerint keresheti meg az URL-címeket helyettesítő karakterek nélkül:
>
>1. Regisztrálja virtuális gépeit a Windows virtuális asztal gazdagépkészletében.
>2. Nyissa meg **az Eseménynaplót,** és keresse **meg** > az Application**WVD-Agent** **alkalmazást,** > és keresse meg a 3702-es eseményazonosítót.
>3. A 3702-es eseményazonosító alatt található URL-címek engedélyezési listája. A 3702-es eseményazonosító alatti URL-címek régióspecifikusak. Meg kell ismételnie az engedélyezési folyamatot a megfelelő URL-címekkel minden egyes régióban szeretné telepíteni a virtuális gépeket.

A Windows virtuális asztal tartalmazza a Windows-asztalokat és -alkalmazásokat, amelyeket a felhasználóknak szállít, valamint a felügyeleti megoldást, amelyet a Microsoft szolgáltatásként üzemeltet az Azure-ban. Az asztalok és az alkalmazások bármely Azure-régióban telepíthetők virtuális gépekre (VM-ek), és a virtuális gépek felügyeleti megoldása és adatai az Egyesült Államokban lesznek. Ez az Egyesült Államokba történő adatátvitelt eredményezheti.

Az optimális teljesítmény érdekében győződjön meg arról, hogy a hálózat megfelel az alábbi követelményeknek:

* Oda-vissza (RTT) késés az ügyfél hálózatáról az Azure-régióba, ahol a gazdagépkészletek üzembe helyezése 150 ms-nál kisebb lehet.
* A hálózati forgalom az ország/régió határain kívül is folyhat, amikor az asztali számítógépeket és alkalmazásokat üzemeltető virtuális gépek csatlakoznak a felügyeleti szolgáltatáshoz.
* A hálózati teljesítmény optimalizálása érdekében azt javasoljuk, hogy a munkamenet gazdagép virtuális gépei ugyanabban az Azure-régióban helyezkednek el, mint a felügyeleti szolgáltatás.

## <a name="supported-remote-desktop-clients"></a>Támogatott távoli asztali ügyfelek

A következő Távoli asztali ügyfelek támogatják a Windows virtuális asztalt:

* [Windows Asztal](connect-windows-7-and-10.md)
* [Web](connect-web.md)
* [Macos](connect-macos.md)
* [iOS](connect-ios.md)
* [Android (előzetes verzió)](connect-android.md)

> [!IMPORTANT]
> A Windows virtuális asztal nem támogatja a RemoteApp és az Asztali kapcsolatok (RADC) vagy a Távoli asztali kapcsolat (MSTSC) ügyfelet.

> [!IMPORTANT]
> A Windows virtuális asztal jelenleg nem támogatja a Távoli asztali ügyfél programot a Windows Áruházból. Az ügyfél támogatása egy későbbi kiadásban lesz hozzáadva.

A Távoli asztali ügyfeleknek a következő URL-címekhez kell hozzáférniük:

|Cím|Kimenő port|Cél|Ügyfél(ek)|
|---|---|---|---|
|*.wvd.microsoft.com|443-as TCP-port|Szolgáltatási forgalom|Összes|
|*.servicebus.windows.net|443-as TCP-port|Adatok kalelhárítása|Összes|
|go.microsoft.com|443-as TCP-port|Microsoft FWLinks|Összes|
|aka.ms|443-as TCP-port|Microsoft URL-rövidítő|Összes|
|docs.microsoft.com|443-as TCP-port|Dokumentáció|Összes|
|privacy.microsoft.com|443-as TCP-port|Adatvédelmi nyilatkozat|Összes|
|query.prod.cms.rt.microsoft.com|443-as TCP-port|Ügyfélfrissítések|Windows asztali rendszer|

>[!IMPORTANT]
>Ezeknek az URL-címeknek a megnyitása elengedhetetlen a megbízható ügyfélélményhez. Az URL-címekhez való hozzáférés letiltása nem támogatott, és hatással lesz a szolgáltatás működésére. Ezek az URL-címek csak az ügyfélhelyeknek és az erőforrásoknak felelnek meg, és nem tartalmaznak URL-címeket más szolgáltatásokhoz, például az Azure Active Directoryhoz.

## <a name="supported-virtual-machine-os-images"></a>Támogatott virtuálisgép-operációs rendszer lemezképei

A Windows Virtual Desktop a következő x64-es operációs rendszerképeket támogatja:

* Windows 10 Enterprise többmunkamenetes, 1809-es vagy újabb verzió
* Windows 10 Enterprise, 1809-es vagy újabb verzió
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

A Windows Virtual Desktop nem támogatja az x86 (32 bites), a Windows 10 Enterprise N vagy a Windows 10 Enterprise KN operációs rendszer lemezképeit. A Windows 7 a szektorméret-korlátozás miatt nem támogatja a felügyelt Azure Storage-ban üzemeltetett virtuális merevlemez- vagy VHDX-alapú profilmegoldásokat sem.

A rendelkezésre álló automatizálási és telepítési lehetőségek attól függnek, hogy melyik operációs rendszert és verziót választja, ahogy az az alábbi táblázatban látható: 

|Operációs rendszer|Azure képgaléria|Kézi virtuális gép telepítése|Azure Resource Manager-sablon integrációja|Gazdagépkészletek kiépítése az Azure Piactéren|Windows virtuális asztali ügynök frissítései|
|--------------------------------------|:------:|:------:|:------:|:------:|:------:|
|Windows 10 többmunkamenetes, 1903-as verzió|Igen|Igen|Igen|Igen|Automatikus|
|Windows 10 többmunkamenetes, 1809-es verzió|Igen|Igen|Nem|Nem|Automatikus|
|Windows 10 Enterprise, 1903-as verzió|Igen|Igen|Igen|Igen|Automatikus|
|Windows 10 Enterprise, 1809-es verzió|Igen|Igen|Nem|Nem|Automatikus|
|Windows 7 Enterprise|Igen|Igen|Nem|Nem|Kézi|
|Windows Server 2019|Igen|Igen|Nem|Nem|Automatikus|
|Windows Server 2016|Igen|Igen|Igen|Igen|Automatikus|
|Windows Server 2012 R2|Igen|Igen|Nem|Nem|Automatikus|

## <a name="next-steps"></a>További lépések

A kezdéshez létre kell hoznia egy bérlőt. Ha többet szeretne megtudni arról, hogyan hozhat létre egy bérlőt, folytassa a bérlő-létrehozási oktatóanyag.

> [!div class="nextstepaction"]
> [Bérlő létrehozása a Windows Virtual Desktopon](tenant-setup-azure-active-directory.md)
