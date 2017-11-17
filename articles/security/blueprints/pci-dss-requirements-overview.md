---
title: "Az Azure támogatási feldolgozási tervezetének - magas szintű áttekintése"
description: "Az Azure fizetési feldolgozási tervezetének - ügyfél felelősségi mátrix (áttekintés)"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 23cf68d8-bebd-4ac4-a194-39e052281c0e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 49acce706f09fe08b257ce8a8554de5da20060a1
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="pci-dss-requirements---high-level-overview"></a>PCI DSS követelmények – magas szintű áttekintése

A Payment Card adatvédelmi szabvány (PCI DSS) célja ösztönözzék és kártya tulajdonosát adatok biztonságáról és fokozzák azonos adatokat biztonsági intézkedéseket széles körű bevezetése globálisan. PCI DSS fiók adatait védő műszaki és működési követelmények alapterv biztosít. PCI DSS minden részt vevő entitásokat fizetési kártya feldolgozási, beleértve a kereskedők, a processzorok, a együttesen, a kiállítók és a szolgáltatók vonatkozik. PCI DSS is vonatkozik az összes végző más entitásokkal tárolására, feldolgozni vagy kártya tulajdonosát (CHD) és/vagy bizalmas hitelesítési adatok (SAD) továbbítja. Az alábbiakban van a 12 PCI DSS követelmények magas szintű áttekintését.

> [!NOTE]
> Ezeket a követelményeket határozzák meg a [Payment Card Industry (PCI) biztonsági szabványok Tanács](https://www.pcisecuritystandards.org/pci_security/) részeként a [PCI adatok biztonsági szabvány (DSS) 3.2-es verziójú](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Tekintse meg a PCI DSS tesztelési eljárások és az egyes követelményekhez útmutatót olvashat.

|   |   |
|---|---|
| **Hozza létre és karbantartása a biztonságos<br/>hálózati és** | 1. [Telepítheti és karbantarthatja a tűzfal beállításait a kártya tulajdonosát adatok védelme](pci-dss-requirement-1-firewall.md)<br/><br/> 2. [Ne használjon szállító által biztosított alapértelmezett rendszer jelszavakat és egyéb biztonsági paraméterek](pci-dss-requirement-2-password.md) |  
| **Kártya tulajdonosát adatok védelme** | 3. [Tárolt kártya tulajdonosát adatok védelme](pci-dss-requirement-3-chd.md)<br/><br/> 4. [Adatátvitel kártya tulajdonosát titkosítása nyitott, nyilvános hálózatokon](pci-dss-requirement-4-encryption.md) |
| **A biztonsági rés karbantartása<br/>felügyeleti Program** | 5. [Minden rendszer kártevő szoftverek elleni védelmét, és rendszeresen frissíti a Programok telepítése és a víruskereső szoftver](pci-dss-requirement-5-malware.md)<br/><br/> 6. [Fejlesztéséhez és biztonságos rendszerek és alkalmazások karbantartása](pci-dss-requirement-6-secure-system.md) |
| **Erős hozzáférés bevezetéséhez<br/>ellenőrzési intézkedések** | 7. [Kártya tulajdonosát adatok által az üzleti szükséges mértékű ismeretek hozzáférés korlátozása](pci-dss-requirement-7-access.md)<br/><br/> 8. [Azonosíthatják és hitelesíthetik a hozzáférés-összetevőkkel](pci-dss-requirement-8-identity.md) <br/><br/> 9. [Kártya tulajdonosát adatok való fizikai hozzáférés korlátozása](pci-dss-requirement-9-physical-access.md) |
| **Rendszeresen figyelje és<br/>hálózatok tesztelése** | 10. [Követheti és figyelheti az összes hálózati erőforrásokhoz és adatokhoz történő hozzáférés kártya tulajdonosát](pci-dss-requirement-10-monitoring.md) <br/><br/> 11. [Rendszeresen tesztelniük a biztonsági rendszerek és folyamatok](pci-dss-requirement-11-testing.md) |
| **Az adatok karbantartása<br/>biztonsági házirend** | 12. [Egy házirendet, amely információ-biztonság személyek karbantartása](pci-dss-requirement-12-policy.md) |

