---
title: OpenShift üzembe helyezése az Azure Stackben
description: OpenShift üzembe helyezése az Azure Stackben.
author: haroldwongms
manager: joraio
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 51abfd1cbb438d0987554040867625f7fb71630b
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758237"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>OpenShift container platform vagy OKD üzembe helyezése az Azure Stackben

OpenShift az Azure Stackben telepíthető. Van néhány kulcsfontosságú különbség az Azure és az Azure Stack között, így a központi telepítés némileg eltér, és a képességek is kissé eltérnek.

Jelenleg az Azure Cloud-szolgáltató nem működik az Azure Stackben. Emiatt nem fogja tudni használni a lemezcsatolást az Azure Stack ben az állandó tároláshoz. Ehelyett más tárolási lehetőségeket is konfigurálhat, például nfs-t, iSCSI-t, GlusterFS-t stb. Alternatív megoldásként engedélyezheti a cns-t, és használhatja a GlusterFS-t az állandó tároláshoz. Ha a CNS engedélyezve van, három további csomópont lesz telepítve további tárhellyel a GlusterFS használatához.

Az OpenShift Container Platform vagy az OKD üzembe helyezéséhez számos módszer közül választhat az Azure Stackben:

- Manuálisan telepítheti a szükséges Azure-infrastruktúra-összetevőket, majd követheti az [OpenShift Container Platform dokumentációját](https://docs.openshift.com/container-platform) vagy az [OKD dokumentációját.](https://docs.okd.io)
- Egy meglévő [Erőforrás-kezelő sablont](https://github.com/Microsoft/openshift-container-platform/) is használhat, amely leegyszerűsíti az OpenShift container platformfürt telepítését.
- Egy meglévő [Erőforrás-kezelő sablont](https://github.com/Microsoft/openshift-origin) is használhat, amely leegyszerűsíti az OKD-fürt telepítését.

Ha a Resource Manager sablont használja, válassza ki a megfelelő ágat (azurestack-release-3.x). Az Azure-sablonok nem fognak működni, mivel az API-verziók különböznek az Azure és az Azure Stack között. Az RHEL-lemezkép hivatkozás jelenleg az azuredeploy.json fájl változóként kódolt, és módosítani kell, hogy megfeleljen a rendszerkép.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

Minden lehetőséghez Red Hat előfizetés szükséges. A központi telepítés során a Red Hat Enterprise Linux-példány regisztrálva van a Red Hat-előfizetéshez, és az OpenShift container platform jogosultságait tartalmazó készletazonosítóhoz van csatolva.
Győződjön meg arról, hogy rendelkezik érvényes Red Hat Subscription Manager (RHSM) felhasználónévvel, jelszóval és készletazonosítóval. Másik lehetőségként használhat aktiválókulcsot, orgona-azonosítót és készletazonosítót is.  Ezeket az adatokat a https://access.redhat.comrendszerbe bejelentkezve ellenőrizheti.

## <a name="azure-stack-prerequisites"></a>Az Azure Stack előfeltételei

Egy RHEL-lemezképet (OpenShift container platform) vagy CentOS-lemezképet (OKD) kell hozzáadni az Azure Stack-környezethez egy OpenShift-fürt üzembe helyezéséhez. Lépjen kapcsolatba az Azure Stack rendszergazdájával a lemezképek hozzáadásához. Az utasítások itt találhatók:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Telepítés az OpenShift tárolóplatform vagy az OKD Erőforrás-kezelő sablon használatával

Az Erőforrás-kezelő sablon használatával történő telepítéshez egy paraméterfájlt kell használnia a bemeneti paraméterek biztosításához. A központi telepítés további testreszabásához elágazás a GitHub-tármű, és módosítsa a megfelelő elemeket.

Néhány gyakori testreszabási lehetőség, de nem korlátozódik a következőkre:

- Bástya virtuális gép mérete (változó azuredeploy.json)
- Elnevezési konvenciók (változók az azuredeploy.jsonban)
- OpenShift fürtspecifikus, gazdagépfájlon keresztül módosítva (deployOpenShift.sh)
- RHEL-lemezkép-referencia (változó az azuredeploy.json ban)

Az Azure CLI használatával történő üzembe helyezés lépéseit kövesse a megfelelő szakaszt az [OpenShift container platform](./openshift-container-platform-3x.md) szakaszban vagy az [OKD](./openshift-okd.md) szakaszban.

## <a name="next-steps"></a>További lépések

- [Telepítés utáni feladatok](./openshift-container-platform-3x-post-deployment.md)
- [Az OpenShift azure-beli telepítésének hibái](./openshift-container-platform-3x-troubleshooting.md)