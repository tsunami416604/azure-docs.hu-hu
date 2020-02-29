---
title: Red Hat számítási feladatok az Azure-on – áttekintés | Microsoft Docs
description: Ismerje meg az Azure-beli Red Hat-termékek ajánlatait
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 424ef37885d685829a11d1864a72b043a562231c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920552"
---
# <a name="red-hat-workloads-on-azure"></a>Red Hat számítási feladatok az Azure-ban
A Red Hat számítási feladatait az Azure különféle kínálatai támogatják. A Red Hat Enterprise Linux-(RHEL-) lemezképek a RHEL-munkaterhelések középpontjában állnak, mint a Red Hat Update Infrastructure (RHUI).

## <a name="red-hat-enterprise-linux-rhel-images"></a>Red Hat Enterprise Linux (RHEL) rendszerképek
Az Azure széles körben kínál RHEL-lemezképeket az Azure-ban. Ezeket a lemezképeket két különböző licencelési modell teszi elérhetővé: utólagos elszámolású (TB) és saját előfizetés (BYOS). Az Azure-ban új RHEL-lemezképek jelennek meg, amikor az új RHEL-verziók kiadását és frissítését szükség szerint a teljes életciklusuk során frissíti.

### <a name="pay-as-you-go-payg-images"></a>Utólagos elszámolású (TB) rendszerképek
Az Azure számos RHEL TB-lemezképet kínál. Ezek a lemezképek megfelelően jogosultak a RHEL, és a frissítések forrásaként vannak csatolva (Red Hat frissítési infrastruktúra). Ezek a lemezképek prémium díjat számítanak fel a RHEL jogosultságok és a frissítések után. A RHEL TB-rendszerkép változatai a következők:
* Szabványos RHEL
* RHEL az SAP-hoz
* RHEL az SAP számára magas rendelkezésre állású és frissítési szolgáltatásokkal.

Érdemes lehet a TB-lemezképeket használni, ha nem szeretné, hogy a megfelelő számú előfizetésre külön fizessen.

### <a name="red-hat-gold-images-rhel-byos"></a>Red Hat Gold images (`rhel-byos`)
Az Azure Red Hat Gold-képeket is kínál. Ezek a rendszerképek hasznosak lehetnek a már meglévő Red Hat-előfizetésekkel rendelkező ügyfelek számára, és ezeket az Azure-ban szeretné használni. Az Azure-ban való használat előtt engedélyeznie kell a Red Hat Cloud Access meglévő Red Hat-előfizetéseit. A rendszerképekhez való hozzáférés automatikusan megtörténik, ha a Red Hat-előfizetések engedélyezve vannak a Felhőbeli hozzáféréshez, és megfelelnek a jogosultsági követelményeknek. Ezeknek a lemezképeknek a használatával az ügyfél elkerülheti a kettős számlázást, amely a TB-lemezképek használatából merülhet fel.
* [Ismerje meg, hogyan engedélyezheti a Red Hat-előfizetéseket a Felhőbeli hozzáféréshez az Azure-ban](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs)
* [Megtudhatja, hogyan keresheti meg a Red Hat Gold images-képeket a Azure Portal, a CLI vagy a PowerShell-parancsmagban](./byos.md)

> [!NOTE]
> Megjegyzés a kettős számlázás esetében: a kettős számlázás akkor merül fel, amikor egy felhasználó kétszer fizet a RHEL-előfizetésekhez. Ez általában akkor fordul elő, amikor az ügyfél az előfizetés-kezelővel csatol egy jogosultságot egy RHEL TB virtuális géphez. Például egy olyan ügyfél, amely az előfizetés-kezelővel egy RHEL TB-rendszerképben lévő SAP-csomagokra vonatkozó jogosultságot csatol, a rendszer nem fogja megduplázni, mivel a TB díja és az SAP-előfizetése között kétszer kell fizetnie a RHEL. Ez nem fog történni a rendszerképek BYOS.

### <a name="generation-2-images"></a>2\. generációs rendszerképek
A 2. generációs virtuális gépek újabb funkciókat biztosítanak az 1. generációs virtuális gépekhez képest. A részleteket a [2. generációs dokumentáció](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)ismerteti. A RHEL-lemezkép szempontjából a legfontosabb különbség az, hogy a 2. generációs virtuális gépek a BIOS belső vezérlőprogram-felülete helyett UEFI-t használnak, és a fő rendszerindító rekord (MBR) helyett egy GUID partíciós táblát (GPT) használnak a rendszerindítási időben. Ez többek között a 2TB-nál nagyobb operációsrendszer-lemezeket is lehetővé tesz. Emellett a [Mv2 sorozatú virtuális gépek](../../mv2-series.md) csak a 2. generációs lemezképeken futnak.

A RHEL 2. generációs képei elérhetők a piactéren. Keresse meg a "Gen2" elemet a lemezkép SKU-ban, ha az Azure CLI-vel az összes lemezképet felsorolja, majd a virtuális gép üzembe helyezési folyamatának "speciális" lapján lépjen a 2. generációs virtuális gépek üzembe helyezéséhez.

## <a name="red-hat-update-infrastructure-rhui"></a>Red Hat frissítési infrastruktúra (RHUI)
Az Azure csak a TB RHEL virtuális gépek (VM) esetében biztosít Red Hat frissítési infrastruktúrát. A RHUI hatékonyan tükrözi a Red Hat CDNs, de csak az Azure TB RHEL virtuális gépek számára érhető el. A megfelelő csomagokat attól függően érheti el, hogy melyik RHEL-lemezképet telepítette. Az SAP-rendszerképekhez tartozó RHEL például az alap RHEL-csomagok mellett hozzáférhetnek az SAP-csomagokhoz is.

### <a name="rhui-update-behavior"></a>RHUI-frissítési viselkedés
A RHUI-hez csatlakozó RHEL-lemezképek alapértelmezés szerint a RHEL legújabb másodlagos verziójára frissülnek, amikor egy `yum update` fut. Ez azt jelenti, hogy a RHEL 7,4 virtuális gép RHEL 7,7-ra frissíthető, ha `yum update` művelet fut rajta. Ez a RHUI megtervezése. Ez a frissítési viselkedés azonban enyhíthető a normál RHEL-adattárakból a [kiterjesztett frissítési támogatási (EUs) adattárakba](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)való áttéréssel.

## <a name="next-steps"></a>Következő lépések
* További információ az [Azure-beli RHEL-lemezképekről](./redhat-images.md)
* További információ a [Red Hat frissítési infrastruktúráról](./redhat-rhui.md)
* További információ a [Red Hat Gold (`rhel-byos`) ajánlatról](./byos.md)
