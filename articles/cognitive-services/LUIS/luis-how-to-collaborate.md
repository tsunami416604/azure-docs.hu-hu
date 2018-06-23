---
title: Más LUIS alkalmazások az Azure-ban lévő közreműködőkkel rendelkeznek együttműködni |} Microsoft Docs
description: Megtudhatja, hogyan együttműködés a más nyelv ismertetése (LUIS) alkalmazások lévő közreműködőkkel rendelkeznek.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/05/2018
ms.author: v-geberr
ms.openlocfilehash: c0451f7621a3c18dbf365f3a03934924c030092f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348955"
---
# <a name="collaborate-with-others-on-language-understanding-luis-apps"></a>Együttműködhet másokkal, a nyelvi ismertetése (LUIS) alkalmazások  

Együttműködhet másokkal a LUIS alkalmazásában együtt. 

## <a name="owner-and-collaborators"></a>Virtuális gép tulajdonosaként és közreműködők
Egy alkalmazás egy tulajdonoshoz rendelkezik, de sok közreműködők is. 

## <a name="add-collaborator"></a>Közreműködő hozzáadása

Engedélyezi az LUIS alkalmazás szerkesztése a közreműködők a **beállítások** lap LUIS alkalmazása, adja meg az e-mailt a közreműködő, majd kattintson a **Hozzáadás közreműködő**.

![Közreműködő hozzáadása](./media/luis-how-to-collaborate/add-collaborator.png)

* Közreműködők bejelentkezhet, és a LUIS alkalmazás szerkesztése az alkalmazás dolgozik egy időben. <!--If a collaborator edits the LUIS app, you see a notification at the top of the browser.-->
* Közreműködők más közreműködők nem vehető fel.

## <a name="set-application-as-public"></a>Nyilvános készlet alkalmazása
Lásd: [nyilvános hozzáférésének végpont](luis-concept-security.md#public-app-endpoint-access) további információt.

### <a name="transfer-of-ownership"></a>A tulajdonjog átruházása
Közben LUIS jelenleg nem támogatja a tulajdonjog átruházása, exportálhatja az alkalmazás, és egy másik LUIS felhasználó importálhatja az alkalmazást. Előfordulhat, hogy különbségek LUIS pontszámok csak a két alkalmazás között. 
