---
title: "Az Azure Security Center gyors üzembe helyezés – előkészítéséről a Security Center Standard Azure-előfizetése |} Microsoft Docs"
description: "A gyors üzembe helyezés jeleníti meg a Security Center Standard tarifacsomag további biztonsági frissítése."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/07/2018
ms.author: terrylan
ms.openlocfilehash: ac4e3b36b08223f7e3b54850ed53a8185e85f0d2
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Gyors üzembe helyezés: Bevezetni az Azure Security Center szokásos-előfizetéshez
A hibrid felhő-munkaterhelések egységes felügyeleti és a fenyegetések védelmet lehetővé teszi az Azure Security Center. Az ingyenes csomagot az Azure-erőforrások csak korlátozott biztonságot nyújt, amíg a Standard csomagot ezeket a képességeket a helyszíni és a többi felhőből terjeszti ki. A Security Center Standard segítségével megkeresheti és javítsa ki a biztonsági rések, hozzáférési és alkalmazás-szabályozási blokkolja a rosszindulatú tevékenységhez, elemzés és az eszközintelligencia segítségével fenyegetések észlelése és, gyorsan képesek reagálni támadás alatt. Próbálja meg biztonsági központ szabványos ingyenesen az első 60 nap.

Ebben a cikkben, váltson Standard csomagra a nagyobb biztonság, és a Microsoft Monitoring Agent telepítését a virtuális gépek biztonsági réseket és a fenyegetések figyelésére.

## <a name="prerequisites"></a>Előfeltételek
A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).

Az előfizetés váltson Standard csomagra, akkor hozzá kell rendelni a szerepkört az előfizetés tulajdonosa, előfizetés közreműködő vagy biztonsági rendszergazdával.

## <a name="enable-your-azure-subscription"></a>Az Azure-előfizetés engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://azure.microsoft.com/features/azure-portal/).
2. A **Microsoft Azure** menüben válassza a **Security Center** elemet. **A Security Center – áttekintés** nyílik meg.

 ![A Security Center áttekintése][2]

**A Security Center – áttekintés** azokat a biztonsági állapotát a hibrid felhő-munkaterhelések, amely lehetővé teszi felderítése és a munkaterhelések biztonsági értékeléséhez és azonosítása és a kockázatnak a mérséklése egyesített nézetét jeleníti meg. A Security Center automatikusan lehetővé teszi, hogy az Azure-előfizetések valamelyikét, vagy az ingyenes csomagot egy másik előfizetés felhasználó nem volt korábban előkészítve.

Megtekintheti és lehetőségre kattintva megjelenítheti az előfizetések listáját az **előfizetések** menüpont. A Security Center most elkezdi értékeli ezeket az előfizetéseket a biztonsági rések azonosítása biztonságát. Az értékelések típusú testreszabásához módosíthatja a biztonsági házirend. A biztonsági szabályzat határozza meg a számítási feladatokhoz tartozó kívánt konfigurációkat, és segít biztosítani a vállalati vagy hatósági követelményeknek való megfelelést.

A Security Center elindításához először percen belül jelenhetnek meg:

- **Javaslatok** biztonságosabbá teheti az Azure-előfizetések módjai a. Kattintson a **javaslatok** csempe elindítja a prioritási sorrend listájáról.
- Leltár **számítási**, **hálózati**, **adatok & tárterület**, és **alkalmazások** által most értékelési erőforrások A Security Center mellett minden egyes biztonsági állapotát.

A Security Center teljes körű kihasználása érdekében szüksége, váltson Standard csomagra, és a Microsoft Monitoring Agent telepítése az alábbi lépések végrehajtásához.

## <a name="upgrade-to-the-standard-tier"></a>Váltson Standard csomagra
A Security Center quickstarts és oktatóanyagok frissítenie kell a Standard csomagra. Az első hatvan szabad, és visszatérhet a ingyenes szint bármikor.

1. Az a Security Center fő menüben válassza a **speciális biztonsági bevezetési**.

2. A **speciális biztonsági bevezetési**, a Security Center előfizetések és a jogosult a bevezetési munkaterületek sorolja fel. Válassza ki az előfizetést a listából.

  ![Előfizetés kiválasztása][4]

3. **Biztonsági házirend** információt nyújt az erőforráscsoportok az előfizetés szerepel. **Árképzési** is megnyílik.
4. A **árazás**, jelölje be **szabványos** verzióra való frissítés szabad szabvány, majd kattintson **mentése**.

  ![Standard kiválasztása][5]

Most már végzett korábban is Standard csomagra, hogy további Security Center szolgáltatások, beleértve a **adaptív alkalmazás vezérlők**, **csak a virtuális gép elérhető**, **biztonsági riasztások**, **eszközintelligencia fenyegetés**, **automation playbooks**, stb. Vegye figyelembe, hogy a biztonsági riasztások csak akkor jelenik meg, amikor a Security Center észleli a rosszindulatú tevékenységhez.

  ![Biztonsági riasztások][7]

## <a name="automate-data-collection"></a>Adatgyűjtés automatizálásához
A Security Center adatokat gyűjti össze az Azure virtuális gépek és -Azure számítógépek biztonsági réseket és a fenyegetések figyelésére. Adatgyűjtés használata a Microsoft Monitoring Agent, különböző biztonsági konfigurációkat és Eseménynapló beolvassa a számítógépről, és másolja az adatokat a munkaterületre elemzés céljából. Alapértelmezés szerint a Security Center új munkaterület hozza létre.

Ha automatikus kiépítés engedélyezve van, a Security Center telepíti a Microsoft Monitoring Agent minden támogatott Azure virtuális gépek és a létrehozott újakat. Automatikus kiépítés erősen ajánlott.

Az a Microsoft Monitoring Agent automatikus kiépítés engedélyezéséhez:

1. Az a Security Center fő menüben válassza a **biztonsági házirend**.
2. Válassza ki az előfizetést.
3. A **biztonsági házirend**, jelölje be **adatgyűjtés**.
4. A **adatgyűjtés**, jelölje be **a** az Automatikus kiépítés engedélyezéséhez.
5. Kattintson a **Mentés** gombra.

  ![Automatikus kiépítés engedélyezése][6]

Az új betekintést nyerhet az Azure virtuális gépeken a Security Center biztosíthat rendszer kapcsolatos további javaslatok állapota, az operációs rendszer biztonsági beállításokkal, az endpoint protection frissítése, valamint további biztonsági riasztásokat.

  ![Javaslatok][8]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Más quickstarts és a gyűjtemény-kra vonatkozó a gyors üzembe helyezés épül. Ha azt tervezi, a későbbi quickstarts és oktatóanyagok munka folytatásához, továbbra is fut, a Standard csomagot, és tartsa be az Automatikus kiépítés engedélyezve van. Ha nem tervezi a folytatáshoz, vagy visszatérhet az ingyenes csomagot szeretne:

1. Térjen vissza a Security Center főmenübe, és válassza ki **biztonsági házirend**.
2. Válassza ki az előfizetés vagy való visszatéréshez szabad beállítani kívánt szabályzatot. **Biztonsági házirend** nyílik meg.
3. A **csoportházirend-összetevők**, jelölje be **tarifacsomag**.
4. Válassza ki **szabad** módosítása a Standard szint az ingyenes csomagba.
5. Kattintson a **Mentés** gombra.

Ha szeretne automatikus kiépítés letiltása:

1. Térjen vissza a Security Center főmenübe, és válassza ki **biztonsági házirend**.
2. Válassza ki az előfizetést, amelyet meg kíván automatikus kiépítés letiltása.
3. A **biztonsági szabályzat – adatgyűjtés**, jelölje be **ki** alatt **bevezetési** automatikus kiépítés letiltása.
4. Kattintson a **Mentés** gombra.

>[!NOTE]
> Automatikus kiépítés letiltása nem a Microsoft Monitoring Agent eltávolítása Azure virtuális gépeken, ha az ügynök van kiépítve. Az automatikus létesítési korlátok biztonsági megfigyelés letiltása az erőforrások.
>

## <a name="next-steps"></a>További lépések
A gyors üzembe helyezés Standard csomagra frissíteni, és között a hibrid felhő-munkaterhelések egységes biztonsági kezelése és a veszélyforrások elleni védelem a Microsoft Monitoring Agent kiépítve. Ha többet szeretne megtudni a Security Center használata, továbbra is a gyors üzembe helyezés bevezetési Windows rendszerű számítógépek, amelyek a helyszíni és a többi felhőből.

> [!div class="nextstepaction"]
> [Gyors üzembe helyezés: Beépített Windows-számítógépek számára az Azure Security Center](quick-onboard-windows-computer.md)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/onboarding.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
