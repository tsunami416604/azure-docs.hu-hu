---
title: Részletes SSH hibaelhárítás egy Azure virtuális géphez | Microsoft dokumentumok
description: Részletesebb SSH hibaelhárítási lépések az Azure virtuális gépekhez való csatlakozással kapcsolatos problémákhoz
keywords: ssh kapcsolat elutasítva,ssh hiba,azure ssh,SSH kapcsolat nem sikerült
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: ee6d437915f6c87ce9ef5f9c711d90793a96048c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920127"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Részletes SSH-hibaelhárítási lépések Linux rendszerű virtuális gépekhez való csatlakozáskor fellépő problémákhoz az Azure-ban
Számos lehetséges oka lehet, hogy az SSH-ügyfél nem tudja elérni az SSH szolgáltatást a virtuális gépen. Ha az [SSH általánosabb hibaelhárítási lépéseit](troubleshoot-ssh-connection.md)követte, a csatlakozási problémát tovább kell hárítania. Ez a cikk részletes hibaelhárítási lépéseken vezet át annak meghatározásához, hogy hol hibásodik meg az SSH-kapcsolat, és hogyan oldható meg.

## <a name="take-preliminary-steps"></a>Az előzetes lépések megtétele
Az alábbi ábra az érintett összetevőket mutatja be.

![Az SSH szolgáltatás összetevőit bemutató diagram](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

A következő lépések segítségével elkülönítheti a hiba forrását, és kitalálja a megoldásokat vagy a kerülő megoldásokat.

1. Ellenőrizze a virtuális gép állapotát a portálon.
   Az [Azure Portalon](https://portal.azure.com)válassza **a Virtuális gépek** > *virtuális gép nevét.*

   A virtuális gép állapotablakának meg kell jelennie a **Futás**. Görgessen le a számítási, tárolási és hálózati erőforrások legutóbbi tevékenységének megjelenítéséhez.

2. Válassza a **Beállítások** lehetőséget a végpontok, IP-címek, hálózati biztonsági csoportok és egyéb beállítások vizsgálatához.

   A virtuális gépnek rendelkeznie kell egy végpontot az SSH-forgalomhoz, amely **et az Endpoints** vagy a **[Network security group](../../virtual-network/security-overview.md)** nézetben tekintheti meg. Az Erőforrás-kezelő vel létrehozott virtuális gépek végpontjai egy hálózati biztonsági csoportban tárolódnak. Ellenőrizze, hogy a szabályok a hálózati biztonsági csoportra lettek-e alkalmazva, és az alhálózat hivatkozik-e rájuk.

A hálózati kapcsolat ellenőrzéséhez ellenőrizze a konfigurált végpontokat, és ellenőrizze, hogy tud-e csatlakozni a virtuális géphez egy másik protokollon, például HTTP-n vagy más szolgáltatáson keresztül.

A lépések után próbálkozzon újra az SSH-kapcsolattal.

## <a name="find-the-source-of-the-issue"></a>A probléma forrásának megkeresése
Előfordulhat, hogy a számítógépen lévő SSH-ügyfél problémák vagy helytelen konfigurációk miatt nem tud csatlakozni az SSH szolgáltatáshoz az Azure virtuális gépen:

* [SSH-ügyfélszámítógép](#source-1-ssh-client-computer)
* [Szervezeti peremhálózati eszköz](#source-2-organization-edge-device)
* [Felhőszolgáltatás végpont- és hozzáférés-vezérlési listája (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Network security groups (Hálózati biztonsági csoportok)](#source-4-network-security-groups)
* [Linux alapú Azure virtuális gép](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Forrás 1: SSH ügyfélszámítógép
A számítógép hiba forrásának kiküszöbölése érdekében ellenőrizze, hogy képes-e SSH-kapcsolatokat létesíteni egy másik helyszíni, Linux-alapú számítógéppel.

![Az SSH ügyfélszámítógép összetevőit kiemelő diagram](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Ha a kapcsolat nem sikerül, ellenőrizze, hogy vannak-e a következő problémák a számítógépen:

* A bejövő vagy kimenő SSH-forgalmat blokkoló helyi tűzfalbeállítás (TCP 22)
* Helyileg telepített ügyfélproxy szoftver, amely megakadályozza az SSH-kapcsolatokat
* Helyileg telepített hálózatfigyelő szoftver, amely megakadályozza az SSH-kapcsolatokat
* Más típusú biztonsági szoftverek, amelyek figyelik a forgalmat, vagy engedélyezik/lehetővé teszik bizonyos típusú forgalmat

Ha a feltételek valamelyike fennáll, ideiglenesen tiltsa le a szoftvert, és próbáljon meg SSH-kapcsolatot létesíteni egy helyszíni számítógéppel, és derítse ki, hogy miért van letiltva a kapcsolat a számítógépen. Ezután a hálózati rendszergazdával együttműködve javítsa ki a szoftverbeállításokat az SSH-kapcsolatok engedélyezéséhez.

Ha tanúsítvány-hitelesítést használ, ellenőrizze, hogy rendelkezik-e ezekkel az engedélyekkel a kezdőkönyvtár .ssh mappájához:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*.pub
* Chmod 600 ~/.ssh/id_rsa (vagy bármely más fájlokat, amelyek a személyes kulcsok at bennük)
* Chmod 644 ~/.ssh/known_hosts (azOkat a állomásokat tartalmazza, amelyekhez SSH-n keresztül csatlakozott)

## <a name="source-2-organization-edge-device"></a>Forrás 2: Szervezeti peremhálózati eszköz
A hiba forrásaként a szervezet peremhálózati eszközének eltávolításához ellenőrizze, hogy az internethez közvetlenül csatlakozó számítógép képes-e SSH-kapcsolatokat létesíteni az Azure virtuális géphez. Ha a virtuális gép egy hely-hely VPN vagy egy Azure ExpressRoute-kapcsolaton keresztül érhető el, ugorjon a [4-es forrás: Hálózati biztonsági csoportok](#nsg)elemre.

![A szervezeti peremhálózati eszközt kiemel rajz](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Ha nem rendelkezik olyan számítógéppel, amely közvetlenül csatlakozik az internethez, hozzon létre egy új Azure-virtuális gép a saját erőforráscsoport ban vagy felhőalapú szolgáltatás, és használja az új virtuális gép. További információ: [Create a virtual machine running a Linux in Azure](../linux/quick-create-cli.md). Törölje az erőforráscsoportot vagy a virtuális gép és a felhőszolgáltatás, ha elvégezte a tesztelést.

Ha ssh-kapcsolatot tud létrehozni egy olyan számítógéppel, amely közvetlenül csatlakozik az internethez, ellenőrizze a szervezet peremhálózati eszközét:

* Belső tűzfal, amely blokkolja az SSH-forgalmat az internettel
* Az SSH-kapcsolatokat megakadályozó proxykiszolgáló
* Behatolásészlelés vagy a peremhálózati eszközökön futó hálózatfigyelő szoftver, amely megakadályozza az SSH-kapcsolatokat

A hálózati rendszergazdával együttműködve javítsa ki a szervezeti peremhálózati eszközök beállításait az SSH-forgalom engedélyezéséhez az interneten.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>3. forrás: A felhőszolgáltatás végpontja és az ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> Ez a forrás csak a klasszikus üzembe helyezési modell használatával létrehozott virtuális gépekre vonatkozik. Az Erőforrás-kezelővel létrehozott virtuális gépek esetében ugorjon a [4-es forrás: Hálózati biztonsági csoportok](#nsg).

A felhőszolgáltatás végpontjának és az ACL-nek a hiba forrásaként való eltávolításához ellenőrizze, hogy egy másik Azure-virtuális gép ugyanabban a virtuális hálózatban csatlakozhat-e az SSH használatával.

![A felhőszolgáltatás végpontját és az ACL-t kiemelő diagram](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Ha nem rendelkezik egy másik virtuális gép ugyanabban a virtuális hálózatban, könnyen létrehozhat egyet. További információ: [Linuxos virtuális gép létrehozása az Azure-ban a CLI használatával.](../linux/quick-create-cli.md) Törölje az extra virtuális gép, ha végzett a tesztelés.

Ha sSH-kapcsolatot hozhat létre egy virtuális géppel ugyanabban a virtuális hálózatban, ellenőrizze a következő területeket:

* **A célvirtuális gép SSH-forgalmának végpontkonfigurációja.** A végpont privát TCP-portjának meg kell egyeznie a TCP-porttal, amelyen a virtuális gép SSH szolgáltatása figyel. (Az alapértelmezett port 22). Ellenőrizze az SSH TCP-port számát az Azure Portalon a **Virtuális gépek** > virtuális*gép névbeállítási* > **Settings** > **végpontjai**kiválasztásával.
* **Az ACL az SSH-forgalom végpontja a cél virtuális gépen.** Az ACL lehetővé teszi az engedélyezett vagy elutasított bejövő forgalom megadását az internetről a forrás IP-címe alapján. A helytelenül konfigurált ACL-k megakadályozhatják a végpontra irányuló bejövő SSH-forgalmat. Ellenőrizze az ACL-eket, és győződjön meg arról, hogy a proxy vagy más peremhálózati kiszolgáló nyilvános IP-címeiből érkező bejövő forgalom engedélyezett. További információt a [Hálózati hozzáférés-vezérlési listák (ACL-k) – további témakörökben talál.](../../virtual-network/virtual-networks-acl.md)

A végpont kiküszöbölése a probléma forrásaként távolítsa el az aktuális végpontot, hozzon létre egy másik végpontot, és adja meg az SSH-nevet (22-es TCP-port a nyilvános és a privát port számához). További információt a [Végpontok beállítása az Azure-ban virtuális gépen című témakörben talál.](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>4. forrás: Hálózati biztonsági csoportok
A hálózati biztonsági csoportok lehetővé teszik, hogy részletesebben szabályozhassa az engedélyezett bejövő és kimenő forgalmat. Olyan szabályokat hozhat létre, amelyek alhálózatokra és felhőszolgáltatásokra terjednek ki egy Azure virtuális hálózatban. Ellenőrizze a hálózati biztonsági csoport szabályait, és győződjön meg arról, hogy az SSH-forgalom engedélyezett az internetről és az internetről.
További információt a [Hálózati biztonsági csoportok – betekintés.](../../virtual-network/security-overview.md)

Az IP-ellenőrzés segítségével is ellenőrizheti az NSG-konfigurációt. További információt az [Azure hálózati figyelésének áttekintése című témakörben talál.](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 

## <a name="source-5-linux-based-azure-virtual-machine"></a>5. forrás: Linux-alapú Azure virtuális gép
A lehetséges problémák utolsó forrása maga az Azure virtuális gép.

![A Linux-alapú Azure virtuális gépet kiemel rajz](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Ha még nem tette meg, kövesse az utasításokat a [jelszó Linux-alapú virtuális gépek alaphelyzetbe állításához.](../linux/reset-password.md)

Próbáljon meg újra csatlakozni a számítógépről. Ha még mindig sikertelen, a következő néhány lehetséges probléma merül fel:

* Az SSH szolgáltatás nem fut a cél virtuális gépen.
* Az SSH szolgáltatás nem figyel a 22-es TCP-porton. A teszteléshez telepítsen egy telnet-ügyfelet a helyi számítógépre, és futtassa a "telnet *cloudServiceName*.cloudapp.net 22" futtassa. Ez a lépés határozza meg, hogy a virtuális gép engedélyezi-e a bejövő és kimenő kommunikációt az SSH-végponthoz.
* A helyi tűzfal a cél virtuális gépen olyan szabályokkal rendelkezik, amelyek megakadályozzák a bejövő vagy kimenő SSH-forgalmat.
* Az Azure virtuális gépen futó behatolásészlelés vagy hálózatfigyelő szoftver megakadályozza az SSH-kapcsolatokat.

## <a name="additional-resources"></a>További források
Az alkalmazások hozzáférésének hibaelhárításáról az [Azure virtuális gépen futó alkalmazáshoz való hozzáférés hibaelhárítása című](../linux/troubleshoot-app-connection.md) témakörben talál további információt.
