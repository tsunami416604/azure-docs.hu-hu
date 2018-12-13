---
title: Az Azure Service Fabric-háló kapcsolatos gyakori kérdések |} A Microsoft Docs
description: További információk a gyakori kérdések és válaszok az Azure Service Fabric-háló.
services: service-fabric-mesh
keywords: ''
author: chackdan
ms.author: chackdan
ms.date: 12/12/2018
ms.topic: troubleshooting
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: 7103557d19b367be0b9f0aa6f4a4642800c14558
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314832"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Gyakori kérdések Service Fabric-háló

Az Azure Service Fabric Mesh egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a fejlesztők számára a mikroszolgáltatás-alkalmazások üzembe helyezését a virtuális gépek, a tárolók és a hálózat kezelése nélkül. Ez a cikk gyakori kérdésekre adott válaszokat tartalmaz.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Hogyan probléma jelentéséhez vagy tegyen fel kérdést?

Kérdések, válaszok a Microsoft mérnökei és a problémák jelentése a [service-fabric-háló-preview GitHub-adattárat](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Kvóta- és költség

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>Az előzetes verzióra való részvételnek mennyibe kerül?

Nem számítunk fel díjat üzembe alkalmazások vagy a háló előzetes tárolók jelenleg. Azonban javasoljuk, hogy törli az erőforrások üzembe helyezése, és nem hagyhatja őket fut, kivéve, ha folyamatosan tesztelt őket.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>A magok és a RAM-MAL számát kvóta korlátozva van?

Igen. A kvóták minden egyes előfizetés esetén a következők:

- Alkalmazások száma: 5
- Magok száma alkalmazás: 12
- Teljes memória alkalmazásonként: 48 GB
- A hálózati bejövő és kimenő végpontok: 5
- Az Azure-köteteket tartalmazza csatolhat a szolgáltatáskéréshez: 10
- Szolgáltatás replikák száma: 3
- A legnagyobb tárolót telepíthet 4 maggal és 16GB RAM-MAL korlátozódik.
- 0,5 mag, legfeljebb 6 magok egységnyi növekményekben a tárolókat, részleges magot foglalhat.

### <a name="how-long-can-i-leave-my-application-deployed"></a>Mennyi ideig hagyhatja üzembe helyezve az alkalmazásom?

Mi jelenleg csak korlátozott két nap az alkalmazás teljes élettartama. Ez az az ingyenes, az előzetes lefoglalt Processzormagok használata maximalizálása érdekében. Ennek eredményeképpen csak engedélyezett futtatni egy adott üzemelő példánya folyamatosan 48 óra, mely később le fog állni.

Ennek elérése látja, ha, hogy a rendszer állítsa le a futó ellenőrizheti a `az mesh app show` parancsot az Azure CLI-ben. Ellenőrizze, hogy ha akkor adja vissza `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

Példa: 

```cli
~$ az mesh app show --resource-group myResourceGroup --name helloWorldApp
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

Az erőforráscsoport törléséhez használja a `az group delete <nameOfResourceGroup>` parancsot.

## <a name="supported-container-os-images"></a>Támogatott operációs rendszer tárolórendszerképek

Ha egy Windows Fall Creators Update (1709-es verzió) gépen fejleszt, használhatja a Windows verzió 1709-es docker a docker-rendszerképek csak.

Ha egy Windows rendszeren fejleszt 2018 április 10. (verzió 1803) machine, Windows 1709-es vagy Windows-verzió 1803 docker-rendszerképeket is használhat.

A következő operációs rendszer tárolórendszerképeket a szolgáltatások üzembe helyezéséhez használható:

- Windows - windowsservercore és nanoserver
    - A Windows Server 1709-es verzió
    - A Windows Server verzió 1803-as verzióban
- Linux
    - Nem ismert korlátozások

## <a name="developer-experience-issues"></a>Fejlesztői élmény kapcsolatos problémák

### <a name="dns-resolution-from-an-outbound-container-doesnt-work"></a>Egy kimenő tárolóból DNS-feloldás nem működik

Szolgáltatások közötti kommunikáció sikertelen bizonyos körülmények között lehet. A rendszer vizsgálata folyamatban van. Csökkentése érdekében:

- Használja a Windows Fall Creators update (1709-es verzió) vagy nagyobb, mint az alapszintű tárolórendszerképet.
- Ha egyedül a szolgáltatás neve sem működik, próbálja meg a teljes nevet: ServiceName.ApplicationName.
- Adja hozzá a Docker-fájlt a szolgáltatáshoz, `EXPOSE <port>` adott portot közzéteszi a szolgáltatást a. Példa:

```
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>DNS nem működik a ugyanaz, mint a Service Fabric fejlesztési fürtök és a háló

Szükség lehet a szolgáltatások a helyi fejlesztési fürt, mint az Azure-háló eltérően hivatkozhat.

A helyi fejlesztési fürt használja `{serviceName}.{applicationName}`. Az Azure Service Fabric tervezhetők, használja `{servicename}`. 

Az Azure-háló jelenleg nem támogatja DNS-feloldás alkalmazások között.

Egyéb ismert DNS-probléma a Windows 10-es egy Service Fabric fejlesztési fürtöt futtat tekintse meg: [Windows-tárolók Debug](/azure/service-fabric/service-fabric-how-to-debug-windows-containers) és [ismert DNS-probléma](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#known-issues).

### <a name="networking"></a>Hálózat

A ServiceFabric hálózathoz hálózati Címfordítás az alkalmazást futtató helyi gépen használata során előfordulhat, hogy eltűnnek. Diagnosztizálhatja, hogy ez történt, futtassa a következő parancsot a parancssorba:

`docker network ls` és Megjegyzés: e `servicefabric_nat` szerepel a listán.  Ha nem, majd futtassa a következő parancsot: `docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

Ez a probléma irányulnak, még akkor is, ha az alkalmazás már van telepítve, helyileg és a nem kifogástalan állapotú.

### <a name="issues-running-multiple-apps"></a>Több alkalmazás futtatásakor problémákat

CPU rendelkezésre állás és a folyamatban meghatározott alkalmazások közötti határértékek léphetnek fel. Csökkentése érdekében:
- Hozzon létre egy öt csomópontot tartalmazó fürtben.
- Csökkentheti a CPU-használat, a szolgáltatások között az üzembe helyezett alkalmazást. A szolgáltatás service.yaml fájlban módosítsa például `cpu: 1.0` , `cpu: 0.5`

Több alkalmazás nem telepíthető egy csomópontos fürtre. Csökkentése érdekében:
- Egy öt csomópontot tartalmazó fürt használja, amikor több alkalmazás telepítése helyi fürtre.
- Távolítsa el az alkalmazásokat, amelyek jelenleg nem teszteli.

## <a name="feature-gaps-and-other-known-issues"></a>A szolgáltatás hiányosságokat és egyéb ismert problémák

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>Miután üzembe az alkalmazást, a hozzá társított hálózati erőforrás nem rendelkezik a IP-cím

Nincs olyan ismert probléma, amelyben az IP-cím nem azonnal elérhetővé válik. A hálózati erőforrás állapotának ellenőrzéséhez tekintse meg a társított IP-cím, néhány perc múlva.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>Az alkalmazásom nem tud hozzáférni a megfelelő hálózati/köteterőforrás

Az alkalmazásmodell hálózatok és a kötetek teljes erőforrás-azonosítója használatával érhetik el a társított erőforrás. Íme egy példa eltér a rövid útmutató:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>Tudok horizontális felskálázás, amikor az összes saját tárolókat is érintett, többek között kiépítettektől fut

A hiba, és a javítás végrehajtása alatt.

## <a name="next-steps"></a>További lépések

Service Fabric-háló kapcsolatos további információkért olvassa el a [áttekintése](service-fabric-mesh-overview.md).
