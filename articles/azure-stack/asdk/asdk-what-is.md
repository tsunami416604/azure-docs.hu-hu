---
title: Bevezetés az Azure Stack Development Kit (ASDK) való |} A Microsoft Docs
description: Leírja, mi a ASDK és gyakori alkalmazási esetei kiértékelését a Microsoft Azure Stackhez.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 10/25/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 62eccba1b876ca8272dacbbd97a1d28c8b796081
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138310"
---
# <a name="what-is-the-azure-stack-development-kit"></a>Mi az az Azure Stack Development Kit?
[A Microsoft Azure Stack integrált rendszerek](.\.\azure-stack-poc.md) mérete 4 és 16 közötti csomópontjáról tartományt, és közösen egy hardver partner és a Microsoft által támogatott. Azure Stack integrált rendszerek használatával engedélyezhető az új forgatókönyvek használhatók a termelési számítási feladatokhoz. Ha Ön az Azure Stack operátorait, akik az integrált rendszerek infrastruktúrát felügyeli, és szolgáltatásokat kínál, tekintse meg a [operátori dokumentációja](https://docs.microsoft.com/azure/azure-stack).

Az Azure Stack Development Kit (ASDK) az egyetlen csomópontos üzemelő példánya, letöltheti és használhatja az Azure Stack **ingyenes**. Minden ASDK-összetevő telepítve van a virtuális gépek egy egyetlen számítógépen futó kell elérik vagy túllépik a [hardverre vonatkozó minimális](asdk-deploy-considerations.md#hardware). A ASDK hivatott környezet, amelyben az Azure Stack értékeli és API-k használatával, és az Azure-eszközkészlet modern alkalmazások fejlesztése a *nem éles* környezetben. 

> [!IMPORTANT]
> A ASDK nem készült fel-vagy éles környezetben használható.

Minden ASDK összetevője van telepítve egyetlen development kit gazdagépen, mert nincsenek korlátozott fizikai erőforrásokat. Az ASDK központi, az Azure Stack-infrastruktúra virtuális gépeinek és a bérlői virtuális gépek megtalálhatók a kiszolgáló-számítógépen. Ez a konfiguráció nem célja a méretezési csoport vagy a teljesítmény értékeléséhez.

Adjon meg egy Azure-konzisztens hibrid felhőalapú megoldás a ASDK célja:
- **A rendszergazdák** (az Azure Stack-operátorok). A ASDK egy nagyszerű forrás kiértékeléséhez, és ismerje meg a rendelkezésre álló Azure Stack-szolgáltatások.
- **A fejlesztők**. A ASDK fejleszthet hibrid, illetve a modern alkalmazások a helyszíni (fejlesztési-tesztelési környezetben) használható. Ez kínál a fejlesztési környezetet biztosít előtt, vagy az Azure Stack éles környezetekben üzemelő példányok mellett az ismételhetőség. 

Ez a rövid videóban további információ a ASDK:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>ASDK és a több csomópontos az Azure Stack különbségek
Egy csomópontos ASDK központi telepítések érdemes figyelembe vennie, néhány fontos módon különböznek a több csomópontos Azure Stack üzemelő példányok.

|Leírás|ASDK|Több csomópontos az Azure Stack|
|-----|-----|-----|
|**Méretezés**|Az összes összetevő egy egycsomópontos kiszolgálói számítógépre van telepítve.|A 4 és 16 közötti csomópontok mérete terjedhet.|
|**Rugalmasságának biztosításával**|Egy csomópontos konfigurációja nem biztosít magas rendelkezésre állás|[Magas rendelkezésre állásának](.\.\azure-stack-key-features.md#high-availability-for-azure-stack) funkciót támogatnak.|
|**Hálózat**|A ASDK nevű AzS-BGPNAT01 virtuális ASDK minden hálózati forgalmat útvonal használja. Nem vonatkoznak további kapcsoló követelmények.|A AzS-BGPNAT01 virtuális gép nem létezik a több csomópontos rendszerekhez. Összetettebb [hálózati útválasztási infrastruktúra](.\.\azure-stack-network.md#network-infrastructure) szükséges, beleértve a Top-Of-Rack (TOR), alaplapi felügyeleti vezérlő (BMC) és kapcsolók szegély (adatközpont-hálózat).|
|**Javítási és frissítési folyamat**|Helyezze át a ASDK új verziójára, ismét üzembe kell helyeznie a ASDK a development kit gazdagépen.|[Javítása és frissítése](.\.\azure-stack-updates.md) a telepített Azure Stack-verzió frissítésére szolgáló folyamat.|
|**Támogatás**|Fórum az MSDN Azure Stack. A Microsoft ügyfélszolgálata és a támogatási szolgálathoz (CSS) támogatás *nem* nem éles környezetekben érhető el.|[MSDN Azure Stack forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) és teljes CSS támogatja.|
| | |

## <a name="learn-about-available-services"></a>Elérhető szolgáltatások ismertetése
Kezelőként Azure Stack kell tudni, hogy mely szolgáltatások elérhetővé teheti a felhasználók számára. Az Azure Stack egy Azure-szolgáltatások részét támogatja, és a támogatott szolgáltatások teljes listájának továbbra is verzióinformációk.

### <a name="foundational-services"></a>Alapvető szolgáltatások
Alapértelmezés szerint az Azure Stack tartalmazza a következő "alapvető szolgáltatások" az ASDK telepítésekor:
- Compute
- Storage
- Hálózat
- Key Vault

Alapvető szolgáltatások infrastruktúra--szolgáltatásként (IaaS) elérhetővé teheti a felhasználók minimális konfigurációval.

### <a name="additional-services"></a>További szolgáltatások
Jelenleg a következő további Platform--szolgáltatásként (PaaS) szolgáltatások támogatottak:
- App Service
- Azure Functions
- SQL- és MySQL-adatbázisok

> [!NOTE]
> Ezek a szolgáltatások további konfigurációt igényelnek, is elérhetővé tétele a felhasználók számára, és nem érhetők el alapértelmezés szerint a ASDK telepítésekor.

## <a name="service-roadmap"></a>Szolgáltatás-ütemterv
További Azure-szolgáltatások támogatása az Azure Stack továbbra is. Az Azure Stack újdonságokat kapcsolatos további információkért tekintse meg a [Azure Stack ütemterv](https://azure.microsoft.com/roadmap/?tag=azure-stack). 


## <a name="next-steps"></a>További lépések
Ismerkedés az Azure Stack kiértékelése, először szüksége [töltse le a legújabb ASDK](asdk-download.md) és készítse elő a ASDK gazdaszámítógépen. Miután előkészítette a development kit gazdagép, a ASDK telepítse, és az Azure Stack használatának megkezdéséhez jelentkezzen be a rendszergazda és a felhasználói portálon.