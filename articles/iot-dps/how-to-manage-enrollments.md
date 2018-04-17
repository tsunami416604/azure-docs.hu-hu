---
title: Az Azure-portálon eszközbeléptetésnél kezelése |} Microsoft Docs
description: A terjesztési pontok szolgáltatás az Azure portálon eszközbeléptetésnél kezelése
services: iot-dps
keywords: ''
author: dsk-2015
ms.author: dkshir
ms.date: 04/05/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 55486d9a37968351f5313c708e9ef26e5b89063c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Az Azure portál eszközbeléptetésnél kezelése

A *eszközregisztráció* létrehoz egy rekordot egyetlen eszközt vagy eszközöket, amelyek bármikor előfordulhat, hogy regisztrálja az Azure IoT Hub eszköz kiépítése szolgáltatással csoportja. A beléptetési rekord tartalmazza a kezdeti kívánt beállításait, hogy a regisztráció, beleértve a kívánt IoT-központ részeként őket. Ez a cikk bemutatja, hogyan kezelheti az eszközök regisztrációját a létesítési szolgáltatás.


## <a name="create-a-device-enrollment"></a>Hozzon létre egy eszközök beléptetése

Az üzembe helyezési szolgáltatással az eszközök regisztrálása két módja van:

* Egy **beléptetési csoport** , amelyek egy közös igazolás mechanizmus X.509-tanúsítványokat, az azonos aláíró tanúsítványnak, amely lehet írja alá az egy eszközcsoportra bejegyzés a [legfelső szintű tanúsítvány](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) vagy a [köztes tanúsítvány](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), fizikai eszközön eszköz tanúsítvány létrehozásához használt. Azt javasoljuk, egy beléptetési csoport az eszközök, amelyek kívánt kezdeti konfigurációja számos vagy eszközök valamennyi amelyet ugyanannak a bérlőnek. Vegye figyelembe, hogy csak regisztrálhatja az eszközöket, amelyek használják az X.509 tanúsítvány mechanizmust *beléptetési csoportok*. 

    A portálon, a csoport az eszközök az alábbi lépéseket követve létrehozhat egy beléptetési csoport:

    1. Jelentkezzen be az Azure-portálon, majd kattintson a **összes erőforrás** a bal oldali menüből.  
    2. Kattintson az eszköz az erőforrások listájához a regisztrálni kívánt eszközök kiépítését szolgáltatás.  
    3. Az üzembe helyezési szolgáltatásban:  
       a. Kattintson a **regisztrációkat kezelése**, majd jelölje be a **beléptetési csoportok** fülre.  
       b. Kattintson a felül lévő **Hozzáadás** gombra.  
       c. Amikor megjelenik a "Csoport hozzáadása a beléptetési" panelen, írja be a beléptetési listaelem adatait.  **Csoportnév** szükséges. Kiválaszthatja a "CA vagy köztes" lehetőséget a **tanúsítványtípus**, és töltse fel a legfelső szintű **elsődleges tanúsítvány** a csoport az eszközök.  
       d. Kattintson a **Save** (Mentés) gombra. A beléptetési csoport sikeres létrehozásakor kell megjelennie a csoport neve alatt jelennek meg a **beléptetési csoportok** fülre.  

       [![A portál regisztrációs csoport](./media/how-to-manage-enrollments/group-enrollment.png)] (. / media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* Egy **egyes beléptetési** regisztrálni lehet, hogy egyetlen eszköz bejegyzés. Egyes regisztrációkat használhatja bármelyik x509 tanúsítványokat vagy SAS jogkivonatok (a fizikai vagy virtuális TPM), állapotigazolási mechanizmusok. Azt javasoljuk, egyes regisztrációkat az eszközök, amelyek külön kezdeti konfigurációt igényelnek, vagy az eszközök, amelyek is csak TPM vagy virtuális TPM SAS-tokenje igazoló mechanizmusként. Előfordulhat, hogy az egyéni regisztrációkhoz meg van határozva a kívánt IoT Hub-eszközazonosító.

    A portál az alábbi lépéseket követve hozhat létre az egyes tagság:

    1. Jelentkezzen be az Azure-portálon, majd kattintson a **összes erőforrás** a bal oldali menüből.
    2. Kattintson az eszköz az erőforrások listájához a regisztrálni kívánt eszközök kiépítését szolgáltatás.
    3. Az üzembe helyezési szolgáltatásban:  
       a. Kattintson a **regisztrációkat kezelése**, majd válassza a **egyedi regisztrációkat** fülre.  
       b. Kattintson a felül lévő **Hozzáadás** gombra.   
       c. Amikor megjelenik a "Regisztrációja hozzáadása" panelen, írja be a beléptetési listaelem adatait. Először válassza ki azt a tanúkiszolgáló **mechanizmus** az eszköz (X.509 vagy TPM). X.509 tanúsítvány meg feltölteni a levél **elsődleges tanúsítvány** az eszközön. TPM meg kell adnia a **Állapotigazolási kulcs** és **regisztrációs Azonosítót** az eszközön.  
       d. Kattintson a **Save** (Mentés) gombra. A beléptetési csoport sikeres létrehozásakor kell megjelennie az eszköz alatt jelennek meg a **egyedi regisztrációkat** fülre.  

       [![Az egyes beléptetési a portálon](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Regisztrációs bejegyzés frissítése
Frissítheti a meglévő regisztrációs bejegyzése a portálon, az alábbi lépéseket követve:

1. Nyissa meg az eszközök kiépítését szolgáltatást az Azure portálon, és kattintson a **kezelése regisztrációkat**. 
2. Nyissa meg a módosítani kívánt regisztrációs bejegyzés. Kattintson a bejegyzésre, amely megnyit egy az eszközök regisztrálásával kapcsolatos összegző információkat. 
3. Ezen a lapon a Biztonság típusa nem módosítható, és a hitelesítő adatokat, például az IoT-központ az eszközt össze kell kapcsolni, valamint az eszközazonosító. A kezdeti eszköz iker állapotát is módosíthatja. 
4. Ezt követően kattintson **mentése** az eszközregisztráció frissítéséhez. 

    ![Frissíti a regisztrációját a portálon](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Távolítsa el az olyan eszközök beléptetése
Azokban az esetekben, ahol az eszközöket nem kell kell létrehozni, hogy az IoT-központ eltávolíthatja a kapcsolódó regisztrációs bejegyzés a portálon, az alábbi lépéseket követve:

1. Nyissa meg az eszközök kiépítését szolgáltatást az Azure portálon, és kattintson a **kezelése regisztrációkat**. 
2. Keresse meg és válassza ki az eltávolítani kívánt beléptetési. 
3. Kattintson a **törlése** felső gombra, majd válassza ki **Igen** megerősítéséhez. 
5. Ha a művelet befejeződött, a bejegyzés eltávolítja a listáról, az eszközök regisztrációját jelenik meg. 
 
    ![Távolítsa el a regisztrációs a portálon](./media/how-to-manage-enrollments/remove-enrollment.png)


