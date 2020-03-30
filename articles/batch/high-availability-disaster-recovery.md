---
title: Magas rendelkezésre állás és vészhelyreállítás – Azure Batch
description: Ismerje meg, hogyan tervezheti meg a Batch-alkalmazást egy regionális kimaradáshoz. A számítási feladatok feladatátvételt egy másik régióba kell áttekinteniük, vagy két vagy több régió között kell felosztani.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2019
ms.author: labrenne
ms.openlocfilehash: 84b0cce9557b4ae05586579f175cd0f5db14fdfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77026081"
---
# <a name="design-your-application-for-high-availability"></a>Magas rendelkezésre állású alkalmazás tervezése

Az Azure Batch egy regionális szolgáltatás. Batch érhető el az összes Azure-régióban, de amikor egy Batch-fiók jön létre, hogy kell társítható egy régióban. A Batch-fiók összes művelete ezután az adott régióra vonatkozik. Például a készletek és a kapcsolódó virtuális gépek (VM-ek) ugyanabban a régióban jönnek létre, mint a Batch-fiók.

A Batch-et használó alkalmazás tervezésekor figyelembe kell vennie annak lehetőségét, hogy a Batch nem érhető el egy régióban. Előfordulhat, hogy egy ritka helyzet, ahol probléma van a régió egészével, a teljes Batch szolgáltatás a régióban, vagy a probléma az adott Batch-fiókkal.

Ha a Batch használatával az alkalmazás vagy megoldás mindig elérhetőnek kell lennie, majd úgy kell megtervezni, hogy vagy feladatátvétel egy másik régióba, vagy mindig a számítási feladatok két vagy több régió között oszlik meg. Mindkét megközelítés hez legalább két kötegelt fiók szükséges, és minden egyes fiók egy másik régióban található.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Több kötegszámla több régióban

Több Batch-fiók használata különböző régiókban lehetővé teszi, hogy az alkalmazás továbbra is fut, ha egy batch fiók egy másik régióban elérhetetlenné válik. Több fiók használata különösen fontos, ha az alkalmazás magas rendelkezésre állású.

Bizonyos esetekben előfordulhat, hogy egy alkalmazás úgy van kialakítva, hogy mindig két vagy több régiót használjon. Ha például jelentős kapacitásra van szüksége, több régió használatára lehet szükség egy nagyméretű alkalmazás kezeléséhez vagy a jövőbeli növekedés hez.

## <a name="design-considerations-for-providing-failover"></a>Tervezési szempontok a feladatátvételbiztosításához

Egy másik régió feladatátvételi képességének biztosításasorán figyelembe veendő kulcspont az, hogy a megoldás minden összetevőjét figyelembe kell venni; nem elegendő egyszerűen egy második Batch-fiókkal rendelkezni. A legtöbb Batch-alkalmazásban például egy Azure-tárfiókra van szükség, és a tárfióknak és a Batch-fióknak ugyanabban a régióban kell lennie az elfogadható teljesítmény érdekében.

Vegye figyelembe a következő pontokat egy olyan megoldás tervezésekor, amely feladatátvételt tehet le:

- Hozzon létre minden szükséges fiókot az egyes régiókban, például a Batch-fiókot és a tárfiókot. Gyakran nincs díj a fiókok létrehozásáért, csak akkor, ha adatok vannak tárolva, vagy a fiókot használják.
- Győződjön meg arról, hogy a kvóták előre be vannak állítva a fiókokon, így a Batch-fiók használatával lefoglalhatja a szükséges magok számát.
- Sablonok és/vagy parancsfájlok használatával automatizálhatja az alkalmazás központi telepítését egy régióban.
- Az alkalmazásbináris fájlokat és a referenciaadatokat minden régióban naprakészen tarthatja. A naprakészen maradás biztosítja, hogy a régió gyorsan online állapotba kerüljön anélkül, hogy meg kellene várnia a fájlok feltöltését és telepítését. Ha például a készletcsomópontokra telepítendő egyéni alkalmazást batch alkalmazáscsomagok használatával tárolják és hivatkoznak rá, majd az alkalmazás új verziójának létrehozásakor minden Batch-fiókba fel kell tölteni, és a készlet konfigurációja hivatkozni kell rá (vagy hogy az új verzió legyen az alapértelmezett verzió).
- Az alkalmazás ban a Batch, a storage és minden más szolgáltatás, könnyen átválthatók az ügyfelek vagy a terhelés a különböző régióba.
- A feladatátvétel sikeresssé válásának ajánlott gyakorlata, hogy a normál működés részeként gyakran átáll egy másik régióra. Ha például két központi telepítés külön régióban van, havonta átáll az alternatív régióra.

## <a name="next-steps"></a>További lépések

- További információ a Batch-fiókok létrehozásáról az [Azure Portalon,](batch-account-create-portal.md)az [Azure CLI-vel,](cli-samples.md)a [PowerShell-ben](batch-powershell-cmdlets-get-started.md)vagy a [Batch management API-val.](batch-management-dotnet.md)
- Az alapértelmezett kvóták kötegelt fiókhoz vannak társítva; [ez a cikk](batch-quota-limit.md) részletezi az alapértelmezett kvótaértékeket, és leírja, hogyan növelhetők a kvóták.
