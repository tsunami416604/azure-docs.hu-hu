---
title: Hozzáférés osztályterem-tesztkörnyezethez az Azure Lab Servicesben | Microsoft Docs
description: Ebben az oktatóanyagban megtanulhatja, hogyan férhet hozzá egy tanár által létrehozott osztályterem-tesztkörnyezetben lévő virtuális gépekhez.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/19/2019
ms.author: spelluru
ms.openlocfilehash: 8d3d08c772c28ff4e3a32e736253ba69b03684f0
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012297"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Hozzáférés osztályterem-tesztkörnyezethez az Azure Lab Servicesben
Ez a cikk bemutatja, hogyan regisztrálhat egy osztályterem laborba, megtekintheti az összes elérni kívánt labort, elindíthatja vagy leállíthatja a virtuális gépet a laborban, és csatlakozhat a virtuális géphez. 

## <a name="register-to-the-lab"></a>Regisztráció a laborba

1. Keresse fel a **regisztrációs URL-címet**, amelyet a tanártól/oktatótól kapott. A regisztráció befejezése után nem szükséges a regisztrációs URL-cím használata. Ehelyett használja az URL-címet [https://labs.azure.com](https://labs.azure.com):. Az Internet Explorer 11 még nem támogatott. 
1. A regisztráció elvégzéséhez jelentkezzen be a szolgáltatásba az iskolai fiókjával. 
2. A regisztrációt követően ellenőrizze, hogy látja-e a virtuális gépet abban a tesztkörnyezetben, amelyhez hozzáférése van. 
3. Várjon, amíg a virtuális gép készen áll. A virtuális gép csempén figyelje meg a következő mezőket:
    1. A csempe tetején megjelenik a **labor neve**.
    1. A jobb oldalon megjelenik a virtuális gép **operációs rendszerét** jelképező ikon. Ebben a példában ez a Windows operációs rendszer. 
    1. A csempe alján található ikonokat/gombokat a virtuális gép elindításához/leállításához és a virtuális géphez való kapcsolódáshoz használhatja. 
    1. A gombok jobb oldalán megtekintheti a virtuális gép állapotát. Győződjön meg arról, hogy a virtuális gép állapota **leállt**.

        ![Leállított állapotú virtuális gép](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>A virtuális gép elindítása vagy leállítása
1. **Indítsa el** a virtuális gépet az alábbi képen látható első gomb kiválasztásával. Ez a folyamat hosszabb időt vesz igénybe.  

    ![A virtuális gép elindítása](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Győződjön meg arról, hogy a virtuális gép állapota **fut**értékre van állítva. 

    ![Futó állapotú virtuális gép](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Figyelje meg, hogy az első gomb ikonja egy leállítási műveletet jelöl. Ezzel a gombbal állíthatja le a virtuális gépet. 

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

1. A labor virtuális géphez való kapcsolódáshoz kattintson a következő képen látható második gombra. 

    ![Kapcsolódás egy virtuális géphez](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Hajtsa végre az alábbi lépések egyikét: 
    1. **Windows rendszerű** virtuális gépek esetén mentse az **RDP** -fájlt a merevlemezre. Nyissa meg az RDP-fájlt a virtuális géphez való kapcsolódáshoz. Használja az oktató/professzor által a gépre való bejelentkezéshez kapott felhasználónevet és **jelszót** . 
    3. **Linux** rendszerű virtuális gépek esetén az **SSH** vagy az **RDP** (ha engedélyezve van) használatával csatlakozhat hozzájuk. További információ: [Távoli asztali kapcsolat engedélyezése Linux rendszerű gépekhez](how-to-enable-remote-desktop-linux.md). 
    1. Ha **Mac** -t használ a tesztkörnyezet virtuális géphez való kapcsolódáshoz, kövesse a következő szakaszban található utasításokat. 

## <a name="connect-to-a-vms-ui-using-rdp-on-a-mac"></a>Kapcsolódás virtuális gép felhasználói felületéhez RDP használatával Mac gépen
Ez a szakasz azt mutatja be, hogy egy tanuló hogyan tud csatlakozni a virtuális gép felhasználói felületéhez a Mac rendszerből RDP használatával. Az alábbi lépéseket követve részletesen ismertetjük a részleteket: 

1. Telepítse a Microsoft Távoli asztalt a Mac számítógépen.
2. Kapcsolódjon a Mac rendszerű virtuális gép felhasználói felületéhez RDP használatával. 

### <a name="install-microsoft-remote-desktop-on-a-mac"></a>Microsoft Távoli asztal telepítése Mac gépen
1. Nyissa meg az App Store-t a Mac számítógépen, és keressen rá **Microsoft távoli asztal**.

    ![Microsoft Távoli asztal](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Telepítse a Microsoft Távoli asztal legújabb verzióját. 

### <a name="access-the-vms-ui-from-your-mac-using-rdp"></a>A virtuális gép felhasználói felületének elérése Mac-ről RDP használatával
1. Nyissa meg a számítógépen letöltött **RDP** -fájlt, **Microsoft távoli asztal** telepítve van. Meg kell kezdenie a virtuális géphez való csatlakozást. 

    ![Kapcsolódás virtuális géphez felhasználói felülethez](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Ha a következő hibaüzenetet kapja, válassza a **Continue (folytatás** ) lehetőséget. 

    ![Tanúsítvány hibája](../media/how-to-use-classroom-lab/certificate-error.png)
1. Ekkor meg kell jelennie a virtuális gép felhasználói felületének. 

    > [!NOTE]
    > Az alábbi példa egy CentOS Linux rendszerű virtuális gép. 

    ![VIRTUÁLIS GÉP FELHASZNÁLÓI FELÜLETE](../media/how-to-use-classroom-lab/vm-ui.png)

## <a name="progress-bar"></a>Folyamatjelző 
A csempe folyamatjelzője megjeleníti az órák számát, amelyet a rendszer az Ön által hozzárendelt [kvóta-órák](how-to-configure-student-usage.md#set-quotas-for-users) száma alapján használ. Ez az idő az a további időpont, amelyet a tesztkörnyezet ütemezett ideje mellett is kiosztottak. Az állapotjelző sáv színe és a folyamatjelző alatti szöveg a következő esetekben változik:

- Ha egy osztály folyamatban van (az osztály ütemtervén belül), a folyamatjelző sáv szürkén jelenik meg, hogy a rendszer ne használja a kvótákat. 

    ![Folyamatjelző sáv szürke színnel](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- Ha a kvóta nincs hozzárendelve (nulla óra), az **osztályok alatt elérhető szöveg csak** a folyamatjelző sáv helyén jelenik meg. 
    
    ![Állapot, ha nincs beállítva kvóta](../media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- Ha kifogyott a **kvóta**, a folyamatjelző színe **vörös**. 

    ![Folyamatjelző sáv piros színnel](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- A folyamatjelző színe **kék** , ha a tesztkörnyezet ütemezett ideje kívül esik, és egy bizonyos kvóta-idő is használatban van. 

    ![Folyamatjelző sáv kék színnel](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-classroom-labs"></a>Az összes osztályterem-tesztkörnyezet megtekintése
A laborba való regisztráció után az alábbi lépéseket követve megtekintheti az összes osztályterem Labs-t: 

1. Navigáljon [https://labs.azure.com](https://labs.azure.com)a következőhöz:. Az Internet Explorer 11 még nem támogatott. 
2. Jelentkezzen be a szolgáltatásba a laborba való regisztrációhoz használt felhasználói fiók használatával. 
3. Győződjön meg arról, hogy az összes olyan labor megjelenik, amelyhez hozzáfér. 

    ![Összes tesztkörnyezet megtekintése](../media/how-to-use-classroom-lab/all-labs.png)


## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Rendszergazdaként, labor-fiókok létrehozása és kezelése](how-to-manage-lab-accounts.md)
- [Labor tulajdonosaként Labs létrehozása és kezelése](how-to-manage-classroom-labs.md)
- [A labor tulajdonosaként hozzon létre és tegyen közzé sablonokat](how-to-create-manage-template.md)
- [Tesztkörnyezet tulajdonosaként konfigurálhatja és szabályozhatja a labor használatát](how-to-configure-student-usage.md)
 