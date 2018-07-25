---
title: Együttműködhet más közreműködőkkel párhozamosan LUIS-alkalmazások az Azure-ban |} A Microsoft Docs
description: Ismerje meg, hogyan lehet másokkal együttműködve segítségével más közreműködőkkel párhozamosan Language Understanding (LUIS) alkalmazások.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/05/2018
ms.author: diberry
ms.openlocfilehash: 9ea0269439b3d00bf36186cf2fd5c73311526bec
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225601"
---
# <a name="collaborate-with-others-on-language-understanding-luis-apps"></a>Együttműködés másokkal a Language Understanding (LUIS) alkalmazások  

Akkor is működhet együtt másokkal a LUIS-alkalmazás a együtt. 

## <a name="owner-and-collaborators"></a>Tulajdonos és közreműködő
Egy alkalmazás egyetlen tulajdonosnak rendelkezik, de sok közreműködők is. 

## <a name="add-collaborator"></a>Adja hozzá a közreműködő

Hogy a LUIS-alkalmazás szerkesztése a közreműködők a **beállítások** lapon adja meg az e-mailt, a közreműködő, majd kattintson a LUIS-alkalmazás **Hozzáadás közreműködő**.

![Adja hozzá a közreműködő](./media/luis-how-to-collaborate/add-collaborator.png)

* Közreműködők bejelentkezhet és a LUIS-alkalmazás szerkesztése az alkalmazás éppen dolgozik egy időben. <!--If a collaborator edits the LUIS app, you see a notification at the top of the browser.-->
* Közreműködők más közreműködők nem vehető fel.

Lásd: [Azure Active Directory-bérlő felhasználói](luis-how-to-account-settings.md#azure-active-directory-tenant-user) további információ az Active Directory felhasználói fiókokat. 

## <a name="set-application-as-public"></a>Nyilvános csoport alkalmazás
Lásd: [nyilvános alkalmazás-végpont hozzáférés](luis-concept-security.md#public-app-endpoint-access) további információt.

### <a name="transfer-of-ownership"></a>A tulajdonjog átruházása
Közben a LUIS jelenleg nem támogatja a tulajdonjog átruházása, exportálhatja az alkalmazás, és a egy másik LUIS felhasználó importálhatja az alkalmazás. Előfordulhat, hogy a két alkalmazás közötti LUIS pontszámok kisebb különbségek. 
