---
title: Részletes távoli asztali hibaelhárítás az Azure-ban | Microsoft dokumentumok
description: Tekintse át a távoli asztali hibák részletes hibaelhárítási lépéseit, ha az Azure-ban nem lehet Windows-alapú virtuális gépekre
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: nem tud csatlakozni a távoli asztalhoz, hibaelhárítás, a távoli asztal nem tud csatlakozni, távoli asztalhibák, távoli asztal hibaelhárítása, távoli asztalproblémák
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: ea448b87f9e6954abecead2934bfb7f4ed04a9c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920144"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>A Windows rendszerű virtuális gépekkel létesített távoli asztali kapcsolatok problémáinak részletes hibaelhárítási útmutatója
Ez a cikk részletes hibaelhárítási lépéseket tartalmaz a Windows-alapú Azure virtuális gépek összetett távoli asztalhibáinak diagnosztizálásához és javításához.

> [!IMPORTANT]
> A Távoli asztal általános hibáinak kiküszöbölése érdekében a folytatás előtt olvassa el [a Távoli asztal alapvető hibaelhárítási cikkét.](troubleshoot-rdp-connection.md)

Előfordulhat, hogy olyan távoli asztal hibaüzenet jelenik meg, amely nem hasonlít [a Távoli asztal alapvető hibaelhárítási útmutatójában](troubleshoot-rdp-connection.md)szereplő egyik hibaüzenethez sem. Kövesse az alábbi lépéseket annak megállapításához, hogy a távoli asztali (RDP) ügyfél miért nem tud csatlakozni az RDP szolgáltatáshoz az Azure virtuális gépen.


Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel [az MSDN Azure-ban és a Veremtúlcsordulás fórumain.](https://azure.microsoft.com/support/forums/) Azt is megteheti, hogy egy Azure-támogatási incidenst is benyújthat. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és kattintson a Támogatás beszerezni e **gombra.** Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.

## <a name="components-of-a-remote-desktop-connection"></a>Távoli asztali kapcsolat összetevői
Az RDP-kapcsolatban a következő összetevők vesznek részt:

![](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Mielőtt folytatná, segíthet mentálisan áttekinteni, hogy mi változott a virtuális géphez való legutóbbi sikeres távoli asztali kapcsolat óta. Példa:

* A virtuális gép nyilvános IP-címe vagy a virtuális gépet (más néven a virtuális IP-cím [VIP)](https://en.wikipedia.org/wiki/Virtual_IP_address)tartalmazó felhőszolgáltatás megváltozott. Az RDP-hiba oka lehet, hogy a DNS-ügyfél gyorsítótárában még mindig a *régi IP-cím* van regisztrálva a DNS-névhez. Ürítse ki a DNS-ügyfél gyorsítótárát, és próbálja meg újra csatlakoztatni a virtuális gép. Vagy próbálj meg közvetlenül csatlakozni az új VIP-hez.
* Egy külső alkalmazás használatával kezelheti a távoli asztali kapcsolatokat az Azure Portal által létrehozott kapcsolat használata helyett. Ellenőrizze, hogy az alkalmazás konfigurációja tartalmazza-e a távoli asztali forgalommegfelelő TCP-portját. Az [Azure Portalon](https://portal.azure.com)egy klasszikus virtuális gép a porton keresztül ellenőrizheti, hogy a virtuális gép beállításai > végpontok kattintva.

## <a name="preliminary-steps"></a>Előzetes lépések
Mielőtt továbblépne a részletes hibaelhárításhoz,

* Ellenőrizze a virtuális gép állapotát az Azure Portalon az esetleges nyilvánvaló problémák.
* Kövesse az [általános rdp-hibák gyorsjavítását az alapvető hibaelhárítási útmutatóban.](troubleshoot-rdp-connection.md#quick-troubleshooting-steps)
* Egyéni képek esetén győződjön meg arról, hogy a virtuális merevlemez megfelelően előkészített feltöltés előtt. További információt a [Windows VHD vagy VHDX feltöltésének előkészítése az Azure-ba című témakörben talál.](../windows/prepare-for-upload-vhd-image.md)


Próbáljon meg újra csatlakozni a virtuális géphez a távoli asztalon keresztül a következő lépések után.

## <a name="detailed-troubleshooting-steps"></a>Részletes hibaelhárítási útmutató
Előfordulhat, hogy a távoli asztali ügyfél nem tudja elérni a távoli asztali szolgáltatást az Azure virtuális gépen a következő forrásokban felmerülő problémák miatt:

* [Távoli asztali ügyfélszámítógép](#source-1-remote-desktop-client-computer)
* [Szervezeti intranetes peremhálózati eszköz](#source-2-organization-intranet-edge-device)
* [Felhőszolgáltatás végpont- és hozzáférés-vezérlési listája (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Network security groups (Hálózati biztonsági csoportok)](#source-4-network-security-groups)
* [Windows-alapú Azure Virtuális gép](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>1. forrás: Távoli asztali ügyfélszámítógép
Ellenőrizze, hogy a számítógép képes-e távoli asztali kapcsolatokat létesíteni egy másik helyszíni, Windows-alapú számítógéppel.

![](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Ha nem, ellenőrizze a következő beállításokat a számítógépen:

* Helyi tűzfalbeállítás, amely blokkolja a Távoli asztal forgalmát.
* Helyileg telepített ügyfélproxy szoftver, amely megakadályozza a távoli asztali kapcsolatokat.
* Helyileg telepített hálózatfigyelő szoftver, amely megakadályozza a távoli asztali kapcsolatokat.
* Más típusú biztonsági szoftverek, amelyek figyelik a forgalmat, vagy engedélyezik/lehetővé teszik a távoli asztali kapcsolatokat akadályozó bizonyos típusú forgalmat.

Ezekben az esetekben ideiglenesen tiltsa le a szoftvert, és próbáljon meg csatlakozni egy helyszíni számítógéphez a Távoli asztalon keresztül. Ha így meg tudja tudni a tényleges okot, a hálózati rendszergazdával együttműködve javítsa ki a szoftverbeállításokat a Távoli asztali kapcsolatok engedélyezéséhez.

## <a name="source-2-organization-intranet-edge-device"></a>Forrás 2: Szervezet intranetes peremhálózati eszköze
Ellenőrizze, hogy az internethez közvetlenül csatlakozó számítógép képes-e távoli asztali kapcsolatokat létesíteni az Azure virtuális gépéhez.

![](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Ha nem rendelkezik olyan számítógéppel, amely közvetlenül csatlakozik az internethez, hozzon létre és teszteljen egy új Azure virtuális gépegy erőforráscsoportban vagy felhőszolgáltatásban. További információt a [Windows rendszert futtató virtuális gép létrehozása az Azure-ban](../virtual-machines-windows-hero-tutorial.md)című témakörben talál. A teszt után törölheti a virtuális gépet és az erőforráscsoportot vagy a felhőszolgáltatást.

Ha távoli asztali kapcsolatot tud létrehozni egy közvetlenül az internethez csatlakoztatott számítógéppel, ellenőrizze a szervezet intranetes peremhálózati eszközét:

* Az internethez https-kapcsolatot blokkoló belső tűzfal.
* A távoli asztali kapcsolatokat megakadályozó proxykiszolgáló.
* Behatolásészlelés vagy a peremhálózati eszközökön futó hálózatfigyelő szoftver, amely megakadályozza a távoli asztali kapcsolatokat.

A hálózati rendszergazdával együttműködve javítsa ki a szervezet intranetes peremhálózati eszközének beállításait a HTTPS-alapú távoli asztali kapcsolatok engedélyezéséhez az internethez.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>3. forrás: A felhőszolgáltatás végpontja és az ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

A klasszikus üzembe helyezési modell használatával létrehozott virtuális gépek esetén ellenőrizze, hogy egy másik Azure-beli virtuális gép, amely ugyanabban a felhőszolgáltatásban vagy virtuális hálózatban található, képes-e távoli asztali kapcsolatokat létesíteni az Azure virtuális géphez.

![](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> Az Erőforrás-kezelőben létrehozott virtuális gépek esetében ugorjon a [4- es forrás: Hálózati biztonsági csoportok](#source-4-network-security-groups).

Ha nem rendelkezik másik virtuális gépugyanabban a felhőszolgáltatásban vagy virtuális hálózaton, hozzon létre egyet. Kövesse a Windows rendszert futtató virtuális gép létrehozása az [Azure-ban](../virtual-machines-windows-hero-tutorial.md)című lépéseit. Törölje a teszt virtuális gépet a teszt befejezése után.

Ha a Távoli asztalon keresztül ugyanazon a felhőszolgáltatásban vagy virtuális hálózatban lévő virtuális géphez tud csatlakozni, ellenőrizze az alábbi beállításokat:

* A célvirtuális gép távoli asztali forgalmának végpontkonfigurációja: A végpont privát TCP-portjának meg kell egyeznie a TCP-porttal, amelyen a virtuális gép távoli asztali szolgáltatása figyel (az alapértelmezett érték 3389).
* A távoli asztali forgalom végpontja a cél virtuális gép: ACL-ek lehetővé teszik, hogy adja meg az engedélyezett vagy elutasított bejövő forgalmat az internetről a forrás IP-címe alapján. A helytelenül konfigurált ACL-k megakadályozhatják a távoli asztali bejövő forgalmat a végpontra. Ellenőrizze az ACL-eket, és győződjön meg arról, hogy a proxy vagy más peremhálózati kiszolgáló nyilvános IP-címeiből érkező bejövő forgalom engedélyezett. További információ: [Mi a hálózati hozzáférés-vezérlési lista (ACL)?](../../virtual-network/virtual-networks-acl.md)

Annak ellenőrzéséhez, hogy a végpont-e a probléma forrása, távolítsa el az aktuális végpontot, és hozzon létre egy újat, és válasszon egy véletlenszerű portot a 49152–65535 tartományban a külső portszámhoz. További információ: [Végpontok beállítása virtuális gépre című témakörben.](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="source-4-network-security-groups"></a>4. forrás: Hálózati biztonsági csoportok
A hálózati biztonsági csoportok lehetővé teszik az engedélyezett bejövő és kimenő forgalom részletesebb vezérlését. Az Azure virtuális hálózatban alhálózatokra és felhőszolgáltatásokra kiterjedő szabályokat hozhat létre.

Az [IP-folyamat ellenőrzésével](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) győződjön meg róla, hogy a hálózati biztonsági csoportok szabályai nem blokkolják a virtuális gépek kimenő és bejövő forgalmát. A hatályos biztonsági csoportszabályokat is áttekintheti annak érdekében, hogy a bejövő "Engedélyezés" NSG-szabály létezik, és az RDP-port (alapértelmezett 3389) prioritása legyen. További információ: [A hatékony biztonsági szabályok használata a virtuális gép forgalomának hibáinak elhárításához.](../../virtual-network/diagnose-network-traffic-filter-problem.md)

## <a name="source-5-windows-based-azure-vm"></a>5. forrás: Windows-alapú Azure Virtuális gép
![](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Kövesse az ebben a [cikkben](../windows/reset-rdp.md)található utasításokat . Ez a cikk visszaállítja a távoli asztali szolgáltatást a virtuális gépen:

* Engedélyezze a "Távoli asztal" Windows tűzfal alapértelmezett szabályát (3389-es TCP-port).
* Távoli asztali kapcsolatok engedélyezése a HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections beállításérték 0-ra állításával.

Próbálkozzon újra a számítógépről a kapcsolattal. Ha továbbra sem tud csatlakozni a Távoli asztal on keresztül, ellenőrizze a következő lehetséges problémákat:

* A Távoli asztal szolgáltatás nem fut a cél virtuális gépen.
* A Távoli asztal szolgáltatás nem figyel a 3389-es TCP-porton.
* A Windows tűzfal vagy más helyi tűzfal rendelkezik egy kimenő szabállyal, amely megakadályozza a távoli asztal forgalmát.
* Az Azure virtuális gépen futó behatolásészlelés vagy hálózatfigyelő szoftver megakadályozza a távoli asztali kapcsolatokat.

A klasszikus üzembe helyezési modell használatával létrehozott virtuális gépek hez egy távoli Azure PowerShell-munkamenetet használhat az Azure virtuális géphez. Először telepítenie kell egy tanúsítványt a virtuális gép üzemeltetési felhőszolgáltatásához. Nyissa [meg a Biztonságos távoli PowerShell-hozzáférés konfigurálása az Azure virtuális gépekhez című](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) témakört, és töltse le az **InstallWinRMCertAzureVM.ps1** parancsfájlt a helyi számítógépre.

Ezután telepítse az Azure PowerShellt, ha még nem tette meg. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

Ezután nyisson meg egy Azure PowerShell-parancssort, és módosítsa az aktuális mappát az **InstallWinRMCertAzureVM.ps1** parancsfájl helyére. Egy Azure PowerShell-parancsfájl futtatásához be kell állítania a megfelelő végrehajtási szabályzatot. Futtassa a **Get-ExecutionPolicy** parancsot az aktuális házirendszint meghatározásához. A megfelelő szint beállításáról a [Set-ExecutionPolicy című témakörben talál](https://technet.microsoft.com/library/hh849812.aspx)további információt.

Ezután töltse ki az Azure-előfizetés nevét, a felhőszolgáltatás nevét és a virtuális gép nevét (a < és a > karakterek eltávolítása), majd futtassa ezeket a parancsokat.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

A **Get-AzureSubscription** parancs megjelenítésének *SubscriptionName* tulajdonságából a megfelelő előfizetésnevét kaphatja be. A virtuális gép felhőszolgáltatás nevét a **Get-AzureVM** parancs megjelenítésében található *ServiceName* oszlopból szerezheti be.

Ellenőrizze, hogy rendelkezik-e az új tanúsítvánnyal. Nyisson meg egy Tanúsítványok beépülő modult az aktuális felhasználószámára, és keresse meg a **Megbízható legfelső szintű hitelesítésszolgáltatók\Tanúsítványok** mappában. A felhőszolgáltatás DNS-nevével rendelkező tanúsítványnak a Kiállító oszlopban kell megjelennie (például: cloudservice4testing.cloudapp.net).

Ezután kezdeményezzen egy távoli Azure PowerShell-munkamenetet ezekkel a parancsokkal.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Az érvényes rendszergazdai hitelesítő adatok megadása után a következő Azure PowerShell-parancssorhoz hasonló üzenet jelenik meg:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

A kérdés első része a felhőszolgáltatás neve, amely tartalmazza a cél virtuális gép, amely eltérhet a "cloudservice4testing.cloudapp.net". Most már kiadhat Azure PowerShell-parancsokat ehhez a felhőszolgáltatáshoz az említett problémák kivizsgálásához és a konfiguráció javításához.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>A Távoli asztali szolgáltatások szolgáltatás tcp-portját manuálisan kijavítani
A távoli Azure PowerShell-munkamenet-parancssorban futtassa ezt a parancsot.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

A PortNumber tulajdonság az aktuális portszámot mutatja. Szükség esetén ezzel a paranccsal módosítsa a Távoli asztal portszámát az alapértelmezett értékre (3389).

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Ezzel a paranccsal ellenőrizze, hogy a port 3389-re módosult-e.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Lépjen ki a távoli Azure PowerShell-munkamenetből ezzel a paranccsal.

```powershell
Exit-PSSession
```

Ellenőrizze, hogy az Azure virtuális gép távoli asztali végpontja a 3398-as TCP-portot is használja-e belső portként. Indítsa újra az Azure virtuális gép, és próbálja meg újra a távoli asztali kapcsolat.

## <a name="additional-resources"></a>További források
[Jelszó vagy a Távoli asztal szolgáltatás visszaállítása Windows virtuális gépekhez](../windows/reset-rdp.md)

[How to install and configure Azure PowerShell (Az Azure PowerShell telepítése és konfigurálása)](/powershell/azure/overview)

[Secure Shell (SSH) kapcsolatok linuxos Azure virtuális géppel való – problémamegoldás](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Azure-beli virtuális gépen futó alkalmazásokhoz való hozzáférés hibaelhárítása](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

