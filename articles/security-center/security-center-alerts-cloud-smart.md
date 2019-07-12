---
title: Felhőbeli intelligens riasztások között az Azure Security Centerben (események) |} A Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan fusion használ felhőalapú intelligens riasztási korrelációs biztonsági incidensek vannak az Azure Security Centerben.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 2ab4dab8cb7729b0c2ca023f22066f7b5d166a02
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571776"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Felhőbeli intelligens riasztások között az Azure Security Centerben (események)

A Security Center folyamatosan elemzi a hibrid felhőalapú számítási feladatokhoz fejlett analitikával és fenyegetésészleléssel ismerheti használatával a kártékony tevékenységek riasztást.

Bővítheti a fenyegetés lefedettség növekedésével, illetve kell észlelése, még a barnulás arra utalhat, hogy sérült a biztonsága nő, a biztonsági elemzőknek bizonyultak tényleges támadásnak azonosításához és a különböző osztályozhassa kihívást jelenthet. A Security Center segít megbirkózni a riasztási fáradás és a támadások diagnosztizálása, amikor bekövetkezik, elemzők által a különböző riasztásokhoz és kis pontosságú jelek naplókezelője biztonsági incidensekre való.

Fusion a technológia és elemzési háttérrendszer adott powers a Security Center az incidensek, lehetővé teszi a különböző riasztások és a környezetfüggő jelek együtt összekapcsolását. Az erőforrások között egy adott előfizetés jelentett megnézzük a különböző jelekkel fúziós működik, és támadás metódushívásainak megjelenítő, vagy azt jelzi, amely azt jelzi, hogy egy egyesített válasz eljárás megosztott környezeti információkat az elterjedt minták keresése kell lennie Megnyílik a számukra.

Fusion analytics kombinálva elemzéséhez, riasztások, új támadási mintákat felderítése a bekövetkezésük AI biztonsági adatait. 

A Security Center kihasználja a MITRE támadási mátrix riasztások társítandó azok mintavételezéskor szándékkal, így tegye formálissá a biztonsági adatait. Emellett az egyes lépéseinek támadások gyűjtött információkat, a Security Center is zárja ki a tevékenységet, lehet, támadások lépéseket, de nem.  

Támadások különböző bérlőkben gyakran történnek, mivel a Security Center kombinálhatja AI-algoritmusokat, amik az egyes előfizetésekhez azonosításához azokat az elterjedt riasztási minták helyett egyszerűen lehet átváltani a társítás egyes támadási feladatütemezések elemzéséhez más.

Vizsgálatok során egy incidens az elemzőknek gyakran kell hogy elháríthassák veszélyt a fenyegetés jellege kapcsolatos. Például, még ha hálózati anomáliát észlel, történik a hálózaton vagy a célzott erőforráson ismerete nélkül akkor nehezen érthető tovább milyen műveleteket. Annak érdekében, a biztonsági incidensek összetevőket, kapcsolódó események és információkat tartalmazhatnak. A biztonsági incidensek elérhető további információt a észlelt fenyegetés típusát és a környezet konfigurációjától függően változik. 

![Biztonsági incidensek részleteit](./media/security-center-alerts-cloud-smart/security-incident.png)

Biztonsági incidensek segít jobban megérteni, lásd: [az Azure Security Centerben a biztonsági incidensek kezelése](https://docs.microsoft.com/azure/security-center/security-center-incident).

