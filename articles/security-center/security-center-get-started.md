---
title: Azure Security Center rövid útmutató – Azure-előfizetés felvétele a Security Center Standard verziójába | Microsoft Docs
description: Ez a rövid útmutató bemutatja, hogyan frissíthet a Security Center Standard tarifacsomagjára a biztonság további erősítése érdekében.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: rkarlin
ms.openlocfilehash: 8a0467a383e2c8e3efab685187c5a37d1549f664
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52838159"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Rövid útmutató: Azure-előfizetés felvétele a Security Center Standard verziójába
Az Azure Security Center egységes biztonsági felügyeletet és fenyegetésvédelmet biztosít a hibrid felhőalapú számítási feladatokhoz. Míg az Ingyenes szint korlátozott biztonságot nyújt, és azt is csak az Azure-erőforrások számára, a Standard szint kiterjeszti ezeket a képességeket a helyszíni és egyéb felhőkre. A Security Center segítségével megtalálhatja és kijavíthatja a biztonsági réseket, hozzáférés- és alkalmazásvezérlőket alkalmazhat a kártékony tevékenységek blokkolásához, észlelheti a fenyegetéseket az analitika és az intelligencia használatával, valamint gyorsan reagálhat, ha a rendszer támadás alatt áll. Megpróbálhatja Security Center Standard költségek nélkül. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).

Ebben a cikkben frissíteni fog a Standard szintre a további biztonság érdekében, és telepíteni fogja a Microsoft Monitoring Agentet a virtuális gépekre a biztonsági rések és fenyegetések monitorozásához.

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

A Security Center képességeinek teljes körű kihasználása érdekében végre kell hajtania az alábbi lépéseket a Standard szintre való frissítéshez és a Microsoft Monitoring Agent telepítéséhez.

## <a name="upgrade-to-the-standard-tier"></a>Frissítés a Standard szintre
A Security Center rövid útmutatóinak és oktatóanyagainak elvégzéséhez frissítenie kell a Standard szintre. Security Center Standard ingyenes próbaverziója van. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/). 

1. A Security Center főmenüjében válassza az **Első lépések** elemet.
 
  ![Bevezetés][4]

2. A **Frissítés** területen a Security Center felsorolja az előkészítésre jogosult előfizetéseket és munkaterületeket. 
   - A **Próbaverzió alkalmazása** elemre kattintva megtekintheti előfizetéseinek és munkaterületeinek listáját, valamint a próbaidőszakkal kapcsolatos jogosultsági állapotukat.
   -    Frissítheti azokat az előfizetéseket és munkaterületeket, amelyek nem jogosultak a próbaidőszakra.
   -    A próbaidőszak elkezdéséhez válassza ki az erre jogosult munkaterületeket és előfizetéseket.
3.  A **Próbaverzió indítása** gombbal elindítja a próbaidőszakot a kiválasztott előfizetéseken.


  ![Biztonsági riasztások][9]

## <a name="automate-data-collection"></a>Adatgyűjtés automatizálása
A Security Center adatokat gyűjt az Azure-beli virtuális gépekről és a nem Azure-beli számítógépekről a biztonsági rések és fenyegetések megfigyeléséhez. Az adatgyűjtés a Microsoft Monitoring Agent segítségével történik, amely a biztonsághoz kapcsolódó különböző konfigurációkat és eseménynaplókat olvas be a gépről, és elemzés céljából átmásolja az adatokat az Ön munkaterületére. Alapértelmezés szerint a Security Center létre fog hozni egy új munkaterületet.

Ha az automatikus kiépítés engedélyezve van, a Security Center telepíti a Microsoft Monitoring Agentet az összes támogatott Azure-beli és újonnan létrehozott virtuális gépen. Az automatikus kiépítés használata erősen ajánlott.

A Microsoft Monitoring Agent automatikus kiépítésének engedélyezése:

1. A Security Center főmenüjében válassza a **Biztonsági szabályzat** elemet.
2. Az előfizetést tartalmazó soron kattintson a **Beállítások szerkesztése>** elemre.
3. Az **Adatgyűjtés** lapon állítsa az **Automatikus telepítést** **Be** értékre.
4. Kattintson a **Mentés** gombra.
****
  ![Automatikus kiépítés engedélyezése][6]

Az Azure-beli virtuális gépekbe való új betekintésnek köszönhetően a Security Center további javaslatokkal láthatja el Önt a rendszerfrissítés állapotával, az operációs rendszer biztonsági konfigurációival és a végpontvédelemmel kapcsolatban, illetve további biztonsági riasztásokat is létrehozhat.

  ![Javaslatok][8]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, továbbra is a Standard szintet futtassa, és hagyja engedélyezve az automatikus kiépítést. Ha nem folytatja a munkát, vagy vissza szeretne térni az Ingyenes szintre:

1. Térjen vissza a Security Center főmenüjébe, és válassza a **Biztonsági szabályzat** elemet.
2. Azon a soron, amelyen az ingyenes állapotba visszaállítani kívánt előfizetés található, válassza ki a **Beállítások szerkesztése>** elemet.
3. A **Tarifacsomag** pont alatt válassza az **Ingyenes** lehetőséget az előfizetés Standard szintről Ingyenes szintre való módosításához.
5. Kattintson a **Mentés** gombra.

Ha le szeretné tiltani az automatikus kiépítést:

1. Térjen vissza a Security Center főmenüjébe, és válassza a **Biztonsági szabályzat** elemet.
2. Válassza ki a **Beállítások szerkesztése>** elemet azon a soron, amelyen az az előfizetés található, amelyiknek le szeretné tiltani az automatikus telepítését.
3. Az **Adatgyűjtés** lapon állítsa az **Automatikus telepítést** **Ki** értékre.
4. Kattintson a **Mentés** gombra.

>[!NOTE]
> Az automatikus kiépítés letiltása nem távolítja el a Microsoft Monitoring Agentet azon Azure-beli virtuális gépekről, ahol az ügynök üzembe lett helyezve. Az automatikus kiépítés letiltása korlátozza az erőforrások biztonsági monitorozását.
>

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban frissített a Standard szintre, és kiépítette a Microsoft Monitoring Agentet, hogy biztosítsa az egységes biztonsági felügyeletet és fenyegetésvédelmet a hibrid felhőalapú számítási feladatokon. Ha többet szeretne megtudni a Security Center használatáról, folytassa a helyszínen és egyéb felhőszolgáltatásokban található Windows rendszerű számítógépek felvételével foglalkozó oktatóanyaggal.

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
