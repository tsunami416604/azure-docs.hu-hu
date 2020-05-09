---
title: Tesztkörnyezet létrehozása a mélyreható tanuláshoz a Azure Lab Services használatával | Microsoft Docs
description: Ismerje meg, hogyan állíthat be egy labort a rendszerhéj-parancsfájlok Linuxon való tanításához.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 0dab5f3dcdfb6ddabbd94960dcf8a8bf2bce98af
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890779"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Tesztkörnyezet létrehozása a természetes nyelvi feldolgozással kapcsolatos mély tanuláshoz Azure Lab Services
Ebből a cikkből megtudhatja, hogyan állíthat be egy olyan labort, amely a természetes nyelvi feldolgozás (NLP) mély tanulására koncentrál a Azure Lab Services használatával. A természetes nyelvi feldolgozás (NLP) a mesterséges intelligencia (AI) formája, amely lehetővé teszi, hogy a számítógépek fordítással, beszédfelismeréssel és más nyelvi felismerési képességekkel rendelkeznek.  

Az NLP osztályba tartozó diákok linuxos virtuális gépet (VM) kapnak, amelyből megtudhatja, hogyan alkalmazhat neurális hálózati algoritmusokat az írott emberi nyelv elemzéséhez használt mély tanulási modellek fejlesztéséhez. 

## <a name="lab-configuration"></a>Tesztkörnyezet konfigurációja
A tesztkörnyezet beállításához Azure-előfizetésre van szükség a kezdéshez. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) . Ha már rendelkezik Azure-előfizetéssel, létrehozhat egy új Labor-fiókot Azure Lab Services vagy használhat egy meglévő Lab-fiókot is. A következő oktatóanyagban talál egy új Labor-fiókot: [oktatóanyag a labor-fiók beállításához](tutorial-setup-lab-account.md).
 
A labor-fiók létrehozása után engedélyezze a következő beállításokat a labor-fiókban: 

| Tesztkörnyezet-fiók beállítása | Utasítások |
| ----------- | ------------ |  
| Marketplace-rendszerképek | Engedélyezze a Data Science Virtual Machine for Linux (Ubuntu) rendszerképet a labor-fiókban való használathoz.  A következő cikkben talál útmutatást: a [tesztkörnyezet-készítők számára elérhető Piactéri lemezképek megadása](specify-marketplace-images.md). | 

[Ezt az oktatóanyagot](tutorial-setup-classroom-lab.md) követve hozzon létre egy új labort, és alkalmazza a következő beállításokat:

| Tesztkörnyezet beállításai | Érték/utasítások | 
| ------------ | ------------------ |
| Virtuális gép (VM) mérete | Kis GPU (számítás). Ez a méret a legjobb megoldás a nagy számítási igényű és a hálózati igényű alkalmazások, például a mesterséges intelligencia és a mély tanulás számára. |
| VM-rendszerkép | [Linux (Ubuntu) Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). Ez a rendszerkép mély tanulási keretrendszert és eszközöket biztosít a gépi tanuláshoz és az adatelemzéshez. A képen található telepített eszközök teljes listájának megtekintéséhez tekintse meg a következő cikket: [Mit tartalmaz a DSVM?](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Távoli asztali kapcsolat engedélyezése | <p>A beállítás engedélyezése lehetővé teszi a tanárok és a tanulók számára, hogy Távoli asztal (RDP) használatával csatlakozzanak a Virtual Machineshoz (VM).</p><p>**Fontos**: Ez a beállítás csak a Linux rendszerű gépeken lévő **RDP** -portot nyitja meg. Ha az RDP már telepítve van és konfigurálva van a virtuális gép rendszerképén, akkor a tanulók RDP-kapcsolaton keresztül csatlakozhatnak a virtuális gépekhez a további lépések követése nélkül. <p>Ha a virtuálisgép-rendszerképben nincs telepítve és konfigurálva az RDP, először az SSH-val kell csatlakoznia a Linux rendszerű géphez, és telepítenie kell az RDP-és GUI-csomagokat, hogy a tanulók később is csatlakozhassanak a Linux-számítógéphez. További információkért lásd: [Távoli asztal telepítése és konfigurálása egy linuxos virtuális géphez való kapcsolódáshoz az Azure-ban](../../virtual-machines/linux/use-remote-desktop.md). Ezt követően közzéteheti a képet, hogy a tanulók RDP-be tudják állítani a tanulói Linux virtuális gépeket.  |

A Linux-rendszerképekhez készült Data Science Virtual Machine az ilyen típusú osztályok számára szükséges mély tanulási keretrendszereket és eszközöket biztosít. Ennek eredményeképpen a sablon létrehozása után nem kell további testreszabást végeznie. A tanulók számára közzé lehet tenni. Kattintson a **Közzététel** gombra a sablon lapon a sablonnak a laborba való közzétételéhez.  

## <a name="cost"></a>Költségek
Ha a labor költségeit szeretné megbecsülni, a következő példát használhatja: 

Egy 25 tanulós osztály esetében, amely 20 órányi időpontot és 10 órányi kvótát biztosít a munkahelyhez vagy a hozzárendelésekhez, a labor díja 25 diák * (20 + 10) óra * 139 labor egység * 0,01 USD/óra = 1042,5 USD

További részletek a díjszabásról: [Azure Lab Services díjszabása](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Összegzés
Ez a cikk végigvezeti a tesztkörnyezet a természetes nyelvi feldolgozó osztályhoz való létrehozásának lépésein. Hasonló beállításokat használhat más Deep learning-osztályokhoz.

## <a name="next-steps"></a>További lépések
A következő lépések közösek a laborok beállításához:

- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemterv beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-mail-regisztráció a tanulók számára](how-to-configure-student-usage.md#send-invitations-to-users). 

