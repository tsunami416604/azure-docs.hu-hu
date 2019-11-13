---
title: Tudnivalók Azure Site Recovery konfiguráció/folyamat/fő célkiszolgáló
description: Ez a cikk áttekintést nyújt azokról a konfigurációs, feldolgozási és fő célkiszolgáló-kiszolgálókról, amelyek a helyszíni VMware virtuális gépek vész-helyreállításának beállításakor az Azure-ba Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: ad816f39dd4182dfa41fca975c99824a5d77f860
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961310"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Tudnivalók Site Recovery összetevőkről (konfiguráció, folyamat, fő cél)

Ez a cikk a VMware virtuális gépek és fizikai kiszolgálók Azure-ba történő replikálásához használt konfigurációs, folyamat-és fő célkiszolgáló-kiszolgálókat ismerteti [site Recovery](site-recovery-overview.md) szolgáltatással.

## <a name="configuration-server"></a>Konfigurációs kiszolgáló

A helyszíni VMware virtuális gépek és fizikai kiszolgálók vész-helyreállításához a helyszínen üzembe helyezett Site Recovery konfigurációs kiszolgálóra van szükség.

**Beállítás** | **Részletek** | **Linkek**
--- | --- | ---
**Összetevők**  | A konfigurációs kiszolgáló számítógép az összes helyszíni Site Recovery-összetevőt futtatja, amely tartalmazza a konfigurációs kiszolgálót, a feldolgozási kiszolgálót és a fő célkiszolgáló-kiszolgálót.<br/><br/> A konfigurációs kiszolgáló beállításakor a rendszer az összes összetevőt automatikusan telepíti. | [Olvassa el](vmware-azure-common-questions.md#configuration-server) a konfigurációs kiszolgálóval kapcsolatos gyakori kérdéseket.
**Szerepkör** | A konfigurációs kiszolgáló koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és felügyeli az adatreplikációt. | További információ a [VMware](vmware-azure-architecture.md) és a [fizikai kiszolgáló](physical-azure-architecture.md) által az Azure-ba való vész-helyreállítás architektúráján.
**VMware-követelmények** | A helyszíni VMware virtuális gépek vész-helyreállításához a konfigurációs kiszolgálót helyszíni, magasan elérhető VMware virtuális gépre kell telepítenie és futtatnia. | [További](vmware-azure-deploy-configuration-server.md#prerequisites) információ az előfeltételekről.
**VMware üzemelő példány** | Javasoljuk, hogy a konfigurációs kiszolgálót egy letöltött PETESEJT-sablonnal telepítse. Ez a módszer egyszerűen beállítható egy olyan konfigurációs kiszolgáló beállítására, amely megfelel az összes követelménynek és előfeltételnek.<br/><br/> Ha valamilyen okból kifolyólag nem tudja telepíteni a VMware virtuális gépet egy PETESEJT-sablonnal, manuálisan is beállíthatja a konfigurációs kiszolgáló-gépeket, az alábbiakban leírtak szerint a fizikai gép vész-helyreállítási feladataihoz. | [Üzembe helyezés](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) petesejt-sablonnal.
**Fizikai kiszolgáló követelmények** | A helyszíni fizikai kiszolgálókon a vész-helyreállításhoz manuálisan kell telepíteni a konfigurációs kiszolgálót. | [További](physical-azure-set-up-source.md#prerequisites) információ az előfeltételekről.
**Fizikai kiszolgáló üzembe helyezése** | Ha nem telepíthető VMware virtuális gépként, akkor telepítheti azt egy fizikai kiszolgálóra. | [Telepítse](physical-azure-set-up-source.md#set-up-the-source-environment) manuálisan a konfigurációs kiszolgálót.


## <a name="process-server"></a>Kiszolgáló feldolgozása

**Beállítás** | **Részletek** | **Linkek**
--- | --- | ---
**Üzembe helyezés**  | A helyszíni VMware virtuális gépek és fizikai kiszolgálók vészhelyzeti helyreállításához és replikálásához helyszíni folyamatra van szükség. Alapértelmezés szerint a rendszer telepíti a Process Servert a konfigurációs kiszolgálóra. | [Részletek](vmware-azure-architecture.md?#architectural-components).
**Szerepkör (helyszíni** | – A replikáláshoz engedélyezett gépekről érkező replikációs adatok fogadása.<br/> – A replikációs adatokat a gyorsítótárazással, tömörítéssel és titkosítással optimalizálja, és elküldi az Azure Storage-nak.<br/> – A Site Recovery mobilitási szolgáltatás leküldéses telepítését végzi a replikálni kívánt helyszíni VMware virtuális gépeken és fizikai kiszolgálókon.<br/> -A helyszíni gépek automatikus felderítését végzi. | [Részletek](vmware-physical-azure-config-process-server-overview.md#process-server). 
**Szerepkör (feladat-visszavétel az Azure-ból)** | A helyszíni helyről történő feladatátvétel után az Azure-beli Process Servert egy Azure-beli virtuális gépként állíthatja be a helyszíni helyre történő feladat-visszavétel kezeléséhez.<br/><br/> Az Azure-beli Process Server ideiglenes. Az Azure-beli virtuális gép a feladat-visszavétel befejezése után törölhető. | [Részletek](vmware-azure-set-up-process-server-azure.md).
**Méretezés** | A helyszíni nagyobb telepítések esetén további, kibővíthető folyamat-kiszolgálókat is beállíthat. A további kiszolgálók kibővítik a kapacitást, így nagyobb számú replikáló gépet kezelnek, és nagyobb mennyiségű replikációs forgalmat bonyolítanak le.<br/><br/> A gépeket áthelyezheti két folyamat kiszolgálója között a replikálási forgalom terheléselosztásához. | [További információ](vmware-azure-set-up-process-server-scale.md)


## <a name="master-target-server"></a>Fő célkiszolgáló

A fő célkiszolgáló az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat.

- Alapértelmezés szerint telepítve van a konfigurációs kiszolgálón.
- Nagyméretű központi telepítések esetén további, különálló fő célkiszolgáló adható hozzá a feladat-visszavételhez.


## <a name="next-steps"></a>Következő lépések
- Tekintse át a VMware virtuális gépek és fizikai kiszolgálók vész-helyreállítási [architektúráját](vmware-azure-architecture.md) .
- Tekintse át a VMware virtuális gépek és fizikai kiszolgálók Azure-ba való vész-helyreállításának [követelményeit és előfeltételeit](vmware-physical-azure-support-matrix.md) . 
