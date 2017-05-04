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
ms.date: 04/17/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: cf652f1ba6b7d3aa0717a2e3a54000a4aebccc78
ms.lasthandoff: 04/27/2017


---

# <a name="create-your-first-service-fabric-cluster-on-azure"></a>Az első saját Service Fabric-fürt létrehozása az Azure-on
A [Service Fabric-fürt](service-fabric-deploy-anywhere.md) virtuális és fizikai gépek hálózaton keresztül csatlakozó készlete, amelyen mikroszolgáltatásokat helyezhet üzembe és felügyelhet. A rövid útmutató segítségével csupán pár perc alatt létrehozhat egy öt csomópontot számláló, Windows- vagy Linux-alapú fürtöt az [Azure Portalon](http://portal.azure.com) keresztül.  

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be az Azure Portalra a [http://portal.azure.com](http://portal.azure.com) webhelyen.

## <a name="create-the-cluster"></a>A fürt létrehozása

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

## <a name="view-cluster-status"></a>Fürt állapotának megtekintése
Miután a fürt létrejött, a Portal **Áttekintés** paneljén tekintheti meg. A fürt részletei megjelennek az irányítópulton, beleértve a fürt nyilvános végpontját és egy, a Service Fabric Explorerre mutató hivatkozást.

![Fürt állapota][cluster-status]

## <a name="visualize-the-cluster-using-service-fabric-explorer"></a>A fürt megjelenítése a Service Fabric Explorerrel
A [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) hatékony eszköz a fürtök megjelenítéséhez és az alkalmazások kezeléséhez.  A Service Fabric Explorer a fürtben futó szolgáltatás,  amely a webböngészőből érhető el, ha a **Service Fabric Explorer** hivatkozásra kattint a fürt **Áttekintés** oldalán a Portalon.  A címet közvetlenül a böngészőben is megadhatja: [http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer](http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer)

A fürt irányítópultja áttekintést nyújt a fürtről, beleértve az alkalmazások és a csomópontok állapotának összefoglalását. A csomópontnézet a fürt fizikai elrendezését mutatja. Az egyes csomópontoknál megtekintheti, hogy melyik alkalmazások kódja üzemel az adott csomóponton.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="connect-to-the-cluster-using-powershell"></a>Csatlakozás a fürthöz PowerShell használatával
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

## <a name="remove-the-cluster"></a>A fürt eltávolítása
A Service Fabric-fürtben a fürt erőforrásán felül egyéb Azure-erőforrások is megtalálhatók. A Service Fabric-fürtök teljes törléséhez ezért az összes őket alkotó erőforrást is törölni kell. A fürt és az összes hozzá tartozó erőforrás törlésének legegyszerűbb módja az erőforráscsoport törlése. A fürt törlésének egyéb módjaival, illetve egy erőforráscsoportba tartozó némely (de nem az összes) erőforrás törlésével kapcsolatban lásd a [fürt törlésével](service-fabric-cluster-delete.md) foglalkozó témakört.

Erőforráscsoport törlése az Azure Portalon:
1. Lépjen a törölni kívánt Service Fabric-fürtre.
2. Kattintson az **Erőforráscsoport** nevére a fürt alapvető erőforrásainak lapján.
3. Az **Erőforráscsoport alapvető erőforrásai** oldalon kattintson a **Törlés** gombra, és kövesse a lapon megjelenő utasításokat az erőforráscsoport törléséhez.
    ![Az erőforráscsoport törlése][cluster-delete]

## <a name="next-steps"></a>Következő lépések
Most, hogy telepített egy önálló fejlesztési fürtöt, megpróbálkozhat a következőkkel:
* [Biztonságos fürt létrehozása a Portalon](service-fabric-cluster-creation-via-portal.md)
* [Fürt létrehozása sablonból](service-fabric-cluster-creation-via-arm.md) 
* [Appokat helyezhet üzembe a PowerShell használatával](service-fabric-deploy-remove-applications.md)


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png

