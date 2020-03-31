---
title: B2B vállalati integráció
description: Ismerje meg az automatikus B2B-munkafolyamatok készítését a vállalati integrációhoz az Azure Logic Apps és a Vállalati integrációs csomag használatával
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 08/01/2019
ms.openlocfilehash: 9356cecb22672ae15505d14b5ac73d5baeef09e0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77191375"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>B2B vállalati integrációs megoldások az Azure Logic Alkalmazásokkal és a vállalati integrációs csomaggal

A vállalatok közötti (B2B) megoldások és a szervezetek közötti zökkenőmentes kommunikáció érdekében automatizált méretezhető vállalati integrációs munkafolyamatokat hozhat létre az [Azure Logic Apps](../logic-apps/logic-apps-overview.md)vállalati integrációs csomag (EIP) használatával. Bár a szervezetek különböző protokollokat és formátumokat használnak, elektronikus úton is válthatnak üzeneteket. Az EIP a különböző formátumokat olyan formátumba alakítja, amelyet a szervezetek rendszerei fel dolgozhatnak, és támogatja az iparági szabványnak megfelelő protokollokat, beleértve az [AS2,](../logic-apps/logic-apps-enterprise-integration-as2.md) [X12](logic-apps-enterprise-integration-x12.md)és [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md)protokollokat. Az üzenetek biztonságát titkosítással és digitális aláírásokkal is növelheti. Az EIP támogatja ezeket a [vállalati integrációs csatlakozókat](../connectors/apis-list.md#integration-account-connectors) és ezeket az iparági szabványokat:

* Elektronikus adatcsere (EDI)
* Vállalati alkalmazásintegráció (EAI)

Ha ismeri a Microsoft BizTalk Server vagy az Azure BizTalk Services szolgáltatást, az EIP hasonló fogalmakat követ, így a funkciók könnyen használhatók. Az egyik fő különbség azonban az, hogy az EIP építészetileg "integrációs számlákon" alapul, hogy egyszerűsítse a B2B kommunikációban használt műtermékek tárolását és kezelését. Ezek a fiókok felhőalapú tárolók, amelyek az összes összetevőt, például a partnereket, a megállapodásokat, a sémákat, a leképezéseket és a tanúsítványokat tárolják. 

## <a name="why-use-the-enterprise-integration-pack"></a>Miért érdemes használni az Enterprise Integration Pack csomagot?

* Az EIP segítségével az összes műtermékét egy helyen tárolhatja - integrációs fiókjában.

* B2B-munkafolyamatokat hozhat létre, és integrálhatja a külső szoftverszolgáltatásként (SaaS) alkalmazásokkal, helyszíni alkalmazásokkal és egyéni alkalmazásokkal az Azure Logic Apps és az összekötők használatával.

* Az Azure-függvényekkel egyéni kódot hozhat létre a logikai alkalmazásokhoz.

## <a name="how-do-i-get-started"></a>Hogyan kezdhetek hozzá?

Mielőtt megkezdhetné a B2B logikai alkalmazás munkafolyamatainak készítését az EIP-vel, az alábbi elemekre van szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Integrációs [fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a használni kívánt összetevőkkel

* Térképek és sémák létrehozásához használhatja a [Microsoft Azure Logic Apps vállalati integrációs eszközeit a Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas) és a Visual Studio 2015 számára.

Miután létrehozott egy integrációs fiókot, és hozzáadja a műtermékek, megkezdheti a B2B-munkafolyamatok létrehozása ezekkel az összetevőkkel egy logikai alkalmazás az Azure Portalon. Ha még nem ismert a logikai alkalmazások, próbáljon [meg létrehozni egy alapvető logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Ezek az összetevők, győződjön meg arról, hogy először is csatolja az integrációs fiók a logikai alkalmazás. Ezt követően a logikai alkalmazás hozzáférhet az integrációs fiókhoz. A Visual Studio vagy a [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp)használatával is létrehozhat, kezelhet és üzembe helyezhet logikai alkalmazásokat.

A B2B logikai alkalmazások létrehozásának első lépései az alábbiak:

![A B2B logikai alkalmazások létrehozásának előfeltételei](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>Kipróbálom most

[Teljesen működőképes mintalogikai alkalmazás üzembe helyezése, amely AS2-üzeneteket küld és fogad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>További lépések

* [Hozzon létre kereskedelmi partnereket](logic-apps-enterprise-integration-partners.md)
* [Megállapodások létrehozása](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [Sémák felvétele](logic-apps-enterprise-integration-schemas.md)
* [Térképek hozzáadása](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Migráció a BizTalk Servicesből](../logic-apps/logic-apps-move-from-mabs.md)
