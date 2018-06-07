---
title: Hibaelhárítás az Azure-ban részletes távoli asztal |} Microsoft Docs
description: Tekintse át a Windows Azure virtuális gépek távoli asztali hibáit, ahol nem részletes hibaelhárítási lépéseket
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: nem lehet kapcsolódni a távoli asztal hibaelhárítása a távoli asztal, távoli asztali hibák, a távoli asztal – hibaelhárítás, a távoli asztali problémák nem lehet kapcsolódni a távoli asztal, amelyeket
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: ab101d78320819b9fb48f2c431fb0f6afdb895ec
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657825"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Windows Azure virtuális gépek távoli asztali kapcsolat hibák részletes hibaelhárítási lépéseket
A cikkben az Azure virtuális gépek Windows-alapú összetett távoli asztal hibák megoldásában részletes hibaelhárítási lépéseket.

> [!IMPORTANT]
> A távoli asztal gyakori hibák elkerülése érdekében, mindenképpen olvassa el [a távoli asztal alapvető hibaelhárítási cikk](troubleshoot-rdp-connection.md) a folytatás előtt.

Foglalt hibaüzenet jelenik meg, az adott hibaüzenetek nem hasonlítanak a távoli asztal felmerülhet [a távoli asztal alapvető hibaelhárítási útmutató](troubleshoot-rdp-connection.md). Kövesse az alábbi lépéseket annak meghatározásához, hogy miért a távoli asztal (RDP) ügyfél nem tud kapcsolódni az RDP-szolgáltatáshoz, az Azure virtuális gépen.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a [az MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl is az Azure támogatási incidens. Lépjen a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) kattintson **támogatás**. Azure-támogatás használatával kapcsolatos információkért olvassa el a [Microsoft Azure támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Távoli asztali kapcsolat összetevői
A következő összetevők részt egy RDP-kapcsolat:

![](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

A folytatás előtt hasznos lehet a szellemi tekintse át a virtuális gép utolsó sikeres távoli asztali kapcsolat óta változásai. Példa:

* A virtuális gép vagy a virtuális Gépet tartalmazó felhőalapú szolgáltatás nyilvános IP-címét (más néven a virtuális IP-cím [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) megváltozott. Az RDP hiba oka lehet, a DNS-ügyfél gyorsítótára még a *korábbi IP-címet* regisztrálva a DNS-nevét. A DNS-ügyfél-gyorsítótár ürítése, és próbáljon újra csatlakozni a virtuális gép. Vagy próbáljon közvetlenül az új VIP.
* A külső alkalmazás által az Azure-portálon létrehozott kapcsolat használata helyett a távoli asztali kapcsolatok kezelésére használ. Győződjön meg arról, hogy az alkalmazás konfigurációját tartalmazza-e a megfelelő TCP-portot a távoli asztal forgalomhoz. Ellenőrizheti, hogy ezt a portot, a klasszikus virtuális gép a [Azure-portálon](https://portal.azure.com), kattintson a Virtuálisgép-beállítások > Végpontok.

## <a name="preliminary-steps"></a>Első lépések
A részletes hibaelhárítási, a folytatás előtt

* Az Azure-portál a nyilvánvaló probléma merül fel a virtuális gép állapotának ellenőrzése.
* Kövesse a [gyorsjavítást közös RDP hibák az alapvető hibaelhárítási útmutatóban található lépések](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* Győződjön meg arról, hogy a virtuális merevlemez megfelelő előkészítése előtt töltse fel az egyéni lemezképek. További információkért lásd: [Windows VHD vagy VHDX az Azure-bA feltöltendő előkészítése](prepare-for-upload-vhd-image.md).


Próbáljon újra csatlakozni a virtuális gép távoli asztalon keresztül, a lépések után.

## <a name="detailed-troubleshooting-steps"></a>Részletes hibaelhárítási útmutató
Lehet, hogy a távoli asztali ügyfél nem képes elérni a távoli asztali szolgáltatás a következő forrásokból, problémákból adódó az Azure virtuális gépen:

* [Távoli asztali ügyfél számítógép](#source-1-remote-desktop-client-computer)
* [Szervezeti intraneten peremhálózati eszköz](#source-2-organization-intranet-edge-device)
* [A felhőalapú szolgáltatás végpontjának és hozzáférés-vezérlési lista (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Hálózati biztonsági csoportok](#source-4-network-security-groups)
* [Windows-alapú Azure virtuális gép](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>1. forrás: Távoli asztali ügyfél számítógép
Győződjön meg arról, hogy a számítógép biztosíthat a távoli asztali kapcsolatot egy másik a helyszínen, a Windows-alapú számítógép.

![](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Ha nem, ellenőrizze a számítógépen a következő beállításokat:

* Egy helyi beállítástól, amely blokkolja tűzfal a távoli asztal-forgalmat.
* Helyileg telepített ügyfél proxy szoftver, amely megakadályozza, hogy a távoli asztali kapcsolatok.
* Helyileg telepített szoftver, amely megakadályozza, hogy a távoli asztali kapcsolatok hálózatfigyelési.
* Más típusú biztonsági szoftver felügyeljék a forgalmat, vagy a megadott típusú forgalom, amely megakadályozza, hogy a távoli asztali kapcsolatok engedélyezése vagy letiltása.

Ideiglenesen minden ezekben az esetekben, tiltsa le a szoftvert, és próbáljon csatlakozni egy távoli asztalon keresztül a helyi számítógépen. Ha a tényleges OK így talál, a hálózati rendszergazda segítségét a szoftverfrissítési beállítások lehetővé teszik a távoli asztali kapcsolatok használható.

## <a name="source-2-organization-intranet-edge-device"></a>2. forrás: Szervezet intranetes peremhálózati eszköz
Győződjön meg arról, hogy közvetlenül az internethez kapcsolódó számítógép biztosíthat a távoli asztali kapcsolatok az Azure virtuális géphez.

![](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Ha még nem rendelkezik olyan számítógépre, amely közvetlenül kapcsolódik az internethez, hozzon létre, és egy új Azure virtuális gép egy erőforrás vagy felhőhatókört szolgáltatás tesztelése. További információkért lásd: [Windows Azure-ban futó virtuális gép létrehozása](../virtual-machines-windows-hero-tutorial.md). A virtuális gép és az erőforráscsoportot, vagy a felhőalapú szolgáltatás, a vizsgálat megszüntetését követően törölheti.

Ha a távoli asztali kapcsolat hozhat létre egy olyan számítógéppel, közvetlenül csatlakozik az internethez, ellenőrizze a szervezeti intraneten peremhálózati eszköz a:

* Egy belső tűzfal blokkolja a HTTPS-kapcsolatok az interneten.
* Megakadályozza a távoli asztali kapcsolatok proxykiszolgálót.
* Behatolásérzékelési vagy hálózatfigyelési a peremhálózati hálózat, amely megakadályozza, hogy a távoli asztali kapcsolatok eszközökön futó szoftver.

A hálózati rendszergazdával, javítsa ki a szervezeti intraneten peremhálózati eszköz a beállítások lehetővé teszik a HTTPS-alapú távoli asztali kapcsolatok az interneten működnek.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>3. forrás: Felhő szolgáltatásvégpont és hozzáférés-vezérlési lista
A klasszikus telepítési modell használatával létrehozott virtuális gép esetében ellenőrizze, hogy egy másik Azure virtuális Gépet, amely az ugyanazon a felhőalapú szolgáltatás, vagy a virtuális hálózati tehet távoli asztali kapcsolatok az Azure virtuális gép.

![](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> A virtuális gépek erőforrás-kezelőt hozott létre, folytassa a [forrás 4: hálózati biztonsági csoportok](#source-4-network-security-groups).

Ha egy másik virtuális gép felhőalapú szolgáltatás- vagy virtuális hálózat nem rendelkezik, hozzon létre egyet. Kövesse a [Windows Azure-ban futó virtuális gép létrehozása](../virtual-machines-windows-hero-tutorial.md). Törli a teszt virtuális gépet, a vizsgálat befejezése után.

Ha a Kapcsolódás távoli asztalon keresztül a felhőalapú szolgáltatás- vagy virtuális hálózati egy virtuális géphez, ellenőrizze ezeket a beállításokat:

* A végpont-konfiguráció a távoli asztal-forgalmat a cél virtuális gép: a végpont titkos TCP-portot meg kell egyeznie a TCP-portot, amelyen a virtuális gép távoli asztal szolgáltatás figyeli-e (alapértelmezett érték a 3389-es).
* A cél virtuális gép a távoli asztal forgalom végpont ACL: hozzáférés-vezérlési listák lehetővé teszik adja meg a engedélyezett vagy megtagadott a bejövő forgalom az internetről, ha a forrás IP-címe alapján. Helytelenül konfigurált hozzáférés-vezérlési listák előfordulhat, hogy a végpont a távoli asztal a bejövő forgalom. Ellenőrizze a hozzáférés-vezérlési listákat, annak érdekében, hogy a proxy a nyilvános IP-címekről érkező bejövő forgalmat, vagy más biztonsági kiszolgáló engedélyezett. További információkért lásd: [Mi az a hálózati hozzáférés-vezérlési lista (ACL)?](../../virtual-network/virtual-networks-acl.md)

Ellenőrizze, hogy a végpont-e a probléma, távolítsa el a jelenlegi végpontot, és hozzon létre egy újat, véletlenszerű port kiválasztása külső portszám 49152 – 65535 közötti tartományban. További információkért lásd: [beállítása végpontok egy virtuális géphez](classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="source-4-network-security-groups"></a>4. forrás: Hálózati biztonsági csoportok
Hálózati biztonsági csoportok lehetővé teszik a részletesebben vezérelhető, engedélyezett bejövő és kimenő forgalom. Alhálózatok átfedés szabályokat hozhat létre, és a felhőalapú szolgáltatások egy Azure virtuális hálózatra.

Az [IP-folyamat ellenőrzésével](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) győződjön meg róla, hogy a hálózati biztonsági csoportok szabályai nem blokkolják a virtuális gépek kimenő és bejövő forgalmát. Hatékony biztonsági csoport szabályokat, hogy biztosítsa a bejövő "Engedélyezés" NSG-t is felhasználhatja a szabály létezik-e, és a rendszer előrébb RDP-porthoz (3389-es alapértelmezett). További információkért lásd: [használatával hatékony biztonsági szabályokat hibáinak elhárítása a virtuális gép forgalom bonyolódjon](../../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="source-5-windows-based-azure-vm"></a>5. forrás: Windows-alapú Azure virtuális gép
![](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Kövesse az utasításokat a [Ez a cikk](reset-rdp.md). Ez a cikk visszaállítja a távoli asztali szolgáltatás a virtuális gépen:

* Engedélyezze a "Távoli asztal" Windows tűzfal alapértelmezett szabály (3389-es TCP-port).
* Távoli asztali kapcsolatok engedélyezése a HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections beállításazonosítót 0 értékre állításával.

Próbálja meg újra a kapcsolatot a számítógépről. Ha továbbra sem lehet a távoli asztalon keresztül csatlakozni, ellenőrizze a következő lehetséges okok:

* A távoli asztali szolgáltatás nem fut a cél virtuális gép.
* A távoli asztali szolgáltatás nem figyeli a 3389-es TCP-port.
* A Windows tűzfal vagy egy másik helyi tűzfal van egy kimenő forgalomra vonatkozó szabály, amely megakadályozza a távoli asztal forgalmat.
* Távoli asztali kapcsolatok behatolásérzékelési vagy az Azure virtuális gépen futó szoftverek hálózatfigyelési megakadályozza.

A virtuális gépek a klasszikus telepítési modellel készült használhatja a távoli Azure PowerShell-munkamenetben az Azure virtuális géphez. Először meg kell a virtuális gép üzemeltetési felhőszolgáltatás tanúsítvány telepítése. Ugrás a [biztonságos távoli PowerShell elérésének konfigurálása az Azure virtuális gépek](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) , és töltse le a **InstallWinRMCertAzureVM.ps1** parancsfájlt a helyi számítógépen.

Következő lépésként telepítse az Azure PowerShell, ha még nem tette meg. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

Ezután nyissa meg az Azure PowerShell-parancssort, és módosítsa az aktuális mappa helyét az **InstallWinRMCertAzureVM.ps1** parancsfájlt. Az Azure PowerShell parancsfájl futtatásához meg kell adni a megfelelő végrehajtási házirendet. Futtassa a **Get-ExecutionPolicy** parancsot annak meghatározásához, a jelenlegi házirendszint. További információ a megfelelő szint: [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Ezután töltse ki az Azure-előfizetés nevét, a felhőszolgáltatás neve és a virtuális gép nevét (eltávolítása a < és > karakter), és futtassa a parancsokat.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

Beszerezheti a megfelelő előfizetés nevét a *SubscriptionName* megjelenítését tulajdonsága a **Get-AzureSubscription** parancs. A felhőszolgáltatás neve a virtuális gépek esetén a kaphat a *szolgáltatásnév* a a megjelenített oszlop a **Get-AzureVM** parancs.

Ellenőrizze, hogy van-e az új tanúsítványt. Nyissa meg a tanúsítványok beépülő modul az aktuális felhasználó számára, és keresse meg a **megbízható legfelső szintű hitelesítésszolgáltatók\Tanúsítványok** mappa. Láthatja, hogy egy tanúsítványt a felhőalapú szolgáltatás, a tulajdonos oszlopban szereplő DNS-nevét (például: cloudservice4testing.cloudapp.net).

A következő Azure PowerShell távoli munkamenet kezdeményezése az alábbi parancsokkal.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Érvényes rendszergazdai hitelesítő adatok megadása, megtekintheti az alábbihoz hasonlót a következő Azure PowerShell-parancssorba:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

Ez az üzenet első része a felhőszolgáltatás neve, amely tartalmazza a cél virtuális gép, amely eltér a "cloudservice4testing.cloudapp.net" lehet. Most kiadhatja Azure PowerShell parancsok a felhőalapú szolgáltatás, a problémák vizsgálatára említett, és javítsa ki a konfigurációt.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Kijavíthatja a TCP-portot figyeli a távoli asztali szolgáltatások számára
A parancssorba a távoli Azure PowerShell munkamenet, futtassa a parancsot.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

A portszám tulajdonság jelenlegi port számát mutatja. Ha szükséges, módosítsa a távoli asztal port száma vissza az alapértelmezett értékre (3389-es) Ez a parancs használatával.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Győződjön meg arról, hogy a port megváltozott-e a 3389-es a parancs segítségével.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Kilépés a távoli Azure PowerShell-munkamenetet a parancs segítségével.

```powershell
Exit-PSSession
```

Győződjön meg arról, hogy a távoli asztal végpont az az Azure virtuális gépekhez is használ 3398 TCP-portot, a belső portjára. Indítsa újra az Azure virtuális Gépen, és próbálja meg újra a távoli asztali kapcsolat.

## <a name="additional-resources"></a>További források
[Jelszó vagy a Windows virtuális gépekhez távoli asztal szolgáltatás alaphelyzetbe állításával](reset-rdp.md)

[Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)

[Végezzen hibaelhárítást a Secure Shell (SSH) kapcsolatokon egy Linux-alapú Azure virtuális géphez](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Azure-beli virtuális gépen futó alkalmazásokhoz való hozzáférés hibaelhárítása](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

