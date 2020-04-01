---
title: Azure Bástya | Microsoft dokumentumok
description: További információ az Azure Bastionról
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 01/31/2020
ms.author: cherylmc
ms.openlocfilehash: 299a69675eed1ba958c6d13cf447407450df2abb
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411102"
---
# <a name="what-is-azure-bastion"></a>Mi az az Azure Bastion?

Az Azure Bastion szolgáltatás egy új, teljesen platform által felügyelt PaaS-szolgáltatás, amelyet a virtuális hálózaton belül biztosít. Biztonságos és zökkenőmentes RDP/SSH-kapcsolatot biztosít a virtuális gépekhez közvetlenül az Azure Portalon a TLS-en keresztül. Ha az Azure Bastionon keresztül csatlakozik, a virtuális gépeinek nincs szüksége nyilvános IP-címre.

A Bástya biztonságos RDP- és SSH-kapcsolatot biztosít a virtuális hálózat összes virtuális gépéhez, amelyben ki van építve. Az Azure Bastion használatával megvédi a virtuális gépeket az RDP/SSH portok külvilág számára való kiteszik, miközben továbbra is biztonságos hozzáférést biztosít az RDP/SSH használatával. Az Azure Bastion segítségével közvetlenül az Azure Portalról csatlakozhat a virtuális géphez. Nincs szüksége további ügyfélre, ügynökre vagy szoftverre.

## <a name="architecture"></a>Architektúra

Az Azure Bastion központi telepítése virtuális hálózatonként, nem előfizetésenként/fiókonként vagy virtuális gépenként. Miután üzembe létesítegy Azure-bastion szolgáltatást a virtuális hálózatban, az RDP/SSH élmény érhető el az összes virtuális gép ugyanabban a virtuális hálózatban.

RdP és SSH néhány alapvető eszköz, amelyen keresztül csatlakozhat az Azure-ban futó számítási feladatokhoz. RdP/SSH portok az interneten keresztül nem kívánatos, és tekintik jelentős fenyegetés felületén. Ez gyakran a protokoll biztonsági rései miatt történt. A fenyegetésfelület megfékezéséhez a szegélyhálózat nyilvános oldalán telepítheti a megerősített állomásokat (más néven ugrókiszolgálókat). A megerősített gazdakiszolgálók at úgy tervezték és konfigurálták, hogy ellenálljanak a támadásoknak. A megerősített kiszolgálók RDP és SSH kapcsolatot is biztosítanak a bástya mögött ülő munkaterhelésekhez, valamint a hálózaton belüli további feladatokhoz.

![architektúra](./media/bastion-overview/architecture.png)

Ez az ábra egy Azure-bastion-telepítés architektúráját mutatja be. Ezen az ábrán:

* A megerősített állomás telepítve van a virtuális hálózatban.
* A felhasználó bármely HTML5-böngészővel csatlakozik az Azure Portalhoz.
* A felhasználó kiválasztja azt a virtuális gépet, amelyhez csatlakozni szeretne.
* Egyetlen kattintással megnyílik az RDP/SSH munkamenet a böngészőben.
* Nincs szükség nyilvános IP-cím az Azure virtuális gép.

## <a name="key-features"></a>A legfontosabb jellemzők

A következő funkciók érhetők el:

* **RDP és SSH közvetlenül az Azure portalon:** Közvetlenül is eljuthat az RDP- és SSH-munkamenethez közvetlenül az Azure Portalon egy kattintásnélküli élmény használatával.
* **Távoli munkamenet TLS-en keresztül és tűzfalbejárás RDP/SSH esetén:** Az Azure Bastion egy HTML5-alapú webes klienst használ, amely automatikusan streamelésre kerül a helyi eszközre, így az RDP/SSH munkamenet a 443-as porton lévő TLS-en keresztül érhető el, amely lehetővé teszi a vállalati tűzfalak biztonságos áthaladását.
* **Nincs szükség nyilvános IP-címre az Azure virtuális gépen:** Az Azure Bastion megnyitja az RDP/SSH-kapcsolatot az Azure virtuális gépére a virtuális gép privát IP-címének használatával. Nincs szükség nyilvános IP-címre a virtuális gépen.
* **Nem gond az NSG-k kezelésével:** Az Azure Bastion egy teljes körűen felügyelt platform PaaS-szolgáltatás az Azure-ból, amely belsőleg megerősített, hogy biztonságos RDP/SSH-kapcsolatot biztosítson. Nem kell nsg-ket alkalmaznia az Azure Bastion alhálózaton. Mivel az Azure Bastion privát IP-címén keresztül csatlakozik a virtuális gépekhez, konfigurálhatja az NSG-ket úgy, hogy csak az Azure Bastion RDP/SSH-t engedélyezze. Ez eltávolítja az NSG-k kezelésével kapcsolatos minden olyan alkalommal, amikor biztonságosan kell csatlakoznia a virtuális gépekhez.
* **Védelem a portbeolvasás ellen:** Mivel nem kell a virtuális gépeket nyilvános internetnek kitenni, a virtuális gépek et a virtuális hálózaton kívül található rosszindulatú és rosszindulatú felhasználók védik a portok általi vizsgálatellen.
* **Védelem a nulladik napi támadások ellen. Edzés csak egy helyen:** Az Azure Bastion egy teljesen platform által felügyelt PaaS-szolgáltatás. Mivel a virtuális hálózat peremén helyezkedik el, nem kell aggódnia a virtuális hálózat egyes virtuális gépeinek megkeményedése miatt. Az Azure platform védelmet nyújt a nulladik napi biztonsági rések ellen azáltal, hogy az Azure Bastion-t megkeményítve és mindig naprakészen tartja.

## <a name="faq"></a>GYIK

[!INCLUDE [Bastion FAQ](../../includes/bastion-faq-include.md)]

## <a name="next-steps"></a>További lépések

* [Hozzon létre egy Azure Bastion gazdaállomás-erőforrást.](bastion-create-host-portal.md)
* Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.
