---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 2f490a5b12484a91e963d068810b292d7761521a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89484297"
---
> [!NOTE]
> A Azure DNS használatával egyéni DNS-nevet állíthat be Azure App Servicehoz. További információt az [egyéni tartománybeállítások egy Azure-szolgáltatáshoz az Azure DNS használatával történő megadását](../articles/dns/dns-custom-domain.md#app-service-web-apps) ismertető cikkben talál.
>
>

1. Jelentkezzen be a tartományszolgáltatója webhelyére.

1. Keresse meg a DNS-rekordok kezelésére szolgáló oldalt. Minden tartományszolgáltató saját felülettel rendelkezik a DNS-rekordok kezelésére, ezért tekintse meg a szolgáltatói dokumentációt. A webhely **Tartománynév**, **DNS** vagy **Névkiszolgáló kezelése** címkével ellátott területeit keresse.

   A DNS-rekordok oldalon gyakran megtekintheti a fiók adatait, majd egy olyan hivatkozást keres, mint például **a saját tartományok**. Nyissa meg a lapot, majd keresse meg a nevet, például a **zónafájl**, a **DNS-rekordok**vagy a **Speciális konfiguráció**nevű hivatkozást.

   A következő képernyőkép egy DNS-rekordokat tartalmazó oldalra mutat példát:

   ![A DNS-rekordok egy példáját megjelenítő képernyőkép.](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. A példa képernyőképén válassza a **Hozzáadás** elemet a rekord létrehozásához. Egyes szolgáltatók eltérő hivatkozásokat használnak a különböző rekordtípusok hozzáadásához. Erről is a szolgáltató dokumentációjában talál további információt.

> [!NOTE]
> Bizonyos szolgáltatók (például a GoDaddy) esetében a DNS-rekordok módosításai csak a külön **Módosítások mentése** hivatkozás kiválasztása után lépnek életbe.
