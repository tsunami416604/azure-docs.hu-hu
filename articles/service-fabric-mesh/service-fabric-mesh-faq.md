---
title: Gyakori kérdések az Azure Service Fabric Mesh-hez
description: Ismerje meg az Azure Service Fabric Mesh gyakran feltett kérdéseit és válaszait.
ms.author: pepogors
ms.date: 4/23/2019
ms.topic: troubleshooting
ms.openlocfilehash: 2a5c2ea63d162eb6fb78ab702e0519f8ac25dcc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252492"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Gyakori kérdés a Service Fabric Mesh-hez

Az Azure Service Fabric Mesh egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a fejlesztők számára a mikroszolgáltatás-alkalmazások üzembe helyezését a virtuális gépek, a tárolók és a hálózat kezelése nélkül. Ez a cikk választ ad a gyakran feltett kérdésekre.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Hogyan jelenthetek be egy problémát, vagy hogyan tehetek fel kérdést?

Kérdéseket tehet fel, válaszokat kaphat a Microsoft mérnökeitől, és problémákat jelenthet a [service-fabric-mesh-preview GitHub-tárházban.](https://aka.ms/sfmeshissues)

## <a name="quota-and-cost"></a>Kvóta és költség

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>Mennyibe kerül az előzetes verzióban való részvétel?

Jelenleg nincs díj az alkalmazások vagy tárolók üzembe helyezéséért a Mesh előzetes verzióban. Kérjük, figyelje meg a frissítéseket május engedélyezéséhez számlázás. Javasoljuk azonban, hogy törölje az üzembe helyezett erőforrásokat, és ne hagyja őket futni, hacsak nem teszteli őket aktívan.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>Van-e kvótakorlát a magok és a RAM-ok számára?

Igen. Az egyes előfizetések kvótái a következők:

- Kérelmek száma: 5
- Magok alkalmazásonként: 12
- Alkalmazásonként összes RAM: 48 GB
- Hálózati és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és
- A csatolható Azure-kötetek: 10
- Szolgáltatási replikák száma: 3
- A legnagyobb tároló, amelyet telepíthet, 4 magra és 16 GB RAM-ra korlátozódik.
- Részleges magokat rendelhet a tárolókhoz 0,5 magnyi lépésekben, de legfeljebb 6 magig.

### <a name="how-long-can-i-leave-my-application-deployed"></a>Mennyi ideig hagyhatom az alkalmazás üzembe helyezve?

Jelenleg két napra korlátoztuk egy alkalmazás élettartamát. Ez annak érdekében, hogy maximalizálja a szabad magok kiosztott az előzetes verzió. Ennek eredményeképpen csak 48 órán keresztül futtathat egy adott központi telepítést, amely idő után le áll.

Ha ezt látja, ellenőrizheti, hogy a rendszer leállította-e a parancsot az `az mesh app show` Azure CLI-ben. Ellenőrizze, hogy visszatér-e`"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

Példa: 

```azurecli
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

```output
{
  "debugParams": null,
  "description": "Service Fabric Mesh HelloWorld Application!",
  "diagnostics": null,
  "healthState": "Ok",
  "id": "/subscriptions/1134234-b756-4979-84re-09d671c0c345/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabricMesh/applications/helloWorldApp",
  "location": "eastus",
  "name": "helloWorldApp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "serviceNames": [
    "helloWorldService"
  ],
  "services": null,
  "status": "Failed",
  "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue.",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/applications",
  "unhealthyEvaluation": null
}
```

Az erőforráscsoport törléséhez `az group delete <nameOfResourceGroup>` használja a parancsot.

## <a name="deployments"></a>Központi telepítés

### <a name="what-container-images-are-supported"></a>Milyen tárolórendszerképek támogatottak?

Ha Windows Fall Creators Update (1709-es verzió) gépen fejleszt, csak a Windows 1709-es verziójú docker-lemezképeit használhatja.

Ha windows 10 április 2018-as frissítéssel (1803-as verzióval) fejleszt, használhatja a Windows 1709-es vagy a Windows 1803-as verziójú docker-lemezképeit.

A következő tároló operációsrendszer-rendszerképek szolgáltatások üzembe helyezéséhez használhatók:
- Windows - windowsservercore és nanoserver
    - Windows Server 1709
    - Windows Server 1803
    - Windows Server 1809
    - Windows Server 2019 LTSC
- Linux
    - Nincsenek ismert korlátozások

> [!NOTE]
> A Visual Studio mesh-i eszközhasználata még nem támogatja a Windows Server 2019 és 1809 tárolókba való telepítést.

### <a name="what-types-of-applications-can-i-deploy"></a>Milyen típusú alkalmazásokat telepíthetek? 

Bármit üzembe helyezhet, amely olyan tárolókban fut, amelyek beleférnek az alkalmazás-erőforrásra vonatkozó korlátozásokba (a kvótákról további információt lásd). Ha azt észleljük, hogy a Mesh-t illegális számítási feladatok futtatásához vagy a rendszerrel (azaz a bányászattal) való visszaéléshez használja, fenntartjuk a jogot, hogy megszüntessük az üzemelő példányait, és letiltsuk az előfizetését a szolgáltatás futtatásából. Kérjük, lépjen kapcsolatba velünk, ha bármilyen kérdése van egy adott munkaterhelés futtatásával kapcsolatban. 

## <a name="developer-experience-issues"></a>Fejlesztői élményekkel kapcsolatos problémák

### <a name="dns-resolution-from-a-container-doesnt-work"></a>A tárolóból származó DNS-feloldás nem működik

Bizonyos körülmények között sikertelen lehet a kimenő DNS-lekérdezések egy tárolóból a Service Fabric DNS-szolgáltatásába. Ezt még vizsgálják. A következők enyhítése:

- A Windows Fall Creators frissítése (1709-es verzió) vagy újabb verzió az alaptárolórendszerképként.
- Ha a szolgáltatás neve önmagában nem működik, próbálkozzon a teljesen minősített névvel: ServiceName.ApplicationName.
- A szolgáltatás Docker-fájljában `EXPOSE <port>` adja hozzá, hogy hol található a port az a port, amelyen a szolgáltatást felfedi. Példa:

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>A DNS nem ugyanúgy működik, mint a Service Fabric fejlesztői fürtjein és a Mesh-ben

Előfordulhat, hogy a helyi fejlesztési fürtben a szolgáltatásokra másképp kell hivatkoznia, mint az Azure Mesh-ben.

A helyi fejlesztési `{serviceName}.{applicationName}`fürt használatában. Az Azure Service Fabric `{servicename}`Mesh alkalmazásban használja a használatát. 

Az Azure Mesh jelenleg nem támogatja a DNS-feloldás t alkalmazások között.

A Service Fabric fejlesztői fürt windows 10-en való futtatásával kapcsolatos egyéb ismert DNS-problémákról a Következő témakörben lehet tudni: [Windows-tárolók hibakeresése](/azure/service-fabric/service-fabric-how-to-debug-windows-containers) és [ismert DNS-problémák](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#known-issues).

### <a name="networking"></a>Hálózat

A ServiceFabric hálózati hálózati hálózati kapcsolaton belül eltűnhet az alkalmazás helyi számítógépen való futtatása közben. Annak diagnosztizálásához, hogy ez megtörtént-e, futtassa a következőket a parancssorból:

`docker network ls`és jegyezze fel, hogy szerepel-e `servicefabric_nat` a listán.  Ha nem, akkor futtassa a következő parancsot:`docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

Ez akkor is megoldanak a problémát, ha az alkalmazás már telepítve van helyileg és nem megfelelő állapotban.

### <a name="issues-running-multiple-apps"></a>Több alkalmazás futtatásával kapcsolatos problémák

Előfordulhat, hogy a processzor rendelkezésre állása és korlátok rögzített az összes alkalmazás között. A következők enyhítése:
- Hozzon létre egy öt csomós fürtöt.
- Csökkentse a cpu-használatot a telepített alkalmazás szolgáltatásaiban. Például a szolgáltatás service.yaml fájljában `cpu: 1.0` módosítsa a`cpu: 0.5`

Több alkalmazás nem telepíthető egycsomópontos fürtre. A következők enyhítése:
- Öt csomópontos fürt használata több alkalmazás helyi fürtre történő telepítésekor.
- Távolítsa el azokat az alkalmazásokat, amelyeket jelenleg nem tesztel.

### <a name="vs-tooling-has-limited-support-for-windows-containers"></a>A VS Tooling korlátozott mértékben támogatja a Windows-tárolókat

A Visual Studio eszközeszköze csak a Windows Server 1709 és 1803 operációs rendszer alapverziójával rendelkező Windows-tárolók telepítését támogatja. 

## <a name="feature-gaps-and-other-known-issues"></a>Jellemzőhézagok és egyéb ismert problémák

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>Az alkalmazás telepítése után a hozzá társított hálózati erőforrás nem rendelkezik IP-címmel

Van egy ismert probléma, amelyben az IP-cím nem válik azonnal elérhetővé. Ellenőrizze a hálózati erőforrás állapotát néhány perc alatt a társított IP-cím megtekintéséhez.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>Az alkalmazás nem fér hozzá a megfelelő hálózati/köteterőforráshoz

Az alkalmazásmodellben használja a teljes erőforrás-azonosítót a hálózatokhoz és a kötetekhez, hogy hozzáférhessen a társított erőforráshoz. Íme egy példa a rövid útmutatóból:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>Amikor kinagyulok, az összes tárolóm érintett, beleértve a futótárolókat is

Ez egy rovar és egy erősít van lét eszköz.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Service Fabric Mesh-ről, olvassa el az [áttekintést.](service-fabric-mesh-overview.md)
