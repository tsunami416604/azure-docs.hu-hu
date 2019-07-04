---
title: Hozzáférés a Vezérlőpult Avere vFXT – Azure
description: Hogyan lehet csatlakozni a vFXT fürt és a böngésző alapú Avere Vezérlőpult a Avere vFXT konfigurálása
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: v-erkell
ms.openlocfilehash: 830be92d37f304598cca05c3ac80973158c38a59
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439974"
---
# <a name="access-the-vfxt-cluster"></a>Hozzáférés a vFXT fürt

Módosítsa a beállításokat, és a Avere vFXT fürt monitorozására, használja a Avere Vezérlőpult. Avere Vezérlőpult nem böngészőalapú grafikus felület a fürthöz.

A vFXT fürt egy privát virtuális hálózaton belül helyezkedik el, mert az SSH-alagút létrehozása, vagy másik módszerrel kívánja érni a fürt felügyeleti IP-címet. Alapszintű két lépésből áll: 

1. A munkaállomáson és a privát virtuális hálózat közötti kapcsolat létrehozása 
1. Egy webböngészőben a fürt Vezérlőpult betöltése 

> [!NOTE] 
> Ez a cikk feltételezi, hogy meg van egy nyilvános IP-címet a fürt vezérlő vagy a fürt virtuális hálózaton belül egy másik virtuális Géphez. Ez a cikk ismerteti, hogyan használható a virtuális gép gazdagépként, aki a fürtöt. Ha a VPN-t vagy az ExpressRoute virtuális hálózati hozzáféréshez használ, akkor ugorjon a [Connect Avere Vezérlőpult](#connect-to-the-avere-control-panel-in-a-browser).

Mielőtt csatlakozna, győződjön meg arról, hogy az SSH nyilvános/titkos kulcspár, amelyet a fürt vezérlő létrehozásakor használt telepítve van-e a helyi gépen. Olvassa el az SSH kulcsok dokumentációját [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) Ha segítségre van szüksége. (A jelszó helyett a nyilvános kulcs használatakor kéri, adja meg, ha csatlakozik.) 

## <a name="create-an-ssh-tunnel"></a>SSH-alagút létrehozása 

Létrehozhat egy SSH-alagutat a parancssorból, a Linux-alapú vagy a Windows 10-es ügyfél rendszer. 

Használja az SSH-alagútkezelési az űrlap-parancsot: 

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*\@*controller_public_IP*

Ezzel a paranccsal összekapcsolja a fürt felügyeleti IP-címre a fürt vezérlő IP-címen keresztül.

Példa:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

Hitelesítés automatikusan végbemegy, ha a fürt létrehozásához használt nyilvános SSH-kulcsot, és a megfelelő kulcs telepítve van az ügyfélrendszeren. Ha jelszót használt, a rendszer kérni fogja annak megadására.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Csatlakozás a Avere Vezérlőpult böngészőben

Ebben a lépésben egy webböngészőben a konfigurációs eszközt, a vFXT fürtön futó való kapcsolódáshoz használ.

* Az SSH-alagút kapcsolatot, nyissa meg a webböngészőjét, és navigáljon a `https://127.0.0.1:8443`. 

  Akkor csatlakozik a fürt IP-cím létrehozásakor az alagút, így ugyanúgy kell használnia a localhost IP-címet a böngészőben. Ha nem 8443 helyi portot használta, a portszámot használja.

* Ha a fürt eléréséhez a VPN-t vagy ExpressRoute használ, lépjen a fürt felügyeleti IP-címét a böngészőben. Például: ``https://203.0.113.51``

A böngésző típusától függően előfordulhat, hogy kell kattintson **speciális** , és ellenőrizze, hogy biztonságosan oldalára.

Adja meg a felhasználónevét `admin` és a fürt létrehozásakor megadott rendszergazdai jelszót.

![Képernyőkép a Avere jelentkezzen be a lapon a rendszergazda felhasználónév és jelszó használatával](media/avere-vfxt-gui-login.png)

Kattintson a **bejelentkezési** vagy nyomja le az enter billentyűt.

## <a name="next-steps"></a>További lépések

Most, hogy Ön hozzáfér a fürthöz, [támogatja](avere-vfxt-enable-support.md).
