---
title: Az Azure IoT Hub Device Provisioning Service eszközök regisztrálásának kezelése a Azure Portal
description: Eszközök regisztrálásának kezelése az eszköz kiépítési szolgáltatásához (DPS) az Azure Portalon
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 8bd896573dbd0b24df11c0d66009098de19654ac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74974938"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Az eszközök regisztrálásának kezelése az Azure Portal használatával

Az *eszközök* regisztrálása létrehoz egy egyetlen eszközről vagy egy olyan eszközről szóló adatcsoportot, amely egy ponton regisztrálhat az Azure IoT hub Device Provisioning Service. A beléptetési rekord tartalmazza az eszköz (ek) kezdeti kívánt konfigurációját a regisztráció részeként, beleértve a kívánt IoT hubot is. Ez a cikk bemutatja, hogyan kezelheti az eszközök regisztrációját a kiépítési szolgáltatáshoz.


## <a name="create-a-device-enrollment"></a>Eszközök regisztrálásának létrehozása

Az eszközöket kétféleképpen lehet regisztrálni a kiépítési szolgáltatással:

* A **beléptetési csoport** olyan eszközök csoportja, amelyek közös igazolási mechanizmussal rendelkeznek X. 509 tanúsítványokban, és amelyek aláírását ugyanazzal az aláíró tanúsítvánnyal írta alá, amely lehet a [Főtanúsítvány](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) vagy a [köztes tanúsítvány](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), amely az eszköz tanúsítványának fizikai eszközön történő előállítására szolgál. Azt javasoljuk, hogy nagy számú eszközhöz használjon egy regisztrációs csoportot, amely megosztja a kívánt kezdeti konfigurációt, vagy ha az eszközök mindegyike ugyanahhoz a bérlőhöz fog csatlakozni. Vegye figyelembe, hogy csak az X. 509 igazolási mechanizmust használó eszközök regisztrálása *beléptetési csoportként*. 

    Az alábbi lépéseket követve létrehozhat egy regisztrációs csoportot a portálon az eszközök egy csoportjának használatával:

  1. Jelentkezzen be a Azure Portalba, és a bal oldali menüben kattintson az **összes erőforrás** elemre.  
  1. Kattintson arra az eszköz-kiépítési szolgáltatásra, amelyhez regisztrálni kívánja az eszközt az erőforrások listájából.  
  1. A kiépítési szolgáltatásban:  
     a. Kattintson a **regisztrációk kezelése**lehetőségre, majd válassza a **regisztrációs csoportok** lapot.  
     b. Kattintson a felül lévő **Hozzáadás** gombra.  
     c. Amikor megjelenik a "beléptetési csoport hozzáadása" panel, adja meg a beléptetési lista bejegyzésének információit.  A **csoport nevét** kötelező megadni. Válassza a "CA vagy Intermediate" lehetőséget is a **tanúsítvány típusához**, majd töltse fel az eszközök csoport **elsődleges tanúsítványát** .  
     d. Kattintson a **Save** (Mentés) gombra. A regisztrációs csoport sikeres létrehozásakor a csoport neve jelenik meg a **regisztrációs csoportok** lapon.  

     [![Beléptetési csoport a portálon](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* Az **Egyéni regisztráció** egy olyan bejegyzés, amely regisztrálható egyetlen eszközön. Az egyéni regisztrációk x509-tanúsítványokat vagy SAS-jogkivonatokat (fizikai vagy virtuális TPM-ből) is használhatnak igazolási mechanizmusként. Azt javasoljuk, hogy egyéni regisztrációkat használjon olyan eszközökhöz, amelyek egyedi kezdeti konfigurációt igényelnek, vagy olyan eszközök esetében, amelyek csak a TPM vagy a virtuális TPM modulon keresztül használhatják az igazolási mechanizmusként szolgáló SAS-jogkivonatokat. Előfordulhat, hogy az egyéni regisztrációkhoz meg van határozva a kívánt IoT Hub-eszközazonosító.

    Az alábbi lépéseket követve létrehozhat egy egyéni regisztrációt a portálon:

    1. Jelentkezzen be a Azure Portalba, és a bal oldali menüben kattintson az **összes erőforrás** elemre.
    1. Kattintson arra az eszköz-kiépítési szolgáltatásra, amelyhez regisztrálni kívánja az eszközt az erőforrások listájából.
    1. A kiépítési szolgáltatásban:  
       a. Kattintson a **regisztrációk kezelése**lehetőségre, majd válassza az **Egyéni regisztrációk** lapot.  
       b. Kattintson a felül lévő **Hozzáadás** gombra.   
       c. Amikor megjelenik a "regisztráció hozzáadása" panel, adja meg a beléptetési lista bejegyzésének információit. Először válassza ki az eszköz igazolási **mechanizmusát** (X. 509 vagy TPM). Az X. 509 igazolásához fel kell töltenie az eszközhöz tartozó levél **elsődleges tanúsítványát** . A TPM használatához meg kell adnia az eszköz **igazolási kulcsát** és **regisztrációs azonosítóját** .  
       d. Kattintson a **Save** (Mentés) gombra. A regisztrációs csoport sikeres létrehozásakor látnia kell az eszközt az **Egyéni regisztrációk** lapon.  

       [![Egyéni regisztráció a portálon](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Beléptetési bejegyzés frissítése
A meglévő regisztrációs bejegyzéseket a következő lépésekkel frissítheti a portálon:

1. Nyissa meg az eszköz kiépítési szolgáltatását a Azure Portalon, és kattintson a **regisztrációk kezelése**lehetőségre. 
1. Navigáljon a módosítani kívánt beléptetési bejegyzéshez. Kattintson a bejegyzésre, amely megnyit egy összegző információt az eszköz regisztrálásáról. 
1. Ezen az oldalon a biztonsági típustól és a hitelesítő adatoktól eltérő elemeket is módosíthat, például az eszközhöz kapcsolódó IoT hub-t, valamint az eszköz AZONOSÍTÓját. Emellett módosíthatja a kezdeti eszköz kettős állapotát is. 
1. Ha elkészült, kattintson a **Save (Mentés** ) gombra az eszköz regisztrálásának módosításához. 

    ![Regisztráció frissítése a portálon](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Eszközök regisztrációjának eltávolítása
Azokban az esetekben, amikor az eszköz (ek) et nem kell IoT-hubhoz kiépíteni, a következő lépésekkel távolíthatja el a kapcsolódó beléptetési bejegyzést a portálon:

1. Nyissa meg az eszköz kiépítési szolgáltatását a Azure Portalon, és kattintson a **regisztrációk kezelése**lehetőségre. 
1. Navigáljon a elemre, és válassza ki az eltávolítani kívánt beléptetési bejegyzést. 
1. Kattintson a felül található **Törlés** gombra, majd válassza az **Igen** lehetőséget, amikor a rendszer felszólítja a megerősítésre. 
1. A művelet befejezése után a rendszer eltávolítja a bejegyzést az eszközök regisztrációjának listájáról. 
 
    ![Regisztráció eltávolítása a portálon](./media/how-to-manage-enrollments/remove-enrollment.png)


