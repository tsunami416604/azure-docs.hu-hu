---
title: Az Azure Security Centerben a hálózat védelme |} Microsoft Docs
description: Ez a dokumentum címek, amelyek segítenek az Azure Security Center javaslatait az Azure-hálózat védelme és maradnak meg a biztonsági házirendeknek megfelelően.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: terrylan
ms.openlocfilehash: 00b715507a7c3a4d784b800e7bf0c700f6ea6ff1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866477"
---
# <a name="protecting-your-network-in-azure-security-center"></a>Az Azure Security Centerben a hálózat védelme
Az Azure Security Center elemzi az Azure-erőforrások biztonsági állapotát. Amikor a Security Center a potenciális biztonsági hiányosságokat azonosít, ajánlásokat, amely végigvezeti Önt a szükséges szabályozási folyamatán hoz létre.  Javaslat alkalmazható azonban az Azure erőforrás típusa: virtuális gépek (VM), hálózati, SQL és alkalmazások.

Ez a cikk foglalkozik a hálózattal kapcsolatos javaslatokat.  Hálózati javaslatok központ következő generációs tűzfal, a hálózati biztonsági csoportok, az konfigurálása bejövő forgalomra vonatkozó szabályokat és több.  Az alábbi táblázatban használja hivatkozásként segít megérteni a rendelkezésre álló hálózati ajánlásokat, és mindegyiket egy funkciója Ha alkalmazása.

## <a name="available-network-recommendations"></a>Rendelkezésre álló hálózati javaslatok
| Ajánlás | Leírás |
| --- | --- |
| [Újgenerációs tűzfal hozzáadása](security-center-add-next-generation-firewall.md) |Javasolja, hogy növelje a biztonsági védelmet egy Microsoft-partner adja hozzá a következő generációs tűzfal (NGFW). |
| [Csak az újgenerációs tűzfalon keresztül haladjon a forgalom](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Javasolja, hogy konfigurálja a hálózati biztonsági csoport (NSG) szabályok kényszerítheti a bejövő forgalom a virtuális géphez a NGFW keresztül. |
| [Hálózati biztonsági csoportok engedélyezi az alhálózatokat vagy a virtuális gépek](security-center-enable-network-security-groups.md) |Az alhálózatok és virtuális gépek NSG-k engedélyezését javasolja. |
| [Internetre irányuló végpont-en keresztüli hozzáférés korlátozása](security-center-restrict-access-through-internet-facing-endpoints.md) |Azt javasolja, hogy az NSG-ket konfigurálhat a bejövő forgalomra vonatkozó szabályokat. |

## <a name="see-also"></a>Lásd még:
Egyéb Azure erőforrástípusok vonatkozó javaslatok kapcsolatos további tudnivalókért olvassa el a következőket:

* [Az Azure Security Centerben a virtuális gépek védelme](security-center-virtual-machine-recommendations.md)
* [Az alkalmazások az Azure Security Centerben védelme](security-center-application-recommendations.md)
* [Az Azure Security Centerben az Azure SQL-szolgáltatás védelme](security-center-sql-service-recommendations.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
