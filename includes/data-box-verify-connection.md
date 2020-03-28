---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 8a09a52db40f4f52219bce3e703e275b0f310c1a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67179786"
---
Az alábbi lépésekkel csatlakozhat a tárfiókhoz, és ellenőrizheti a kapcsolatot.

1. A Storage Explorer ben nyissa meg a Csatlakozás az **Azure Storage-hoz párbeszédpanelt.** A Csatlakozás az **Azure Storage-hoz** párbeszédpanelen válassza **a Tárfiók nevének és kulcsának használata**lehetőséget.

    ![Adatdoboz irányítópultja](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Illessze be a **fiók nevét** és **a fiókkulcsot** (az 1. kulcs értéke a helyi webes felhasználói felület **Connect és másolása** lapján). Válassza a Storage-végpontok tartományt egyéb ként **(írja be alább),** majd adja meg a blob szolgáltatás végpontját az alábbiak szerint. Jelölje be **a HTTP használata** jelölőnégyzetet, ha *http-n*keresztül kell átvinni. Https használata *esetén*hagyja a beállítást bejelölve. Válassza a **Tovább lehetőséget.**

    ![Adatdoboz irányítópultja](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. A **Kapcsolat összegzése** párbeszédpanelen tekintse át a megadott információkat. Kattintson a **Csatlakozás** gombra.

    ![Adatdoboz irányítópultja](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. A sikeresen hozzáadott fiók a Storage Explorer bal oldali ablaktáblájában jelenik meg a nevéhez (Külső, Egyéb) csatolva. Kattintson **a Blob Containers** elemre a tároló megtekintéséhez.

    ![Adatdoboz irányítópultja](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
