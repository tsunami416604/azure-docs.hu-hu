---
title: A Windows Server Node-készletek korlátai az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg az ismert korlátozásokat a Windows Server Node-készletek és az alkalmazás-munkaterhelések Azure Kubernetes szolgáltatásban (ak) való futtatásakor
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 501aeb60eba1d94b4c5882a7c6cbfa8d0359e44d
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033900"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>A Windows Server Node-készletek és az alkalmazások számítási feladatainak jelenlegi korlátai az Azure Kubernetes szolgáltatásban (ak)

Az Azure Kubernetes szolgáltatásban (ak) olyan csomópont-készletet hozhat létre, amely a Windows Server rendszert futtatja a csomópontokon lévő vendég operációs rendszerként. Ezek a csomópontok natív Windows-tárolókat futtathatnak, például a .NET-keretrendszerre épülő alkalmazásokat. Mivel a Linux és a Windows operációs rendszerekben jelentős különbségek vannak a tárolók támogatásában, néhány gyakori Kubernetes és Pod-hez kapcsolódó szolgáltatás jelenleg nem érhető el a Windows-csomópontok készletei számára.

Ez a cikk a Windows Server-csomópontokra vonatkozó korlátozásokat és operációsrendszer-fogalmakat ismerteti az AK-ban. A Windows Serverhez készült Node-készletek jelenleg előzetes verzióban érhetők el.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói önkiszolgáló opt-in. Az előzetes verziók az "adott állapotban" és "ahogy elérhető" módon vannak kizárva, és ki vannak zárva a szolgáltatói szerződésekből és a korlátozott jótállásból. A következő részben az ügyfélszolgálat a lehető leghatékonyabban foglalkozik. Ezért ezeket a funkciókat nem éles használatra szánták. További részletekért tekintse meg a következő támogatási cikkeket:
>
> * [AK-támogatási szabályzatok][aks-support-policies]
> * [Azure-támogatás – gyakori kérdések][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>A Windows Serverre vonatkozó korlátozások a Kubernetes-ben

A Windows Server-tárolóknak Windows-alapú tároló gazdagépen kell futniuk. A Windows Server-tárolók az AK-ban való futtatásához [létrehozhat egy Windows Servert futtató csomópont][windows-node-cli] -készletet a vendég operációs rendszerként. A Windows Server-csomópontok készletének támogatása olyan korlátozásokat tartalmaz, amelyek a felsőbb rétegbeli Windows-kiszolgáló részét képezik a Kubernetes projektben. Ezek a korlátozások nem kifejezetten az AK-ra vonatkoznak. További információ a Windows Server rendszerhez készült, a Kubernetes-ben elérhető felső szintű támogatásról: [Windows Server-tárolók Kubernetes-korlátozásokban](https://docs.microsoft.com/azure/aks/windows-node-limitations).

A Kubernetes-ben a Windows Server-tárolók következő felsőbb rétegbeli korlátozásai vonatkoznak az AK-ra:

- A Windows Server-tárolók csak a Windows Server 2019-et használhatják, amely megfelel a mögöttes Windows Server-csomópont operációs rendszernek.
    - A Windows Server 2016 használatával létrehozott tároló-lemezképek, mivel az alapszintű operációs rendszer nem támogatott.
- A privilegizált tárolók nem használhatók.
- A Linux-specifikus funkciók, például a RunAsUser, a SELinux, a AppArmor vagy a POSIX képességek nem érhetők el a Windows Server-tárolókban.
    - A Linux-specifikus, például a UUI/GUID azonosítóval rendelkező fájlrendszerbeli korlátozások a felhasználónkénti engedélyek esetében nem érhetők el a Windows Server-tárolókban.
- Az Azure-lemezek és a Azure Files támogatott mennyiségi típusok, amelyek a Windows Server-tárolóban NTFS-kötetként érhetők el.
    - Az NFS-alapú tárolás/kötetek nem támogatottak.

## <a name="aks-limitations-for-windows-server-node-pools"></a>AK-korlátozások a Windows Server Node-készletekhez

A következő további korlátozások érvényesek a Windows Server Node Pool támogatására az AK-ban:

- Az AK-fürt mindig tartalmaz egy Linux-csomópontot, amely az első csomópont-készlet. Ez az első Linux-alapú csomópont-készlet nem törölhető, kivéve, ha maga az AK-fürt törölve lett.
- Az AK-fürtöknek az Azure CNI (Advanced) hálózatkezelési modellt kell használniuk.
    - A Kubenet (alapszintű) hálózatkezelés nem támogatott. Nem hozhat létre kubenet-t használó AK-fürtöt. További információ a hálózati modellekkel kapcsolatos különbségekről: az [AK-beli alkalmazások hálózati fogalmai][azure-network-models].
    - Az Azure CNI hálózati modellje további tervezést és szempontokat igényel az IP-címek kezeléséhez. Az Azure-CNI tervezésével és megvalósításával kapcsolatos további információkért lásd: [Az Azure CNI hálózatkezelésének konfigurálása az AK-ban][configure-azure-cni].
- Az AK-ban lévő Windows Server-csomópontokat *frissíteni* kell a legújabb Windows Server 2019 kiadásra a javítások és a frissítések legújabb frissítéseinek fenntartása érdekében. A Windows-frissítések nincsenek engedélyezve az Alapcsomópont-rendszerképben az AK-ban. A Windows Update kiadási ciklus és a saját ellenőrzési folyamata körüli rendszeres ütemterv esetén frissíteni kell a Windows Server Node-készlet (ek) et az AK-fürtben. A Windows Server-csomópontok készletének frissítésével kapcsolatos további információkért lásd: [csomópont-készlet frissítése az AK-ban][nodepool-upgrade].
    - Ezek a Windows Server-csomópontok a virtuális hálózat alhálózatában átmenetileg felhasználnak további IP-címeket, mivel a rendszer a régi csomópont eltávolítása előtt üzembe helyezi az új csomópontot.
    - az előfizetésben az vCPU-kvótákat is ideiglenesen felhasználjuk, mivel a rendszer új csomópontot telepít, majd eltávolítja a régi csomópontot.
    - A rendszer nem tudja automatikusan frissíteni és kezelni az újraindításokat az AK-ban található Linux-csomópontok használatával `kured` .
- Az AK-fürt legfeljebb nyolc csomópont-készletet tartalmazhat.
    - Ezen nyolc csomópont-készletben legfeljebb 400 csomópont lehet.
- A Windows Server-csomópontok készletének neve legfeljebb 6 karakterből állhat.
- A Windows Server-csomópontok esetében a rendszer nem támogatja az AK-ban található, például a hálózati házirend és a fürt automéretező funkcióinak előzetes verzióját.
- A bejövő vezérlők csak Linux-csomópontokon NodeSelector használatával ütemezhetők.
- Az Azure dev Spaces jelenleg csak a Linux-alapú csomópont-készletek esetében érhető el.
- Csoportosan felügyelt szolgáltatásfiókok (gMSA-EK) támogatása, ha a Windows Server-csomópontok nem csatlakoznak Active Directory tartományhoz jelenleg nem érhető el az AK-ban.
    - Ha ezt a funkciót szeretné használni, a nyílt forráskódú, a felsőbb rétegbeli [AK-motor][aks-engine] projekt jelenleg biztosít gMSA-támogatást.

## <a name="os-concepts-that-are-different"></a>Eltérő operációsrendszer-fogalmak

A Kubernetes történelmileg Linux-alapú. A felsőbb rétegbeli [Kubernetes.IO][kubernetes] webhely számos példája Linux-csomópontokon használható. Ha Windows Server-tárolókat használó központi telepítéseket hoz létre, a következő szempontokat kell figyelembe venni az operációs rendszer szintjén:

- Az **Identity** -Linux a userid (UID) és a GROUPID (GID) karakterláncot használja egész típusúként. A felhasználók és csoportok neve nem kanonikus – csak egy alias a */etc/groups* -ben, vagy */etc/passwd* vissza a UID + GID-be.
    - A Windows Server egy nagyobb bináris biztonsági azonosítót (SID) használ, amelyet a Windows Security Access Manager (SAM) adatbázisa tárol. Ez az adatbázis nincs megosztva a gazdagép és a tárolók között, vagy a tárolók között.
- Fájlengedélyek – a Windows Server SID-alapú hozzáférés-vezérlési listát használ a bitmaszk és az UID + GID helyett
- **Fájlelérési utak** – a Windows Server-konvenció a \ helyett a következőt használja:/.
    - A kötetek csatlakoztatására szolgáló Pod specifikációban a Windows Server-tárolók megfelelő elérési útját kell megadnia. Például egy Linux-tárolóban a */mnt/Volume* csatlakoztatási pontja helyett olyan meghajtóbetűjelet és helyet kell megadnia, mint amilyen például a */K/Volume* a *K:* meghajtó csatlakoztatására.

## <a name="next-steps"></a>További lépések

A Windows Server-tárolók az AK-ban való megkezdéséhez [hozzon létre egy olyan csomópont-készletet, amely a Windows Server rendszerű][windows-node-cli]

<!-- LINKS - external -->
[upstream-limitations]: https://kubernetes.io/docs/setup/windows/#limitations
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
