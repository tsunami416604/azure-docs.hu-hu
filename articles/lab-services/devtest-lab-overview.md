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
ms.openlocfilehash: 599b4325e0eb9aa7f6ccca2626c6d4b14f38149d
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339329"
---
# <a name="about-azure-devtest-labs"></a>Tudnivalók az Azure DevTest Labs szolgáltatásban
Az Azure DevTest Labs egy szolgáltatása, amely lehetővé teszi a virtuális gépek hatékony önkiszolgáló és/vagy PaaS-erőforrásokhoz, hogy az eszközöket, amelyek szükségük van a konstans jóváhagyások várakozás nélkül a fejlesztés, tesztelés, betanítási és bemutatók stb. kell egy csapatban. 

A tesztkörnyezet már előre konfigurált adatbázisok vagy az összes szükséges eszközöket és szoftvereket, hogy a fejlesztők a környezetek létrehozása Resource Manager-sablonok áll. A környezetek létrehozhatnak órák vagy napok helyett pár perc alatt. 

DevTest Labs használatával ellenőrizheti az alkalmazás legújabb verzióját a következő feladatok végrehajtásával:

- Gyors üzembe helyezési Windows és Linux-környezetek újrahasznosítható sablonok és összetevők használatával
- Egyszerűen integrálhatja az üzembe helyezési folyamatait a DevTest Labs szolgáltatással igény szerinti környezetek kiépítéséhez
- Vertikális felskálázás terheléstesztelését több tesztügynök kiépítésével és a képzés és a demók előre kiépített környezeteket hozhat létre.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]
> 
> 

## <a name="capabilities"></a>Funkciók
DevTest Labs szolgáltatásban a virtuális gépek dolgozó fejlesztők az alábbi képességeket biztosítja:

- Hozzon létre egy virtuális gép gyorsan kevesebb mint öt egyszerű lépéseket követve.
- Válassza ki a Virtuálisgép-adatbázisok konfigurált, jóváhagyott, és a csapat vezető vagy a központi által rendszerezett listáját informatikai.
- Gépek, amelyek a szoftverek és a képen telepített eszközök, előre létrehozott egyéni rendszerképekből létrehozása. 
- Hozzon létre gépek képleteket is, amelyek lényegében az egyéni lemezképek és a legfrissebb a szoftver telepítve van a virtuális gép létrehozásának időpontjában.
- -Összetevők, amelyek a virtuális Gépet annak kiépítése után telepített bővítmények telepítése.
- Automatikus leállítás beállítása és automatikus indítási ütemezések a gépek leállítása rendelkezik a nap végén, és fel, és tovább reggel futtatása.
- Egyszerűen jogcím egy előre létrehozott virtuális gépet nem kell a folyamatot a gép létrehozásakor. 

DevTest Labs szolgáltatásban az alábbi képességeket biztosítja a fejlesztők számára a PaaS-környezetek használata:

- Kevesebb mint három egyszerű lépésben gyorsan következő PaaS Azure Resource Manager-környezetek létrehozása.
- Válassza ki a Resource Manager-sablonok, konfigurált, engedélyezett és a csapat vezető vagy a központi által rendszerezett listáját informatikai.
- A Resource Manager-sablon használatával megismerheti az Azure teljes maradva háríthatják továbbra is a labor kontextusában üres erőforráscsoport (tesztkörnyezet) üzembe helyezése.
- DevTest Labs a következő előnyökkel létrehozása, konfigurálása és kezelése a fejlesztői és tesztelési környezeteket a felhőben

Egy csapat a fejlesztők számára egy önkiszolgáló modell szereplőkkel a szolgáltatás lehetővé teszi a központi informatikai hulladékok szabályozhatja, optimalizálhatja a költségeket az erőforrásokat, és költségvetése belül a következő feladatok végrehajtásával: 

- Automatikus leállítás beállítása és automatikus indítása ütemezések virtuális gépeken.
- Szabályzatok beállítása a felhasználók virtuális gépeket hozhat létre.
- Szabályzatok beállítása a virtuális gépek méretei és a katalógus rendszerképek felhasználók közül választhat.
- Költségeit, és a tárolók a labor beállítása.
- Első értesítést a tesztkörnyezethez magas előre jelzett költségek, hogy a szükséges műveleteket hajthatja végre. 

DevTest Labs létrehozása, konfigurálása és kezelése környezeteket a felhőben az alábbi előnyöket nyújtja:

## <a name="cost-control-and-governance"></a>A Cost irányítása és szabályozása
DevTest Labs szolgáltatással egyszerűbben csökkenthetők a költségek azáltal, hogy a következő feladatokat végezheti el:

- Házirendek beállítása a laborgyakorlat – például a virtuális gépek (VM) száma felhasználónként és tesztlabor virtuális gépek száma. 
- Olyan házirendeket hoz létre automatikusan leállítja, majd indítsa el a virtuális gépeket.
- Lehetővé teszi a virtuális gépek és PaaS-erőforrásokat hoz létre a labor belül, hogy maradhat, ha az előre meghatározott költségvetésen belül nyomon követheti a költségek. 
- A fejlesztőket, hogy azok nem végül olyan erőforrások, a mögöttes erőforráscsoport vagy előfizetés-en kívül kell indítani a labor létrehozása a környezeten belül maradjon.

## <a name="quickly-get-to-ready-to-test"></a>Gyorsan hozzáférhet kész-teszt
DevTest Labs lehetővé teszi, hogy mindent a csapat által igényelt fejleszthetik és tesztelhetik az alkalmazások indítása az előre kiépített környezetek létrehozását. Egyszerűen jogcím a környezetekben, ahol az alkalmazás a legutóbbi megfelelő buildje telepítve van, és azonnal munka megkezdéséhez. És még gyorsabb és Karcsúbb környezeteket alakíthat ki a tárolók használatával.

## <a name="create-once-use-everywhere"></a>Ha egyszer létrehozta, bárhol használhatja
Rögzítése és a PaaS-környezetben sablonok és összetevők belül csapatával vagy cégével - mind a verziókövetés - megosztás létrehozása a fejlesztői és tesztelési környezetek.

## <a name="worry-free-self-service"></a>Önkiszolgáló aggódjon – ingyenes
DevTest Labs lehetővé teszi a fejlesztők és tesztelők gyorsan és egyszerűen hoz létre a virtuális gépek (IaaS) és a PaaS-erőforrásokhoz a számukra előre konfigurált erőforrások használatával néhány kattintással belül.

## <a name="use-iaas-and-paas-resources"></a>IaaS és PaaS-erőforrások használata 
DevTest Labs is lehetővé teszi a fejlesztők számára a PaaS-erőforrásokhoz, például a Web Apps, a Service Fabric-fürtök, a SharePoint-farmok üzembe helyezése, és így tovább. belül a labor létrehozása Resource Manager-sablon használatával. A Resource Manager-sablonok használata a nyilvános környezetben adattárból, vagy a labor csatlakozhat a saját Git-tárház első lépések a PaaS Labs-környezetben. Is követheti ezeket az erőforrásokat az előre meghatározott költségvetése belülre költségeket. 

## <a name="integrate-with-your-existing-toolchain"></a>Integráció a meglévő eszközlánccal
Az előre elkészített beépülő modulok vagy az API segítségével közvetlenül a kívánt folyamatos integrációs (CI) eszközről, a fejlesztési és tesztelési környezetet az integrált fejlesztőkörnyezetből (IDE), vagy az automatizált kibocsátási folyamatok kiépítéséhez. Az átfogó parancssori eszközt is használhatja.

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket: 

- DevTest Labs kapcsolatos további információkért lásd: [DevTest Labs-fogalmak](devtest-lab-concepts.md)
- Lépésről lépésre egy bemutatóért lásd: [oktatóanyag: Az Azure DevTest Labs-tesztkörnyezet beállítása](tutorial-create-custom-lab.md)


