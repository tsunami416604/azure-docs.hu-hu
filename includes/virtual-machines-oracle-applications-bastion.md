---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361543"
---
### <a name="bastion-tier"></a>Bástyaszint

A megerősített állomás egy választható összetevő, amely et ugrókiszolgálóként használhatja az alkalmazás és az adatbázispéldányok eléréséhez. A megerősített állomás virtuális gép rendelkezhet egy nyilvános IP-címet hozzárendelve, bár a javaslat az, hogy hozzon létre egy ExpressRoute-kapcsolat vagy a helyek közötti VPN a helyszíni hálózat biztonságos hozzáférést. Ezenkívül csak SSH (22-es, Linux-port) vagy RDP (3389-es, Windows Server-port) kell megnyitni a bejövő forgalmat. Magas rendelkezésre állás érdekében telepítsen egy megerősített gazdatestet két rendelkezésre állási zónában vagy egyetlen rendelkezésre állási csoportban.

Az SSH-ügynök továbbítása a virtuális gépeken is engedélyezheti, amely lehetővé teszi a virtuális hálózat más virtuális gépeinek elérését a megerősített gazdagép hitelesítő adatainak továbbításával. Vagy használja az SSH bújtatás más példányok eléréséhez.

Íme egy példa az ügynöktovábbításra:

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

Ez a parancs csatlakozik a bástyához, majd azonnal újra fut, `ssh` így kap egy terminált a célpéldányon. Előfordulhat, hogy a célpéldányon kívül nem root felhasználót kell megadnia, ha a fürt másképp van konfigurálva. Az `-A` argumentum továbbítja az ügynökkapcsolatot, így a helyi számítógépen lévő személyes kulcs automatikusan használható. Vegye figyelembe, hogy az ügynöktovábbítás `ssh` egy lánc, így a második parancs is tartalmazza, `-A` hogy a célpéldányról kezdeményezett későbbi SSH-kapcsolatok is a helyi titkos kulcsot használják.