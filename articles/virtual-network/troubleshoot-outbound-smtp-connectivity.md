---
title: A kimenő SMTP-kapcsolatok hibáinak megoldása az Azure-ban | Microsoft Docs
description: Ismerje meg a javasolt módszert az e-mailek küldéséhez és az Azure-beli kimenő SMTP-kapcsolatok hibáinak elhárításához.
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
ms.openlocfilehash: e58f498ca254378354fb0e9e21d04fe9969588c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87265262"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>A kimenő SMTP-kapcsolati problémák elhárítása az Azure-ban

A (z) 2017. november 15-én kezdődően a kimenő e-mailek, amelyeket közvetlenül a külső tartományokra (például outlook.com és gmail.com) küldenek egy virtuális gépről (VM), csak az egyes előfizetési típusok számára érhetők el Microsoft Azureban. A 25-ös TCP-portot használó kimenő SMTP-kapcsolatok blokkolva lettek. (A 25-ös port elsődlegesen a nem hitelesített e-mail-küldéshez használatos.)

Ez a változás csak az új előfizetésekre és az új üzemelő példányokra vonatkozik, a 2017. november 15. óta.

## <a name="recommended-method-of-sending-email"></a>E-mailek küldésének ajánlott módja
Javasoljuk, hogy használjon hitelesített SMTP Relay-szolgáltatásokat (amelyek jellemzően a 587-es vagy a 443-es TCP-porton keresztül csatlakoznak, de más portokat is támogatnak) az Azure-beli virtuális gépekről vagy az Azure App Services-ról Ezek a szolgáltatások az IP-címek és a tartományok hírnevének fenntartására szolgálnak, hogy a harmadik féltől származó e-mail-szolgáltatók ne utasítsa el az üzenetet. Az ilyen SMTP-továbbító szolgáltatások közé tartoznak a [SendGrid](https://sendgrid.com/partners/azure/), de nem korlátozódnak. Az is lehetséges, hogy rendelkezik egy olyan biztonságos SMTP Relay szolgáltatással, amely a helyszínen fut.

Az e-mailes kézbesítési szolgáltatások használata nem korlátozódik az Azure-ban, az előfizetés típusától függetlenül.

## <a name="enterprise-agreement"></a>Nagyvállalati Szerződés
Nagyvállalati Szerződés Azure-felhasználók esetében a technikai képesség nem változik, hogy hitelesített továbbító használata nélkül küldjön e-mailt. Az új és a meglévő Nagyvállalati Szerződés felhasználók is kipróbálhatják a kimenő e-mailek küldését az Azure-beli virtuális gépekről az Azure-platform korlátozásai nélkül. Habár nem garantált, hogy az e-mail-szolgáltatók elfogadják a bejövő e-maileket az adott felhasználótól, az Azure platform nem blokkolja a Nagyvállalati Szerződés előfizetéseken belüli virtuális gépekre vonatkozó kézbesítési kísérleteket. Az üzenetek kézbesítésével vagy a levélszemét szűrésével kapcsolatos olyan problémák megoldásához, amelyek bizonyos szolgáltatókra vonatkoznak, közvetlenül kell működnie az e-mail-szolgáltatókkal.

## <a name="pay-as-you-go"></a>Utólagos, használatalapú fizetés
Ha az utólagos elszámolású vagy Microsoft Partner Network előfizetési ajánlatok esetében a 2017. november 15. előtt regisztrált, akkor a kimenő e-mailek kézbesítésének technikai lehetősége nem változik. Az Azure-beli virtuális gépekről a kimenő e-mailek kézbesítését továbbra is kipróbálhatja közvetlenül a külső e-mail-szolgáltatók számára, az Azure platform korlátozásai nélkül. Ismét nem garantált, hogy az e-mail-szolgáltatók elfogadják a bejövő e-maileket bármely adott felhasználótól, és a felhasználóknak közvetlenül kell dolgozniuk az e-mail szolgáltatókkal az üzenetek kézbesítési vagy levélszemét-szűrési problémáinak kijavításához.

A 2017. november 15. után létrehozott utólagos elszámolású vagy Microsoft Partner Network előfizetésekhez technikai korlátozások vonatkoznak, amelyek letiltják a közvetlenül az előfizetésekben lévő virtuális gépekről küldött e-maileket. Ha azt szeretné, hogy az Azure-beli virtuális gépekről érkező e-mailek közvetlenül a külső e-mail-szolgáltatók számára is elküldhetők legyenek (nem hitelesített SMTP-továbbító használatával), a korlátozás eltávolítására vonatkozó kérést kérhet. A kérelmeket a Microsoft saját belátása szerint értékeli és hagyja jóvá, és a rendszer csak a további csalás elleni ellenőrzés után kapja meg őket. A kérelem elvégzéséhez a következő probléma típusának használatával nyisson meg egy támogatási esetet: **technikai**  >  **Virtual Network**a  >  **kapcsolat**  >  **nem tud e-mailt küldeni (SMTP/port 25)**. Győződjön meg arról, hogy a központi telepítésnek miért kell közvetlenül a levelezési szolgáltatónak küldenie a leveleket a hitelesített továbbító használata helyett.

Az utólagos elszámolású vagy Microsoft Partner Network előfizetések kivételt képeznek, és a virtuális gépeket a Azure Portal leállították & "elindult", az előfizetésben lévő összes virtuális gépet mentesíti a rendszer. A kivétel csak a kért előfizetésre érvényes, és csak a közvetlenül az internethez irányított virtuális gépek forgalmára vonatkozik. A 25-ös porton keresztüli forgalom az Azure Pásti-szolgáltatásokon keresztül, például a [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) nem támogatott.

> [!NOTE]
> A Microsoft fenntartja a jogot arra, hogy visszavonja ezt a kivételt, ha megállapítják, hogy a szolgáltatási feltételek megsértése történt.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Azure Pass, Azure in Open licencprogram, Education, BizSpark és ingyenes próbaverzió
Ha az MSDN, az Azure Pass, az Azure in Open licencprogram, az oktatás, a BizSpark, a Azure Sponsorship, az Azure Student, az ingyenes próbaverzió vagy bármely Visual Studio-előfizetés a 2017. november 15. után lett létrehozva, akkor technikai korlátozásokkal fog rendelkezni, amelyek letiltják a virtuális gépeken belül küldött e-maileket az e-mail-szolgáltatóknak A korlátozásokat a rendszer a visszaélések megelőzése érdekében hajtja végre. Nem kell megadnia a korlátozás eltávolítására irányuló kérelmeket.

Ha ezeket az előfizetési típusokat használja, javasoljuk, hogy használja az SMTP Relay-szolgáltatásokat a jelen cikk korábbi részében ismertetett módon, vagy módosítsa az előfizetés típusát.

## <a name="cloud-service-provider-csp"></a>Felhőalapú szolgáltató (CSP)

Ha az Azure-erőforrásokat a CSP-n keresztül használja, kérheti a CSP-t, hogy hozzon létre egy tiltás feloldására irányuló kérelmet a Microsoftnál az Ön nevében, ha nem használható biztonságos SMTP-továbbító.

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldásához a következő probléma beírásával: **előfizetés-kezelési** probléma típusa: **kérelem a 25-ös porton keresztüli adatforgalom engedélyezéséhez**.
