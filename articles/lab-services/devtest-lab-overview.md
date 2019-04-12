---
title: Tudnivalók az Azure DevTest Labs |} A Microsoft Docs
description: Ismerje meg, hogyan DevTest Labs is könnyen létrehozása, kezelése és monitorozása az Azure-beli virtuális gépek
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
ms.openlocfilehash: e07149865d2dda52e33003964c2852a8aaccf76f
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59493195"
---
# <a name="about-azure-devtest-labs"></a>Tudnivalók az Azure DevTest Labs szolgáltatásban
Az Azure DevTest Labs lehetővé teszi a fejlesztők a munkacsoportok hatékonyabban önálló kezelhetők a virtuális gépek (VM) és a PaaS-erőforrásokhoz jóváhagyások várakozás nélkül.

DevTest Labs, előre konfigurált adatbázisok vagy az Azure Resource Manager-sablonok álló labs hoz létre. Ezek rendelkezik a szükséges eszközöket és szoftvereket, amelyek segítségével hozhat létre környezeteket. Órák vagy napok helyett, néhány perc alatt létrehozhat környezeteket.

DevTest Labs használatával tesztelheti az alkalmazások a legfrissebb verzióit a következő feladatok végrehajtásával:

- Gyorsan üzembe helyezhet Windows és Linux-környezetek újrahasznosítható sablonok és összetevők használatával.
- Egyszerűen integrálhatja az üzembe helyezési folyamatait a DevTest Labs szolgáltatással igény szerinti környezetek kiépítéséhez.
- Vertikális felskálázás terheléstesztelését több tesztügynök kiépítésével és a képzés és a demók előre kiépített környezeteket hozhat létre.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]
> 
> 

## <a name="capabilities"></a>Funkciók
DevTest Labs szolgáltatásban a virtuális gépek dolgozó fejlesztők az alábbi képességeket biztosítja:

- Virtuális gépek gyors létrehozásához a következő kevesebb mint öt egyszerű lépésben.
- Válassza ki a Virtuálisgép-adatbázisok konfigurált, jóváhagyott, és a csapat vezető vagy a központi által rendszerezett listáját informatikai.
- Hozzon létre a virtuális gépek előre létrehozott egyéni lemezképek, a szoftverek és eszközök már telepítve van. 
- Virtuális gépek létrehozása az egyéni rendszerképekből a szoftver telepítve van a virtuális gépek létrehozásakor a legújabb buildjei kombinálva.
- -Összetevők, amelyek az üzembe helyezett virtuális gépeken, miután azok üzemelnek bővítmények telepítése.
- Állítsa be az automatikus leállítási és automatikusan elinduló ütemezések virtuális gépeken.
- Jogcím egy előre létrehozott virtuális Gépet a létrehozási folyamat áthaladás nélkül.

DevTest Labs szolgáltatásban az alábbi képességeket biztosítja a fejlesztők számára a PaaS-környezetek használata:

- Resource Manager használatával PaaS-környezetek gyors létrehozását a következő kevesebb mint három egyszerű lépésben.
- Válassza ki a Resource Manager-sablonokat, amelyek a konfigurált és a csapat vezető vagy a központi által rendszerezett listáját informatikai.
- Üres erőforráscsoport (védőfal) labor kontextusában Fedezze fel az Azure Resource Manager-sablon használatával helyezhet üzembe.

DevTest Labs lehetővé teszi a központi informatikai hulladékok szabályozhatja, optimalizálhatja a költségeket az erőforrások és a következő feladatok végrehajtásával költségvetése maradva: 

- Virtuális gépek automatikus leállítása és automatikus indítási ütemezéseket beállítása.
- Szabályzatok beállítása a felhasználók hozhatnak létre virtuális gépek száma.
- Szabályzatok beállítása a virtuális gépek méretei és katalógus rendszerkép található, a felhasználók közül választhat.
- Költségeit, és a tárolók labs beállítást.
- Első magas előre jelzett költséget laborral kapcsolatos értesítést, így a szükséges műveleteket hajthatja végre.

DevTest Labs létrehozása, konfigurálása és kezelése a környezeteket a felhőben található a következő előnyöket biztosítja.

## <a name="control-costs-and-governance"></a>Költségek korlátozására és irányítás
DevTest Labs szolgáltatással egyszerűbben csökkenthetők a költségek azáltal, hogy a következő feladatokat végezheti el:

- Házirendek beállítása a gyakorlatot, például a felhasználónkénti vagy egy tesztlabor virtuális gépek száma. 
- Olyan házirendeket hoz létre automatikusan leállítja, majd indítsa el a virtuális gépeket.
- A virtuális gépek és PaaS-erőforrásokat nyomon követheti a költségek fonva mentése belül labs a költségvetésen belül marad.
- Legyen naprakész a gyakorlatot kontextusában így erőforrásokat kívül nem indíthat el.

## <a name="quickly-get-to-ready-to-test"></a>Gyorsan hozzáférhet kész-teszt
DevTest Labs lehetővé teszi minden fejlesztőcsapatának szüksége alkalmazások fejlesztéséhez és teszteléséhez felszerelt előre kiépített környezeteket hozhat létre. Csak igényeljen környezetet, ahol az alkalmazás a legutóbbi megfelelő buildje telepítve van, és megkezdi a munkát. És még gyorsabb és Karcsúbb környezeteket alakíthat ki a tárolók használatával.

## <a name="create-once-use-everywhere"></a>Ha egyszer létrehozta, bárhol használhatja
Rögzítheti és megoszthatja a PaaS-környezetben sablonok és összetevők a csapat vagy szervezet – az összes verziókövetési rendszerben – egyszerűen létrehozása a fejlesztői és tesztelési környezetek.

## <a name="save-time-on-setup"></a>Időt takaríthat meg a telepítő  
Az előre konfigurált erőforrások használatával egyszerűen IaaS virtuális gépeknél és PaaS-erőforrásokat hozhat létre.

## <a name="use-iaas-and-paas-resources"></a>IaaS és PaaS-erőforrások használata 
A fejlesztők is is üzembe helyezése PaaS-erőforrásokhoz, például az Azure Service Fabric-fürtök, a Web Apps funkció az Azure App Service és a SharePoint-farmok, Resource Manager-sablonok használatával. Első lépésként a PaaS Labs-környezetben a sablonok használata a nyilvános környezetben adattárból, vagy a labor csatlakozhat a saját Git-tárház. Is követheti ezeket az erőforrásokat a költségvetést költségeket.

## <a name="integrate-with-your-existing-toolchain"></a>Integráció a meglévő eszközlánccal
Előre elkészített modulok vagy az API-t üzembe fejlesztési-tesztelési környezetet közvetlenül a kívánt folyamatos integrációs (CI) eszközről, az integrált fejlesztőkörnyezetből (IDE), vagy automatizált kiadási folyamattal. Az átfogó parancssori eszközt is használhatja.

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- DevTest Labs kapcsolatos további információkért lásd: [DevTest Labs-fogalmak](devtest-lab-concepts.md).
- Lépésről lépésre egy bemutatóért lásd: [oktatóanyag: Tesztkörnyezet beállítása az Azure DevTest Labs használatával](tutorial-create-custom-lab.md).


