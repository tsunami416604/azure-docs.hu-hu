---
title: Az Azure verem Development Kit (ASDK) történő bemutatása |} Microsoft Docs
description: A ASDK ismerteti és a Microsoft Azure verem értékelése gyakori alkalmazási esetei.
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
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 951cd1adc09373b9af560097b088fd740ceb51a8
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850627"
---
# <a name="what-is-the-azure-stack-development-kit"></a>Mi az az Azure verem szoftverfejlesztői készlet?
[A Microsoft Azure verem integrált rendszerek](.\.\azure-stack-poc.md) mérete a 4 – 12 csomópontok között, és a hardver partnerek és a Microsoft által közösen támogatott. Integrált Azure verem rendszerek használatával engedélyezze a termelési számítási feladatokhoz új forgatókönyvek használhatók. Ha egy Azure verem operátort, akik az integrált rendszerek infrastruktúrával kezeli és szolgáltatásokat biztosít, tekintse meg a [operátor dokumentáció](https://docs.microsoft.com/azure/azure-stack).

Az Azure verem Development Kit (ASDK) Azure-vermet, töltse le, és használja egy egy csomópontos telepítési **szabad**. Minden ASDK összetevőinek telepítése a virtuális gépek gazdagépen futó egy egyetlen kiszolgáló kell elérik vagy túllépik a [hardverre vonatkozó minimális elvárásokat](asdk-deploy-considerations.md#hardware). A ASDK célja, hogy olyan környezetet, amelyben Azure verem kiértékelheti és API-k és tooling eszköz konzisztensek legyenek az Azure a modern alkalmazások fejlesztése biztosítson egy *nem éles* környezetben. 

> [!IMPORTANT]
> A ASDK nem használhatók fel-vagy éles környezetben támogatott.

A ASDK összetevőket egy egyetlen development kit állomás számítógépen van telepítve, mert nincsenek korlátozott fizikai erőforrásokat. Az ASDK központi telepítésével, az Azure-verem infrastruktúra virtuális gépek és a bérlői virtuális gépeket lehet kiszolgáló ugyanazon a számítógépen. Ez a konfiguráció nem készült méretezési vagy a teljesítmény kiértékelése.

A ASDK arra tervezték, hogy az Azure-konzisztens hibrid felhő felhasználói élményt nyújtja a:
- **A rendszergazdák** (Azure verem operátorok). A ASDK értékeli, és ismerje meg az elérhető Azure verem szolgáltatások kiváló forrást.
- **A fejlesztők**. A ASDK fejlesztéséhez hibrid vagy a modern alkalmazások helyszíni (fejlesztési/tesztelési környezetben) használható. Ez kínál ismételhetőség előtt, vagy együtt, Azure verem üzemelő fejlesztési felület. 

A rövid videót a ASDK tájékozódhat:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>ASDK és több csomópontos Azure verem különbségek
Több csomópontos rendszerekhez. Azure-verem kell ügyelnie, néhány fontos módszerek egycsomópontos ASDK központi telepítések eltérnek.

|Leírás|ASDK|Több csomópontos Azure verem|
|-----|-----|-----|
|**Méretezés**|Az összes összetevő egy egy csomópontos kiszolgáló számítógépen telepített.|Mérete a 4 – 12 csomópontok között lehet.|
|**Rugalmasság**|Single-csomópont-konfiguráció nem biztosít magas rendelkezésre állás|[Magas elérhetőségét](.\.\azure-stack-key-features.md#high-availability-for-azure-stack) képességek támogatottak.|
|**Hálózat**|A ASDK AzS-BGPNAT01 nevű virtuális gép irányíthatja az összes ASDK hálózati forgalom használ. Nincsenek további kapcsoló.|A AzS-BGPNAT01 VM nem létezik a több csomópontos rendszerekhez. Összetettebb [útválasztási infrastruktúrán hálózati](.\.\azure-stack-network.md#network-infrastructure) szükséges, beleértve a tor (TOR), a alaplapi felügyeleti vezérlő (BMC) és a szegély (adatközpont hálózatán) kapcsolók.|
|**Javítási és frissítési folyamat**|Helyezze át a ASDK egy új verziója, újra kell telepítenie az development kit állomáson ASDK.|[Javítása és frissítése](.\.\azure-stack-updates.md) a telepített Azure verem frissítésére szolgáló folyamat.|
|**Támogatás**|Azure-verem MSDN fórum. A Microsoft ügyfél- és szolgálathoz (CSS) támogatás *nem* érhető el, nem éles környezetben.|[Azure-verem MSDN fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) és teljes CSS támogatja.|
| | |

## <a name="learn-about-available-services"></a>További információk az elérhető szolgáltatások
Kezelőként Azure verem kell tudni, hogy mely szolgáltatások elérhetővé teheti a felhasználók számára. Azure verem támogatja az Azure-szolgáltatások egy részét, és továbbra is támogatott szolgáltatások listájában verzióinformációk.

### <a name="foundational-services"></a>Eligazodást szolgáltatások
Alapértelmezés szerint az Azure-verem tartalmazza a következő "eligazodást szolgáltatások" a ASDK telepítésekor:
- Compute
- Storage
- Hálózat
- Key Vault

Ezekkel a legalapvetőbb szolgáltatásokkal kínálhat minimális konfigurációval a felhasználók infrastruktúra,--szolgáltatás (IaaS).

### <a name="additional-services"></a>További szolgáltatások
Jelenleg a következő további Platform,--szolgáltatás (PaaS) szolgáltatások támogatottak:
- App Service
- Azure Functions
- Az SQL és a MySQL-adatbázisok

> [!NOTE]
> Ezek a szolgáltatások további beállításokra van szükség, mielőtt is elérhetővé azokat a felhasználókat, és nem érhetők el alapértelmezés szerint a ASDK telepítésekor.

## <a name="service-roadmap"></a>Szolgáltatás terv
Az Azure verem továbbra is támogatásáról további Azure-szolgáltatásokhoz. Mi az Azure veremnek megfelelő tovább várható kapcsolatos további tudnivalókért lásd: a [Azure verem terv](https://azure.microsoft.com/roadmap/?tag=azure-stack). 


## <a name="next-steps"></a>További lépések
Első lépésként Azure verem értékelése, elő kell készíteni a development kit állomás számítógépén, majd [telepítése a ASDK](asdk-install.md). Ezt követően is bejelentkezik a rendszergazda és a felhasználói portálon Azure verem elindítására.