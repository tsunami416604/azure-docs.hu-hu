---
title: Az Azure IoT Hub-eszközkiépítési szolgáltatás eszközregisztrációinak kezelése az Azure Portalon
description: Az eszközkiépítési szolgáltatás (DPS) eszközregisztrációinak kezelése az Azure Portalon
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 8bd896573dbd0b24df11c0d66009098de19654ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974938"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Eszközregisztrációk kezelése az Azure Portalon

Az *eszközregisztráció* egyetlen eszköz vagy eszközök egy csoportjának rekordját hozza létre, amely egy bizonyos ponton regisztrálhat az Azure IoT Hub-eszközkiépítési szolgáltatással. A regisztrációs rekord tartalmazza az eszköz(ek) kezdeti kívánt konfigurációját a regisztráció részeként, beleértve a kívánt IoT hubot is. Ez a cikk bemutatja, hogyan kezelheti a kiépítési szolgáltatás eszközregisztrációit.


## <a name="create-a-device-enrollment"></a>Eszközregisztráció létrehozása

Az eszközöket kétféleképpen regisztrálhatja a kiépítési szolgáltatással:

* A **beléptetési csoport** olyan eszközök csoportjának bejegyzése, amelyek az Azonos aláíró tanúsítvánnyal aláírt, azonos aláíró tanúsítvánnyal , amely lehet a [főtanúsítvány](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) vagy a [köztes tanúsítvány,](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate)amelyek az eszköztanúsítvány fizikai eszközön történő előállításához használt, közös, X.509 tanúsítványokat használnak. Azt javasoljuk, hogy egy regisztrációs csoport számos olyan eszközök, amelyek a kívánt kezdeti konfiguráció, vagy az eszközök minden megy ugyanahhoz a bérlőhöz. Ne feledje, hogy az X.509 tanúsítványmechanizmust regisztrációs *csoportként*használó eszközöket csak regisztrálhatja. 

    A portálon az alábbi lépésekkel hozhat létre regisztrációs csoportot a portálon az alábbi lépésekkel:

  1. Jelentkezzen be az Azure Portalra, és kattintson a bal oldali menü **Minden erőforrás** parancsára.  
  1. Kattintson arra az eszközkiépítési szolgáltatásra, amelyhez az eszközt az erőforrások listájából szeretné igényelni.  
  1. A létesítési szolgáltatásban:  
     a. Kattintson **a Regisztrációk kezelése gombra,** majd válassza a **Regisztrációs csoportok** lapot.  
     b. Kattintson a felül lévő **Hozzáadás** gombra.  
     c. Amikor megjelenik a "Beiktatási csoport hozzáadása" panel, adja meg a regisztrációs lista bejegyzésének adatait.  **A csoportneve** kötelező. A **Tanúsítványtípushoz**válassza a "Hitelesítésszolgáltató vagy köztes" lehetőséget is, és töltse fel az **eszközcsoport elsődleges főtanúsítványát.**  
     d. Kattintson a **Mentés** gombra. A regisztrációs csoport sikeres létrehozásakor a csoport neve megjelenik a **Regisztrációs csoportok** lapon.  

     [![Regisztrációs csoport a portálon](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* **Az egyéni regisztráció** egyetlen eszköz bejegyzése, amely regisztrálhat. Az egyéni beléptetések x509-es tanúsítványokat vagy SAS-jogkivonatokat (fizikai vagy virtuális TPM-dokumentumból) használhatnak igazolási mechanizmusként. Azt javasoljuk, hogy egyedi regisztrációkat használjon az egyedi kezdeti konfigurációt igénylő eszközökhöz, vagy olyan eszközökhöz, amelyek csak TPM-en vagy virtuális TPM-en keresztül használhatják a SAS-jogkivonatokat igazolási mechanizmusként. Előfordulhat, hogy az egyéni regisztrációkhoz meg van határozva a kívánt IoT Hub-eszközazonosító.

    A portálon az alábbi lépésekkel hozhat létre egyéni regisztrációt:

    1. Jelentkezzen be az Azure Portalra, és kattintson a bal oldali menü **Minden erőforrás** parancsára.
    1. Kattintson arra az eszközkiépítési szolgáltatásra, amelyhez az eszközt az erőforrások listájából szeretné igényelni.
    1. A létesítési szolgáltatásban:  
       a. Kattintson **a Regisztrációk kezelése gombra,** majd válassza az **Egyéni regisztrációk** lapot.  
       b. Kattintson a felül lévő **Hozzáadás** gombra.   
       c. Amikor megjelenik a "Regisztráció hozzáadása" panel, adja meg a regisztrációs lista bejegyzésének adatait. Először válassza ki az eszköz igazolási **mechanizmusát** (X.509 vagy TPM). Az X.509 tanúsítvány hoz, hogy feltöltsd az eszköz elsődleges **levelét.** A TPM megköveteli, hogy megadja az **eszköz igazolási kulcsát** és **regisztrációs azonosítóját.**  
       d. Kattintson a **Mentés** gombra. A regisztrációs csoport sikeres létrehozásakor az eszköz nek az **Egyéni regisztrációk** lapon jelenik meg.  

       [![Egyéni regisztráció a portálon](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Regisztrációs bejegyzés frissítése
A portálon meglévő regisztrációs bejegyzéseket a következő lépésekkel frissíthet:

1. Nyissa meg az Eszközkiépítési szolgáltatást az Azure Portalon, és kattintson **a Regisztrációk kezelése gombra.** 
1. Nyissa meg a módosítani kívánt regisztrációs bejegyzést. Kattintson arra a bejegyzésre, amely összesítő információt nyit meg az eszközregisztrációról. 
1. Ezen a lapon módosíthatja a biztonsági típustól és a hitelesítő adatoktól eltérő elemeket, például azt az IoT-központot, amelyhez az eszközt csatolni kell, valamint az eszközazonosítót. Módosíthatja a kezdeti ikereszköz-állapotot is. 
1. Miután elkészült, kattintson a **Mentés** gombra az eszközregisztráció frissítéséhez. 

    ![Regisztráció frissítése a portálon](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Eszközregisztráció eltávolítása
Azokban az esetekben, amikor az eszköz(ek) nem kell kiépíteni, hogy bármely IoT hub, eltávolíthatja a kapcsolódó regisztrációs bejegyzést a portálon a következő lépésekkel:

1. Nyissa meg az Eszközkiépítési szolgáltatást az Azure Portalon, és kattintson **a Regisztrációk kezelése gombra.** 
1. Keresse meg és jelölje ki az eltávolítani kívánt regisztrációs bejegyzést. 
1. Kattintson **a** törlés gombra a tetején, majd válassza az **Igen** lehetőséget, amikor a program a megerősítést kéri. 
1. A művelet befejezése után a bejegyzés törlődik az eszközregisztrációk listájáról. 
 
    ![Regisztráció eltávolítása a portálon](./media/how-to-manage-enrollments/remove-enrollment.png)


