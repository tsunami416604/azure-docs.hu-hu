---
title: Frissítés a standard szintű csomagra – Azure Security Center
description: Ez a rövid útmutató bemutatja, hogyan frissíthet a Security Center Standard tarifacsomagjára a biztonság további erősítése érdekében.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: memildin
ms.openlocfilehash: 3f0d624605f617a8e5ab914c49c4c94a40ebdcc6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80435779"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Rövid útmutató: Azure-előfizetés felvétele a Security Center Standard verziójába
Az Azure Security Center egységes biztonsági felügyeletet és fenyegetésvédelmet biztosít a hibrid felhőalapú számítási feladatokhoz. Míg az Ingyenes szint korlátozott biztonságot nyújt, és azt is csak az Azure-erőforrások számára, a Standard szint kiterjeszti ezeket a képességeket a helyszíni és egyéb felhőkre. A Security Center segítségével megtalálhatja és kijavíthatja a biztonsági réseket, hozzáférés- és alkalmazásvezérlőket alkalmazhat a kártékony tevékenységek blokkolásához, észlelheti a fenyegetéseket az analitika és az intelligencia használatával, valamint gyorsan reagálhat, ha a rendszer támadás alatt áll. Security Center Standard díjmentesen is kipróbálható. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).

Ebben a cikkben a biztonsági rések és fenyegetések figyelése érdekében a standard szintre frissíti a biztonságot, és telepíti a Log Analytics-ügynököt a virtuális gépekre.

## <a name="prerequisites"></a>Előfeltételek
A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).

A Standard szintre való frissítéshez Előfizetés tulajdonosa, Előfizetés közreműködője vagy Biztonsági rendszergazda hozzárendelt szerepkörrel kell rendelkeznie.

## <a name="enable-your-azure-subscription"></a>Azure-előfizetés engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://azure.microsoft.com/features/azure-portal/).
2. A **Microsoft Azure** menüben válassza a **Security Center** elemet. Megnyílik a **Security Center – Áttekintés** képernyő.

   ![Security Center – Áttekintés][2]

A **Security Center – Áttekintés** egységes képet ad a hibrid felhőbeli számítási feladatok biztonsági rendszeréről, lehetővé téve a számítási feladatok biztonsági állapotának felderítését és felmérését, valamint a kockázatok észlelését és csökkentését. A Security Center automatikusan engedélyezi az Ingyenes szintet bármely olyan Azure-előfizetés számára, amelyet Ön vagy egy másik előfizetés-felhasználó korábban még nem vett fel.

Az **Előfizetések** menüpontra kattintva megtekintheti és szűrheti az előfizetések listáját. A Security Center most fel fogja mérni az előfizetések biztonsági állapotát a biztonsági rések azonosításához. A felmérések típusainak testre szabásához módosíthatja a biztonsági szabályzatot. A biztonsági szabályzat határozza meg a számítási feladatokhoz tartozó kívánt konfigurációkat, és segít biztosítani a vállalati vagy hatósági követelményeknek való megfelelést.

A Security Center első indítása után néhány percen belül a következők jelenhetnek meg:

- **Javaslatok** az Azure-előfizetések biztonságának javítására. Ha a **Javaslatok** csempére kattint, elindít egy rangsorolt listát.
- A Security Center által éppen felmért **Számítás és alkalmazások**, **Hálózat**, **Adatbiztonság** és **Identitás és hozzáférés** erőforrások leltára, valamint ezek biztonsági állapota.

A Security Center teljes körű kihasználása érdekében az alábbi lépéseket kell elvégeznie a standard szintre való frissítéshez és a Log Analytics-ügynök telepítéséhez.

## <a name="upgrade-to-the-standard-tier"></a>Frissítés a Standard szintre
A Security Center rövid útmutatóinak és oktatóanyagainak elvégzéséhez frissítenie kell a Standard szintre. Security Center Standard ingyenes próbaverziót is rendelkezésre áll. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/). 

1. A Security Center főmenüjében válassza az **Első lépések** elemet.
 
   ![Első lépések][4]

2. A **Frissítés** területen a Security Center felsorolja az előkészítésre jogosult előfizetéseket és munkaterületeket. 
   - A **Próbaverzió alkalmazása** elemre kattintva megtekintheti előfizetéseinek és munkaterületeinek listáját, valamint a próbaidőszakkal kapcsolatos jogosultsági állapotukat.
   -    Frissítheti azokat az előfizetéseket és munkaterületeket, amelyek nem jogosultak a próbaidőszakra.
   -    A próbaidőszak elkezdéséhez válassza ki az erre jogosult munkaterületeket és előfizetéseket.
3. A **Próbaverzió indítása** gombbal elindítja a próbaidőszakot a kiválasztott előfizetéseken.


  ![Biztonsági riasztások][9]

## <a name="automate-data-collection"></a>Adatgyűjtés automatizálása
A Security Center adatokat gyűjt az Azure-beli virtuális gépekről és a nem Azure-beli számítógépekről a biztonsági rések és fenyegetések megfigyeléséhez. Az adatok gyűjtése a Log Analytics ügynök használatával történik, amely beolvassa a különböző biztonsággal kapcsolatos konfigurációkat és eseménynaplókat a gépről, és az adatokat a munkaterületre másolja az elemzéshez. Alapértelmezés szerint a Security Center létre fog hozni egy új munkaterületet.

Ha engedélyezve van az automatikus kiépítés, Security Center telepíti a Log Analytics ügynököt az összes támogatott Azure-beli virtuális gépre és a létrehozott újakra. Az automatikus kiépítés használata erősen ajánlott.

Az log Analytics-ügynök automatikus kiépítés engedélyezése:

1. A Security Center Főmenüben válassza a **díjszabás & beállítások**lehetőséget.
2. Az előfizetés sorában kattintson arra az előfizetésre, amelyre módosítani szeretné a beállításokat.
3. Az **Adatgyűjtés** lapon állítsa az **Automatikus telepítést****Be** értékre.
4. Kattintson a **Mentés** gombra.
---
  ![Automatikus kiépítés engedélyezése][6]

Az Azure-beli virtuális gépekbe való új betekintésnek köszönhetően a Security Center további javaslatokkal láthatja el Önt a rendszerfrissítés állapotával, az operációs rendszer biztonsági konfigurációival és a végpontvédelemmel kapcsolatban, illetve további biztonsági riasztásokat is létrehozhat.

  ![Javaslatok][8]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, továbbra is a Standard szintet futtassa, és hagyja engedélyezve az automatikus kiépítést. Ha nem folytatja a munkát, vagy vissza szeretne térni az Ingyenes szintre:

1. Térjen vissza a Security Center főmenüre, és válassza a **díjszabás & beállítások**lehetőséget.
2. Kattintson arra az előfizetésre, amelyet az ingyenes szintjére szeretne váltani.
3. A **Tarifacsomag** pont alatt válassza az **Ingyenes** lehetőséget az előfizetés Standard szintről Ingyenes szintre való módosításához.
5. Kattintson a **Mentés** gombra.

Ha le szeretné tiltani az automatikus kiépítést:

1. Térjen vissza a Security Center főmenüre, és válassza a **díjszabás & beállítások**lehetőséget.
2. Tisztítsa meg az előfizetést, amelynél le szeretné tiltani az automatikus kiépítés szolgáltatást.
3. Az **Adatgyűjtés** lapon állítsa az **Automatikus telepítést****Ki** értékre.
4. Kattintson a **Mentés** gombra.

>[!NOTE]
> Az automatikus kiépítés letiltása nem távolítja el a Log Analytics ügynököt az Azure-beli virtuális gépekről, ahol az ügynököt kiépítték. Az automatikus kiépítés letiltása korlátozza az erőforrások biztonsági monitorozását.
>

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban frissített a standard szintre, és kiépítte a Log Analytics ügynököt az egységes biztonsági felügyelet és a fenyegetések elleni védelem érdekében a hibrid felhőalapú számítási feladatokban. Ha többet szeretne megtudni a Security Center használatáról, folytassa a helyszínen és egyéb felhőszolgáltatásokban található Windows rendszerű számítógépek felvételével foglalkozó oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: Windows rendszerű számítógépek felvétele az Azure Security Centerbe](quick-onboard-windows-computer.md)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
