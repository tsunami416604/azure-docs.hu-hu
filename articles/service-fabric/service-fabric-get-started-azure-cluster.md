---
title: "Azure Service Fabric-fürt telepítése | Microsoft Docs"
description: "Rövid útmutató – Windows vagy Linux Service Fabric-fürt létrehozása az Azure-ban."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/05/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: ad473a2c9006e2593a84364f03e3954a569adcab
ms.contentlocale: hu-hu
ms.lasthandoff: 05/08/2017


---

# <a name="create-your-first-service-fabric-cluster-on-azure"></a>Az első saját Service Fabric-fürt létrehozása az Azure-on
A [Service Fabric-fürt](service-fabric-deploy-anywhere.md) virtuális és fizikai gépek hálózaton keresztül csatlakozó készlete, amelyen mikroszolgáltatásokat helyezhet üzembe és felügyelhet. A rövid útmutató segítségével csupán pár perc alatt létrehozhat egy öt csomópontot számláló, Windows- vagy Linux-alapú fürtöt az [Azure PowerShellen](https://msdn.microsoft.com/library/dn135248) vagy az [Azure Portalon](http://portal.azure.com) keresztül.  

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Jelentkezzen be az Azure Portalra a [http://portal.azure.com](http://portal.azure.com) webhelyen.

### <a name="create-the-cluster"></a>A fürt létrehozása

1. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.
2. Válassza a **Számítás** elemet az **Új** panelen, majd a **Service Fabric-fürt** elemet a **Számítás** panelen.
3. Töltse ki a Service Fabric **Alapvető beállítások** űrlapját. Az **Operációs rendszer** beállításban adja meg a Windows vagy Linux azon verzióját, amelyiken a fürt csomópontjait futtatni szeretné. Az itt megadott felhasználónévvel és jelszóval bejelentkezhet a virtuális gépbe. Hozzon létre egy új **Erőforráscsoportot**. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer létrehozza és együttesen kezeli az Azure-erőforrásokat. Amikor végzett, kattintson az **OK** gombra.

    ![A fürtbeállítás kimenete][cluster-setup-basics]

4. Töltse ki a **Fürtkonfiguráció** űrlapot.  A **Csomóponttípusok száma** elemnél adja meg az „1”, a [Tartóssági szint](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) elemnél a „Bronz” értéket.

5. Válassza **Az egyes csomóponttípusok konfigurálása** lehetőséget, és töltse ki a **Csomóponttípus konfigurációja** űrlapot. A csomóponttípusok határozzák meg a virtuális gépek méretét, számát, egyedi végpontjait, valamint az adott típusú virtuális gépek egyéb beállításait. Mindegyik megadott csomóponttípus külön virtuálisgép-méretezési csoportként lesz beállítva, amely a virtuális gépek csoportként való üzembe helyezésére és felügyeletére használható. Mindegyik csomóponttípus egymástól függetlenül skálázható vertikálisan le vagy föl, eltérő nyitott portokkal rendelkezik, és eltérő kapacitásmetrikái lehetnek.  Az első – vagy elsődleges – csomóponttípus az, ahol a Service Fabric rendszerszolgáltatásai futnak, és ennek öt vagy annál több virtuális gépet kell tartalmaznia.

    A [kapacitástervezés](service-fabric-cluster-capacity.md) az éles rendszerek üzembe helyezésének lényeges lépése.  A jelen rövid útmutatóban azonban nem fog alkalmazásokat futtatni, így válassza a *DS1_v2 Standard* VM-méretet.  [Megbízhatósági szintként](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) adja meg az „Ezüst” értéket, a virtuálisgép-méretezési csoport kezdő kapacitását pedig állítsa 5-re.  

    Az egyedi végpontok portokat nyitnak meg az Azure Load Balancerben, hogy csatlakozni tudjon a fürtben futó alkalmazásokhoz.  Adja meg a „80, 8172” értéket a 80-as és a 8172-es portok megnyitásához.

    Ne jelölje be a **Speciális beállítások konfigurálása** jelölőnégyzetet, amely a TCP/HTTP felügyeleti végpontok, alkalmazásport-tartományok, [elhelyezési korlátozások](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints) és [kapacitástulajdonságok](service-fabric-cluster-resource-manager-metrics.md) testreszabására szolgál.    

    Kattintson az **OK** gombra.

6. A **Fürtkonfiguráció** űrlapon állítsa a **Diagnosztika** beállítást **Be** értékre.  A jelen rövid útmutató során nem kell megadnia [hálóbeállítási](service-fabric-cluster-fabric-settings.md) tulajdonságokat.  A **Fabric verziója** elemnél válassza az **Automatikus** frissítési módot, így a Microsoft automatikusan frissíti a fürtön futó Fabric-kód verzióját.  Állítsa a módot **Manuálisra**, ha ki szeretne [választani egy támogatott verziót](service-fabric-cluster-upgrade.md), amelyre frissíteni szeretne. 

    ![Csomóponttípus konfigurálása][node-type-config]

    Kattintson az **OK** gombra.

7. Töltse ki a **Biztonság** űrlapot.  Ehhez a rövid útmutatóhoz válassza a **Nem biztonságos** beállítást.  Az éles számítási feladatokhoz azonban határozottan javasolt biztonságos fürtöket létrehozni, mivel a nem biztonságos fürtökhöz bárki név nélkül csatlakozhat, és ott felügyeleti tevékenységeket hajthat végre.  

    A Service Fabric tanúsítványokat használ a hitelesítéshez és titkosításhoz a fürtök és a rajtuk található alkalmazások különféle részeinek védelmére. A tanúsítványok Service Fabricban való használatával kapcsolatos további információkért lásd a [Service Fabric-fürtök biztonsági forgatókönyveit](service-fabric-cluster-security.md).  Az Azure Active Directoryval végzett felhasználóhitelesítés engedélyezéséhez, illetve a tanúsítványok alkalmazásbiztonsági célú beállításához [a fürtöt Resource Manager-sablonból hozza létre](service-fabric-cluster-creation-via-arm.md).

    Kattintson az **OK** gombra.

8. Tekintse át az összegzést.  Ha le szeretné tölteni a megadott beállítások alapján összeállított Resource Manager-sablont, válassza a **Sablon és paraméterek letöltése** lehetőséget.  A fürt létrehozásához kattintson a **Létrehozás** gombra.

    A létrehozás folyamatát az értesítésekben követheti nyomon. (Kattintson a „Harang” ikonra az állapotsor mellett, a képernyő jobb felső részén.) Ha a fürt létrehozásakor **A kezdőpulton rögzít** lehetőségre kattintott, a **Service Fabric-fürt üzembe helyezése** a **Kezdőpultra** rögzítve látható.

### <a name="view-cluster-status"></a>Fürt állapotának megtekintése
Miután a fürt létrejött, a Portal **Áttekintés** paneljén tekintheti meg. A fürt részletei megjelennek az irányítópulton, beleértve a fürt nyilvános végpontját és egy, a Service Fabric Explorerre mutató hivatkozást.

![Fürt állapota][cluster-status]

### <a name="visualize-the-cluster-using-service-fabric-explorer"></a>A fürt megjelenítése a Service Fabric Explorerrel
A [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) hatékony eszköz a fürtök megjelenítéséhez és az alkalmazások kezeléséhez.  A Service Fabric Explorer a fürtben futó szolgáltatás,  amely a webböngészőből érhető el, ha a **Service Fabric Explorer** hivatkozásra kattint a fürt **Áttekintés** oldalán a Portalon.  A címet közvetlenül a böngészőben is megadhatja: [http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer](http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer)

A fürt irányítópultja áttekintést nyújt a fürtről, beleértve az alkalmazások és a csomópontok állapotának összefoglalását. A csomópontnézet a fürt fizikai elrendezését mutatja. Az egyes csomópontoknál megtekintheti, hogy melyik alkalmazások kódja üzemel az adott csomóponton.

![Service Fabric Explorer][service-fabric-explorer]

### <a name="connect-to-the-cluster-using-powershell"></a>Csatlakozás a fürthöz PowerShell használatával
Ha ellenőrizni szeretné, hogy a fürt fut-e, csatlakozzon hozzá a PowerShell-lel.  A ServiceFabric PowerShell-modul a [Service Fabric SDK](service-fabric-get-started.md)-val települ.  A [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag kiépít egy kapcsolatot a fürttel.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
A fürtökhöz való csatlakozást bemutató egyéb példákért lásd: [Csatlakozás biztonságos fürthöz](service-fabric-connect-to-secure-cluster.md). Miután csatlakozott a fürthöz, a [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) parancsmaggal listázza a fürtben lévő csomópontokat és az egyes csomópontok állapotinformációit. A **HealthState** tulajdonságnak *OK* értékűnek kell lennie minden csomópont esetében.

```powershell
PS C:\> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName     IpAddressOrFQDN NodeType  CodeVersion ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- --------     --------------- --------  ----------- ------------- ---------- ---------- ------------ -----------
                     _nodetype1_2 10.0.0.6        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_1 10.0.0.5        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_0 10.0.0.4        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_4 10.0.0.8        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_3 10.0.0.7        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
```

### <a name="remove-the-cluster"></a>A fürt eltávolítása
A Service Fabric-fürtben a fürt erőforrásán felül egyéb Azure-erőforrások is megtalálhatók. A Service Fabric-fürtök teljes törléséhez ezért az összes őket alkotó erőforrást is törölni kell. A fürt és az összes általa használt erőforrás törlésének legegyszerűbb módja az erőforráscsoport törlése. A fürt törlésének egyéb módjaival, illetve egy erőforráscsoportba tartozó némely (de nem az összes) erőforrás törlésével kapcsolatban lásd a [fürt törlésével](service-fabric-cluster-delete.md) foglalkozó témakört.

Erőforráscsoport törlése az Azure Portalon:
1. Lépjen a törölni kívánt Service Fabric-fürtre.
2. Kattintson az **Erőforráscsoport** nevére a fürt alapvető erőforrásainak lapján.
3. Az **Erőforráscsoport alapvető erőforrásai** oldalon kattintson a **Törlés** gombra, és kövesse a lapon megjelenő utasításokat az erőforráscsoport törléséhez.
    ![Az erőforráscsoport törlése][cluster-delete]


## <a name="use-azure-powershell-to-deploy-a-secure-cluster"></a>Biztonságos fürt üzembe helyezése az Azure PowerShell használatával


1) Töltse le a számítógépre az [Azure PowerShell-modul 4.0-s vagy újabb](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) verzióját.

2) Nyisson meg egy Windows PowerShell-ablakot, és futtassa a következő parancsot. 
    
```powershell

Get-Command -Module AzureRM.ServiceFabric 
```

Az alábbihoz hasonló kimenetnek kell megjelennie.

![ps-list][ps-list]

3) Jelentkezzen be az Azure-ba, és válassza ki azt az előfizetést, amelyikben létre szeretné hozni a fürtöt

```powershell

Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId "Subcription ID" 

```

4) Futtassa az alábbi parancsot egy biztonságos fürt létrehozásához. Ne felejtse el testre szabni a paramétereket. 


````powershell

$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
$RDPpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 
$RDPuser="vmadmin"
$RGname="mycluster" # this is also the name of your cluster
$clusterloc="SouthCentralUS"
$subname="$RGname.$clusterloc.cloudapp.azure.com"
$certfolder="c:\mycertificates\"
$clustersize=1 # can take values 1, 3-99

New-AzureRmServiceFabricCluster -ResourceGroupName $RGname -Location $clusterloc -ClusterSize $clustersize -VmUserName $RDPuser -VmPassword $RDPpwd -CertificateSubjectName $subname -CertificatePassword $certpwd -CertificateOutputFolder $certfolder

````

A parancs végrehajtása 10 perctől akár 30 percig is eltarthat, és a végén az alábbihoz hasonló kimenetnek kell megjelennie. A kimenet információkat tartalmaz a tanúsítványról, arról a KeyVault tárolóról, ahová feltöltötték, és a helyi mappáról, ahová másolták. 

![ps-out][ps-out]

5) Másolja ki a teljes kimenetet, és mentse egy szövegfájlba, mivel később hivatkoznia kell rá. Jegyezze fel az alábbi, kimenetből származó adatokat.
 

- **CertificateSavedLocalPath** : c:\mycertificates\mycluster20170504141137.pfx
- **CertificateThumbprint** : C4C1E541AD512B8065280292A8BA6079C3F26F10
- **ManagementEndpoint** : https://mycluster.southcentralus.cloudapp.azure.com:19080
- **ClientConnectionEndpointPort** : 19000

### <a name="install-the-certificate-on-your-local-machine"></a>A tanúsítvány telepítése helyi számítógépre
  
A fürthöz való csatlakozáshoz telepíteni kell a tanúsítványt az aktuális felhasználó Személyes (Saját) tárolójába. 

Futtassa az alábbi PowerShell-parancsot

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\the name of the cert.pfx `
        -Password (ConvertTo-SecureString -String certpwd -AsPlainText -Force)
```

Most már készen áll a biztonságos fürthöz való csatlakozásra.

### <a name="connect-to-a-secure-cluster"></a>Csatlakozás biztonságos fürthöz 

A biztonságos fürthöz való csatlakozáshoz futtassa az alábbi PowerShell-parancsot. A tanúsítvány részleteinek meg kell egyezniük a fürt beállításához használt egyik tanúsítvány részleteivel. 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```


Az alábbi példa az eredményül kapott paramétereket mutatja be: 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mycluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Az alábbi parancs futtatásával ellenőrizze, hogy csatlakozik-e, és hogy a fürt állapota kifogástalan-e.

```powershell

Get-ServiceFabricClusterHealth

```
### <a name="publish-your-apps-to-your-cluster-from-visual-studio"></a>Alkalmazások közzététele a fürtön a Visual Studióból

Most, hogy beállított egy Azure-fürtöt, a [fürtön történő közzétételt](service-fabric-publish-app-remote-cluster.md) ismertető dokumentum utasításait követve közzéteheti rajta az alkalmazásait a Visual Studióból. 

### <a name="remove-the-cluster"></a>A fürt eltávolítása
A fürtben a fürt erőforrásán felül egyéb Azure-erőforrások is megtalálhatók. A fürt és az összes általa használt erőforrás törlésének legegyszerűbb módja az erőforráscsoport törlése. 

```powershell

Remove-AzureRmResourceGroup -Name $RGname -Force

```

## <a name="next-steps"></a>Következő lépések
Most, hogy üzembe helyezett egy fejlesztési fürtöt, megpróbálkozhat a következőkkel:
* [Biztonságos fürt létrehozása a Portalon](service-fabric-cluster-creation-via-portal.md)
* [Fürt létrehozása sablonból](service-fabric-cluster-creation-via-arm.md) 
* [Appokat helyezhet üzembe a PowerShell használatával](service-fabric-deploy-remove-applications.md)


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png
[ps-list]: ./media/service-fabric-get-started-azure-cluster/pslist.PNG
[ps-out]: ./media/service-fabric-get-started-azure-cluster/psout.PNG

