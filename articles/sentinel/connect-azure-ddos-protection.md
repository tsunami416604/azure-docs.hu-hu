---
title: Azure DDoS Protection-adatkapcsolatok összekötése az Azure Sentinel szolgáltatással
description: Megtudhatja, hogyan tölthet be Azure DDoS Protection-adatot az Azure Sentinelbe, így megtekintheti, elemezheti és megvizsgálhatja.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/01/2020
ms.author: yelevin
ms.openlocfilehash: b5d98a834ed4f87fb95a13e666967a7b2d80975d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656054"
---
# <a name="connect-data-from-azure-ddos-protection"></a>Adatok összekapcsolásának Azure DDoS Protection

> [!IMPORTANT]
> Az Azure Sentinel Azure DDoS Protection adatösszekötője jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az elosztott szolgáltatásmegtagadási (DDoS) támadások megpróbálnak kimeríteni egy alkalmazás erőforrásait, így az alkalmazás nem érhető el a legitim felhasználók számára. A DDoS-támadások bármilyen, az interneten keresztül nyilvánosan elérhető végpontot megcélozhatnak. Az [Azure DDoS Protection](../ddos-protection/ddos-protection-overview.md)az alkalmazások kialakításával kapcsolatos ajánlott eljárásokkal kombinálva robusztus védelmet nyújt a DDOS-támadások ellen. Azure DDoS Protection naplókat összekapcsolhatja az Azure Sentinel szolgáltatással, így megtekintheti a munkafüzetek naplófájljait, felhasználhatja egyéni riasztások létrehozásához és a vizsgálatok javítására. 

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedélyekkel kell rendelkeznie az Azure Sentinel munkaterületen.

- Rendelkeznie kell egy konfigurált [Azure DDoS standard Protection-csomaggal](../ddos-protection/manage-ddos-protection.md#create-a-ddos-protection-plan).

- Rendelkeznie kell egy konfigurált [virtuális hálózattal, amelyen engedélyezve van az Azure DDoS standard](../ddos-protection/manage-ddos-protection.md#enable-ddos-protection-for-a-new-virtual-network).

## <a name="connect-to-azure-ddos-protection"></a>Kapcsolódás Azure DDoS Protectionhoz
    
1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Válassza a **Azure DDoS Protection** lehetőséget az adatösszekötők katalógusában, majd az előnézet ablaktáblán válassza az **összekötő megnyitása lapot** .

1. Engedélyezze a **diagnosztikai naplókat** az összes olyan tűzfalon, amelynek a naplóihoz csatlakozni szeretne:

    1. Válassza a **diagnosztikai beállítások megnyitása >** hivatkozást, és válasszon egy **nyilvános IP-cím** erőforrást a listából.

    1. Válassza a **+ diagnosztikai beállítások hozzáadása** elemet.

    1. A **diagnosztikai beállítások** képernyőn:
       - Adjon meg egy nevet a  **diagnosztikai beállítás neve** mezőben.

       - Jelölje be a **küldés log Analyticsra** jelölőnégyzetet. Alatta két új mező jelenik meg. Válassza ki a megfelelő **előfizetést** és **log Analytics munkaterületet** (ahol az Azure Sentinel található).

       - Megjelölheti azoknak a szabályoknak a jelölőnégyzeteit, amelyek naplóit be szeretné állítani. Javasoljuk, hogy a **DDoSProtectionNotifications**, a **DDoSMitigationFlowLogs** és a **DDoSMitigationReports**.

    1. Kattintson a képernyő felső részén található **Mentés** gombra. Ismételje meg ezt a folyamatot minden olyan további tűzfalnál (nyilvános IP-cím), amelyhez engedélyezte a DDoS Protection szolgáltatást.

1. Ha Azure DDoS Protection riasztások esetében szeretné használni a Log Analytics vonatkozó sémát, keresse meg a **AzureDiagnostics**.

> [!NOTE]
>
> Ezzel az adatösszekötővel a kapcsolati állapot mutatói (az adatösszekötők katalógusában és az adattípusok neve melletti kapcsolati ikonokban lévő színcsíkok) csak akkor jelennek *meg, ha* az adatok betöltése az elmúlt két hétben egy bizonyos pontján történik. Ha két hét telt el az adatfeldolgozás nélkül, az összekötő leválasztva jelenik meg. Minél több információ érkezik, a *csatlakoztatott* állapot vissza fog térni.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakozhat Azure DDoS Protection naplókat az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).