---
title: Amazon Web Services-beli (AWS) virtuális gépek migrálása az Azure-ba az Azure Site Recovery szolgáltatással | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan migrálhatók az Amazon Web Servicesben (AWS-ben) futó Windows rendszerű virtuális gépek az Azure-ba az Azure Site Recovery használatával.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: dd91e99b45405cca10b9ddc2982674e72ad6bf86
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281293"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Amazon Web Services-beli (AWS) virtuális gépek áttelepítése az Azure-ba

Ez a cikk a Amazon Web Services-(AWS-) példányok Azure-ba való áttelepítésének lehetőségeit ismerteti.

## <a name="migrate-with-azure-migrate"></a>Migrálás Azure Migrate

Javasoljuk, hogy telepítse az AWS-példányokat az Azure-ba a [Azure Migrate](../migrate/migrate-services-overview.md) szolgáltatás használatával. A Azure Migrate központosított hubot biztosít a helyszíni gépek Azure-ba történő értékeléséhez és áttelepítéséhez Azure Migrate, más Azure-szolgáltatások és harmadik féltől származó eszközök használatával.

[Ismerje meg, hogyan](../migrate/tutorial-migrate-aws-virtual-machines.md) migrálhatja az AWS-példányokat Azure Migrate használatával. 


## <a name="migrate-with-site-recovery"></a>Migrálás Site Recovery

Site Recovery csak vész-helyreállításra kell használni, és nem kell áttelepítenie.

Ha már használja a Azure Site Recoveryt, és szeretné tovább használni az AWS-áttelepítéshez, kövesse a [fizikai gépek vész-helyreállításának](physical-azure-disaster-recovery.md)beállításához használt lépéseket.


> [!NOTE]
> Ha feladatátvételt futtat a vész-helyreállításhoz, a feladatátvétel utolsó lépéseként véglegesíti a tranzakciót. AWS-példányok áttelepítésekor a **véglegesítés** lehetőség nem érvényes. Ehelyett válassza a **teljes áttelepítési** lehetőséget. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tekintse át](../migrate/resources-faq.md) a Azure Migrateával kapcsolatos gyakori kérdéseket.
