---
title: Eszközök beléptetésének kezelése az Azure Portalon |} A Microsoft Docs
description: A DPS szolgáltatást az Azure Portalon az eszközök beléptetésének kezelése
author: dsk-2015
ms.author: dkshir
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: ff28d9730cc95d934c78163534aed08271c6b98c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439822"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Hogyan eszközök beléptetésének kezelése az Azure Portalon

A *eszközregisztráció* létrehoz egy rekordot egy adott eszköz vagy egy csoport, az eszközről, bármikor előfordulhat, hogy regisztrálja az Azure IoT Hub Device Provisioning Service szolgáltatással. A regisztrációs rekord az regisztrálása, beleértve a kívánt IoT-központ részeként (ök) höz kezdeti kívánt beállításait tartalmazza. Ez a cikk bemutatja, hogyan kezelheti az eszközök regisztrációját a kiépítési szolgáltatás.


## <a name="create-a-device-enrollment"></a>Hozzon létre egy eszközregisztrációs

Regisztrálhatja az eszközöket a kiépítési szolgáltatás két módja van:

* Egy **regisztrációs csoportot** , amelyek egy közös igazolási mechanizmus az ugyanazon aláíró tanúsítványnak, amely lehet által aláírt X.509-tanúsítványok az eszközök egy csoportját egy bejegyzés az [főtanúsítvány](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) vagy a [köztes tanúsítványt](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate)előállításához eszközre tanúsítvány fizikai eszközön szolgál. Javasoljuk egy regisztrációs csoportnak eszközök, amelyek a megosztani kívánt kezdeti konfigurációval nagy számú, vagy eszközök célzó ugyanazt bérlőhöz. Vegye figyelembe, hogy csak regisztrálhat eszközöket, amelyek az X.509-igazolási mechanizmus, *regisztrációs csoportok*. 

    A portálon az alábbi lépéseket követve eszközök egy csoportját, egy regisztrációs csoportot hozhat létre:

    1. Jelentkezzen be az Azure Portalon, és kattintson a **összes erőforrás** elemet a bal oldali menüben.  
    1. Kattintson a Device Provisioning service szeretne regisztrálni az eszközt az erőforrások listájából.  
    1. A kiépítési szolgáltatás:  
       a. Kattintson a **beléptetések kezelése**, majd válassza ki a **regisztrációs csoportok** fülre.  
       b. Kattintson a felül lévő **Hozzáadás** gombra.  
       c. Ha az "A regisztrációs csoport hozzáadása" panel jelenik meg, írja be a beléptetési listabejegyzés adatait.  **Csoport neve** megadása kötelező. Kiválaszthatja a "CA vagy a köztes" lehetőséget a **tanúsítványtípus**, és töltse fel a legfelső szintű **elsődleges tanúsítvány** eszközök csoportja.  
       d. Kattintson a **Save** (Mentés) gombra. A regisztrációs csoport sikeres létrehozás esetén kell megjelennie a csoport neve meg fog jelenni a **regisztrációs csoportok** fülre.  

       [![Regisztrációs csoportot a portálon](./media/how-to-manage-enrollments/group-enrollment.png)] (. / media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* Egy **egyéni regisztráció** regisztrálható egyetlen eszközhöz tartozó bejegyzés. Egyéni regisztrációk használhatja bármelyik x509 tanúsítványokat vagy SAS-tokeneket (az egy fizikai vagy virtuális TPM-ben) igazolási mechanizmusként. Azt javasoljuk, hogy egyéni regisztrációk használatát, eszközök, az egyedi kezdeti konfigurációt igénylő vagy az eszközök, amelyek a TPM-eszköz vagy virtuális TPM-n keresztül SAS-tokeneket csak használhatja az igazolási mechanizmusként. Előfordulhat, hogy az egyéni regisztrációkhoz meg van határozva a kívánt IoT Hub-eszközazonosító.

    A portálon az alábbi lépésekkel hozhat létre egyéni regisztrációt:

    1. Jelentkezzen be az Azure Portalon, és kattintson a **összes erőforrás** elemet a bal oldali menüben.
    1. Kattintson a Device Provisioning service szeretne regisztrálni az eszközt az erőforrások listájából.
    1. A kiépítési szolgáltatás:  
       a. Kattintson a **beléptetések kezelése**, majd válassza ki a **egyéni regisztrációk** fülre.  
       b. Kattintson a felül lévő **Hozzáadás** gombra.   
       c. Ha a "Regisztráció hozzáadása" panel jelenik meg, írja be a beléptetési listabejegyzés adatait. Először válassza ki az igazolási **mechanizmus** az eszköz (X.509 vagy TPM). X.509-igazoláshoz kötelező feltölteni a levél **elsődleges tanúsítvány** az eszközhöz. TPM kell adnia a **igazolási kulcs** és **regisztrációs azonosító** az eszközhöz.  
       d. Kattintson a **Save** (Mentés) gombra. A regisztrációs csoport sikeres létrehozás esetén kell megjelennie az eszköz meg fog jelenni a **egyéni regisztrációk** fülre.  

       [![Egyéni regisztráció a portálon](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Regisztrációs bejegyzés frissítése
Egy meglévő eszközregisztrációs bejegyzés a portálon az alábbi lépéseket követve frissítheti:

1. Nyissa meg a Device Provisioning service az Azure Portalon, és kattintson a **regisztrációk kezelése**. 
1. Keresse meg a módosítani kívánt regisztrációs bejegyzés. Kattintson a bejegyzésre, amely megnyit egy eszközök regisztrálásával kapcsolatos összegző információkat. 
1. Ezen az oldalon módosíthatja a biztonsági típus eltérő elemek, és a hitelesítő adatokat, például az IoT hubhoz az eszközt össze kell kapcsolni, valamint az eszköz azonosítójával. Az ikereszköz kezdeti állapota is módosíthatja. 
1. Ha befejeződött, kattintson a **mentése** az eszközregisztráció frissítéséhez. 

    ![Regisztráció a portálon frissítése](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Távolítsa el a eszközök regisztrálása
Azokban az esetekben, ahol az eszköz nem kell lennie minden olyan IoT hubon építette ki akkor a kapcsolódó regisztrációs bejegyzést a portálon az alábbi lépéseket követve távolíthatja el:

1. Nyissa meg a Device Provisioning service az Azure Portalon, és kattintson a **regisztrációk kezelése**. 
1. Keresse meg és válassza ki az eltávolítani kívánt regisztrációs bejegyzés. 
1. Kattintson a **törlése** gombra az oldal tetején, majd **Igen** megerősítéséhez. 
1. A művelet befejezése után az eszközök regisztrációját a listából eltávolított bejegyzést fog látni. 
 
    ![Regisztráció a portálon eltávolítása](./media/how-to-manage-enrollments/remove-enrollment.png)


