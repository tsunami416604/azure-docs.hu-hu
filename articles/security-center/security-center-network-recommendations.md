---
title: Hálózat védelme az Azure Security Centerben |} A Microsoft Docs
description: Ez a dokumentum címek javaslatok az Azure Security Centerben, amelyekkel védelme az Azure-hálózatok, és megfelel a biztonsági házirendek maradjon.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: terrylan
ms.openlocfilehash: 12c00d6dfac6c9c2a377a8c142118ff6fd0af751
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302272"
---
# <a name="protecting-your-network-in-azure-security-center"></a>Hálózat védelme az Azure Security Centerben
Az Azure Security Center elemzi az Azure-erőforrások biztonsági állapotát. Ha a Security Center azonosítja a potenciális biztonsági réseket, javaslatok, amelyek végigvezetik a szükséges vezérlők konfigurálásának folyamatán hoz létre.  Javaslatok alkalmazása az Azure-erőforrástípus: virtuális gépek (VM), hálózati, SQL és az alkalmazások.

Ez a cikk foglalkozik, amely a hálózattal kapcsolatos ajánlások.  Hálózati javaslatok központ következő generációs tűzfalak, hálózati biztonsági csoportok, konfigurálását, bejövő forgalomra vonatkozó szabályokat és további körül.  Referenciaként az alábbi táblázat segítségével segítségével megismerheti a rendelkezésre álló hálózati javaslatok, és mindegyik funkciója alkalmazásuk esetén.

## <a name="available-network-recommendations"></a>Rendelkezésre álló hálózati javaslatok
| Ajánlás | Leírás |
| --- | --- |
| [Újgenerációs tűzfal hozzáadása](security-center-add-next-generation-firewall.md) |Javasolja, hogy egy Microsoft-partner a biztonsági megoldásokat növelése érdekében adjon hozzá egy Next Generation Firewall (NGFW). |
| [Csak az újgenerációs tűzfalon keresztül haladjon a forgalom](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Javasolja, hogy konfigurálja a hálózati biztonsági csoport (NSG) szabályai kényszerítheti a bejövő forgalmat a virtuális géphez az Újgenerációs tűzfalon keresztül. |
| [Hálózati biztonsági csoportok engedélyezése az alhálózatokra vagy virtuális gépekre](security-center-enable-network-security-groups.md) |Az NSG-ket alhálózatokhoz vagy virtuális gépeken engedélyezését javasolja. |
| [Internetről elérhető végponton keresztüli hozzáférés korlátozása](security-center-restrict-access-through-internet-facing-endpoints.md) |Javasolja, hogy a bejövő forgalomra vonatkozó szabályokat az NSG-k konfigurálása. |

## <a name="see-also"></a>Lásd még
Javaslatok, amelyek vonatkoznak a többi Azure-erőforrásokkal kapcsolatos további információkért tekintse meg a következőket:

* [Virtuális gépek védelme az Azure Security Centerben](security-center-virtual-machine-recommendations.md)
* [Alkalmazások védelme az Azure Security Centerben](security-center-application-recommendations.md)
* [Az Azure SQL-szolgáltatás az Azure Security Center védelme](security-center-sql-service-recommendations.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
