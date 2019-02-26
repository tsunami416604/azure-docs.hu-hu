---
title: Osztályterem-tesztkörnyezetek használata betanítások – Azure Lab Services |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure DevTest Labs-képzési forgatókönyveihez.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: spelluru
ms.openlocfilehash: 4d2ba11181977f1976b5ae933e8b93a92424fa96
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56808644"
---
# <a name="use-classroom-labs-for-trainings"></a>Osztályterem-tesztkörnyezetek használata betanítások
Beállíthat egy tesztkörnyezetet betanítások. Tanterem Laborgyakorlatok az Azure Lab Services lehetővé teszi a képzési tesztkörnyezetek létrehozása amelyben minden tanuló használ a betanításhoz azonos és elkülönített környezetben. Annak érdekében, hogy képzési környezetek elérhető minden tanuló, csak akkor, amikor szükség van rájuk, és tartalmaz elég erőforrások – például a virtuális gépek – szükséges a képzés-szabályzatok alkalmazása. 

![Osztályterem-tesztkörnyezet](../media/classroom-labs-scenarios/classroom.png)

Osztályterem-tesztkörnyezetek megfelel a következő képzést bármely virtuális környezetben szükséges: 

- Résztvevők gyorsan üzembe helyezhet a betanítási környezetek
- Minden Azure-képzési gép azonosnak kell lenniük.
- Résztvevők nem látható egyéb résztvevők által létrehozott virtuális gépek
- Költség szabályozhatja, hogy a résztvevők nem olvasható be a szükséges a képzési, valamint a virtuális gépek leállítása, ha azok nem használja őket több virtuális gépet biztosítva
- A képzési labor könnyedén megoszthatja minden tanuló
- A képzési labor újra és újra felhasználhatja

Ez a cikk bemutatja, amely megfelel a dátumtáblázatok fenti képzési követelményekkel és részletes lépéseket követve állítsa be a képzési tesztkörnyezetek segítségével különböző Azure Lab Services szolgáltatásairól.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>A lab fiók létrehozása a lab fiók rendszergazdaként
Az Azure Lab Services használatának első lépése, hogy a labor-fiók létrehozása az Azure Portalon. Miután egy tesztlabor fiókadminisztrátort a labor-fiókot, a rendszergazda hozzáadja a felhasználók, akik létre szeretné hozni a laborgyakorlat a **tesztkörnyezet létrehozója** szerepkör. Az oktatók labort létrehozni ehhez a tanfolyam azok tantárgyak gyakorlatok diákoknak virtuális gépekkel. További információkért lásd: [létrehozása és a labor-fiók kezelése](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-classroom-labs"></a>Hozzon létre és osztályterem-tesztkörnyezetek kezelése
Egy trainer, aki tagja a tesztkörnyezet létrehozója szerepkör a lab-fiókokban, a labor-fiók egy vagy több labs hozhat létre. Hozzon létre, és a Virtuálisgép-sablon konfigurálásához a szükséges szoftverhez adatelemzésre gyakorlatok a tanfolyam során. Válasszon egy előre elkészített képet osztályterem-tesztkörnyezet létrehozásához az elérhető rendszerképek közül, és majd testre szabhatja a labor létrehozása szükséges a szoftver telepítésével. További információkért lásd: [létrehozása és kezelése az osztályterem-tesztkörnyezetek](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>És használati beállítások és szabályzatok konfigurálása
A tesztkörnyezet létrehozója hozzáadhat vagy eltávolíthat felhasználókat a labor létrehozása, szerezzen be regisztrációs hivatkozást labor felhasználókat, például egyéni kvóták beállítással felhasználónként, frissítést el a tesztkörnyezetben, és további virtuális gépek szabályzatok beállítása. További információkért lásd: [és használati beállítások és szabályzatok konfigurálása](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Ütemezések létrehozása és felügyelete
Ütemezésekkel osztályterem-tesztkörnyezet konfigurálása, hogy a labor virtuális gépeinek automatikusan elindítani, és állítsa le a megadott időpontban. Egyszeri ütemezés szerint vagy ismétlődő ütemezés szerint határozhatja meg. További információkért lásd: [létrehozása és ütemezése a osztályterem-tesztkörnyezetek kezelése](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Állítsa be, és a virtuális gép sablon közzététele
A tesztkörnyezet sablonja egy alapszintű virtuálisgép-rendszerkép, amelyből az összes felhasználó virtuális gépe létrejön. A Virtuálisgép-sablon beállítása, hogy pontosan mit szeretne biztosítani a betanítási résztvevőknek lesz konfigurálva. A sablonhoz megadhat egy nevet és egy leírást, amely a tesztkörnyezet felhasználói számára jelenik meg. Ezt követően a sablont a Virtuálisgép-sablon példányainak a labor számára elérhetővé tehet közzé. Amikor közzétesz egy sablont, az Azure Lab Services létrehozza a virtuális gépeket a tesztkörnyezetben a sablon használatával. A folyamat során létrehozott virtuális gépek száma megegyezik a tesztkörnyezet felhasználóinak maximális számával, amelyet a tesztkörnyezet használati szabályzatában állíthat be. A virtuális gépek konfigurációja megegyezik a sablonéval. További információkért lásd: [állítsa be, és közzététele a sablon virtuális gépek](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Virtuális gépek használata az osztályterem-tesztkörnyezet
Egy tanuló vagy a képzés résztvevői a laborhoz regisztrálja, és letöltésekhez a tanfolyam ehhez a virtuális gép csatlakozik. További információkért lásd: [osztályterem-tesztkörnyezet elérése](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>További lépések
Indítsa el a tesztkörnyezetfiók osztályterem-tesztkörnyezetek létrehozása a cikkben található utasításokat követve: [Oktatóanyag: Az Azure Lab Services tesztkörnyezetfiók beállítása](tutorial-setup-lab-account.md).