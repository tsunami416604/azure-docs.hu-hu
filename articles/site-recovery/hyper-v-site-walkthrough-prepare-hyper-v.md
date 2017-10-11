---
title: "A replikáció az Azure Hyper-V állomás (a System Center VMM nélkül) előkészítése |} Microsoft Docs"
description: "Ismerteti, hogyan készíti elő a Hyper-V-gazdagépek a replikálást az Azure Site Recovery segítségével Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0f204e24-8d78-4076-95c5-8137d1be9c01
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: f9bcaa8e55be6e8fddaf88ebc3f18f5dbb2811e4
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="step-6-prepare-hyper-v-hosts-for-replication-to-azure"></a>6. lépés: Hyper-V-gazdagépek előkészítése a replikálás az Azure-bA

Ebben a cikkben az utasításokat követve készítse elő a helyszíni Hyper-V gazdagépek Azure Site Recovery kommunikál.

A cikk elolvasása után bármely fűzhetnek alsó, vagy a műszaki kérdései a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prepare-hosts"></a>Gazdagépek előkészítése

- Győződjön meg arról, hogy a Hyper-V-gazdagépek megfelelnek a [Előfeltételek](site-recovery-prereq.md#disaster-recovery-of-hyper-v-vms-to-azure-no-vmm).
- Győződjön meg arról, hogy a gazdagépek férhessen hozzá a szükséges URL-címek:

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- Ha IP-címeken alapuló tűzfalszabályokat használ, ellenőrizze, hogy engedélyezik-e az Azure-ral való kommunikációt.
- Engedélyezze az [Azure-adatközpont IP-tartományait](https://www.microsoft.com/download/confirmation.aspx?id=41653) és a HTTPS-portot (443).
- Engedélyezze az előfizetéshez tartozó Azure-régió, illetve az USA nyugati régiójának IP-tartományait (a hozzáférés-vezérléshez és az identitáskezeléshez szükséges).

Site Recovery üzembe helyezése során egy Hyper-V helyre replikálni kívánt virtuális gépeket tartalmazó Hyper-V-gazdagépek hozzáadása. A Site Recovery Provider és Recovery Services Agent ügynököt minden gazdagépen van telepítve. A Recovery Services-tároló regisztrálva van a Hyper-V helyet.

## <a name="next-steps"></a>Következő lépések

Ugrás a [7. lépés: a tároló létrehozása](hyper-v-site-walkthrough-create-vault.md)

