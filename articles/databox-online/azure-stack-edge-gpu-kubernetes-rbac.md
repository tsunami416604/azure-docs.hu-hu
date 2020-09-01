---
title: A Kubernetes szerepköralapú Access Control megismerése Azure Stack Edge-eszközön | Microsoft Docs
description: Ismerteti, hogyan történik a Kubernetes szerepköralapú Access Control Azure Stack Edge-eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: 310fde15a850214aa1741c9cb587c0edcf570a37
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084094"
---
# <a name="kubernetes-role-based-access-control-on-your-azure-stack-edge-device"></a>Szerepköralapú Access Control Kubernetes az Azure Stack Edge-eszközön


A Azure Stack Edge-eszközön a számítási szerepkör konfigurálásakor létrejön egy Kubernetes-fürt. A Kubernetes szerepköralapú hozzáférés-vezérlés (RBAC) használatával korlátozhatja az eszközön található fürterőforrások elérését.

Ez a cikk áttekintést nyújt a Kubernetes által biztosított RBAC rendszerről, valamint arról, hogy a Kubernetes-RBAC hogyan valósulnak meg az Azure Stack Edge-eszközön. 

## <a name="rbac-for-kubernetes"></a>RBAC a Kubernetes

A Kubernetes RBAC lehetővé teszi felhasználók vagy felhasználói csoportok hozzárendelését, valamint az erőforrások létrehozását és módosítását, illetve a naplók megtekintését az alkalmazás számítási feladatainak futtatásához. Ezek az engedélyek egyetlen névtérre vagy a teljes fürtre is érvényesek. 

A Kubernetes-fürt beállításakor a rendszer egyetlen felhasználót hoz létre a fürtnek megfelelően, és neve a fürt rendszergazdai felhasználója.  Egy `kubeconfig` fájl társítva van a fürt rendszergazdai felhasználója számára. A `kubeconfig` fájl egy szövegfájl, amely tartalmazza a fürthöz való kapcsolódáshoz szükséges összes konfigurációs információt a felhasználó hitelesítéséhez. 

### <a name="namespaces-and-users"></a>Névterek és felhasználók

A valós világban fontos a fürtöt több névtérre osztani. 

- **Több felhasználó**: Ha több felhasználóval is rendelkezik, akkor több névtér is lehetővé teszi, hogy ezek a felhasználók az alkalmazásait és szolgáltatásait az adott névterekben, egymástól elkülönítve telepíthessék. 
- **Egyetlen felhasználó**: akkor is, ha egyetlen felhasználó van, több névtér is lehetővé teszi, hogy a felhasználó ugyanazon a Kubernetes-fürtön futtassa az alkalmazások több verzióját.

### <a name="roles-and-rolebindings"></a>Szerepkörök és RoleBindings

A Kubernetes a szerepkör és a szerepkör-kötés fogalmával rendelkezik, amely lehetővé teszi, hogy a felhasználók vagy erőforrások számára a névtér szintjén és a fürt szintjén biztosítson engedélyeket. 

- **Szerepkörök**: megadhatja a felhasználóknak az engedélyeket **szerepkörként** , majd **szerepkörök** használatával engedélyeket adhat meg a névtéren belül. 
- **RoleBindings**: Miután definiálta a szerepköröket, a **RoleBindings** használatával rendelhet hozzá szerepköröket egy adott névtérhez. 

Ez a megközelítés lehetővé teszi, hogy logikailag elkülönítse egyetlen Kubernetes-fürtöt, és a felhasználók csak a hozzájuk rendelt névtérben tudják elérni az alkalmazás erőforrásait. 


## <a name="rbac-on-azure-stack-edge"></a>RBAC Azure Stack Edge-ben

A RBAC jelenlegi implementációjában a Azure Stack Edge lehetővé teszi a következő műveletek végrehajtását egy korlátozott PowerShell-RunSpace:

- Hozzon létre névtereket.  
- Hozzon létre további felhasználókat.
- Rendszergazdai hozzáférést biztosít a létrehozott névterekhez. Ne feledje, hogy nem lesz hozzáférése a fürt rendszergazdai szerepköréhez vagy a teljes fürtre kiterjedő erőforrások nézetéhez.
- `kubeconfig`A Kubernetes-fürt eléréséhez szükséges adatokat tartalmazó fájl beolvasása.


Az Azure Stack Edge-eszköz több rendszernévtérrel rendelkezik, és olyan felhasználói névtereket hozhat létre, amelyek `kubeconfig` fájlokkal férnek hozzá ezekhez a névterekhez. A felhasználók teljes körűen vezérelhetik ezeket a névtereket, és létrehozhatnak vagy módosíthatnak felhasználókat, vagy hozzáférést biztosíthatnak a felhasználóknak. Csak a fürt rendszergazdája fér hozzá a rendszernévtérhez és a fürtre kiterjedő erőforrásokhoz. Az egy `aseuser` csak olvasási hozzáféréssel rendelkezik a rendszernévterekhez.

Itt látható egy diagram, amely a RBAC Azure Stack Edge-eszközön való megvalósítását ábrázolja.

![RBAC Azure Stack Edge-eszközön](./media/azure-stack-edge-gpu-kubernetes-rbac/rbac-view-1.png)

Ebben az ábrában Alice, Bob és Chuck csak a hozzárendelt felhasználói névterekhez fér hozzá, ami ebben az esetben a, a `ns1` `ns2` és a `ns3` . Ezeken a névtereken belül rendszergazdai hozzáférésük van. A fürt rendszergazdájának viszont rendszergazdai hozzáférése van a rendszer névteréhez és a fürtre kiterjedő erőforrásokhoz.

`kubectl`Parancsok használatával névtereket hozhat létre, felhasználókat rendelhet hozzá, felhasználókat rendelhet hozzá, vagy fájlokat tölthet le `kubeconfig` . Magas szintű munkafolyamat:

1. Hozzon létre egy névteret és egy felhasználót.  

    `New-HcsKubernetesNamespace -Namespace`  

2. Hozzon létre egy felhasználót.  

    `New-HcsKubernetesUser -UserName`  

3. Társítsa a névteret a létrehozott felhasználóval.  

    `Grant-HcsKubernetesNamespaceAccess -Namespace -UserName`  

4. Mentse a felhasználói konfigurációt a következőre: `C:\Users\<username>\.kube` .  

5. `kubectl`Alkalmazások telepítése és elindítása a alkalmazásban `kubectl` . 

Részletes útmutatásért lásd: [hozzáférés a Kubernetes-fürthöz a Azure stack Edge-n keresztül a kuebctl-on keresztül](azure-stack-edge-gpu-create-kubernetes-cluster.md).


Ha névtereket és felhasználókat használ a Azure Stack Edge-eszközökön, a következő kikötések érvényesek:

- Nem végezhet olyan műveleteket, mint például a felhasználók létrehozása, a névtér elérésének engedélyezése vagy visszavonása a felhasználóhoz a rendszernévtérek esetében. Példák a rendszernévtérre:,,, `kube-system` `metallb-system` `kubernetes-dashboard` `default` `kube-node-lease` , `kube-public` . A rendszernévtérek közé tartoznak a központi telepítési típusok számára fenntartott névterek (például a `iotedge` (IoT Edge névtér) és az `azure-arc` Azure arc-névtér is.
- Létrehozhat felhasználói névtereket, és ezeken a névterekben létrehozhat további felhasználókat, és engedélyezheti vagy visszavonhatja a névtér elérését a felhasználók számára.
- Nem hozhat létre olyan névtereket, amelyek neve megegyezik a rendszernévtér bármely rendszernévterével. A rendszernévtér neve fenntartva.  
- Nem hozhat létre olyan felhasználói névtereket, amelyek más felhasználói névterek által már használt névvel rendelkeznek. Ha például Ön létrehozta a t `test-ns` , nem hozhat létre újabb `test-ns` névteret.
- A már fenntartott névvel rendelkező felhasználókat nem lehet létrehozni. Például `aseuser` egy fenntartott fürt rendszergazdája, és nem használható.

Azure Stack peremhálózati névterekkel kapcsolatos további információkért lásd: [névterek típusai](azure-stack-edge-gpu-kubernetes-workload-management.md#namespaces-types).


<!--To deploy applications on an Azure Stack Edge device, use the following :
 
- First, you will use the PowerShell runspace to create a user, create a namespace, and grant user access to that namespace.
- Next, you will use the Azure Stack Edge resource in the Azure portal to create persistent volumes using either static or dynamic provisioning for the stateful applications that you will deploy.
- Finally, you will use the services to expose applications externally and within the Kubernetes cluster.-->

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan hozhat létre egy felhasználót, hozzon létre egy névteret, és engedélyezze a hozzáférést a névtérhez, tekintse meg a [Kubernetes-fürt elérése a kubectl keresztül](azure-stack-edge-gpu-create-kubernetes-cluster.md)című témakört.

