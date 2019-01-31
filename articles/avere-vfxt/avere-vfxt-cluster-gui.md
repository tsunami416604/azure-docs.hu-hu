---
title: Hozzáférés a Vezérlőpult Avere vFXT – Azure
description: Hogyan lehet csatlakozni a vFXT fürt és a böngésző alapú Avere Vezérlőpult a Avere vFXT konfigurálása
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 30c03d52e31f70448eef07b4567083061605d8dd
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55300472"
---
# <a name="access-the-vfxt-cluster"></a>Hozzáférés a vFXT fürt

Módosítsa a beállításokat, és a Avere vFXT fürt monitorozására, használja a Avere Vezérlőpult. Avere Vezérlőpult nem böngészőalapú grafikus felület a fürthöz.

A vFXT fürt egy privát virtuális hálózaton belül helyezkedik el, mert az SSH-alagút létrehozása, vagy másik módszerrel kívánja érni a fürt felügyeleti IP-címet. Alapszintű két lépésből áll: 

1. A munkaállomáson és a privát virtuális hálózat közötti kapcsolat létrehozása 
1. Egy webböngészőben a fürt Vezérlőpult betöltése 

> [!NOTE] 
> Ez a cikk feltételezi, hogy meg van egy nyilvános IP-címet a fürt vezérlő vagy a fürt virtuális hálózaton belül egy másik virtuális Géphez. Ez a cikk ismerteti, hogyan használható a virtuális gép gazdagépként, aki a fürtöt. Ha a VPN-t vagy az ExpressRoute virtuális hálózati hozzáféréshez használ, akkor ugorjon a [Connect Avere Vezérlőpult](#connect-to-the-avere-control-panel-in-a-browser).

Mielőtt csatlakozna, győződjön meg arról, hogy az SSH nyilvános/titkos kulcspár, amelyet a fürt vezérlő létrehozásakor használt telepítve van-e a helyi gépen. Olvassa el az SSH kulcsok dokumentációját [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) Ha segítségre van szüksége. (A jelszó helyett a nyilvános kulcs használatakor kéri, adja meg, ha csatlakozik.) 

## <a name="ssh-tunnel-with-a-linux-host"></a>SSH-alagutat egy Linux-gazdagépre

Ha Linux-alapú ügyfelet használ, használja az SSH-alagútkezelési az űrlap-parancsot: 

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*@*controller_public_IP*

Ezzel a paranccsal összekapcsolja a fürt felügyeleti IP-címre a fürt vezérlő IP-címen keresztül.

Példa:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

Hitelesítés automatikusan végbemegy, ha a fürt létrehozásához használt nyilvános SSH-kulcsot, és a megfelelő kulcs telepítve van az ügyfélrendszeren. Ha jelszót használt, a rendszer kérni fogja annak megadására.

## <a name="ssh-tunnel-with-a-windows-host"></a>Windows állomással való SSH-alagút

Ez a példa a gyakori Windows-alapú terminál segédprogram, a putty-t használja.

Adja meg a PuTTY **állomásnév** mezőt a fürt vezérlő felhasználónévvel és az IP-címére: *your_username*@*controller_public_IP*.

Például: ``azureuser@203.0.113.51``

Az a **konfigurációs** panelen:

1. Bontsa ki a **kapcsolat** > **SSH** a bal oldalon. 
1. Kattintson a **alagutak**. 
1. Adja meg a forrásport, például a 8443. 
1. A célhelyen adja meg a vFXT fürt felügyeleti IP-cím és a 443-as porton. 
   Például: ``203.0.113.51:443``
1. Kattintson a **Hozzáadás** parancsra.
1. Kattintson az **Open** (Megnyitás) elemre.

![Képernyőkép a Putty alkalmazást helyéről a alagút hozzáadásához kattintson](media/avere-vfxt-ptty-numbered.png)

Hitelesítés automatikusan végbemegy, ha a fürt létrehozásához használt nyilvános SSH-kulcsot, és a megfelelő kulcs telepítve van az ügyfélrendszeren. Ha jelszót használt, a rendszer kérni fogja annak megadására.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Csatlakozás a Avere Vezérlőpult böngészőben

Ebben a lépésben egy webböngészőben a konfigurációs eszközt, a vFXT fürtön futó való kapcsolódáshoz használ.

* Az SSH-alagút kapcsolatot, nyissa meg a webböngészőjét, és navigáljon a https://127.0.0.1:8443. 

  Akkor csatlakozik a fürt IP-cím létrehozásakor az alagút, így ugyanúgy kell használnia a localhost IP-címet a böngészőben. Ha nem 8443 helyi portot használta, a portszámot használja.

* Ha a fürt eléréséhez a VPN-t vagy ExpressRoute használ, lépjen a fürt felügyeleti IP-címét a böngészőben. Például: ``https://203.0.113.51``

A böngésző típusától függően előfordulhat, hogy kell kattintson **speciális** , és ellenőrizze, hogy biztonságosan oldalára.

Adja meg a felhasználónevét `admin` és a fürt létrehozásakor megadott rendszergazdai jelszót.

![Képernyőkép a Avere jelentkezzen be a lapon a rendszergazda felhasználónév és jelszó használatával](media/avere-vfxt-gui-login.png)

Kattintson a **bejelentkezési** vagy nyomja le az enter billentyűt.

## <a name="next-steps"></a>További lépések

Most, hogy Ön hozzáfér a fürthöz, [támogatja](avere-vfxt-enable-support.md).
