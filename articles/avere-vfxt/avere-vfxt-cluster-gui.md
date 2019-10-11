---
title: A avere vFXT-Vezérlőpult elérése – Azure
description: A vFXT-fürthöz és a böngészőalapú avere-vezérlőpulthoz való kapcsolódás a avere-vFXT konfigurálásához
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rohogue
ms.openlocfilehash: 098ed98c1680fa2ea38c377e9e34719ba778b175
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255035"
---
# <a name="access-the-vfxt-cluster"></a>A vFXT-fürt elérése

A beállítások módosításához és a avere vFXT-fürt figyeléséhez használja a avere vezérlőpultot. A avere Vezérlőpult egy böngészőalapú grafikus felület a fürthöz.

Mivel a vFXT-fürt egy privát virtuális hálózaton belül helyezkedik el, létre kell hoznia egy SSH-alagutat, vagy másik módszerrel kell elérnie a fürt felügyeleti IP-címét. Két alapvető lépés: 

1. Kapcsolat létrehozása a munkaállomás és a privát vnet között 
1. A fürt Vezérlőpultjának betöltése böngészőben 

> [!NOTE] 
> Ez a cikk azt feltételezi, hogy nyilvános IP-címet állított be a fürt vezérlőjén vagy egy másik virtuális GÉPEN a fürt virtuális hálózatán belül. Ez a cikk azt ismerteti, hogyan használható a virtuális gép gazdagépként a fürt eléréséhez. Ha VPN-vagy ExpressRoute használ a vnet eléréséhez, ugorjon a [Csatlakozás a avere vezérlőpulthoz](#connect-to-the-avere-control-panel-in-a-browser)lehetőségre.

A csatlakozás előtt győződjön meg arról, hogy a fürt létrehozásakor használt nyilvános vagy titkos SSH-kulcspár telepítve van a helyi gépen. Ha segítségre van szüksége, olvassa el az SSH-kulcsok [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) rendszerhez készült dokumentációját. (Ha nyilvános kulcs helyett jelszót használt, a rendszer kérni fogja, hogy a csatlakozáskor megírja a következőt:.) 

## <a name="create-an-ssh-tunnel"></a>SSH-alagút létrehozása 

Létrehozhat egy SSH-alagutat egy Linux-alapú vagy Windows 10 rendszerű ügyféloldali parancssorból. 

SSH-bújtatási parancs használata az alábbi űrlappal: 

SSH-L *local_port*:*cluster_mgmt_ip*: 443 *controller_username*\@*controller_public_IP*

Ez a parancs a fürt felügyeleti IP-címéhez csatlakozik a tartományvezérlő IP-címén keresztül.

Példa:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

A hitelesítés automatikusan történik, ha az SSH nyilvános kulcsával létrehozta a fürtöt, és a megfelelő kulcs telepítve van az ügyfél rendszerére. Ha jelszót használt, a rendszer kérni fogja, hogy adja meg.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Kapcsolódás a avere vezérlőpulthoz egy böngészőben

Ez a lépés egy webböngészőt használ a vFXT-fürtön futó konfigurációs segédprogramhoz való kapcsolódáshoz.

* SSH-bújtatási kapcsolat esetén nyissa meg a webböngészőt, és navigáljon `https://127.0.0.1:8443` értékre. 

  Az alagút létrehozásakor kapcsolódott a fürt IP-címéhez, így csak a localhost IP-címet kell használnia a böngészőben. Ha a 8443-től eltérő helyi portot használt, használja helyette a portszámot.

* Ha VPN-vagy ExpressRoute-t használ a fürt eléréséhez, navigáljon a fürt felügyeleti IP-címéhez a böngészőben. Például: ``https://203.0.113.51``

A böngészőtől függően előfordulhat, hogy a **speciális** elemre kell kattintania, és meg kell győződnie arról, hogy a lap továbbra is biztonságos.

Adja meg `admin` felhasználónevet és a fürt létrehozásakor megadott rendszergazdai jelszót.

![Képernyőkép a avere bejelentkezési oldaláról, amely a Felhasználónév "admin'" és a "password" névvel van feltöltve.](media/avere-vfxt-gui-login.png)

Kattintson a **Bejelentkezés** gombra, vagy nyomja le az ENTER billentyűt a billentyűzeten.

## <a name="next-steps"></a>Következő lépések

Most, hogy el tudja érni a fürtöt, engedélyezze a [támogatást](avere-vfxt-enable-support.md).
