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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38764722"
---
Nincsenek különböző okok miatt nem kezdődhet és nem egy Azure virtuális gépen (VM) futó alkalmazásokhoz való csatlakozáshoz. Okok közé tartozik az alkalmazás nem fut, vagy a várt portokat figyeli, a letiltott figyelő portját és a hálózat szabályok nem megfelelően megadásának forgalmat az alkalmazáshoz. Ez a cikk ismerteti a módszeres megközelítés keresse meg és hárítsa el a problémát.

Ha a virtuális Géphez, RDP vagy SSH használatával való csatlakozással kapcsolatos problémákat tapasztal, először tekintse meg az alábbi cikkekben:

* [A Windows-alapú Azure virtuális gép távoli asztali kapcsolatok hibaelhárítása](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)
* [Secure Shell (SSH) kapcsolatok egy Linux-alapú Azure virtuális gépek hibaelhárítása](../articles/virtual-machines/linux/troubleshoot-ssh-connection.md).

> [!NOTE]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../articles/resource-manager-deployment-model.md). A jelen cikk mindkét modell használatát bemutatja, de a Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a [az MSDN Azure és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik megoldásként is fájl is egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az első**.

## <a name="quick-start-troubleshooting-steps"></a>Gyors üzembe helyezési hibaelhárítási lépéseket
Ha problémába ütközik egy alkalmazást, próbálkozzon az alábbi általános hibaelhárítási lépéseket. Minden lépése után próbáljon újból az alkalmazást:

* A virtuális gép újraindítása
* Hozza létre újra a végpontot / tűzfalszabályok / hálózati biztonsági csoport (NSG) szabályai
  * [Resource Manager-modell – hálózati biztonsági csoportok kezelése](../articles/virtual-network/manage-network-security-group.md)
  * [A Klasszikus modell - végpontok kezelése a Cloud Services](../articles/cloud-services/cloud-services-enable-communication-role-instances.md)
* Csatlakozás a más helyre, például egy másik Azure virtuális hálózatot
* A virtuális gép ismételt üzembe helyezése
  * [Windows virtuális gép újratelepítése](../articles/virtual-machines/windows/redeploy-to-new-node.md)
  * [Linux rendszerű virtuális gép újratelepítése](../articles/virtual-machines/linux/redeploy-to-new-node.md)
* Hozza létre újra a virtuális gép

További információkért lásd: [Végpontkapcsolatának hibaelhárítási (RDP/SSH/HTTP, a hibák stb.)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Részletes hibaelhárítási áttekintése
Nincsenek a hozzáférés-beli virtuális gépen futó alkalmazás hibaelhárítása négy fő területtel.

![hibaelhárítás aplikaci nelze spustit.](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. Az az Azure virtuális gépen futó alkalmazás.
   * Maga az alkalmazás megfelelően működnek-e?
2. Az Azure virtuális gépen.
   * Az a virtuális gépre, megfelelően működnek-e, és válaszol a kérelmekre?
3. Az Azure network-végpontok.
   * A felhő-Szolgáltatásvégpontok a virtuális gépek a klasszikus üzemi modellben.
   * Hálózati biztonsági csoportok és bejövő NAT-szabályok a Resource Manager üzemi modell virtuális gépek számára.
   * A felhasználók a folyamat a várt portokat a virtuális gép/alkalmazáshoz is forgalom?
4. Az internetes edge-eszköz.
   * Tűzfalszabályok helyen megakadályozza, hogy az adatforgalom megfelelően áramló?

Az alkalmazás egy helyek közötti VPN vagy ExpressRoute-kapcsolaton keresztül elérő ügyfélszámítógépeken a területekre, amelyek problémákat okozhat az alkalmazás és az Azure virtuális gépen.

A problémáról és annak javítási okának meghatározása, kövesse az alábbi lépéseket.

## <a name="step-1-access-application-from-target-vm"></a>1. lépés: Alkalmazás elérését a cél virtuális Gépen
Próbálja meg elérni az alkalmazást a megfelelő ügyféloldali-program, amelyen fut a virtuális gépről. Használja a helyi gazdagép nevét, a helyi IP-címet, vagy a visszacsatolási cím (127.0.0.1).

![Indítsa el az alkalmazást közvetlenül a virtuális gépről](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Például ha az alkalmazás egy webkiszolgáló, nyisson meg egy böngészőt a virtuális gépen, és próbálja meg elérni a virtuális gépen üzemeltetett weblapon.

Ha az alkalmazás férhet hozzá, lépjen a [2. lépés](#step2).

Ha nem tudja elérni az alkalmazást, ellenőrizze a következő beállításokat:

* Az alkalmazás fut, a cél virtuális gépen.
* Az alkalmazás a várt TCP és UDP-portokon figyel.

A Windows és Linux-alapú virtuális gépek használata a **netstat - a** parancsot az aktív figyelőportjait megjelenítéséhez. Vizsgálja meg a várt portokat, amelyeken az alkalmazás figyelésére kell kimenetét. Indítsa újra az alkalmazást, vagy konfigurálja úgy, hogy a várt portokat használja, szükség szerint, és próbálja meg újból elérni az alkalmazás helyi.

## <a id="step2"></a>2. lépés: Alkalmazás elérését egy másik virtuális Géphez ugyanazon a virtuális hálózaton
Próbálja meg elérni az alkalmazást, egy másik virtuális gépről, de az azonos virtuális hálózatba, a virtuális gép állomásnevét vagy az Azure által hozzárendelt nyilvános, magán vagy a szolgáltató IP-címének használatával. A klasszikus üzemi modellel létrehozott virtuális gépek ne használja a felhőalapú szolgáltatás nyilvános IP-címét.

![Indítsa el az alkalmazást egy másik virtuális gépről](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Például ha az alkalmazás egy webkiszolgáló, próbálja meg elérni egy weblap egy böngészőből, egy másik virtuális Gépet az azonos virtuális hálózatba.

Ha az alkalmazás férhet hozzá, lépjen a [3. lépés](#step3).

Ha nem tudja elérni az alkalmazást, ellenőrizze a következő beállításokat:

* A cél virtuális gép gazdagép tűzfala engedélyezi-e a bejövő kérelem és válasz kimenő forgalmat.
* Behatolásészlelés vagy a cél virtuális Gépen futó szoftver hálózatfigyelési engedélyezi-e a forgalmat.
* Cloud Services végpontjainak vagy a hálózati biztonsági csoportok engedélyezi a forgalmat:
  * [A Klasszikus modell - végpontok kezelése a Cloud Services](../articles/cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Resource Manager-modell – hálózati biztonsági csoportok kezelése](../articles/virtual-network/manage-network-security-group.md)
* Egy külön összetevő fut a virtuális Gépen az elérési út között a teszt virtuális gép és a virtuális gép, például a terheléselosztó vagy a tűzfal engedélyezi-e a forgalmat.

Windows-alapú virtuális gépen fokozott biztonságú Windows tűzfal segítségével határozza meg, hogy a tűzfalszabályok kizárása az alkalmazás bejövő és kimenő forgalmat.

## <a id="step3"></a>3. lépés: A virtuális hálózaton kívül az alkalmazás eléréséhez
Próbálja meg elérni az alkalmazást a virtuális hálózaton kívüli számítógépről, mint a virtuális gép, amelyen fut az alkalmazás. Egy másik hálózatot használja az eredeti ügyfélszámítógépen.

![Indítsa el az alkalmazást a virtuális hálózaton kívüli számítógépről](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Például ha az alkalmazás egy webkiszolgáló, próbálja meg elérni a weblap a számítógépen, amely nem a virtuális hálózatban futó közvetlenül a böngészőből.

Ha nem tudja elérni az alkalmazást, ellenőrizze a következő beállításokat:

* A virtuális gépek a klasszikus üzemi modellel létrehozott:
  
  * Győződjön meg arról, hogy a végpont-konfiguráció a virtuális gép számára engedélyezi a bejövő forgalmat, különösen a protokollt (TCP vagy UDP) és a nyilvános és privát portszámokat.
  * Ellenőrizze, hogy a végponti hozzáférés-vezérlési listák (ACL) nem akadályozzák az internetről bejövő forgalmat.
  * További információkért lásd: [hogyan állítsa be végpontok egy virtuális géphez](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* A létrehozott virtuális gépek a Resource Manager üzemi modell használatával:
  
  * Győződjön meg arról, hogy a virtuális gép bejövő NAT-szabály konfigurációjának engedélyezi a bejövő forgalmat, különösen a protokollt (TCP vagy UDP) és a nyilvános és privát portszámokat.
  * Győződjön meg arról, hogy a hálózati biztonsági csoportok engedélyezi a bejövő kérelem és válasz kimenő forgalmat.
  * További információkért lásd: [Mi az a hálózati biztonsági csoport?](../articles/virtual-network/security-overview.md)

Ha a virtuális gép vagy a végpont egy elosztott terhelésű készlet tagjai:

* Győződjön meg arról, hogy a mintavétel protocol (TCP vagy UDP) és a portszám helyességéről.
* Ha a mintavételi protokoll és port eltér attól az elosztott terhelésű készlet protokoll és port:
  * Győződjön meg arról, hogy az alkalmazás figyeli-e a mintavételi protocol (TCP vagy UDP) és a portszám (használata **netstat – a** a cél virtuális Gépen).
  * Győződjön meg arról, hogy a gazdagép tűzfal a cél virtuális Gépen engedélyezi a bejövő állapotminta iránti kérelem és a kimenő mintavételi válaszok forgalmat.

Ha elérhetik az alkalmazást, győződjön meg arról, hogy engedélyezi-e az internetes edge-eszköz:

* A kimenő kérelem alkalmazásforgalomba az ügyfélszámítógépről, az Azure virtuális gépen.
* A bejövő kérelem érkező az Azure virtuális gépen.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Lépés 4 nem tud hozzáférni az alkalmazáshoz, használatával IP ellenőrzéséhez ellenőrizze a beállításokat. 

További információkért lásd: [Figyelés áttekintése az Azure network](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="additional-resources"></a>További források
[A Windows-alapú Azure virtuális gép távoli asztali kapcsolatok hibaelhárítása](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)

[Egy Linux-alapú Azure virtuális gép Secure Shell (SSH) kapcsolatok hibaelhárítása](../articles/virtual-machines/linux/troubleshoot-ssh-connection.md)

