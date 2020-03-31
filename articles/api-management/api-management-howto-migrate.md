---
title: Az Azure API Management áttelepítése régiók között
description: Ismerje meg, hogyan telepítheti át az API Management-példányokat egyik régióból a másikba.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 3294a7b2112e9527041ef343f4452aedb7a2a272
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073465"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>Az Azure API Management áttelepítése régiók között
Az API Management-példányok egyik Azure-régióból a másikba történő áttelepítéséhez használhatja a [biztonsági mentési és visszaállítási](api-management-howto-disaster-recovery-backup-restore.md) funkciót. Ugyanazt az API Management tarifacsomagot kell választania a forrás- és célrégiókban. 

> [!NOTE]
> A biztonsági mentés és visszaállítás nem működik a különböző felhőtípusok közötti áttelepítés ekor. Ahhoz az erőforrást [sablonként](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates)kell exportálnia. Ezután igazítsa az exportált sablont a cél Azure-régióhoz, és hozza létre újra az erőforrást. 

## <a name="option-1-use-a-different-api-management-instance-name"></a>1. lehetőség: Másik API-felügyeleti példány név használata

1. A célrégióban hozzon létre egy új API Management-példányt ugyanazzal a tarifacsomaggal, mint a forrás API Management-példány. Az új példánynak más néven kell rendelkeznie. 
1. Meglévő API Management-példány biztonsági mentése egy tárfiókba.
1. Állítsa vissza a 2.
1. Ha egy egyéni tartomány a forrásrégió API Management példányára mutat, frissítse az egyéni cname tartományt az új API Management-példányra. 


## <a name="option-2-use-the-same-api-management-instance-name"></a>2. lehetőség: Használja ugyanazt az API Management-példánynevet

> [!NOTE]
> Ez a beállítás az áttelepítés során állásidőt eredményez.

1. Biztonsági másolatot a forrásrégióban az API Management-példányról egy tárfiókba.
1. Törölje az API Management a forrásrégióban. 
1. Hozzon létre egy új API Management-példányt a célrégióban, amelynek neve megegyezik a forrásrégióban lévővel.
1. Állítsa vissza az 1.  


## <a name="next-steps"></a><a name="next-steps"> </a>További lépések
* A biztonsági mentési és visszaállítási funkcióról a [vészhelyreállítás megvalósításáról](api-management-howto-disaster-recovery-backup-restore.md)talál további információt.
* Az Azure-erőforrások áttelepítésével kapcsolatos információkért tekintse meg az [Azure régiók közötti áttelepítési útmutatóját.](https://github.com/Azure/Azure-Migration-Guidance)