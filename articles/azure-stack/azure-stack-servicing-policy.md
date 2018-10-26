---
title: Az Azure Stack karbantartási szabályzat |} A Microsoft Docs
description: További információ az Azure Stack karbantartási szabályzat, és hogyan kell fenntartani egy integrált rendszer támogatott állapotban.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2018
ms.author: sethm
ms.reviewer: harik
ms.openlocfilehash: f00b175fed36fbe2b28f1d11843bb17c54ac3873
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086964"
---
# <a name="azure-stack-servicing-policy"></a>Az Azure Stack karbantartási szabályzat
Ez a cikk ismerteti a karbantartási szabályzat az Azure Stack integrált rendszerek, és hogy mit kell tennie, hogy a rendszer egy támogatott állapotban. 

## <a name="update-package-types"></a>A csomagok frissítése

Integrált rendszerek frissítési csomagokat két típusa van: 

- **A Microsoft szoftverfrissítések**. A Microsoft a Microsoft update szoftvercsomagok életciklusának teljes körű karbantartási feladata. Ezeket a csomagokat a Windows Server legújabb biztonsági frissítéseket, nem biztonsági frissítés és az Azure Stack funkciófrissítések tartalmazhatnak. Letöltheti a következő csomagok közvetlenül a Microsofttól.

- **OEM hardvert gyártó által biztosított frissítések**. Az Azure Stack a hardvergyártó partnerektől felelős a végpontok közötti karbantartási életciklusának (útmutatást is beleértve) a hardverrel kapcsolatos belső vezérlőprogram és illesztőprogram-csomagok frissítése. Emellett az Azure Stack a hardvergyártó partnerektől tulajdonosa, és útmutatást az összes szoftver- és a hardver életciklus gazdagép karbantartása. A számítógép-Gyártói szállítójával futtatja ezeket a csomagokat a saját letöltési hely frissítése.


## <a name="update-package-release-cadence"></a>Frissítési csomag kiadási ütem
A Microsoft vár a szoftverfrissítési csomagok havi kiadása ütemben történik felszabadítása érdekében. Viszont azt, lehetséges, hogy több, vagy nincs kiadásai az adott hónapban. OEM hardverszállítók a frissítéseket egy igény szerint történik. 

Megtervezheti és kezelheti a frissítéseket, és miként állapítható meg, a jelenlegi verziót dokumentáció [kezelés frissítések – áttekintés](azure-stack-updates.md). 

Egy adott frissítés, és töltheti le, beleértve kapcsolatos információkat lásd: kibocsátási megjegyzései, amely frissítése: 
- [Azure Stack 1809 frissítése](azure-stack-update-1809.md)
- [Azure Stack 1808 frissítése](azure-stack-update-1808.md)
- [Azure Stack 1807 frissítése](azure-stack-update-1807.md)

## <a name="hotfixes"></a>Gyorsjavítások
Néha előfordul, a Microsoft kínál gyorsjavítások Azure Stack egy adott probléma, amely gyakran megelőző vagy időérzékeny címmel.  A megfelelő Microsoft Tudásbázis megfelelő cikkében, amely leírja a problémát, a kiváltó okkal és a megoldás, az egyes gyorsjavítások szabadul fel. 

Gyorsjavítások letölthető és telepíthető csakúgy, mint a rendszeres teljes frissítési csomagokat az Azure Stackhez. Azonban ellentétben a teljes frissítés, gyorsjavítások perc is telepíthető. Azt javasoljuk, hogy az Azure Stack-operátorok gyorsjavítások telepítésekor állítson be karbantartási ablakokat. Gyorsjavítások frissítése az Azure Stack-felhőben, azt könnyen megállapíthatja, hogy ha a gyorsjavítás telepítve van-e. Azure Stack támogatása továbbra is szereplő minden egyes verziója külön gyorsjavítás biztosítunk. Minden egyes javítása egy adott iteráció összesített, és ugyanazt a verziót a korábbi frissítéseit tartalmazza. Tudjon meg többet a egy Tudásbázis megfelelő javításokat egy adott gyorsjavítást alkalmazhatóságát kapcsolatos cikket.  


## <a name="keep-your-system-under-support"></a>A rendszer a támogatás
A folytatáshoz szeretne támogatást kapni az Azure Stack üzemelő példányához kell tartania, aktuális. A frissítések késleltetésének házirend: támogatási megőrzési ideje az Azure Stack üzemelő frissítés nemrég kiadott verzióját futtató vagy a két előző frissítési verziók valamelyikével kell azt. Gyorsjavítások nem veszi figyelembe a nagyobb frissítési verziókat. Ha az Azure Stack-felhő mögött *több mint két frissítések*, azt a megfelelő számít, és frissítenie kell a legalább a minimális támogatott verziót szeretne támogatást kapni. 

Például ha a rendelkezésre álló legfrissebb frissített verziót 1805, és az előző két frissítési csomagokat is 1804 és 1803 verziója, 1803 és 1804 is maradnak támogatása. Azonban az 1802, nem támogatott. A szabályzat igaznak, ha egy hónapban, vagy két nincs kiadási. Például a jelenlegi kiadásban 1805, és nincs 1804 kiadás volt, az előző két frissítési csomagokat 1803 és 1802 továbbra is támogatott.

A szoftverfrissítési csomagokat a Microsoft nem összesítő és a korábbi frissítési csomag előfeltételként szükséges. Ha úgy dönt, hogy egy vagy több frissítések késleltetése, fontolja meg a teljes runtime, ha szeretne kapni a legújabb verzióra. 

## <a name="get-support"></a>Támogatás kérése
Az Azure Stack az Azure támogatási folyamatot követi. Nagyvállalati ügyfeleink is kövesse az ismertetett folyamatot [létrehozása egy Azure-támogatáskérést](/azure/azure-supportability/how-to-create-azure-support-request). Ha egy ügyfél egy Cloud Service Provider (CSP), forduljon a CSP.  További információkért lásd: a [Azure támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/). 


## <a name="next-steps"></a>További lépések

- [Az Azure Stack frissítéseinek kezelése](azure-stack-updates.md)


