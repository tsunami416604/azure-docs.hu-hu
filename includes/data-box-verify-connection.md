---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 8a09a52db40f4f52219bce3e703e275b0f310c1a
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550574"
---
Kövesse az alábbi lépéseket a tárfiókhoz való csatlakozáshoz, és ellenőrizze a kapcsolatot.

1. A Storage Explorerben nyissa meg a **csatlakozás az Azure Storage** párbeszédpanel. Az a **csatlakozás az Azure Storage** párbeszédablakban válassza **tárfiók nevének és kulcsának**.

    ![Data Box-irányítópult](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Illessze be a **fióknév** és **fiókkulcs** (kulcs 1 értékét a a **csatlakozás és másolás** oldal a helyi webes felhasználói felületen). Válassza ki tárolási végpontok tartományát, **egyéb (adja meg alább)** , és adja meg a blob-szolgáltatásvégpont alább látható módon. Ellenőrizze **HTTP használata** beállítást, csak ha átvitele közben *http*. Ha használ *https*, hagyja bejelölve a beállítást. Kattintson a **Tovább** gombra.

    ![Data Box-irányítópult](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. Az a **kapcsolatok összegzése** párbeszédpanelen tekintse át a megadott információkat. Kattintson a **Csatlakozás** gombra.

    ![Data Box-irányítópult](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. A fiók sikeresen hozzáadott jelenik meg a bal oldali panelen, a Storage Explorer (külső, egyéb) jelöléssel nevére. Kattintson a **Blobtárolók** megtekintéséhez a tároló.

    ![Data Box-irányítópult](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
