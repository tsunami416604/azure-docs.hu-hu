---
title: "Részletes SSH hibaelhárítása az Azure virtuális gép |} Microsoft Docs"
description: "Részletesebb hibaelhárítási lépések az Azure virtuális géphez való kapcsolódás problémák SSH"
keywords: "ssh kapcsolat visszautasította, ssh hiba és az azure-ssh, SSH-kapcsolat sikertelen"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: support-article
ms.date: 07/06/2017
ms.author: iainfou
ms.openlocfilehash: 9ccdb3fbca21264065eeb1c4e46314c62af4c2e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Részletes SSH hibaelhárításának lépései az Azure Linux virtuális gép csatlakozni
Nincsenek számos oka lehet, hogy az SSH-ügyfél nem feltétlenül tudni elérni az SSH szolgáltatás a virtuális Gépen. Ha már elvégezte a több keresztül [hibaelhárítási lépések általános SSH](troubleshoot-ssh-connection.md), további hibáinak kapcsolódási problémáját. Ez a cikk végigvezeti részletes hibaelhárítási lépések elvégzésével ellenőrizheti, ha az SSH-kapcsolat nem működik, és a megoldásának módjával.

## <a name="take-preliminary-steps"></a>Előzetes lépések
Az alábbi ábrán látható, az összetevők, amelyek szerepet játszanak.

![SSH-szolgáltatás összetevője bemutató diagram, amelyek](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

A következő lépések segítenek a különítse el a hiba forrását, és hogy megoldások, illetve a lehetséges megoldások.

1. A virtuális Gépet a portálon állapotának ellenőrzéséhez.
   Az a [Azure-portálon](https://portal.azure.com), jelölje be **virtuális gépek** > *nevet a virtuális gép*.

   A virtuális gép a állapot panelje megjeleníti **futtató**. Görgessen le a számítási, tárolási és hálózati erőforrásokat megjelenítése a legutóbbi tevékenységek.

2. Válassza ki **beállítások** vizsgálata végpontok, IP-címek, hálózati biztonsági csoportok és egyéb beállításokat.

   A virtuális gép rendelkeznie kell egy SSH-forgalomhoz, megtekintheti a definiált végpontot **végpontok** vagy  **[hálózati biztonsági csoport](../../virtual-network/virtual-networks-nsg.md)**. Az erőforrás-kezelő használatával létrehozott virtuális gépeken végpontok hálózati biztonsági csoport vannak tárolva. Azt is ellenőrizze, hogy a szabályok vannak alkalmazva a hálózati biztonsági csoportot, és, hogy azok által hivatkozott alhálózaton.

Ellenőrizze a hálózati kapcsolatot, ellenőrizze a beállított végpontjaikra, és tekintse meg, ha a virtuális gép egy másik protokoll, például http- vagy egy másik szolgáltatás keresztül érhető el.

Ezeket a lépéseket alatti próbálja meg újra az SSH-kapcsolat.

## <a name="find-the-source-of-the-issue"></a>Keresse meg a probléma forrása
Az SSH-ügyfél a számítógépen előfordulhat, hogy nem sikerül az SSH szolgáltatás az Azure virtuális gépen hibái vagy a következő területeken konfigurációs hibák miatt:

* [SSH-ügyfélszámítógép](#source-1-ssh-client-computer)
* [Szervezet a peremhálózati eszköz](#source-2-organization-edge-device)
* [A felhőalapú szolgáltatás végpontjának és hozzáférés-vezérlési lista (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Hálózati biztonsági csoportok](#source-4-network-security-groups)
* [Linux-alapú Azure virtuális gép](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>1. forrás: SSH ügyfélszámítógép
Elkerülése érdekében a számítógépet a probléma forrása, győződjön meg arról, hogy segítségükkel SSH-kapcsolatok egy másik helyszíni, Linux-alapú számítógép.

![Diagram, amely kiemeli az SSH-ügyfél számítógép összetevői](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Ha nem sikerül, ellenőrizze a számítógépen a következő problémákat:

* A beállítás, amely helyi tűzfal blokkolja a bejövő vagy kimenő SSH forgalom (22-es TCP)
* Helyileg telepített proxy ügyfélszoftvert, amely megakadályozza az SSH-kapcsolatokhoz
* Helyileg telepített hálózatfigyelési szoftver, amely megakadályozza az SSH-kapcsolatokhoz
* Más típusú biztonsági szoftverek felügyeljék a forgalmat, vagy az adott típusú forgalom engedélyezése vagy letiltása

Ha ezek a feltételek vonatkoznak, ideiglenesen tiltsa le a szoftvert, és próbálkozzon az SSH-kapcsolatot egy helyi számítógép található a kapcsolat a számítógép letiltásának okát. A hálózati rendszergazdával, javítsa ki a szoftver a beállítások lehetővé teszik az SSH-kapcsolatok majd működni.

Ha a tanúsítvány alapú hitelesítést használ, ellenőrizze, hogy rendelkezik ezeket az engedélyeket az .ssh mappa a kezdőkönyvtárban:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*.pub
* Chmod 600 ~/.ssh/id_rsa (vagy bármely más fájlokat a rajtuk tárolt titkos kulcsok)
* Chmod 644 ~/.ssh/known_hosts (SSH-kapcsolaton keresztül csatlakozott gazdagépeket tartalmaz)

## <a name="source-2-organization-edge-device"></a>2. forrás: Szervezet peremhálózati eszköz
A szervezet a peremhálózati eszköz a probléma forrása a hibát, győződjön meg arról, hogy olyan számítógépre, amely közvetlenül kapcsolódik az internethez SSH-kapcsolatok tehet az Azure virtuális gép. Ha a telephelyek közötti VPN vagy Azure ExpressRoute-kapcsolaton keresztül érik el a virtuális Gépet, folytassa a [forrás 4: a hálózati biztonsági csoportok](#nsg).

![Diagram, amely kiemeli a szervezet a peremhálózati eszköz](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Ha még nem rendelkezik olyan számítógépre, amely közvetlenül csatlakozik az internethez, hozzon létre egy új Azure virtuális gép saját erőforráscsoportban vagy felhőalapú szolgáltatás és használják. További információkért lásd: [Azure-ban futó Linux virtuális gép létrehozása](quick-create-cli.md). Amikor elkészült, a tesztet, törölje az erőforráscsoportba vagy a virtuális gép és a felhőalapú szolgáltatás.

Ha az SSH-kapcsolat létrehozhat egy olyan számítógéppel, közvetlenül csatlakozik az internethez, ellenőrizze a szervezet peremhálózati eszközön:

* Az SSH-forgalmat az Internet megakadályozó belső tűzfal
* Egy proxykiszolgálóra, amely megakadályozza az SSH-kapcsolatokhoz
* Behatolásérzékelési vagy hálózatfigyelési a peremhálózati hálózat, amely megakadályozza az SSH-kapcsolatok eszközökön futó szoftverek

A hálózati rendszergazda segítségét, a szervezet biztonsági eszközök a beállítások lehetővé teszik a SSH-forgalmat az Internet dolgozni.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>3. forrás: Felhő szolgáltatásvégpont és hozzáférés-vezérlési lista
> [!NOTE]
> Ebből a forrásból csak a klasszikus üzembe helyezési modell használatával létrehozott virtuális gépek vonatkozik. Virtuális gépek erőforrás-kezelő használatával létrehozott, folytassa a [forrás-4: a hálózati biztonsági csoportok](#nsg).

A felhőalapú szolgáltatás végpontjának és hozzáférés-vezérlési lista kiküszöbölhetők a hiba a forrásaként, győződjön meg arról, hogy az ugyanazon virtuális hálózatban lévő másik Azure virtuális gép SSH-kapcsolatok tehet a virtuális gép.

![Diagram, amely kiemeli a felhőalapú szolgáltatás végpontjának és hozzáférés-vezérlési lista](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Ha az azonos virtuális hálózatban lévő másik virtuális gép nem rendelkezik, könnyedén létrehozhat egyet. További információkért lásd: [Linux virtuális gép létrehozása az Azure-ban a parancssori felület](quick-create-cli.md). A tesztelés befejezése az extra virtuális gép törlése

Ha az SSH-kapcsolat az azonos virtuális hálózatban lévő virtuális gépen is létrehozhat, ellenőrizze a következő területeken:

* **A végpont-konfiguráció a cél virtuális gép SSH-forgalmat.** A végpont titkos TCP-portot meg kell felelnie a TCP-portot, amelyet a virtuális gép SSH szolgáltatást figyel. (Az alapértelmezett port a 22-es). Az SSH TCP-portszámot, az Azure portálon ellenőrizze a kiválasztásával **virtuális gépek** > *nevet a virtuális gép* > **beállítások**  >   **Végpontok**.
* **A hozzáférés-vezérlési listája az SSH-forgalom végpont a cél virtuális gépen.** Hozzáférés-vezérlési Listában lehetővé teszi megadását engedélyezett vagy megtagadott a bejövő forgalom az internetről, a forrás IP-címe alapján. Helytelenül konfigurált hozzáférés-vezérlési listák előfordulhat, hogy a végponthoz SSH bejövő forgalmat. Ellenőrizze a hozzáférés-vezérlési listákat, annak érdekében, hogy a proxy a nyilvános IP-címekről érkező bejövő forgalmat, vagy más biztonsági kiszolgáló engedélyezett. További információkért lásd: [hálózati hozzáférés-vezérlési listák (ACL)](../../virtual-network/virtual-networks-acl.md).

A végpont forrásként a probléma kiküszöbölése, távolítsa el az aktuális végpont, hozzon létre egy másik végpontjával, és adja meg az SSH-nevét (TCP-portját 22 a nyilvános és magánhálózati port számát). További információkért lásd: [beállítása az Azure virtuális gép végpontja](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>4. forrás: Hálózati biztonsági csoportok
Hálózati biztonsági csoportok lehetővé teszik a részletesebb vezérlést engedélyezett bejövő és kimenő forgalom. Létrehozhat szabályokat, amelyek span alhálózatok és a felhőalapú szolgáltatások egy Azure virtuális hálózatra. Ellenőrizze a hálózati biztonsági csoportszabályok annak ellenőrzéséhez, hogy engedélyezett-e SSH-forgalmat az Internet felé és felől.
További információkért lásd: [hálózati biztonsági csoportok](../../virtual-network/virtual-networks-nsg.md).

Is használhatja IP ellenőrizze az NSG-konfiguráció érvényesítése céljából. További információkért lásd: [áttekintése Azure hálózatfigyelési](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>5. forrás: Az Azure virtuális Linux-alapú számítógép
A lehetséges problémák utolsó forrása az Azure virtuális gép saját magát.

![Diagram, amely kiemeli a Linux-alapú Azure virtuális gépek](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Ha még nem tette meg, kövesse az utasításokat [jelszó vagy SSH a Linux-alapú virtuális gépek alaphelyzetbe](classic/reset-access.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

Próbáljon újra kapcsolódni a számítógépről. Ha továbbra is sikertelen, az alábbiakban a lehetséges problémákat:

* Az SSH szolgáltatás nem fut a cél virtuális gépen.
* Az SSH szolgáltatás nem figyeli a 22-es TCP-portot. Teszteléséhez, a helyi számítógépen a telnet ügyfélprogram telepítése és futtatása "telnet *cloudServiceName*. cloudapp.net 22". Ez a lépés határozza meg, ha a virtuális gép lehetővé teszi a bejövő és kimenő kommunikáció az SSH-végponthoz.
* Az a cél virtuális gép helyi tűzfalának szabályokat, amelyek akadályozzák a bejövő vagy kimenő SSH-forgalmat.
* Behatolásérzékelési vagy az Azure virtuális gépen futó hálózatfigyelési megakadályozza az SSH-kapcsolatokhoz.

## <a name="additional-resources"></a>További források
Alkalmazás-hozzáférés hibaelhárítással kapcsolatos további információkért lásd: [egy Azure virtuális gépen futó alkalmazáshoz való hozzáférés hibáinak elhárítása](troubleshoot-app-connection.md)
