---
title: Mit takar a Windows Virtual Desktop? – Azure
description: A Windows rendszerű virtuális asztal áttekintése.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 05/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ab1d0318464f6b44e1f46bd30dc76272584fde64
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82929825"
---
# <a name="what-is-windows-virtual-desktop"></a>Mit takar a Windows Virtual Desktop? 

A Windows virtuális asztal egy felhőben futó asztali és app Virtualization szolgáltatás.

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

|Operációs rendszer|Szükséges licenc|
|---|---|
|Windows 10 Enterprise multi-session vagy Windows 10 Enterprise|Microsoft 365 E3, E5, a3, a5, F3, üzleti prémium<br>Windows E3, E5, a3, a5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, a3, a5, F3, üzleti prémium<br>Windows E3, E5, a3, a5|
|Windows Server 2012 R2, 2016, 2019|Távoli asztali ügyféllicenc (CAL) frissítési garanciával|

Az infrastruktúrának a következő dolgokra van szüksége a Windows rendszerű virtuális asztalok támogatásához:

* Egy [Azure Active Directory](/azure/active-directory/)
* A Windows Server Active Directory Azure Active Directorysal szinkronizálva. Ezt a következők egyikével konfigurálhatja:
  * Azure AD Connect (hibrid szervezetekhez)
  * Azure AD Domain Services (hibrid vagy Felhőbeli szervezetekhez)
* Olyan Azure-előfizetés, amely egy olyan virtuális hálózatot tartalmaz, amely vagy amely a Windows Serverhez csatlakozik, vagy amely kapcsolódik a Active Directory
  
A Windows rendszerű virtuális asztali környezethez létrehozott Azure-beli virtuális gépeknek a következőknek kell lenniük:

* [Szabványos tartományhoz csatlakoztatott](../active-directory-domain-services/active-directory-ds-comparison.md) vagy [hibrid ad-csatlakozás](../active-directory/devices/hybrid-azuread-join-plan.md). A virtuális gépek nem csatlakoztathatók Azure AD-hez.
* A következő [támogatott operációsrendszer-lemezképek](#supported-virtual-machine-os-images)egyikének futtatása.

>[!NOTE]
>Ha Azure-előfizetésre van szüksége, [regisztrálhat egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free/). Ha az Azure ingyenes próbaverzióját használja, akkor a Azure AD Domain Services segítségével tartsa a Windows Server Active Directory szinkronizálását Azure Active Directory használatával.

A Windows rendszerű virtuális asztali környezethez létrehozott Azure-beli virtuális gépeknek hozzáféréssel kell rendelkezniük a következő URL-címekhez:

|Cím|Kimenő TCP-port|Cél|Szolgáltatás címkéje|
|---|---|---|---|
|*. wvd.microsoft.com|443|Szolgáltatás forgalma|WindowsVirtualDesktop|
|mrsglobalsteus2prod.blob.core.windows.net|443|Ügynök-és SXS-verem frissítései|AzureCloud|
|*.core.windows.net|443|Ügynök forgalma|AzureCloud|
|*.servicebus.windows.net|443|Ügynök forgalma|AzureCloud|
|prod.warmpath.msftcloudes.com|443|Ügynök forgalma|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Piactér|AzureCloud|
|kms.core.windows.net|1688|A Windows aktiválása|Internet|
|wvdportalstorageblob.blob.core.windows.net|443|Azure Portal támogatás|AzureCloud|

>[!IMPORTANT]
>A Windows virtuális asztal mostantól támogatja a FQDN címkét. További információ: [a Azure Firewall használata a Windows rendszerű virtuális asztali környezetek elleni védelemhez](../firewall/protect-windows-virtual-desktop.md).
>
>Javasoljuk, hogy az URL-címek helyett a teljes tartománynevet vagy a szolgáltatás címkéit használja a szolgáltatási problémák megelőzése érdekében. A felsorolt URL-címek és címkék csak a Windows rendszerű virtuális asztali helyekhez és erőforrásokhoz tartoznak. Nem tartalmaznak URL-címeket más szolgáltatásokhoz, például a Azure Active Directoryhoz.

A következő táblázat felsorolja azokat az opcionális URL-címeket, amelyekhez az Azure-beli virtuális gépek hozzáférhetnek:

|Cím|Kimenő TCP-port|Cél|Szolgáltatás címkéje|
|---|---|---|---|
|*.microsoftonline.com|443|Hitelesítés MS online-szolgáltatásokhoz|Nincs|
|*. events.data.microsoft.com|443|Telemetria szolgáltatás|Nincs|
|www.msftconnecttest.com|443|Észleli, ha az operációs rendszer csatlakozik az internethez|Nincs|
|*. prod.do.dsp.mp.microsoft.com|443|Windows Update|Nincs|
|login.windows.net|443|Jelentkezzen be az MS online Servicesbe, az Office 365-be|Nincs|
|*. sfx.ms|443|OneDrive-ügyfélszoftver frissítései|Nincs|
|*. digicert.com|443|Tanúsítvány visszavonásának ellenőrzése|Nincs|


>[!NOTE]
>A Windows rendszerű virtuális asztal jelenleg nem tartalmaz olyan IP-címtartományok listáját, melyeket engedélyezheti a hálózati forgalom engedélyezéséhez. Jelenleg csak a konkrét URL-címek engedélyezése támogatott.
>
>Az Office-hoz kapcsolódó URL-címek, például a szükséges Azure Active Directory kapcsolódó URL-címek listáját lásd: [Office 365 URL-címek és IP-címtartományok](/office365/enterprise/urls-and-ip-address-ranges).
>
>A szolgáltatás forgalmát tartalmazó URL-címekhez a helyettesítő karaktert (*) kell használnia. Ha nem kívánja használni az ügynökkel kapcsolatos forgalmat, a következő módon keresheti meg az URL-címeket helyettesítő karakterek nélkül:
>
>1. Regisztrálja a virtuális gépeket a Windows rendszerű virtuális asztali gazdaszámítógépen.
>2. Nyissa meg az **eseménynaplót** , és keresse meg a **Windows-naplók** > **Application** > **WVD-Agent** alkalmazást, és keresse meg a 3702-es azonosítójú eseményt.
>3. Az 3702-as AZONOSÍTÓJÚ esemény alatt található URL-címek engedélyezési listája. Az 3702-es AZONOSÍTÓJÚ esemény URL-címei régiónként jellemzőek. Az engedélyezési folyamatot meg kell ismételnie minden olyan régióhoz tartozó URL-címmel, amelybe telepíteni kívánja a virtuális gépeket.

A Windows rendszerű virtuális asztali szolgáltatások a felhasználók és a felügyeleti megoldás által szolgáltatott Windows-asztalok és-alkalmazások, amelyeket a Microsoft az Azure-szolgáltatásként üzemeltet. Az asztali számítógépek és alkalmazások bármely Azure-régióban üzembe helyezhetők virtuális gépeken, valamint a felügyeleti megoldás és az ezen virtuális gépekhez tartozó adat a Egyesült Államok. Ez adatátvitelt eredményezhet a Egyesült Államokba.

Az optimális teljesítmény érdekében győződjön meg arról, hogy a hálózat megfelel a következő követelményeknek:

* Az ügyfél hálózatáról az Azure-régióba, ahol a gazdagépek üzembe helyezése megtörtént, a RTT késése nem haladhatja meg a 150 MS-ot.
* A hálózati forgalom az ország/régió határain kívül is eltarthat, amikor asztali számítógépeket és alkalmazásokat futtató virtuális gépek csatlakoznak a felügyeleti szolgáltatáshoz.
* A hálózati teljesítmény optimalizálása érdekében javasoljuk, hogy a munkamenet-gazdagép virtuális gépei a közös elhelyezésű azonos Azure-régióban legyenek.

## <a name="supported-remote-desktop-clients"></a>Támogatott Távoli asztal ügyfelek

A következő Távoli asztal-ügyfelek támogatják a Windows rendszerű virtuális asztalt:

* [Windows asztal](connect-windows-7-and-10.md)
* [Web](connect-web.md)
* [macOS](connect-macos.md)
* [iOS](connect-ios.md)
* [Android (előzetes verzió)](connect-android.md)

> [!IMPORTANT]
> A Windows virtuális asztal nem támogatja a RemoteApp-és asztali kapcsolatok (RADC) ügyfelet vagy a Távoli asztali kapcsolat (MSTSC) ügyfelet.

> [!IMPORTANT]
> A Windows virtuális asztal jelenleg nem támogatja a Távoli asztal ügyfelet a Windows áruházból. Az ügyfél támogatása egy későbbi kiadásban lesz hozzáadva.

A Távoli asztal ügyfeleknek hozzáféréssel kell rendelkezniük a következő URL-címekhez:

|Cím|Kimenő TCP-port|Cél|Ügyfél (ek)|
|---|---|---|---|
|*. wvd.microsoft.com|443|Szolgáltatás forgalma|Összes|
|*.servicebus.windows.net|443|Hibaelhárítási hibák|Összes|
|go.microsoft.com|443|Microsoft-Fwlinkek|Összes|
|aka.ms|443|Microsoft URL-rövidített|Összes|
|docs.microsoft.com|443|Dokumentáció|Összes|
|privacy.microsoft.com|443|Adatvédelmi nyilatkozat|Összes|
|query.prod.cms.rt.microsoft.com|443|Ügyfelek frissítései|Windows asztali rendszer|

>[!IMPORTANT]
>Az URL-címek megnyitása elengedhetetlen a megbízható ügyfél-élményhez. Az ezekhez az URL-címekhez való hozzáférés letiltása nem támogatott, és a szolgáltatás funkcióit is befolyásolja. Ezek az URL-címek csak az ügyfél helyeinek és erőforrásainak felelnek meg, és nem tartalmaznak URL-címeket más szolgáltatásokhoz, például a Azure Active Directoryhoz.

## <a name="supported-virtual-machine-os-images"></a>Támogatott virtuális gépek operációsrendszer-lemezképei

A Windows rendszerű virtuális asztali szolgáltatás a következő x64 operációsrendszer-lemezképeket támogatja:

* Windows 10 Enterprise multi-session, 1809-es vagy újabb verzió
* Windows 10 Enterprise, 1809-es vagy újabb verzió
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

A Windows virtuális asztal nem támogatja az x86-os (32 bites), a Windows 10 Enterprise N vagy a Windows 10 Enterprise KN-lemezképek használatát. A Windows 7 szintén nem támogatja a felügyelt Azure Storage-ban üzemeltetett VHD-vagy VHDX-alapú profilokat az adott szektor méretének korlátozása miatt.

Az elérhető automatizálási és üzembe helyezési lehetőségek attól függnek, hogy melyik operációs rendszerre és verzióra van kiválasztva, ahogy az a következő táblázatban látható: 

|Operációs rendszer|Azure képtára|VIRTUÁLIS gépek manuális üzembe helyezése|Azure Resource Manager sablonok integrációja|Gazdagép-készletek kiépítése az Azure piactéren|
|--------------------------------------|:------:|:------:|:------:|:------:|
|Windows 10 multi-session, 1903-es verzió|Igen|Igen|Igen|Igen|
|Windows 10 multi-session, 1809-es verzió|Igen|Igen|Nem|Nem|
|Windows 10 Enterprise, 1903-es verzió|Igen|Igen|Igen|Igen|
|Windows 10 Enterprise, 1809-es verzió|Igen|Igen|Nem|Nem|
|Windows 7 Enterprise|Igen|Igen|Nem|Nem|
|Windows Server 2019|Igen|Igen|Nem|Nem|
|Windows Server 2016|Igen|Igen|Igen|Igen|
|Windows Server 2012 R2|Igen|Igen|Nem|Nem|

## <a name="next-steps"></a>További lépések

Ha a Windows rendszerű virtuális asztali számítógép 2019-as verzióját használja, megkezdheti az oktatóanyagot a [bérlő létrehozása a Windows rendszerű virtuális asztalon](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)című részében.

Ha a Windows rendszerű virtuális asztali Spring 2020 kiadást használja, helyette létre kell hoznia egy gazdagép-készletet. Az első lépésekhez kövesse az alábbi oktatóanyagot.

> [!div class="nextstepaction"]
> [Gazdagépcsoport létrehozása az Azure Portalon](create-host-pools-azure-marketplace.md)
