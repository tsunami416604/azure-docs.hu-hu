---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 02/24/2020
ms.topic: include
ms.openlocfilehash: c77849b2285283a34e6adf84dc3845a4076407af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597940"
---
## <a name="attack-scenario"></a>Támadási forgatókönyv

Brute force támadások gyakran cél felügyeleti portok, mint egy eszköz, hogy hozzáférjenek a virtuális gép. Ha sikeres, a támadó átveheti az irányítást a virtuális gép felett, és megvetheti a lábát a környezetben.

A találgatásos támadásnak való kitettség csökkentésének egyik módja a port nyitva töltött idejének korlátozása. A felügyeleti portoknak nem kell mindig nyitva lenniük. Csak akkor kell nyitva lennie, amíg csatlakozik a virtuális géphez, például felügyeleti vagy karbantartási feladatok elvégzéséhez. Ha a just-in-time engedélyezve van, a Security Center [hálózati biztonsági csoport](../articles/virtual-network/security-overview.md#security-rules) (NSG) és az Azure Firewall-szabályokat használ, amelyek korlátozzák a felügyeleti portokhoz való hozzáférést, így a támadók nem célozhatnak meg.

![Just-in-time forgatókönyv](../articles/security-center/media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Hogyan működik a jit-hozzáférés?

Ha a just-in-time engedélyezve van, a Security Center lefagy a bejövő forgalmat az Azure-beli virtuális gépek egy NSG-szabály létrehozásával. Válassza ki a portokat a virtuális gép, amelyhez a bejövő forgalom lesz zárolva. Ezeket a portokat a just-in-time megoldás vezérli.

Amikor egy felhasználó hozzáférést kér egy virtuális géphez, a Security Center ellenőrzi, hogy a felhasználó [rendelkezik-e szerepköralapú hozzáférés-vezérlési (RBAC)](../articles/role-based-access-control/role-assignments-portal.md) engedélyekkel az adott virtuális géphez. Ha a kérelmet jóváhagyják, a Security Center automatikusan konfigurálja a hálózati biztonsági csoportok (NSG- k) és az Azure tűzfal, hogy a bejövő forgalmat a kiválasztott portok és a kért forrás IP-címek vagy tartományok, a megadott ideig. Az idő lejárta után a Security Center visszaállítja az NSG-ket a korábbi állapotokra. A már létrehozott kapcsolatokat azonban nem szakítják meg.

 > [!NOTE]
 > Ha egy JIT hozzáférési kérelem jóváhagyott egy virtuális gép mögött egy Azure-tűzfal, majd a Security Center automatikusan módosítja az NSG és a tűzfal házirend-szabályokat. A megadott ideig a szabályok engedélyezik a bejövő forgalmat a kiválasztott portok és a kért forrás IP-címek vagy tartományok. Az idő eltelte után a Security Center visszaállítja a tűzfal és az NSG-szabályok korábbi állapotok.


## <a name="permissions-needed-to-configure-and-use-jit"></a>A jit konfigurálásához és használatához szükséges engedélyek

| A felhasználó engedélyezése: | A beállítási engedélyek|
| --- | --- |
| JIT-házirend konfigurálása vagy szerkesztése virtuális géphez | *Rendelje hozzá ezeket a műveleteket a szerepkörhöz:*  <ul><li>A virtuális géphez társított előfizetés vagy erőforráscsoport hatókörén:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> A virtuális gép előfizetésének vagy erőforráscsoportjának hatókörén: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|JIT-hozzáférés kérése virtuális géphez | *Rendelje hozzá ezeket a műveleteket a felhasználóhoz:*  <ul><li>A virtuális géphez társított előfizetés vagy erőforráscsoport hatókörén:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>A virtuális géphez társított előfizetés vagy erőforráscsoport hatókörén:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  Előfizetés vagy erőforráscsoport vagy virtuális gép hatókörén:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  Előfizetés vagy erőforráscsoport vagy virtuális gép hatókörén:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|