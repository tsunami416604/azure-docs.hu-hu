---
title: Mit takar a Windows Virtual Desktop? – Azure
description: A Windows rendszerű virtuális asztal áttekintése.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 01/21/2020
ms.author: helohr
ms.openlocfilehash: 318997e2ebd7a423d7793a75575617d06ab842ac
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514272"
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
|Windows 10 Enterprise multi-session vagy Windows 10 Enterprise|Microsoft 365 E3, E5, a3, a5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, a3, a5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|RDS Client Access License (CAL) with Software Assurance|

Your infrastructure needs the following things to support Windows Virtual Desktop:

* An [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* A Windows Server Active Directory in sync with Azure Active Directory. You can configure this with one of the following:
  * Azure AD Connect (for hybrid organizations)
  * Azure AD Domain Services (for hybrid or cloud organizations)
* An Azure subscription that contains a virtual network that either contains or is connected to the Windows Server Active Directory
  
The Azure virtual machines you create for Windows Virtual Desktop must be:

* [Standard domain-joined](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) or [Hybrid AD-joined](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan). Virtual machines can't be Azure AD-joined.
* Running one of the following [supported OS images](#supported-virtual-machine-os-images).

>[!NOTE]
>If you need an Azure subscription, you can [sign up for a one-month free trial](https://azure.microsoft.com/free/). If you're using the free trial version of Azure, you should use Azure AD Domain Services to keep your Windows Server Active Directory in sync with Azure Active Directory.

The Azure virtual machines you create for Windows Virtual Desktop must have access to the following URLs:

|Cím|Kimenő port|Rendeltetés|
|---|---|---|
|*.wvd.microsoft.com|443-as TCP-port|Service traffic|
|*.blob.core.windows.net|443-as TCP-port|Agent, SXS stack updates, and Agent traffic|
|*.core.windows.net|443-as TCP-port|Agent traffic|
|*.servicebus.windows.net|443-as TCP-port|Agent traffic|
|prod.warmpath.msftcloudes.com|443-as TCP-port|Agent traffic|
|catalogartifact.azureedge.net|443-as TCP-port|Azure Piactér|
|kms.core.windows.net|TCP port 1688|Windows 10 activation|

>[!IMPORTANT]
>Opening these URLs is essential for a reliable Windows Virtual Desktop deployment. Blocking access to these URLs is unsupported and will affect service functionality. These URLs only correspond to Windows Virtual Desktop sites and resources, and do not include URLs for other services like Azure AD.

>[!NOTE]
>You must use the wildcard character (*) for URLs involving service traffic. If you prefer to not use * for agent-related traffic, here's how to find the URLs without wildcards:
>
>1. Register your virtual machines to the Windows Virtual Desktop host pool.
>2. Open **Event viewer** and navigate to **Windows** > **Application logs** and look for Event ID 3702.
>3. Whitelist the URLs that you find under Event ID 3702. The URLs under Event ID 3702 are region-specific. You'll need to repeat the whitelisting process with the relevant URLs for each region you want to deploy your virtual machines in.

Windows Virtual Desktop comprises the Windows desktops and apps you deliver to users and the management solution, which is hosted as a service on Azure by Microsoft. Desktops and apps can be deployed on virtual machines (VMs) in any Azure region, and the management solution and data for these VMs will reside in the United States. This may result in data transfer to the United States.

For optimal performance, make sure your network meets the following requirements:

* Round-trip (RTT) latency from the client's network to the Azure region where host pools have been deployed should be less than 150 ms.
* Network traffic may flow outside country/region borders when VMs that host desktops and apps connect to the management service.
* To optimize for network performance, we recommend that the session host's VMs are collocated in the same Azure region as the management service.

## <a name="supported-remote-desktop-clients"></a>Supported Remote Desktop clients

The following Remote Desktop clients support Windows Virtual Desktop:

* [Windows](connect-windows-7-and-10.md)
* [Web](connect-web.md)
* [Mac](connect-macos.md)
* [iOS](connect-ios.md)
* [Android (Preview)](connect-android.md)

## <a name="supported-virtual-machine-os-images"></a>Supported virtual machine OS images

Windows Virtual Desktop supports the following x64 operating system images:

* Windows 10 Enterprise multi-session, version 1809 or later
* Windows 10 Enterprise, version 1809 or later
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Windows Virtual Desktop does not support x86 (32-bit), Windows 10 Enterprise N, or Windows 10 Enterprise KN operating system images.

Available automation and deployment options depend on which OS and version you choose, as shown in the following table: 

|Operációs rendszer|Azure Image Gallery|Manual VM deployment|Azure Resource Manager template integration|Provision host pools on Azure Marketplace|Windows Virtual Desktop Agent updates|
|--------------------------------------|:------:|:------:|:------:|:------:|:------:|
|Windows 10 multi-session, version 1903|Igen|Igen|Igen|Igen|Automatikus|
|Windows 10 multi-session, version 1809|Igen|Igen|Nem|Nem|Automatikus|
|Windows 10 Enterprise, version 1903|Igen|Igen|Igen|Igen|Automatikus|
|Windows 10 Enterprise, version 1809|Igen|Igen|Nem|Nem|Automatikus|
|Windows 7 Enterprise|Igen|Igen|Nem|Nem|Kézi|
|Windows Server 2019|Igen|Igen|Nem|Nem|Automatikus|
|Windows Server 2016|Igen|Igen|Igen|Igen|Automatikus|
|Windows Server 2012 R2|Igen|Igen|Nem|Nem|Automatikus|

## <a name="next-steps"></a>Következő lépések

To get started, you'll need to create a tenant. To learn more about how to create a tenant, continue to the tenant creation tutorial.

> [!div class="nextstepaction"]
> [Bérlő létrehozása a Windows Virtual Desktopon](tenant-setup-azure-active-directory.md)
