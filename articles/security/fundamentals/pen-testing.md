---
title: Toll tesztelése | Microsoft dokumentumok
description: A cikk áttekintést nyújt a penetrációs tesztelési (pentest) folyamatról, és arról, hogy miként hajtsa végre a tollazatot az Azure-infrastruktúrában futó alkalmazásokkal szemben.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2018
ms.author: terrylan
ms.openlocfilehash: 413ec8b121838a4ffac4119421ec3266e141618b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549253"
---
# <a name="penetration-testing"></a>Penetrációs vizsgálat
Az Azure alkalmazásteszteléshez és -üzembe helyezéshez való használatának egyik előnye, hogy gyorsan létrehozhat környezeteket. Nem kell aggódnia a saját helyszíni hardverének igénylése, beszerzése és "állványozása és halmozása" miatt.

Ez nagyszerű - de még mindig meg kell győződnie arról, hogy elvégzi a szokásos biztonsági átvilágítást. Az egyik dolog, amit valószínűleg szeretne megtenni, a penetráció tesztelése az Azure-ban telepített alkalmazások.

Lehet, hogy már tudja, hogy a Microsoft elvégzi [az Azure-környezet behatolási tesztelését.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e) Ez segít az Azure-fejlesztések javításában.

Mi nem penetráció teszt a kérelmet az Ön számára, de megértjük, hogy lesz akar, és el kell végezni e vizsgálatot a saját alkalmazásokat. Ez jó dolog, mert ha növeli alkalmazásai biztonságát, a teljes Azure-ökoszisztéma biztonságosabbá tétele.

2017. június 15-től a Microsoftnak már nincs szüksége előzetes jóváhagyásra az Azure-erőforrásokkal való behatolási teszt elvégzéséhez. Azok az ügyfelek, akik hivatalosan dokumentálni kívánják a Microsoft Azure-ral szembeni közelgő behatolástesztelési kötelezettségvállalásokat, javasoljuk, hogy töltsék ki az [Azure-szolgáltatás penetrációs tesztelési tesztelési értesítési űrlapját.](https://portal.msrc.microsoft.com/en-us/engage/pentest) Ez a folyamat csak a Microsoft Azure-hoz kapcsolódik, és nem vonatkozik más Microsoft Felhőszolgáltatásra.

>[!IMPORTANT]
>Bár a Microsoft értesítése a tolltesztelési tevékenységekről már nem szükséges, az ügyfeleknek továbbra is meg kell felelniük a [Microsoft Cloud Unified Penetration Testing Rules of Engagement szabályainak.](https://technet.microsoft.com/mt784683)

A szabványos tesztek a következők:

* Tesztek a végpontokon az [Open Web Application Security Project (OWASP) 10 legfontosabb biztonsági résének feltárására](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* A végpontok [fuzz tesztelése](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)
* A végpontok [portszkennelése](https://en.wikipedia.org/wiki/Port_scanner)

Az egyik típusú teszt, amely nem hajtható végre bármilyen [szolgáltatásmegtagadási (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) támadás. Ez magában foglalja a DoS-támadás kezdeményezését, vagy a kapcsolódó tesztek elvégzését, amelyek bármilyen Típusú DoS-támadást meghatározhatnak, bemutathatnak vagy szimulálhatnak.

## <a name="next-steps"></a>További lépések

- Ha hivatalosan is dokumentálni szeretné a Microsoft Azure-ban üzemeltetett alkalmazásaival szembeni közelgő behatolási tesztelést, menjen át a [behatolástesztelési szabályokra,](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) és töltse ki a tesztelési értesítési űrlapot.
