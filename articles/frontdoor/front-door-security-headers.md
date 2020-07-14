---
title: Azure Front Door
description: Ebből a cikkből megtudhatja, hogyan konfigurálhat egy biztonsági fejlécet az Azure-beli előtérben található Rules Engine használatával
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 6/22/2020
ms.author: mebeatty
ms.openlocfilehash: d489186dc1c012fe8c181f17e00bcdb999e230dd
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232134"
---
# <a name="add-security-headers-with-rules-engine"></a>Biztonsági fejlécek hozzáadása a szabálymotorral

A biztonsági fejlécek megvalósításával megakadályozhatja a böngészőalapú biztonsági réseket, például a HTTP Strict-Transport-Security (HSTS), az X-XSS-Protection, a Content-Security-Policy vagy az X-frame-Options használatát. A biztonság-alapú attribútumok cookie-kkal is meghatározhatók.

Az alábbi példa bemutatja, hogyan adhat hozzá egy Content-Security-Policy fejlécet az összes olyan bejövő kérelemhez, amely megfelel a szabályok motor-konfigurációjának útvonalán megadott elérési útnak. Itt csak a megbízható webhelyről származó szkripteket engedélyezzük **https://apiphany.portal.azure-api.net** az alkalmazáson való futtatáshoz.

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Tartalom-biztonsági házirend fejlécének hozzáadása a Azure Portal

1. Az adott szabály létrehozása előtt Ismerje meg, hogyan [hozhat létre egy bejárati ajtót](quickstart-create-front-door.md) , vagy hogyan [hozhat létre egy szabály-végrehajtót](front-door-tutorial-rules-engine.md) , ha első alkalommal használja a AFD vagy a Rules Engine funkciót.

2. Adjon meg új szabályt a **Hozzáadás** lehetőséggel. Adja meg a szabály nevét, majd kattintson a **műveleti**  >  **Válasz fejlécének**hozzáadása lehetőségre.

3. Állítsa be úgy a kezelőt **, hogy ezt** a fejlécet adja hozzá az útvonal összes bejövő kérelmének válaszként.

4. Adja hozzá a fejléc neve: **Content-Security-Policy** , és határozza meg, hogy a fejlécnek milyen értékeket kell elfogadnia. Ebben a forgatókönyvben a *"script-src" saját https://apiphany.portal.azure-api.net * lehetőséget választjuk.

5. Ha hozzáadta az összes szabályt, amelyet szeretne konfigurálni, ne felejtse el, hogy a kívánt útvonalra lépjen, és a szabályok motorjának beállításait az útválasztási szabályhoz rendeli. Ez a lépés a szabály működésének engedélyezéséhez szükséges. 

![portál minta](./media/front-door-rules-engine/rules-engine-security-header-example.png)

> [!NOTE]
> Ebben a forgatókönyvben nem vettünk fel [egyezési feltételeket](front-door-rules-engine-match-conditions.md) a szabályhoz. Az útvonal-szabályban definiált elérési útra megegyező összes bejövő kérelem esetében ez a szabály lesz alkalmazva. Ha azt szeretné, hogy csak a kérelmek egy részhalmazára vonatkozzon, ügyeljen arra, hogy a szabályhoz az adott egyeztetési feltételeket adja hozzá.


## <a name="next-steps"></a>További lépések

- További információ a [AFD-szabályok motorról](front-door-rules-engine.md). 
- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
- További információ a szabályok motorjának [egyeztetési feltételeiről](front-door-rules-engine-match-conditions.md)
- További részletekért tekintse meg a AFD-szabályok motor [CLI-referenciáját](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest). 
- További részletekért tekintse meg a AFD-szabályok motor [PowerShell-referenciáját](https://docs.microsoft.com/powershell/module/az.frontdoor/?view=azps-3.8.0). 
