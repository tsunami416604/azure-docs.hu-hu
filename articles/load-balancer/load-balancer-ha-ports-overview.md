---
title: Magas rendelkezésre állású portok – áttekintés az Azure-ban |} Microsoft Docs
description: További információk a magas rendelkezésre állású portok terheléselosztási egy belső terheléselosztón.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: kumud
ms.openlocfilehash: 53e09498324f802ce1839d262999657d37ee973b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32169118"
---
# <a name="high-availability-ports-overview"></a>Magas rendelkezésre állású portok áttekintése

Az Azure szabványos terheléselosztó segít összes porton TCP és UDP-forgalom terheléselosztásához egyidejűleg amikor belső terheléselosztót használja. 

Egy magas rendelkezésre ÁLLÁS portok szabály variant terheléselosztási szabályok, egy belső szabványos terheléselosztó konfigurálva. Egy belső szabványos terheléselosztó az összes porton érkező összes TCP és UDP-forgalom terheléselosztásához egyetlen szabály megadásával leegyszerűsítheti a terheléselosztó használatát. A terheléselosztás döntés ne. Ez a művelet a következő 5 rekordos kapcsolat alapul: forrás IP-cím, forrásport, cél IP-címe, célport és protokoll.

A magas rendelkezésre ÁLLÁSÚ portok funkció segítségével kritikus forgatókönyvek, például a magas rendelkezésre állás és a hálózati virtuális készülékek (NVAs) belső virtuális hálózatok számára is méretezhető. A szolgáltatás is hozzájárulhat a nagy számú portok terhelésű kell alapulnia. 

A magas rendelkezésre ÁLLÁSÚ portok szolgáltatás van konfigurálva, amikor az előtér- és portok **0** és a protokollt, hogy **összes**. A belső terheléselosztó erőforrást majd összes TCP és UDP-adatfolyamok, függetlenül attól, portszám egyensúlyba kerüljön.

## <a name="why-use-ha-ports"></a>Magas rendelkezésre ÁLLÁSÚ portok miért érdemes használni?

### <a name="nva"></a>Virtuális hálózati készülékek

Segítségével NVAs biztonságossá az Azure számítási a biztonsági fenyegetések többféle típusú. NVAs használatakor a következő használati helyzetekben megbízható és magas rendelkezésre állású kell lenniük, és azok kell horizontális felskálázás igény.

Ezen célok egyszerűen hozzáadásával NVA példányok a háttér-készletnek a belső terheléselosztó érhet el, és a terheléselosztó szabály konfigurálása egy magas rendelkezésre ÁLLÁSÚ portokat.

NVA magas rendelkezésre ÁLLÁSÚ forgatókönyvek esetén magas rendelkezésre ÁLLÁSÚ portok a következő előnyöket nyújtják:
- Gyors megfelelő példányok biztosítanak járó példányonkénti állapotfigyelő mintavételt
- Győződjön meg arról nagyobb teljesítményt és a kibővített az *n*-aktív példányok
- Adjon meg *n*-aktív és aktív-passzív forgatókönyvek
- Szükségtelenné teszik a bonyolult megoldások, például a figyelés készülékek Apache ZooKeeper csomópontok

Az alábbi ábra mutatja be a virtuális hálózati hub és küllős központi telepítés. A küllők kényszerített bújtatás az adatforgalmat, a központ virtuális hálózat és az NVA, mielőtt elhagynák a megbízható terület keresztül. A NVAs portok magas rendelkezésre ÁLLÁSÚ konfigurációval egy belső szabványos terheléselosztó mögött van. Az összes forgalom feldolgozni, és ennek megfelelően továbbítani.

![Hub és küllős rendelkező virtuális hálózatban, a magas rendelkezésre ÁLLÁSÚ módban rendszerbe NVAs ábrája](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Ha NVAs használ, győződjön meg arról a szolgáltatókkal legjobban a magas rendelkezésre ÁLLÁSÚ portok használatára és ismerje meg, melyik forgatókönyvek is támogatottak.

### <a name="load-balancing-large-numbers-of-ports"></a>Terheléselosztás nagyszámú portok

Magas rendelkezésre ÁLLÁSÚ portok is használ, a nagy számú portok terheléselosztást igénylő alkalmazásokat. Egy belső használatával leegyszerűsítheti a forgatókönyvekben [szabványos terheléselosztó](load-balancer-standard-overview.md) magas rendelkezésre ÁLLÁSÚ porttal. Terheléselosztás egyetlen szabály több egyedi terheléselosztási szabályok, egy, az egyes portok váltja fel.

## <a name="region-availability"></a>Régiónkénti elérhetőség

A magas rendelkezésre ÁLLÁSÚ portok funkció minden globális Azure területen érhető el.

## <a name="supported-configurations"></a>Támogatott konfigurációk

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Egy egyetlen, a nem fix IP-(nem - közvetlen kiszolgálói válasz) magas rendelkezésre ÁLLÁSÚ-portok konfigurációs egy szabványos belső terheléselosztón

Ez a konfiguráció nincs magas rendelkezésre ÁLLÁSÚ portok alapkonfigurációt. Egy magas rendelkezésre ÁLLÁSÚ konfigurálható portok terheléselosztás-szabályok egyetlen előtér-IP-címnek a következő tevékenységek végrehajtásával:
1. Standard terheléselosztó beállításakor válassza ki a **magas rendelkezésre ÁLLÁSÚ portok** a terheléselosztó-konfiguráció jelölőnégyzetet.
2. A **fix IP-Címek**, jelölje be **letiltott**.

Ez a konfiguráció nem teszi lehetővé más terheléselosztási szabály beállításokat az aktuális erőforrás-terhelés terheléselosztó. Azt is lehetővé teszi, hogy nincs más belső terheléselosztó erőforrás-konfiguráció a háttér-példányok adott halmazát.

Azonban a magas rendelkezésre ÁLLÁSÚ portok szabály mellett a háttér-példányok nyilvános szabványos terheléselosztó konfigurálhatja.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Egy egyetlen, a lebegőpontos NAN (közvetlen kiszolgálói válasz) IP-magas rendelkezésre ÁLLÁSÚ-portok konfigurációs egy szabványos belső terheléselosztón

Hasonló módon konfigurálhatja a terheléselosztó terheléselosztási szabály használandó **magas rendelkezésre ÁLLÁSÚ Port** egyetlen előtér úgy, hogy az a **fix IP-Címek** való **engedélyezve**. 

Ez a konfiguráció segítségével adhat hozzá további fix IP terheléselosztási szabályok és/vagy egy nyilvános terheléselosztó. Azonban nem használható egy nem fix IP-cím, magas rendelkezésre ÁLLÁSÚ-portok terheléselosztás konfigurációs fölött ezt a konfigurációt.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Egy szabványos belső terheléselosztón több magas rendelkezésre ÁLLÁSÚ-portok konfiguráció

Ha kell, hogy konfigurálja-e egynél több magas rendelkezésre ÁLLÁSÚ port kezelőfelülete a háttér-készlethez, a következőket teheti: 
- Egynél több előtér-privát IP-cím konfigurálása egy adott belső szabványos terheléselosztó erőforrás.
- Több terheléselosztási szabályok konfigurálása, ahol minden egyes szabály egy egyetlen egyedi előtér-IP-címmel rendelkezik kiválasztott.
- Válassza ki a **magas rendelkezésre ÁLLÁSÚ portok** lehetőséget, és utána állítsa be **fix IP-Címek** való **engedélyezve** a terheléselosztási szabályok.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>A magas rendelkezésre ÁLLÁSÚ és egy nyilvános terheléselosztó a háttér-példányt a belső terheléselosztók

Konfigurálható *egy* a háttér-erőforrások egyetlen belső szabványos terheléselosztó magas rendelkezésre ÁLLÁSÚ porttal rendelkező nyilvános szabványos terheléselosztó erőforrást.

>[!NOTE]
>Ez a funkció jelenleg elérhető Azure Resource Manager-sablonok, de az Azure-portál nem érhető el.

## <a name="limitations"></a>Korlátozások

- Magas rendelkezésre ÁLLÁSÚ portok beállítási lehetőségek érhetők el csak belső terheléselosztók. Nincs elérhető nyilvános terheléselosztókhoz.

- Egy magas rendelkezésre ÁLLÁSÚ egyesítésére portok terheléselosztási szabályt és egy nem magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabály nem támogatott.

- A magas rendelkezésre ÁLLÁSÚ portok funkció nem érhető el az IPv6.

- Csak egyetlen hálózati adapter folyamata szimmetria NVA forgatókönyvek esetén támogatott. Tekintse meg a leírás és diagram a [virtuális készülékekre](#nva). Azonban ha a cél a forgatókönyv NAT is képes működni, segítségével, győződjön meg arról, hogy a belső terheléselosztó elküldi a forgalom az azonos NVA.


## <a name="next-steps"></a>További lépések

- [Egy szabványos belső terheléselosztón magas rendelkezésre ÁLLÁSÚ portok konfigurálása](load-balancer-configure-ha-ports.md)
- [Standard terheléselosztó megismerése](load-balancer-standard-overview.md)
