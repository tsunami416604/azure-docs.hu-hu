---
title: Minták – ISO 27001 Megosztott szolgáltatások terve – Áttekintés
description: Az ISO 27001 Megosztott szolgáltatások tervmintájának áttekintése és architektúrája.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
---
# <a name="overview-of-the-azure-blueprints-iso-27001-shared-services-blueprint-sample"></a>Az ISO 27001 Azure Blueprints Megosztott szolgáltatások tervmintájának áttekintése

Az ISO 27001 Megosztott szolgáltatások tervmintája a szabványoknak megfelelő infrastruktúra-minták és szabályzati védőkorlátok együttesét biztosítja, amelyek segítséget nyújtanak az ISO 27001-tanúsítvány megszerzéséhez. E terv segítségével az ügyfelek olyan felhőalapú architektúrákat helyezhetnek üzembe, amelyek akkreditációs vagy megfelelőségi követelményekkel rendelkező alkalmazási helyzetekre kínálnak megoldást.

Az [ISO 27001 App Service Environment/SQL Database-számításifeladat](../iso27001-ase-sql-workload/index.md) tervminta e minta kiegészítéseként szolgál.

## <a name="architecture"></a>Architektúra

Az ISO 27001 Megosztott szolgáltatások tervminta alapszintű infrastruktúrát helyez üzembe az Azure-ban, amelynek segítségével a szervezetek több, virtuális adatközponti (VDC) megközelítésen alapuló számítási feladatot üzemeltethetnek.
A VDC referenciaarchitektúrák, automatizáló eszközök és bevonási modellek bevált együttese, amelyet a Microsoft legnagyobb vállalati ügyfelei használnak. A Megosztott szolgáltatások tervminta az alább látható, teljesen natív Azure VDC-környezeten alapul.

![Az ISO 27001 Megosztott szolgáltatások tervminta kialakítása](../../media/sample-iso27001-shared/iso27001-shared-services-blueprint-sample-design.png)

Ez a környezet több Azure-szolgáltatásból épül fel, és ISO 27001 szabványokon alapuló, biztonságos, teljes körűen monitorozott, vállalati használatra kész megosztottszolgáltatás-infrastruktúrát biztosít. A környezet összetevői:

- [Szerepköralapú hozzáférés-vezérlési](../../../../role-based-access-control/overview.md) (RBAC) szerepkör, amellyel a feladatkörök elkülöníthetők a vezérlősík szempontjából. Az infrastruktúra üzembe helyezését megelőzően a következő három szerepkör lett meghatározva:
  - A NetOps szerepkör a hálózati környezet, például a tűzfalbeállítások, NSG-beállítások, útválasztás és egyéb hálózatkezelési funkciók kezeléséhez rendelkezik jogosultsággal
  - A SecOps szerepkör az [Azure Security Center](../../../../security-center/security-center-intro.md) kezeléséhez és üzembe helyezéséhez, [Azure-szabályzatok](../../../policy/overview.md) meghatározásához szükséges jogosultsággal, illetve egyéb, biztonsághoz kapcsolódó jogosultságokkal is rendelkezik
  - A SysOps szerepkör az egyéb működési jogosultságok mellett az [Azure-szabályzatok](../../../policy/overview.md) előfizetésen belüli meghatározásához, valamint a [Log Analytics](../../../../azure-monitor/overview.md) teljes környezetre kiterjedő kezeléséhez szükséges jogosultságokkal is rendelkezik
- A [Log Analytics](../../../../azure-monitor/overview.md) elsőként lesz üzembe helyezve az Azure-szolgáltatások közül. Ez biztosítja, hogy a biztonságos üzembe helyezés megkezdésétől fogva az összes művelet és szolgáltatás naplózása egyetlen központi helyen történjen
- Alhálózatokat támogató virtuális hálózat helyszíni adatközponthoz való csatlakozáshoz, bemeneti és kimeneti verem az internetkapcsolathoz, valamint NSG-ket és ASG-ket használó megosztott szolgáltatási alhálózat a teljes körű mikroszegmentáláshoz, amely a következőket foglalja magába:
  - Felügyeleti célra használt jumpbox vagy bástyagazdagép, amely csak a bemeneti verem alhálózatán üzembe helyezett [Azure-tűzfalon](../../../../firewall/overview.md) keresztül érhető el
  - Az Active Directory Directory Servicest (ADDS) futtató két virtuális gép és a DNS csak a jumpboxon keresztül érhető el, és kizárólag úgy konfigurálható, hogy az AD-t VPN-en vagy [ExpressRoute-kapcsolaton](../../../../expressroute/expressroute-introduction.md) keresztül replikálja (a terv nem helyezi üzembe)
  - Az [Azure Net Watcher](../../../../network-watcher/network-watcher-monitoring-overview.md) és a standard DDoS Protection használata
- Az [Azure Key Vault](../../../../key-vault/key-vault-whatis.md) egy példánya, amely a megosztott szolgáltatások környezetében üzembe helyezett virtuális gépek titkos kulcsainak tárolására szolgál

A fentiek mindegyike teljesíti az [Azure Architecture Center referenciaarchitektúrákkal foglalkozó részében](/azure/architecture/reference-architectures/) közzétett, bevált módszerek követelményeit.

> [!NOTE]
> Az ISO 27001 Megosztott szolgáltatások infrastruktúra adja a számítási feladatok alapvető architektúráját.
> Ezen alapvető architektúrán kívül továbbra is üzembe kell helyezni számítási feladatokat.

További információért tekintse meg a [virtuális adatközpont dokumentációját](/azure/architecture/vdc/).

## <a name="next-steps"></a>További lépések

Ezzel megismerte az ISO 27001 Megosztott szolgáltatások tervmintájának áttekintését és architektúráját.
Következő lépésként tekintse meg az alábbi cikkeket, amelyek a vezérlőelem-leképezést és a minta üzembe helyezését ismertetik:

> [!div class="nextstepaction"]
> [ISO 27001 Megosztott szolgáltatások terve – Vezérlőelem-leképezés](./control-mapping.md)
> [ISO 27001 Megosztott szolgáltatások terve – Üzembehelyezési lépések](./deploy.md)

További cikkek a tervekről és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.