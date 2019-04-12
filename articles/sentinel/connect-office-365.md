---
title: Office 365-adatokhoz csatlakozhat az Azure-on Előzetesben Sentinel-|} A Microsoft Docs
description: Ismerje meg, hogyan Azure Sentinel-Office 365-adatokhoz csatlakozhat.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ff7c862e-2e23-4a28-bd18-f2924a30899d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 77587b0b7506ef0ccadbeb6d1f010f5b6a72d93e
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495190"
---
# <a name="connect-data-from-office-365-logs"></a>Adatok csatlakoztatása az Office 365-naplók

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A vizsgálati naplók streamelheti [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) be Azure Sentinel-egyetlen kattintással. Auditnaplók több bérlő egy egyetlen Azure Sentinel-munkaterületnek is adatfolyam. Az Office 365-tevékenység log összekötő folyamatban lévő felhasználói tevékenységek betekintést nyújt. Információ a különböző felhasználói, felügyeleti, rendszer, és a műveletek és események kap Office 365-ből. Office 365-naplók az Azure Sentinel-csatlakozással használhatja ezeket az adatokat megjelenítheti az irányítópultokat, egyéni riasztásokat is létrehozhat, és javíthatja a vizsgálat során.


## <a name="prerequisites"></a>Előfeltételek

- A bérlő kell lennie egy globális rendszergazdai vagy biztonsági rendszergazda
- A számítógépen, amelyről jelentkezett be Azure Sentinel-hozhat létre a kapcsolatot, győződjön meg arról, hogy port 4433 nyissa meg a webes forgalom.

## <a name="connect-to-office-365"></a>Csatlakozás az Office 365-höz

1. Az Azure-Sentinel, válassza **adatösszekötők** és kattintson a **Office 365** csempére.

2. Ha Ön még nem engedélyezte, a **kapcsolat** használja a **engedélyezése** gomb az Office 365-megoldások engedélyezéséhez való. Ha már engedélyezve lett, akkor azonosítja, a kapcsolat képernyő szerint már engedélyezve van.
1. Az Office 365 lehetővé teszi adatok streamelése az Azure-Sentinel több bérlő. Minden bérlő, amelyhez csatlakozni kíván, adja hozzá a bérlő **bérlők kapcsolódni az Azure-Sentinel**. 
1. Megnyílik az Active Directory képernyő. Minden egyes Azure Sentinel-csatlakozhat, és engedélyeket biztosíthat az Azure Sentinel-naplók olvasásához kívánt bérlő globális rendszergazdai felhasználóval történő hitelesítés kéri. 
5. Kattintson a Stream az Office 365-Tevékenységnaplók, **válassza** mely log típusok az Azure-Sentinel kívánja választani. Jelenleg Azure Sentinel-támogatja az Exchange és SharePoint.

4. Kattintson a **módosítások alkalmazása**.

3. A megfelelő sémát használ a Log Analytics az Office 365-naplókat, keresse meg **OfficeActivity**.


## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megismerkedhetett az Office 365-höz csatlakozhat az Azure-Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).

