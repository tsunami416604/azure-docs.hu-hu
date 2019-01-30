---
title: MySQL-adatbázisok használata PaaS Azure Stack-ként |} A Microsoft Docs
description: Ismerje meg, hogyan lehet a MySQL erőforrás-szolgáltató üzembe helyezése, és adja meg a MySQL-adatbázisok szolgáltatásként az Azure Stacken.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 03e429e1a40f9b23a2a3bedef5ef488d81392d9c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236904"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>MySQL-adatbázisok használata a Microsoft Azure Stackhez

MySQL-adatbázisok gyakoriak a webhelyekről, és támogatja a webhely számos platformon. Létrehozhat például a Web Apps platformmal a platformszolgáltatás (PaaS) végpontállapot WordPress-webhelyeit.

Az erőforrás-szolgáltató üzembe helyezése, után teszi lehetővé:

* Hozzon létre MySQL-kiszolgálók és adatbázisokat az Azure Resource Manager központi telepítési sablonok használatával.
* Adja meg a MySQL-adatbázisok szolgáltatásként.  

## <a name="mysql-resource-provider-adapter-architecture"></a>MySQL erőforrás-szolgáltató adapter architektúrája

Az erőforrás-szolgáltató a következő összetevőket tartalmazza:

* **A MySQL erőforrás szolgáltató adapter a virtuális gép (VM)**, azaz a szolgáltató-szolgáltatásokat futtató Windows Server virtuális gép.
* **Az erőforrás-szolgáltató**, amely feldolgozza a kérelmeket, és azt adatbázis-erőforrás.
* **MySQL-kiszolgálót üzemeltető kiszolgálók**, amely az adatbázisok, az úgynevezett üzemeltető kiszolgálók-kapacitást biztosítanak. MySQL-példányok létrehozásához, saját maga, vagy külső MySQL-példányok eléréséhez. A [Azure Stack gyorsindítási galéria](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) rendelkezik egy példa a sablon, amely segítségével:

  * MySQL-kiszolgáló létrehozása az Ön számára.
  * Töltse le és üzembe helyezése az Azure Marketplace-ről egy MySQL-kiszolgálóhoz.

> [!NOTE]
> Üzemeltető kiszolgálók telepített Azure Stack integrált rendszerek kell létrehozni egy bérlő előfizetésből. Az alapértelmezett szolgáltatója előfizetésből nem hozhatók létre. A bérlői portál vagy egy PowerShell-munkamenetet egy megfelelő bejelentkezési, létre kell hozni. Minden üzemeltetési kiszolgáló számlázható virtuális gépeket, és rendelkeznie kell licenccel. A szolgáltatás-rendszergazda lehet a bérlő előfizetés tulajdonosa.

### <a name="required-privileges"></a>Szükséges jogosultságok

A system fióknak a következő jogosultságokkal kell rendelkeznie:

* **Adatbázis:** létrehozni, eldobni
* **Bejelentkezés:** létrehozása, beállítása, dobja el, adja meg, visszavonása  

## <a name="next-steps"></a>További lépések

[A MySQL erőforrás-szolgáltató üzembe helyezése](azure-stack-mysql-resource-provider-deploy.md)
