---
title: Részletes SSH-hibaelhárítás egy Azure-beli virtuális gépen | Microsoft Docs
description: Az Azure-beli virtuális gépekhez való csatlakozással kapcsolatos problémák részletesebb SSH-hibaelhárítási lépései
keywords: SSH-kapcsolatok elutasítása, SSH-hiba, Azure SSH, SSH-kapcsolatok nem sikerült
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 41265973df21be289e63cbd6ed2703febc50cff2
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70090500"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Részletes SSH-hibaelhárítási lépések az Azure-beli Linux rendszerű virtuális gépekhez való csatlakozással kapcsolatban
Az SSH-ügyfélnek számos oka lehet annak, hogy az SSH-szolgáltatás nem érhető el a virtuális gépen. Ha követte a további [általános SSH-hibaelhárítási lépéseket](troubleshoot-ssh-connection.md), akkor a kapcsolódási problémával kapcsolatos további hibaelhárításra van szükség. Ez a cikk végigvezeti a részletes hibaelhárítási lépéseken, amelyekkel meghatározhatja, hogy az SSH-kapcsolatok hol és Hogyan oldhatók meg.

## <a name="take-preliminary-steps"></a>Előzetes lépések
Az alábbi ábrán az érintett összetevők láthatók.

![Az SSH-szolgáltatás összetevőit megjelenítő diagram](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

A következő lépések segítségével elkülönítheti a hiba forrását, és kiderítheti a megoldásokat és a megkerülő megoldásokat.

1. Keresse meg a virtuális gép állapotát a portálon.
   A [Azure Portal](https://portal.azure.com)válassza a **virtuális gépek virtuális gép** > *neve*elemet.

   A virtuális gép állapotjelző paneljének futniakell. Görgessen le a számítási, tárolási és hálózati erőforrások legutóbbi tevékenységének megjelenítéséhez.

2. Válassza a **Beállítások** lehetőséget a végpontok, IP-címek, hálózati biztonsági csoportok és egyéb beállítások vizsgálatához.

   A virtuális gépnek rendelkeznie kell egy, a **végpontokban** vagy a **[hálózati biztonsági csoportban](../../virtual-network/security-overview.md)** megtekinthető SSH-forgalomhoz megadott végponttal. A Resource Manager használatával létrehozott virtuális gépeken található végpontokat egy hálózati biztonsági csoport tárolja. Ellenőrizze, hogy a szabályok érvényesek-e a hálózati biztonsági csoportra, és hivatkoznak-e az alhálózatra.

A hálózati kapcsolat ellenőrzéséhez tekintse meg a konfigurált végpontokat, és ellenőrizze, hogy tud-e csatlakozni a virtuális géphez egy másik protokollon keresztül, például HTTP vagy más szolgáltatás használatával.

Ezeket a lépéseket követve próbálja megismételni az SSH-kapcsolatokat.

## <a name="find-the-source-of-the-issue"></a>A probléma forrásának megkeresése
Előfordulhat, hogy az SSH-ügyfél nem tud csatlakozni az SSH szolgáltatáshoz az Azure-beli virtuális gépen az alábbi területeken található problémák vagy helytelen konfiguráció miatt:

* [SSH-ügyfélszámítógép](#source-1-ssh-client-computer)
* [Szervezeti peremhálózati eszköz](#source-2-organization-edge-device)
* [Cloud Service-végpont és hozzáférés-vezérlési lista (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Hálózati biztonsági csoportok](#source-4-network-security-groups)
* [Linux-alapú Azure-beli virtuális gép](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>1\. Forrás: SSH-ügyfélszámítógép
Ha a számítógépét a hiba forrásaként szeretné felvenni, ellenőrizze, hogy képes-e SSH-kapcsolatot létesíteni egy másik helyszíni, Linux-alapú számítógéppel.

![Az SSH-ügyfélszámítógépek összetevőit kiemelő diagram](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Ha a kapcsolat meghiúsul, ellenőrizze a következő problémákat a számítógépen:

* Helyi tűzfal-beállítás, amely letiltja a bejövő vagy kimenő SSH-forgalmat (TCP 22)
* Az SSH-kapcsolatokat megakadályozó helyileg telepített alkalmazásproxy szoftver
* Helyileg telepített hálózatfigyelő szoftver, amely megakadályozza az SSH-kapcsolatokat
* Más típusú biztonsági szoftverek, amelyek figyelik a forgalmat, vagy bizonyos típusú forgalmat engedélyeznek vagy tiltanak le

Ha az alábbi feltételek valamelyike teljesül, átmenetileg tiltsa le a szoftvert, és próbáljon meg SSH-kapcsolatokat létesíteni egy helyszíni számítógéppel, hogy megtudja, milyen okból blokkolja a rendszer a kapcsolódást a számítógépen. Ezután a hálózati rendszergazdával együttműködve javítsa ki a szoftver beállításait az SSH-kapcsolatok engedélyezéséhez.

Ha tanúsítványalapú hitelesítést használ, ellenőrizze, hogy rendelkezik-e az alábbi engedélyekkel a saját könyvtárában található. ssh mappához:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*. pub
* Chmod 600 ~/.ssh/id_rsa (vagy bármely más, saját titkos kulccsal rendelkező fájl)
* Chmod 644 ~/.ssh/known_hosts (tartalmazza azokat a gazdagépeket, amelyekhez SSH-n keresztül kapcsolódott)

## <a name="source-2-organization-edge-device"></a>2\. Forrás: Szervezeti peremhálózati eszköz
Ha a szervezet peremhálózati eszközét a hiba forrásaként szeretné felvenni, ellenőrizze, hogy az internethez közvetlenül csatlakozó számítógép képes-e SSH-kapcsolatot létesíteni az Azure-beli virtuális géppel. Ha helyek közötti VPN-en vagy Azure ExpressRoute-kapcsolaton keresztül éri el a virtuális gépet, ugorjon a [4. forrásra: Hálózati biztonsági csoportok](#nsg).

![A szervezet peremhálózati eszközét kiemelő diagram](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Ha nincs olyan számítógépe, amely közvetlenül csatlakozik az internethez, hozzon létre egy új Azure-beli virtuális gépet a saját erőforráscsoporthoz vagy a Cloud Service-ben, és használja az új virtuális gépet. További információ: [Linux rendszerű virtuális gép létrehozása az Azure-ban](../linux/quick-create-cli.md). Ha elkészült a teszteléssel, törölje az erőforráscsoportot vagy a virtuális gépet és a felhőalapú szolgáltatást.

Ha SSH-kapcsolatot hoz létre közvetlenül az internethez csatlakozó számítógéppel, ellenőrizze a szervezet peremhálózati eszközét a következőhöz:

* Belső tűzfal, amely blokkolja az internettel való SSH-forgalmat
* Az SSH-kapcsolatokat megakadályozó proxykiszolgáló
* A peremhálózati hálózatban lévő eszközökön futó behatolás-észlelési vagy hálózati figyelő szoftver, amely megakadályozza az SSH-kapcsolatokat

A hálózati rendszergazdával együttműködve javítsa ki a szervezet peremhálózati eszközeinek beállításait, hogy engedélyezze az SSH-forgalmat az internettel.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>3\. Forrás: Cloud Service-végpont és ACL
> [!NOTE]
> Ez a forrás csak a klasszikus üzemi modell használatával létrehozott virtuális gépekre vonatkozik. A Resource Manager használatával létrehozott virtuális gépek esetén ugorjon a [4. forrásra: Hálózati biztonsági csoportok](#nsg).

Ha el szeretné távolítani a Cloud Service-végpontot és az ACL-t a hiba forrásaként, ellenőrizze, hogy az azonos virtuális hálózatban lévő másik Azure-beli virtuális gép csatlakozhat-e az SSH használatával.

![A Cloud Service-végpontot és az ACL-t kiemelő diagram](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Ha nem rendelkezik másik virtuális géppel ugyanabban a virtuális hálózatban, egyszerűen létrehozhat egyet. További információ: [Linux rendszerű virtuális gép létrehozása az Azure-ban a parancssori felület használatával](../linux/quick-create-cli.md). Ha végzett a teszteléssel, törölje a további virtuális gépet.

Ha az azonos virtuális hálózatban lévő virtuális géppel is létrehozhat SSH-kapcsolatokat, ellenőrizze a következő területeket:

* **A cél virtuális gépen lévő SSH-forgalom végpont-konfigurációja.** A végpont magánhálózati TCP-portjának meg kell egyeznie azzal a TCP-porttal, amelyen a virtuális gépen futó SSH-szolgáltatás figyel. (Az alapértelmezett port a 22). Ellenőrizze az SSH TCP-port számát a Azure Portal a **virtuális gépek** > virtuálisgép-*név* > **beállításai** > **végpontok**kiválasztásával.
* **Az SSH-forgalom végpontjának hozzáférés-vezérlési listája a cél virtuális gépen.** Az ACL lehetővé teszi, hogy a forrás IP-címe alapján megadhatja az internetről engedélyezett vagy megtagadott bejövő forgalmat. A helytelenül konfigurált ACL-ek megakadályozhatják a bejövő SSH-forgalmat a végponthoz. Ellenőrizze az ACL-eket, hogy a proxy vagy más peremhálózati kiszolgáló nyilvános IP-címeiről érkező bejövő forgalom engedélyezett-e. További információ: [a hálózati hozzáférés-vezérlési listák (ACL-ek)](../../virtual-network/virtual-networks-acl.md).

Ha el szeretné távolítani a végpontot a probléma forrásaként, távolítsa el az aktuális végpontot, hozzon létre egy másik végpontot, és adja meg az SSH-nevet (a nyilvános és a magánhálózati portszám esetében a 22-es TCP-port). További információ: végpontok [beállítása egy virtuális gépen az Azure-ban](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>4\. Forrás: Network security groups (Hálózati biztonsági csoportok)
A hálózati biztonsági csoportok lehetővé teszik a bejövő és kimenő forgalom részletesebb szabályozását. Létrehozhat olyan szabályokat, amelyek az alhálózatokat és a Cloud Servicest egy Azure-beli virtuális hálózatban fogják kialakítani. Ellenőrizze a hálózati biztonsági csoport szabályait, és győződjön meg arról, hogy az internetre irányuló és onnan érkező SSH-forgalom engedélyezett.
További információ: [Tudnivalók a hálózati biztonsági csoportokról](../../virtual-network/security-overview.md).

Az IP-ellenőrzés használatával is ellenőrizheti a NSG konfigurációját. További információ: az [Azure Network monitoring áttekintése](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>5\. Forrás: Linux-alapú Azure-beli virtuális gép
A lehetséges problémák utolsó forrása az Azure-beli virtuális gép.

![A Linux-alapú Azure virtuális gépet kiemelő ábra](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Ha még nem tette meg, kövesse az utasításokat a [jelszó Linux-alapú virtuális gépek](../linux/reset-password.md)alaphelyzetbe állításához.

Próbálkozzon újra a számítógépről való csatlakozással. Ha a probléma továbbra is fennáll, a következő lehetséges problémák merülhetnek fel:

* Az SSH szolgáltatás nem fut a cél virtuális gépen.
* Az SSH szolgáltatás nem figyeli a 22-es TCP-portot. A teszteléshez telepítsen egy Telnet-ügyfelet a helyi számítógépen, és futtassa a "Telnet *felhőszolgáltatásneve*. cloudapp.net 22" parancsot. Ez a lépés határozza meg, hogy a virtuális gép engedélyezi-e a bejövő és kimenő kommunikációt az SSH-végpont számára.
* A célként megadott virtuális gépen a helyi tűzfal olyan szabályokkal rendelkezik, amelyek megakadályozzák a bejövő vagy kimenő SSH-forgalmat.
* Az Azure-beli virtuális gépen futó behatolás-észlelési vagy Hálózatfigyelő szoftver megakadályozza az SSH-kapcsolatokat.

## <a name="additional-resources"></a>További források
Az alkalmazás-hozzáférés hibaelhárításával kapcsolatos további információkért lásd: Azure-beli [virtuális gépen futó alkalmazásokhoz való hozzáférés hibaelhárítása](../linux/troubleshoot-app-connection.md)
