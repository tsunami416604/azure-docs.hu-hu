---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: e4b366075cb16f62a0e16b5b06da6fb19ffefdb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "67179784"
---
1. Jelentkezzen be a Data Box eszközre. Győződjön meg róla, hogy fel van oldva.

    ![Data Box irányítópult](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Válassza a **hálózati adapterek beállítása**lehetőséget. Jegyezze fel az eszköz IP-címét az ügyfélhez való kapcsolódáshoz használt hálózati adapterhez.

    ![Data Box irányítópult](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Lépjen a **Kapcsolódás és másolás** lehetőségre, és kattintson a **Rest**elemre.

    ![Data Box irányítópult](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. A **hozzáférési Storage-fiók és az adatok feltöltése** párbeszédpanelen másolja a **blob Service-végpontot**.

    ![Data Box irányítópult](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Indítsa el a **jegyzettömböt** rendszergazdaként, majd nyissa meg a következő helyen található **hosts** fájlt: `C:\Windows\System32\Drivers\etc` .
6. Adja hozzá a következő bejegyzést a **gazdagépek** fájljához: `<device IP address> <Blob service endpoint>`
7. A hivatkozáshoz használja az alábbi képet. Mentse a **hosts** fájlt.

    ![Data Box irányítópult](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
