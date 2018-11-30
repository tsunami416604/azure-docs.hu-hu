---
title: Az Azure-ban kimenő SMTP-kapcsolatának hibaelhárítása |} A Microsoft Docs
description: Ismerje meg, hogyan háríthatók el a problémákat a kimenő SMTP-kapcsolat az Azure-ban.
services: virtual-network
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 34d42f9987303c1381584ae4b2991a8f30a67ed5
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52618959"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Kimenő SMTP kapcsolati hibák elhárításához, az Azure-ban

2017. November 15-én kezdődő kimenő e-mailben küldött üzenetekben meg közvetlenül a külső tartományt (például Outlook.com-os és a gmail.com) egy virtuális gép (VM) számára elérhetővé válnak csak bizonyos típusú előfizetésessel Microsoft Azure-ban. Letiltott kimenő SMTP-kapcsolatot, amely 25-ös TCP-port használatára. (25-ös porton elsősorban a nem hitelesített e-mailben kézbesítésre.)

Viselkedés csak érvényes új előfizetés és az új központi telepítéseknél, 2017. November 15. óta.

## <a name="recommended-method-of-sending-email"></a>E-mailt küld az ajánlott módszer
Azt javasoljuk, hogy hitelesített SMTP-továbbítási szolgáltatásokkal (amely általában 587 vagy 443-as TCP-porton keresztül csatlakozni, de más portok túl támogatja) használatával az Azure virtuális gépekről, vagy az Azure App Services e-mailt. Ezek a szolgáltatások IP- vagy tartományi megbízhatósági besorolása minimalizálása érdekében, hogy a külső e-mail-szolgáltatók elutasítja-e az üzenet foglalkozik a gondolattal megőrzésére szolgálnak. Ilyen SMTP-továbbítási szolgáltatások közé tartozik, de nem korlátozódik [SendGrid](http://sendgrid.com/partners/azure/). Lehetőség arra is van egy biztonságos SMTP-továbbítási szolgáltatás, amely a helyszínen fut, amelyet használhat.

E-mailek kézbesítési szolgáltatások használatba az Azure-ban, nincs korlátozva az előfizetés típusától függetlenül.

## <a name="enterprise-agreement"></a>Nagyvállalati szerződés
A nagyvállalati szerződés program Azure-felhasználók számára nem történik változás a műszaki teszi e-mail küldését hitelesített-továbbítás használata nélkül. Új és meglévő nagyvállalati szerződés felhasználók közvetlenül a külső e-mail-szolgáltatónak korlátozása nélkül az Azure platform az Azure-beli virtuális gépekről származó kimenő e-mailek kézbesítési próbálhatja ki. Bár nem garantált, hogy a levelező szolgáltatók elfogadják az adott felhasználó a bejövő e-mailek, kézbesítési kísérletek során nem tiltható le az Azure platform által a virtuális gépek nagyvállalati előfizetői szerződéssel belül. Az ügyfelek közvetlenül e-mail-szolgáltatók megoldásához bármely az üzenetek kézbesítését, vagy a LEVÉLSZEMÉT-szűrési adott szolgáltatók érintő problémákat kell.

## <a name="pay-as-you-go"></a>Utólagos, használatalapú fizetés
Ha 2017. November 15. előtt való regisztráció a használatalapú fizetés vagy biztosít a Microsoft Partner Network-előfizetéssel, nem lesznek műszaki képes kimenő e-mailek kézbesítési próbálja meg nem változik. Fogja tudni kimenő e-mailek kézbesítési Azure-beli virtuális gépekről származó belül ezeket az előfizetéseket közvetlenül a külső e-mail-szolgáltatót az Azure platform korlátozása nélkül próbálja folytatni. Újra nem garantált, hogy a levelező szolgáltatók elfogadják a bejövő e-mailek, az adott felhasználó, és a felhasználóknak kell közvetlenül Együttműködünk a megoldásához bármely az üzenetek kézbesítését, vagy a LEVÉLSZEMÉT-szűrési adott szolgáltatók érintő problémák e-mail-szolgáltatók.

2017. November 15. után létrehozott használatalapú fizetéses vagy Microsoft Partner Network előfizetések lesz műszaki korlátozások, amelyek közvetlenül a virtuális gépek ezeket az előfizetéseket küldött e-mailek letiltása. Ha azt szeretné, hogy e-mailt az Azure virtuális gépek közvetlenül a külső e-mail-szolgáltatónak (nem a hitelesített SMTP-továbbítás használatával) lehetővé teszi, hogy egy kérelem a korlátozás feloldását. Kérelmek fog tekintse át és hagyja jóvá, a Microsoft saját belátása szerint értékeli, és azok kapja, csak a visszaélések kiküszöbölésére szolgáló megfelelő további ellenőrzések végrehajtott után. A kérést, nyissa meg egy támogatási esetet használatával a következő probléma típusa: **technikai** > **virtuális hálózat** > **kapcsolat**  >  **Nem lehet elküldeni az e-mailben (SMTP-portját 25)**. Ellenőrizze, hogy miért a központi telepítés rendelkezik az e-mail küldése közvetlenül a levelezési szolgáltatónak hitelesített-továbbítás használata helyett részleteit.

Ha egy használatalapú fizetéses vagy Microsoft Partner Network előfizetés ki van zárva, virtuális gépek adott előfizetésen belül csak annak kötelezettsége alól a jövőben.

> [!NOTE]
> A Microsoft fenntartja a jogot visszavonásához ezt a kivételt, ha megállapította, hogy a szolgáltatási feltételek megsértése történt.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>Az MSDN, Azure Pass, Azure in Open, Education, BizSpark és ingyenes próbaverzió
Ha az Open, Education, BizSpark, Azure sponsorship ajánlattal, Azure tanulók, ingyenes próbaverzió vagy minden Visual Studio-előfizetéssel, 2017. November 15-létrehozta az MSDN, Azure Pass, Azure, akkor műszaki korlátozások, hogy ezek belüli virtuális gépek által küldött letiltása az e-mail előfizetések közvetlenül az e-mail-szolgáltatók. A korlátozásokat a visszaélések megelőzése érdekében kell elvégezni. Ez a korlátozás feloldását nincsenek kérelmek fog kapni.

Ezek a típusok előfizetés használata esetén, hogy javasoljuk, hogy az SMTP-továbbítási szolgáltatások használata ebben a cikkben korábban ismertetett módon.

## <a name="cloud-service-provider-csp"></a>Felhőszolgáltató (CSP)

Azure-erőforrások CSP keresztül használja, ha tudja hozzon létre egy támogatási kérést a választott keresztül a CSP, és kérheti a CSP-feloldása eset létrehozása az Ön nevében, ha egy biztonságos SMTP-továbbítás nem használható.

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
