---
title: Azure Migrate applianc beállítása
description: Ismerje meg, hogyan állíthat be egy Azure Migrate-berendezést a VMware virtuális gépek felméréséhez és áttelepítéséhez.
ms.topic: article
ms.date: 11/18/2019
ms.openlocfilehash: 0447443bb6a642cac09566450ea2d9bb6f6453d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337661"
---
# <a name="set-up-an-azure-migrate-appliance"></a>Azure Migrate-berendezés beállítása

Ez a cikk összegzi az Azure Migrate-berendezés beállításának lehetőségeit. 

Az Azure Migrate berendezés egy könnyű berendezés üzembe helyezése a helyszínen, és számos forgatókönyvben használható.

**Forgatókönyv** | **Részletek**
--- | ---
VMware virtuális gépek felmérése az Azure Migrate:Server assessment segítségével | Virtuálisgép-alkalmazások és -függőségek felfedezése<br/><br/> A gépek metaadatait és teljesítménymetaadatait az értékelésekhez gyűjtheti.
Hyper-V virtuális gépek felmérése az Azure Áttelepítése:Kiszolgálófelmérés segítségével | Hyper-V virtuális gépek felfedezése<br/><br/> A gépek metaadatait és teljesítménymetaadatait az értékelésekhez gyűjtheti.
Fizikai gépek értékelése | Gépek felderítése fizikai kiszolgálóként<br/><br/> A gépek metaadatait és teljesítménymetaadatait az értékelésekhez gyűjtheti.
VMware virtuális gépek replikálása ügynök nélküli áttelepítéssel. | VMware virtuális gépek replikálása nélkül semmit a replikálni kívánt virtuális gépeken.


## <a name="deployment-options"></a>Üzembe helyezési beállítások

A készüléket többféleképpen is telepítheti.

**Forgatókönyv** | **Sablon** | **Script** 
--- | --- | --- | ---
**VMware virtuális gépek értékelése** | Üzembe helyezés letöltött OVA sablonnal.<br/><br/> Ismerje meg, hogyan telepítheti a készüléket [egy sablon használatával,](how-to-set-up-appliance-vmware.md)vagy indítsa el az [értékelési oktatóanyagot,](tutorial-prepare-vmware.md) és telepítse a készüléket egy sablonnal az oktatóanyag során.  | Telepítse egy PowerShell telepítő parancsfájl használatával.<br/><br/>  [Tekintse át](deploy-appliance-script.md) a készülék parancsfájlutasításait.
**Hyper-V virtuális gépek értékelése** | Üzembe helyezés egy letöltött VHD-sablonnal. <br/><br/> Ismerje meg, hogyan telepítheti a készüléket [egy sablon használatával,](how-to-set-up-appliance-vmware.md)vagy indítsa el az [értékelési oktatóanyagot,](tutorial-prepare-vmware.md) és telepítse a készüléket egy sablonnal az oktatóanyag során. | Telepítse egy PowerShell telepítő parancsfájl használatával.<br/><br/> [Tekintse át](deploy-appliance-script.md) a készülék parancsfájlutasításait. 
**Fizikai kiszolgálók felmérése** | Nincs sablon. | Telepítse egy PowerShell telepítő parancsfájl használatával.<br/><br/> Tekintse át [a készülék parancsfájl-utasításait](how-to-set-up-appliance-physical.md), vagy indítsa el az [értékelési oktatóanyagot,](tutorial-prepare-physical.md)és telepítse a készüléket az oktatóanyag során.
**VMware virtuális gépek replikálása (ügynök nélküli)** | Üzembe helyezés letöltött OVA sablonnal.<br/><br/> Ha már értékelte a replikálni kívánt virtuális gépeket, akkor már beállította a készüléket az értékelés során.<br/><br/> Ha a VMware virtuális gépek et anélkül replikálja, hogy értékeli őket, ismerje meg, hogyan telepítheti a készüléket egy sablon használatával, vagy indítsa el az [ügynök nélküli áttelepítési oktatóanyagot,](tutorial-migrate-vmware.md)és telepítse a készüléket egy sablonnal az oktatóanyag során. | Telepítse egy PowerShell telepítő parancsfájl használatával. <br/><br/> [Tekintse át](deploy-appliance-script.md) a készülék parancsfájlutasításait. 




## <a name="next-steps"></a>További lépések

[Tekintse át](migrate-appliance.md) a készülékre vonatkozó követelményeket.