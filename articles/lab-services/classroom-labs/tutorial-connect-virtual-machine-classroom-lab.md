---
title: Hozzáférés egy osztályterem-tesztkörnyezethez az Azure Lab Services szolgáltatásban | Microsoft Docs
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
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 5482ea720ea8d21230587dd9216bd006bf4e5a6e
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650648"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Oktatóanyag: Az Azure Lab Services osztályterem-tesztkörnyezet elérése
Ebben az oktatóanyagban a diákok megtanulnak csatlakozni egy osztályterem-tesztkörnyezet virtuális gépéhez (VM). 

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Regisztrációs hivatkozás használata 
> * Csatlakozás a virtuális géphez

## <a name="use-the-registration-link"></a>A regisztrációs hivatkozás használata

1. Keresse fel a **regisztrációs URL-címet**, amelyet a tanártól/oktatótól kapott. A regisztrációs URL-cím használatához a regisztráció befejezése után nincs szükség. Használja ezt az URL-cím: [ https://labs.azure.com ](https://labs.azure.com). 
1. A regisztráció elvégzéséhez jelentkezzen be a szolgáltatásba az iskolai fiókjával. 
2. A regisztrációt követően ellenőrizze, hogy látja-e a virtuális gépet abban a tesztkörnyezetben, amelyhez hozzáférése van. 
3. Várja meg, amíg a virtuális gép készen áll, majd **indítsa el** azt. Ez a folyamat hosszabb időt vesz igénybe.  

    ![A virtuális gép elindítása](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

1. Válassza ki **Connect** a csempére a virtuális gép a labor létrehozása, amely el szeretne érni. 

    ![Kapcsolódás egy virtuális géphez](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Hajtsa végre a következő lépések egyikét: 
    1. A **Windows** virtuális gépeket, mentse a **RDP** a merevlemez-fájlt. Nyissa meg a virtuális géphez csatlakozni RDP-fájlt. Használja a **felhasználónév** és **jelszó** bejelentkezni a gép az oktató/professzor kérhet. 
    3. A **Linux** használható virtuális gépeket, **SSH** vagy **RDP** (Ha engedélyezve van) a hozzájuk való kapcsolódáshoz. További információkért lásd: [engedélyezze a távoli asztali kapcsolatot a Linux rendszerű gépek](how-to-enable-remote-desktop-linux.md). 

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanult hozzáférni egy osztályterem-tesztkörnyezethez az oktatótól/tanártól kapott regisztrációs hivatkozás használatával.

Labortulajdonosként meg szeretné tekinteni, hogy regisztrálva van a tesztkörnyezet a és a virtuális gépek használatának nyomon követése. Folytassa a következő oktatóanyaggal, megtudhatja, hogyan a labor létrehozása a használat nyomon követéséhez:

> [!div class="nextstepaction"]
> [Tesztkörnyezet használatának nyomon követése](tutorial-track-usage.md) 
