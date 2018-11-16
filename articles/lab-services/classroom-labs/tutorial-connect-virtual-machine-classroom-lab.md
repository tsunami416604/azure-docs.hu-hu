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
ms.date: 11/14/2018
ms.author: spelluru
ms.openlocfilehash: b9d140eedf69ed730985412ba1dcdca2d626b191
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706704"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Oktatóanyag: Hozzáférés egy osztályterem-tesztkörnyezethez az Azure Lab Services szolgáltatásban
Ebben az oktatóanyagban a diákok megtanulnak csatlakozni egy osztályterem-tesztkörnyezet virtuális gépéhez (VM). 

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Regisztrációs hivatkozás használata 
> * Csatlakozás a virtuális géphez

## <a name="use-the-registration-link"></a>A regisztrációs hivatkozás használata

1. Keresse fel a **regisztrációs URL-címet**, amelyet a tanártól/oktatótól kapott. 
2. A regisztráció elvégzéséhez jelentkezzen be a szolgáltatásba az iskolai fiókjával. 
3. A regisztrációt követően ellenőrizze, hogy látja-e a virtuális gépet abban a tesztkörnyezetben, amelyhez hozzáférése van. 
2. Várja meg, amíg a virtuális gép készen áll, majd **indítsa el** azt. Ez a folyamat hosszabb időt vesz igénybe.  

    ![A virtuális gép elindítása](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

1. Válassza ki **Connect** a csempére a virtuális gép a labor létrehozása, amely el szeretne érni. 

    ![Kapcsolódás egy virtuális géphez](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Mentse az RDP-fájlt a merevlemezre, és nyissa meg (feltéve, hogy-e egy Windows virtuális gép)
3. Adja meg a **felhasználónevet** és **jelszót**, amelyet az oktatótól/tanártól kapott a gépbe való bejelentkezéshez. 

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanult hozzáférni egy osztályterem-tesztkörnyezethez az oktatótól/tanártól kapott regisztrációs hivatkozás használatával.

Labortulajdonosként meg szeretné tekinteni, hogy regisztrálva van a tesztkörnyezet a és a virtuális gépek használatának nyomon követése. Folytassa a következő oktatóanyaggal, megtudhatja, hogyan a labor létrehozása a használat nyomon követéséhez:

> [!div class="nextstepaction"]
> [Tesztkörnyezet használatának nyomon követése](tutorial-track-usage.md) 