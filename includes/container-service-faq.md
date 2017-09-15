# <a name="container-service-frequently-asked-questions"></a>A Container Service-re vonatkozó gyakori kérdések

## <a name="orchestrators"></a>Vezénylők

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>Mely tárolóvezénylőket támogatja az Azure Container Service? 

A következők élveznek támogatást: nyílt forráskódú DC/OS, Docker Swarm és Kubernetes. További információ: [Áttekintés](../articles/container-service/kubernetes/container-service-intro-kubernetes.md).
 
### <a name="do-you-support-docker-swarm-mode"></a>Támogatja a Docker Swarm módot? 

A Swarm mód jelenleg még nem élvez támogatást, de már a szolgáltatáshoz kapcsolódó ütemterv részét képezi. 

### <a name="does-azure-container-service-support-windows-containers"></a>Az Azure Container Service támogatja a Windows-tárolókat?  

Jelenleg a Linux-tárolók az összes vezénylővel támogatást élveznek. A Windows-tárolók Kubernetesszel való használatának támogatása előzetes verzióban érhető el.

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>Ajánlanak egy adott vezénylőt az Azure Container Service-ben? 
Nem ajánlunk általánosan egy adott vezénylőt. Ha már rendelkezik tapasztalattal a támogatott vezénylők egyikéhez kapcsolódóan, tapasztalataira építhet az Azure Container Service-ben is. Az adattrendek azonban azt jelzik, hogy a DC/OS kiválóan használható éles környezetben a Big Data és IoT számítási feladatokhoz, a Kubernetes a felhők natív számítási feladataihoz megfelelő, a Docker Swarm pedig közismerten integrálható Docker-eszközökkel, illetve egyszerűen elsajátítható a használata.

A forgatókönyvtől függően más Azure-szolgáltatásokkal is létrehozhat és kezelhet egyéni tárolómegoldásokat. Ilyen szolgáltatás például a [Virtual Machines](../articles/virtual-machines/linux/overview.md), a [Service Fabric](../articles/service-fabric/service-fabric-overview.md), a [Web Apps](../articles/app-service-web/app-service-web-overview.md) és a [Batch](../articles/batch/batch-technical-overview.md).  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Mi a különbség az Azure Container Service és ASC Engine között? 
Az Azure Container Service egy SLA-alapú Azure-szolgáltatás, amely az Azure Portalon elérhető funkciókkal, Azure parancssori eszközökkel és Azure API-kkal rendelkezik. A szolgáltatás lehetővé teszi, hogy viszonylag kevés konfigurációs beállítás megadásával, gyorsan valósíthasson meg és kezelhessen általános, tárolóvezénylési eszközöket futtató fürtöket. 

Az [ACS Engine](http://github.com/Azure/acs-engine) egy nyílt forráskódú projekt, amely lehetővé teszi a kiemelt felhasználók számára a fürtkonfiguráció egyéni beállítását minden szinten. Az infrastruktúra és a szoftverek konfigurációjának módosíthatósága azzal jár, hogy nem kínálunk SLA-t az ACS Engine esetén. A támogatás kezelése a GitHubon található nyílt forráskódú projekt segítségével történik a hivatalos Microsoft-csatornák helyett. 

További részleteket a [tárolókra vonatkozó támogatási szabályzatban](https://support.microsoft.com/en-us/help/4035670/support-policy-for-containers) talál.

## <a name="cluster-management"></a>Fürtkezelés

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>Hogyan hozhatok létre SSH-kulcsokat a fürtöm számára?

A fürt Linux rendszerű virtuális gépei felé történő hitelesítéshez szükséges nyilvános és titkos SSH RSA-kulcspár létrehozásához az operációs rendszer megszokott eszközeit használhatja. A lépéseket az [OS X és Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) vagy a [Windows](../articles/virtual-machines/linux/ssh-from-windows.md) rendszerhez készült útmutatóban tekintheti meg. 

Ha az [Azure CLI 2.0 parancsait](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) használja egy Container Service-fürt központi telepítéséhez, az SSH-kulcsok automatikusan létrehozhatóak a fürt számára.

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>Hogyan hozhatok létre egyszerű szolgáltatást a Kubernetes-fürtöm számára?

Ha az Azure Container Service-ben szeretne Kubernetes-fürtöt létrehozni, szüksége lesz egy egyszerű Azure Active Directory-szolgáltatás azonosítójára és jelszavára. További információ: [Tudnivalók az Kubernetes-fürthöz tartozó egyszerű szolgáltatásról](../articles/container-service/kubernetes/container-service-kubernetes-service-principal.md).

Ha az [Azure CLI 2.0 parancsait](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) használja egy Kubernetes-fürt központi telepítéséhez, az egyszerű szolgáltatás hitelesítő adatai automatikusan létrehozhatók a fürt számára.

### <a name="how-large-a-cluster-can-i-create"></a>Legfeljebb mekkora fürtöket hozhatok létre?
1, 3 vagy 5 fő csomóponttal rendelkező fürtöket hozhat létre. Legfeljebb 100 ügynökcsomópontot használhat.

> [!IMPORTANT]
> Nagyobb fürtök esetén és a virtuális gép a csomópontokhoz választott méretétől függően esetleg növelnie kell előfizetése magkvótáját. Ha kérni szeretné a kvóta növelését, hozzon létre egy ingyenes [támogatási kérést](../articles/azure-supportability/how-to-create-azure-support-request.md) az interneten. Amennyiben [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) használ, csak korlátozott számú számítási magot használhat az Azure-ban.
> 

### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>Hogyan növelhetem a főkiszolgálók számát a fürt létrehozása után? 
A fürt létrehozása után a főkiszolgálók száma állandó lesz, és nem módosítható. A fürt létrehozása során érdemes több főkiszolgálót kijelölni magas rendelkezésre állásra.

### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>Hogyan növelhetem az ügynökök számát a fürt létrehozása után? 
Az Azure Portal vagy a parancssori eszközök használatával módosíthatja a fürtben található ügynökök számát. Lásd: [Azure Container Service-fürt méretezése](../articles/container-service/kubernetes/container-service-scale.md).

### <a name="what-are-the-urls-of-my-masters-and-agents"></a>Mik a főkiszolgálóim és ügynökeim URL-címei? 
Az Azure Container Service-ben található fürterőforrások URL-címei a megadott DNS-név előtagján és a központi telepítéshez választott Azure-régió nevén alapulnak. A főcsomópont teljes tartományneve (FQDN) például az alábbi formátumú:

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

Kereshet gyakran használt URL-címeket a fürt számára az Azure Portalon, az Azure Resource Explorerben vagy más Azure-eszközben.

### <a name="how-do-i-tell-which-orchestrator-version-is-running-in-my-cluster"></a>Hogyan állapíthatom meg, hogy melyik vezénylőverzió fut a fürtben?

* DC/OS: Lásd a [Mesosphere dokumentációját](https://support.mesosphere.com/hc/en-us/articles/207719793-How-to-get-the-DCOS-version-from-the-command-line-).
* Docker Swarm: Futtassa a `docker version` parancsot
* Kubernetes: Futtassa a `kubectl version` parancsot

### <a name="how-do-i-upgrade-the-orchestrator-after-deployment"></a>Hogyan frissíthetem a vezénylőt az üzembe helyezést követően?

Az Azure Container Service jelenleg nem biztosít eszközöket a fürtre telepített vezénylő verziófrissítéséhez. Ha a Container Service egy későbbi verziót támogat, üzembe helyezhet egy új fürtöt. Egy másik lehetőség a vezénylőspecifikus eszközök használata, amelyek segítségével a már beüzemelt fürtök frissíthetők. Lásd például a [DC/OS verziófrissítését](https://dcos.io/docs/1.8/administration/upgrading/) ismertető cikket.
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>Hol találom a fürtömhöz tartozó SSH kapcsolati karakterláncot?

A kapcsolati karakterláncot az Azure Portalon vagy az Azure parancssori eszközök segítségével keresheti meg. 

1. A portálon lépjen a fürttelepítés erőforráscsoportjába.  

2. Kattintson az **Overview** (Áttekintés) elemre, majd kattintson a **Deployments** (Üzemelő példányok) hivatkozásra az **Essentials** (Alapvető erőforrások) részen. 

3. A **Deployment history** (Telepítési előzmények) panelen kattintson arra az üzemelő példányra, amelynek neve a **microsoft-acs** karakterlánccal kezdődik, és a központi telepítés dátumával folytatódik. Például: microsoft-acs-201701310000.  

4. A **Summary** (Összefoglalás) lapon az **Outputs** (Kimenetek) részben számos fürthivatkozás található. Az **SSHMaster0** biztosítja a Container Service-fürt első főkiszolgálójához tartozó SSH kapcsolati karakterláncot. 

A fent említetteknek megfelelően Azure-eszközöket is használhat a főkiszolgálók teljes tartománynevének megkeresésére. Létesítsen SSH-kapcsolatot a főkiszolgálóval a főkiszolgáló teljes tartománynevét és a fürt létrehozásakor megadott felhasználónevet használva. Példa:

```bash
ssh userName@masterFQDN –A –p 22 
```

Több információ: [Csatlakozás Azure Container Service-fürthöz](../articles/container-service/kubernetes/container-service-connect.md).

## <a name="next-steps"></a>Következő lépések

* [További információ](../articles/container-service/kubernetes/container-service-intro-kubernetes.md) az Azure Container Service-ről.
* Container Service-fürt központi telepítése a [portál](../articles/container-service/dcos-swarm/container-service-deployment.md) vagy az [Azure CLI 2.0](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) segítségével.
