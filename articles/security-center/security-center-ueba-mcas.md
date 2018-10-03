---
title: Fenyegetések észlelése az Azure Security Centerben |} A Microsoft Docs
description: " Fenyegetések észlelése és a kártevő a Microsoft Cloud App Security az Azure Security Centerrel való integrálásával. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: 2a747bdd8de41283b9cba1e40e2652aa826e9c60
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48044450"
---
# <a name="ueba-for-azure-resources-and-users"></a>Azure-erőforrások és -felhasználók UEBA-elemzése 

A Microsoft Cloud App Securityvel ahhoz, hogy az Ön felhasználói és elemzését (UEBA) az Azure-erőforrások és a felhasználók számára (az Azure-tevékenység) alapuló riasztások partnerek az Azure Security Center. Ezek a riasztások a felhasználók viselkedésének rendellenességek észlelése, és felhasználói és az entitások viselkedésének elemzése és a machine learning (gépi tanulás), így azonnal futtathatja a fejlett fenyegetésészlelés az előfizetések tevékenységek között. Automatikusan engedélyezettek, mert az új anomáliadetektálási észlelések azonnali eredményeket azáltal, hogy azonnali észlelések, a felhasználók és az előfizetéséhez tartozó erőforrások között számos tevékenységbeli anomáliákat célzó adja meg. Ezek a riasztások Ezenkívül használhatja a további adatokat, hogy már megtalálható a Microsoft Cloud App Security motorban segítségével felgyorsíthatja a vizsgálat során, és tartalmazhat elleni folyamatos fenyegetések. 

> [!NOTE]
> Azure Security Center, mellyel tárolhatja az ügyféladatok biztonsági másolatát, gyűjtött vagy (pl. virtuális gép vagy Azure Active Directory-bérlő) egy ügyfél erőforráshoz kapcsolódó: (a) a erőforrásként ugyanabban a földrajzi említett helyeken, kivéve ahol Microsoft még nem üzembe helyezése az Azure Security Center, amely esetben egy másolatot az ilyen adatok tárolni fogja az Egyesült Államokban; és (b) az Azure Security Center feldolgozni az adatokat egy másik, a Microsoft Online szolgáltatás használ, ahol az ilyen adatokat más Online szolgáltatás földrajzi hely meghatározásának szabályainak megfelelően tárolja.
>

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Előfeltételek

- Egy érvényes aktiválva [Microsoft Cloud App Security-licenc](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)
- [A Security Center standard szintű csomag](https://azure.microsoft.com/pricing/details/security-center/)
 
## <a name="threat-detection-alerts"></a>Fenyegetésészlelési riasztások

A Security Center támogatja a Cloud App Security rendellenességek észlelése riasztások, például:

**Lehetetlen utazás**
-  Az észlelés azonosítja a két felhasználói tevékenység (az egy vagy több munkamenet) származó földrajzilag távoli helyekről időnél rövidebb idő alatt, vett volna a felhasználót, hogy az első helyen a másodpercet jelző utazik hogy egy másik felhasználó használja ugyanazokat a hitelesítő adatokat. Az észlelés a gépi tanulási algoritmus, amely figyelmen kívül hagyja a nyilvánvaló "vakriasztásokat" a lehetetlen utazás feltételt, például a VPN-EK és a szervezet más felhasználói által rendszeresen használt helyek hozzájáruló használja. Az észlelés egy hét nap, mely során egy új felhasználók tevékenységi mintáit megtanulja betanulási időszakra van.

**Ritkán használt országból indított tevékenység**
- Az észlelés múltbeli tevékenységet helyek meghatározásához az új és a ritka figyelembe veszi. Az anomáliadetektálási motor a fenti helyeken, a szervezet felhasználói által használt információkat tárol. Riasztást vált ki, hogy a rendszer nem nemrég vagy soha nem látogatta meg a szervezet bármely felhasználója helyről egy tevékenység esetén. 

**Névtelen IP-címekről indított tevékenység**
- Az észlelés azonosítja, hogy a felhasználó volt aktív, a névtelen proxy IP-címként azonosított IP-címet. Ezek proxyk, akik az eszköz IP-címének elrejtésére és rosszindulatú tevékenységek végrehajtására használhatók. Az észlelés kihasználja a gépi tanulási algoritmus, amely csökkenti a "vakriasztásokat", például a szervezet felhasználói által széles körben használt gépeltünk címkézett IP-címek.
 
  ![Fenyegetés-észlelési riasztás](./media/security-center-ueba-mcas/security-center-mcas-alert.png)

## <a name="disabling-threat-detection-alerts"></a>Fenyegetésészlelési riasztások letiltása

Ezek a riasztások alapértelmezés szerint engedélyezve vannak, de letilthatja azokat:

1. Válassza ki a Security Center panel **biztonsági házirend**. Az előfizetés használatára szeretne átállni, kattintson a **beállításainak szerkesztése**.
2.  Kattintson a **Fenyegetésészlelés**.
3. Alatt **Integrációk engedélyezése**, törölje a jelet **engedélyezése a Microsoft Cloud App Security az adatok eléréséhez**, és kattintson a **mentése**.

   ![Fenyegetés-észlelési riasztás](./media/security-center-ueba-mcas/security-center-mcas-optout.png)

> [!NOTE]
> Egy hét nap, mely nem minden anomáliadetektálási során fenyegetésészlelési riasztások aktiválódnak betanulási időszakra van. Ezt követően minden egyes munkamenetnél a rendszer összehasonlítja a tevékenységet, amikor a felhasználó volt aktív, IP-címek, eszközök, az elmúlt hónapban, és ezeknek a tevékenységeknek a kockázati pontszám észlelt stb. Ezek az észlelések a gépi tanulási anomáliadetektálási motorral, hogy a környezet és az eseményindítók riasztást küld, amely a szervezet tevékenység ismert egy megtanult profilok részét képezik. Ezek az észlelések is használhatja a machine learning-algoritmusok készült profil, a felhasználók és a bejelentkezés a minta vakriasztások csökkentésének érdekében.
>
  
## <a name="next-steps"></a>További lépések
Ez a cikk bemutatta, hogy az Azure Security Center fenyegetésészlelési. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Azure Security Center: gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) –Tanulja meg az Azure-erőforrások állapotfigyelésének módját.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
