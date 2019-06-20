---
title: Az Azure megerősített |} A Microsoft Docs
description: További tudnivalók az Azure megerősített
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 06/17/2019
ms.author: cherylmc
ms.openlocfilehash: cfd68bbacf4cf8171efdba7878ec8c06055a4997
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191167"
---
# <a name="what-is-azure-bastion-preview"></a>Mi az Azure megerősített? (Előzetes verzió)

Az Azure megerősített szolgáltatás egy új teljes platform felügyelt PaaS-szolgáltatás, amely üzembe helyezi a virtuális hálózaton belül. Biztonságos és zökkenőmentes közvetlenül az Azure Portalon a virtuális gépekhez RDP/SSH-kapcsolatot biztosít SSL-en keresztül. Ha az Azure Bastionon keresztül csatlakozik, a virtuális gépeinek nincs szüksége nyilvános IP-címre.

 Megerősített biztonságos RDP és SSH-kapcsolatot biztosít, amelyben üzembe a virtuális hálózaton lévő összes virtuális gép. A virtuális gépek Azure megerősített használatával védi az adatokhoz hozzáférést biztosító külvilág RDP/SSH-portokat, miközben ugyanúgy biztosít az RDP/SSH-val biztonságos hozzáférést. A Azure megerősített akkor csatlakozhat a virtuális gép közvetlenül az Azure Portalról. Nem kell egy további ügyfél, az ügynök vagy szoftveres.

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="architecture"></a>Architektúra

Az Azure megerősített van telepítve, a virtuális hálózat és, telepítés után, a virtuális hálózat a virtuális gépek a biztonságos RDP/SSH-élményt biztosít. Miután a virtuális hálózaton üzembe helyez egy Azure megerősített szolgáltatás, az RDP/SSH-élmény ugyanazon a virtuális hálózaton lévő összes virtuális gép számára érhető el. Az üzembe helyezés nem száma virtuális hálózatonként nem / előfizetés/fiók vagy a virtuális gép.

Az RDP és SSH néhány az alapvető eszközökkel, amelyen keresztül csatlakozhat az Azure-ban futó munkaterheléseket. RDP/SSH portjainak elérhetővé tétele az interneten keresztül nem kívánt és a egy jelentős threat surface tekintendő. Ezt gyakran okozza rései. A veszélyforrások elleni surface tartalmaz, telepítheti a megerősített hosts (más néven a jump-kiszolgálók) nyilvános oldalán a szegélyhálózaton található. Megerősített gazdakiszolgálók kialakítása és konfigurációja ellenállni a támadásoknak. Megerősített kiszolgálók is biztosít a számítási feladatok a megerősített mögött található RDP és SSH-kapcsolatot, valamint a további a hálózaton belül.

![architektúra](./media/bastion-overview/architecture.png)

Ez az ábra az Azure megerősített központi architektúráját mutatja be. Az alábbi ábrán látható:

* A virtuális hálózatban van üzembe helyezve a bástyagazdagép.
* A felhasználó csatlakozik az Azure Portalon HTML5 böngészőkben használatával.
* A felhasználó kijelöli a virtuális gép csatlakozni.
* Egyetlen kattintással az RDP/SSH-munkamenetet a böngészőben nyílik meg.
* Az Azure virtuális gépen nincs nyilvános IP-cím szükséges.

## <a name="key-features"></a>A legfontosabb jellemzők

A következő funkciókat szeretné kipróbálni a nyilvános előzetes verzióban érhetők el:

* **Az RDP és SSH közvetlenül az Azure Portalon:** Közvetlenül kérheti le az RDP és SSH-munkamenethez közvetlenül az Azure Portalon egyetlen kattintással zökkenőmentes élményt használatával.
* **Távoli munkamenet az RDP/SSH-hoz az SSL- és tűzfalbeállítások bejárása során:** Azure megerősített egy HTML5-alapú webes ügyfél, amely automatikusan adatfolyamként történő a helyi eszközt használ, annak érdekében, hogy az RDP/SSH-munkamenet SSL-en keresztül, hogy biztonságosan a vállalati tűzfal bejárása lehetővé teszi a 443-as porton.
* **Nincs nyilvános IP-cím szükséges az Azure virtuális gépen:** Az Azure megerősített megnyílik a RDP/SSH-kapcsolatot az Azure virtuális gép magánhálózati IP használata a virtuális Gépen. A virtuális gép nyilvános IP-cím nem szükséges.
* **Nincsenek nem kell az NSG-k kezelése:** Az Azure megerősített egy teljes körűen felügyelt platform, amely belsőleg RDP/SSH-kapcsolat védelmének biztosításához a megerősített Azure PaaS-szolgáltatás. Nem kell minden olyan NSG-ket alkalmaz Azure megerősített alhálózaton. Mivel Azure megerősített a virtuális gépek magánhálózati IP-kapcsolatot létesít, konfigurálhatja úgy az NSG-ket csak az Azure megerősített érkező RDP/SSH engedélyezése. Ez eltávolítja az NSG-k minden alkalommal, amikor szüksége biztonságosan csatlakozhat a virtuális gépek felügyelete nem kell.
* **Védelem ismételt portok:** Nem kell tegye elérhetővé a virtuális gépek nyilvános internetről, mert a virtuális gép védelme portfigyelés engedélyezetlen és a virtuális hálózaton kívül található, a rosszindulatú felhasználókkal szemben.
* **Nulladik napi támadások ellen. Csak egy helyen megerősítése:** Az Azure megerősített egy teljes körűen platform felügyelt PaaS-szolgáltatás. A virtuális hálózat peremén lévő helyezkedik el, mert nem kell aggódnia a virtuális hálózaton lévő virtuális gépek mindegyike korlátozására. Az Azure platform nulladik napi támadások ellen védi a tartja, az Azure megerősített megerősített és mindig naprakész az Ön számára.

## <a name="faq"></a>GYIK

[!INCLUDE [Bastion FAQ](../../includes/bastion-faq-include.md)]

## <a name="next-steps"></a>További lépések

* [Hozzon létre egy Azure megerősített gazdagépi erőforrás](bastion-create-host-portal.md).
* Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.