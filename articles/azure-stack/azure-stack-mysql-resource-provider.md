---
title: MySQL-adatbázisok használata Azure veremben PaaS |} Microsoft Docs
description: Ismerje meg, hogyan telepítheti a MySQL erőforrás-szolgáltató, és adja meg a MySQL-adatbázisok Azure veremben szolgáltatásként.
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
ms.date: 06/21/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 24ba595413cde07c420a94de234d7926e0eb0e7f
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309840"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>A Microsoft Azure verem használható MySQL-adatbázisok

A MySQL erőforrás-szolgáltató API-t használja az Azure-verem telepítve MySQL-adatbázisok telepítése. Az erőforrás-szolgáltató API kapcsolatos további információkért lásd: [Windows Azure Pack MySQL erőforrás szolgáltató REST API-referencia](https://msdn.microsoft.com/library/dn528442.aspx).

MySQL-adatbázisok közös webhelyeken és webhely több platform támogatására. Például a Web Apps platform (PaaS) szolgáltatás-bővítmény használatával WordPress-webhelyek is létrehozhat.

Az erőforrás-szolgáltató telepítése után végezhetők el:

* MySQL-kiszolgálók és adatbázisok használata Azure Resource Manager központi telepítési sablonok létrehozása.
* Adja meg a MySQL-adatbázisok szolgáltatásként.  

## <a name="mysql-resource-provider-adapter-architecture"></a>MySQL erőforrás-szolgáltató adapter architektúrája

Az erőforrás-szolgáltató a következő részből áll:

* **A MySQL erőforrás szolgáltató adapter virtuális gép (VM)**, vagyis a szolgáltató-szolgáltatásokat futtató Windows Server virtuális gép.
* **Az erőforrás-szolgáltató**, amely feldolgozza a kérelmeket, és hozzáférések adatbázis-erőforrásokat.
* **MySQL-kiszolgálót üzemeltető kiszolgáló**, adatbázisok, nevezzük üzemeltető kiszolgálók kapacitásának nyújt. MySQL-példányok saját maga hoz létre, vagy külső MySQL-példányokat hozzáférést biztosítanak. A [Azure verem gyorsindítási galéria](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) egy példa sablonja, amelyek segítségével:

  * Hozza létre a MySQL-kiszolgáló.
  * Töltse le és telepít egy MySQL-kiszolgálót az Azure piactérről.

> [!NOTE]
> Üzemeltető kiszolgálók Azure verem telepített integrált rendszerek bérlői előfizetéssel kell létrehozni. Az alapértelmezett szolgáltató előfizetésből nem hozhatók létre. A bérlői portálon vagy egy PowerShell munkamenetből rendelkező megfelelő bejelentkezési objektumokat létre kell hozni. Minden üzemeltetési kiszolgáló számlázható virtuális gép, és rendelkeznie kell licencekkel. A szolgáltatás-rendszergazdát a bérlő előfizetés tulajdonosának lehet.

### <a name="required-privileges"></a>Szükséges jogosultságok

A system fiók következő jogosultságokkal kell rendelkeznie:

* **Adatbázis:** létrehozni, eldobni
* **Bejelentkezés:** létrehozása, állítsa be, dobja el, adja meg, visszavonása  

## <a name="next-steps"></a>További lépések

[A MySQL erőforrás-szolgáltató telepítése](azure-stack-mysql-resource-provider-deploy.md)
