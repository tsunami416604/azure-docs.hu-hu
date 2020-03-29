---
title: Az Azure Site Recovery konfigurációja/folyamat/fő célkiszolgálói
description: Ez a cikk áttekintést nyújt a konfigurációs, folyamat- és főcélkiszolgálók használatáról a helyszíni VMware virtuális gépek vész-helyreállítási szolgáltatásának az Azure-ba való beállításakor az Azure Site Recovery szolgáltatással
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: cd5ded18d1a8f1f5fd96212d37725bb5db13002f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062090"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>A Webhely-helyreállítási összetevők (konfiguráció, folyamat, fő cél) –

Ez a cikk a [Site Recovery](site-recovery-overview.md) szolgáltatás által a VMware virtuális gépek és a fizikai kiszolgálók Azure-ba replikálására használt konfigurációs, folyamat- és főcélkiszolgálókat ismerteti.

## <a name="configuration-server"></a>Konfigurációs kiszolgáló

A helyszíni VMware virtuális gépek és fizikai kiszolgálók vész-helyreállítási helyreállítása érdekében telepítsen egy helyszíni site recovery konfigurációs kiszolgálót.

**Beállítás** | **Részletek** | **Hivatkozások**
--- | --- | ---
**Összetevők**  | A konfigurációs kiszolgáló gépe futtatja az összes helyszíni site recovery összetevőt, beleértve a konfigurációs kiszolgálót, a folyamatkiszolgálót és a fő célkiszolgálót.<br/><br/> A konfigurációs kiszolgáló beállításakor az összes összetevő automatikusan települ. | [Olvassa el](vmware-azure-common-questions.md#configuration-server) a konfigurációs kiszolgáló gyakori kérdéseket.
**Szerepkör** | A konfigurációs kiszolgáló koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és felügyeli az adatreplikációt. | További információ a [VMware](vmware-azure-architecture.md) architektúrájáról és a [fizikai kiszolgálói](physical-azure-architecture.md) vész-helyreállításról az Azure-ba.
**VMware követelmények** | A helyszíni VMware virtuális gépek vész-helyreállítási, telepítenie kell és futtatnia kell a konfigurációs kiszolgálót, mint egy helyszíni, magas rendelkezésre állású VMware virtuális gép. | További információ az [előfeltételekről.](vmware-azure-deploy-configuration-server.md#prerequisites)
**A VMware telepítése** | Javasoljuk, hogy a konfigurációs kiszolgálót letöltött OVA sablonnal telepítse. Ez a módszer egyszerű módot biztosít egy olyan konfigurációs kiszolgáló beállítására, amely megfelel az összes követelménynek és előfeltételnek.<br/><br/> Ha valamilyen okból nem tudja telepíteni a VMware virtuális gép egy OVA sablon használatával, beállíthatja a konfigurációs kiszolgáló gépek manuálisan, az alábbiak szerint a fizikai gép vész-helyreállítási. | [Üzembe helyezés](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) OVA sablonnal.
**Fizikai kiszolgáló követelmények** | A helyszíni fizikai kiszolgálókon a vészhelyreállításhoz manuálisan kell telepítenie a konfigurációs kiszolgálót. | További információ az [előfeltételekről.](physical-azure-set-up-source.md#prerequisites)
**Fizikai kiszolgáló telepítése** | Ha nem telepíthető VMware virtuális gépként, telepítheti azt egy fizikai kiszolgálóra. | [Telepítse](physical-azure-set-up-source.md#set-up-the-source-environment) a konfigurációs kiszolgálót manuálisan.

## <a name="process-server"></a>Folyamatkiszolgáló

A folyamatkiszolgáló a feladatátvétel és a feladat-visszavétel során kezeli a replikációs adatokat, és telepíti a hordozható sági szolgáltatást a helyszíni VMware virtuális gépekhez és fizikai kiszolgálókhoz.

**Beállítás** | **Részletek** | **Hivatkozások**
--- | --- | ---
**Környezet**  | Alapértelmezés szerint a konfigurációs kiszolgáló telepítésekor a folyamatkiszolgáló telepítve van. <br/><br/> A helyszíni folyamatkiszolgáló szükséges a vész-helyreállítási és a helyszíni VMware virtuális gépek és fizikai kiszolgálók replikációjához. | [További információ](vmware-azure-architecture.md#architectural-components).
**Szerepkör (helyszíni)** | Replikációs adatok fogadása a replikációra engedélyezett gépekről. <br/><br/> Gyorsítótárazásával, tömörítéssel és titkosítással optimalizálja a replikációs adatokat, és elküldi azokat az Azure Storage-nak. <br/><br/> Leküldéses telepítést hajt végre a hely-helyreállítási mobilitási szolgáltatásra a helyszíni VMware virtuális gépeken és a replikálni kívánt fizikai kiszolgálókon. <br/><br/> A helyszíni gépek automatikus felderítését hajtja végre. | [További információ](vmware-azure-enable-replication.md).
**Szerepkör (feladat-visszavétel az Azure-ból)** | Miután feladatátvételt a helyszíni helyről, bekell állítania egy folyamatkiszolgálót az Azure-ban, mint egy Azure virtuális gép, a feladat-visszavétel a helyszíni helyre.<br/><br/> Az Azure folyamatkiszolgálója ideiglenes. Az Azure virtuális gép törölhető a feladat-visszavétel után. | [További információ](vmware-azure-set-up-process-server-azure.md).
**Méretezés** | Nagyobb telepítések esetén a helyszíni további, kibővített folyamatkiszolgálókat is beállíthat. További kiszolgálók nagyobb számú replikálógép és nagyobb mennyiségű replikációs forgalom kezelésével szélesítik a kapacitást.<br/><br/> A gépeket két folyamatkiszolgáló között is áthelyezheti a replikációs forgalom terhelése érdekében. | [További információ](vmware-azure-set-up-process-server-scale.md).

## <a name="master-target-server"></a>Fő célkiszolgáló

A fő célkiszolgáló az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat.

- Alapértelmezés szerint a fő célkiszolgáló telepítve van a konfigurációs kiszolgálón.
- Nagy telepítések esetén hozzáadhat egy további, különálló fő célkiszolgálót a feladat-visszavételhez.

## <a name="next-steps"></a>További lépések

- Tekintse át a VMware virtuális gépek és a fizikai kiszolgálók vész-helyreállítási [architektúráját.](vmware-azure-architecture.md)
- Tekintse át a vmware virtuális gépek és fizikai kiszolgálók azure-ba való vész-helyreállítási követelményeit és [előfeltételeit.](vmware-physical-azure-support-matrix.md)
