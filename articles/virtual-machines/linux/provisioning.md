---
title: A Linux-kiépítés áttekintése
description: A Linux rendszerű virtuális gépek rendszerképeinek beszerzése, illetve az Azure-ban használandó új rendszerképek létrehozása.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 6530d05b8e1aa565e64256054e81b785572edfb0
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85308141"
---
# <a name="azure-linux-vm-provisioning"></a>Azure Linux rendszerű virtuális gépek üzembe helyezése
Ha általánosított rendszerképből (megosztott képkatalógusból vagy felügyelt rendszerképből) hoz létre virtuális gépet, a vezérlő síkja lehetővé teszi a virtuális gép létrehozását, valamint paramétereket és beállításokat adhat át a virtuális géphez. Ezt a virtuális gépek *üzembe*helyezésének nevezzük. A kiépítés során a platform lehetővé teszi, hogy a virtuális gép a rendszerindítás során elérhetővé hozzon paramétereket (állomásnév, Felhasználónév, jelszó, SSH-kulcsok, customData). 

A rendszerképbe bekészített kiépítési ügynök a platformmal együtt csatlakozik a több független kiépítési interfészhez, és a tulajdonságokat és a jeleket a befejezett platformra állítja be. 

A kiépítési ügynökök lehetnek az [Azure Linux-ügynök](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)vagy a [Cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init). Ezek az általánosított rendszerképek létrehozásának [előfeltételei](create-upload-generic.md) .

A kiépítési ügynökök támogatást biztosítanak az összes támogatott [Azure Linux-disztribúcióhoz](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros), és számos esetben a támogatott disztribúciós rendszerképeket a Cloud-init és a Linux-ügynökkel is megtalálhatja. Ez lehetőséget biztosít a Cloud-init használatára a kiépítés kezeléséhez, majd a Linux-ügynök támogatást nyújt az [Azure-bővítmények](https://docs.microsoft.com/azure/virtual-machines/extensions/features-windows)kezeléséhez. A bővítmények támogatása azt jelenti, hogy a virtuális gép ezután jogosult a további Azure-szolgáltatások támogatására, mint például a virtuális gép jelszavának alaphelyzetbe állítása, az Azure monitoring, a Azure Backup, az Azure Disk Encryption stb.

A kiépítés befejezése után a Cloud-init minden rendszerindításkor le fog futni. A Cloud-init figyeli a virtuális gép módosításait, például a hálózati változásokat, az ideiglenes lemez csatlakoztatását és formázását, és elindítja a Linux-ügynököt. A Linux-ügynök folyamatosan fut a kiszolgálón, és egy "cél állapotot" (új konfigurációt) keres az Azure platformról, így a bővítmények telepítésekor az ügynök képes lesz feldolgozni őket.

Bár jelenleg két kiépítési ügynök található, a Cloud-init legyen a választott kiépítési ügynök, és a Linux-ügynököt telepíteni kell a bővítmények támogatásához. Így kihasználhatja a platform optimalizálásait, és lehetővé teszi a Linux-ügynök letiltását vagy eltávolítását, az ügynök nélküli lemezképek létrehozásával és eltávolításával kapcsolatos további részletekért tekintse át ezt a [dokumentációt](disable-provisioning.md).

Ha olyan linuxos kernelt használ, amely nem támogatja az ügynök futtatását, de a virtuális gépek némelyikét szeretné beállítani, például állomásnév, customData, Felhasználónév, jelszó, ssh-kulcsok, akkor ebben a dokumentumban azt tárgyaljuk, hogyan hozhat [létre általánosított rendszerképeket ügynök nélkül](no-agent.md), és kielégítheti a platformra vonatkozó követelményeket.


## <a name="provisioning-agent-responsibilities"></a>Kiépítési ügynök feladatai

**Rendszerkép kiépítés**
  
- Felhasználói fiók létrehozása
- SSH-hitelesítési típusok konfigurálása
- Nyilvános SSH-kulcsok és kulcspár üzembe helyezése
- Az állomásnév beállítása
- Az állomásnév közzététele a platform DNS-ben
- SSH-gazdagép kulcsának ujjlenyomatának jelentése a platformnak
- Erőforrás-lemezek kezelése
- Az erőforrás lemezének formázása és csatlakoztatása
- Fogyasztás és feldolgozás`customData`
 
**Hálózat**
  
- Kezeli az útvonalakat a platform DHCP-kiszolgálókkal való kompatibilitás javítása érdekében
- Biztosítja a hálózati adapter nevének stabilitását

**Kernel**
  
- Virtuális NUMA konfigurálása (kernel <letiltása `2.6.37` )
- A Hyper-V entrópia használata a következőhöz:`/dev/random`
- SCSI-időtúllépések konfigurálása a gyökérszintű eszközhöz (amely távoli lehet)

**Diagnosztika**
  
- Konzol átirányítása a soros portra

## <a name="communication"></a>Kommunikáció
A platformról az ügynökre irányuló információáramlás két csatornán keresztül történik:

- Egy rendszerindítási idő csatolt DVD-je a IaaS üzemelő példányokhoz. A DVD tartalmaz egy OVF-kompatibilis konfigurációs fájlt, amely tartalmazza az összes kiépítési információt, kivéve a tényleges SSH-kulcs párokat.
- Egy TCP-végpont, amely az üzembe helyezés és a topológia konfigurációjának megszerzéséhez használt REST API teszi közzé.


## <a name="azure-provisioning-agent-requirements"></a>Az Azure kiépítési ügynökre vonatkozó követelmények
A Linux-ügynök és a Cloud-init a megfelelő működés érdekében néhány rendszercsomagtól függ:
- Python 2.6 +
- OpenSSL 1.0 +
- OpenSSH 5.3 +
- Filesystem segédprogramok: `sfdisk` ,, `fdisk` `mkfs` ,`parted`
- Jelszó-eszközök: chpasswd, sudo
- Text Processing Tools: sed, grep
- Hálózati eszközök: IP-útvonal
- Kernel-támogatás az UDF-fájlrendszerek csatlakoztatásához.

## <a name="next-steps"></a>További lépések

Ha szükséges, [letilthatja a kiépítés és a Linux-ügynök eltávolítását](disable-provisioning.md).
