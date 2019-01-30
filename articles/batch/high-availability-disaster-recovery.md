---
title: Magas rendelkezésre állás és vészhelyreállítás helyreállítási – Azure Batch |} A Microsoft Docs
description: Ismerje meg, hogyan tervezhet a Batch-alkalmazás számára regionális kimaradás
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2019
ms.author: lahugh
ms.openlocfilehash: b863785575263fedd144b3d599962a8e1559e0a3
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55252383"
---
# <a name="design-your-application-for-high-availability"></a>Magas rendelkezésre állású alkalmazás tervezése

Az Azure Batch szolgáltatás egy regionális szolgáltatás. A Batch minden Azure-régióban érhető el, de egy Batch-fiók létrehozása után hozzá kell rendelnie az egy régióban. Ezután alkalmazza a Batch-fiók összes művelet adott régióban. Ha például készletek és a hozzájuk tartozó virtuális gépeket (VM) jönnek létre a Batch-fiók ugyanabban a régióban.

Batch használó alkalmazások tervezésekor figyelembe kell vennie a Batch nem érhetők az egy régióban lehetőségét. Lehetséges, olyan ritka helyzet állhat elő, ahol a régió teljes problémája van, az egész köteget szolgáltatást a régiót, vagy probléma a megadott Batch-fiók.

Ha az alkalmazás vagy megoldás használata Batch-mindig elérhetőnek kell lennie, akkor azt egy másik régióba történő feladatátvételt kell megtervezni, vagy mindig van elosztva a két vagy több régióban a számítási. Mindkét módszerénél legalább két Batch-fiókok, minden más régióban található fiók szükséges.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Több Batch-fiókok több régióban

Különböző régiókban lévő több Batch-fiókok használatával teszi lehetővé, hogy az alkalmazás marad, ha a Batch-fiók egy másik régióban elérhetetlenné válik. Több fiók használata különösen fontos, ha az alkalmazásnak kell magas rendelkezésre állású legyen.

Bizonyos esetekben egy alkalmazást úgy tervezték, hogy mindig a két vagy több régiót használnak. Például amikor szüksége van egy jelentős mennyiségű kapacitást, több régióban használatával lehet szükség nagyméretű alkalmazások kezeléséhez, vagy gondoskodjon arról, hogy a jövőbeli növekedésre.

## <a name="design-considerations-for-providing-failover"></a>Feladatátvételi nyújtó kapcsolatos kialakítási szempontok

Feladatátvétele egy másodlagos régióba megadásakor vegye figyelembe a lényeg az, hogy a megoldás összes összetevő figyelembe kell venni; azt azonban nem egyszerűen rendelkezik második Batch-fiókkal. Például a legtöbb Batch-alkalmazások Azure-tárfiókra szükség, a storage-fiók és a Batch-fiók láthatják el az elfogadható teljesítmény ugyanabban a régióban.

Egy megoldás, amely a feladatátvétel tervezésekor, vegye figyelembe a következőket:

- Előre hozzon létre minden egyes régióban, mint például a Batch-fiók és a storage-fiókot az összes szükséges fiókokat. Gyakran nem áll bármilyen díjat kellene létrehozott fiókok, csak akkor van, tárolt adatok vagy a fiókot használja.
- Ellenőrizze, hogy a kvóták időben, a fiókok van beállítva, hogy a szükséges a Batch-fiók használatával magok száma foglalhat.
- Sablonok és/vagy parancsprogramok segítségével automatizálni az üzembe helyezést, az alkalmazás egy régióban.
- Tartsa naprakészen a alkalmazás bináris fájljainak és a referenciaadatok az összes régióban. Naprakész állapotban tartózkodó biztosítja a régió is lehet gyorsabban online állapotba nem kell megvárnia a fájlok telepítéséhez és a feltöltés. Például ha a készlet csomópontjain telepítendő egyéni alkalmazás tárolt hivatkozott használata Batch-alkalmazáscsomagokkal az alkalmazás egy új verzió jön létre, amikor azt kell kell minden egyes Batch-fiókhoz feltöltött és a tárolókészlet konfigurációját által hivatkozott (vagy Győződjön meg az új verziót az alapértelmezett verzió).
- Az alkalmazásban, Batch, storage, és más szolgáltatásokat, egyszerűen áttérés ügyfelek vagy a terhelés hívása a másik régióba.
- Ajánlott eljárás az, győződjön meg arról, a feladatátvétel sikeres lesz, hogy gyakran Váltás normál működés részeként egy másodlagos régióba. Ha például a két külön régióban, a másodlagos régióba havonta áttérés üzemelő.

## <a name="next-steps"></a>További lépések

- További tudnivalók a Batch-fiókok létrehozása a [az Azure portal](batch-account-create-portal.md), a [Azure CLI-vel](cli-samples.md), [Powershell](batch-powershell-cmdlets-get-started.md), vagy a [Batch management API](batch-management-dotnet.md).
- Batch-fiók; alapértelmezett kvóták tartoznak [Ez a cikk](batch-quota-limit.md) részletek alapértelmezett értéket, majd azt ismerteti, hogyan lehet növelni a kvóták.
