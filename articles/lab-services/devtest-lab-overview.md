---
title: Az Azure DevTest Labs névjegye | Microsoft dokumentumok
description: Ismerje meg, hogyan teheti lehetővé a DevTest Labs az Azure virtuális gépek létrehozását, kezelését és figyelését
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1b9eed3b-c69a-4c49-a36e-f388efea6f39
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: b7cd6bb1fd0377ca1440d9c667453df922aacbd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561455"
---
# <a name="about-azure-devtest-labs"></a>Információk az Azure DevTest Labs szolgáltatásról
Az Azure DevTest Labs lehetővé teszi a csapatok fejlesztői számára, hogy hatékonyan kezeljék a virtuális gépeket (VM-eket) és a PaaS-erőforrásokat anélkül, hogy jóváhagyásra várnának.

A DevTest Labs előre konfigurált bázisokból vagy Azure Resource Manager-sablonokból álló teszteredményeket hoz létre. Ezek rendelkeznek az összes szükséges eszközzel és szoftverrel, amelyekkel környezeteket hozhat létre. Néhány perc alatt létrehozhat környezeteket, nem pedig órákat vagy napokat.

A DevTest Labs használatával az alábbi feladatok elvégzésével tesztelheti az alkalmazások legújabb verzióit:

- Windows- és Linux-környezetek gyors kiépítése újrafelhasználható sablonok és összetevők használatával.
- Az igény szerinti környezetek kiépítéséhez egyszerűen integrálhatja az üzembe helyezési folyamatait a DevTest Labs-zel.
- A terheléstesztelés tágíthatja több tesztügynök kiépítésével, és hozhat létre előre kiépített környezeteket a betanításhoz és a bemutatókhoz.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Funkciók
A DevTest Labs a következő képességeket biztosítja a virtuális gépekkel dolgozó fejlesztőknek:

- Hozzon létre virtuális gépeket gyorsan kevesebb, mint öt egyszerű lépés végrehajtásával.
- Válasszon a virtuálisgép-bázisok válogatott listájából, amelyek et a csoportvezető vagy a központi informatikai szakember konfigurált, jóváhagyott és engedélyezett.
- Hozzon létre virtuális gépeket előre létrehozott egyéni lemezképekből, amelyek rendelkeznek az összes szoftver rel és eszközökkel. 
- Hozzon létre virtuális gépeket olyan képletekből, amelyek lényegében egyéni lemezképek, a virtuális gépek létrehozásakor telepített szoftver legújabb buildjeivel kombinálva. 
- Telepítse azokat a összetevőket, amelyek a virtuális gépeken üzembe helyezett bővítmények a kiépítésük után.
- Állítsa be az automatikus leállítási és automatikus indítási ütemezéseket a virtuális gépeken.
- Igényeljen egy előre létrehozott virtuális gép anélkül, hogy a létrehozási folyamat.

A DevTest Labs a következő képességeket biztosítja a PaaS-környezetekkel dolgozó fejlesztők számára:

- Az Erőforrás-kezelő vel gyorsan létrehozhat PaaS-környezeteket háromnál rövidebb egyszerű lépéssel.
- Válasszon az Erőforrás-kezelő sablonok válogatott listájából, amelyek et a csoportvezető vagy a központi informatikai szakember engedélyezte.
- Egy üres erőforráscsoport (sandbox) egy Erőforrás-kezelő sablon használatával az Azure-t egy tesztkörnyezetben vizsgálja.

A DevTest Labs lehetővé teszi a központi informatikai rendszer számára a hulladékok ellenőrzését, az erőforrások költségeinek optimalizálását, valamint a következő feladatok elvégzésével a költségvetéseken belüli tartózkodást:  

- Automatikus leállítás és automatikus indításütemezésbeállítása a virtuális gépeken.
- Házirendek beállítása a felhasználók által létrehozható virtuális gépek számára vonatkozóan.
- Házirendek beállítása a virtuális gépek méreteés a galéria képeket, amelyek a felhasználók közül választhatnak.
- A költségek nyomon követése és a laborok céltábláinak beállítása.
- Értesítést kap a laborok magas tervezett költségeiről, így megteheti a szükséges műveleteket.

A DevTest Labs a következő előnyökkel jár a felhőben lévő környezetek létrehozása, konfigurálása és kezelése során.

## <a name="cost-control-and-governance"></a>Költségellenőrzés és irányítás
A DevTest Labs megkönnyíti a költségek szabályozását azáltal, hogy lehetővé teszi a következő feladatok elvégzését:

- [Állítsa be a szabályzatok a laborok,](devtest-lab-get-started-with-lab-policies.md)például a virtuális gépek felhasználónkénti vagy laboronkénti száma. 
- Hozzon létre házirendeket a virtuális gépek [automatikus leállításához](devtest-lab-set-lab-policy.md) és indításához.
- Kövesse nyomon a virtuális gépek és a PaaS-erőforrások költségeit a laborokon belül, hogy [a költségvetésen](devtest-lab-configure-cost-management.md)belül maradjon.
- Maradjon a laborok kontextusában, hogy ne pörögjön fel rajtuk kívül.

## <a name="quickly-get-to-ready-to-test"></a>Gyorsan felkészüla tesztelésre
A DevTest Labs lehetővé teszi, hogy előre kiépített környezeteket hozzon létre, amelyek mindennel rendelkeznek, amire a csapatnak szüksége van az alkalmazások fejlesztéséhez és teszteléséhez. Csak [azt állítják, a környezetben,](devtest-lab-add-claimable-vm.md) ahol az utolsó jó épít az alkalmazás telepítve van, és kezdj el dolgozni. Vagy használjon tárolókat a még gyorsabb, karcsúbb környezet létrehozásához.

## <a name="create-once-use-everywhere"></a>Hozzon létre egyszer, használja mindenhol
A [PaaS-környezetsablonjainak](devtest-lab-create-environment-from-arm.md) és [összetevőinek](add-artifact-repository.md) rögzítése és megosztása a csapaton vagy szervezeten belül – mind a forrásellenőrzés ben – a fejlesztői és tesztelési környezetek egyszerű létrehozásához.

## <a name="worry-free-self-service"></a>Gondtalan önkiszolgáló
A DevTest Labs lehetővé teszi a fejlesztők és a tesztelők számára, hogy gyorsan és egyszerűen [hozzanak létre IaaS virtuális gépeket](devtest-lab-add-vm.md) és [PaaS-erőforrásokat](devtest-lab-create-environment-from-arm.md) előre konfigurált erőforrások használatával.

## <a name="use-iaas-and-paas-resources"></a>Az IaaS- és PaaS-erőforrások használata 
A fejlesztők a PaaS-erőforrásokat, például az Azure Service Fabric-fürtöket, az Azure App Service Web Apps szolgáltatását és a SharePoint-farmokat is felpörgethetik a Resource Manager-sablonok használatával. A PaaS laborokban való használatának megkezdéséhez használja a [nyilvános környezeti tárházból](devtest-lab-configure-use-public-environments.md) származó sablonokat, vagy [csatlakoztassa a labort a saját Git-tárházához.](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories) Ezen erőforrások költségeit is nyomon követheti, hogy a költségkereten belül maradjon.

## <a name="integrate-with-your-existing-toolchain"></a>Integrálás meglévő eszközláncával
Az előre elkészített beépülő modulok vagy az API segítségével közvetlenül az előnyben részesített [folyamatos integrációs (CI) eszközből,](devtest-lab-integrate-ci-cd-vsts.md)integrált fejlesztői környezetből (IDE) vagy automatikus kiadási folyamatból helyezhet el fejlesztési/tesztelési környezeteket. Az átfogó parancssori eszközt is használhatja.

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- Ha többet szeretne megtudni a DevTest Labs- ről, olvassa el a [DevTest Labs fogalmait.](devtest-lab-concepts.md)
- A részletes útmutató részletes utasításokat, olvassa [el az oktatóanyag: Labor beállítása az Azure DevTest Labs használatával.](tutorial-create-custom-lab.md)