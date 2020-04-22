---
title: Biztonsági megoldások integrálása az Azure Security Centerbe | Microsoft Docs
description: Megtudhatja, hogy az Azure Security Center hogyan integrálható a partnerekkel az Azure-erőforrások általános biztonságának növelése érdekében.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: memildin
ms.openlocfilehash: 48869140ba8cd1a9598562b0057b0005d8fcd9c7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758018"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Biztonsági megoldások integrálása az Azure Security Centerbe
Ez a dokumentum az Azure Security Centerhez már csatlakoztatott biztonsági megoldások kezelésében és újak hozzáadásában segít.

## <a name="integrated-azure-security-solutions"></a>Integrált Azure biztonsági megoldások
A Security Center használatával egyszerűen engedélyezhet integrált biztonsági megoldásokat az Azure-ban. Az előnyök:

- **Egyszerűsített üzembe helyezés**: A Security Center segítségével az integrált partnermegoldások egy optimalizált folyamat mentén helyezhetőek üzembe. Olyan megoldások, mint a kártevőirtó és a biztonsági rés felmérése, a Security Center kiépítheti az ügynököt a virtuális gépeken. Tűzfalak esetében a Security Center gondoskodik a szükséges hálózati konfiguráció nagy részéről.
- **Integrált észlelések:** A partnermegoldások biztonsági eseményei automatikusan összegyűjtésre, összesítésre kerülnek, és a Security Center riasztásainak és incidenseinek részeként jelennek meg. Ezek az események más forrásoktól érkező észlelésekhez is kapcsolódnak, ami fejlett fenyegetésészlelési képességeket biztosít.
- **Egyesített állapotmonitorozás és -kezelés**: Az integrált állapotesemények lehetővé teszik az összes partnermegoldás gyors monitorozását. Az alapszintű felügyeletből könnyen elérhető a speciális beállítás a partnermegoldás használatával.

Jelenleg az integrált biztonsági megoldások közé tartozik a [Qualys,](https://www.qualys.com/public-cloud/#azure) valamint a [Rapid7](https://www.rapid7.com/products/insightvm/) és a Microsoft Application Gateway webalkalmazás tűzfalának biztonsági résfelmérése.

> [!NOTE]
> A Security Center nem telepíti a Log Analytics-ügynököt a partner virtuális készülékekre, mert a legtöbb biztonsági szállító tiltja a készülékeiken futó külső ügynökökhasználatát.

Ha többet szeretne megtudni a Qualys biztonsági résellenőrző eszközeinek integrálásáról, beleértve a szabványos szintű ügyfelek számára elérhető beépített lapolvasót, olvassa el a következő témakört: 

- [Integrált biztonsági rés szkenner virtuális gépek](built-in-vulnerability-assessment.md).
- [Partnerbiztonsági ellenőrző megoldás telepítése](partner-vulnerability-assessment.md).

A Security Center biztonsági réselemzést is kínál a következőkhez:

* SQL-adatbázisok – [lásd: Sebezhetőségértékelési jelentések feltárása a biztonsági résértékelési irányítópulton](security-center-iaas-advanced-data.md#explore-vulnerability-assessment-reports)
* Azure Container Registry rendszerképek – lásd: [Azure Container Registry integráció a Security Centerrel (előzetes verzió)](azure-container-registry-integration.md)

## <a name="how-security-solutions-are-integrated"></a>A biztonsági megoldások integrálása
A Security Centerből üzembe helyezett Azure biztonsági megoldások automatikusan csatlakoztatva vannak. Más biztonsági adatforrásokat is csatlakoztathat, beleértve a helyszíni vagy más felhőkön futó számítógépeket is.

[![Partnermegoldások integrációja](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Integrált Azure biztonsági megoldások és egyéb adatforrások kezelése

1. Az [Azure Portalon](https://azure.microsoft.com/features/azure-portal/)nyissa meg a **Security Center t.**

1. A Biztonsági központ menüjében válassza a **Biztonsági megoldások lehetőséget.**

A **Biztonsági megoldások** lapon láthatja az integrált Azure biztonsági megoldások állapotát, és alapvető felügyeleti feladatokat futtathat.

### <a name="connected-solutions"></a>Csatlakoztatott megoldások

A **Csatlakoztatott megoldások** szakasz olyan biztonsági megoldásokat tartalmaz, amelyek jelenleg a Security Centerhez csatlakoznak. Azt is mutatja, az egyes megoldások állapotát.  

![Csatlakoztatott megoldások](./media/security-center-partner-integration/connected-solutions.png)

A partneri megoldás állapota a következő lehet:

* **Egészséges** (zöld) - nincs egészségügyi probléma.
* **Egészségtelen** (piros) - van egy egészségügyi probléma, amely azonnali figyelmet igényel.
* **Leállította a jelentéskészítést** (narancssárga) - a megoldás leállt az állapotjelentésével.
* **Nem jelentett** (szürke) – a megoldás még nem jelentett semmit, és nem állnak rendelkezésre egészségügyi adatok. Előfordulhat, hogy a megoldás állapota nem lesz bejelentve, ha nemrég csatlakozott, és még mindig telepítve van.

> [!NOTE]
> Ha az állapotadatok nem állnak rendelkezésre, a Security Center megjeleníti az utolsó kapott esemény dátumát és időpontját, jelezve, hogy a megoldás jelentést tesz-e vagy sem. Ha nem állnak rendelkezésre állapotadatok, és az elmúlt 14 napban nem érkezett riasztás, a Security Center azt jelzi, hogy a megoldás nem kifogástalan, vagy nem jelent jelentést.
>
>

Válassza a **NÉZET** lehetőséget a további információkért és a következő beállításokért:

   - **Megoldáskonzol** – Megnyitja a megoldás felügyeleti élményét.
   - **VM csatolása** – Megnyitja az Alkalmazások csatolása lapot. Itt erőforrásokat kapcsolhat hozzá a partnermegoldáshoz.
   - **Megoldás törlése**
   - **Konfigurálás**

   ![Partneri megoldás részletei](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>Felderített megoldások

A Security Center automatikusan felderíti az Azure-ban futó, de a Security Centerhez nem kapcsolódó biztonsági megoldásokat, és megjeleníti a megoldásokat a **Felderített megoldások** szakaszban. Ezek a megoldások közé tartoznak az Azure-megoldások, például [az Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)és a partnermegoldások.

> [!NOTE]
> A Felderített megoldások funkcióhoz a Standard szintű Security Centerre az előfizetés szintjén van szükség. További információ a [díjszabási](security-center-pricing.md) szintekről.
>

Válassza a **CONNECT** lehetőséget a Biztonsági központtal integrálható megoldás alatt, és értesítést kapjon a biztonsági riasztásokról.

### <a name="add-data-sources"></a>Adatforrások hozzáadása

Az **Adatforrások hozzáadása** szakasz sorolja fel az egyéb csatlakoztatható adatforrásokat. Az ezekből a forrásokból származó adatok hozzáadásával kapcsolatos utasításokért kattintson a **HOZZÁADÁS** gombra.

![Adatforrások](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett a partnermegoldások Security Centerrel való integrálásával. A kapcsolódó információkat a következő cikkekben talál:

* [Biztonsági riasztások és javaslatok exportálása](continuous-export.md). Ismerje meg, hogyan lehet integrációt beállítani az Azure Sentinel vagy bármely más SIEM használatával.
* [Biztonsági állapot monitorozása a Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.