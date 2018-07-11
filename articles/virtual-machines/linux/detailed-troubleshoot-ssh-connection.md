---
title: Egy Azure virtuális gép részletes SSH hibaelhárítása |} A Microsoft Docs
description: Részletes hibaelhárítási lépései az Azure virtuális gépként való csatlakozással kapcsolatos problémákat SSH
keywords: ssh kapcsolat elutasítva, ssh hiba, az azure ssh, SSH-kapcsolatot nem sikerült
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: 425bb5bc7e6a1bc877041d447e999e9270dd3bfb
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932024"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Az SSH részletes hibaelhárítási lépései Linux rendszerű virtuális gép az Azure-ban való csatlakozással kapcsolatos problémákat
Nincsenek számos oka lehet, hogy az SSH-ügyfél nem képes elérni a virtuális gép SSH-szolgáltatást. Ha követte, minél több keresztül [hibaelhárítási lépések általános SSH](troubleshoot-ssh-connection.md), a csatlakozási probléma további hibaelhárításához kell. Ez a cikk végigvezeti a felhasználót az részletes hibaelhárítási lépések meghatározásához, ahol az SSH-kapcsolat nem működik, és a megoldásának módjával.

## <a name="take-preliminary-steps"></a>Előzetes lépések
A következő ábrán az összetevők, amelyek szerepet játszanak.

![Bemutató diagram, amelyek SSH-szolgáltatás összetevője](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

A következő lépések segítenek elkülöníteni a hiba forrását, és döntse el, hogy megoldásai és megkerülő megoldások.

1. A portálon a virtuális gép állapotának ellenőrzéséhez.
   Az a [az Azure portal](https://portal.azure.com)válassza **virtuális gépek** > *virtuális gép neve*.

   Meg kell jelennie a virtuális gép állapota panel **futó**. Görgessen le a közelmúltbeli tevékenység megjelenítése a számítási, tárolási és hálózati erőforrásokat.

2. Válassza ki **beállítások** megvizsgálhatja a végpontok, IP-címek, hálózati biztonsági csoportok és egyéb beállításokat.

   A virtuális gép megtekintheti az SSH-forgalom a meghatározott végpontot kell **végpontok** vagy  **[hálózati biztonsági csoport](../../virtual-network/security-overview.md)**. Végpontok a Resource Manager használatával létrehozott virtuális gépek hálózati biztonsági csoportok vannak tárolva. Győződjön meg arról, hogy a szabályok lettek alkalmazva a hálózati biztonsági csoportot, és az alhálózat hivatkozott.

Ellenőrizze a hálózati kapcsolatot, ellenőrizze a konfigurált végpontokra, és tekintse meg, ha a virtuális gép egy másik protokoll, például a HTTP- vagy más szolgáltatáson keresztül csatlakozhat.

Ezeket a lépéseket próbálja meg újra az SSH-kapcsolat.

## <a name="find-the-source-of-the-issue"></a>Keresse meg a probléma forrása
Az SSH-ügyfél, a számítógép csatlakozni az SSH-szolgáltatást az Azure virtuális gépen problémák vagy a következő területeken konfigurációs hibák miatt meghiúsulhat:

* [SSH-ügyfélszámítógép](#source-1-ssh-client-computer)
* [Szervezet edge-eszköz](#source-2-organization-edge-device)
* [Cloud service-végpont, és hozzáférés-vezérlési lista (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Hálózati biztonsági csoportok](#source-4-network-security-groups)
* [Linux-alapú Azure virtuális Gépen](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>1. forrás: SSH ügyfélszámítógép
A számítógép a hiba forrását a hibát, ellenőrizze, hogy azt egy másik helyszíni-, Linux-alapú számítógép SSH-kapcsolatok kezdeményezhetik.

![Ábra, amely kiemeli az SSH-ügyfél számítógép összetevői](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Ha a kapcsolódás sikertelen, ellenőrizze a számítógépen a következő problémákat:

* Beállítás, amely egy helyi tűzfal blokkolja a bejövő vagy kimenő SSH-forgalom (TCP 22)
* Helyileg telepített ügyfél proxy szoftver, amely megakadályozza az SSH-kapcsolatok
* Helyileg telepítve szoftver, amely megakadályozza az SSH-kapcsolatok Hálózatfigyelő
* Biztonsági szoftver más típusú forgalom figyelésére, vagy adott típusú forgalom engedélyezése/letiltása

Ha ezek a feltételek közül a alkalmazni, ideiglenesen tiltsa le a szoftvert, és próbálja meg egy SSH-kapcsolatot egy helyszíni számítógépen található, a kapcsolat a számítógép letiltásának okát. Ezután használja a hálózati rendszergazda segítségét az SSH-kapcsolatok engedélyezése a szoftverfrissítési beállításait.

Ha a tanúsítvány alapú hitelesítést használ, ellenőrizze, hogy ezeket az engedélyeket az .ssh mappa a kezdőkönyvtárban:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*.pub
* Chmod 600 ~/.ssh/id_rsa (vagy bármely más olyan fájlra, amely a bennük tárolt titkos kulcsok)
* Chmod 644 ~/.ssh/known_hosts (tartalmazza a gazdagépek, amelyek SSH-n keresztül csatlakozott)

## <a name="source-2-organization-edge-device"></a>2. forrás: Szervezet edge-eszköz
A szervezet kiküszöbölése peremhálózati eszköz, a hiba okának győződjön meg arról, hogy egy számítógép közvetlenül csatlakozik az internethez végezhet SSH-kapcsolatok az Azure virtuális gép. Ha a virtuális gép site-to-site VPN vagy az Azure ExpressRoute-kapcsolaton keresztül ér el, akkor ugorjon a [forrás 4: hálózati biztonsági csoportok](#nsg).

![Ábra, amely kiemeli a szervezet a peremhálózati eszköz](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Ha nem rendelkezik olyan számítógépre, amely közvetlenül csatlakozik az internethez, egy új Azure virtuális gép létrehozása a saját erőforráscsoport vagy felhőalapú szolgáltatás és új virtuális gép használja. További információkért lásd: [Azure-ban futó Linux rendszerű virtuális gép létrehozása](quick-create-cli.md). Ha elkészült, a tesztet, törölje az erőforráscsoportra vagy a virtuális gép és a felhőalapú szolgáltatás.

Ha az SSH-kapcsolatot is létrehozhat egy olyan számítógéppel, közvetlenül csatlakozik az internethez, ellenőrizze a szervezet az edge-eszköz:

* Egy belső tűzfal blokkolja az internettel SSH-forgalom
* Egy proxykiszolgálóra, amely megakadályozza az SSH-kapcsolatok
* Behatolásészlelés vagy hálózatfigyelési az edge-hálózatot, amely megakadályozza az SSH-kapcsolatokat az eszközökön futó szoftver

Javítsa ki a szervezet a peremhálózati eszközökre, hogy az internetes SSH-forgalom beállításait a hálózati rendszergazdával együttműködve.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>3. forrás: Cloud service-végpont és ACL-JEI
> [!NOTE]
> Ebből a forrásból csak a klasszikus üzemi modell használatával létrehozott virtuális gépek vonatkozik. A virtuális gépek Resource Manager használatával létrehozott, hagyja ki [forrás 4: hálózati biztonsági csoportok](#nsg).

A cloud service-végpont és ACL-JEI nyilvánvaló, hogy a hiba forrását, győződjön meg arról, hogy egy másik Azure virtuális Géphez ugyanazon a virtuális hálózaton csatlakozhat SSH-val is.

![Ábra, amely kiemeli a cloud service-végpont és ACL-JEI](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Ha egy másik virtuális Géphez ugyanazon a virtuális hálózaton nem rendelkezik, könnyedén létrehozhat egyet. További információkért lásd: [Linux rendszerű virtuális gép létrehozása az Azure-ban a parancssori felületről](quick-create-cli.md). Törölje a felesleges virtuális Gépet, amikor elkészült, a tesztet.

Ha az SSH-kapcsolatot is létrehozhat egy virtuális géppel azonos virtuális hálózatban, ellenőrizze a következő területeken:

* **A végpont-konfigurációja a cél virtuális Gépre SSH-forgalmat.** A végpont a privát TCP-portot meg kell egyeznie a TCP-portot, amelyen a virtuális gép SSH-szolgáltatást figyel. (Az alapértelmezett port a 22-es). Az SSH TCP-port száma, az Azure Portalon ellenőrizze kiválasztásával **virtuális gépek** > *virtuális gép neve* > **beállítások**  >   **Végpontok**.
* **A hozzáférés-Vezérlési az SSH-forgalom végpont a cél virtuális gépen.** Egy ACL lehetővé teszi, hogy adja meg engedélyezett vagy tiltott bejövő forgalom az internetről, a forrás IP-címe alapján. Helytelenül konfigurált hozzáférés-vezérlési listák megakadályozhatja a bejövő SSH-forgalmat a végponthoz. Ellenőrizze a hozzáférés-vezérlési listák, győződjön meg arról, hogy a proxy nyilvános IP-címekről érkező bejövő forgalmat, vagy más biztonsági kiszolgáló engedélyezve van. További információkért lásd: [hálózati hozzáférés-vezérlési listák (ACL)](../../virtual-network/virtual-networks-acl.md).

Nyilvánvaló, hogy a végpont a probléma forrását, távolítsa el a jelenlegi végpont, hozzon létre egy másik végpontot, és adja meg az SSH-nevét (TCP-port 22 nyilvános és privát portszám). További információkért lásd: [az Azure-beli virtuális gépen végpontok beállítása](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>4. forrás: Hálózati biztonsági csoportok
Hálózati biztonsági csoportok lehetővé teszik az engedélyezett bejövő és kimenő forgalom pontosabban szabályozhatja. Létrehozhat szabályokat, amelyek span alhálózatok és a felhőalapú szolgáltatások Azure-beli virtuális hálózathoz. Ellenőrizze a hálózati biztonsági csoport szabályait, győződjön meg arról, hogy engedélyezve van-e az SSH-forgalom az Internet felé és felől.
További információkért lásd: [hálózati biztonsági csoportokkal kapcsolatos](../../virtual-network/security-overview.md).

Is használhatja IP ellenőrizze az NSG-konfiguráció ellenőrzése. További információkért lásd: [Figyelés áttekintése az Azure network](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>5. forrás: Linux-alapú Azure virtuális gépen
A lehetséges problémákat utolsó forrása az Azure virtuális gépen magát.

![Ábra, amely kiemeli a Linux-alapú Azure virtuális gépen](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Ha még nem tette meg, kövesse az utasításokat [alaphelyzetbe állítja a jelszót Linux-alapú virtuális gépek](reset-password.md).

Próbáljon meg ismét csatlakozni a számítógépről. Ha továbbra is sikertelen, az alábbiakban a lehetséges problémák:

* Az SSH szolgáltatás nem fut a cél virtuális gépen.
* Az SSH-szolgáltatást nem figyeli a 22-es TCP-portot. Tesztelni, a helyi számítógépen a telnet ügyfélprogram telepítése és futtatása "telnet *cloudServiceName*. cloudapp.net 22-es". Ez a lépés határozza meg, ha a virtuális gép lehetővé teszi a bejövő és kimenő kommunikációt az SSH-végpont.
* A helyi tűzfal a cél virtuális gépen, amely megakadályozza, hogy az SSH-forgalom a bejövő vagy kimenő szabályokat tartalmaz.
* Behatolásészlelés vagy az Azure virtuális gépen futó szoftver hálózatfigyelési megakadályozza az SSH-kapcsolatokat.

## <a name="additional-resources"></a>További források
Alkalmazás-hozzáférés hibaelhárítással kapcsolatos további információkért lásd: [egy Azure virtuális gépen futó alkalmazásokhoz való hozzáférés hibaelhárítása](troubleshoot-app-connection.md)
