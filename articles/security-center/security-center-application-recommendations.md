---
title: Védi az alkalmazások az Azure Security Centerben |} Microsoft Docs
description: Ez a dokumentum címek, amelyek segítenek az Azure Security Center javaslatait az Azure-alkalmazások védelmét, és maradnak meg a biztonsági házirendeknek megfelelően.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: b5fc7a9e-24b1-415f-b3b5-62a53f5dd424
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2016
ms.author: terrylan
ms.openlocfilehash: b5bc02082fa8c2681aa67910729870921fec329d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866267"
---
# <a name="protecting-your-applications-in-azure-security-center"></a>Az alkalmazások az Azure Security Centerben védelme
Az Azure Security Center elemzi az Azure-erőforrások biztonsági állapotát. Amikor a Security Center a potenciális biztonsági hiányosságokat azonosít, ajánlásokat, amely végigvezeti Önt a szükséges szabályozási folyamatán hoz létre.  Javaslat alkalmazható azonban az Azure erőforrás típusa: virtuális gépek (VM), hálózati, SQL és alkalmazások.

Ez a cikk foglalkozik javaslatok alkalmazásra érvényes.  Alkalmazás javaslatok center körül webalkalmazási tűzfal központi telepítését.  Az alábbi táblázatban használja hivatkozásként segít megérteni a rendelkezésre álló ajánlásokat, és mindegyiket egy funkciója Ha alkalmazása.

## <a name="available-application-recommendations"></a>Rendelkezésre álló javaslatok
| Ajánlás | Leírás |
| --- | --- |
| [Webalkalmazási tűzfal hozzáadása](security-center-add-web-application-firewall.md) |Javasolja, hogy telepít egy webalkalmazási tűzfal (WAF) webszolgáltatási végpontok. WAF ajánlás bármely nyilvános felé néző IP-cím (példány szint IP vagy terhelés eloszlik IP), amely rendelkezik egy társított hálózati biztonsági csoportot a nyissa meg a bejövő webes portokkal (80,443) jelenik meg.</br></br>A Security Center javasolja, hogy egy WAF a webes alkalmazások, virtuális gépek és az App Service Environment-környezet célzó támadások elleni védelemre való ellátásához. Az App Service környezetben (ASE) van egy [prémium](https://azure.microsoft.com/pricing/details/app-service/) terv lehetőséget az Azure App Service egy teljesen elkülönített és dedikált környezetben történő biztonságos futtatására az Azure App Service apps biztosító szolgáltatás. ASE kapcsolatos további tudnivalókért tekintse meg a [App Service környezetben dokumentáció](../app-service/environment/intro.md).</br></br>Adja hozzá ezeket az alkalmazásokat a meglévő WAF-központitelepítések védelmet biztosíthat a Security Center több webalkalmazás. |
| [Alkalmazásvédelem véglegesítése](security-center-add-web-application-firewall.md#finalize-application-protection) |Egy WAF konfigurációjának befejezéséhez forgalom WAF készülékre át irányítva. Ez a javaslat a következő befejezi a szükséges telepítőfájlokat módosításokat. |

## <a name="see-also"></a>Lásd még:
Egyéb Azure erőforrástípusok vonatkozó javaslatok kapcsolatos további tudnivalókért olvassa el a következőket:

* [Az Azure Security Centerben a virtuális gépek védelme](security-center-virtual-machine-recommendations.md)
* [Az Azure Security Centerben a hálózat védelme](security-center-network-recommendations.md)
* [Az Azure Security Centerben az Azure SQL-szolgáltatás védelme](security-center-sql-service-recommendations.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
