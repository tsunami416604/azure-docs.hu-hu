---
title: A távoli asztal részletes hibaelhárítása Az Azure-ban | Microsoft Docs
description: A távoli asztali hibákkal kapcsolatos részletes hibaelhárítási lépések áttekintése, ahol nem lehet Windowsos virtuális gépek az Azure-ban
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: nem lehet csatlakozni a távoli asztalhoz, a távoli asztal hibaelhárítása, távoli asztal nem tud csatlakozni, távoli asztali hibák, távoli asztal hibaelhárítása, távoli asztali problémák
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: ee2fb3757b0e3a7015a98f4e04084fd9c6a4850d
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75747540"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Az Azure-beli Windows rendszerű virtuális gépek távoli asztali kapcsolataival kapcsolatos problémáinak részletes hibaelhárítási lépései
Ez a cikk részletes hibaelhárítási lépéseket tartalmaz a Windows-alapú Azure-beli virtuális gépek összetett Távoli asztal hibáinak diagnosztizálásához és javításához.

> [!IMPORTANT]
> A gyakoribb Távoli asztal hibák elkerülése érdekében olvassa el [az alapszintű hibaelhárítási cikket távoli asztal](troubleshoot-rdp-connection.md) a továbblépés előtt.

Előfordulhat, hogy Távoli asztal hibaüzenet jelenik meg, amely nem hasonlít az [alapszintű távoli asztal hibaelhárítási útmutatójában](troubleshoot-rdp-connection.md)ismertetett hibaüzenetekre. Az alábbi lépéseket követve megállapíthatja, hogy miért nem tud csatlakozni az Azure-beli virtuális gép RDP-szolgáltatásához a Távoli asztal (RDP) ügyfél.


Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel [az MSDN Azure-ban és a stack overflow fórumokon](https://azure.microsoft.com/support/forums/). Azt is megteheti, hogy Azure-támogatási incidenst is beküld. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és kattintson a **támogatás kérése**lehetőségre. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Távoli asztal-kapcsolatok összetevői
Az RDP-kapcsolatok a következő összetevőket érintik:

![](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

A továbblépés előtt hasznos lehet a virtuális géphez való legutóbbi sikeres Távoli asztal-kapcsolódás óta megváltozott változások mentális áttekintése. Példa:

* Módosult a virtuális gép vagy a virtuális GÉPET tartalmazó felhőalapú szolgáltatás nyilvános IP-címe (más néven virtuális IP-cím: [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)). Az RDP-hiba oka az lehet, hogy a DNS-ügyfél gyorsítótára továbbra is a DNS-névhez regisztrált *régi IP-címmel* rendelkezik. Ürítse ki a DNS-ügyfél gyorsítótárát, és próbálkozzon újra a virtuális gép csatlakoztatásával. Vagy próbáljon meg közvetlenül kapcsolódni az új VIP-hez.
* Harmadik féltől származó alkalmazást használ a Távoli asztal kapcsolatok kezeléséhez ahelyett, hogy a Azure Portal által létrehozott kapcsolatot használja. Ellenőrizze, hogy az alkalmazás konfigurációja tartalmazza-e a Távoli asztal adatforgalom megfelelő TCP-portját. A [Azure Portal](https://portal.azure.com)klasszikus virtuális géphez tartozó PORTOT a virtuális gép beállításai > végpontok elemre kattintva tekintheti meg.

## <a name="preliminary-steps"></a>Előzetes lépések
Mielőtt folytatná a részletes hibaelhárítást,

* A Azure Portalban található virtuális gép állapotának ellenőrzését bármilyen nyilvánvaló probléma miatt.
* Az [alapszintű hibaelhárítási útmutatóban kövesse az általános RDP-hibák gyors javításának lépéseit](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* Egyéni lemezképek esetén győződjön meg arról, hogy a virtuális merevlemez megfelelően fel van készítve a feltöltés előtt. További információ: [Windows VHD vagy VHDX előkészítése az Azure](../windows/prepare-for-upload-vhd-image.md)-ba való feltöltéshez.


A lépések elvégzése után próbálja meg újracsatlakozni a virtuális géphez Távoli asztal.

## <a name="detailed-troubleshooting-steps"></a>Részletes hibaelhárítási útmutató
Előfordulhat, hogy az Távoli asztal ügyfél nem tudja elérni a Távoli asztal szolgáltatást az Azure-beli virtuális gépen az alábbi forrásokkal kapcsolatos problémák miatt:

* [Ügyfélszámítógép Távoli asztal](#source-1-remote-desktop-client-computer)
* [Szervezeti intranet Edge-eszköz](#source-2-organization-intranet-edge-device)
* [Cloud Service-végpont és hozzáférés-vezérlési lista (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Hálózati biztonsági csoportok](#source-4-network-security-groups)
* [Windows-alapú Azure-beli virtuális gép](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>1\. Forrás: Távoli asztal ügyfélszámítógép
Ellenőrizze, hogy a számítógép tud-e Távoli asztal kapcsolatot létesíteni egy másik helyszíni, Windows-alapú számítógéppel.

![](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Ha nem, keresse meg a következő beállításokat a számítógépen:

* Egy helyi tűzfal-beállítás, amely blokkolja Távoli asztal forgalmat.
* Helyileg telepített alkalmazásproxy szoftver, amely megakadályozza a Távoli asztal kapcsolatokat.
* Helyileg telepített hálózatfigyelő szoftver, amely megakadályozza a Távoli asztal kapcsolatokat.
* Más típusú biztonsági szoftverek, amelyek figyelik a forgalmat, vagy engedélyezik vagy letiltják az Távoli asztal kapcsolatokat megakadályozó bizonyos típusú forgalmat.

Ezekben az esetekben átmenetileg tiltsa le a szoftvert, és próbáljon meg Távoli asztalon keresztül csatlakozni a helyszíni számítógéphez. Ha így találja meg a tényleges okot, akkor a hálózati rendszergazdával együttműködve javítsa a Távoli asztal kapcsolatokat.

## <a name="source-2-organization-intranet-edge-device"></a>2\. Forrás: szervezeti intranet Edge-eszköz
Győződjön meg arról, hogy az internethez közvetlenül csatlakozó számítógép Távoli asztal kapcsolatot tud létesíteni az Azure-beli virtuális géppel.

![](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Ha nincs olyan számítógépe, amely közvetlenül csatlakozik az internethez, hozzon létre és végezzen el egy új Azure-beli virtuális gépet egy erőforráscsoport vagy egy felhőalapú szolgáltatásban. További információkért lásd: [Windows rendszerű virtuális gép létrehozása az Azure-ban](../virtual-machines-windows-hero-tutorial.md). A teszt után törölheti a virtuális gépet és az erőforráscsoportot, vagy a Cloud Service-t.

Ha Távoli asztal kapcsolatot hozhat létre közvetlenül az internethez csatlakozó számítógéppel, ellenőrizze a szervezet intranetes peremhálózati eszközét a következőhöz:

* Egy belső tűzfal blokkolja a HTTPS-kapcsolatokat az internettel.
* Egy proxykiszolgáló, amely megakadályozza A Távoli asztal kapcsolatokat.
* A peremhálózati hálózat eszközein futó behatolás-észlelési vagy Hálózatfigyelő szoftver, amely megakadályozza a Távoli asztal kapcsolatokat.

A hálózati rendszergazdával együttműködve javítsa ki a szervezet intranetes peremhálózati eszközének beállításait, hogy engedélyezze a HTTPS-alapú Távoli asztal kapcsolatokat az internethez.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>3\. Forrás: Cloud Service-végpont és ACL
A klasszikus üzemi modellel létrehozott virtuális gépek esetében ellenőrizze, hogy egy másik Azure-beli virtuális gép, amely ugyanabban a felhőalapú szolgáltatásban vagy virtuális hálózaton található, Távoli asztal-e kapcsolatot az Azure-beli virtuális géppel.

![](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> A Resource Managerben létrehozott virtuális gépek esetében ugorjon a [forrás 4: hálózati biztonsági csoportok](#source-4-network-security-groups)elemre.

Ha nem rendelkezik másik virtuális géppel ugyanabban a felhőalapú szolgáltatásban vagy virtuális hálózaton, hozzon létre egyet. Kövesse a [Windows rendszerű virtuális gép létrehozása az Azure-ban](../virtual-machines-windows-hero-tutorial.md)című témakör lépéseit. A teszt befejezése után törölje a teszt virtuális gépet.

Ha Távoli asztalon keresztül tud csatlakozni egy felhőalapú szolgáltatásban vagy virtuális hálózaton lévő virtuális géphez, ellenőrizze a következő beállításokat:

* A cél virtuális gépen Távoli asztal forgalom végpont-konfigurációja: a végpont magánhálózati TCP-portjának meg kell egyeznie azzal a TCP-porttal, amelyen a virtuális gép Távoli asztal szolgáltatását figyeli (az alapértelmezett érték 3389).
* A cél virtuális gépen a Távoli asztal forgalmi végpont ACL-je: az ACL-ek lehetővé teszik a bejövő forgalom engedélyezett vagy megtagadását az internetről a forrás IP-címe alapján. A helytelenül konfigurált ACL-ek megakadályozhatják a bejövő Távoli asztal forgalmat a végpontra. Ellenőrizze az ACL-eket, hogy a proxy vagy más peremhálózati kiszolgáló nyilvános IP-címeiről érkező bejövő forgalom engedélyezett-e. További információ: [Mi az a Network Access Control List (ACL)?](../../virtual-network/virtual-networks-acl.md)

Annak vizsgálatához, hogy a végpont a probléma forrása-e, távolítsa el az aktuális végpontot, és hozzon létre egy újat, és válasszon egy véletlenszerű portot a 49152 – 65535 tartományból a külső portszámhoz. További információ: [végpontok beállítása virtuális géphez](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="source-4-network-security-groups"></a>4\. Forrás: hálózati biztonsági csoportok
A hálózati biztonsági csoportok lehetővé teszik a bejövő és kimenő forgalom részletesebb szabályozását. Az alhálózatokat és a Cloud Servicest egy Azure-beli virtuális hálózaton átívelő szabályok hozhatók létre.

Az [IP-folyamat ellenőrzésével](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) győződjön meg róla, hogy a hálózati biztonsági csoportok szabályai nem blokkolják a virtuális gépek kimenő és bejövő forgalmát. A hatályos biztonsági csoportok szabályait is áttekintheti, így biztosítva, hogy a bejövő "engedélyezés" NSG szabály létezik, és az RDP-portra van rangsorolva (alapértelmezett 3389). További információ: [hatékony biztonsági szabályok használata a virtuális gépek forgalmának hibakereséséhez](../../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="source-5-windows-based-azure-vm"></a>5\. Forrás: Windows-alapú Azure-beli virtuális gép
![](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Kövesse a [cikk](../windows/reset-rdp.md)utasításait. Ez a cikk alaphelyzetbe állítja a Távoli asztal szolgáltatást a virtuális gépen:

* Engedélyezze a "Távoli asztal" Windows tűzfal alapértelmezett szabályát (3389-as TCP-port).
* Távoli asztal kapcsolatok engedélyezéséhez állítsa a HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections beállításazonosító értékét 0-ra.

Próbálja megismételni a kapcsolatokat a számítógépről. Ha továbbra sem tud csatlakozni a Távoli asztalon keresztül, ellenőrizze a következő lehetséges problémákat:

* A Távoli asztal szolgáltatás nem fut a cél virtuális gépen.
* A Távoli asztal szolgáltatás nem figyeli a 3389-es TCP-portot.
* A Windows tűzfal vagy egy másik helyi tűzfal olyan kimenő szabályt tartalmaz, amely megakadályozza a Távoli asztal forgalmat.
* Az Azure-beli virtuális gépen futó behatolás-észlelési vagy Hálózatfigyelő szoftver megakadályozza a Távoli asztal kapcsolatokat.

A klasszikus üzemi modellel létrehozott virtuális gépek esetében távoli Azure PowerShell munkamenetet használhat az Azure-beli virtuális géphez. Először telepítenie kell egy tanúsítványt a virtuális gép üzemeltetési felhőalapú szolgáltatásához. Lépjen a [biztonságos távoli PowerShell-hozzáférés konfigurálása az Azure Virtual Machineshoz](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) , és töltse le a **InstallWinRMCertAzureVM. ps1** parancsfájlt a helyi számítógépre.

Ezután telepítse Azure PowerShell, ha még nem tette meg. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

Ezután nyisson meg egy Azure PowerShell parancssort, és módosítsa az aktuális mappát a **InstallWinRMCertAzureVM. ps1** parancsfájl helyére. Azure PowerShell szkript futtatásához be kell állítania a megfelelő végrehajtási házirendet. Futtassa a **Get-ExecutionPolicy** parancsot az aktuális házirend szintjének meghatározásához. A megfelelő szint beállításával kapcsolatos információkért lásd: [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Ezután töltse ki az Azure-előfizetés nevét, a Cloud Service-nevet és a virtuális gép nevét (a < és > karaktereket), majd futtassa ezeket a parancsokat.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

A megfelelő előfizetés nevét a **Get-azuresubscription parancsot** parancs megjelenítésének *SubscriptionName* tulajdonságában érheti el. A virtuális gép Cloud Service-nevét a **Get-AzureVM** parancs megjelenítésének *szolgáltatásnév* oszlopában érheti el.

Ellenőrizze, hogy rendelkezik-e az új tanúsítvánnyal. Nyisson meg egy Tanúsítványkezelő beépülő modult az aktuális felhasználó számára, és tekintse meg a **megbízható legfelső szintű tanúsítvány \ tanúsítványok részhez** mappáját. Meg kell jelennie a Felhőbeli szolgáltatás DNS-nevével rendelkező tanúsítványnak a kiállító oszlopban (például: cloudservice4testing.cloudapp.net).

Ezután kezdeményezzen egy távoli Azure PowerShell-munkamenetet ezen parancsok használatával.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Az érvényes rendszergazdai hitelesítő adatok beírása után a következő Azure PowerShellhoz hasonló üzenetnek kell megjelennie:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

A kérdés első része a felhőalapú szolgáltatás neve, amely a célként megadott virtuális gépet tartalmazza, ami különbözhet a "cloudservice4testing.cloudapp.net" típustól. Mostantól Azure PowerShell parancsokat is kiállíthat ehhez a felhőalapú szolgáltatáshoz, hogy megvizsgálják a megemlített problémákat, és javítsák a konfigurációt.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>A Távoli asztali szolgáltatások figyelő TCP-port manuális javítása
A távoli Azure PowerShell munkamenet parancssorába futtassa ezt a parancsot.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

A portszám tulajdonság az aktuális portszámot jeleníti meg. Ha szükséges, módosítsa a Távoli asztal portszámát az alapértelmezett értékre (3389) a parancs használatával.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Ezzel a paranccsal ellenőrizze, hogy a port a 3389 értékre változott-e.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

A parancs használatával lépjen ki a távoli Azure PowerShell munkamenetből.

```powershell
Exit-PSSession
```

Győződjön meg arról, hogy az Azure-beli virtuális gép Távoli asztal végpontja a 3398-as TCP-portot is használja belső portként. Indítsa újra az Azure-beli virtuális gépet, majd próbálja megismételni a Távoli asztal-kapcsolatokat.

## <a name="additional-resources"></a>További források
[Jelszó alaphelyzetbe állítása vagy a Távoli asztal szolgáltatás használata Windows rendszerű virtuális gépekhez](../windows/reset-rdp.md)

[How to install and configure Azure PowerShell (Az Azure PowerShell telepítése és konfigurálása)](/powershell/azure/overview)

[A Secure Shell-(SSH-) kapcsolatok hibakeresése Linux-alapú Azure-beli virtuális gépeken](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Azure-beli virtuális gépen futó alkalmazásokhoz való hozzáférés hibaelhárítása](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

