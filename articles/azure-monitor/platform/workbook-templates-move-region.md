---
title: Azure Monitor munkafüzet-sablonok – régiók áthelyezése
description: Munkafüzet-sablon áthelyezése másik régióba
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: f37550d914cf7efb0c75ed3dfa8854e1ec7be7e0
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875680"
---
# <a name="move-an-azure-workbook-template-to-another-region"></a>Azure munkafüzet-sablon áthelyezése másik régióba

Ez a cikk azt ismerteti, hogyan helyezhető át az Azure munkafüzet-sablon erőforrásai egy másik Azure-régióba. Az erőforrásokat több okból is áthelyezheti egy másik régióba. Ha például egy új Azure-régió előnyeit szeretné kihasználni, csak meghatározott régiókban elérhető szolgáltatásokat vagy szolgáltatásokat telepíthet, a belső házirend-és irányítási követelmények teljesítéséhez, vagy a kapacitás megtervezésének követelményeire reagálva.

Jelenleg nincs olyan portál felhasználói felülete, amely munkafüzet-sablon-erőforrásokat hoz létre, a létrehozásuk egyetlen jelenlegi módja [Azure Resource Manager sablon (ARM-sablon) központi telepítésen keresztül](./workbooks-automate.md). Ennek megfelelően a sablon áthelyezésének legegyszerűbb módja az előző ARM-sablon újbóli felhasználása, és az új régióban való üzembe helyezésének frissítése.

## <a name="prerequisites"></a>Előfeltételek

* Győződjön meg arról, hogy a megcélzott régióban támogatottak a munkafüzetek sablonjai.

## <a name="prepare"></a>Előkészítés

* Azonosítsa az előzőleg használt ARM-sablont a munkafüzet sablonhoz.

## <a name="move"></a>Áthelyezés

1. Frissítse a korábban használt sablont az új régióra való hivatkozáshoz.

   > [!NOTE]
   > Előfordulhat, hogy a munkafüzet sablonjának új nevet kell használnia az ismétlődő nevek elkerüléséhez.

2. Telepítse a frissített sablont az ARM-sablon üzembe helyezésével, és hozzon létre egy új munkafüzet-sablont a kívánt régióban.

## <a name="verify"></a>Ellenőrzés

Az újonnan üzembe helyezett munkafüzet-sablon megkereséséhez használja az Azure-munkafüzetek Tallózás felhasználói felületét. Győződjön meg arról, hogy a hely a célhely.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Miután létrehozta a munkafüzet-sablont az új régióban, törölje az eredeti munkafüzet-sablont az előző régióban.
1. Keresse meg a munkafüzet sablonját az Azure-munkafüzetek Tallózás felhasználói felületén.
2. Válassza ki a törölni kívánt munkafüzet-sablont.
3. Válassza a "Törlés" parancsot.

Ha átnevezte a munkafüzet sablonját egy új régióba való importáláshoz, a munkafüzet sablonját átnevezheti az előző névre az eredeti elem törlése után az Azure munkafüzet-sablon erőforrás nézetének "Átnevezés" parancsával.

## <a name="next-steps"></a>További lépések

Sablon helyett egy munkafüzetet kell áthelyeznie? Lásd: [Azure-munkafüzetek áthelyezése másik régióba](./workbooks-move-region.md).

