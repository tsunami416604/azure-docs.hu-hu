---
author: WenJason
ms.service: databox
ms.topic: include
origin.date: 12/07/2018
ms.date: 02/25/2019
ms.author: v-jay
ms.openlocfilehash: 0a9aaa86d44e71e429f2bfff13a56ddcb1ee2071
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60728279"
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
