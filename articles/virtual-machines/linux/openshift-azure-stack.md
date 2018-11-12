---
title: Telepíthető az OpenShift az Azure Stackben |} A Microsoft Docs
description: Telepítse az OpenShift az Azure Stackben.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: 2b111967548dd90a8795af617a80a23d763861fe
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037670"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>OpenShift Tárolóplatform, vagy az Azure Stackben OKD üzembe helyezése

OpenShift az Azure Stackben is telepíthető. Nincsenek Azure-ban és az Azure Stack közötti fontosabb különbségeket, üzembe helyezési némileg eltérőek lehetnek, és képességeket is némileg eltér.

Jelenleg az Azure Felhőszolgáltató nem működik az Azure Stackben. Ebből kifolyólag akkor tudja használni a lemez csatolása a hosszú távú adattárolásra az Azure Stackben. Ehelyett beállíthatja az egyéb tároló-beállítások, például az NFS, iSCSI, GlusterFS, stb. Alternatív megoldásként CNS engedélyezheti és hosszú távú adattárolásra GlusterFS használja. Ha CNS engedélyezve van, három további csomópontokat a további tárhely az GlusterFS használati telepíthető.

OpenShift Tárolóplatform vagy az Azure Stackben OKD üzembe többféle módszer egyikét használhatja:

- Manuálisan telepítheti a szükséges Azure-infrastruktúra-összetevőket, és kövesse a [OpenShift Tárolóplatform dokumentáció](https://docs.openshift.com/container-platform) vagy [OKD dokumentáció](https://docs.okd.io).
- Használhatja egy meglévő [Resource Manager-sablon](https://github.com/Microsoft/openshift-container-platform/) , amely leegyszerűsíti az OpenShift Tárolóplatform fürt központi telepítése.
- Használhatja egy meglévő [Resource Manager-sablon](https://github.com/Microsoft/openshift-origin) , amely leegyszerűsíti a OKD fürt központi telepítése.

Ha a Resource Manager-sablont használ, válassza ki a megfelelő fiókirodai (azurestack-release-3.x). A sablonok az Azure-hoz, az API-verziók különböznek az Azure és az Azure Stack nem fog működni. Az RHEL-lemezkép referencia jelenleg kódolt változóként az azuredeploy.json fájlban, és a lemezkép megfelelően módosítani kell.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

Minden beállítás a Red Hat-előfizetésüket szükség. Az üzembe helyezés során a Red Hat Enterprise Linux-példány van regisztrálva a Red Hat-előfizetésüket, a készlet azonosítója, amely tartalmazza a jogosultságokat az OpenShift Tárolóplatform kapcsolódik.
Ellenőrizze, hogy egy érvényes Red Hat előfizetés Manager (RHSM) felhasználónév, jelszó és készlet azonosítója. Másik lehetőségként használhatja egy aktiválási kulcsot, a szervezeti azonosító és a készlet azonosítója.  Bejelentkezés a ellenőrizheti ezt az információt https://access.redhat.com.

## <a name="azure-stack-prerequisites"></a>Az Azure Stack előfeltételei

Egy RHEL-lemezkép (OpenShift Tárolóplatform) vagy a CentOS képet (OKD) az OpenShift-fürt üzembe helyezése az Azure Stack környezettel való hozzá kell adnia. Lépjen kapcsolatba az Azure Stack rendszergazdai ezeket a lemezképeket. Útmutatás itt található:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Az OpenShift Tárolóplatform vagy OKD Resource Manager-sablon segítségével üzembe helyezése

Használatával a Resource Manager-sablon üzembe helyezéséhez használhatja egy paraméterfájl adja meg a bemeneti paraméterek. További testreszabását az üzembe helyezés, a GitHub-tárház elágaztatását, és módosítsa a megfelelő elemeket.

Néhány gyakori testreszabási lehetőségek közé tartozik, de nem korlátozódik:

- Megerősített Virtuálisgép-méret (az azuredeploy.json változó)
- Elnevezési konvenciók (az azuredeploy.json változók)
- OpenShift fürt tulajdonságairól, módosítani állomásleíró fájlhoz (deployOpenShift.sh)
- RHEL-lemezkép-hivatkozás (az azuredeploy.json változó)

Üzembe helyezés az Azure CLI használatával lépéseit, hajtsa végre a megfelelő szakaszában a [OpenShift Tárolóplatform](./openshift-container-platform.md) szakasz vagy a [OKD](./openshift-okd.md) szakaszban.

## <a name="next-steps"></a>További lépések

- [Üzembe helyezés utáni feladatok](./openshift-post-deployment.md)
- [Az Azure-ban az OpenShift telepítés hibaelhárítása](./openshift-troubleshooting.md)