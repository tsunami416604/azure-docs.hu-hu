---
title: Azure virtuális gépek méretei - GPU | Microsoft dokumentumok
description: Az Azure-ban a virtuális gépekhez elérhető különböző GPU-optimalizált méretek listája. A vCPU-k, adatlemezek és hálózati adapterek számával, valamint a tárolóátviteli és hálózati sávszélességgel kapcsolatos információkat sorolja fel a sorozat méreteihez.
services: virtual-machines
documentationcenter: ''
author: vikancha
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 7e1e0d488844a94bd0be2b91398678e620295729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913582"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU-ra optimalizált virtuális gépméretek

A GPU-ra optimalizált virtuálisgépek méretei olyan speciális virtuális gépek, amelyek egy vagy több NVIDIA GPU-val érhetők el. Ezeket a méreteket a számításigényes, nagy grafikai igényű és vizualizációs számítási feladatokhoz tervezték. Ez a cikk a GPU-k, a vCPU-k, az adatlemezek és a hálózati adapterek számáról és típusáról tartalmaz tájékoztatást. A tárolási átviteli és a hálózati sávszélesség is szerepel a csoportosítás minden egyes méretéhez.

- [NC-sorozatú](nc-series.md), [NCv2 sorozatú](ncv2-series.md), [NCv3 sorozatú](ncv3-series.md) méretek a számításigényes és hálózatigényes alkalmazásokhoz és algoritmusokhoz vannak optimalizálva. Néhány példa a CUDA és openCL-alapú alkalmazások és szimulációk, AI és deep learning. Az NCv3 sorozat az NVIDIA Tesla V100 GPU-jával rendelkező nagy teljesítményű számítási feladatokra összpontosít. Az NC sorozat az Intel Xeon E5-2690 v3 2.60GHz v3 (Haswell) processzort, az NCv2 és NCv3 sorozatú virtuális gépek pedig az Intel Xeon E5-2690 v4 (Broadwell) processzort használják.

- [Az ND sorozat](nd-series.md)és az [NDv2 sorozat](ndv2-series.md) méretei a mélytanulásra vonatkozó képzési és következtetési forgatókönyvekre összpontosítanak. Az NVIDIA Tesla P40 GPU-t és az Intel Xeon E5-2690 v4 (Broadwell) processzort használják. Az NDv2 sorozat az Intel Xeon Platinum 8168 (Skylake) processzort használja.

- [Az NV-sorozatú](nv-series.md) és [NVv3-as sorozatú](nvv3-series.md) méreteket optimalizálták és távoli vizualizációra, streamelésre, játékra, kódolásra és VDI-forgatókönyvekre tervezték olyan keretrendszerek használatával, mint az OpenGL és a DirectX. Ezeket a virtuális gépeket az NVIDIA Tesla M60 GPU támogatja.

- [NVv4 sorozat](nvv4-series.md) VM-méretek optimalizálva és vdi-re és távoli megjelenítésre optimalizálva és tervezve. Particionált GPU-kkal az NVv4 a megfelelő méretet kínálja a kisebb GPU-erőforrásokat igénylő munkaterhelések számára. Ezeket a virtuális gépeket az AMD Radeon Instinct MI25 GPU támogatja. Az NVv4 virtuális gépek jelenleg csak a Windows vendég operációs rendszerét támogatják.

## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

Az Azure N sorozatú virtuális gépek GPU-képességeinek kihasználásához telepíteni kell az NVIDIA GPU-illesztőprogramokat.

Az [NVIDIA GPU illesztőprogram-bővítmény](/azure/virtual-machines/extensions/hpccompute-gpu-windows) megfelelő NVIDIA CUDA vagy GRID illesztőprogramokat telepít egy N sorozatú Virtuális gépre. Telepítse vagy kezelje a bővítményt az Azure Portalon vagy az olyan eszközökkel, például az Azure PowerShell vagy az Azure Resource Manager-sablonok használatával, mint például az Azure PowerShell vagy az Azure Resource Manager-sablonok. A támogatott operációs rendszereket és a telepítési lépéseket az [NVIDIA GPU-illesztőprogram-bővítmény dokumentációjában](/azure/virtual-machines/extensions/hpccompute-gpu-windows) találja. A virtuálisgép-bővítményekről az [Azure virtuálisgép-bővítmények és -szolgáltatások](/azure/virtual-machines/extensions/overview)című témakörben talál általános tudnivalókat.

Ha úgy dönt, hogy manuálisan telepíti az NVIDIA GPU-illesztőprogramokat, olvassa el az [N sorozatú GPU-illesztőprogram beállítása Windows](/azure/virtual-machines/windows/n-series-driver-setup) vagy N [sorozatú GPU-illesztőprogram linuxos beállítását](/azure/virtual-machines/linux/n-series-driver-setup) a támogatott operációs rendszerek, illesztőprogramok, telepítés és ellenőrzési lépések hez.

## <a name="deployment-considerations"></a>Telepítési szempontok

- Az N sorozatú virtuális gépek elérhetőségéről a [Termékek régiónként elérhető című](https://azure.microsoft.com/regions/services/)témakörben tájékot keres.

- Az N sorozatú virtuális gépek csak az Erőforrás-kezelő telepítési modelljében telepíthetők.

- Az N sorozatú virtuális gépek különböznek a lemezekhez támogatott Azure Storage típusától. Az NC és nv virtuális gépek csak a standard lemeztároló (HDD) által támogatott virtuálisgép-lemezeket támogatják. Az NCv2, NCv3, ND, NDv2 és NVv2 virtuális gépek csak a prémium szintű lemeztároló (SSD) által támogatott virtuálisgép-lemezeket támogatják.

- Ha néhány N sorozatú virtuális gépnél többet szeretne telepíteni, fontolja meg a felosztó-kiosztó előfizetést vagy más vásárlási lehetőségeket. Amennyiben [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) használ, csak korlátozott számú számítási magot használhat az Azure-ban.

- Előfordulhat, hogy növelnie kell a magok kvótáját (régiónként) az Azure-előfizetésben, és növelnie kell a külön kvótát az NC, NCv2, NCv3, ND, NDv2, NV vagy NVv2 magok esetében. A kvótanövelésének igényléséhez [nyisson meg egy online ügyfélszolgálati kérelmet](../azure-portal/supportability/how-to-create-azure-support-request.md) díjmentesen. Az alapértelmezett korlátozások az előfizetési kategóriától függően változhatnak.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

További információ arról, hogy [az Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek a számítási teljesítmény összehasonlításában az Azure-sKU-k között.
