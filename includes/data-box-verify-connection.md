---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 8a09a52db40f4f52219bce3e703e275b0f310c1a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67179786"
---
Kövesse az alábbi lépéseket a Storage-fiókhoz való kapcsolódáshoz és a kapcsolat ellenőrzéséhez.

1. A Storage Explorerban nyissa meg a **Kapcsolódás az Azure Storage-hoz** párbeszédpanelt. A **Kapcsolódás az Azure Storage-hoz** párbeszédpanelen válassza **a Storage-fiók nevének és kulcsának használata**lehetőséget.

    ![Data Box irányítópult](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Illessze be a **fiók nevét** és a **fiók kulcsát** (1. kulcs értékét a helyi webes felhasználói felületen a **kapcsolat és a másolás** lapról). Válassza ki a tárolási végpontok tartományt **(adja meg az alábbit)** , majd adja meg a blob Service-végpontot az alábbi ábrán látható módon. Ellenőrizze, hogy csak *a HTTP protokollon keresztüli*átvitelt kell-e **használni** . Ha a *HTTPS protokollt*használja, hagyja meg a jelölőnégyzet jelölését. Kattintson a **Tovább** gombra.

    ![Data Box irányítópult](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. A **kapcsolatok összegzése** párbeszédpanelen tekintse át a megadott adatokat. Kattintson a **Csatlakozás** gombra.

    ![Data Box irányítópult](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. A sikeresen hozzáadott fiók megjelenik a Storage Explorer bal oldali ablaktábláján (külső, egyéb) a nevéhez hozzáfűzve. A tároló megtekintéséhez kattintson a **blob-tárolók** elemre.

    ![Data Box irányítópult](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
