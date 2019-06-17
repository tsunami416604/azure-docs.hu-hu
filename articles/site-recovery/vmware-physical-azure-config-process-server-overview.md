---
title: Az Azure Site Recovery konfigurációs, folyamat, és fő célkiszolgálók |} A Microsoft Docs
description: Ez a cikk áttekintést a konfigurációt, a folyamat, valamint a fő célkiszolgálókat, ha állít be vészhelyreállítást helyszíni VMware virtuális gépek az Azure-bA az Azure Site Recovery használatával
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 8b3815fc9dc44484779a70b51ebff4802265d53a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66417737"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Tudnivalók a Site Recovery-összetevők (konfigurációs, folyamat, fő célkiszolgáló)

Ez a cikk ismerteti a konfiguráció, a folyamat, valamint a VMware virtuális gépek és fizikai kiszolgálók replikálása az Azure-ban használt fő célkiszolgálókat a [Site Recovery](site-recovery-overview.md) szolgáltatás.

## <a name="configuration-server"></a>Konfigurációs kiszolgáló

A helyszíni VMware virtuális gépek és fizikai kiszolgálók vészhelyreállítása, szüksége lesz egy Site Recovery konfigurációs kiszolgáló üzembe helyezte a helyszínen.

**Beállítás** | **Részletek** | **Hivatkozások**
--- | --- | ---
**Összetevők**  | A konfigurációs kiszolgáló gép fut, a helyszíni Site Recovery minden összetevőjét, többek között a konfigurációs kiszolgáló, folyamatkiszolgáló és fő célkiszolgáló.<br/><br/> A konfigurációs kiszolgáló beállításakor a rendszer automatikusan telepíti az összetevők. | [Olvasási](vmware-azure-common-questions.md#configuration-server) a konfigurációs kiszolgáló gyakori kérdések.
**Szerepkör** | A konfigurációs kiszolgáló koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és felügyeli az adatreplikációt. | További információ az architektúrát [VMware](vmware-azure-architecture.md) és [fizikai kiszolgáló](physical-azure-architecture.md) vészhelyreállítás az Azure-bA.
**A VMware követelményeinek** | A helyszíni VMware virtuális gépek vész-helyreállítási telepítse és futtassa a konfigurációs kiszolgáló, egy helyszíni, magas rendelkezésre állású VMware virtuális gép. | [Ismerje meg](vmware-azure-deploy-configuration-server.md#prerequisites) az előfeltételeket.
**VMware-telepítés** | Azt javasoljuk, hogy a konfigurációs kiszolgáló OVA letöltött sablon használatával helyezze üzembe. Ezzel a módszerrel egy egyszerűen állítható be a konfigurációs kiszolgáló, amely megfelel az összes követelmények és előfeltételek.<br/><br/> Ha valamilyen okból nem tud a VMware virtuális gép üzembe helyezése egy OVA-sablonnal, állíthat be a konfigurációs kiszolgáló gépek manuálisan, a fizikai gépek vészhelyreállítására alább leírt módon. | [Üzembe helyezése](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) OVA-sablonnal.
**Fizikai kiszolgáló követelmények** | Vész-helyreállítási a helyszíni fizikai kiszolgálókon, akkor a konfigurációs kiszolgáló telepítése manuálisan. | [Ismerje meg](physical-azure-set-up-source.md#prerequisites) az előfeltételeket.
**Fizikai kiszolgáló telepítése** | Ha VMware virtuális gépként nem telepíthető, telepítheti egy fizikai kiszolgálón. | [Üzembe helyezése](physical-azure-set-up-source.md#set-up-the-source-environment) a konfigurációs kiszolgálót manuálisan.


## <a name="process-server"></a>Folyamatkiszolgáló

**Beállítás** | **Részletek** | **Hivatkozások**
--- | --- | ---
**Üzembe helyezés**  | Vészhelyreállítás és a helyszíni VMware virtuális gépek és fizikai kiszolgálók replikálását, szüksége lesz a folyamatkiszolgálók a helyszínen. Alapértelmezés szerint a folyamatkiszolgáló telepítve van a konfigurációs kiszolgálón üzembe helyezésekor. | [További információk](vmware-azure-architecture.md?#architectural-components).
**(Helyszíni szerepkör** | -A replikációs adatokat fogad a gépek engedélyezve van a replikáció.<br/> – Optimalizálja a gyorsítótárazás, tömörítés és titkosítás a replikációs adatokat, és elküldi azt az Azure Storage.<br/> -Hajt végre egy a Site Recovery mobilitási szolgáltatás leküldéses telepítéséhez a helyszíni VMware virtuális gépek és fizikai kiszolgálókon, amelyet replikálni szeretne.<br/> -Elvégzi a helyszíni gépek automatikus felderítését. | [További információk](vmware-physical-azure-config-process-server-overview.md#process-server). 
**Szerepkör (feladat-visszavétel az Azure-ból)** | A helyszíni helyről a feladatátvételt követően akkor állíthatja be a folyamatkiszolgáló az Azure-ban, egy Azure virtuális gép feladat-visszavétel kezeléséhez a helyszíni helyre.<br/><br/> A folyamatkiszolgáló az Azure-ban csak átmenetileg létezik. Az Azure virtuális gép feladat-visszavétel befejezése után lehet törölni. | [További információk](vmware-azure-set-up-process-server-azure.md).
**Méretezés** | Nagyobb telepítések esetén a helyszíni állíthat be további, a horizontális felskálázási folyamatkiszolgáló. További kiszolgálók horizontális felskálázás több replikáló gépek és a replikációs forgalom nagyobb mértékű kezeli a kapacitást.<br/><br/> Áthelyezheti a gépek folyamat két kiszolgáló, annak érdekében, hogy a replikációs forgalom között. | [További](vmware-azure-set-up-process-server-scale.md),


## <a name="master-target-server"></a>Fő célkiszolgáló

A fő célkiszolgáló az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat.

- Ez a konfigurációs kiszolgálón alapértelmezés szerint telepítve van.
- Nagyméretű környezetekben hozzáadhat egy további, különálló fő célkiszolgálót a feladat-visszavételhez.


## <a name="next-steps"></a>További lépések
- Tekintse át a [architektúra](vmware-azure-architecture.md) vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók.
- Tekintse át a [követelmények és előfeltételek](vmware-physical-azure-support-matrix.md) vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók Azure-bA. 
