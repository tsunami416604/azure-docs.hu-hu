---
title: Red Hat számítási feladatok az Azure-on – áttekintés | Microsoft Docs
description: Ismerje meg az Azure-beli Red Hat-termékek ajánlatait
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: df787d4102752bdf61e30bc00d0d08307ac12319
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473163"
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

### <a name="bring-your-own-subscription-byos-images"></a>Saját előfizetéses (BYOS) lemezképek
Az Azure RHEL BYOS-lemezképeket is biztosít. Ezek a rendszerképek hasznosak lehetnek a már meglévő Red Hat-előfizetésekkel rendelkező ügyfelek számára, és ezeket az Azure-ban szeretné használni. Az Azure-ban való használat előtt át kell alakítania a meglévő előfizetéseket a felhőalapú hozzáférési előfizetésekre. Ezekhez a képekhez csak akkor férhet hozzá, ha a Red Hat megfelelő felhőalapú hozzáférési előfizetéssel rendelkezik. Ezeknek a lemezképeknek a használatával az ügyfél elkerülheti a kettős számlázást, amely a TB-lemezképek használatából merülhet fel. [Itt](https://aka.ms/rhel-byos)kérhet hozzáférést a BYOS-lemezképekhez.

> [!NOTE]
> Megjegyzés a kettős számlázás esetében: a kettős számlázás akkor merül fel, amikor egy felhasználó kétszer fizet a RHEL-előfizetésekhez. Ez általában akkor fordul elő, amikor az ügyfél az előfizetés-kezelővel csatol egy jogosultságot egy RHEL TB virtuális géphez. Például egy olyan ügyfél, amely az előfizetés-kezelővel egy RHEL TB-rendszerképben lévő SAP-csomagokra vonatkozó jogosultságot csatol, a rendszer nem fogja megduplázni, mivel a TB díja és az SAP-előfizetése között kétszer kell fizetnie a RHEL. Ez nem fog történni a rendszerképek BYOS.

## <a name="red-hat-update-infrastructure-rhui"></a>Red Hat frissítési infrastruktúra (RHUI)
Az Azure csak a TB RHEL virtuális gépek (VM) esetében biztosít Red Hat frissítési infrastruktúrát. A RHUI hatékonyan tükrözi a Red Hat CDNs, de csak az Azure TB RHEL virtuális gépek számára érhető el. A megfelelő csomagokat attól függően érheti el, hogy melyik RHEL-lemezképet telepítette. Az SAP-rendszerképekhez tartozó RHEL például az alap RHEL-csomagok mellett hozzáférhetnek az SAP-csomagokhoz is.

### <a name="rhui-update-behavior"></a>RHUI-frissítési viselkedés
A RHUI-hez csatlakozó RHEL-lemezképek alapértelmezés szerint a RHEL legújabb másodlagos verziójára frissülnek, amikor egy `yum update` fut. Ez azt jelenti, hogy a RHEL 7,4 virtuális gép RHEL 7,7-ra frissíthető, ha `yum update` művelet fut rajta. Ez a RHUI megtervezése. Ez a frissítési viselkedés azonban enyhíthető a normál RHEL-adattárakból a [kiterjesztett frissítési támogatási (EUs) adattárakba](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)való áttéréssel.

## <a name="next-steps"></a>Következő lépések
* További információ az [Azure-beli RHEL-lemezképekről](./redhat-images.md)
* További információ a [Red Hat frissítési infrastruktúráról](./redhat-rhui.md)
* További információ a [RHEL BYOS ajánlatáról](./redhat-byos.md)