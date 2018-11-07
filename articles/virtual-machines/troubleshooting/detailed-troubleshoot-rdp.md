---
title: Hibaelhárítás az Azure-ban részletes a távoli asztal |} A Microsoft Docs
description: Tekintse át egy Azure-beli Windows virtuális gépek távoli asztali hibák, ahol nem részletes hibaelhárítási lépései
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: nem lehet kapcsolódni a távoli asztal hibaelhárítása a távoli asztal, távoli asztal nem sikerül, távoli asztali hibák, a távoli asztal – hibaelhárítás, a távoli asztali problémák
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: af36f033dbca6c9f594b3568bfe7567a959e2d2f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51237152"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Windows virtuális gépek az Azure-ban a távoli asztali kapcsolatok problémáinak részletes hibaelhárítási lépései
Ez a cikk diagnosztizálhatja és megoldhatja az Azure-beli virtuális gépek Windows-alapú összetett távoli asztal-hibák részletes hibaelhárítási lépéseket.

> [!IMPORTANT]
> A távoli asztal gyakori hibák kiküszöbölése, mindenképpen olvassa el [a távoli asztal alapvető hibaelhárítási cikk](troubleshoot-rdp-connection.md) a folytatás előtt.

Felmerülhet a távoli asztal, hibaüzenet jelenik meg, az adott hibaüzenetek nem hasonlítanak a kezelt [az alapszintű távoli asztal – hibaelhárítási útmutató](troubleshoot-rdp-connection.md). Kövesse az alábbi lépéseket annak megállapításához, hogy miért a távoli asztali (RDP) ügyfél nem lehet csatlakozni az RDP-szolgáltatás az Azure virtuális gépen.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a [az MSDN Azure és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik megoldásként is fájl is egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) kattintson **támogatás kérése**. Az Azure-támogatás használatáról további információért olvassa el a [a Microsoft Azure támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Távoli asztali kapcsolat összetevői
A következő összetevőket is érint egy RDP-kapcsolat:

![](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

A folytatás előtt segíthet szellemi áttekintése, mi változott, a legutóbbi sikeres távoli asztali kapcsolatot a virtuális gép óta. Példa:

* A virtuális gép vagy a felhőalapú szolgáltatás, amely tartalmazza a virtuális gép nyilvános IP-címét (más néven a virtuális IP-cím [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) megváltozott. Az RDP-hiba oka az lehet, a DNS-ügyfél gyorsítótára még a *régi IP-cím* regisztrált DNS-nevét. A DNS-ügyfél-gyorsítótár ürítése, és próbáljon újra kapcsolódni a virtuális gép. Vagy próbáljon meg csatlakozni közvetlenül az új virtuális IP-cím.
* Egy külső alkalmazás segítségével kezelheti a távoli asztali kapcsolatokat a kapcsolatot az Azure portál által létrehozott használata helyett. Győződjön meg arról, hogy az alkalmazás konfigurációját tartalmazza-e a távoli asztali forgalmat a megfelelő TCP-portját. Ellenőrizheti, hogy ezt a portot, a klasszikus virtuális gép a [az Azure portal](https://portal.azure.com), a virtuális gép beállításainak kattintva > Végpontok.

## <a name="preliminary-steps"></a>Első lépések
A részletes hibaelhárítási, a folytatás előtt

* Nyilvánvaló problémákkal az Azure Portalon a virtuális gép állapotának ellenőrzéséhez.
* Kövesse a [gyorsjavítás az alapvető hibaelhárítási útmutató gyakori RDP-hibák lépések](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* Egyéni rendszerképek győződjön meg arról, hogy a virtuális merevlemez megfelelően előkészítve előtt töltse fel. További információkért lásd: [készítse elő a Windows VHD vagy VHDX, az Azure-bA feltöltendő](../windows/prepare-for-upload-vhd-image.md).


Próbáljon újra csatlakozni a virtuális gép távoli asztalon keresztül ezeket a lépéseket.

## <a name="detailed-troubleshooting-steps"></a>Részletes hibaelhárítási útmutató
A távoli asztali ügyfél nem lehet elérni a távoli asztali szolgáltatás a következő forrásokat a problémák miatt az Azure virtuális gépen:

* [Távoli asztali ügyfél-számítógépen](#source-1-remote-desktop-client-computer)
* [Szervezeti intraneten edge-eszköz](#source-2-organization-intranet-edge-device)
* [Cloud service-végpont, és hozzáférés-vezérlési lista (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Hálózati biztonsági csoportok](#source-4-network-security-groups)
* [Windows-alapú Azure virtuális gép](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>1. forrás: Távoli asztali ügyfél számítógép
Győződjön meg arról, hogy a számítógép kezdeményezhetik egy másik a helyszínen, Windows-alapú számítógép távoli asztali kapcsolatok.

![](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Ha nem, ellenőrizze a számítógépen a következő beállításokat:

* A beállítás, amely helyi tűzfal blokkolja a távoli asztali forgalmat.
* Helyileg telepített ügyfél proxy szoftver, amely megakadályozza, hogy a távoli asztali kapcsolatok.
* Helyileg telepítve szoftver, amely megakadályozza, hogy a távoli asztali kapcsolatok hálózatfigyelési.
* Más típusú biztonsági szoftver, amely a forgalom figyelésére, vagy az adott típusú forgalmat, amely megakadályozza, hogy a távoli asztali kapcsolatainak engedélyezése vagy letiltása.

Az összes ilyen esetben ideiglenesen tiltsa le a szoftvert, és próbáljon kapcsolódni a távoli asztalon keresztül a helyszíni számítógépen. Ha talál a tényleges OK ezzel a módszerrel, a hálózati rendszergazda segítségét a szoftverfrissítési beállításait, hogy a távoli asztali kapcsolatok használható.

## <a name="source-2-organization-intranet-edge-device"></a>2. forrás: Szervezeti intraneten edge-eszköz
Győződjön meg arról, hogy közvetlenül az internethez kapcsolódó számítógép kezdeményezhetik az Azure virtuális gép távoli asztali kapcsolatok.

![](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Ha nem rendelkezik olyan számítógépre, amely közvetlenül csatlakozik az internethez, hozzon létre, és a egy új Azure virtuális gép egy erőforrás vagy szolgáltatás tesztelése. További információkért lásd: [Windows Azure-ban futó virtuális gép létrehozása](../virtual-machines-windows-hero-tutorial.md). A vizsgálat után törölheti a virtuális gép és az erőforráscsoportot, vagy a felhőszolgáltatás.

Ha távoli asztali kapcsolatot is létrehozhat egy olyan számítógéppel, közvetlenül az internethez csatlakoztatott, ellenőrizze a szervezet intranetes edge-eszköz számára:

* Egy belső tűzfal blokkolja a HTTPS-kapcsolatok az interneten.
* Megakadályozza a távoli asztali kapcsolatok proxykiszolgálót.
* Behatolásészlelés vagy hálózatfigyelési az edge-hálózatot, amely megakadályozza, hogy a távoli asztali kapcsolatok az eszközön futó szoftvert.

Működjön együtt hálózati rendszergazdájával, javítsa ki a beállításokat a szervezet intranetes peremhálózati eszköz, hogy a HTTPS-alapú távoli asztali kapcsolatok az interneten.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>3. forrás: Cloud service-végpont és ACL-JEI
A klasszikus üzemi modellel létrehozott virtuális gép esetében ellenőrizze, hogy egy másik Azure virtuális Gépet, amely az ugyanazon a felhőszolgáltatáson vagy virtuális hálózati kezdeményezhetik távoli asztali kapcsolatok az Azure virtuális géphez.

![](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> A Resource Managerben létrehozott virtuális gépek, hagyja ki [forrás 4: a hálózati biztonsági csoportok](#source-4-network-security-groups).

Ha nem rendelkezik az ugyanazon a felhőszolgáltatáson vagy virtuális hálózat egy másik virtuális géphez, hozzon létre egyet. Kövesse a [Windows Azure-ban futó virtuális gép létrehozása](../virtual-machines-windows-hero-tutorial.md). Törölje a tesztcélú virtuális gépet, a teszt befejeződése után.

Ha egy virtuális géphez ugyanazon a felhőszolgáltatáson vagy virtuális hálózati távoli asztalon keresztül kapcsolódhatnak, ellenőrizze ezeket a beállításokat:

* A végpont-konfiguráció a távoli asztali forgalmat a cél virtuális Gépen: a végpont a privát TCP-portot meg kell egyeznie a TCP-portot, amelyen a virtuális gép távoli asztali szolgáltatás figyel (alapértelmezés: 3389).
* Az ACL-t a cél virtuális Gépre a távoli asztali forgalmat végpont: hozzáférés-vezérlési listák lehetővé teszik annak megadását engedélyezett vagy tiltott bejövő forgalom az internetről, a forrás IP-címe alapján. Helytelenül konfigurált hozzáférés-vezérlési listák megakadályozhatja a bejövő távoli asztali forgalmat a végponthoz. Ellenőrizze a hozzáférés-vezérlési listák, győződjön meg arról, hogy a proxy a nyilvános IP-címekről érkező bejövő forgalmat, vagy más biztonsági kiszolgáló engedélyezve van. További információkért lásd: [Mi az a hálózati hozzáférés-vezérlési lista (ACL)?](../../virtual-network/virtual-networks-acl.md)

Ellenőrizze, hogy a végpont-e a probléma forrása, távolítsa el a jelenlegi végpont, és hozzon létre egy újat egy véletlenszerű portot kiválasztása külső portszám 49152 – 65535 közötti tartományban. További információkért lásd: [beállítása a virtuális gép végpontjainak](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="source-4-network-security-groups"></a>4. forrás: A hálózati biztonsági csoportok
Hálózati biztonsági csoportok engedélyezése az engedélyezett bejövő és kimenő forgalom részletesebb vezérléshez. Alhálózatok átfedés szabályokat hozhat létre, és a felhőalapú szolgáltatások Azure-beli virtuális hálózathoz.

Az [IP-folyamat ellenőrzésével](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) győződjön meg róla, hogy a hálózati biztonsági csoportok szabályai nem blokkolják a virtuális gépek kimenő és bejövő forgalmát. Emellett áttekintheti az érvényben lévő biztonsági csoport szabályai annak biztosítása érdekében a bejövő "Engedélyezés" NSG-t a szabály létezik, és van priorizálva (alapértelmezés: 3389) RDP-portra. További információkért lásd: [használatával érvényes biztonsági szabályokat a virtuális gép forgalom áramlása](../../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="source-5-windows-based-azure-vm"></a>5. forrás: Windows-alapú Azure virtuális gép
![](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Kövesse a [Ez a cikk](../windows/reset-rdp.md). Ez a cikk a virtuális gépen a távoli asztal szolgáltatás alaphelyzetbe állítása:

* Engedélyezze a "Távoli asztali" Windows tűzfal alapértelmezett szabály (3389-es TCP-port).
* Engedélyezze a távoli asztali kapcsolatok azzal a HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections beállításazonosítót a 0.

Próbálja meg újra a kapcsolatot a számítógépéről. Ha Ön még mindig nem tudja, távoli asztali kapcsolaton keresztül, ellenőrizze a következő lehetséges problémákat:

* A távoli asztali szolgáltatás nem fut a cél virtuális Gépen.
* A távoli asztali szolgáltatás nem figyel a következőn: 3389-es TCP-porton.
* Windows tűzfal vagy egy másik helyi tűzfal rendelkezik az kimenő szabályt, amely megakadályozza, hogy a távoli asztali forgalmat.
* Behatolásészlelés vagy hálózatfigyelési az Azure virtuális gépen futó szoftver nem engedélyezi, távoli asztali kapcsolatok.

A klasszikus üzemi modellel létrehozott virtuális gép esetében használhatja az Azure virtuális gépen egy távoli Azure PowerShell-munkamenetet. Először telepítenie kell egy tanúsítványt a virtuális gépet üzemeltető felhőszolgáltatás számára. Lépjen a [biztonságos távoli PowerShell elérésének konfigurálása az Azure Virtual Machines](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) , és töltse le a **InstallWinRMCertAzureVM.ps1** parancsfájlt a helyi számítógépen.

Ezután telepítse az Azure PowerShell-lel, ha még nem tette. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

Ezután nyisson meg egy Azure PowerShell-parancssort, és módosítsa az aktuális mappa helyét, a **InstallWinRMCertAzureVM.ps1** parancsfájlt. Az Azure PowerShell-parancsprogrammal, be kell a megfelelő végrehajtási házirendet. Futtassa a **Get-ExecutionPolicy** parancsot annak meghatározásához, a jelenlegi házirend szint. További információ a megfelelő szint: [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Ezután töltse ki az Azure-előfizetés neve, a felhőszolgáltatás neve és a virtuális gép nevét (eltávolítása a < és > karakterek), majd futtassa ezeket a parancsokat.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

Megtekintheti a megfelelő előfizetés nevét a *SubscriptionName* tulajdonságának megjelenítéséhez a **Get-AzureSubscription** parancsot. Beszerezheti a felhőszolgáltatás neve a virtuális gép számára a *ServiceName* megjelenítésének oszlopa a **Get-AzureVM** parancsot.

Ellenőrizze, hogy van-e az új tanúsítványt. Nyissa meg a tanúsítványok beépülő modul az aktuális felhasználó számára, és tekintse meg a **megbízható legfelső szintű hitelesítésszolgáltatók\Tanúsítványok** mappát. Megjelenik egy tanúsítványt a tulajdonos oszlopban a cloud service DNS-nevét (Példa: cloudservice4testing.cloudapp.net).

Ezután kezdeményezése egy távoli Azure PowerShell-munkamenetet a parancsok használatával.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Ha érvényes rendszergazdai hitelesítő adatok megadása, jelenik meg a következő Azure PowerShell-parancssorba hasonló:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

Ez a kérdés első része a felhőszolgáltatás neve, amely tartalmazza a céloldali virtuális Gépet, amely eltér a "cloudservice4testing.cloudapp.net" lehet. A felhőalapú szolgáltatás, a problémák vizsgálatára parancsok említett, és javítsa ki a konfigurációs Azure PowerShell most már küldhet.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Manuálisan a TCP-portot figyeli a távoli asztali szolgáltatások javítása
Az Azure PowerShell távoli munkamenet parancssorba a következő parancs futtatásával.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

A portszám tulajdonság a jelenlegi port számát jeleníti meg. Ha szükséges, módosítsa a távoli asztal port száma vissza az alapértelmezett értékére (3389-es) a következő paranccsal.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Győződjön meg arról, hogy a port megváltozott-e a 3389-es a következő paranccsal.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

A következő paranccsal lépjen ki a távoli Azure PowerShell-munkamenetet.

```powershell
Exit-PSSession
```

Győződjön meg arról, hogy a távoli asztali végpontot, az Azure virtuális gép is használ TCP-port 3398 a belső portként. Indítsa újra az Azure virtuális Gépen, és próbálja meg újra a távoli asztali kapcsolatot.

## <a name="additional-resources"></a>További források
[Jelszó vagy a Windows virtuális gépek távoli asztali szolgáltatás visszaállítása](../windows/reset-rdp.md)

[Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)

[Egy Linux-alapú Azure virtuális gép Secure Shell (SSH) kapcsolatok hibaelhárítása](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Azure-beli virtuális gépen futó alkalmazásokhoz való hozzáférés hibaelhárítása](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

