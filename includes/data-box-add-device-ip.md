---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: e4b366075cb16f62a0e16b5b06da6fb19ffefdb9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67179784"
---
1. Jelentkezzen be a Data Box eszközbe. Győződjön meg róla, hogy fel van oldva.

    ![Adatdoboz irányítópultja](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Nyissa meg a **Hálózati adapterek beállítása (Set network interfaces) (Hálózati adapterek beállítása) (Hálózati adapterek** Jegyezze fel az ügyfélhez való csatlakozáshoz használt hálózati adapter eszköz IP-címét.

    ![Adatdoboz irányítópultja](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Nyissa meg a **Csatlakozás és másolás,** majd **a Többi (Tovább)** menülap .go to Connect and copy and click Rest .

    ![Adatdoboz irányítópultja](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. Másolja a **blobszolgáltatás végpontját**az **Access Storage-fiók és a feltöltési adatok** párbeszédpanelről.

    ![Adatdoboz irányítópultja](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Indítsa el **a Jegyzettömböt** rendszergazdaként, majd `C:\Windows\System32\Drivers\etc`nyissa meg **a** hosts fájlt a helyen.
6. Adja hozzá a következő bejegyzést a **hosts** fájlhoz:`<device IP address> <Blob service endpoint>`
7. Referenciaként használja az alábbi képet. Mentse a **hosts** fájlt.

    ![Adatdoboz irányítópultja](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
