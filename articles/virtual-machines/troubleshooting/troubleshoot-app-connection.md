---
title: Virtuálisgép-alkalmazások hozzáférése az Azure-ban | Microsoft dokumentumok
description: Ezekkel a részletes hibaelhárítási lépésekkel elkülönítheti az Azure-ban virtuális gépeken futó alkalmazásokhoz való csatlakozással kapcsolatos problémákat.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: nem lehet elindítani az alkalmazást, a program nem nyílik meg, a figyelőport blokkolva van, nem tudja elindítani a programot, figyelni a portot blokkolva
ms.assetid: b9ff7cd0-0c5d-4c3c-a6be-3ac47abf31ba
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: caf73ffbc18a603ace22acfbd0da490048da698a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058125"
---
# <a name="troubleshoot-application-connectivity-issues-on-virtual-machines-in-azure"></a>Azure-beli virtuális gépeken futó alkalmazások csatlakozási hibáinak elhárítása

Különböző okok miatt, ha nem tud elindítani, vagy csatlakozni egy Azure virtuális gépen (VM) futó alkalmazáshoz. Ennek okai közé tartozik, hogy az alkalmazás nem fut, vagy figyel a várt portokon, a figyelőport blokkolva van, vagy a hálózati szabályok nem megfelelően továbbítják a forgalmat az alkalmazásnak. Ez a cikk a probléma megkeresésének és javításának módszeres megközelítését ismerteti.

Ha problémái vannak a virtuális géphez az RDP vagy az SSH használatával való csatlakozással, először tekintse meg az alábbi cikkek egyikét:

* [Windows-alapú Azure virtuális gép távoli asztali kapcsolatainak – problémamegoldás](troubleshoot-rdp-connection.md)
* [A Biztonságos rendszerhéj (SSH) kapcsolatainak hibaelhárítása linuxos Azure-alapú virtuális géppel.](troubleshoot-ssh-connection.md)

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel [az MSDN Azure-ban és a Veremtúlcsordulás fórumain.](https://azure.microsoft.com/support/forums/) Azt is megteheti, hogy egy Azure-támogatási incidenst is benyújthat. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**

## <a name="quick-start-troubleshooting-steps"></a>Gyorsútmutató – hibaelhárítási lépések
Ha problémái vannak egy alkalmazáshoz való csatlakozással, próbálkozzon az alábbi általános hibaelhárítási lépésekkel. Minden lépés után próbáljon meg újra csatlakozni az alkalmazáshoz:

* A virtuális gép újraindítása
* A végpont/ tűzfalszabályok / hálózati biztonsági csoport (NSG) szabályainak újbóli létrehozása
  * [Erőforrás-kezelő modellje – Hálózati biztonsági csoportok kezelése](../../virtual-network/manage-network-security-group.md)
  * [Klasszikus modell – A felhőszolgáltatások végpontjainak kezelése](../../cloud-services/cloud-services-enable-communication-role-instances.md)
* Csatlakozás más helyről, például egy másik Azure virtuális hálózatról
* A virtuális gép ismételt üzembe helyezése
  * [Windows virtuális gép újratelepítése](redeploy-to-new-node-windows.md)
  * [Linux os virtuális gép újratelepítése](redeploy-to-new-node-linux.md)
* A virtuális gép újbóli létrehozása

További információt a [Végponti kapcsolat hibaelhárítása (RDP/SSH/HTTP stb.) című témakörben](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows)talál.

## <a name="detailed-troubleshooting-overview"></a>Részletes hibaelhárítás – áttekintés
Négy fő terület van egy Azure virtuális gépen futó alkalmazás hozzáférésének hibaelhárításához.

![a hibaelhárítás nem indítható el az alkalmazás](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. Az Azure virtuális gépen futó alkalmazás.
   * Maga az alkalmazás megfelelően fut?
2. Az Azure virtuális gép.
   * Maga a virtuális gép megfelelően fut, és válaszol a kérésekre?
3. Az Azure hálózati végpontok.
   * Felhőszolgáltatás-végpontok a klasszikus üzembe helyezési modell ben a virtuális gépek.
   * Hálózati biztonsági csoportok és bejövő NAT-szabályok az Erőforrás-kezelő telepítési modelljében lévő virtuális gépekhez.
   * Forgalom a felhasználók tól a virtuális gép/alkalmazás a várt portokon?
4. Az internetpernek eszköze.
   * A tűzfalszabályok megakadályozzák a forgalom megfelelő áramlását?

Az ügyfélszámítógépek, amelyek az alkalmazást egy hely-hely VPN vagy ExpressRoute-kapcsolaton keresztül, a fő területek, amelyek problémákat okozhat az alkalmazás és az Azure virtuális gép.

A probléma forrásának és javításának megállapításához kövesse az alábbi lépéseket.

## <a name="step-1-access-application-from-target-vm"></a>1. lépés: Alkalmazás elérése a cél virtuális gépről
Próbálja meg elérni az alkalmazást a megfelelő ügyfélprogrammal attól a virtuális géptől, amelyen fut. Használja a helyi állomásnevet, a helyi IP-címet vagy a visszacsatolási címet (127.0.0.1).

![az alkalmazás indítása közvetlenül a virtuális gépről](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Ha például az alkalmazás egy webkiszolgáló, nyisson meg egy böngészőt a virtuális gépen, és próbáljon meg hozzáférni egy, a virtuális gépen tárolt weblaphoz.

Ha hozzá tud férni az alkalmazáshoz, folytassa a [2.](#step2)

Ha nem tud hozzáférni az alkalmazáshoz, ellenőrizze a következő beállításokat:

* Az alkalmazás fut a cél virtuális gépen.
* Az alkalmazás figyeli a várt TCP- és UDP-portokat.

Windows és Linux alapú virtuális gépeken is használja a **netstat -a** parancsot az aktív figyelőportok megjelenítéséhez. Vizsgálja meg a kimenetet a várt portok, amelyeken az alkalmazás figyelni. Indítsa újra az alkalmazást, vagy konfigurálja úgy, hogy szükség szerint a várt portokat használja, és próbálja meg újra helyileg elérni az alkalmazást.

## <a name="step-2-access-application-from-another-vm-in-the-same-virtual-network"></a><a id="step2"></a>2. lépés: Alkalmazás elérése egy másik virtuális gépről ugyanabban a virtuális hálózatban
Próbálja meg elérni az alkalmazást egy másik virtuális gépről, de ugyanabban a virtuális hálózatban, a virtuális gép gazdagép nevével vagy az Azure által hozzárendelt nyilvános, privát vagy szolgáltató IP-cím használatával. A klasszikus üzembe helyezési modell használatával létrehozott virtuális gépek, ne használja a nyilvános IP-címet a felhőszolgáltatás.

![alkalmazás indítása egy másik virtuális gépről](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Ha például az alkalmazás egy webkiszolgáló, próbáljon meg hozzáférni egy weblapot egy másik virtuális gép egy másik virtuális gép egy másik virtuális gépen ugyanabban a virtuális hálózatban.

Ha hozzá tud férni az alkalmazáshoz, folytassa a [3.](#step3)

Ha nem tud hozzáférni az alkalmazáshoz, ellenőrizze a következő beállításokat:

* A célvirtuális gép gazdatűzfala engedélyezi a bejövő kérelem és a kimenő válaszforgalmat.
* Behatolásészlelés vagy a hálózati figyelő szoftver fut a cél virtuális gép lehetővé teszi a forgalmat.
* A Felhőszolgáltatások végpontjai vagy a hálózati biztonsági csoportok engedélyezik a forgalmat:
  * [Klasszikus modell – A felhőszolgáltatások végpontjainak kezelése](../../cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Erőforrás-kezelő modellje – Hálózati biztonsági csoportok kezelése](../../virtual-network/manage-network-security-group.md)
* A virtuális gép ben futó külön összetevő a teszt virtuális gép és a virtuális gép közötti útvonalon, például egy terheléselosztó vagy tűzfal, amely lehetővé teszi a forgalmat.

Windows alapú virtuális gépen a Fokozott biztonságú Windows tűzfal segítségével állapítsa meg, hogy a tűzfalszabályok kizárják-e az alkalmazás bejövő és kimenő forgalmát.

## <a name="step-3-access-application-from-outside-the-virtual-network"></a><a id="step3"></a>3. lépés: Az alkalmazás elérése a virtuális hálózaton kívülről
Próbálja meg elérni az alkalmazást a virtuális hálózaton kívüli számítógépről, mint a virtuális gép, amelyen az alkalmazás fut. Eredeti ügyfélszámítógépként használjon másik hálózatot.

![alkalmazás indítása a virtuális hálózaton kívüli számítógépről](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Ha például az alkalmazás webkiszolgáló, próbálja meg elérni a weblapot egy olyan böngészőből, amely nem a virtuális hálózatban található.

Ha nem tud hozzáférni az alkalmazáshoz, ellenőrizze a következő beállításokat:

* A klasszikus üzembe helyezési modell használatával létrehozott virtuális gépek esetén:
  
  * Ellenőrizze, hogy a virtuális gép végpontkonfigurációja engedélyezi-e a bejövő forgalmat, különösen a protokollt (TCP vagy UDP), valamint a nyilvános és privát portszámokat.
  * Ellenőrizze, hogy a végponthozzáférés-vezérlési listák (Hozzáférés-vezérlési listák) nem akadályozzák-e az internetről érkező bejövő forgalmat.
  * További információt a [Végpontok beállítása virtuális gépre című témakörben talál.](../windows/classic/setup-endpoints.md)
* Az Erőforrás-kezelő telepítési modelljével létrehozott virtuális gépek esetén:
  
  * Ellenőrizze, hogy a virtuális gép bejövő NAT-szabálykonfigurációja engedélyezi-e a bejövő forgalmat, különösen a protokollt (TCP vagy UDP), valamint a nyilvános és privát portszámokat.
  * Ellenőrizze, hogy a hálózati biztonsági csoportok engedélyezik-e a bejövő és a kimenő válaszforgalmat.
  * További információ: [Mi a hálózati biztonsági csoport?](../../virtual-network/security-overview.md)

Ha a virtuális gép vagy a végpont egy kiegyenlítő terhelésű készlet tagja:

* Ellenőrizze, hogy a mintavételi protokoll (TCP vagy UDP) és a portszáma helyes-e.
* Ha a mintavételi protokoll és a port eltér a terheléselosztásos beállított protokolltól és porttól:
  * Ellenőrizze, hogy az alkalmazás figyeli-e a mintavételi protokollt (TCP vagy UDP) és a portszámát (használja a **netstat –a** a célvirtuális gépen).
  * Ellenőrizze, hogy a gazdagép tűzfala a cél virtuális gépen engedélyezi-e a bejövő mintavételi kérelmet és a kimenő mintavételi válaszforgalmat.

Ha hozzá tud férni az alkalmazáshoz, győződjön meg arról, hogy az internetperemeszköz engedélyezi:

* A kimenő alkalmazás az ügyfélszámítógépről az Azure virtuális gépre irányuló forgalmat kér.
* A bejövő alkalmazásválasz-forgalmat az Azure virtuális gépről.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>4. lépés Ha nem tud hozzáférni az alkalmazáshoz, az IP-ellenőrzés segítségével ellenőrizze a beállításokat. 

További információt az [Azure hálózati figyelésének áttekintése című témakörben talál.](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 

## <a name="additional-resources"></a>További források
[Windows-alapú Azure virtuális gép távoli asztali kapcsolatainak – problémamegoldás](troubleshoot-rdp-connection.md)

[Secure Shell (SSH) kapcsolatok linuxos Azure virtuális géppel való – problémamegoldás](troubleshoot-ssh-connection.md)


