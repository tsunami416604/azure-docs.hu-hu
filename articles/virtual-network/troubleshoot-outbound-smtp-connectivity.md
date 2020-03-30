---
title: Kimenő SMTP-kapcsolat az Azure-ban | Microsoft dokumentumok
description: Ismerje meg, hogyan háríthatja el a kimenő SMTP-kapcsolattal kapcsolatos problémákat az Azure-ban.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: b8acb50978c5932fe6e6838be86b65c12a0984ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058939"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Kimenő SMTP-kapcsolati problémák elhárítása az Azure-ban

2017. november 15-től kezdődően a közvetlenül a külső tartományokba (például outlook.com és gmail.com) küldött kimenő e-mailek csak a Microsoft Azure bizonyos előfizetési típusai számára érhetők el. A 25-ös TCP-portot használó kimenő SMTP-kapcsolatok le vannak tiltva. (A 25-ös portot elsősorban a nem hitelesített e-mailek kézbesítéséhez használják.)

Ez a viselkedésbeli változás csak az új előfizetések és az új telepítések november 15 óta, 2017.

## <a name="recommended-method-of-sending-email"></a>Az e-mailek küldésének ajánlott módja
Azt javasoljuk, hogy használjon hitelesített SMTP-továbbító szolgáltatásokat (amelyek általában az 587-es vagy 443-as TCP-porton keresztül csatlakoznak, de más portokat is támogatnak) az Azure virtuális gépekről vagy az Azure App Services-ből történő e-mailek küldéséhez. Ezeket a szolgáltatásokat az IP- vagy tartományhírnevének fenntartására használjuk annak érdekében, hogy minimálisra csökkenjen annak a lehetősége, hogy a harmadik fél e-mail szolgáltatók elutasítják az üzenetet. Az ilyen SMTP-továbbítási szolgáltatások közé tartozik, de nem korlátozódik a [SendGridre.](https://sendgrid.com/partners/azure/) Az is lehetséges, hogy rendelkezik egy biztonságos SMTP-továbbító szolgáltatás, amely a helyszínen futó, amely használhatja.

Ezek az e-mail kézbesítési szolgáltatások használata nincs korlátozva az Azure-ban, függetlenül az előfizetés típusától.

## <a name="enterprise-agreement"></a>Nagyvállalati Szerződés
Nagyvállalati szerződés Azure-felhasználók esetében nincs változás a technikai képesség, hogy küldjön e-mailt anélkül, hogy egy hitelesített továbbító. Mind az új, mind a meglévő nagyvállalati szerződéssel rendelkező felhasználók az Azure-beli virtuális gépekről közvetlenül külső e-mail-szolgáltatóknak is próbálkozhatnak, az Azure platformkorlátozásai nélkül. Bár nem garantált, hogy az e-mail-szolgáltatók elfogadják a bejövő e-maileket egy adott felhasználótól, a kézbesítési kísérleteket nem blokkolja az Azure platform a nagyvállalati szerződés-előfizetéseken belüli virtuális gépekhez. Közvetlenül kell együttműködnie az e-mail szolgáltatókkal az egyes szolgáltatókat érinti konkretor üzenetek kézbesítésének vagy spam-szűrésének megoldásához.

## <a name="pay-as-you-go"></a>Utólagos, használatalapú fizetés
Ha 2017. november 15-e előtt regisztrált a használatalapú fizetéses vagy a Microsoft Partner Network előfizetési ajánlatokra, akkor a kimenő e-mailek kézbesítésének kipróbálására vonatkozó technikai képesség nem változik. Továbbra is megpróbálhatja az Azure-beli virtuális gépekkimenő e-mail kézbesítését ezeken az előfizetéseken belül közvetlenül a külső e-mail szolgáltatóknak, az Azure platform korlátozásai nélkül. Ismét, ez nem garantált, hogy az e-mail szolgáltatók elfogadja a bejövő e-mail bármely adott felhasználó, és a felhasználóknak meg kell dolgozni közvetlenül az e-mail szolgáltatók kijavítani az üzenet kézbesítése vagy SPAM szűrési problémák, amelyek magukban foglalják az adott szolgáltatók.

2017. november 15-e után létrehozott használatra annyi- vagy Microsoft Partner Network-előfizetések esetén technikai korlátozások lesznek érvényben, amelyek blokkolják a közvetlenül a virtuális gépekről küldött e-maileket ezeken az előfizetéseken belül. Ha azt szeretné, hogy az Azure virtuális gépekről közvetlenül külső e-mail-szolgáltatóknak küldjön e-mailt (nem hitelesített SMTP-továbbítóhasználatával), kérheti a korlátozás megszüntetését. A kérelmeket a Microsoft saját belátása szerint felülvizsgáljuk és jóváhagyjuk, és csak a további csalás elleni ellenőrzésekután adják meg. Kérés hez nyisson meg egy támogatási esetet a következő problématípus használatával: **A technikai** > **virtuális hálózati** > **kapcsolat** > **nem tud e-mailt küldeni (SMTP/Port 25)**. Győződjön meg arról, hogy adja meg a részleteket arról, hogy miért a központi telepítés kell küldeni e-mail eket közvetlenül a levelezési szolgáltatók használata helyett egy hitelesített továbbító.

Miután egy kiosztóni vagy Microsoft Partner Network-előfizetés kivételt kapott, és a virtuális gépek "Leállítva" & az Azure Portalról "Elindult" lettek, az előfizetésen belüli összes virtuális gép a jövőben mentességet élvez. A mentesség csak a kért előfizetésre vonatkozik, és csak az internetre közvetlenül az internetre irányított virtuálisgép-forgalomra vonatkozik. Az Azure PaaS-szolgáltatásokon, például az [Azure Tűzfalon](https://azure.microsoft.com/services/azure-firewall/) keresztül imitátr a 25-ös porton keresztül nem támogatott.

> [!NOTE]
> A Microsoft fenntartja magának a jogot, hogy visszavonja ezt a mentességet, ha megállapítást nyer, hogy a szolgáltatási feltételek megsértése történt.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Azure Pass, Azure open, oktatási, bizsparkés ingyenes próbaverzió
Ha 2017. november 15., előfizetéseket közvetlenül az e-mail szolgáltatóknak. A korlátozások a visszaélések megelőzése érdekében történnek. A korlátozás megszüntetésére irányuló kérelmek nem adhatók meg.

Ha ezeket az előfizetéstípusokat használja, javasoljuk, hogy használja az SMTP-továbbítási szolgáltatásokat, ahogy azt a cikk korábbi ismertetése vagy az előfizetés típusának módosítása is ismerteti.

## <a name="cloud-service-provider-csp"></a>Felhőszolgáltató (CSP)

Ha azure-erőforrásokat használ a CSP-n keresztül, kérheti a kripta-felhasználót, hogy hozzon létre egy feloldási mentességi kérelmet a Microsoft az Ön nevében, ha egy biztonságos SMTP-továbbító nem használható.

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha továbbra is segítségre van szüksége, lépjen kapcsolatba az [ügyfélszolgálattal,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) hogy gyorsan megoldhassa a problémát a következő problématípus használatával: **Előfizetés-kezelési** probléma típusa: **A 25-ös port e-mail folyamatának engedélyezése.**
