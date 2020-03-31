---
title: Mi az Azure Blockchain szolgáltatás blockchain-adatkezelője?
description: Blockchain Data Manager rögzíti, átalakítja, és biztosítja a blokklánc adatok Event Grid témakörök.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: chroyal
ms.openlocfilehash: 78c93880007259267b26cf53e93de722be1c7323
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209443"
---
# <a name="what-is-blockchain-data-manager-for-azure-blockchain-service"></a>Mi az Azure Blockchain Service-hez készült Blockchain Data Manager?

A Blockchain Data Manager rögzíti, átalakítja és kézbesíti az Azure Blockchain Service tranzakciós adatait az Azure Event Grid-témakörökbe, amelyek megbízható és méretezhető blockchain főkönyvi integrációt biztosítanak az Azure-szolgáltatásokkal.

A legtöbb vállalati blokklánc-forgatókönyvben a blokklánc-főkönyv a megoldás egyik része. Ha például egy eszközt egyik entitásból a másikba szeretne átvinni, a tranzakció elküldéséhez mechanizmusra van szükség. Ezután szükség van egy mechanizmus tantermi adatok olvasásához annak biztosításához, hogy a tranzakció történt, elfogadták, és az eredményül kapott állapotváltozások majd integrálni a teljes körű megoldás. Ebben a példában, ha intelligens szerződést ír az eszközök átviteléhez, a Blockchain Data Manager segítségével integrálhatja a láncon kívüli alkalmazásokat és adattárakat. Az eszközátvitel például, ha egy eszköz át a blockchain, események és a tulajdonság állapota változások által szállított Blockchain Data Manager keresztül Event Grid. Ezután az Event Grid több lehetséges eseménykezelőjét is használhatja a blokklánc-adatok láncon kívüli tárolásához vagy az állapotváltozásokvalós idejű reagálásához.

A Blockchain Data Manager három fő funkciót lát el: rögzítés, átalakítás és kézbesítés.

![A Blockchain Data Manager funkciói](./media/data-manager/functions.png)

## <a name="capture"></a>Rögzítés

Minden Blockchain Data Manager-példány egyetlen Azure Blockchain Service tagtranzakció-csomóponthoz csatlakozik. Csak a tranzakciócsomóponthoz hozzáféréssel rendelkező felhasználók hozhatnak létre kapcsolatot, amely biztosítja az ügyféladatokhoz való megfelelő hozzáférést. A Blockchain Data Manager-példány megbízhatóan rögzíti az összes nyers blokk- és nyers tranzakciós adatot a tranzakciós csomópontról, és skálázható a vállalati munkaterhelések támogatása érdekében.

## <a name="transform"></a>Átalakítás

A Blockchain Data Manager segítségével dekódolhatja az eseményeket és a tulajdonságállapotot intelligens szerződésalkalmazások konfigurálásával a Blockchain Data Manager ben. Intelligens szerződés hozzáadásához adja meg a szerződés ABI-t és bytecode-ot. A Blockchain Data Manager az intelligens szerződésösszetevőivel dekódolja és felderíti a szerződéscímeket. Miután hozzáadta a blokklánc-alkalmazást a példányhoz, a Blockchain Data Manager dinamikusan felderíti az intelligens szerződés címét, amikor az intelligens szerződés telepítve van a konzorciumban, és dekódolt eseményt és tulajdonságállapotot küld a konfigurált célhelyekre.

## <a name="deliver"></a>Kézbesítés

A Blockchain Data Manager több Eseményrács-témakör kimenő kapcsolatot támogat bármely adott Blockchain Data Manager-példányhoz. Blockchain adatokat küldhet egyetlen célhelyre, vagy küldhet blokklánc-adatokat több célhelyre. A Blockchain Data Manager használatával skálázható eseményalapú adatközzétételi megoldást hozhat létre bármely blokklánc-telepítéshez.

## <a name="configuration-options"></a>Beállítási lehetőségek

A Blockchain Data Manager konfigurálható a megoldás igényeinek megfelelően. Például a következőket építheti ki:

* Egyetlen Blockchain Data Manager-példány egy Azure Blockchain szolgáltatástaghoz.
* Blockchain Data Manager-példány Azure Blockchain Service tranzakciós csomópontonként. Például a privát tranzakciócsomópontok saját Blockchain Data Manager-példával rendelkezhetnek a bizalmas kezelés fenntartása érdekében.
* A Blockchain Data Manager-példány több kimeneti kapcsolatot is támogathat. Egy Blockchain Data Manager-példány használható az Azure Blockchain szolgáltatás tagjainak összes adatközzétételi integrációs pontjának kezelésére.

## <a name="next-steps"></a>További lépések

Hozzon [létre egy Blockchain Data Manager-példányt](data-manager-portal.md) egy Azure Blockchain szolgáltatástaghoz.
