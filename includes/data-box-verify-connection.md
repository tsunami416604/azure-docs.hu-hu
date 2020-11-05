---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 3e4edc55834d322dede36e9f429f5af7a4a79846
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376070"
---
Kövesse az alábbi lépéseket a Storage-fiókhoz való kapcsolódáshoz és a kapcsolat ellenőrzéséhez.

1. A Storage Explorerban nyissa meg a **Kapcsolódás az Azure Storage-hoz** párbeszédpanelt. A **Kapcsolódás az Azure Storage-hoz** párbeszédpanelen válassza **a Storage-fiók nevének és kulcsának használata** lehetőséget.

    ![Data Box irányítópult](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Illessze be a **fiók nevét** és a **fiók kulcsát** (1. kulcs értékét a helyi webes felhasználói felületen a **kapcsolat és a másolás** lapról). Válassza ki a tárolási végpontok tartományt **(adja meg az alábbit)** , majd adja meg a blob Service-végpontot az alábbi ábrán látható módon. Ellenőrizze, hogy csak *a HTTP protokollon keresztüli* átvitelt kell-e **használni** . Ha a *HTTPS protokollt* használja, hagyja meg a jelölőnégyzet jelölését. Válassza a **Tovább** gombot.

    ![Data Box irányítópult 2](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. A **kapcsolatok összegzése** párbeszédpanelen tekintse át a megadott adatokat. Válassza a **Kapcsolódás** lehetőséget.

    ![Data Box irányítópult 3](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. A sikeresen hozzáadott fiók megjelenik a Storage Explorer bal oldali ablaktábláján (külső, egyéb) a nevéhez hozzáfűzve. A tároló megtekintéséhez kattintson a **blob-tárolók** elemre.

    ![Data Box irányítópult 4](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
