---
title: Erőforrás kivétele Azure Security Center biztonsági javaslatok és a biztonságos pontszám alapján
description: Ismerje meg, hogyan mentesítheti az erőforrásokat a biztonsági javaslatokból és a biztonságos pontszámból
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: b4d043812800bec1070624382755aeb355ea595a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940260"
---
# <a name="exempt-a-resource-from-recommendations-and-secure-score"></a>Erőforrások kizárása a javaslatokból és a biztonságos pontszámból

Minden biztonsági csapat alapvető prioritása annak biztosítása, hogy az elemzők a szervezet számára fontos feladatokra és incidensekre összpontosítsanak. A Security Center számos funkcióval rendelkezik a leginkább rangsorolt információk testreszabásához, és gondoskodik arról, hogy a biztonságos pontszám a szervezet biztonsági döntéseinek érvényes áttekintése legyen. A kivételt képező erőforrások egy ilyen szolgáltatás.

Ha a Azure Security Center biztonsági javaslatát vizsgálja, az elsőként áttekintett információk egyike az érintett erőforrások listája.

Időnként előfordulhat, hogy egy erőforrás jelenik meg, amelyet nem tartalmaz. Lehetséges, hogy a Security Center által nem követett folyamat szervizelte. Vagy lehet, hogy a szervezete úgy döntött, hogy elfogadja az adott erőforrás kockázatát. 

Ilyen esetekben létrehozhat egy kivételi szabályt, és gondoskodhat arról, hogy az erőforrás ne szerepeljen a nem megfelelő állapotú erőforrásokon a jövőben, és nem befolyásolja a biztonságos pontszámot. 

Az erőforrás nem alkalmazhatóként jelenik meg, és az OK "kivételként" jelenik meg a kiválasztott indoklással.

## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Előnézet|
|Árképzési|Ez egy prémium szintű Azure Policy-képesség, amely az Azure Defender-ügyfelek számára ingyenes, díjmentesen használható. Más felhasználók esetében a jövőben a díjak is vonatkozhatnak.|
|Szükséges szerepkörök és engedélyek:|**Előfizetés tulajdonosának** vagy **szabályzatának közreműködője** a kivétel létrehozásához<br>Szabály létrehozásához engedélyekre van szükség a házirendek szerkesztéséhez Azure Policy.<br>További információ: [Azure Policy RBAC engedélyei](../governance/policy/overview.md#rbac-permissions-in-azure-policy).|
|Felhők|![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![No](./media/icons/no-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||


## <a name="create-an-exemption-rule"></a>Kivételi szabály létrehozása

1. A nem megfelelő állapotú erőforrások listájában válassza a kizárni kívánt erőforráshoz tartozó három pontot ("...").

    :::image type="content" source="./media/exempt-resource/create-exemption.png" alt-text="Kivételi lehetőség létrehozása a helyi menüből":::

    Megnyílik a kivétel létrehozása panel.

    :::image type="content" source="./media/exempt-resource/exemption-rule-options.png" alt-text="Kivétel panel létrehozása":::

1. Adja meg a feltételeket, és válasszon egy olyan feltételt, amely miatt az erőforrást mentesíteni kell:
    - **Enyhített** – ez a probléma nem vonatkozik az erőforrásra, mert egy másik eszköz vagy folyamat kezelte, mint a javasolt
    - **Mentesítés** – az erőforrás kockázatának elfogadása
1. Kattintson a **Mentés** gombra.
1. Egy idő után (akár 24 órát is igénybe vehet):
    - Az erőforrás nem befolyásolja a biztonságos pontszámot.
    - Az erőforrás a javaslat részletei lap **nem alkalmazható** lapján jelenik meg.
    - A javaslat részletei oldal tetején található információs szalag a mentesített erőforrások számát sorolja fel:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Mentesített erőforrások száma":::

1. A mentesített erőforrások áttekintéséhez nyissa meg a **nem alkalmazható** lapot.

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Kivétel módosítása":::

    Az egyes kivételek okát a táblázat tartalmazza. 

    A kivételek módosításához vagy törléséhez válassza a három pontot ("...") a látható módon (2).


## <a name="review-all-of-the-exemption-rules-on-your-subscription"></a>Az előfizetésre vonatkozó összes kivételi szabály áttekintése

A kivételi szabályok az Azure Policy használatával hoznak létre kivételt az erőforráshoz a szabályzat-hozzárendelésben.

Azure Policy segítségével nyomon követheti az összes kivételt a **kivételek** oldalán:

:::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Azure Policy kivételének lapja":::



## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan mentesített egy erőforrást egy javaslatból, hogy az ne befolyásolja a biztonságos pontszámot. A biztonságos pontszámról további információt a következő témakörben talál:

- [Biztonságos pontszám Azure Security Center](secure-score-security-controls.md)