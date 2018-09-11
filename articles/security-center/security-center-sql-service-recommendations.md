---
title: Az Azure SQL-szolgáltatás és az Azure Security Center adatok védelme |} A Microsoft Docs
description: Ez a dokumentum címek javaslatok az Azure Security Centerben, amelyekkel védje meg adatait és az Azure SQL-szolgáltatás, és megfelel a biztonsági házirendek maradjon.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2017
ms.author: terrylan
ms.openlocfilehash: 0b3b8082412b12a0fffbaea04409a8bbb3f4ac15
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44295377"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Az Azure SQL-szolgáltatás és az Azure Security Center adatok védelme
Az Azure Security Center elemzi az Azure-erőforrások biztonsági állapotát. Ha a Security Center azonosítja a potenciális biztonsági réseket, javaslatok, amelyek végigvezetik a szükséges vezérlők konfigurálásának folyamatán hoz létre.  Javaslatok alkalmazása az Azure-erőforrástípus: virtuális gépek (VM), hálózati, SQL és az adatokhoz és alkalmazásokhoz.

Ez a cikk foglalkozik az Azure SQL-szolgáltatás és az adatokra vonatkozó javaslatok. Az Azure SQL-kiszolgáló és adatbázis-titkosítási SQL-adatbázisok és az Azure storage-fiók titkosításának engedélyezésével engedélyezése naplózásának engedélyezésével center javaslatokat.  Referenciaként az alábbi táblázat segítségével segítségével megismerheti az elérhető SQL szolgáltatás és adat javaslatokat, és mindegyik funkciója alkalmazásuk esetén.

## <a name="available-sql-service-and-data-recommendations"></a>Elérhető az SQL szolgáltatás és adat javaslatok
| Ajánlás | Leírás |
| --- | --- |
| [Naplózás és fenyegetésészlelés engedélyezése az SQL-kiszolgálókon](security-center-enable-auditing-on-sql-servers.md) |Javasolja, hogy kapcsolja be a naplózás és fenyegetésészlelés az Azure SQL-kiszolgáló (az Azure SQL-szolgáltatás csak; nem tartalmazza a virtuális gépeken futó SQL). |
| [Naplózás és fenyegetésészlelés engedélyezése az SQL-adatbázisokon](security-center-enable-auditing-on-sql-databases.md) |Javasolja, hogy kapcsolja be a naplózás és fenyegetésészlelés az Azure SQL Database (Azure SQL-szolgáltatás csak; nem tartalmazza a virtuális gépeken futó SQL). |
| [Az SQL Database-adatbázisok transzparens adattitkosítás engedélyezése](security-center-enable-transparent-data-encryption.md) |(Csak a szolgáltatás az Azure SQL) SQL-adatbázisok titkosításának engedélyezését javasolja. |

## <a name="see-also"></a>Lásd még
Javaslatok, amelyek vonatkoznak a többi Azure-erőforrásokkal kapcsolatos további információkért tekintse meg a következőket:

* [Virtuális gépek védelme az Azure Security Centerben](security-center-virtual-machine-recommendations.md)
* [Alkalmazások védelme az Azure Security Centerben](security-center-application-recommendations.md)
* [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
