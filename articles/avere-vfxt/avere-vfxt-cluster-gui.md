---
title: Az Avere vFXT vezérlőpult elérése – Azure
description: Hogyan lehet csatlakozni a vFXT klaszter és a böngésző-alapú Avere Vezérlőpult beállítani a Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: fe2fc062f690498f3d1f588887279aa33d2434b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75416152"
---
# <a name="access-the-vfxt-cluster"></a>Hozzáférés a vFXT-fürthöz

A fürt beállításainak módosításához és a fürt figyeléséhez használja az Avere vezérlőpultot. Az Avere Vezérlőpult a fürt böngészőalapú grafikus felülete.

Mivel a vFXT-fürt egy privát virtuális hálózaton belül helyezkedik el, létre kell hoznia egy SSH-alagutat, vagy más módszert kell használnia a fürt felügyeleti IP-címének eléréséhez.

Két alapvető lépés:

1. Kapcsolat létrehozása a munkaállomás és a magánvirtuális hálózat között
1. A fürt vezérlőpultjának betöltése webböngészőben

> [!NOTE]
> Ez a cikk feltételezi, hogy nyilvános IP-címet állított be a fürtvezérlőn vagy a fürt virtuális hálózatán belül lévő másik virtuális gépen. Ez a cikk azt ismerteti, hogyan használhatja a virtuális gép gazdagépként a fürt eléréséhez. Ha virtuális hálózati hozzáféréshez VPN-t vagy ExpressRoute-ot használ, ugorjon [a Csatlakozás a Vezérlőpulthoz](#connect-to-the-avere-control-panel-in-a-browser).

Csatlakozás előtt győződjön meg arról, hogy a fürtvezérlő létrehozásakor használt SSH nyilvános/titkos kulcspár telepítve van a helyi számítógépen. Olvassa el az SSH-kulcsok dokumentációját [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) rendszerhez, ha segítségre van szüksége. Ha nyilvános kulcs helyett jelszót használt, a rendszer kéri, hogy a csatlakozáskor adja meg azt.

## <a name="create-an-ssh-tunnel"></a>SSH-alagút létrehozása

SSH-alagutat egy Linux-alapú vagy Windows 10-es ügyfélrendszer parancssorából hozhat létre.

SSH bújtatási parancsot használ ebben az űrlapon:

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*\@*controller_public_IP*

Ez a parancs a fürtvezérlő IP-címén keresztül kapcsolódik a fürt felügyeleti IP-címéhez.

Példa:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

A hitelesítés automatikus, ha az SSH nyilvános kulcsát használta a fürt létrehozásához, és a megfelelő kulcs telepítve van az ügyfélrendszeren. Ha jelszót használt, a rendszer kérni fogja, hogy adja meg.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Csatlakozás az Avere vezérlőpulthoz böngészőben

Ez a lépés egy webböngészőt használ a vFXT-fürt konfigurációs segédprogramjához való csatlakozáshoz.

* SSH-alagútkapcsolat esetén nyissa meg a `https://127.0.0.1:8443`webböngészőt, és keresse meg a lehetőséget.

  Az alagút létrehozásakor csatlakozott a fürt IP-címéhez, ezért csak a helyi állomás IP-címét kell használnia a böngészőben. Ha nem a 8443-as helyi portot használta, használja inkább a portszámát.

* Ha VPN-t vagy ExpressRoute-ot használ a fürt eléréséhez, keresse meg a fürtfelügyeleti IP-címet a böngészőben. Például: ``https://203.0.113.51``

A böngészőtől függően előfordulhat, hogy a Speciális gombra kell **kattintania,** és ellenőriznie kell, hogy biztonságos-e a lapra lépni.

Adja meg `admin` a fürt létrehozásakor megadott felhasználónevet és rendszergazdai jelszót.

![Képernyőkép az Avere bejelentkezési oldalról, amelyen az "admin" felhasználónév és egy jelszó található.](media/avere-vfxt-gui-login.png)

Kattintson **a Bejelentkezés gombra,** vagy nyomja meg az Enter billentyűt a billentyűzeten.

## <a name="next-steps"></a>További lépések

Miután bejelentkezett a fürt vezérlőpultjára, engedélyezze a [támogatást.](avere-vfxt-enable-support.md)
