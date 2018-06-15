---
title: Azure SQL-szolgáltatás és az Azure Security Centerben adatok védelme |} Microsoft Docs
description: Ez a dokumentum címek, amelyek segítenek az Azure Security Center javaslatait védeni kell az adatok és az Azure SQL-szolgáltatás, és maradnak meg a biztonsági házirendeknek megfelelően.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2017
ms.author: terrylan
ms.openlocfilehash: 0c3a11e9a86767641533b16de1b96b4c59bfdf51
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866274"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Azure SQL-szolgáltatás és az Azure Security Center adatainak védelme
Az Azure Security Center elemzi az Azure-erőforrások biztonsági állapotát. Amikor a Security Center a potenciális biztonsági hiányosságokat azonosít, ajánlásokat, amely végigvezeti Önt a szükséges szabályozási folyamatán hoz létre.  Javaslat alkalmazható azonban az Azure erőforrás típusa: virtuális gépek (VM), hálózati, SQL és az adatokhoz és alkalmazásokhoz.

Ez a cikk foglalkozik az Azure SQL-szolgáltatás és az adatok vonatkozó javaslatokat. Javaslatok center körül naplózás engedélyezése az Azure SQL-kiszolgálók és adatbázisok, SQL-adatbázisokhoz tartozó titkosítási és az Azure storage-fiók engedélyezése titkosításának engedélyezése.  Az alábbi táblázatban használja hivatkozásként segít megérteni a rendelkezésre álló SQL szolgáltatás és az adatok ajánlásokat, és mindegyiket funkciója Ha alkalmazza azt.

## <a name="available-sql-service-and-data-recommendations"></a>Elérhető az SQL szolgáltatás és az adatok javaslatok
| Ajánlás | Leírás |
| --- | --- |
| [Naplózás és fenyegetésészlelés engedélyezése az SQL-kiszolgálókon](security-center-enable-auditing-on-sql-servers.md) |Javasolja, hogy kapcsolja be az Azure SQL-kiszolgálókra (Azure SQL-szolgáltatás csak; nem tartalmazza a virtuális gépeken futó SQL) a naplózás és a fenyegetések észlelésére. |
| [Naplózás és fenyegetésészlelés engedélyezése az SQL-adatbázisokon](security-center-enable-auditing-on-sql-databases.md) |Javasolja, hogy kapcsolja be az Azure SQL-adatbázisok (Azure SQL-szolgáltatás csak; nem tartalmazza a virtuális gépeken futó SQL) a naplózás és a fenyegetések észlelésére. |
| [Az átlátható adattitkosítási engedélyezése az SQL-adatbázisok](security-center-enable-transparent-data-encryption.md) |Titkosítás az SQL-adatbázisok (csak Azure SQL szolgáltatás) engedélyezését javasolja. |

## <a name="see-also"></a>Lásd még:
Egyéb Azure erőforrástípusok vonatkozó javaslatok kapcsolatos további tudnivalókért olvassa el a következőket:

* [Az Azure Security Centerben a virtuális gépek védelme](security-center-virtual-machine-recommendations.md)
* [Az alkalmazások az Azure Security Centerben védelme](security-center-application-recommendations.md)
* [Az Azure Security Centerben a hálózat védelme](security-center-network-recommendations.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
