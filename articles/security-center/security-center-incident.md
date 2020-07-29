---
title: Biztonsági incidensek kezelése a Azure Security Centerban | Microsoft Docs
description: Ez a dokumentum segít a biztonsági incidensek kezeléséhez Azure Security Center használatával.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: 28a6ea4ed40df909b4d74ff52703babb8e8cd949
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791738"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Biztonsági incidensek kezelése Azure Security Center

A osztályozásakor és a biztonsági riasztások kivizsgálása időigényes lehet a legképzettebb biztonsági elemzők számára is. Sok esetben nehéz megismerni, hogy hol kell kezdeni. 

A Security Center az [elemzések](security-center-detection-capabilities.md) segítségével összekapcsolja az adatokat a különböző [biztonsági riasztások](security-center-managing-and-responding-alerts.md)között. Ezekkel a kapcsolatokkal a Security Center egyetlen nézetet biztosíthat a támadási kampányról és a kapcsolódó riasztásokról, amelyek segítenek megérteni a támadó műveleteit és az érintett erőforrásokat.

Ez az oldal áttekintést nyújt Security Center incidensekről.

## <a name="what-is-a-security-incident"></a>Mi az a biztonsági incidens?

A Security Centerben egy biztonsági incidens az adott erőforráshoz tartozó összes olyan riasztás együttese, amelyek egy [támadási folyamatba](alerts-reference.md#intentions) illeszthetők. Az incidensek a [biztonsági riasztások](security-center-managing-and-responding-alerts.md) lapon jelennek meg. Válasszon ki egy incidenst a kapcsolódó riasztások megtekintéséhez, és szerezzen további információkat.

## <a name="managing-security-incidents"></a>Biztonsági incidensek kezelése

1. A Security Center áttekintése lapon válassza a **biztonsági riasztások** csempét. Az incidensek és a riasztások szerepelnek a felsorolásban. Figyelje meg, hogy a biztonsági incidensek eltérő ikonnal rendelkeznek a biztonsági riasztásokhoz.

    ![Biztonsági incidensek megtekintése](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. A részletek megtekintéséhez válasszon ki egy incidenst. A **biztonsági incidens** oldal további részleteket jelenít meg. 

    [![Válaszadás a Azure Security Center biztonsági incidensekre](media/security-center-incident/incident-details.png)](media/security-center-incident/incident-details.png#lightbox)

    A biztonsági incidens oldal bal oldali ablaktábláján a biztonsági incidensre vonatkozó magas szintű információk láthatók: cím, súlyosság, állapot, tevékenység ideje, leírás és az érintett erőforrás. Az érintett erőforrás mellett láthatja a megfelelő Azure-címkéket. Ezekkel a címkékkel a riasztás kivizsgálása során következtetheti ki az erőforrás szervezeti környezetét.

    A jobb oldali ablaktábla tartalmazza a **riasztások** lapot az incidens részeként korrelált biztonsági riasztásokkal. 

    >[!TIP]
    > Egy adott riasztással kapcsolatos további információkért válassza ki azt. 

    [![Incidens műveletének lapja](media/security-center-incident/incident-take-action-tab.png)](media/security-center-incident/incident-take-action-tab.png#lightbox)

    A **művelet** lapra váltáshoz kattintson a jobb oldali ablaktábla alján található fülre vagy a gombra. Ezen a lapon további műveleteket végezhet, például a következőket:
    - *A fenyegetés enyhítése* – a biztonsági incidens manuális szervizelési lépéseit nyújtja
    - A *jövőbeli támadások megelőzése* – biztonsági javaslatokat biztosít a támadási felület csökkentéséhez, a biztonsági helyzet növeléséhez és a jövőbeli támadások megelőzéséhez
    - *Automatizált válasz indítása* – lehetővé teszi, hogy a logikai alkalmazás a biztonsági incidensre adott válaszként legyen aktiválva
    - *Hasonló riasztások letiltása* – lehetővé teszi a hasonló tulajdonságokkal rendelkező jövőbeli riasztások mellőzését, ha a riasztás nem releváns a szervezet számára 

   > [!NOTE]
   > Ugyanez a riasztás egy incidens részeként is létezhet, valamint önálló riasztásként is látható.

1. Az incidensben lévő fenyegetések javításához kövesse az egyes riasztások által biztosított szervizelési lépéseket.


## <a name="next-steps"></a>További lépések

Ez az oldal a Security Center biztonsági incidensekkel kapcsolatos képességeit ismerteti. A kapcsolódó információkat a következő lapokon tekintheti meg:

* [Veszélyforrások elleni védelem Security Center](threat-protection.md)
* [Biztonsági riasztások Security Center](security-center-alerts-overview.md)
* [A biztonsági riasztások kezelése és a riasztásokra való válaszadás](security-center-managing-and-responding-alerts.md)