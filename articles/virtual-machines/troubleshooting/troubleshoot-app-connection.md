---
title: Virtuálisgép-alkalmazások Azure-beli hozzáférésének hibáinak megoldása | Microsoft Docs
description: Ezekkel a részletes hibaelhárítási lépésekkel elkülönítheti az Azure-beli virtuális gépeken futó alkalmazásokhoz való csatlakozással kapcsolatos problémákat.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: az alkalmazás nem indítható el, a program nem nyílik meg, a figyelő port blokkolva, nem indítható el a program, a figyelési port blokkolva
ms.assetid: b9ff7cd0-0c5d-4c3c-a6be-3ac47abf31ba
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: a2fe1b8bdc80a5265add22bd3602050034471fe2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87036437"
---
# <a name="troubleshoot-application-connectivity-issues-on-virtual-machines-in-azure"></a>Azure-beli virtuális gépeken futó alkalmazások csatlakozási hibáinak elhárítása

Az Azure-beli virtuális gépen (VM) futó alkalmazások nem indíthatók el és nem csatlakozhatnak. Az okok közé tartozik az alkalmazás nem fut, vagy nem figyeli a várt portokat, a figyelő port letiltva, vagy a hálózati szabályok nem adják meg megfelelően az alkalmazás felé irányuló forgalmat. Ez a cikk a probléma megoldásának módszerét ismerteti.

Ha a virtuális géphez RDP vagy SSH használatával csatlakozik, először olvassa el az alábbi cikkek egyikét:

* [Windows-alapú Azure-beli virtuális gépekkel létesített Távoli asztal-kapcsolatok hibáinak megoldása](troubleshoot-rdp-connection.md)
* [A Secure Shell-(SSH-) kapcsolatok hibakeresése Linux-alapú Azure-beli virtuális gépeken](troubleshoot-ssh-connection.md).

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel [az MSDN Azure-ban és a stack overflow fórumokon](https://azure.microsoft.com/support/forums/). Azt is megteheti, hogy Azure-támogatási incidenst is beküld. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.

## <a name="quick-start-troubleshooting-steps"></a>Gyors indítás – hibaelhárítási lépések
Ha problémája merül fel az alkalmazáshoz való csatlakozás során, próbálkozzon az alábbi általános hibaelhárítási lépésekkel. Az egyes lépések után próbálkozzon újra az alkalmazással való csatlakozással:

* A virtuális gép újraindítása
* Hozza létre újra a végponti/tűzfalszabályok/hálózati biztonsági csoport (NSG) szabályait
  * [Resource Manager-modell – hálózati biztonsági csoportok kezelése](../../virtual-network/manage-network-security-group.md)
  * [Klasszikus modell – Cloud Services-végpontok kezelése](../../cloud-services/cloud-services-enable-communication-role-instances.md)
* Más helyről, például egy másik Azure-beli virtuális hálózatról is kapcsolódhat
* A virtuális gép ismételt üzembe helyezése
  * [Windows rendszerű virtuális gép újbóli üzembe helyezése](redeploy-to-new-node-windows.md)
  * [Linux rendszerű virtuális gép újbóli üzembe helyezése](redeploy-to-new-node-linux.md)
* A virtuális gép újbóli létrehozása

További információ: a [végponti kapcsolat hibaelhárítása (RDP/SSH/http stb. hibák)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Részletes hibaelhárítás – áttekintés
Az Azure-beli virtuális gépeken futó alkalmazások hozzáférésének hibakereséséhez négy fő terület áll rendelkezésre.

![az alkalmazás nem indítható el.](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. Az Azure-beli virtuális gépen futó alkalmazás.
   * Az alkalmazás megfelelően fut?
2. Az Azure-beli virtuális gép.
   * Maga a virtuális gép megfelelően fut, és válaszol a kérelmekre?
3. Azure-beli hálózati végpontok.
   * Cloud Service-végpontok a virtuális gépekhez a klasszikus üzemi modellben.
   * Hálózati biztonsági csoportok és a virtuális gépek bejövő NAT-szabályai a Resource Manager-alapú üzemi modellben.
   * Képes a felhasználóktól a virtuális gépre/alkalmazásba irányuló adatforgalom a várt portokon?
4. Az Internet Edge-eszköz.
   * Vannak olyan tűzfalszabályok, amelyek megakadályozzák a forgalom megfelelő áramlását?

Az alkalmazást helyek közötti VPN-vagy ExpressRoute-kapcsolaton keresztül elérő ügyfélszámítógépek esetében az alkalmazás és az Azure-beli virtuális gép okozhatja a problémát okozó fő területeket.

A probléma forrásának és a javításának megállapításához kövesse az alábbi lépéseket.

## <a name="step-1-access-application-from-target-vm"></a>1. lépés: az alkalmazás elérése a cél virtuális gépről
Próbálja meg elérni az alkalmazást a megfelelő ügyfélprogrammal azon a virtuális gépen, amelyen fut. Használja a helyi gazdagép nevét, a helyi IP-címet vagy a visszacsatolási címet (127.0.0.1).

![alkalmazás elindítása közvetlenül a virtuális gépről](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Ha például az alkalmazás egy webkiszolgáló, nyisson meg egy böngészőt a virtuális gépen, és próbáljon meg hozzáférni egy, a virtuális gépen tárolt weboldalhoz.

Ha elérheti az alkalmazást, folytassa a [2. lépéssel](#step2).

Ha nem fér hozzá az alkalmazáshoz, ellenőrizze a következő beállításokat:

* Az alkalmazás a cél virtuális gépen fut.
* Az alkalmazás figyeli a várt TCP-és UDP-portokat.

A Windows-és a Linux-alapú virtuális gépeken a **netstat-a** parancs használatával jelenítse meg az aktív figyelési portokat. Vizsgálja meg a várt portok kimenetét, amelyeken az alkalmazásnak figyelnie kell. Indítsa újra az alkalmazást, vagy konfigurálja úgy, hogy igény szerint használja a várt portokat, és próbálja meg helyileg elérni az alkalmazást.

## <a name="step-2-access-application-from-another-vm-in-the-same-virtual-network"></a><a id="step2"></a>2. lépés: az alkalmazás elérése ugyanazon a virtuális hálózaton lévő másik virtuális gépről
Próbálja meg elérni az alkalmazást egy másik virtuális gépről, de ugyanabban a virtuális hálózatban, a virtuális gép állomásneve vagy az Azure-ban hozzárendelt nyilvános, magánhálózati vagy szolgáltatói IP-címének használatával. A klasszikus üzemi modellel létrehozott virtuális gépek esetében ne használja a felhőalapú szolgáltatás nyilvános IP-címét.

![alkalmazás elindítása egy másik virtuális gépről](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Ha például az alkalmazás egy webkiszolgáló, próbáljon meg hozzáférni egy weboldalhoz egy másik, ugyanazon a virtuális hálózaton lévő virtuális GÉPEN.

Ha elérheti az alkalmazást, folytassa a [3. lépéssel](#step3).

Ha nem fér hozzá az alkalmazáshoz, ellenőrizze a következő beállításokat:

* A célként megadott virtuális gépen a gazdagép tűzfala lehetővé teszi a bejövő kérelmek és a kimenő válaszok forgalmát.
* A cél virtuális gépen futó behatolás-észlelési vagy Hálózatfigyelő szoftver lehetővé teszi a forgalmat.
* Cloud Services végpontok vagy hálózati biztonsági csoportok engedélyezik a forgalmat:
  * [Klasszikus modell – Cloud Services-végpontok kezelése](../../cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Resource Manager-modell – hálózati biztonsági csoportok kezelése](../../virtual-network/manage-network-security-group.md)
* A virtuális gépen a teszt virtuális gép és a virtuális gép (például terheléselosztó vagy tűzfal) közötti útvonalon futó különálló összetevő engedélyezi a forgalmat.

Windows-alapú virtuális gépen a fokozott biztonságú Windows tűzfal használatával állapítsa meg, hogy a tűzfalszabályok kizárják-e az alkalmazás bejövő és kimenő forgalmát.

## <a name="step-3-access-application-from-outside-the-virtual-network"></a><a id="step3"></a>3. lépés: az alkalmazás elérése a virtuális hálózaton kívülről
Próbáljon meg hozzáférni az alkalmazáshoz a virtuális hálózaton kívüli számítógépről, mert az alkalmazást futtató virtuális gép. Használjon másik hálózatot az eredeti ügyfélszámítógépként.

![alkalmazás elindítása a virtuális hálózaton kívüli számítógépről](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Ha például az alkalmazás egy webkiszolgáló, akkor próbáljon meg hozzáférni a weboldalhoz egy olyan böngészőben, amely nem a virtuális hálózaton található.

Ha nem fér hozzá az alkalmazáshoz, ellenőrizze a következő beállításokat:

* A klasszikus üzembe helyezési modellel létrehozott virtuális gépek esetén:
  
  * Ellenőrizze, hogy a virtuális gép végpont-konfigurációja lehetővé teszi-e a bejövő forgalmat, különösen a protokollt (TCP vagy UDP), valamint a nyilvános és a privát portszámot.
  * Ellenőrizze, hogy a végponton lévő hozzáférés-vezérlési listák (ACL-ek) nem akadályozzák-e az internetről érkező bejövő forgalmat.
  * További információ: [végpontok beállítása virtuális géphez](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints).
* A Resource Manager-alapú üzemi modell használatával létrehozott virtuális gépek esetén:
  
  * Ellenőrizze, hogy a virtuális gép bejövő NAT-szabályának konfigurációja lehetővé teszi-e a bejövő forgalmat, különösen a protokollt (TCP vagy UDP), valamint a nyilvános és a privát portszámot.
  * Ellenőrizze, hogy a hálózati biztonsági csoportok engedélyezik-e a bejövő kérelmeket és a kimenő válaszok forgalmát.
  * További információ: [Mi az a hálózati biztonsági csoport?](../../virtual-network/security-overview.md)

Ha a virtuális gép vagy végpont egy elosztott terhelésű készlet tagja:

* Ellenőrizze, hogy a mintavételi protokoll (TCP vagy UDP) és a portszám helyes-e.
* Ha a mintavételi protokoll és a port eltér a terheléselosztási készlet protokolljának és portjától:
  * Győződjön meg arról, hogy az alkalmazás figyeli a mintavételi protokollt (TCP vagy UDP) és a portszámot (használja a **netstat – a** elemet a cél virtuális gépen).
  * Győződjön meg arról, hogy a célként megadott virtuális gépen a gazdagép tűzfala lehetővé teszi a bejövő mintavételi kérelmek és a kimenő mintavételi forgalom megterhelését.

Ha hozzáfér az alkalmazáshoz, győződjön meg arról, hogy az internetes peremhálózati eszköz lehetővé teszi a következőket:

* A kimenő alkalmazás az ügyfélszámítógépről az Azure-beli virtuális gépre irányuló adatforgalmat kéri le.
* A bejövő alkalmazás válaszának forgalma az Azure-beli virtuális gépről.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>4. lépés ha nem fér hozzá az alkalmazáshoz, az IP-ellenőrzés használatával ellenőrizze a beállításokat. 

További információ: az [Azure Network monitoring áttekintése](../../network-watcher/network-watcher-monitoring-overview.md). 

## <a name="additional-resources"></a>További források
[Windows-alapú Azure-beli virtuális gépekkel létesített Távoli asztal-kapcsolatok hibáinak megoldása](troubleshoot-rdp-connection.md)

[A Secure Shell-(SSH-) kapcsolatok hibakeresése Linux-alapú Azure-beli virtuális gépeken](troubleshoot-ssh-connection.md)
