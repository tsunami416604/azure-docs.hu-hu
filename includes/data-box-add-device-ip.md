---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 0a9aaa86d44e71e429f2bfff13a56ddcb1ee2071
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550539"
---
1. Jelentkezzen be a Data Box-eszközre. Győződjön meg arról, hogy fel oldva.

    ![Data Box-irányítópult](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Lépjen a **állítsa be a hálózati adapterek**. Jegyezze fel az ügyfél való kapcsolódáshoz használt hálózati adapter IP-cím.

    ![Data Box-irányítópult](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Lépjen a **csatlakozás és másolás** kattintson **Rest (előzetes verzió)**.

    ![Data Box-irányítópult](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. Az a **tárolás fiók és adatfeltöltés** párbeszédpanelen, a Másolás a **Blob-szolgáltatásvégpont**.

    ![Data Box-irányítópult](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Indítsa el **Jegyzettömb** rendszergazdaként, és ezután nyissa meg a **gazdagépek** helyen található fájl `C:\Windows\System32\Drivers\etc`.
6. Adja hozzá a következő bejegyzést a **gazdagépek** fájlt: `<device IP address> <Blob service endpoint>`
7. Hivatkozás használja az alábbi képen. Mentse a **gazdagépek** fájlt.

    ![Data Box-irányítópult](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
