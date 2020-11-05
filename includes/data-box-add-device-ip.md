---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 6b716640bc8117ccc2d473b337ee3e555e8d9b0a
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375912"
---
1. Jelentkezzen be a Data Box eszközre. Győződjön meg róla, hogy fel van oldva.

    ![Data Box irányítópult](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Válassza a **hálózati adapterek beállítása** lehetőséget. Jegyezze fel az eszköz IP-címét az ügyfélhez való kapcsolódáshoz használt hálózati adapterhez.

    ![Data Box irányítópult 2](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Lépjen a **Kapcsolódás és másolás** lehetőségre, és kattintson a **Rest** elemre.

    ![Data Box irányítópult 3](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. A **hozzáférési Storage-fiók és az adatok feltöltése** párbeszédpanelen másolja a **blob Service-végpontot**.

    ![Data Box irányítópult 4](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Indítsa el a **jegyzettömböt** rendszergazdaként, majd nyissa meg a következő helyen található **hosts** fájlt: `C:\Windows\System32\Drivers\etc` .
6. Adja hozzá a következő bejegyzést a **gazdagépek** fájljához: `<device IP address> <Blob service endpoint>`
7. A hivatkozáshoz használja az alábbi képet. Mentse a **hosts** fájlt.

    ![Data Box irányítópult 5](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
