---
title: Azure Security Center rövid útmutató – Azure-előfizetés felvétele a Security Center Standard verziójába | Microsoft Docs
description: Ez a rövid útmutató bemutatja, hogyan frissíthet a Security Center Standard tarifacsomagjára a biztonság további erősítése érdekében.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: terrylan
ms.openlocfilehash: 0004db332ec13e23ed49f2c19e3454a516ca6a40
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2018
ms.locfileid: "29531672"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Rövid útmutató: Azure-előfizetés felvétele a Security Center Standard verziójába
Az Azure Security Center egységes biztonsági felügyeletet és fenyegetésvédelmet biztosít a hibrid felhőalapú számítási feladatokhoz. Míg az Ingyenes szint korlátozott biztonságot nyújt, és azt is csak az Azure-erőforrások számára, a Standard szint kiterjeszti ezeket a képességeket a helyszíni és egyéb felhőkre. A Security Center segítségével megtalálhatja és kijavíthatja a biztonsági réseket, hozzáférés- és alkalmazásvezérlőket alkalmazhat a kártékony tevékenységek blokkolásához, észlelheti a fenyegetéseket az analitika és az intelligencia használatával, valamint gyorsan reagálhat, ha a rendszer támadás alatt áll. Az első 60 napban díjmentesen próbálhatja ki a Security Center Standard verzióját.

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
- A Security rendszer által éppen felmért **Számítás**, **Hálózat**, **Tárolás és adatok**, és **Alkalmazások** erőforrások leltára, valamint ezek biztonsági állapota.

A Security Center képességeinek teljes körű kihasználása érdekében végre kell hajtania az alábbi lépéseket a Standard szintre való frissítéshez és a Microsoft Monitoring Agent telepítéséhez.

## <a name="upgrade-to-the-standard-tier"></a>Frissítés a Standard szintre
A Security Center rövid útmutatóinak és oktatóanyagainak elvégzéséhez frissítenie kell a Standard szintre. Az első 60 nap ingyenes, és bármikor visszatérhet az Ingyenes szintre.

1. A Security Center főmenüjében válassza a **Speciális biztonsági megoldás előkészítése** elemet.

2. A **Speciális biztonsági megoldás előkészítése** területen a Security Center kilistázza a felvételre jogosult előfizetéseket és munkaterületeket. Válasszon ki egy előfizetést a listából.

  ![Előfizetés kiválasztása][4]

3. A **Biztonsági szabályzat** információt nyújt az előfizetésben található erőforráscsoportokról. A **Díjszabás** is megnyílik.
4. A **Díjszabás** területen válassza ki a **Standard** elemet az Ingyenesről a Standard szintre való frissítéshez, majd kattintson a **Mentés** gombra.

  ![Standard kiválasztása][5]

Most, hogy frissített a Standard szintre, elérheti a Security Center további funkcióit, köztük az **adaptív alkalmazásvezérlőket**, az **igény szerinti virtuálisgép-hozzáférést**, a **biztonsági riasztásokat**, a **fenyegetésfelderítést**, az **automatizálási forgatókönyveket**, és egyéb funkciókat. Vegye figyelembe, hogy a biztonsági riasztások csak akkor jelennek meg, ha a Security Center kártékony tevékenységet észlel.

  ![Biztonsági riasztások][7]

## <a name="automate-data-collection"></a>Adatgyűjtés automatizálása
A Security Center adatokat gyűjt az Azure-beli virtuális gépekről és a nem Azure-beli számítógépekről a biztonsági rések és fenyegetések megfigyeléséhez. Az adatgyűjtés a Microsoft Monitoring Agent segítségével történik, amely a biztonsághoz kapcsolódó különböző konfigurációkat és eseménynaplókat olvas be a gépről, és elemzés céljából átmásolja az adatokat az Ön munkaterületére. Alapértelmezés szerint a Security Center létre fog hozni egy új munkaterületet.

Ha az automatikus kiépítés engedélyezve van, a Security Center telepíti a Microsoft Monitoring Agentet az összes támogatott Azure-beli és újonnan létrehozott virtuális gépen. Az automatikus kiépítés használata erősen ajánlott.

A Microsoft Monitoring Agent automatikus kiépítésének engedélyezése:

1. A Security Center főmenüjében válassza a **Biztonsági szabályzat** elemet.
2. Válassza ki az előfizetést.
3. A **Biztonsági szabályzat** területen válassza az **Adatgyűjtés** elemet.
4. Az **Adatgyűjtés** területen válassza a **Be** lehetőséget az automatikus kiépítés engedélyezéséhez.
5. Kattintson a **Mentés** gombra.

  ![Automatikus kiépítés engedélyezése][6]

Az Azure-beli virtuális gépekbe való új betekintésnek köszönhetően a Security Center további javaslatokkal láthatja el Önt a rendszerfrissítés állapotával, az operációs rendszer biztonsági konfigurációival és a végpontvédelemmel kapcsolatban, illetve további biztonsági riasztásokat is létrehozhat.

  ![Javaslatok][8]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, továbbra is a Standard szintet futtassa, és hagyja engedélyezve az automatikus kiépítést. Ha nem folytatja a munkát, vagy vissza szeretne térni az Ingyenes szintre:

1. Térjen vissza a Security Center főmenüjébe, és válassza a **Biztonsági szabályzat** elemet.
2. Válassza ki az előfizetést vagy a szabályzatot, amelyet vissza szeretne állítani Ingyenes állapotba. Megnyílik a **Biztonsági szabályzat** képernyő.
3. A **SZABÁLYZAT ÖSSZETEVŐI** alatt válassza a **Tarifacsomag** lehetőséget.
4. Válassza az **Ingyenes** lehetőséget az előfizetés Standard szintről Ingyenes szintre való módosításához.
5. Kattintson a **Mentés** gombra.

Ha le szeretné tiltani az automatikus kiépítést:

1. Térjen vissza a Security Center főmenüjébe, és válassza a **Biztonsági szabályzat** elemet.
2. Válassza ki azt az előfizetést, amelynél le szeretné tiltani az automatikus kiépítést.
3. Az automatikus kiépítés letiltásához a **Biztonsági szabályzat – Adatgyűjtés** területen válassza a **Ki** lehetőséget az **Előkészítés** elemnél.
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
[4]: ./media/security-center-get-started/onboarding.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
