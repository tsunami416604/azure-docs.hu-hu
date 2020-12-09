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
ms.openlocfilehash: d2e5996da5a1fe3f5b154d57ee509f25e54e30ac
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862360"
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

Ha az utólagos elszámolású előfizetéshez tartozó 2017. november 15. előtt regisztrált, akkor a kimenő e-mail-kézbesítés kipróbálásának technikai lehetősége nem változik. Az Azure-beli virtuális gépekről a kimenő e-mailek kézbesítését továbbra is kipróbálhatja közvetlenül a külső e-mail-szolgáltatók számára, az Azure platform korlátozásai nélkül. Ismét nem garantált, hogy az e-mail-szolgáltatók elfogadják a bejövő e-maileket bármely adott felhasználótól, és a felhasználóknak közvetlenül kell dolgozniuk az e-mail szolgáltatókkal az üzenetek kézbesítési vagy levélszemét-szűrési problémáinak kijavításához.

A 2017. november 15. után létrehozott utólagos elszámolású előfizetések esetében technikai korlátozások lesznek érvényesek, amelyek letiltják a közvetlenül az előfizetésekben lévő virtuális gépekről küldött e-maileket. Ha azt szeretné, hogy az Azure-beli virtuális gépekről közvetlenül küldjön e-maileket a külső e-mail-szolgáltatók számára (nem hitelesített SMTP-továbbító használatával), és Ön rendelkezik egy fizetési előzményekkel rendelkező fiókkal, akkor a Azure Portal Azure Virtual Network erőforrásának **diagnosztizálás és megoldás** paneljének a **kapcsolata** című részében kérheti le a korlátozást. Ha minősített, az előfizetés engedélyezve lesz, vagy a következő lépésekre vonatkozó utasításokat fog kapni. 

Az utólagos elszámolású előfizetések kivételt képeznek, és a virtuális gépeket leállították és elindították a Azure Portal, az előfizetésben lévő összes virtuális gép mentesül a továbbításra. A kivétel csak a kért előfizetésre vonatkozik, és csak az internethez közvetlenül átirányított VM-forgalomra érvényes.

> [!NOTE]
> A Microsoft fenntartja a jogot arra, hogy visszavonja ezt a kivételt, ha megállapítják, hogy a szolgáltatási feltételek megsértése történt.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-vistual-studio-and-free-trial"></a>MSDN, Azure Pass, Azure in Open licencprogram, oktatás, Azure diákoknak, Visual Studio és ingyenes próbaverzió

Ha a 2017. november 15. után létrehozott MSDN-, Azure Pass-, Azure in Open licencprogram-, oktatási, Azure Student-, ingyenes próbaverziós vagy bármely Visual Studio-előfizetést, technikai korlátozásokkal rendelkezik, amelyek letiltják a virtuális gépeken belül küldött e-maileket az e-mail-szolgáltatók számára. A korlátozásokat a rendszer a visszaélések megelőzése érdekében hajtja végre. Nem kell megadnia a korlátozás eltávolítására irányuló kérelmeket.

Ha ezeket az előfizetési típusokat használja, javasoljuk, hogy használja az SMTP Relay-szolgáltatásokat a jelen cikk korábbi részében ismertetett módon, vagy módosítsa az előfizetés típusát.

## <a name="cloud-service-provider-csp"></a>Felhőalapú szolgáltató (CSP)

Ha az Azure-erőforrásokat a CSP-n keresztül használja, megadhatja a korlátozást az Azure Portal Virtual Network erőforrásának **diagnosztizálás és megoldás** paneljének **kapcsolat** szakaszában. Ha minősített, az előfizetés engedélyezve lesz, vagy a következő lépésekre vonatkozó utasításokat fog kapni.

## <a name="microsoft-partner-network-mpn-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network (MPN), BizSpark Plus vagy Azure Sponsorship

Az Microsoft Partner Network (MPN), a BizSpark Plus vagy a Azure Sponsorship 2017-es, a-es év november 15. után létrehozott előfizetések esetében technikai korlátozásokat kell letiltani, amelyek letiltja a közvetlenül az előfizetésekben található virtuális gépekről küldött e-mail Ha azt szeretné, hogy az Azure-beli virtuális gépekről küldjön e-maileket közvetlenül a külső e-mail-szolgáltatók számára (nem hitelesített SMTP-továbbító használatával), akkor a következő probléma típusának használatával egy támogatási eset megnyitásával teheti meg a kérelmet: **technikai**  >  **Virtual Network** a  >  **kapcsolat**  >  **nem tud e-mailt küldeni (SMTP/port 25)**. Győződjön meg arról, hogy a központi telepítésnek miért kell közvetlenül a levelezési szolgáltatónak küldenie a leveleket a hitelesített továbbító használata helyett. A kérelmeket a Microsoft saját belátása szerint fogja felülvizsgálni és jóváhagyni. A kérelmek csak akkor adhatók meg, ha további csalás elleni csalások is befejeződik. 

Az előfizetés kivétele és a virtuális gépek leállítása és elindítása után a Azure Portalon az előfizetésben lévő összes virtuális gép mentesül. A kivétel csak a kért előfizetésre vonatkozik, és csak az internethez közvetlenül átirányított VM-forgalomra érvényes.

## <a name="restrictions-and-limitations"></a>Korlátozások és korlátozások

- A 25-ös porton keresztüli forgalom az Azure Pásti-szolgáltatások (például [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) ) használatával nem támogatott.

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldásához a következő probléma típusának használatával: **technikai**  >  **Virtual Network** a  >  **kapcsolat**  >  **nem tud e-mailt küldeni (SMTP/port 25)**.
