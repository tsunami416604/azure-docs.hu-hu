---
title: "Telepítse a mobilitási szolgáltatást, a VMware Azure replikációs |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan telepíteni a mobilitási szolgáltatás ügynöke, a VMware Azure replikálás az Azure Site Recovery szolgáltatással."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 3189fbcd-6b5b-4ffb-b5a9-e2080c37f9d9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: bc520bd2ea54208889861a7a3b275e3008a05d53
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="step-10-install-the-mobility-service"></a>10. lépés: A mobilitási szolgáltatás telepítése


A cikk ismerteti a forrás és cél beállítások konfigurálása, ha a helyszíni VMware virtuális gépek replikálása Azure-ba, használja a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás az Azure portálon.

A mobilitási szolgáltatást a gépen végbemenő adatírásokat, és továbbítja őket a folyamatkiszolgálónak. Az egyes Azure-bA replikálni kívánt gépek telepíthető.

A Site Recovery folyamat kiszolgálóról ügyfélleküldéses telepítést használ, ha engedélyezve van a replikáció a mobilitási szolgáltatás manuális telepítése, vagy a System Center Configuration Manager eszközzel. Ha ügyfélleküldéses telepítést használ, a szolgáltatás telepítve van a virtuális gép replikációs engedélyezésekor a rendszer.

Ebben a cikkben, vagy az alsó megjegyzések és kérdéseket küldje a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="install-manually"></a>Manuális telepítés

1. Ellenőrizze a [Előfeltételek](site-recovery-vmware-to-azure-install-mob-svc.md#prerequisites) manuális telepítésre.
2. Hajtsa végre a [ezeket az utasításokat](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) manuális telepítésre a portál használatával.
3. Ha inkább a parancssorból telepíti, hajtsa végre az [ezeket az utasításokat](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt).

## <a name="install-from-the-process-server"></a>A folyamatkiszolgáló telepítése

Ha kíván leküldeni a mobilitási szolgáltatás telepítési adatok a folyamatkiszolgálótól, ha engedélyezi a virtuális gépek replikálása, akkor egy fiókot, amelyet a virtuális gép eléréséhez a folyamatkiszolgáló által használható. A fiók csak szolgál a leküldéses telepítés.

1. Rendelkeznie kell [hoztak létre fiókot](vmware-walkthrough-prepare-vmware.md) , amely leküldéses telepítéséhez használható. Majd adja meg a Site Recovery üzembe helyezése során az adatforrás-beállítások konfigurálásakor használni kívánt fiókot.
2. Hajtsa végre [ezeket az utasításokat](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) Ha azt szeretné, hogy a mobilitási szolgáltatás leküldéses Windows vagy Linux rendszerű virtuális gépeken.

## <a name="other-methods"></a>Más módszerrel

További információ [a mobilitási szolgáltatást a Configuration Manager telepítése](site-recovery-install-mobility-service-using-sccm.md), vagy [Azure Automation DSC](site-recovery-automate-mobility-service-install.md).

## <a name="next-steps"></a>Következő lépések

Ugrás a [11. lépés: replikálás engedélyezése](vmware-walkthrough-enable-replication.md)
