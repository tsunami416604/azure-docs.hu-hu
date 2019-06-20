---
title: A Windows Server csomópontkészletek Azure Kubernetes Service (AKS) vonatkozó korlátozások
description: Ismerje meg az ismert korlátozások futtatásakor csomópontkészleteit a Windows Server és az alkalmazás számítási feladatainak Azure Kubernetes Service (AKS)
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: twhitney
ms.openlocfilehash: 4b72b6e33ad59ffceebf58aed7b315a4833b02f9
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203675"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Aktuális korlátozások csomópontkészleteit a Windows Server és az alkalmazás számítási feladatainak Azure Kubernetes Service (AKS)

Az Azure Kubernetes Service (aks) Szolgáltatásban, létrehozhat egy csomópont-készlet, amely a Windows Server fut, a vendég operációs rendszer a csomópontokon. Ezek a csomópontok futtathatja a natív Windows tárolóalkalmazásokat, például a .NET-keretrendszer épül. Hogyan a Linux és Windows operációs rendszer tároló támogatást biztosít a nagy különbségek vannak, mivel néhány gyakori Kubernetes és a pod kapcsolatos funkciók nem érhetők jelenleg Windows csomópontkészletek.

Ez a cikk néhány korlátozás és az aks-ben a Windows Server-csomópontok operációs rendszer fogalmakat ismerteti. A Windows Server csomópontkészletei jelenleg előzetes verzióban érhető el.

> [!IMPORTANT]
> Az AKS előzetes verziójú funkciók önkiszolgáló, a rendszer. A biztosított gyűjthet visszajelzéseket és a hibák kapcsolódóan a Közösség részéről. Előzetes verzióban elérhető ezeket a funkciókat nem üzemi használat céljára. Nyilvános előzetes verzióban érhető el "ajánlott beavatkozást" támogatás keretében tartoznak. Az AKS technikai támogatási csapat segítségét munkaidőben csendes-óceáni időzóna (PST) csak alatt érhető el. További információkért tekintse meg a következő cikkek támogatja:
>
> * [Az AKS támogatási házirendek][aks-support-policies]
> * [Az Azure-támogatás – gyakori kérdések][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>Korlátozások a Windows Server a Kubernetesben

A Windows Server-tárolók egy Windows-alapú tárolót gazdagépen kell futtatni. Az aks-ben a Windows Server-tárolók futtatásához is [létrehozása a Windows Server rendszert futtató csomópontkészletek] [ windows-node-cli] , a vendég operációs rendszer. Ablak kiszolgáló csomópont készlet támogatás magában foglalja a Kubernetes-projektben a felsőbb rétegbeli Windows Server részét képező bizonyos korlátozások. Ezek a korlátozások nem konkrétan az aks-ben. A felsőbb szintű támogatás Windows Serverhez a Kubernetesben további információkért lásd: [Kubernetes korlátozásai a Windows Server-tárolók](https://docs.microsoft.com/azure/aks/windows-node-limitations).

A következő felsőbb rétegbeli korlátozások, a Windows Server-tárolók a Kubernetes AKS kapcsolódnak:

- A Windows Server-tárolók csak használhatja az alapul szolgáló operációs rendszer Windows Server csomópont megegyezik a Windows Server 2019.
    - Az alap operációs rendszer nem támogatottak a Windows Server 2016 használatával létrehozott tárolórendszerképek.
- Emelt szintű tároló nem használható.
- Linux-specifikus szolgáltatások, például felhasználó, SELinux, AppArmor vagy POSIX képességek nem érhetők el a Windows Server-tárolók.
    - Fájl rendszer korlátozások, amelyek Linux-specifikus UUI/GUID, például egy felhasználó engedélyeinek még nem érhetők el a Windows Server-tárolók.
- Az Azure lemezeket és az Azure Files a támogatott kötettípusok, címen NTFS-köteteken a Windows Server-tárolóban is elérhető.
    - NFS-alapú tárolás / kötetek nem támogatottak.

## <a name="aks-limitations-for-windows-server-node-pools"></a>A Windows Server csomópontkészletek AKS korlátozásai

Windows Server csomópont készlet támogatása az aks-ben a következő további korlátozások vonatkoznak:

- AKS-fürt Linux csomópontkészletek mindig tartalmazza az első node-címkészlettel. Az első Linux-alapú csomópont-készlet nem törölhető, kivéve, ha maga az AKS-fürt törlése.
- Az AKS jelenleg csak az alapszintű load balancer, amely csak lehetővé teszi, hogy egy háttérkészlet, az alapértelmezett Linux-csomópontkészlet támogatja. Ennek eredményeképpen a Windows podok kimenő forgalmát mindig lesz [Azure által felügyelt nyilvános IP-címhez a lefordított][azure-outbound-traffic]. Mivel az IP-címet nem konfigurálható, nincs lehetőség jelenleg Windows podok engedélyezési lista adatforgalma. 
- AKS-fürtök az Azure CNI (speciális) hálózatkezelési modellről kell használnia.
    - Hálózatkezelés Kubenet (alapszintű) nem támogatott. AKS-fürt által használt kubenet nem hozható létre. A hálózati modellek közötti különbségek további információkért lásd: [fogalmak az aks-ben az alkalmazások hálózati][azure-network-models].
    - A Azure CNI modellt igényel, további tervezésre és az IP-címkezelés szempontjai. Megtervezése és megvalósítása Azure CNI vonatkozó további információkért lásd: [konfigurálása Azure CNI hálózatkezelés az aks-ben][configure-azure-cni].
- Az aks-ben a Windows Server-csomópontnak kell lennie *frissített* a legújabb Windows Server 2019 kiadásra a legújabb javítást fenntartásához javításokat tartalmaz, és frissíti. Windows-frissítések nincsenek engedélyezve a Alapcsomópont képen az aks-ben. A Windows Update kiadási ciklus, és a saját érvényesítési folyamat rendszeres időközönként meg kell hajtsa végre a Windows Server-csomóponton vagy tárolókészleteit a frissítés az AKS-fürt. A Windows Server csomópontkészletek frissítésével kapcsolatos további információkért lásd: [frissítése az aks-ben csomópontkészletek][nodepool-upgrade].
    - Ezeket a Windows Server-csomópont frissítése ideiglenesen fel további IP-címek a virtuális hálózat alhálózatán, egy új csomópont telepített, el a régi csomópontot.
    - vCPU-kvóták ideiglenesen felhasználják az előfizetést, egy új csomópont van telepítve, akkor a régi csomópont eltávolítja.
    - Nem lehet automatikusan frissíti és az újraindítások használata kezelheti `kured` a Linux-csomópontokat az aks-ben.
- Az AKS-fürtöt egy legfeljebb nyolc csomópontkészletek rendelkezhet.
    - 400 csomópontok legfeljebb nyolc csomópont készletekben között rendelkezhet.
- A Windows Server-csomópont készlet nevének maximális hossza 6 karakter rendelkezik.
- Az aks-ben előzetes funkciókat, például a hálózati házirend- és a fürt méretező, a Windows Server-csomópontok nem támogatott.
- Bejövő forgalom tartományvezérlőket csak a Linux-csomópontok egy NodeSelector használatával lehet ütemezni.
- Az Azure fejlesztési tárolóhelyek jelenleg csak a Linux-alapú csomópontkészletek érhető el.
- Csoport felügyelt szolgáltatásfiókok (gMSA) támogatás, ha a Windows Server-csomópontok egy Active Directory-tartományhoz nem csatlakoztatott jelenleg nem áll rendelkezésre az aks-ben.
    - A nyílt forráskódú, felső [aks-motor] [ aks-engine] projekt jelenleg támogatja a csoportosan felügyelt szolgáltatásfiók Ha a funkció használatához van szüksége.

## <a name="os-concepts-that-are-different"></a>Az eltérő operációs rendszer fogalmak

Kubernetes hagyományosan Linux szempontok alapján. Számos példái között a felsőbb rétegbeli [Kubernetes.io] [ kubernetes] webhely a Linux-csomópontok használatra készültek. Amikor hoz létre Windows Server-tárolók, az operációs rendszer szintű alkalmaz, az alábbi szempontokat használó központi telepítések:

- **Identitás** – Linux használja a felhasználói azonosítóját (UID), és groupID (CSOPORTAZONOSÍTÓ), egész szám típusú ábrázolva. Felhasználó és csoport nevében a rendszer nem kanonikus – csak az alias azok */etc/csoportok* vagy */etc/passwd* vissza az UID + CSOPORTAZONOSÍTÓ.
    - A Windows Server egy nagyobb bináris biztonsági azonosítóval (SID), amely a Windows biztonsági hozzáférési fiókkezelő (SAM) adatbázisban tárolt használja. Ez az adatbázis nem megosztott, a gazdagép és a tárolók vagy tárolók között.
- **Engedéllyel** – Windows Server biztonsági azonosítókkal való létrehozásához, nem pedig egy bitmaszk-engedélyek és UID + CSOPORTAZONOSÍTÓ alapuló hozzáférés-vezérlési lista használja
- **Fájlok elérési útjainak** -egyezmény a Windows Server rendszer használandó \ helyett /.
    - A pod adatait tartalmazza, amely a kötet csatlakoztatása adja meg a megfelelő Windows Server-tárolók elérési útja. Például ahelyett, hogy egy csatlakoztatási pontja */mnt/kötet* Linux-tárolóban, adjon meg egy meghajtóbetűjellel és a hely például */ezer/kötet* csatlakoztassa a *masz* meghajtót.

## <a name="next-steps"></a>További lépések

Az aks-ben, Windows Server-tárolók használatába [az aks-ben a Windows Server rendszert futtató csomópont-készlet létrehozása][windows-node-cli].

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
