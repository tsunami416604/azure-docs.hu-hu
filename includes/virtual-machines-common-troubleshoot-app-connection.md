---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/17/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ab668a905b435287a4eaf96ff04b2fa5b54deb1d
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313513"
---
Nincsenek különböző okokból nem kezdődhet vagy egy Azure virtuális gépen (VM) futó alkalmazás csatlakozzon. Okai az alkalmazás nem fut, vagy a várt porton figyel, a figyelő portja, blokkolva vagy a hálózat szabályok nem megfelelő az alkalmazás sikeres forgalmat. Ez a cikk ismerteti a módszeres megközelítés található, és kijavítja a hibát.

Ha a virtuális gép RDP és az SSH használatával való kapcsolódás problémát tapasztal, először tekintse meg a következő cikkekben egyikét:

* [Távoli asztali kapcsolatok számára Windows-alapú Azure virtuális gép hibaelhárítása](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)
* [Végezzen hibaelhárítást a Secure Shell (SSH) kapcsolatokon a Linux-alapú Azure virtuális gépekhez](../articles/virtual-machines/linux/troubleshoot-ssh-connection.md).

> [!NOTE]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../articles/resource-manager-deployment-model.md). A jelen cikk mindkét modell használatát bemutatja, de a Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a [az MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl is az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az beszerzése**.

## <a name="quick-start-troubleshooting-steps"></a>Gyors üzembe helyezési hibaelhárítási lépések
Ha egy alkalmazás problémái, próbálja a következő általános hibaelhárítási lépéseket. Minden lépés után próbáljon meg újra az alkalmazáshoz:

* A virtuális gép újraindítása
* Hozza létre újra a végpontot / tűzfal-szabályokat / hálózati biztonsági csoport (NSG) szabályok
  * [Erőforrás-kezelő modell - hálózati biztonsági csoportok kezelése](../articles/virtual-network/manage-network-security-group.md)
  * [Klasszikus modell - Felhőszolgáltatások kezelése végpontok](../articles/cloud-services/cloud-services-enable-communication-role-instances.md)
* Csatlakozás másik helyről, például különböző Azure virtuális hálózathoz
* A virtuális gép újbóli üzembe helyezése
  * [Telepítse újra a Windows virtuális gép](../articles/virtual-machines/windows/redeploy-to-new-node.md)
  * [Telepítse újra a Linux virtuális gép](../articles/virtual-machines/linux/redeploy-to-new-node.md)
* Hozza létre újra a virtuális gép

További információkért lásd: [hibaelhárítási végpont kapcsolat (RDP/SSH/HTTP, hiba stb.)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Részletes hibaelhárítási áttekintése
Nincsenek a hozzáférést egy Azure virtuális gépen futó alkalmazás hibaelhárítása négy fő területet.

![hibaelhárítás alkalmazás nem indítható el.](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. Az Azure virtuális gépen futó alkalmazás.
   * Maga az alkalmazás megfelelően fut?
2. Az Azure virtuális géphez.
   * Az a virtuális gépért megfelelően működik és válaszol a kérelmekre?
3. Azure-hálózat végpontok.
   * A felhőalapú szolgáltatás végpontok a klasszikus üzembe helyezési modellel virtuális gépekhez.
   * Hálózati biztonsági csoportok és a bejövő NAT-szabályok virtuális gépek erőforrás-kezelő üzembe helyezési modellben.
   * Folyamat a felhasználók a virtuális gép/alkalmazás a várt portokon is forgalom?
4. Az Internet peremhálózati eszköz.
   * Tűzfalszabályok helyen akadályozzák a forgalom továbbítására megfelelően?

A pont-pont származó VPN- vagy ExpressRoute-kapcsolaton keresztül az alkalmazást használó ügyfélszámítógépek számára a fő területet problémákat okozhat, az alkalmazás és az Azure virtuális géphez.

A problémáról és annak javítása megállapításán, kövesse az alábbi lépéseket.

## <a name="step-1-access-application-from-target-vm"></a>1. lépés: A cél virtuális gép alkalmazás elérése
Próbáljon meg hozzáférni az alkalmazást a megfelelő ügyféloldali program amelyen fut a virtuális gépről. A helyi számítógép neve, a helyi IP-cím vagy a visszacsatolási cím (127.0.0.1) használja.

![Indítsa el az alkalmazás közvetlenül a virtuális gépről](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Például ha az alkalmazás egy webkiszolgálón, nyisson meg egy böngészőt, a virtuális Gépre, és próbáljon meg hozzáférni egy weblapot, a virtuális Gépen futó.

Ha az alkalmazást érheti el, folytassa a [2. lépés](#step2).

Ha az alkalmazás nem fér hozzá, ellenőrizze a következő beállításokat:

* Az alkalmazás fut a cél virtuális gépen.
* Az alkalmazás a várt TCP és UDP-porton figyel.

A Windows és Linux-alapú virtuális gépek használata a **netstat - a** aktív figyelőportjait a parancsot. Vizsgálja meg a várt portokat, amelyen az alkalmazás figyelésére kell a kimenetét. Indítsa újra az alkalmazást, vagy konfigurálja úgy, hogy a várt portok használatára, szükség szerint, és próbálja meg újra az alkalmazás helyi eléréséhez.

## <a id="step2"></a>2. lépés: Az azonos virtuális hálózatban lévő másik virtuális alkalmazás elérése
Próbáljon meg hozzáférni az alkalmazás, egy másik virtuális gépről, de az azonos virtuális hálózatban, a virtuális gép állomásnevét vagy az Azure által hozzárendelt public, private vagy szolgáltatói IP-címét. A virtuális gépek a klasszikus telepítési modellel készült ne használja a felhőalapú szolgáltatás nyilvános IP-címét.

![Indítsa el az alkalmazás egy másik virtuális gépről](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Például ha az alkalmazás egy webkiszolgálón, próbáljon meg hozzáférni egy weblap eltérő virtuális gépet az azonos virtuális hálózatban lévő böngészővel.

Ha az alkalmazást érheti el, folytassa a [3. lépés](#step3).

Ha az alkalmazás nem fér hozzá, ellenőrizze a következő beállításokat:

* A cél virtuális gép a gazdagép tűzfalának engedélyezi, hogy a bejövő kérelem és a kimenő válasz forgalmat.
* Behatolásérzékelési vagy a cél virtuális gép futó szoftver hálózatfigyelési átengedi a forgalmat.
* Cloud Services végpontjainak vagy a hálózati biztonsági csoportok a forgalmat engedélyezi:
  * [Klasszikus modell - Felhőszolgáltatások kezelése végpontok](../articles/cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Erőforrás-kezelő modell - hálózati biztonsági csoportok kezelése](../articles/virtual-network/manage-network-security-group.md)
* Egy külön összetevő fut a virtuális Gépet, az elérési út között a teszt virtuális gép és a virtuális Gépet, például terheléselosztó vagy tűzfal, engedélyezi, hogy a forgalmat.

A Windows-alapú virtuális gépen biztonságú Windows tűzfal segítségével határozza meg, hogy a tűzfalszabályok kizárása az alkalmazás bejövő és kimenő forgalmat.

## <a id="step3"></a>3. lépés: A virtuális hálózaton kívül, az alkalmazás elérése
Az alkalmazáshoz való hozzáférés a virtuális hálózaton kívüli gépről a virtuális gép, amelyen az alkalmazás fut, próbálja meg. Egy másik hálózati használják az eredeti ügyfélszámítógépen.

![Indítsa el az alkalmazást a virtuális hálózaton kívüli gépről](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Például ha az alkalmazás egy webkiszolgálón, próbáljon meg hozzáférni a weblap, amely nincs a virtuális hálózatban lévő számítógépeken futó böngészővel.

Ha az alkalmazás nem fér hozzá, ellenőrizze a következő beállításokat:

* A virtuális gépek a klasszikus telepítési modellel készült:
  
  * Győződjön meg arról, hogy a végpont-konfiguráció, a virtuális gép átengedi a bejövő forgalmat, különösen a protocol (TCP és UDP) és a nyilvános és titkos portszámokat.
  * Győződjön meg arról, hogy a végpont hozzáférés-vezérlési listák (ACL) nem akadályozzák meg a bejövő forgalom az internetről.
  * További információkért lásd: [hogyan állítsa be végpontok egy virtuális géphez](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* A virtuális gépek erőforrás-kezelő telepítési modellel készült:
  
  * Győződjön meg arról, hogy a VM bejövő NAT-szabály konfigurációjának átengedi a bejövő forgalmat, különösen a protocol (TCP és UDP) és a nyilvános és titkos portszámokat.
  * Győződjön meg arról, hogy hálózati biztonsági csoport engedélyezi a bejövő kérelem és a kimenő válasz forgalmat.
  * További információkért lásd: [Mi az a hálózati biztonsági csoport?](../articles/virtual-network/security-overview.md)

Ha a virtuális gép vagy a végpont egy elosztott terhelésű készlet tagja:

* Győződjön meg arról, hogy a mintavétel protocol (TCP és UDP) és a portszám helyességéről.
* Ha a mintavételi protokoll és port eltér attól az elosztott terhelésű készlet protokoll és port:
  * Győződjön meg arról, hogy az alkalmazás figyeli-e a mintavételi protocol (TCP és UDP) és a portszám (használata **netstat – a** a cél virtuális gép).
  * Győződjön meg arról, hogy a cél virtuális gép a gazdagép tűzfalának engedélyezi, hogy a bejövő mintavételi kérelem és a kimenő mintavételi válasz forgalmat.

Ha az alkalmazást érheti el, győződjön meg arról, hogy az Internet peremhálózati eszközön keresztülhaladó:

* A kimenő alkalmazás kérelem-forgalom az ügyfélszámítógépen az Azure virtuális géphez.
* A bejövő kérelem válasz forgalmát az Azure virtuális géphez.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Lépés 4 Ha nem fér hozzá az alkalmazás segítségével IP ellenőrizze ellenőrizze a beállításokat. 

További információkért lásd: [áttekintése Azure hálózatfigyelési](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="additional-resources"></a>További források
[Távoli asztali kapcsolatok számára Windows-alapú Azure virtuális gép hibaelhárítása](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)

[Végezzen hibaelhárítást a Secure Shell (SSH) kapcsolatokon egy Linux-alapú Azure virtuális géphez](../articles/virtual-machines/linux/troubleshoot-ssh-connection.md)

