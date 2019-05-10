---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: e4b366075cb16f62a0e16b5b06da6fb19ffefdb9
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508296"
---
1. Jelentkezzen be a Data Box-eszközre. Győződjön meg arról, hogy fel oldva.

    ![Data Box-irányítópult](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Lépjen a **állítsa be a hálózati adapterek**. Jegyezze fel az ügyfél való kapcsolódáshoz használt hálózati adapter IP-cím.

    ![Data Box-irányítópult](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Lépjen a **csatlakozás és másolás** kattintson **Rest**.

    ![Data Box-irányítópult](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. Az a **tárolás fiók és adatfeltöltés** párbeszédpanelen, a Másolás a **Blob-szolgáltatásvégpont**.

    ![Data Box-irányítópult](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Indítsa el **Jegyzettömb** rendszergazdaként, és ezután nyissa meg a **gazdagépek** helyen található fájl `C:\Windows\System32\Drivers\etc`.
6. Adja hozzá a következő bejegyzést a **gazdagépek** fájlt: `<device IP address> <Blob service endpoint>`
7. Hivatkozás használja az alábbi képen. Mentse a **gazdagépek** fájlt.

    ![Data Box-irányítópult](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
